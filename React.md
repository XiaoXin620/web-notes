# 使用props传递函数


```jsx
//传送方:
<FormInput 
    label="邮箱" 
    handleChange={this.handleChange} 
    type="email" 
    name="email" 
    value={this.state.email} required  
/>

//接受方:
// React传输函数给组件
    // 0. 注意：react是可以传输，函数的。比如现在的handleChange函数
const FormInput = ({ label, handleChange, ...otherProps }) => {
    return(
        <div className="group">
            <input 
            onChange={handleChange} 
            className="form-input"
            {...otherProps}
            />
        </div>
    );
}
```


## REACT-REACT导入SVG文件


1. _import { ReactComponent as Logo } from "../../assets/crown.svg";_
2. _因为文件为jsx文件格式，所以需要借用 ReactComponent 这是规则，_
3. _{ ReactComponent as Logo }修改名称为logo_
4. _这样就可以使用自定义标签来代表svg图像_
5. _SVG的优势: svg图标，为矢量图，并且很小_



```jsx
import { ReactComponent as Logo } from "../../assets/crown.svg"; // 导入svg
const Header = () => {
    return(
           <Logo /> // 使用svg图像
    );
}
```


## REACT处理FORM表单


1. 解构法,面对标签时创建赋值变量
2. 变量值变为对象的键值
3. 感悟: 感觉REACT组件,就像是一个一个函数一样



### REACT处理from提交数据与处理input改变数据


- React处理from提交数据
   1. form准备: 
   2. 注意: cur.preventDefault()函数的本质为,阻止默认事件发生,好由React来处理form数据
   3. 函数准备: 如下 - cur为接受的form数据
- React处理input改变数据
   1. React中,input必须要有onChange={xxx}来处理表单数据,否则会报错
   2. input准备: 
   3. 函数准备: 如下 - cur为接受的input数据



```jsx
import React from "react";
import "./sign-in.style.scss";
import FormInput from "../form-input/form-input.component";
import CustomButton from "../custom-button/custom-button.component";

class SignIn extends React.Component {
    constructor(){
        super();
        this.state = {
            email:'',
            password:'',
        }
    }

   
    handleSubmit = cur => {
        cur.preventDefault();    
        this.setState( { email:'', password:'' } );
    }

    handleChange = cur => {
        // 解构法,面对标签时创建赋值变量
            // 0. 因为其js"抓取的标签.target"本质也为对象
            // 1. 所以可以使用解构法创建变量, 但注意名字要一致
        const { value, name } = cur.target; // 相当于 cosnt value = cur.target.vaule, name = cur.target.name;

        // 变量值变为对象的键值
            // 0. this.setState( { [name]: value } ); 
            // 1. 其中[name]的意思为，将name的变量值作为键值,方便键值动态变化。
            // 2. 比如name="password"那么相当于this.setState( password: value );
        	this.setState( {[name]: value} );
    }
    // 感悟: 感觉React组件,就像是一个一个函数一样
    render(){
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

                    <CustomButton type="submit" >
                        登陆
                    </CustomButton>
                </form>
            </div>
        );
    }

}

export default SignIn;
```


### 解构变量属性读取


```jsx
const FormInput = ({ label, handleChange, ...otherProps }) => {
    return(
            // 解构变量属性读取
                // 0. '...otherProps' 当为解构的对象时
                // 1. otherProps.xxx可以直接读取其中的属性,当然名称要一致
                <label className={ `${ otherProps.value.length ? 'shrink' : null } form-input-label` } >
                    {label} 
                </label>
    );
}
export default FormInput
```


### 对象解构法 - 创建对象属性变量并赋值


1. _const { collectionShop } = this.state; 相当于 const collectionShop = this.state.collectionShop;_
2. _注意属性名称与变量名称一致，以及注意大括号。_



```jsx
const { collectionShop } = this.state;
```


## HOCS高阶组件


### HOCS高阶组件构建方式一


```jsx
// 构建: react加载器构建,用于加载异步数据显示加载动画
import React from 'react';
import { SpinnerContainer, SpinnerOverlay } from './with-spinner.styles';

// 加载器使用的高级用法
    // 0. curring函数 - 多个函数嵌套
    // 1. 高阶组件 - 这里使用了, 高阶组件用法
        // a) 传递接受组件, 并加工
const WithSpinner = WrappedComponent => ({ isLoading, ...otherProps }) => {

    return isLoading ? ( // isLoading为真则显示加载器, 否则返回传递来的组件
        <SpinnerOverlay>
            <SpinnerContainer />
        </SpinnerOverlay>
    ) :
    (
        <WrappedComponent { ...otherProps } />        
    );
}

export default WithSpinner;
```


### HOCS高阶组件使用方式一


- react加载器在route中的使用
- 有redux时react简化版state写法,可以配合this.setState()使用```jsx
// 使用: react加载器的使用
import WithSpinner from '../../components/with-spinner/with-spinner.component';

import CollectionOverView  from '../../components/collection-overview/collection-overview.component';
import CollectionPage from '../collectionpage/collectionpage.component';

// react加载器: 要使用加载器的组件准备
const CollectionOverViewWithSpinner = WithSpinner( CollectionOverView );
const CollectionPageWithSpinner = WithSpinner( CollectionPage );

class ShopPage extends React.Component {
 // react加载器: 有redux时react简化版state写法,可以配合this.setState()使用
    state = {
        loadding: true,
    }

    render(){
        const { match } = this.props;
        const { loadding } = this.state;
        return (
   // react加载器在route中的使用
                // 0. 注意loadding变量来自于this.state
            <div className="shop-page">
                <Route 
                    exact 
                    path={`${match.path}`} 
                    render={
                        props => ( <CollectionOverViewWithSpinner isLoading={ loadding } {...props} /> )
                    }
                />            
                <Route 
                    path={`${match.path}/:collectionId`} 
                    render={
                        props => ( <CollectionPageWithSpinner isLoading={ loadding } {...props} /> )
                    }
                />            
            </div>        
        );
    }
}

const mapDispatchToProps = dispatch => ({
    updateCollections: collectionsData => dispatch( updateCollections( collectionsData ) ),
});

export default connect(null,mapDispatchToProps)(ShopPage);
```

