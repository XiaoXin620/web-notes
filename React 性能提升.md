# 配置代码拆分( React Lazy+ supeans+ Error Boundary)


## 先配置React Lazy+ supeans使组件按需加载


### 目的：使程序按需加载组件


- 注意: react lazy要与create-react-router路由组件配合```jsx
import React, { useEffect, lazy, Suspense } from 'react';
import { Switch,Route,Redirect } from 'react-router-dom';
import { GlobalStyle } from './global.styles'; // 使用作用于全局的css( 完成笔记 )
import Header from './components/header/header.component';
import Spinner from './components/spinner/spinner.component';
import { connect } from 'react-redux';
import { setCurrentUser, checkUserSession } from './redux/user/user.actions';
import { createStructuredSelector } from 'reselect';
import { selectUserCurrentUser } from './redux/user/user.selectors';
import { selectCollectionShopArray } from './redux/shop/shop.selectors';

// 使用: 抓取错误组件
import ErrorBoundary from './components/error-boundary/error-boundary.component';

// React Lazy + Suspense代码拆分,性能提升
    // 0. 目的: 
        // a) ReactLazy: 代码拆分成块,只加载所需的代码,不加载多余的代码,使我们的程序初试加载使更加迅速
        // b) Suspense: 精确抓取组件错误,同时给ReactLazy擦屁股,因为使用ReactLazy会出现错误
        // c) 适合大型项目
    // 1. 使用:
        // a) ReactLazy:
            // 0. 要与路由组件配合
            // 1. 被Lazy处理过的组件，要嵌套在Suspense组件内, 否则将出错
            // 2. 模型: const HomePage = lazy( () => import('./pages/homepage/homepage.component') );
        // b) Suspense:
            // 0. 模型: <Suspense fallback={<Spinner />} > 嵌套路由标签，渲染被lazy加工过的组件 </Suspense> 
const HomePage = lazy( () => import('./pages/homepage/homepage.component') );
const ShopPage = lazy( () => import('./pages/shoppage/shoppage.component') );
const SignPage = lazy( () => import('./pages/signpage/signpage.component') );
const CheckoutPage = lazy( () => import('./pages/checkout/checkout.component') );
const TelPage = lazy( () => import('./pages/telpage/telpage.component') );

const App = ({ checkUserSession, currentUser }) => {
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
      <GlobalStyle />
      <Header />
      <Switch>
        <ErrorBoundary>
          <Suspense fallback={<Spinner />} >
            <Route exact path='/' component={HomePage} />
            <Route path='/shop' component={ShopPage} />
            <Route exact path='/sign' render={ ()=> currentUser ? <Redirect to='/' /> : <SignPage />  } />
            <Route exact path='/checkout' component={CheckoutPage} />
            <Route path='/tel' component={TelPage} />
          </Suspense>
        </ErrorBoundary>
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
### 此时查看页面效果
![Untitled.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872628677-c77c0507-a006-45a1-aae0-c808860cb198.png#align=left&display=inline&height=399&margin=%5Bobject%20Object%5D&name=Untitled.png&originHeight=399&originWidth=698&size=253417&status=done&style=none&width=698)
## 配置Error Boundary精确抓取组件错误，并显示指定错误界面
### 构建ERROR-BOUNDARY组件
```jsx
import React from 'react';
import { ErrorImageContainer, ErrorImageOverlay, ErrorImageText } from './error-boundary.styles';
/**
* 构建: 抓取错误的组件error-boundary
 *      a) 目的: 控制组件出错时渲染的页面，以及获取报错信息
 *      b) 使用: 嵌套Suspense标签外，用于监测错误, 并渲染自定义错误页面
 *          <ErrorBoundary>
 *              <Suspense fallback={<Spinner />} >         
 *              </Suspense>
 *          </ErrorBoundary>
 */

class ErrorBoundary extends React.Component {
    constructor(){
        super();
        this.state = {
            errorState: false,
        }
    }

