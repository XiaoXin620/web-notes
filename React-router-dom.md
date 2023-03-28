# React路由模块安装


安装模块：yarn add react-router-dom


如果出现安装问题，则直接，删除项目中的缓存，重新yarn install。


# BrowserRouter - 函数路由初始化


1. 在render()下要渲染的标签使用 标签
2. 这样在此标签下的其它路由函数才能生效```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter } from 'react-router-dom';
import './index.css';
import App from './App';

ReactDOM.render(
    // 展示路由功能的基础标签
    <BrowserRouter>
        <App /> // 待渲染自定义标签
    </BrowserRouter>, 
    document.getElementById('root')
);
```




## Router/Switch– 函数的使用


## Route标签使用:


1. 模型: 默认3个参数
2. exact属性:
   1. exact为是否开启精确路由访问
   2. 如果Route标签不写exact: 则默认exact为false否则为true
   3. 关于exact属性的使用:
      1. 在内，
         1. 不开启exact: 则只能访问'/'路由页面
         2. 开启exact: 则可以访问精确路由页面
      2. 不在则进行叠加JSX渲染,就是根目录页面+指定路由页面渲染在同页
3. path属性:
   1. path="/"按照web路径来写
4. component属性:
   1. component={JSX}: 放置渲染的JSX变量( 原自定义标签名称 )



```jsx
import { Switch,Route } from 'react-router-dom';
// 测试路由 - 0
const HatsPage = () => ( <div> <h2 className="display-2" > TEST - Router </h2></div> );
const TestPage = () => ( <div> <h2 className="display-2" > TEST2 - Router </h2></div> );

function App() {
  return (
    <div className="App">
      <Switch>
        <Route exact={ true } path='/' component={HomePage} />
        <Route path='/hats' component={HatsPage} />
        <Route path='/test' component={TestPage} />
      </Switch>
    </div>
  );
}
export default App;
```


## 关于Router标签传递的参数PROPS -当前页面路由信息


### 关于Route标签传递的参数props


1. **match属性对象:**
   - url: 保存‘当前’在浏览器中的url链接( 仅限于保存符合路由规则的url,想要完整的url信息请看props.location.pathname属性解析 )
      - 常用于创建动态路径
   - path: 保存在Route标签中的path属性内容
      - 如:  那么 props.match.path == '/pro/:xxxId';
   - isExact: 当url符合path规定的路由路径时为True, 否则为false
   - params: 常用于它的参数来配置'动态路由'页面跳转
      - 常与Route标签中 path='/:xxx'下的':'符号配合, 因为它能让’:xxx’变为params的属性
      - 如```jsx
/**
 * 路由配置: <Route path='/pro/:proID' component={ProTextTest} />
 * url访问: xxxx/pro/123
 * 那么params对象属性有: params = { proID: 123 }
 * **/
```

2. **history属性对象:**
   - history下有很多函数属性,目前最常用的为push函数,用于跳转指定url页面,与Link标签功能相似
   - push(): 跳转指定url
      - 如: <button onClick={ ()=> props.history.push('/pro') } > 产品页面 
3. **location属性对象:**
   - pathname: 保存完整的‘当前url信息’
   - 如```jsx
/**
 * 路由规则: <Route path='/pro/:proID' component={ProTextTest} />
 * 访问域名: http://localhost:3000/pro/123123/123123
 */
// a) props.location.pathname = "/pro/123123/123123" ;
// c) props.match.url = "/pro/123123";
// b) 与match.url的区别: 在于pathname保留完整的url信息，但math.url只保存符合路由规则的url信息
```




### Link- 函数使用及Link标签的使用


- Link标签库：直接跳转到指定url
   - 如:  返回主页 
   - 在前端界面将被渲染为‘a标签’



```jsx
import { Switch,Route,Link } from 'react-router-dom';
const HomePageTest = props => {
  console.log( '1',props );
  return ( 
    <div> 
      <h2 className="display-1" > 主页 </h2>
      <button onClick={ ()=> props.history.push('/pro') } > 产品页面 </button>
    </div> 
  );
}
const ProTest = props => {
  console.log( '2',props );
  return ( 
      <div> 
        <h2 className="display-1" > 产品页 </h2>
        <Link to="/" > 返回主页 </Link>      

        <Link to={ `${props.match.url}/13` } > 产品13 - match.url实战 </Link>      
        <Link to={ `${props.match.url}/14` } > 产品14 - match.url实战 </Link>      
      </div> 
    );
}
const ProTextTest = props => { 
  console.log( '3',props );
  return ( <div> <h2 className="display-1" > 产品详情页ID: { props.match.params.proID } </h2></div> ); 
}

function App() {
  return (
    <div className="App">
      <Switch>
        <Route exact path='/' component={HomePageTest} />
        <Route exact path='/pro' component={ProTest} />
        <Route path='/pro/:proID' component={ProTextTest} />
      </Switch>
    </div>
  );
}
```


