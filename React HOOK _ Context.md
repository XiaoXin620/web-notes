# HOOK


- 使用前提：react版本 >= 16.8.6
- Hooks直接索引即可： import React, { useState, useEffect } from ‘react’;



## HOOKS的函数实践测试


### useState使用方式


```jsx
/**
* hooks的函数实践测试
 * 0. hooks的注意事项:
 *    a) 不能在class中使用: 因为hooks目的之一就是为了避免使用class, 降低代码的复杂度
 *    b) 代替"React生命周期": 当组件随着功能增, 其在生命周期的代码复杂度增加, 用于代替react默认的生命周期, 降低代码复杂度.
 * 1. useState使用方式
 *    a) 创建: const [ xxx, setXxx ] = useState('默认值'); 这里使用了数组的解构
 *    b) 使用: setXxx('改变的值'): 使用此函数后xxx的值将改变
 */
import React, { useState, useEffect } from 'react';
import Card from '../card/card.component';

const UseStateExample = () => {
* useState使用方式
  const [ name, setName ] = useState('ZTaer');
  const [ address, setAddress ] = useState('山东菏泽');

  /**
 * useEffect使用方式
   *    a) useEffect的注意事项:
   *      0. 不能将useEffect嵌套在if等大括号内
   *      1. 必须在"组件函数"的顶级位置
   *    b) useEffect的3种情况:
   *      0. useEffect( ()=>{}, [xxx] )
   *        a) 当xxx发生改变时,促发useEffect的函数
   *        b) 组件初始化时,也将促发
   *      1. useEffect( ()=>{}, [] )
   *        a) 当组件初始化时, 促发一次useEffect的函数
   *        b) 生命周期: 相当于react的生命周期componentDidMount()
   *      2. useEffect( ()=>{} )
   *        a) 组件中每一次改变都会促发useEffect的函数
   *        b) 谨慎使用, 容易造成组件的死循环
   *        c) 如果要求"数据保持实时最新"可以使用,但依然要谨慎使用,因为他使程序的可控性降低
   *    c) 使用地方:
   *      0. 通常使用在监听目标, 做出反应
   */
  useEffect( ()=>{
    console.log('目标值改变时/初始化时,促发');
  },[name,address] );

  useEffect( ()=>{
    console.log('初始化时促发');
  },[] );

  useEffect( ()=>{
    console.log('每一次改动都将促发');
  } );

  return(
    <Card>
      <h1> {name} </h1>
      <h1> {address} </h1>
      <button onClick={ ()=>setName('OO7__ZTaer') } >Set Name to Andrei</button> // setXxxx(‘改变xxx变量的数据’)
      <button onClick={ ()=>setAddress('牡丹区') } >Set Address</button>
    </Card>
  );
};

export default UseStateExample;
```


### useEffect异步用法


```jsx
import React, { useState, useEffect } from 'react';
import axios from 'axios';

import Card from '../card/card.component';

const UseEffectExample = () => {
  const [ user, setUser ] = useState(null);
  const [ searchContent, setSearchContent ] = useState('Bret');

  /**
 useEffect异步用法( 等待笔记 )
      a) useEffect的注意事项:
        0. 不能将useEffect嵌套在if等大括号内
        1. 必须在"组件函数"的顶级位置
      b) 使用地方:
        0. 代替react生命周期
        1. 监听指定变量，做出响应
  */
  useEffect( ()=>{
    if( searchContent.length > 0 ){ // useEffect只能在组件函数顶级位置,所以过滤条件要在之内
      const searchAsync = async () => {
        try{
          const content = await axios(`https://jsonplaceholder.typicode.com/users?username=${searchContent}`);
          await setUser( content.data[0] );
        }
        catch(error){
          console.log( error );
        }
      }
      searchAsync();
    }

  }, [searchContent] );

  return (
    <Card>
      <input
        type='search'
        value={ searchContent }
        onChange={ cur => setSearchContent( cur.target.value )  } // 注意onChange的使用方式
      />
      {user ? (
        <div>
          <h3>{user.name}</h3>
          <h3> {user.username} </h3>
          <h3> {user.email} </h3>
        </div>
      ) : (
        <p>No user found</p>
      )}
    </Card>
  );
};

