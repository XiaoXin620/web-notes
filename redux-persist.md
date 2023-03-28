- 配合redux浏览器本地存储变量数据
- 安装：yarn add redux-persist



## 1.redux-persist的store.js配置


```jsx
// redux-persist的store.js配置，导入必须的库
import { persistStore } from 'redux-persist';

import rootReducer from './root-reducer';

const middlewares = [ logger ];
const store = createStore( rootReducer, applyMiddleware(...middlewares) );

// redux-persist的store.js配置
const persistor = persistStore(store); // 处理store
export { store, persistor }; // 导出persistor
```


## 2. redux-persist的root-reducer.js配置


```jsx
import { combineReducers } from 'redux';

// redux-persist的root-reducer.js配置，导入必须的库
import { persistReducer } from 'redux-persist';
import storage from 'redux-persist/lib/storage';

import userReducer from './user/user.reducer';
import cartReducer from './cart/cart.reducer';
import modalReducer from './modal/modal.reducer';
import direReducer from './dire/dire.reducer';
import shopReducer from './shop/shop.reducer';

// redux-persist基本信息配置
const persistConfig = {
    key: 'root', // 设定存储的开始位置
    storage, // 导入存储库
    whitelist: ['cart'], // 控制核心：白名单,放置本地存储的变量目标,以字符串的形式
}

const rootReducer = combineReducers({
    user: userReducer,
    cart: cartReducer,
    modal: modalReducer,
    dire: direReducer,
    shop: shopReducer,
});

// redux-persist要经过persistReducer( redux-persist配置, redux目录 )
    // 0. 处理后导出rootReducer
    // 1. 所以说,接收方依然import rootReducer from 'xxx/root-reducer';正常接受即可
export default persistReducer( persistConfig, rootReducer );
```


## 3.redux-persist在index.js配置


```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { Provider } from 'react-redux';

// redux-persist在index.js配置
    // 0. PersistGate标签 
        // a) 将渲染的APP标签嵌套在PersistGate标签中,并将persistor信息传入此标签
        // b) 方便redux-persist抓取目标变量进行处理
import { PersistGate } from 'redux-persist/integration/react';
import { store, persistor } from './redux/store';

import './index.css';
import App from './App';

ReactDOM.render(
    <Provider store={store} >
        <BrowserRouter>
            <PersistGate persistor={persistor} >
                <App />
            </PersistGate>
        </BrowserRouter>
    </Provider>
    ,
    document.getElementById('root')
);
```