 // 定义静态函数: 专门抓取错误, 出现错误errorState: true
    static getDerivedStateFromError( error ){
        return {
           errorState:  true 
        };
    }

    componentDidCatch( error,  info ){
        console.log(error);
    }

 // 错误状态为true则渲染报错组件，否则渲染正常组件
    render(){
        if( this.state.errorState ){
            return ( // 这里为报错显示的组件
                <ErrorImageOverlay>
                    <ErrorImageContainer imageUrl = '/images/error/error.png' />
                    <ErrorImageText>
                        对不起! 好像出现了一些错误/(ㄒoㄒ)/~~ <br/>
                            Sorry! Some errors have occurred.
                    </ErrorImageText>
                </ErrorImageOverlay>
            );
        }
        return this.props.children; // 不要忘记是继承的children哦，嵌套在组件内的内容 
    }

}

export default ErrorBoundary;
```
### 使用ERROR-BOUNDARY

   - 注意：嵌套Suspense标签外，用于监测错误, 并渲染自定义错误页面![Untitled 1.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872649243-e38f50fd-49a0-403b-945d-fcd3fb94b801.png#align=left&display=inline&height=393&margin=%5Bobject%20Object%5D&name=Untitled%201.png&originHeight=393&originWidth=599&size=173126&status=done&style=none&width=599)
   - 如果页面出错，显示效果如下![Untitled 2.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872661305-f618c387-66be-406f-9dca-c325db7ec16f.png#align=left&display=inline&height=256&margin=%5Bobject%20Object%5D&name=Untitled%202.png&originHeight=256&originWidth=698&size=124834&status=done&style=none&width=698)



# React.memo：性能优化( React.memo )


## 配置React.memo


- 注意: 是"传入组件的数据"不发生改变时，组件不会重复渲染
- 缺点: 初始化安装组件时，消耗时间加长，所以不适合全局使用，用在经常发生数据改变的组件中，数据改变不频繁的组件完全不需要他，否则就是一种拖累```jsx
import React from "react";

const ShowPerson = ({ person: { age, name } }) => {
  console.log(`子组件渲染---`);
  return (
    <div className="show-person">
      {name}-{age}
    </div>
  );
};

// React.memo: 性能优化组件
// a) 作用:
//    0. 优点: 防止重复渲染，传入组件的数据不发生改变时，不会跟随父类组件重复渲染
//    1. 缺点: 初始化安装组件时，消耗时间加长
//    2. 注意: 是"传入组件的数据"不发生改变时，组件不会重复渲染
// b) 使用分析:
//    0. 用在经常发生数据改变的组件中
//    1. 因会增加初始化安装组件的时间长度，所以不适合所有组件
//    2. 数据改变不频繁的组件完全不需要他，否则就是一种拖累
//    3. 任何性能优化都是有代价的，应理性搭配
//    4. 一般常用于map指定渲染的组件,且渲染的内容经常发生交互变化
// c) 原理:
//    0. React.memo实际上在生命周期shouldComponentUpdate( nextProps, nextState )做了优化
export default React.memo(ShowPerson);
```




## 使用有REACT.MEMO的组件


```jsx
import React from "react";
import ShowPerson from "../showPerson/show-person.compontent"; // 引入有react.memo的组件

class IndexPage extends React.Component {
  constructor() {
    super();
    this.state = {
      number: 0,
      user: { name: "test", age: 12 }
    };
  }

  addNumber = () => {
    this.setState({ number: this.state.number + 1 });
  };
 // React.memo组件正确的用法
  render() {
    const { user, number } = this.state;
    console.log("----父组件渲染----");
    return (
      <div className="index-page">
        <ShowPerson person={user} /> // 正确使用方法
        <ShowPerson // 错误使用方法
          person={{
            name:
              "React.memo将不起作用: 这种写法因为每一次都会创建一个新Object数据来传递",
            age: "123"
          }}
        />
        <p>数字: {number}</p>
        <button onClick={this.addNumber}>增加内容</button>
      </div>
    );
  }
}