export default UseEffectExample;
```


## HOOKS下useState实战


### HOOKS版代替this.state


```jsx
import React,{ useState } from "react";
import "./sign-in.style.scss";

import FormInput from "../form-input/form-input.component";
import CustomButtonExp from "../custom-button-exp/custom-button-exp.component";

import { connect } from 'react-redux';
import { googleSignInStart,emailSignInStart } from '../../redux/user/user.actions';

const SignIn = ( { googleSignInStart, emailSignInStart } ) => {
 // hooks下useState实战 – 代替this.state
    const [ userState, setUserState ] = useState( { email: '', password: '' } );

    const handleSubmit = async cur => {
        cur.preventDefault();    
        const { email, password } = userState;
        emailSignInStart( email, password );
    }

    const handleChange = cur => {
        const { value, name } = cur.target;

      // hooks中对象增加元素时实现方式 – 其实利用的解构的方法
        setUserState( { ...userState ,[name]: value} );
    }
    return(
        <div className="sign-in">
            <h2>
                登陆用户
            </h2>
            <span>
                请使用邮箱和密码登陆
            </span>

            <form onSubmit={handleSubmit}>
                <FormInput 
                    label="邮箱" 
                    handleChange={handleChange} 
                    type="email" 
                    name="email" 
                    value={userState.email} required  
                />

                <FormInput 
                    label="密码" 
                    handleChange={handleChange} 
                    type="password" 
                    name="password" 
                    value={userState.password} required  
                />
                <div className="btn-sign">
                    <CustomButtonExp isSignWidthStyles type="submit" >
                        登陆
                    </CustomButtonExp>
                    <CustomButtonExp type="button" isGoogleStyles onClick={ googleSignInStart } >
                        Google登陆
                    </CustomButtonExp>
                </div>
            </form>
        </div>
    );
}

const mapDispatchToProps = dispatch => ({
    googleSignInStart: ()=>dispatch( googleSignInStart() ),
    emailSignInStart: ( email,password )=>dispatch( emailSignInStart({email,password}) ) ,
});

export default connect(null,mapDispatchToProps)(SignIn) ;
```


### 非HOOKS版代替this.state


```jsx
import React from "react";
import "./sign-in.style.scss";

import FormInput from "../form-input/form-input.component";
import CustomButtonExp from "../custom-button-exp/custom-button-exp.component";

import { connect } from 'react-redux';
import { googleSignInStart,emailSignInStart } from '../../redux/user/user.actions';

class SignIn extends React.Component {
    constructor(props){
        super(props);
        this.state = {
            email:'',
            password:'',
        };
    }

    handleSubmit = async cur => {
        cur.preventDefault();    
        const { email, password } = this.state;
        const { emailSignInStart } = this.props;
        emailSignInStart( email, password );
    }

    // React处理input改变数据( 完成笔记 )
        // 0. React中,input必须要有onChange={xxx}来处理表单数据,否则会报错
        // 1. input准备: <input type="email" onChange={this.handleChange} />
        // 2. 函数准备: 如下 - cur为接受的input数据
    handleChange = cur => {
        const { value, name } = cur.target;

        // 变量值变为对象的键值( 完成笔记 )
            // 0. this.setState( { [name]: value } ); 
            // 1. 其中[name]的意思为，将name的变量值作为键值,方便键值动态变化。
            // 2. 比如name="password"那么相当于this.setState( password: value );
        this.setState( {[name]: value} );
    }

