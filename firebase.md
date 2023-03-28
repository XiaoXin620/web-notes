## FIREBASE创建用户函数


```jsx
handleSubmit = async cur => {
        cur.preventDefault();
        const { displayName, email, password, confirmPassword } = this.state;
        if( password != confirmPassword ){
            alert('二者密码不相同,请重新输入');
            return;
        }
        try{
            // firebase创建用户函数
                // 0. auth.createUserWithEmailAndPassword( email, password )创建用户
                // 1. 成功: 则返回创建用户信息，并在firbase用户管理出现账户信息，不会在数据库中保留
                // 2. 失败: 则返回失败原因
                // 3. 注意: 要在Firebase控制台开启'允许用户使用电子邮件和密码注册'
            let createUser = await auth.createUserWithEmailAndPassword( email, password );
		// 在数据库中创建用户信息
            await createUserProfileDocument( createUser.user, {displayName} ); // 吐血~这个createUser,应该返回createUser.user!!!!无语!!!!
            alert('注册成功');

            // 跳转到主页
            this.props.history.push('/');
        }
        catch( err ){
          // 在控制台显示红色错误信息
            console.error(err);
            alert( err.message );
        }
		// 注册完成state要初始化
        this.setState({
            email: '',
            password: '',
            displayName: '',
            confirmPassword: '',
        });
    }
```


## FIREBASE登陆用户函数


```jsx
import { signInWithGoogle, auth } from "../../firebase/firebase.config";

handleSubmit = async cur => {
        cur.preventDefault();    
        try{
            // firebase登陆用户函数
                	// 0. auth.signInWithEmailAndPassword(email,password)
            await auth.signInWithEmailAndPassword( this.state.email, this.state.password );
            this.setState( { email:'', password:'' } ); // 登陆完,要初始化

            //跳转到主页
            await this.test;
            this.props.history.push('/');
        }
        catch(err){
            alert(err.message);
        }
        
    }
```