## withRouter函数 - 使当前页面下的组件, 可以直接访问"当前页面路由信息"


## withRouter函数用法


1. 引入: import { withRouter } from 'react-router-dom';
2. 输出: export default withRouter( 组件名 );
3. 使用: 路由属性名称 -> 直接传递给组件, 如下实战```jsx
import React from 'react';
import { withRouter } from 'react-router-dom';
import "./menu-item.style.scss";

const MenuItem = ({ title,imageUrl,size,linkUrl,history,match }) => (
    <div 
    onClick={ ()=>history.push( `${match.url}${linkUrl}` ) } 
    className={ ` menu-item ${size} ` }
    >
        <div style={ { 
            backgroundImage: `url(${imageUrl})` 
        } } className="background-image">
        </div>
        <div className="content">
            <h1 className="title">
                {title.toUpperCase()  }
            </h1>
            <span className="subtitle">
                SHOP NOW
            </span>
        </div>
    </div>
);
export default withRouter(MenuItem);
```
### withRouter对象用法
```jsx
import { withRouter } from 'react-router-dom';

class SignIn extends React.Component {
    // withRouter在class中的用法
        	// 通过this.props就能访问当前页面的路由信息
        	// 如: this.props.history; this.props.match;
		// 注意: super(props)确保this.props的存在
    constructor(props){
        super(props);
        this.state = {
            email:'',
            password:'',
        };
}
}
```
## Route高级路由( 在子级组件中创建路由)

   1. 子级组件中使用路由,就叫高级路由
   2. 注意事项:
      - 在主路由中去除exact(当前主路由位置为APP.js)```jsx
<Route path='/shop' component={ShopPage} />
```

      - 在当前组件中路由要有exact,match.path为当前url位置
         1. 当前页面(必备): <Route exact path={`${match.path}`} component={CollectionOverView} />
         2. <Route path={`${match.path}/:collectionId`} component={CollectionPage} />
   3. 通常情况下是由match配置动态路由
```jsx
import React from 'react';
import "./shopage.styles.scss";
import { Route } from 'react-router-dom';
import CollectionOverView  from '../../components/collection-overview/collection-overview.component';
import CollectionPage from '../collectionpage/collectionpage.component';
const ShopPage = ({ match }) => {         
    return (
        <div className="shop-page">
            <Route exact path={`${match.path}`} component={CollectionOverView} />            
            <Route path={`${match.path}/:collectionId`} component={CollectionPage} />            
        </div>        
    );
}
export default ShopPage;
```
# Switch标签

**在Switch标签外不受路由影响 - 无论页面如何变化,组件依然显示存在**

   1. _把导航栏放在switch之外，这样导航栏将一直存在。不会受路由的控制_
   2. _这是一个非常非常重要的功能 - 不受页面刷新影响方法_
```jsx
function App() {
  return (
    <div className="App">
      <Header /> // 把导航栏放在switch之外，这样导航栏将一直存在。不会受路由的控制
      <Switch>
        <Route exact path='/' component={HomePage} />
        <Route exact path='/shop' component={ShopPage} />
        <Route path='/shop/:proName' component={TestPage} />
        <Route path='/sign' component={SignPage} />
      </Switch>
    </div>
  );
}
```
# 关于路由redirect的使用,重定向指定页面
```jsx
{
         // 关于路由render属性的功能，决定渲染组件
              // 0. 用法: <Route exact path render={()=> <自定义标签 />} >
              // 1. 有render属性则不能有component属性
              // 2. 必需要有exact属性
          // 关于路由Redirect的使用,重定向指定页面
              // 0. 导入Redirect: import { Switch,Route,Link,Redirect } from 'react-router-dom';
              // 1. 使用方式<Redirect to='路由位置'>,直要被渲染将直接跳转指定路由位置
              // 2. 实列( 当用户登陆后,则将无法访问注册/登陆页面 ):
          }
          <Route exact path='/sign' render={ ()=> this.props.currentUser ? <Redirect to='/' /> : <SignPage />  } />
```