    render(){
        const { googleSignInStart } = this.props;
        return(
            <div className="sign-in">
                <h2>
                    登陆用户
                </h2>
                <span>
                    请使用邮箱和密码登陆
                </span>

                <form onSubmit={this.handleSubmit}>
                    <FormInput 
                        label="邮箱" 
                        handleChange={this.handleChange} 
                        type="email" 
                        name="email" 
                        value={this.state.email} required  
                    />

                    <FormInput 
                        label="密码" 
                        handleChange={this.handleChange} 
                        type="password" 
                        name="password" 
                        value={this.state.password} required  
                    />
                    <div className="btn-sign">
                        <CustomButtonExp isSignWidthStyles type="submit" >
                            登陆
                        </CustomButtonExp>
                        <CustomButtonExp type="button" isGoogleStyles onClick={ googleSignInStart } >
                            Google登陆
                        </CustomButtonExp>
                    </div>
                </form>
            </div>
        );
    }

}

const mapDispatchToProps = dispatch => ({
    googleSignInStart: ()=>dispatch( googleSignInStart() ),
    emailSignInStart: ( email,password )=>dispatch( emailSignInStart({email,password}) ) ,
});

export default connect(null,mapDispatchToProps)(SignIn) ;
```


## useEffect代替REACT生命周期实战


```jsx
import React, { useEffect } from 'react';
import { Switch,Route,Redirect } from 'react-router-dom';

import Header from './components/header/header.component';
import HomePage from './pages/homepage/homepage.component';
import ShopPage from './pages/shoppage/shoppage.component';
import SignPage from './pages/signpage/signpage.component';
import CheckoutPage from './pages/checkout/checkout.component';
import TelPage from './pages/telpage/telpage.component';

import { connect } from 'react-redux';
import { setCurrentUser, checkUserSession } from './redux/user/user.actions';

import { createStructuredSelector } from 'reselect';
import { selectUserCurrentUser } from './redux/user/user.selectors';

import { selectCollectionShopArray } from './redux/shop/shop.selectors';

const App = ({ checkUserSession, currentUser }) => {

  /**
 * useEffect代替react生命周期
   *    0. componentDidMout
   *    1. componentWillUnmout
   *      a) 注意return的为一个函数,在函数内才属于此生命周期范围
   *      b) return ()=>{ 此内 }
   */
  useEffect( ()=>{
 // 这里相当于: componentDidMout;
    checkUserSession();
    
    return () => {
   		// 这里相当于: componentWillUnmout;
      	console.log('useEffect代替componentWillUnmout');
    }

  },[ checkUserSession ] );

  return(
    <div className="App">
      <Header />
      <Switch>
        <Route exact path='/' component={HomePage} />
        <Route path='/shop' component={ShopPage} />
        <Route exact path='/sign' render={ ()=> currentUser ? <Redirect to='/' /> : <SignPage />  } />
        <Route exact path='/checkout' component={CheckoutPage} />
        <Route path='/tel' component={TelPage} />
      </Switch>
    </div>
  );

}

const mapStateToProps = createStructuredSelector ({
  currentUser: selectUserCurrentUser,
  collectionsArray: selectCollectionShopArray,
});

const mapDispatchToProps = dispatch => ({
  setCurrentUser: user => dispatch( setCurrentUser(user) ),
  checkUserSession: () => dispatch( checkUserSession() ),
});

export default connect(mapStateToProps,mapDispatchToProps)(App);
```


## 构建: 自定义HOOKS容器实战 – 可以理解为”可重复使用的HOOKS函数”


### 文件结构
![Untitled.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872509962-0ba5e9cb-899d-46c8-a7ec-f9499e029b40.png#align=left&display=inline&height=124&margin=%5Bobject%20Object%5D&name=Untitled.png&originHeight=124&originWidth=249&size=7250&status=done&style=none&width=249)
### 构建: 自定义HOOKS容器实战- 获取异步数据


```jsx
/**
* 构建: 自定义hooks容器实战- 获取异步数据( 等待笔记 )
 *      0. 注意文件结构以及名称
 *          a) use-xxx.effect.js
 *      1. hooks容器,调用一次执行一次,因此无需为useEffect强制指定监听目标
 *      2. 但是为了稳定性/可控性,需要给useEffect指定目标
 */

 import { useEffect, useState } from 'react';
 import axios from 'axios';

 const useAxios = url => {
    const [ data, setData ] = useState('');
    useEffect( ()=>{
        const content = async () => {
        try{
            const axiosContent = await axios(url);
            await setData( axiosContent.data[0] );
        }
        catch(error){
            console.log(error);
        }
        }
        content();
    },[url]);
    return data;
 }

 export default useAxios;
