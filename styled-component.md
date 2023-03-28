- 安装: yarn add styled-components
- styled的自定义标签可以使用className属性，换句话说可以于xxx.sass联用
- 支持类似于SASS选择普通标签的方法



## 实例使用


```jsx
import React from 'react';
import styled from 'styled-components'; // 导入此库
// 0. styled-components普通构建方式
const Card = styled.div`
    width:500px;
    height:200px;
    padding:0.5rem;
    display:flex;
    justify-content:center;
    align-items:center;
    background:rgba(0,0,0,0.7);
    border-radius:0.7rem;
    margin:auto;
`;

// 1. styled-components构建: 配合js构建动态css
    // a) 核心: `${}` --> `${ ()=>{} }` --> `${ ({ xxx })=>{} }`;
    // b) 在函数中以字符串形式表达css
const Text = styled.p`
    text-align:center;
    letter-spacing:3px;

    /* 单属性/多属性构建方式 */
    color: ${ ({ door }) => door ? 'red' : '#fff' };

    ${ ({door}) => {
        return door ?
       'font-size:2rem; font-weight:bold;' :
       'font-size:3rem; font-weight:lighter;'; 
    } };
    
`;

// 2. styled-components使用: 及使styled标签在不同组件中,也是同样的使用方法
const TelPage = () => {
    return (
        <Card>
            <Text door={false} >
                styled-components使用动态css, 注意标签中的door的使用
            </Text>
        </Card>
    );
}

export default TelPage;
```


## 在react实战使用方式


