# REDUX核心原则


1. single source of truth(单一来源)
2. state is read only （对象不会被修改）
3. changes using pure functions （只使用，函数进行修改）



### 操控逻辑：


redux专用名词，以及操控逻辑：


1. **Action ：活动**
2. **dispatcher ：收发**
3. **store ：商店**
4. **view : 视图**



![Untitled.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872773985-dc589da5-534c-471d-9043-cdfc0b4863d3.png#align=left&display=inline&height=285&margin=%5Bobject%20Object%5D&name=Untitled.png&originHeight=285&originWidth=1134&size=63560&status=done&style=none&width=1134)


### REDUX 采用MVC模式，来处理交互数据


![Untitled 1.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872783553-6ab6507b-b5d5-424a-84fb-ace20d49a33f.png#align=left&display=inline&height=493&margin=%5Bobject%20Object%5D&name=Untitled%201.png&originHeight=493&originWidth=874&size=442094&status=done&style=none&width=874)


# redux工作方式，以及与正常REACT工作方式区别


## 普通的react，state工作方式
![Untitled 2.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872798295-86d84a97-fe8b-444c-9c89-01d6573d7ffe.png#align=left&display=inline&height=720&margin=%5Bobject%20Object%5D&name=Untitled%202.png&originHeight=720&originWidth=1280&size=368108&status=done&style=none&width=1280)
![Untitled 3.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872807154-6866533e-1eec-4771-94f2-afc0f3d799e7.png#align=left&display=inline&height=720&margin=%5Bobject%20Object%5D&name=Untitled%203.png&originHeight=720&originWidth=1280&size=306782&status=done&style=none&width=1280)
## redux工作方式![Untitled 4.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872830434-ba59eb9b-7f66-4a6b-865f-bcf7b89b0177.png#align=left&display=inline&height=720&margin=%5Bobject%20Object%5D&name=Untitled%204.png&originHeight=720&originWidth=1280&size=353918&status=done&style=none&width=1280)
## redux具体工作流程![Untitled 5.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872845098-d47cd850-8e6a-4d00-9091-f518cc4c69f4.png#align=left&display=inline&height=720&margin=%5Bobject%20Object%5D&name=Untitled%205.png&originHeight=720&originWidth=1280&size=109345&status=done&style=none&width=1280)
## redux概念流程图![Untitled 6.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872856606-f8877f50-f821-4ead-bf89-d3d4f6b762dc.png#align=left&display=inline&height=720&margin=%5Bobject%20Object%5D&name=Untitled%206.png&originHeight=720&originWidth=1280&size=435281&status=done&style=none&width=1280)
## 一. redux具体文件构建
## ![Untitled 7.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872868484-83a78455-488a-4450-a2c2-17a46d16e80d.png#align=left&display=inline&height=241&margin=%5Bobject%20Object%5D&name=Untitled%207.png&originHeight=241&originWidth=221&size=8210&status=done&style=none&width=221)
## redux文件构建流程


1. 目录：root-reducer.js – 用于统计记录redux组件信息
2. 中间件:js – 构建redux商店，方便其它函数使用
3. Redux组件：分3种类型文件
   1. reducer.js: 配合actions选择处理方法
   2. actions.js: 用于改变redux数据参数
   3. Xxx.types.js: 用于保存type字符串，方便reducer/actions文件调用，也方便字符串统一管理



### 目录：root-reducer.js – 用于统计记录redux组件信息


```jsx
import { combineReducers } from 'redux';
import userReducer from './user/user.reducer'; // 引如redux组件

export default combineReducers({
    user: userReducer,
});
```


### 中间件: store.js – 构建redux商店，方便其它函数使用


1. 创建中间件: createStore( rootReducer, applyMiddleware(...middlewares) );
2. 解析: 创建中间件时,使用createStore( rootReducer目录，应用中间件函数 );
3. logger为redux记录器，方便开发查看数据变化



```jsx
import { createStore, applyMiddleware } from 'redux';
import logger from 'redux-logger';
import rootReducer from './root-reducer'; // 引入redux目录

// 中间件: 因为中间件后期要添加很多,所以需要解构符来配合
// process.end.NODE_ENV可以确定当前项目是否为生产环境
    // 0. process.env.NODE_ENV === 'development': 为开发环境
    // 1. process.env.NODE_ENV === 'production': 为生产环境
const middlewares = [];
if( process.env.NODE_ENV === 'development' ){
    middlewares.push(logger);
}

const store = createStore( rootReducer, applyMiddleware(...middlewares) );
export default store;
```


### redux组件：分3种类型文件


1. XXX.reducer.js```jsx
import { UserActionTypes } from './user.types';
// 初始化state
const INITIAL_STATE = {
    currentUser: null
}

// 接受处理action值改变state 
    // 0. 根据action.type选择处理方法,方法执行使用action.payload,处理后返回改进的state
        // a) action.type验证类型
        // b) action.payload传输的数据,可以是对象,函数,数字,字符串,等数据类型。
    // 1. 都不符合则返回默认state
const userReducer = ( state = INITIAL_STATE, action ) => {
    switch( action.type ){
        case UserActionTypes.SET_CURRENT_USER:
            return{
                ...state,
                currentUser: action.payload,
            }
        default:
            return state;
    }
}
export default userReducer;
```

2. xxx.action.js
   1. **type和payload必须要有的**
      - **type：放置type字符串**
      - **payload：action.payload传输的数据,可以是对象,函数,数字,字符串,等数据类型。在没有传输值时payload参数可以忽略不写**
```jsx
import { UserActionTypes } from './user.types';

export const setCurrentUser = user => ({
    type: UserActionTypes.SET_CURRENT_USER,
    payload: user,
});
```

3. xxx.types.js```jsx
// 保存type字符串集合，方便调用修改
export const UserActionTypes = {
    SET_CURRENT_USER: 'SET_CURRENT_USER',
}
```




xxx.utils.js


```jsx
// xx.utils.js专门写入,扩展函数,多用于加工处理reducer传输的数据并返回加工后的结果
export const  addItemToCart = ( cartItems, additem ) => {
   const door = cartItems.find( cur => cur.id === additem.id );
// 如果购物车已存在物品则数量+1
   if( door ){
        return cartItems.map(
            cur => cur.id === additem.id ? { ...cur, quantity: cur.quantity + 1 } : cur 
        );
   } 
 // 解构的方法增加对象元素/解构的方法增加数组元素
// 如果购物车无物品则创建物品,并设定数量为1
   return [ ...cartItems, { ...additem, quantity: 1 } ];
}

import CartActionsType from  "./cart.types";
import { addItemToCart } from "./cart.utility";
const INITIAL_STATE = {
    hidden: false,
    cartItems: [],
}
const cartReducer = ( state = INITIAL_STATE, action ) => {
    switch( action.type ){
        case CartActionsType.TOGGLE_CART_HIDDEN:
            return {
                ...state,
                hidden: !state.hidden,
            }
        case CartActionsType.ADD_CART_ITEMS:
            return {
                ...state,
                cartItems: addItemToCart(state.cartItems, action.payload ),
            }
        default:
            return state;
    }
}
export default cartReducer;
```


xx.selectors.js(reselect框架的使用)


## 二. REDUX组件中使用方式


### 初始化REDUX


```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
/**
 * redux获取组件访问权
 */
import { Provider } from 'react-redux';
import store from './redux/store';

import './index.css';
import App from './App';

// Redux初始化标签必备,获取组件访问权
ReactDOM.render(
    // React-Redux: Provider标签一定要传递store,否则容易报错
    <Provider store={store} >
        <BrowserRouter>
            <App />
        </BrowserRouter>
    </Provider>
    ,
    document.getElementById('root')
);
```


### 核心：REACT组件交互REDUX数据


```jsx
// connect函数使react组件可以访问redux存储
import { connect } from 'react-redux';

// React-Redux: connect()函数,用于交互redux数据
    // 0. connect( mapStateToProps, mapDispatchToProps );
  // 1. mapStateToProps: 用于获取redux数据,默认值为null
        // a) store更新的任何时候mapStateToProps都将被调用,换句话说保持数据更新
        // b) 他返回必须是一个对象类型
        // c) 使用方式：
            // 0. class获取传输的Redux数据:
                    import { connect } from 'react-redux';
                    class xxx {
                        this.props.currentUser; // 通过this.props.xxxx获取
                    }

                    const mapStateToProps = state => ({
                        currentUser: state.user.currentUser,
                    });

                    export default connect(mapStateToProps)(App);
                
            // 1.函数传输的Redux数据
                    import { connect } from 'react-redux';
                    const Header = ({ currentUser }) => {
                        currentUser; // 需要函数接受一下对应变量名称，即可正常使用
                    }
                    const mapStateToProps = state => ({
                        currentUser: state.user.currentUser,
                    });

                    export default connect(mapStateToProps)(Header);
                 
  // 2. mapDispatchToProps: 用于传输参数并执行Actions从而改变redux数据
        // a) 调度redux对应组件actions函数 
        // b) dispatch( actions函数 )，让redux知道我们要执行actions函数,用于改变数据
        // c) 使用方式: - 与mapStateToProps()相同，函数直接传入actions名称，对象通过this.props传入
                import { connect } from 'react-redux';
                import { setCurrentUser } from './redux/user/user.actions';
c
                const mapDispatchToProps = dispatch => ({
                    setCurrentUser: user => dispatch( setCurrentUser(user) ),
                });

                export default connect(null, mapDispatchToProps)(App)
```


### redux的dispatch()可以直接传递使用-便捷式使用dispatch


- withrouter与redux的connect嵌套并不冲突```jsx
import React from 'react';
import "./cart-dropdown.styles.scss";

import { createStructuredSelector } from 'reselect';
import { selectCartItems } from '../../redux/cart/cart.selectors';

import CustomButton from '../custom-button/custom-button.component';
import CartItem from '../cart-item/cart-item.component';

import { withRouter } from 'react-router-dom';
import {connect} from 'react-redux';
import { toggleCartHidden } from '../../redux/cart/cart.actions';

// redux的dispatch()可以直接传递使用-便捷式使用dispatch
    // 0. redux的dispatch()可以直接传递使用
    // 1. 并且在只有mapStateToProps()的情况下
    // 2. dispatch()函数可以直接执行actions中的函数，非常方便
const CartDropdown = ({ cartItems, history, dispatch }) => {
    return (
        <div className="cart-dropdown">
            <div className="cart-items">
                {
                    cartItems.length ? 
                    cartItems.map( cur => (<CartItem key={cur.id} item={cur} />) ) :
                    (<span className="cart-items-alt" >你购物车是空的!</span>)
                }
            </div>
            <CustomButton 
            onClick={ ()=>{
                dispatch(toggleCartHidden()); // 直接执行redux actions 函数
                history.push('/checkout');
            } } 
            selfCss={
                'cart-dropdown-btn'
            } >
                结算
            </CustomButton>
        </div>
    );
}

const mapStateToprops = createStructuredSelector({
    cartItems: selectCartItems,
})

// withRouter与redux的connect嵌套并不冲突
    // 0. 正常使用即可
export default withRouter(connect(mapStateToprops)(CartDropdown));
```
```jsx
// 接受单个对象的解构方法
const CartItem = ({ item: { imageUrl, name, price, quantity } }) => (
    <div className="cart-item">
        <img src={imageUrl} alt={name}/>
    </div>
);

// 相当于
const CartItem = (item) => (
    <div className="cart-item">
        <img src={item.imageUrl} alt={item.name}/>
</div>
);
```