```


### 使用: 自定义HOOKS容器


```jsx
import React from 'react';
import Card from '../card/card.component';
import useAxios from '../../effects/use-axios.effect';

const User = ({ userId }) => {
// 使用: 自定义hooks容器
  const user = useAxios(
    `https://jsonplaceholder.typicode.com/users?id=${userId}`
  );

  return (
    <Card>
      {user ? (
        <div>
          <h3>{user.username}</h3>
          <p>{user.name}</p>
        </div>
      ) : (
        <p>User not found</p>
      )}
    </Card>
  );
};

export default User;
```


### 构建HOOKS中的reducer


```jsx
import React, { useEffect, useReducer } from 'react';
import Card from '../card/card.component';

/**
* 构建hooks中的reducer
 *   0. hooks的reducer必须在同一个文件内,不可分割成其它文件,否则会报错
 *   1. 其实hooks的reducer像是redux的reducer的简化版
 *   2. 感觉hooks的reducer并不建议使用,推荐使用redux的reducer
 */

// reducer部分
const INITIAL_STATE = {
  user: null,
  searchQuery: 'Bret'
};

const reducer = (state, action) => {
  switch (action.type) {
    case 'SET_USER':
      return {
        ...state,
        user: action.payload
      };
    case 'SET_SEARCH_QUERY':
      return {
        ...state,
        searchQuery: action.payload
      };
    default:
      return state;
  }
};

// action部分
const setUser = user => ({
  type: 'SET_USER',
  payload: user
});

const setSearchQuery = queryString => ({
  type: 'SET_SEARCH_QUERY',
  payload: queryString
});

// 组件部分
const UseReducerExample = () => {

  const [state, dispatch] = useReducer(reducer, INITIAL_STATE);  // 启用hooks的reducer
  console.log(state);
  const { user, searchQuery } = state; // 获取reducer中的数据

  useEffect(() => {
    const fetchFunc = async () => {
        try{
            const response = await fetch(
              `https://jsonplaceholder.typicode.com/users?username=${searchQuery}`
            );
            const resJson = await response.json();
            dispatch(setUser(resJson[0]));
        }
        catch(err){
            console.log(err);
        }
    };

    fetchFunc();
  }, [searchQuery]);

  return (
    <Card>
      <input
        type='search'
        value={searchQuery}
        onChange={event => dispatch(setSearchQuery(event.target.value))}
      />
      {user ? (
        <div>
          <h3>{user.name}</h3>
          <h3> {user.username} </h3>
          <h3> {user.email} </h3>
        </div>
      ) : (
        <p>No user found</p>
      )}
    </Card>
  );
};

export default UseReducerExample;
```


## useContext实战


### 局部使用CONTEXT实战( 不推荐 )


### 文件结构


![Untitled 1.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872543878-fe00bf9e-809d-4352-9bd7-dbee6683dc0a.png#align=left&display=inline&height=165&margin=%5Bobject%20Object%5D&name=Untitled%201.png&originHeight=165&originWidth=275&size=20177&status=done&style=none&width=275)


### 构建: xxx.context.js


```jsx
/**
* 构建: xx.context.js简易构建
 * 0. 传输数据:
 *      a) Provider: 给context传递数据
 * 1. 获取数据:
 *      a) Consumer: 获取context数据( 不推荐 )
 *      b) useContext: 获取context数据( 推荐 )
 */
import { createContext } from 'react';
import SHOP_DATA from './shop.data';

const CollectionsContext = createContext( SHOP_DATA ); // 构建context数据

