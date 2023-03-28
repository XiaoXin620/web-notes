# 作用:


- 让异步获取数据方式在REDUX中, 通常用于获取后端API数据



## 观察者模式/承诺模式


- 观察者模式 – firebase就为它，主要是监察动态获取的异步数据
- 承诺模式 – 配合使用redyx thunk 将异步数据写入到redux中



# 配置:


- 安装: yarn add redux-thunk



## redux-thunk配置store.js


```jsx
// redux-thunk配置store.js
    // 0. 注意中间件加入 const middlewares = [thunk];
import thunk from 'redux-thunk';

// 中间件: 因为中间件后期要添加很多,所以需要解构符来配合
const middlewares = [thunk]
```


## redux-thunk使用方式: 以获取后端商品数据为例


### 配置XX.type.js


```jsx
// redux-thunk配置xx.types.js
export const shopActionTypes = {
    AXIOS_COLLECTIONS_STATE: 'AXIOS_COLLECTIONS_STATE',
    AXIOS_COLLECTIONS_SUCCESS: 'AXIOS_COLLECTIONS_SUCCESS',
    AXIOS_COLLECTIONS_FAILURE: 'AXIOS_COLLECTIONS_FAILURE',
}
```


### 配置XX.reducer.js


```jsx
import { shopActionTypes } from './shop.types';
// redux-thunk配置xx.reducer.js
    // 0. 注意获取数据的3种状态,此乃redux获取异步数据常用模式( 变量名称自定义 )
        // a) 当前数据获取状态: isAxiosing ( 注意布尔值的变化, 常用于配合加载器 )
        // b) 获取数据成功: collectionShop
        // c) 获取数据失败: errorMsg
const INITIAL_STATE = {
    collectionShop: null,
    isAxiosing: false, // 获取数据的状态
    errorMsg: undefined, 
}

const shopReducer = ( state=INITIAL_STATE, action ) => {
    switch (action.type) {
        case shopActionTypes.AXIOS_COLLECTIONS_STATE:
            return {
                ...state,
                isAxiosing: true,
            }
        case shopActionTypes.AXIOS_COLLECTIONS_SUCCESS:
            return{
                ...state,
                isAxiosing: false,
                collectionShop: action.payload,
            }
        case shopActionTypes.AXIOS_COLLECTIONS_FAILURE:
            return{
                ...state,
                isAxiosing:false,
                errorMsg: action.payload,
            }

        default:
            return state;
    }
};

export default shopReducer;
```


### 配置XX.action.js


```jsx
import { shopActionTypes } from './shop.types';

// redux-thunk配置xx.action.js
import { firestore,convertCollectionsSnapshotToMap } from '../../firebase/firebase.config';

export const axiosCollectionsState = () => ({
    type: shopActionTypes.AXIOS_COLLECTIONS_STATE,
});

export const axiosCollectionsFailure = errorMsg => ({
    type: shopActionTypes.AXIOS_COLLECTIONS_FAILURE,
    payload: errorMsg,
});

export const axiosCollectionsSuccess = data => ({
    type: shopActionTypes.AXIOS_COLLECTIONS_SUCCESS,
    payload: data,
});

// redux-thunk核心目的: 就是将获取异步数据的函数放在xx.action.js中
    // 0. redux-thunk已配置在'中间件'中, 所以可以直接使用dispatch()来执行对应的action函数
    // 1. redux thunk 在中间件中会抓取自己力所能及的事件，比如action中的dispatch函数( 核心 )
export const axiosCollectionsStateAsync = () => {
    return dispatch => {
        const collectionsRef = firestore.collection('collections');
        dispatch( axiosCollectionsState() ); // 开启获取数据状态

        const collectionMap = async () => {
            try{
                const snapshot = await collectionsRef.get();
                dispatch( axiosCollectionsSuccess( convertCollectionsSnapshotToMap( snapshot ) ) ); // 注意数据获取成功使用的action
            }
            catch( error ){
                dispatch( axiosCollectionsFailure( error ) ); // 注意数据获取失败使用的action
            };
        }

        collectionMap();
    }
}
```


