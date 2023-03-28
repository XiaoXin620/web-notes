- 可以计算派生数据，让Redux存储尽可能少的状态。
- 高效的。除非选择器的一个参数发生变化，否则不会重新计算。
- 可组合的。它们可以被用作其他选择器的输入。



## 关于reselect用法 – 专门处理接受后的state数据( mapStateToProps()传递来的state )


- xxx.selectors.js文件构建，与redux配合提高react性能
- 安装：yarn add reselect



```jsx
/***
 * xxx.selectors.js文件构建，与redux配合提高react性能
	注意：select只与mapStateToProps传递来的数据加工有关系，对于redux的action执行,无任何配置关联
   0. 目的: 保证只有在redux数据改变时，才能重新渲染组件，提高react性能
   1. 总体来说select分，输入/输出
   2. 构建流程：
        a) select分成二步，输入与输出 
        b) 输入: 来选择引入来的数据:
            0. const selectXXX = state => stateXXX;
        c) 输出：数据流只要经过select输出，就能保证只有redux数据方式改变时对应的组件才能重新渲染，提高了react性能。
            0. export const xxx = createSelector(
                [selectXXX], // 导入数据
                xxx => xxx.yyy;
            );
            1. 输出处理核心:
            import {createSelector} from 'reselect';
            createSelector([xxx,yyy],xxx=>xxx.xxx);
    3. 使用流程:
        a) 普通方法使用select
            0. 实例代码
import { selectCartItemsCount } from '../../redux/cart/cart.selectors';
		const mapStateToProps = state => ({
    			itemCount: selectCartItemsCount(state),
});

        b) 便捷式方法使用select
            0. 核心：createStructuredSelector({ xx:yy })
            1. 实例代码
import { createStructuredSelector } from 'reselect';
import { selectCartItems } from '../../redux/cart/cart.selectors';

const mapStateToprops = createStructuredSelector({
                cartItems: selectCartItems,
})
 */

import { createSelector } from 'reselect';
// select - 输入部分 - BGN
const selectCart = state => state.cart;

// select - 输出部分 - END
// 1. select单个数据处理输出使用方式
export const selectCartItems = createSelector(
    [selectCart],
    cart => cart.cartItems,  
);

export const selectCartHidden = createSelector(
    [selectCart],
    cart => cart.hidden, 
);

export const selectCartItemsCount = createSelector(
    [selectCartItems],
    cartItems => cartItems.reduce( (total,cur)=>total+cur.quantity,0 ),
);

// 2. select多个数据处理输出使用方式
const selectCart = state => state.cart;
const selectUser = state => state.user;

export const selectCartItems = createSelector(
    [selectCart, selectUser],
    (cart, user) => user.currentUser,  
);
```


## reselect传入参数的函数构建方式


- 对象转数组
- 高效索引数据-直接索引对象数据最最高效的, 比filuter,find都要高效（ 性能核心 ）



```jsx
import { createSelector } from 'reselect';
const selectShop = state => state.shop;

export const selectCollectionShop = createSelector(
    [selectShop],
    shop => shop.collectionShop,
);

export const selectCollectionShopArray = createSelector(
    [selectCollectionShop],
  // 对象转数组类型
        // 0. 核心: 
            // a) Object.keys( 对象数据 )返回数组对象的键值
                // 0. test = { a:123, b: 334 } --> Object.keys(test) --> [ 'a', 'b' ]
            // b) 键值数组.map( 创建新数组 )通过键值迭代索引对象内容,在通过map创建新数组
        // 1. 实例: collectionShop为对象数据
    collectionShop => Object.keys(collectionShop).map( e=>collectionShop[e] ) ,
);

// 构建: reselect接受参数函数处理写法
export const selectCollectionItem = collectionUrlProps => createSelector(
    [selectCollectionShop],
    collectionShop => collectionShop[ collectionUrlProps ]  // 直接索引对象数据最最高效的, 比filuter,find都要高效:
// 原理https://www.kirupa.com/html5/hashtables_vs_arrays.htm
);
```


- 使用: RESELECT传递参数函数处理写法( 注意与上方”构建: RESELECT接受参数函数处理写法”配套使用 )```jsx
// 使用: reselect传递参数函数处理写法
    // 0. 传入参数解析:
        // a) state: 为redux默认传入的对象数据
        // b) ownProps(任意名称): 为当前组件中的this.props对象数据
    // 1. select函数使用方式:
        // b) selectXXX( 自定义传入参数 )( Redux数据 )
const mapStateToProps = (state, ownProps) => ({
    collectionItem: selectCollectionItem(ownProps.match.params.collectionId)(state),
})
```