export default CollectionsContext;
```


### 使用: CONTEXT获取数据的两种方法


```jsx
import React,{ useContext } from 'react';
import "./collectionpage.styles.scss";
import { withRouter } from 'react-router-dom';
import CollectionItem from '../../components/collection-item/collection-item.component';
import CollectionsContext from '../../contexts/collections/collections.context';

// 使用: context获取数据方法二 | 实战展示( 推荐 )
    // 0. hooks的方法: 使用"useContext"获取context数据
    // 1. const xxx = useContext(XXXX);
const CollectionPage = ({ match }) => {
    const collectionsData = useContext(CollectionsContext);
    const { title, items } = collectionsData[match.params.collectionId];
    return (
        <div className="collection-page">
            <h2 className="title">
                { title }
            </h2>
            <div className="items">
                {
                    items.map( item => ( <CollectionItem key={item.id} item={item} /> ) )
                }
            </div>
        </div>
    );
};

// 使用: context获取数据方法一 | 实战展示( 不推荐 )
    // 0. 模型: <XXX.Consumer> { data => return ( jsx ) } </XXX.Consumer>
const CollectionPage = ({ match }) => {
    return(
        <CollectionsContext.Consumer>
            {
                collectionsData => {
                    const { title, items } = collectionsData[match.params.collectionId];
                    return (
                        <div className="collection-page">
                            <h2 className="title">
                                { title }
                            </h2>
                            <div className="items">
                                {
                                    items.map( item => ( <CollectionItem key={item.id} item={item} /> ) )
                                }
                            </div>
                        </div>
                    );
                }
            }
        </CollectionsContext.Consumer>
    );
};

export default withRouter( CollectionPage );
```


### 传输: 局部使用provide传输改变contextT数据


```jsx
/**
 * context传递数据在局部组件使用,方法一 | ( 不推荐 )
     * 0. 推荐在xx.provider.jsx中，面向全局的context使用
     */
    // 0. <Xxxx.Provider value={传递数据位置}> 使用context数据的组件 </Xxxx.Provider>
    // 1. 就是传递数据给context

    return(
      <div className="App">
        <CurrentUserContext.Provider value={this.state.currentUser} >
          <Header />
        </CurrentUserContext.Provider>
        <Switch>
          <Route exact path='/' component={HomePage} />
          <Route path='/shop' component={ShopPage} />
          <Route exact path='/sign' render={ ()=> this.props.currentUser ? <Redirect to='/' /> : <SignPage />  } />
          <Route exact path='/checkout' component={CheckoutPage} />
        </Switch>
      </div>
    );
```


### 购物车按钮CONTEXT局部传输数据实战


```jsx
import React,{useContext,useState} from 'react';
import "./header.styles.scss";
import { Link } from "react-router-dom";
import {auth} from "../../firebase/firebase.config";
import { connect } from 'react-redux';
import  CartIcon from '../cart-icon/cart-icon.component';
import CartDropdown from '../cart-dropdown/cart-dropdown.component';
import CustomModal from '../custom-modal/custom-modal.component';
import { handleOpenModal } from '../../redux/modal/modal.actions';
import CurrentUserContext from '../../contexts/current-user/current-user.context';
import { ReactComponent as Logo } from "../../assets/crown.svg";

/**
 * 购物车按钮context实战
 */
import CartContext from '../../contexts/cart/cart.context';

const Header = () => {
    const currentUser = useContext(CurrentUserContext);
    
 // 准备传输给Context的数据: 传递数据给购物车context通过provider标签
    const [ hidden, setHidden ] = useState(false);
    const toggleHidden = () => {
        return setHidden(!hidden);
    }

    return(
        <div className="header">

            <CustomModal/>

            <Link className="logo-container" to="/" >
                <Logo className="logo" />
            </Link>
            <div className="options">
                <Link className="option" to="/" >
                    主页
                </Link> 
                <Link className="option" to="/shop" >
                    产品
                </Link>
                <Link className="option" to="/tel" >
                    联系
                </Link>
                {
                    currentUser 
                    ? 
                    ( <div className="option" onClick={ ()=>auth.signOut() } >退出</div> ) 
                    : 
                    ( <Link className="option" to="/sign" >注册/登陆</Link> )
                }
                <div className="option">
                    <CartContext.Provider value={{hidden, toggleHidden}} >
                        <CartIcon/>
                    </CartContext.Provider>
                </div>
            </div>
            {
                hidden ? <CartDropdown  /> : null
            }
        </div>
    );
}