1. 首先构建xxx.styles.jsx
2. styled-components自定义标签赋予CSS
3. styled-component使用css函数构建公共css3属性
![Untitled.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872407564-a44c012b-ae8e-4553-8597-2567b5c377e7.png#align=left&display=inline&height=80&margin=%5Bobject%20Object%5D&name=Untitled.png&originHeight=80&originWidth=225&size=4134&status=done&style=none&width=225)```jsx
import styled, { css } from 'styled-components';
import { Link } from 'react-router-dom';

const HeaderDiv = styled.div`
    height: 70px;
    width: 95%;
    margin:0px auto;
    display: flex;
    justify-content: space-between;
    margin-bottom: 25px;
    font-family: 'Open Sans Condensed';
`; 

// styled-components自定义标签赋予CSS
    // 0. 先导入目标自定义标签,此时为Link标签
    // 1. 在使用styled( 目标标签 )
const LogoContainer = styled(Link)`
    height: 100%;
    width: 70px;
    padding: 25px;
`;

const OptionsDiv = styled.div`
    width: 50%;
    height: 100%;
    display: flex;
    align-items: center;
    justify-content: flex-end;
`;

// styled-component使用css函数构建公共css3属性
    // 0. 当前为构建公共css
const OptionContainerStyles = css`
    cursor: pointer;
    padding: 10px 15px;
`;
    // 1. 使用公共css
const OptionLink = styled(Link)`
    ${OptionContainerStyles} /* 引用公共css3 */
`;

export { HeaderDiv, LogoContainer, OptionsDiv, OptionLink };
```

4. styled( 标签 )也可以继承其它styled标签的属性```jsx
import styled,{css} from 'styled-components';

export const SpanStyled = styled.span`
    width:23%;
`;

// styled( 标签 )也可以继承其它styled标签的属性
export const Quantity = styled(SpanStyled)`
    padding-left: 20px;
    display: flex;
`;
```

5. styled-component使用方式与JSX的自定义标签相同
   - import导入，然后可直接书写自定义标签```jsx
import React from 'react';
import { CartItemStyledContainer, ItemDetails, Name } from './cart-item.styles';

const CartItem = ({ item: { imageUrl, name, price, quantity } }) => (
    <CartItemStyledContainer>
        <img src={imageUrl} alt={name}/>
        <ItemDetails>
            <Name>
                {name}
            </Name>
            <span className="price">
                {quantity} * ￥{price}
            </span>
        </ItemDetails>
    </CartItemStyledContainer>
);

export default CartItem;
```

6. styled-components使用 as 可进行标签类型转换```jsx
// styled-components使用as可进行标签类型转换
    // a) 使用方式: <Xxx as={'div'} ><Xxx/> --> <div></div>
// b) as可以将组件转换为自定义标签,也可以转换为默认HMTL标签

// 0. 没有as时: 此标签将编译为<a></a>
<OptionLink to='' onClick={ ()=>auth.signOut() } >退出</OptionLink>

// 1. 有as时: 此标签将编译为<div></div>
<OptionLink as='div' to='' onClick={ ()=>auth.signOut() } >退出</OptionLink>
```

7. styled-components构建: 根据组件传输的参数以函数操控CSS动态变化,以动态按钮为例```jsx
/**
styled-components构建: 根据组件传输的参数以函数操控css动态变化,以动态按钮为例
 *      a) 核心: const xxx = props => {}; 其中props可以接受到组件传递来的参数
 */
import styled, {css} from 'styled-components';

// 普通登陆界面按钮样式
const signWidthStyles = css`
    width:190px;
`;

// 谷歌按钮样式
let googleColor = '#4284f3';
const googleBtnStyles = css`
    ${signWidthStyles}
    background: ${googleColor};
    border:1px solid ${googleColor};
    &:hover{
        background: #fff;
        color:${googleColor};
        border:1px solid ${googleColor};
    }
`;

// 购物车下拉菜单按钮
const cartDropdownBtnStyles = css`
  margin:0px auto;
  width:100% !important;
`;

// 产品页加入购物车按钮
let animateTimeOnly = '0.1s';
const collectionItemBtnStyles = css`
    width:75%;
    margin-bottom:1rem;
    display: none;
    &:hover{
    background: rgba(255,255,255,0.6);
    }
    &:active{
    transition:all ${animateTimeOnly} linear;
    -moz-transition: all ${animateTimeOnly} linear;
    -webkit-transition: all ${animateTimeOnly} linear;
    background: red;  
    color:#fff;
    }
`;

// 核心: 动态css核心逻辑 - 可接受组件传来的参数
const getButtonStyles = props => {
    if( props.isGoogleStyles ){
        return googleBtnStyles;
    }
    else if( props.isSignWidthStyles ){
        return signWidthStyles;
    }
    else if( props.isCartDropdownBtnStyles ){
        return cartDropdownBtnStyles;
    }
    else if( props.isCollectionItemBtnStyles ){
        return collectionItemBtnStyles;
    }
    
};

let animateTime = '0.3s'; // 控制动画时间
const CustomButtonExp = styled.button`
    min-width: 165px;
    width: auto;
    height: 50px;
    letter-spacing: 0.5px;
    line-height: 50px;
    text-align: center;
    font-size: 15px;
    background-color: black;
    color: white;
    text-transform: uppercase;
    font-family: 'Open Sans Condensed';
    font-weight: bolder;
    border: none;
    cursor: pointer;
    letter-spacing: 2px;
    border: 1px solid black;

    transition:all ${animateTime} linear;
    -moz-transition: all ${animateTime} linear;
    -webkit-transition: all ${animateTime} linear;

    &:hover {
        background-color: white;
        color: black;
        border: 1px solid black;
    }
    ${getButtonStyles} /** 引用逻辑函数, 最好放置最后方便覆盖css样式 */
`;

export { CustomButtonExp };
```

8. 动态CSS使用方式```jsx
import React from "react";
import CustomButtonExp from "../custom-button-exp/custom-button-exp.component"; // 导入组件

class SignIn extends React.Component {
    render(){
        return(
                    <div className="btn-sign">
                        <CustomButtonExp isSignWidthStyles type="submit" > // 通过参数改变组件按钮样式
                            登陆
                        </CustomButtonExp>
                        <CustomButtonExp isGoogleStyles onClick={ signInWithGoogle } > // 通过参数改变组件按钮样式
                            Google登陆
                        </CustomButtonExp>
                    </div>
        );
}

}

export default SignIn;
```

9. 带有参数的styled-components函数写法 | 用于构建辅助框架```jsx
import styled,{css} from 'styled-components';
//注意: 传入参数时为字符串类型
export const display_flex = ( direction = 'row' ) => {
    return css`
        display:flex ;
        display:-webkit-flex ;
        flex-direction: ${direction}  !important;
        -webkit-flex-direction: ${direction} !important;
    `;
}

//使用函数
import styled from 'styled-components';
import { display_flex } from '../../assets/__OO7BTS.v1.0';

export const CollectionFooter = styled.div`
    width: 100%;
    height: 5%;
    display: flex;
    justify-content: space-between;
    font-size: 18px;
    ${ display_flex() };
`;
```

10. global.styles.js: 创建作用于全局的CSS
