# 生成器函数
## 作用:


- 一个generator看上去像一个函数，但可以返回多次。



[学习地址](https://www.liaoxuefeng.com/wiki/1022910821149312/1023024381818112)
# redux-saga


## redux-saga的3条type构建核心


- xxx_START(开始)
- XXX_SUCCESS(成功)
- XXX_FAILURE(失败)



## 与redux-thunk相比


- redux-saga用于高效的处理异步获取数据，他能保证多个异步函数执行情况下且不干扰主程序, 可代替redux-thunk
- redux-sagas非常强大特别适合大型程序，用于代替redux-thunk, redux-thunk适合少量的异步数据的web程序



## 安装:


- yarn add redux-saga



## redux-saga: 重要组件测试实验,深入研究


- redux-saga: takeLatest( type, 生成器函数 )与delay配合: 达到必须等上一个saga完成, 才会促发一个新saga
- redux-saga: takeEvery( type, 生成器函数 )循环监听type促发saga
- redux-saga: delay(毫秒)在delay在创建的异步saga中,并不影响takeEvery的监听效果
- redux-saga: delay(毫秒)与take配合延迟3秒时间,当前3秒期间将无法促发saga
- redux-saga: put( action函数 )可直接执行redux的action函数
- take模仿takeEvery方法
- redux-saga: take( type )监听action只促发一次saga```jsx
import { take, takeEvery, takeLatest, delay, put } from 'redux-saga/effects'; // 注意: redux-saga函数导出的位置

// redux-saga: takeLatest( type, 生成器函数 )与delay配合: 达到必须等上一个saga完成, 才会促发一个新saga
  // 0. redux-saga的核心: 经常使用他来获取服务器的异步数据, 防止多次重复请求数据
  // 1. 注意: delay要在put之前才有效果所述效果
  // 2. 限制请求间隔非常有用
export function* onIncrement(){
  yield console.log( '必须等上一个saga完成, 才会促发一个新saga' );
  yield delay( 300 );
  yield put({ type: 'INCREMENT_FROM_SAGA' });
}

export function* incrementSaga() {
  yield takeLatest( 'INCREMENT', onIncrement );  
}

// redux-saga: takeEvery( type, 生成器函数 )循环监听type促发saga
  // 0. take之促发一次sagas，takeEvery将循环促发sagas； 
  // 1. 核心: takeEvery( type, 生成器函数 )循环监听type从而促发生成器函数
  // 2. 保证异步函数之间不冲突, 且不影响主程序
export function* onIncrement(){
  yield console.log( 'takeEvery循环多次促发saga' );

// redux-saga: delay(毫秒)因delay在创建的异步saga中,并不影响takeEvery的监听效果
  yield delay(2000); 
  
// redux-saga: put( action函数 )可直接执行redux的action函数
    // 0. put({ type: 'xxx' })可以直接执行对应的reducer
    // 1. 同样dispatch()也可以做到, 因为毕竟返回的就是{ type: xx, action: payload }
  yield put({ type: 'INCREMENT_FROM_SAGA' });
}

export function* incrementSaga() {
  yield takeEvery( 'INCREMENT', onIncrement ); // 核心: 循环监听到type则促发一个异步的saga函数, 多个异步函数保证不冲突且不影响主程序
}

// take模仿takeEvery方法
// delay( ms )延迟时间
export function* incrementSaga() {
  while( true ){
    yield take('INCREMENT');
    console.log('take模仿takeEvery');
    yield delay(3000); // 延迟3秒时间,当前3秒期间将无法促发saga
  }
}

// redux-saga: take( type )监听action只促发一次saga
  // 0. type就是redux的type字符串,指定的对应action函数
  // 1. 注意他只有一个参数take( 'type' )监测相应的action 
  // 2. 只促发一次saga, 一次过后以后将不会促发
export function* incrementSaga() {
  yield take('INCREMENT');
  console.log( '促发take' );
}
```




# redux-saga实战1


## 创建XX.SAGA.JS, 异步获取商品数据实战


- redux-saga配置redux的store.js```jsx
import { createStore, applyMiddleware } from 'redux';
import logger from 'redux-logger';
import thunk from 'redux-thunk';

// redux-saga配置store.js
import createSagaMiddleware from 'redux-saga';
import rootSaga from './root-sagas';

import { persistStore } from 'redux-persist';
import rootReducer from './root-reducer';

const sagaMiddleware = createSagaMiddleware(); // redux-saga配置中间件( redux-saga )
const middlewares = [ sagaMiddleware ];

if( process.env.NODE_ENV === 'development' ){
    middlewares.push(logger);
}
const store = createStore( rootReducer, applyMiddleware(...middlewares) );

sagaMiddleware.run( rootSaga ); // 执行saga函数( redux-saga )

const persistor = persistStore(store); // 处理store
export { store, persistor }; // 导出persistor
```




## 构建root-saga.js方便redux管理


- ALL([ 多个CALL函数 ])可直接监听执行大量SAGA函数```jsx
// redux-saga: 构建root-saga.js方便redux管理
import { call, all } from 'redux-saga/effects'
import { axiosCollectionsStart } from './shop/shop.saga'; // 关于saga函数都要来登记

// redux-saga: all([ 多个call函数 ])可直接监听执行大量saga函数
    // 0. all方法( 推荐 )
    export default function* rootSaga(){
        yield all([
            call(axiosCollectionsStart),
        ]);
    }
    
    // 1. 普通方法( 不推荐 )
    export default function* rootSaga(){
        yield axiosCollectionsStart;
        yield xxx;
}
```




## 创建XX.SAGA.JS, 异步获取商品数据实战


1. yield的在saga中的作用
2. takeLatest经常使用他来获取服务器的异步数据, 防止多次重复请求数据```jsx
// redux-saga创建xx.saga.js, 异步获取商品数据实战
import { takeLatest,call,put } from 'redux-saga/effects';
import { shopActionTypes } from './shop.types';
import { firestore,convertCollectionsSnapshotToMap } from '../../firebase/firebase.config';
import { axiosCollectionsFailure, axiosCollectionsSuccess } from './shop.action';

// 0. yield的在saga中的作用
    // a) yield在saga中代表, 中间件值数据加工控制权交由redux-saga处理
    // b) 同时其功能相似与await
    // c) 甚至在生成器函数中可直接使用try{}catch(error){}套装
export function* axiosCollectionsAsync(){
    try{
        const collectionsRef = yield firestore.collection('collections');
        const snapshot = yield collectionsRef.get();

        // 因firestore在未翻墙时会返回空数据故创建此步骤,验证是否真正获取到了数据
        if (snapshot.docs.length){
       // redux-saga: yield call( 函数, 参数 ); call可配合yield执行函数
                // 0. 如果yield convertCollectionsSnapshotToMap( snapshot );则将报错
                // 1. yield call()配合来完成异步函数的执行
            const collectionMap = yield call( convertCollectionsSnapshotToMap, snapshot );

     // redux-saga: yield put( action函数 ); 用于执行redux的action函数
            yield put( axiosCollectionsSuccess( collectionMap ) );

        }else{
    // throw new Error(errorMsg)在try中创建错误,其错误信息交由catch处理
            throw new Error('获取数据失败');
        }
    }
    catch(error){
        yield put( axiosCollectionsFailure( error ) );
    }
}

// 1. takeLatest经常使用他来获取服务器的异步数据, 防止多次重复请求数据
	// a) 等上一个saga完成才能重新促发新的soga, 与delay配合最明显
export function* axiosCollectionsStart(){
    yield takeLatest( shopActionTypes.AXIOS_COLLECTIONS_START, axiosCollectionsAsync );
}
```




## redux-saga的使用方法xxx.component.jsx与redux相同


- 其实就是直接将action函数正常redux使用的方式就好```jsx
import React from 'react';
import "./shopage.styles.scss";

import { Route } from 'react-router-dom';
import { connect } from 'react-redux';

// redux-saga异步数据的使用方法与redux-thunk相同
    // a) 其实就是直接将action函数正常redux使用的方式就好
import { axiosCollectionsStart } from '../../redux/shop/shop.action';

import CollectionOverviewContainer from '../../components/collection-overview/collection-overview.container';
import CollectionpageContainer from '../collectionpage/collectionpage.container';

class ShopPage extends React.Component {

    componentDidMount(){
       const { axiosCollectionsStart } = this.props; 
       axiosCollectionsStart(); // 执行此函数获取异步数据
    }

    render(){
        const { match } = this.props;
        return (
            <div className="shop-page">
                <Route 
                    exact 
                    path={`${match.path}`} 
                    component={CollectionOverviewContainer}
                />            
                <Route 
                    path={`${match.path}/:collectionId`} 
                    component={CollectionpageContainer}
                />            
            </div>        
        );
    }
}

const mapDispatchToProps = dispatch => ({
    axiosCollectionsStart: ()=>dispatch( axiosCollectionsStart() ),
});

export default connect(null,mapDispatchToProps)(ShopPage);
```




# REDUX-SAGA工作流程概念图


### 获取异步数据流程图


当前redux-saga的流程：


1. 组件使用action函数，
2. redux-saga监听中间件促发对应的saga异步函数，
3. 加工处理后数据返回中间件
4. 中间件传输给reducer
4. reducer在反应给组件![Untitled.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872938918-924bafd2-fa94-4fc3-ab8d-ff5b79ba87a7.png#align=left&display=inline&height=792&margin=%5Bobject%20Object%5D&name=Untitled.png&originHeight=792&originWidth=1320&size=173393&status=done&style=none&width=1320)
### 验证用户登陆流程图![Untitled 1.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872947956-34803245-7cd8-4aa8-8a3c-aacccfd190b3.png#align=left&display=inline&height=792&margin=%5Bobject%20Object%5D&name=Untitled%201.png&originHeight=792&originWidth=1320&size=155743&status=done&style=none&width=1320)
# 实战2


- 标准的XX.SAGA.JS构建



1. 配置redux-saga | store.js配置redux-saga```jsx
import { createStore, applyMiddleware } from 'redux';
import logger from 'redux-logger';
import thunk from 'redux-thunk';

// 导入配置redux-saga必备组件
import createSagaMiddleware from 'redux-saga';
import rootSaga from './root-sagas'; // 注意: 其它saga一起整理到了此文件

import { persistStore } from 'redux-persist';
import rootReducer from './root-reducer';

const sagaMiddleware = createSagaMiddleware(); // redux-saga配置中间件( redux-saga )

// 中间件: 因为中间件后期要添加很多,所以需要解构符来配合
const middlewares = [ sagaMiddleware ];

if( process.env.NODE_ENV === 'development' ){
    middlewares.push(logger);
}

const store = createStore( rootReducer, applyMiddleware(...middlewares) );

sagaMiddleware.run( rootSaga ); // 执行saga函数( redux-saga )

const persistor = persistStore(store); // 处理store
export { store, persistor }; // 导出persistor
```

2. xxx.type.js```jsx
// 总体来说就是，开始start，成功success, 失败failure.3种状态
export const UserActionTypes = {
    SET_CURRENT_USER: 'SET_CURRENT_USER',
    GOOGLE_SIGN_IN_START: 'GOOGLE_SIGN_IN_START',
    EMAIL_SIGN_IN_START: 'EMAIL_SIGN_IN_START',
    SIGN_IN_SUCCESS: 'SIGN_IN_SUCCESS',
    SIGN_IN_FAILURE: 'SIGN_IN_FAILURE',
    CHECK_USER_SESSION: 'CHECK_USER_SESSION',
    SIGN_OUT_START: 'SIGN_OUT_START',
    SIGN_OUT_SUCCESS: 'SIGN_OUT_SUCCESS',
    SIGN_OUT_FAILURE: 'SIGN_OUT_FAILURE',
    SIGN_UP_START: 'SIGN_UP_START',
    SIGN_UP_SUCCESS: 'SIGN_UP_SUCCESS',
    SIGN_UP_FAILURE: 'SIGN_UP_FAILURE',
}
```

3. xxx.action.js```jsx
//Action发挥着改变redux的重要作用
import { UserActionTypes } from './user.types';

export const setCurrentUser = user => ({
    type: UserActionTypes.SET_CURRENT_USER,
    payload: user,
});

export const googleSignInStart = () => ({
    type: UserActionTypes.GOOGLE_SIGN_IN_START,
});

export const emailSignInStart = data => ({
    type: UserActionTypes.EMAIL_SIGN_IN_START,
    payload:data,
});

export const signInSuccess = data => ({
    type: UserActionTypes.SIGN_IN_SUCCESS,
    payload: data,
});

export const signInFailure = error => ({
    type: UserActionTypes.SIGN_IN_FAILURE,
    payload: error,
});

export const checkUserSession = () => ({
    type: UserActionTypes.CHECK_USER_SESSION,
});

export const signOutStart = () => ({
    type: UserActionTypes.SIGN_OUT_START,
});

export const signOutSuccess = () => ({
    type: UserActionTypes.SIGN_OUT_SUCCESS,
});

export const signOutFailure = error => ({
    type: UserActionTypes.SIGN_OUT_FAILURE,
    payload: error,
});

export const signUpStart = data => ({
    type: UserActionTypes.SIGN_UP_START,
    payload: data,
});

export const signUpSuccess = data => ({
    type: UserActionTypes.SIGN_UP_SUCCESS,
    payload: data,
});

export const signUpFailure = error => ({
    type: UserActionTypes.SIGN_UP_FAILURE,
    payload: error,
});
```

4. xxx.reducer.js```jsx
import { UserActionTypes } from './user.types';

// 初始化state
const INITIAL_STATE = {
    currentUser: null,
    error: null,
}

const userReducer = ( state = INITIAL_STATE, action ) => {
    switch( action.type ){
        case UserActionTypes.SET_CURRENT_USER:
            return{
                ...state,
                currentUser: action.payload,
            }
        case UserActionTypes.GOOGLE_SIGN_IN_START:
            return{
                ...state,
            }
        case UserActionTypes.EMAIL_SIGN_IN_START:
            return{
                ...state,
            }
        case UserActionTypes.SIGN_IN_SUCCESS:
            return{
                ...state,
                currentUser: action.payload,
                error: null,
            }
        case UserActionTypes.SIGN_OUT_FAILURE:
        case UserActionTypes.SIGN_IN_FAILURE:
        case UserActionTypes.SIGN_UP_FAILURE:
            return{
                ...state,
                error: action.payload,
            }
        case UserActionTypes.CHECK_USER_SESSION:
            return{
                ...state,
            }
        case UserActionTypes.SIGN_OUT_START:
            return{
                ...state,
            }
        case UserActionTypes.SIGN_OUT_SUCCESS:
            return{
                ...state,
                currentUser: null,
                error: null,
            }
        case UserActionTypes.SIGN_UP_START:
            return{
                ...state,
            }
        case UserActionTypes.SIGN_UP_SUCCESS:
            return{
                ...state,
            }
        default:
            return state;
    }
}

export default userReducer;
```

5. xxx.saga.js
   - saga函数继承action的所有传输的参数/action传输数据到saga
```jsx
/** 用户登陆/用户退出redux-saga实战 - 标准xx.saga.js写法 */

import { takeLatest,put,all,call } from 'redux-saga/effects';
import { UserActionTypes } from './user.types';
import { auth,googleProvider,createUserProfileDocument,getCurrentUser } from '../../firebase/firebase.config';
import { signInSuccess,signInFailure,signOutSuccess,signOutFailure,signUpSuccess,signUpFailure } from './user.actions';

// 加工处理获取的账户信息
export function* unsubscribeFromAuth( user ){
    try{
        const userRef = yield createUserProfileDocument(user);
        const userRefSnapshot = yield userRef.get();
        yield put( 
            signInSuccess({ id: userRefSnapshot.id, ...userRefSnapshot.data() }) 
        );
    }
    catch(error){
        yield put( signInFailure(error) );
    }
}

export function* googleSignIn(){
    try{
       yield googleProvider.setCustomParameters({ prompt: 'select_account' });
       const {user} = yield auth.signInWithPopup(googleProvider);
       yield unsubscribeFromAuth( user );
    }
    catch(error){
       yield put( signInFailure(error) );
    }
}

// redux-saga: saga函数继承action的所有传输的参数/action传输数据到saga
    // 0. 简单说: saga函数可以读取对应的action参数,type和payload
export function* emailSignIn(data){
    const { payload:{ email, password } } = data;
    try{
        const { user } = yield auth.signInWithEmailAndPassword( email, password );
        yield unsubscribeFromAuth(user);
    }
    catch(error){
        yield put( signInFailure(error) );
    }
}

export function* checkUserSessiosStart(){
    try{
        const userAuth = yield getCurrentUser();
        if( !userAuth ) return; // 当userAuth为空时,代表最近当前本地没有用户登陆
        yield unsubscribeFromAuth( userAuth );
    }
    catch(error){
        yield put( signInFailure(error) );
    }
}

export function* signOut() {
    try{
        yield auth.signOut();
        yield put( signOutSuccess() );
    }
    catch(error){
        yield put( signOutFailure(error) );   
    }
}

export function* signUpStart(data) {
    const { payload: { email, password, displayName } } = data;
    try{
        let createUser = yield auth.createUserWithEmailAndPassword( email, password );
        const userRef = yield createUserProfileDocument( createUser.user, {displayName} );
        yield put( signUpSuccess() );
        yield alert('注册成功');

        // 注册成功自动登陆
        const userRefSnapshot = yield userRef.get();
        yield put( 
            signInSuccess({ id: userRefSnapshot.id, ...userRefSnapshot.data() }) 
        );
    }
    catch(error){
        yield alert(error.message);
        yield put( signUpFailure(error) );
    }
}

export function* onGoogleSignInStart(){
    yield takeLatest( UserActionTypes.GOOGLE_SIGN_IN_START, googleSignIn );
}

export function* onEmailSignInStart(){
    yield takeLatest( UserActionTypes.EMAIL_SIGN_IN_START, emailSignIn );
}

export function* onCheckUserSession(){
    yield takeLatest( UserActionTypes.CHECK_USER_SESSION, checkUserSessiosStart );
}

export function* onSignOutStart(){
    yield takeLatest( UserActionTypes.SIGN_OUT_START, signOut );
}

export function* onSignUpStart() {
    yield takeLatest( UserActionTypes.SIGN_UP_START, signUpStart );
}

// 此耐每一个xxx.saga.js必备,方便在root-saga.js登记
	// 关于监听type促发saga函数的都要在此调用才有效, 初用时老忘记此步骤
export function* userSaga() {
    yield all([
        call(onGoogleSignInStart),
        call(onEmailSignInStart),
        call(onCheckUserSession),
        call(onSignOutStart),
        call(onSignUpStart),
    ]);
}
```

6. root-sagas.js 登记 xxx.saga.js```jsx
import { call, all } from 'redux-saga/effects'
import { axiosCollectionsStart } from './shop/shop.saga';
import { userSaga } from './user/user.saga';
import { cartSaga } from './cart/cart.saga';

// redux-saga: all([ 多个call函数 ])可直接监听执行大量saga函数
 // 0. all方法
    export default function* rootSaga(){
        yield all([
            call(axiosCollectionsStart),
            call(userSaga),
            call(cartSaga),
        ]);
    }
 // 1. 普通方法
    /*
    export default function* rootSaga(){
        yield axiosCollectionsStart;
        yield xxx;
    }
    */
```

7. 使用: saga函数```jsx
import React from 'react';
import "./sign-up.style.scss";

import FormInput from '../form-input/form-input.component';
import CustomButtonExp from '../custom-button-exp/custom-button-exp.component';

// 像往常一样调用action即可促发对应的redux-saga函数
import { connect } from 'react-redux';
import { signUpStart } from '../../redux/user/user.actions';

class SignUp extends React.Component {
    constructor( props ){
        super( props );
        this.state = {
            email: '',
            password: '',
            displayName: '',
            confirmPassword: '',
        }
    }

// 提交表单会促发此函数，注意from标签中的onSubmit={this.handleSubmit}
    handleSubmit = async cur => {
        cur.preventDefault();
        const { displayName, email, password, confirmPassword } = this.state;
        const { signUpStart } = this.props;

        if( password !== confirmPassword ){
            alert('二者密码不相同,请重新输入');
            return;
        }

        await signUpStart( email, password, displayName ); // 在redux-saga中创建账户

        this.setState({
            email: '',
            password: '',
            displayName: '',
            confirmPassword: '',
        });
    }

    handleChange = props => {
        const { name, value } = props.target;
        this.setState({ [name]: value });
    }

    render(){
        return (
            <div className="sign-up">
            <h2>
                注册用户
            </h2>
            <span>
                请使用邮箱和密码注册
            </span>

            <form onSubmit={this.handleSubmit} >
                 
                <FormInput 
                    label='邮箱'
                    type='email'
                    name='email'
                    handleChange={this.handleChange}
                    value={this.state.email} required="required"
                /> 
                <FormInput 
                    label='名称'
                    type='text'
                    name='displayName'
                    handleChange={this.handleChange}
                    value={this.state.displayName}
                    required
                /> 
                <FormInput 
                    label="密码" 
                    handleChange={this.handleChange} 
                    type="password" 
                    name="password" 
                    value={this.state.password} required  
                />
                <FormInput 
                    label='确认密码'
                    type='password'
                    name='confirmPassword'
                    handleChange={this.handleChange}
                    value={this.state.confirmPassword}
                    required
                />
                <CustomButtonExp 
                type="submit"
                isSignWidthStyles
                >
                    提交
                </CustomButtonExp>
            </form> 
            </div>
        );
    }

}

const mapDispatchToProps = dispatch => ({
    signUpStart: (email,password,displayName)=>dispatch(signUpStart({
        email,
        password,
        displayName,
    })),
});

export default connect(null,mapDispatchToProps)(SignUp) ;
```




# 细节补充:


## [官方文档](https://redux-saga.js.org/docs/api/)


- redux saga多监听促发 – takeLatest多监听促发
- delay与takeLatest可以配合使用，防止连续重复促发函数
- redux-saga 使用select函数配合reselect调用全局数据( 非常重要 )



## 原因: takeLatest( [ xxx,xxx,xx ],function* );


- 实战: Redux-saga-shopping中的cart.saga.js文件```jsx
import { all, put, call, takeLatest, delay, select } from 'redux-saga/effects';
import { selectUserCurrentUser } from '../user/user.selectors';

export function* pushCartItemStart(){
    yield delay(3000); // delay与takeLatest可以配合使用，防止连续重复促发函数
console.log('-----购物车发生变动-----');
const currentUser = yield select( selectUserCurrentUser ); // yield select( reselect中的函数 )可直接获取数据
console.log( “SELECT”,currentUser );
}

export function* onPushCartItemStart(){
    yield takeLatest( [ // 多监听
        CartActionsType.ADD_CART_ITEMS,
        CartActionsType.LOWER_CART_ITEMS,
        CartActionsType.DELETE_CART_ITEM
    ]
        , pushCartItemStart );
}

export function* cartSaga() {
    yield all([
        call(onPushCartItemStart),
    ]);
}
```




## RACE – 请求超时写法


```jsx
import { takeLatest, call, all, put, select, race, delay } from 'redux-saga/effects';
// Redux Saga请求超时写法 - race超时写法s
    // 0. 核心: race函数
    // 1. 模型: const { posts, timeout } = yield race({ posts: 请求数据函数, timeout: delay(设定超时时间) });
export function* fetchPeopleReduxSagaTimeOutStart(){
    try{
        const { posts, timeout } = yield race({
            posts: axios("https://swapi.py4e.com/api/people/"),    // 请求数据
            timeout: delay(1000)                                   // 超时时间设定1秒
        });
        if( posts.data ){
            console.log("成功获取数据!");
        }
    }
    catch(err){
        console.log("访问超时!");
    }
};
```