const mapDispatchToProps = dispatch => ({
    handleOpenModal: text => dispatch(handleOpenModal(text)),
});

export default connect(null,mapDispatchToProps)(Header);
```


### 全局使用CONTEXT实战( 推荐 )


### 文件结构
![Untitled 2.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872562930-88dcb6f8-9628-4220-9fb6-6d34b7090898.png#align=left&display=inline&height=121&margin=%5Bobject%20Object%5D&name=Untitled%202.png&originHeight=121&originWidth=250&size=14170&status=done&style=none&width=250)
### 构建xxx.provider.jsx: 将context在全局使用( 推荐使用 )


```jsx
/**
* 构建xx.provider.jsx: 将context在全局使用( 推荐使用 )
 */
// 0. hooks | context 目前适合中小型项目,不过未来将适应大型项目
    //  a) 原因: 
        // 0. 完整便捷的服务已经在redux社区构建好，可以直接使用。
        // 1. 而hooks因为刚出生不久，功能扩展服务并没有redux完整。
        // 2. 但是hooks在代替class确实很好，至于context确实少些功能，比如数据的跟踪，也是可以实现不过他将比redux更加冗杂并且有些需要自己构建
// 1. xx.provider.jsx构建过程:
    // a) 导入必要组件
    // b) 构建createContext
    // c) 构建Provider

// 导入必要组件
import React,{ createContext, useState, useEffect } from 'react';
import { addItemToCart, lowerCartItem, deleteCartItem } from './cart.utility';

// 构建createContext
    // 注意: 导出用于组件,调用context数据
export const CartContext = createContext({
    hidden: false,
    toggleHidden: ()=>{}, 
    cartItems: [],
    addItem: ()=>{},
    lowerItem: ()=>{},
    deleteItem: ()=>{},
    clearItems: ()=>{},
    cartPriceTotal: 0,
    cartItemsCount: 0,
});

// 构建Provider
    // 注意: 默认导出用于全局配置,通常在app.js
const CartProvider = ({ children }) => {
    const [ hidden, setHidden ] = useState(false);
    const [ cartItems, setCartItems ] = useState([]);
    const [ cartPriceTotal, setCartPriceTotal ] =  useState(0);
    const [ cartItemsCount, setCartitemsCount ] = useState(0);

    const toggleHidden = () => setHidden(!hidden);
    const addItem = item => setCartItems(addItemToCart( cartItems, item ));
    const lowerItem = item => setCartItems(lowerCartItem( cartItems, item ));
    const deleteItem = item => setCartItems(deleteCartItem( cartItems, item));
    const itemsCount = cartItems => cartItems.reduce( (total,cur)=>total+cur.quantity,0 ); // 计算商品数量
    const clearItems = () => setCartItems([]);
    const priceTotal =  cartItems => cartItems.reduce( 
        (total,cur)=>total+cur.quantity*cur.price,
    0 ); // 计算总价格

    // 保证当商品数据发生变化时,重新计算
    useEffect( ()=>{
        setCartitemsCount( itemsCount( cartItems ));
        setCartPriceTotal( priceTotal( cartItems ) );
    }, [cartItems]);

    return <CartContext.Provider value={{
        hidden,
        toggleHidden,
        addItem,
        lowerItem,
        deleteItem,
        cartItems,
        cartPriceTotal,
        cartItemsCount,
        clearItems,
    }} >{children}</CartContext.Provider>;
}

export default CartProvider;
```


### 配置context可以在全局使用


```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import { Provider } from 'react-redux';
import { PersistGate } from 'redux-persist/integration/react';
import { store, persistor } from './redux/store';
/**
* context全局使用配置,标签顺序无要求
 */