### 配置XX.selectors.js


- 核心: 验证异步数据是否存在,配合加载器布尔值,防止出错
- "!!"符号: 将变量数据转布尔值类型



```jsx
import { createSelector } from 'reselect';
const selectShop = state => state.shop;

export const selectCollectionShop = createSelector(
    [selectShop],
    shop => shop.collectionShop,
);

export const selectCollectionShopArray = createSelector(
    [selectCollectionShop],
    collectionShop => collectionShop ? Object.keys(collectionShop).map( e=>collectionShop[e] ) : [] , // 防止没有数据时发生错误, 所以返回一个空数组
);

export const selectCollectionItem = collectionUrlProps => createSelector(
    [selectCollectionShop],
    collectionShop => collectionShop ? collectionShop[ collectionUrlProps ] : null  
);

// 选择失败时返回的数据
export const selectCollectionsFailure = createSelector(
    [selectShop],
    shop => shop.errorMsg,
);

// 选择异步获取数据的状态
export const selectCollectionsState = createSelector(
    [selectShop],
    shop => shop.isAxiosing,
);

// redux-thunk配置xx.selectors.js验证异步数据是否存在,配合加载器布尔值,防止出错
 // 0. "!!"符号: 将变量数据转布尔值类型
        // a) !!"xxx" --> true; 
        // b) !!null --> false;
    // 1. 此时当数据存在时返回false隐藏加载器, 否则相反
export const selectCollectionsLoaded = createSelector(
    [selectCollectionShop],
    collectionShop => !( !!collectionShop ),
)
```


### 异步数据的使用


```jsx
import React from 'react';
import "./shopage.styles.scss";
import { Route } from 'react-router-dom';
import CollectionOverView  from '../../components/collection-overview/collection-overview.component';
import CollectionPage from '../collectionpage/collectionpage.component';
import { connect } from 'react-redux';

// redux-thunk异步数据的使用
import { axiosCollectionsStateAsync } from '../../redux/shop/shop.action';
import { createStructuredSelector } from 'reselect';
import { selectCollectionsLoaded, selectCollectionsState } from '../../redux/shop/shop.selectors';

// react加载器的使用
import WithSpinner from '../../components/with-spinner/with-spinner.component';
// react加载器: 要使用加载器的组件准备
const CollectionOverViewWithSpinner = WithSpinner( CollectionOverView );
const CollectionPageWithSpinner = WithSpinner( CollectionPage );

class ShopPage extends React.Component {

    componentDidMount(){
       const { axiosCollectionsStateAsync } = this.props; 
       axiosCollectionsStateAsync(); // 促发redux异步获取数据( 核心 )
    }

    render(){
        const { match, isCollectionsAxiosing, isCollectionsLoad } = this.props;
        return (
            <div className="shop-page">
                <Route 
                    exact 
                    path={`${match.path}`} 
                    render={
                        props => ( <CollectionOverViewWithSpinner isLoading={ isCollectionsAxiosing  } {...props} /> )
                    }
                />            
                <Route 
                    path={`${match.path}/:collectionId`} 
                    render={
                        props => ( <CollectionPageWithSpinner isLoading={ isCollectionsLoad } {...props} /> )
                    }
                />            
            </div>        
        );
    }
}

// 使用redux基本配置
const mapStateToProps = createStructuredSelector({
    isCollectionsAxiosing: selectCollectionsState, // 异步获取数据的状态信息, 配合加载器使用
    isCollectionsLoad: selectCollectionsLoaded, // 为更好的配合加载器所用,避免发生错误( 更好的兼容性 )
});

const mapDispatchToProps = dispatch => ({
    axiosCollectionsStateAsync: ()=>dispatch( axiosCollectionsStateAsync() ),
});

export default connect(mapStateToProps,mapDispatchToProps)(ShopPage);
```
