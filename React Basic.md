# React JSX


1. react定义的一种类似于XML的JS扩展语法: JS + XML本质是React.createElement(component, props, ...children)方法的语法糖
2. 作用: 用来简化创建虚拟DOM
   - 写法：`var``ele`` = ``<h1>``Hello JSX!``</h1>`
   - 注意1：它不是字符串, 也不是HTML/XML标签
   - 注意2：它最终产生的就是一个JS对象
3. 基本语法规则
   - 遇到 <开头的代码, 以标签的语法解析: html同名标签转换为html同名元素, 其它标签需要特别解析
   - 遇到以 { 开头的代码，以JS语法解析: 标签中的js表达式必须用{ }包含
   - 注意className == class: 目的是为了区分JS的class
   - JSX导入css(react导入css): import 'xx.css';注意JSX,className对应html的class名称



# 组件编程


- 函数组件



```jsx
//1.创建函数式组件
function MyComponent(){
console.log(this); //此处的this是undefined，因为babel编译后开启了严格模式
return <h2>我是用函数定义的组件(适用于【简单组件】的定义)</h2>
}
//2.渲染组件到页面
ReactDOM.render(<MyComponent/>,document.getElementById('test'))
/* 
执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
		1.React解析组件标签，找到了MyComponent组件。
		2.发现组件是使用函数定义的，随后调用该函数，将返回的虚拟DOM转为真实DOM，随后呈现在页面中。
*/
```


- 类式组件



```jsx
//1.创建类式组件
class MyComponent extends React.Component {
	render(){
		//render是放在哪里的？—— MyComponent的原型对象上，供实例使用。
		//render中的this是谁？—— MyComponent的实例对象 <=> MyComponent组件实例对象。
		console.log('render中的this:',this);
		return <h2>我是用类定义的组件(适用于【复杂组件】的定义)</h2>
	}
}
//2.渲染组件到页面
ReactDOM.render(<MyComponent/>,document.getElementById('test'))
/* 
	执行了ReactDOM.render(<MyComponent/>.......之后，发生了什么？
			1.React解析组件标签，找到了MyComponent组件。
			2.发现组件是使用类定义的，随后new出来该类的实例，并通过该实例调用到原型上的render方法。
			3.将render返回的虚拟DOM转为真实DOM，随后呈现在页面中。
*/
```


- 注意:
   1. 组件名必须首字母大写
   2. 虚拟DOM元素只能有一个根元素
   3. 虚拟DOM元素必须有结束标签



### 渲染类组件标签流程


1. React内部会创建组件实例对象
2. 调用render()得到虚拟DOM, 并解析为真实DOM
3. 插入到指定的页面元素内部



# Class 组件中箭头函数与普通函数


1. 组件中render方法中的this为组件实例对象
2. 组件自定义的方法中this为undefined，如何解决？
   1. 强制绑定this: 通过函数对象的bind()
   2. 箭头函数
3. 箭头函数调用更加便利
4. 普通函数调用较为麻烦



```jsx
//1.创建组件
class Weather extends React.Component{
	//初始化状态
	state = {isHot:false,wind:'微风'}

	render(){
		const {isHot,wind} = this.state
		return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
	}

	//自定义方法————要用赋值语句的形式+箭头函数
	changeWeather = ()=>{
		const isHot = this.state.isHot
		this.setState({isHot:!isHot})
	}
}
//2.渲染组件到页面
ReactDOM.render(<Weather/>,document.getElementById('test'))
```


# 组件三大核心属性:


## 1. State:


1. state是组件对象最重要的属性, 值是对象(可以包含多个key-value的组合)
2. 组件被称为"状态机", 通过更新组件的state来更新对应的页面显示(重新渲染组件)



### this.setState()本质


1. this.setState()为异步函数, 属于Component组件
2. 本质用法: this.setState( ( prevState, prevProps )=>{ return {} }, ()=>{ '衔接异步数据处理' } );
   - 其实他有二个回调参数:
      1. prevState: 相当于复制this.state数据
      2. prevProps: 相当于复制this.props数据
      3. prevState/prevProps名称可变
      4. 只所以异步是因为效率, React能根据最佳时机来改变this.state的数据
      5. 正是因为有prevState,prevProps二个回调参数为对象类型, 他才诞生了‘正常用法必须使用对象的形式‘
      6. 注意: this.state函数返回中有return: return { age: 'xxx' }依旧为对象类型
3. 正常用法: this.setState( { xxx: 'yyy' }, ()=>{ '衔接异步数据处理' } );
4. 关于this.props
   - this.props属于 Component组件的默认属性
   - 它包含的是'自定义标签'传递来的标签属性
   - 如: < APP test="123" string="hahah" / > 那么this.props == {test:'123', string='hahah'};


普通版本


```jsx
//1.创建组件
class Weather extends React.Component{
	
	//构造器调用几次？ ———— 1次
	constructor(props){
		console.log('constructor');
		super(props)
		//初始化状态
		this.state = {isHot:false,wind:'微风'}
		//解决changeWeather中this指向问题
		this.changeWeather = this.changeWeather.bind(this)
		}

	//render调用几次？ ———— 1+n次 1是初始化的那次 n是状态更新的次数
	render(){
		console.log('render');
		//读取状态
		const {isHot,wind} = this.state
		return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
	}

	//changeWeather调用几次？ ———— 点几次调几次
	changeWeather(){
		//changeWeather放在哪里？ ———— Weather的原型对象上，供实例使用
		//由于changeWeather是作为onClick的回调，所以不是通过实例调用的，是直接调用
		//类中的方法默认开启了局部的严格模式，所以changeWeather中的this为undefined
		
		console.log('changeWeather');
		//获取原来的isHot值
		const isHot = this.state.isHot
		//严重注意：状态必须通过setState进行更新,且更新是一种合并，不是替换。
		this.setState({isHot:!isHot})
		console.log(this);

		//严重注意：状态(state)不可直接更改，下面这行就是直接更改！！！
		//this.state.isHot = !isHot //这是错误的写法
	}
}
//2.渲染组件到页面
ReactDOM.render(<Weather/>,document.getElementById('test'))
```


简洁版


```jsx
//1.创建组件
class Weather extends React.Component{
	//初始化状态
	state = {isHot:false,wind:'微风'}

	render(){
		const {isHot,wind} = this.state
		return <h1 onClick={this.changeWeather}>今天天气很{isHot ? '炎热' : '凉爽'}，{wind}</h1>
	}

	//自定义方法————要用赋值语句的形式+箭头函数
	changeWeather = ()=>{
		const isHot = this.state.isHot
		this.setState({isHot:!isHot})
	}
}
//2.渲染组件到页面
ReactDOM.render(<Weather/>,document.getElementById('test'))
```


## props:


- 理解
   1. 每个组件对象都会有props(properties的简写)属性
   2. 组件标签的所有属性都保存在props中
- 作用
   3. 通过标签属性从组件外向组件内传递变化的数据
   4. 注意: 组件内部不要修改props数据



1. 内部读取值```jsx
const {name,age,sex} = this.props;
```

2. 对props中的属性值进行类型限制和必要性限制(需要 prop-types库)```jsx
//对标签属性进行类型、必要性的限制
Person.propTypes = {
	name:PropTypes.string.isRequired, //限制name必传，且为字符串
	sex:PropTypes.string,//限制sex为字符串
	age:PropTypes.number,//限制age为数值
	speak:PropTypes.func,//限制speak为函数
}
//指定默认标签属性值
Person.defaultProps = {
	sex:'男',//sex默认值为男
	age:18 //age默认值为18
}
//==================================================================================
//以下在组件简写方式
//对标签属性进行类型、必要性的限制
static propTypes = {
	name:PropTypes.string.isRequired, //限制name必传，且为字符串
	sex:PropTypes.string,//限制sex为字符串
	age:PropTypes.number,//限制age为数值
}

//指定默认标签属性值
static defaultProps = {
	sex:'男',//sex默认值为男
	age:18 //age默认值为18
}
```

3. 函数组件使用props```jsx
//创建组件
function Person (props){
	const {name,age,sex} = props
	return (
			<ul>
				<li>姓名：{name}</li>
				<li>性别：{sex}</li>
				<li>年龄：{age}</li>
			</ul>
		)
}
Person.propTypes = {
	name:PropTypes.string.isRequired, //限制name必传，且为字符串
	sex:PropTypes.string,//限制sex为字符串
	age:PropTypes.number,//限制age为数值
}

//指定默认标签属性值
Person.defaultProps = {
	sex:'男',//sex默认值为男
	age:18 //age默认值为18
}
//渲染组件到页面
ReactDOM.render(<Person name="jerry"/>,document.getElementById('test1'))
```




## ref与事件处理


### ref:


- 理解
   - 组件内的标签可以定义ref属性来标识自己



1. 字符形式的ref```jsx
<input ref="input1" type="text"/>
```

2. 回调形式的ref```jsx
<input ref={c => this.input1 = c } type="text" />
```

3. createRef创建ref容器```jsx
/* 
	React.createRef调用后可以返回一个容器，该容器可以存储被ref所标识的节点,该容器是“专人专用”的
 */
myRef = React.createRef()

<input ref={this.myRef} type="text"/>
```




### 事件处理:


1. 通过onXxx属性指定事件处理函数(注意大小写)
   - React使用的是自定义(合成)事件, 而不是使用的原生DOM事件
   - React中的事件是通过事件委托方式处理的(委托给组件最外层的元素)
2. 通过target得到发生事件的DOM元素对象



```jsx
//创建组件
class Demo extends React.Component{
	/* 
		(1).通过onXxx属性指定事件处理函数(注意大小写)
				a.React使用的是自定义(合成)事件, 而不是使用的原生DOM事件 —————— 为了更好的兼容性
				b.React中的事件是通过事件委托方式处理的(委托给组件最外层的元素) ————————为了的高效
		(2).通过event.target得到发生事件的DOM元素对象 ——————————不要过度使用ref
	 */
	//创建ref容器
	myRef = React.createRef()
	myRef2 = React.createRef()

	//展示左侧输入框的数据
	showData = (event)=>{
		console.log(event.target);
		alert(this.myRef.current.value);
	}

	//展示右侧输入框的数据
	showData2 = (event)=>{
		alert(event.target.value);
	}

	render(){
		return(
			<div>
				<input ref={this.myRef} type="text" placeholder="点击按钮提示数据"/>&nbsp;
				<button onClick={this.showData}>点我提示左侧的数据</button>&nbsp;
				<input onBlur={this.showData2} type="text" placeholder="失去焦点提示数据"/>&nbsp;
			</div>
		)
	}
}
//渲染组件到页面
ReactDOM.render(<Demo a="1" b="2"/>,document.getElementById('test'))
```


# 组件的生命周期


- 理解
   1. 组件从创建到死亡它会经历一些特定的阶段。
   2. React组件中包含一系列勾子函数(生命周期回调函数), 会在特定的时刻调用。
   3. 我们在定义组件时，会在特定的生命周期回调函数中，做特定的工作。
- 生命周期图
![Untitled.png](https://cdn.nlark.com/yuque/0/2021/png/12378899/1614872721227-43049157-7ae1-4dca-869e-eb7fa8f2af12.png#align=left&display=inline&height=788&margin=%5Bobject%20Object%5D&name=Untitled.png&originHeight=788&originWidth=1133&size=96865&status=done&style=none&width=1133)```jsx
`//创建组件
class Count extends React.Component{
	/* 
		1. 初始化阶段: 由ReactDOM.render()触发---初次渲染
						1.	constructor()
						2.	getDerivedStateFromProps 
						3.	render()
						4.	componentDidMount() =====> 常用
									一般在这个钩子中做一些初始化的事，例如：开启定时器、发送网络请求、订阅消息
		2. 更新阶段: 由组件内部this.setSate()或父组件重新render触发
						1.	getDerivedStateFromProps
						2.	shouldComponentUpdate()
						3.	render()
						4.	getSnapshotBeforeUpdate
						5.	componentDidUpdate()
		3. 卸载组件: 由ReactDOM.unmountComponentAtNode()触发
						1.	componentWillUnmount()  =====> 常用
									一般在这个钩子中做一些收尾的事，例如：关闭定时器、取消订阅消息
	*/
	//构造器
	constructor(props){
		console.log('Count---constructor');
		super(props)
		//初始化状态
		this.state = {count:0}
	}

	//加1按钮的回调
	add = ()=>{
		//获取原状态
		const {count} = this.state
		//更新状态
		this.setState({count:count+1})
	}

	//卸载组件按钮的回调
	death = ()=>{
		ReactDOM.unmountComponentAtNode(document.getElementById('test'))
	}

	//强制更新按钮的回调
	force = ()=>{
		this.forceUpdate()
	}
	
	//若state的值在任何时候都取决于props，那么可以使用getDerivedStateFromProps
	static getDerivedStateFromProps(props,state){
		console.log('getDerivedStateFromProps',props,state);
		return null
	}

	//在更新之前获取快照
	getSnapshotBeforeUpdate(){
		console.log('getSnapshotBeforeUpdate');
		return 'atguigu'
	}

	//组件挂载完毕的钩子
	componentDidMount(){
		console.log('Count---componentDidMount');
	}

	//组件将要卸载的钩子
	componentWillUnmount(){
		console.log('Count---componentWillUnmount');
	}

	//控制组件更新的“阀门”
	shouldComponentUpdate(){
		console.log('Count---shouldComponentUpdate');
		return true
	}

	//组件更新完毕的钩子
	componentDidUpdate(preProps,preState,snapshotValue){
		console.log('Count---componentDidUpdate',preProps,preState,snapshotValue);
	}
	
	render(){
		console.log('Count---render');
		const {count} = this.state
		return(
			<div>
				<h2>当前求和为：{count}</h2>
				<button onClick={this.add}>点我+1</button>
				<button onClick={this.death}>卸载组件</button>
				<button onClick={this.force}>不更改任何状态中的数据，强制更新一下</button>
			</div>
		)
	}
}

//渲染组件
ReactDOM.render(<Count count={199}/>,document.getElementById('test'))
```