import CartProvider from './providers/cart/cart.provider';

import './index.css';
import App from './App';
ReactDOM.render(
    <Provider store={store} >
        <CartProvider>
            <BrowserRouter>
                <PersistGate persistor={persistor} >
                    <App />
                </PersistGate>
            </BrowserRouter>
        </CartProvider>
    </Provider>
    ,
    document.getElementById('root')
);
```


### 使用全局CONTEXT调用数据/使用函数等


```jsx
import React,{useContext} from 'react';
import "./cart-icon.styles.scss";
import { ReactComponent as CartIconSvg } from "../../assets/shopping-bag.svg";
import { CartContext } from '../../providers/cart/cart.provider';

// 购物车按钮context实战: 使用context数据以及函数
const CartIcon = () => {
    const { toggleHidden, cartItemsCount } = useContext( CartContext );
    return(
        <div className="cart-icon" onClick={toggleHidden} >
            <CartIconSvg className="shopping-icon" />
            <span className="item-count">
                {cartItemsCount}
            </span>
        </div>
    );
}

export default CartIcon;
```


### provider后端获取数据写法 | 这里的getArrayData可以改写为，异步获取后端数据函数，这里使用了本地JSON数据


### 二次加工数据函数不可被传递,否则会报错


```jsx
import React,{ createContext, useState, useEffect } from 'react';
import SHOP_DATA from './shop.data';
import { objectToArray } from './shop.utilis';
 
export const ShopContext = createContext({
    collectionShop: null,
    collectionShopArray: [],
});

const ShopProvider = ({ children }) => {
    const [ collectionShop, setCollectionShop ] = useState( SHOP_DATA );
    const [ collectionShopArray, setCollectionShopArray ] = useState([]);

    const getArrayData = data => setCollectionShopArray(objectToArray( data )); // 二次加工数据函数不可被传递,否则会报错

    useEffect( ()=>{
        // Provider后端获取数据写法 | 这里的getArrayData可以改写为，异步获取后端数据函数，这里使用了本地json数据
        if( collectionShop ){
            getArrayData( collectionShop )
        }
    },[collectionShop] );

    return (
        <ShopContext.Provider value={{
            collectionShop,
            collectionShopArray,
        }} >
            {children}
        </ShopContext.Provider>
    );
}

export default ShopProvider;
```


### context注意事项:


- 在CONTEXT文件中，二次加工的数据函数，不可被传递，也就是不可以将函数，写入到PROVIDER,VALUE中，注意是加工函数不可被传递，不是加工后的数据```jsx
import React,{ createContext, useState, useEffect } from 'react';
import SHOP_DATA from './shop.data';
import { objectToArray } from './shop.utilis';

export const ShopContext = createContext({
    collectionShop: null,
collectionShopArray: [],
// getArrayData函数不可传递,因为他是加工函数
});

const ShopProvider = ({ children }) => {
    const [ collectionShop, setCollectionShop ] = useState( SHOP_DATA );
    const [ collectionShopArray, setCollectionShopArray ] = useState([]);

    const getArrayData = data => setCollectionShopArray(objectToArray( data )); // 二次加工数据函数不可被传递,否则会报错

    useEffect( ()=>{
        if( collectionShop ){
            getArrayData( collectionShop )
        }
    },[collectionShop] );

    return (
        <ShopContext.Provider value={{
            collectionShop,
            collectionShopArray,
		// getArrayData函数不可传递,因为他是加工函数
        }} >
            {children}
        </ShopContext.Provider>
    );
}

export default ShopProvider;
```
## 其他HOOK

![Untitled 3.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872586800-8e482ef3-72bd-44ec-8caa-ff52ae4df36e.png#align=left&display=inline&height=519&margin=%5Bobject%20Object%5D&name=Untitled%203.png&originHeight=519&originWidth=398&size=21440&status=done&style=none&width=398)
[官网链接](https://zh-hans.reactjs.org/docs/hooks-reference.html)