export default IndexPage;
```


# 性能优化( useCallback| useMemo)


```jsx
import React, { useState, useCallback, useMemo } from "react";

const IndexPage = () => {
  const [num, setNum] = useState(0);

// useCallback( 函数，[依赖关系] )：防止组件重新建立函数，对函数起缓存作用，提高一些性能
  //    0. 目的: 防止重新渲染组件后，组件中函数重复建立，减少计算负担
  //    1. 参数分析:
  //        a) 函数: 加工的函数
  //        b) [依赖关系]:
  //            0. [无变量]: 渲染组件后，建立函数一次，如果组件重新渲染，函数不会重复建立，提高些性能
  //            1. [有变量]: 目的是监听依赖关系，监听变量发生变化，促发函数
  //            2. 功能类似于useEffect的数组
  const showName = useCallback(
    () => console.log("useCallback防止重复建立函数"),
    []
  );
  const addNum = useCallback(() => setNum(num + 1), [num]);
  const cutNum = useCallback(() => setNum(num - 1), [num]);

 // useMemo( 函数，[依赖关系] )：缓存函数以及函数输出的结果，避免函数重复计算
  //    0. 目的: 用于有复杂计算的函数，缓存函数以及输出结果，避免函数重复计算，减轻计算负担
  //    1. 参数分析:
  //        a) 函数: 加工的函数
  //        b) [依赖关系]:
  //            0. [无变量]: 只在组件初始化渲染时，建立并计算函数，如果组件重复渲染，避免重复计算
  //            1. [有变量]: 根据监听变量做出函数反应，如果监听值无变化则输出缓存结果，不进行重复的函数计算
  const numJS = useMemo(() => {
    return ((num * 1000) % 192.8) * 5000 - 3000;
  }, [num]);

  return (
    <div className="index-page">
      <p>{num}</p>
      <button onClick={addNum}>+</button>
      <button onClick={cutNum}>-</button>
      

      <button onClick={showName}>SHOW</button>
      {numJS}
    </div>
  );
};

export default IndexPage;
```


# 性能监听库，能够记录组件渲染的时间( React profiler)


```jsx
// React性能监听库，能够记录组件渲染的时间
    // a) 用途: 用于收集React组件渲染信息，来参考，调整性能函数 
    // b) Profiler组件默认参数: 
        // 0. id: 用于标识当前监听的组件名称
        // 1. onRender: 接受并处理性能信息
            // a) 参数: 注意排序顺序固定，参数名称不固定
                // 0. id: 监听的目标
                // 1. phase: 组件的状态
                // 2. times: 渲染花费的时间，单位为ms
    // c) 使用方式如下:
import React,{Profiler} from 'react';
import { HomePageStyledContainer } from "./homepage.styles";

import DirectoryMenu from "../../components/directory-menu/directory-menu.component";
const HomePage = () => {
    return ( 
        <HomePageStyledContainer>
            <Profiler id="Directory" onRender={(id,phase,times)=>console.log({id,phase,times})} >
                <DirectoryMenu></DirectoryMenu>
            </Profiler>
        </HomePageStyledContainer>
    );
}

export default HomePage
```


## 效果如下


![Untitled 3.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872676971-e18a62a8-f896-4dcc-99e0-c4211f333ac4.png#align=left&display=inline&height=199&margin=%5Bobject%20Object%5D&name=Untitled%203.png&originHeight=199&originWidth=443&size=71363&status=done&style=none&width=443)


# 配置GIZP压缩 ( compression– Gizp压缩 )


- 目的: 让js文件更加的小, 用于node.js后端
- 安装: yarn add compression



## 使用方式


![Untitled 4.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872687354-72a67b0e-d613-458e-bf7b-ef5998c92703.png#align=left&display=inline&height=376&margin=%5Bobject%20Object%5D&name=Untitled%204.png&originHeight=376&originWidth=623&size=156484&status=done&style=none&width=623)
