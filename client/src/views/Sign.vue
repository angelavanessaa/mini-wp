<template>
    <div class="container mt-5" id="container">
        <div class="form-container sign-up-container">
            <form @submit="submitRegister">
                <h1>Sign Up</h1>
                <div class="social-container">
                    <GoogleLogin :params="params" :renderParams="renderParams" :onSuccess="onSuccess" :onFailure="onFailure">Login</GoogleLogin>
                </div>
                <span>or use your email for registration</span>
                <input type="text" placeholder="Name" v-model="registerName" />
                <input type="email" placeholder="Email" v-model="registerEmail" />
                <input type="password" placeholder="Password" v-model="registerPassword" />
                <button type="submit">Sign Up</button>
            </form>
        </div>
        <div class="form-container sign-in-container">
            <form @submit="submitLogin">
                <h1>Sign in</h1>
                <div class="social-container">
                    <GoogleLogin :params="params" :renderParams="renderParams" :onSuccess="onSuccess" :onFailure="onFailure">Login</GoogleLogin>
                </div>
                <span>or use your account</span>
                <input type="email" placeholder="Email" v-model="loginEmail"/>
                <input type="password" placeholder="Password" v-model="loginPassword"/>
                <button type="submit" class="mt-1">Sign In</button>
            </form>
        </div>
        <div class="overlay-container">
            <div class="overlay">
                <div class="overlay-panel overlay-left">
                    <h1>Have an account?</h1>
                    <p>Login with your account.</p>
                    <button class="ghost" id="signIn">Sign In</button>
                </div>
                <div class="overlay-panel overlay-right">
                    <h1>Don't have an account?</h1>
                    <p>Create an account and start blogging right away!</p>
                    <button class="ghost" id="signUp">Sign Up</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
import axios from '../../config/axios'
import GoogleLogin from 'vue-google-login'
import Swal from 'sweetalert2'

export default {
    data () {
        return {
            registerEmail: '',
            registerName: '',
            registerPassword: '',
            loginEmail: '',
            loginPassword: '',
            params: {
                client_id: "415938799508-oh4a2roef8rj6slbbvtlj8j40ebnothl.apps.googleusercontent.com"
            },
            renderParams: {
                width: 250,
                height: 50,
                longtitle: true
            }
        }
    },
    components: {
        GoogleLogin
    },
    methods: {
        submitRegister (evt) {
            evt.preventDefault()

            axios({
                method: 'post',
                url: `/register`,
                data: {
                    name: this.registerName,
                    email: this.registerEmail,
                    password: this.registerPassword
                }
            })
            .then( ({data}) => {
                this.registerEmail = ''
                this.registerName = ''
                this.registerPassword = ''
                Swal.fire(
                    'Registration success!',
                    'Please log in with your new account.',
                    'success'
                )
            })
            .catch(err => {
                this.fireError(err.response.data.message)
            })
        },
        submitLogin (evt) {
            evt.preventDefault()
            axios({
                method: 'post',
                url: `/login`,
                data: {
                    email: this.loginEmail,
                    password: this.loginPassword
                }
            })
            .then( ({data}) => {
                localStorage.setItem('jwt_token', data)
                this.$emit('change-login', true);
                this.changePage('home');
            })
            .catch(err => {
                this.fireError(err.response.data.message)
            })
        },
        changePage(value) {
            this.$emit('change-page', value);
        },
        fireError(msg) {
            Swal.fire({
                title: 'Error!',
                text: msg,
                icon: 'error',
                confirmButtonText: 'Cool'
            })
        },
        onSuccess(googleUser){
        let gProfile = googleUser.getBasicProfile();
        axios({
            method: 'post',
            url: `/gsign`,
            data: {
                gProfile
            }
        })
        .then( ({data}) => {
            localStorage.setItem('jwt_token', data)
            this.$emit('change-login', true);
            this.changePage('home');
        })
        .catch(err => {
            console.log(err)
        })
        },
        onFailure(){}
    },
    mounted () {
        const signUpButton = document.getElementById('signUp');
        const signInButton = document.getElementById('signIn');
        const container = document.getElementById('container');

        signUpButton.addEventListener('click', () => {
            container.classList.add("right-panel-active");
        });

        signInButton.addEventListener('click', () => {
            container.classList.remove("right-panel-active");
        });
    }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css?family=Montserrat:400,800');

* {
	box-sizing: border-box;
}

body {
	background: #f6f5f7;
	display: flex;
	justify-content: center;
	align-items: center;
	flex-direction: column;
	font-family: 'Montserrat', sans-serif;
	height: 100vh;
	margin: -20px 0 50px;
}

h1 {
	font-weight: bold;
	margin: 0;
}

h2 {
	text-align: center;
}

p {
	font-size: 14px;
	font-weight: 100;
	line-height: 20px;
	letter-spacing: 0.5px;
	margin: 20px 0 30px;
}

span {
	font-size: 12px;
}

a {
	color: #333;
	font-size: 14px;
	text-decoration: none;
	margin: 15px 0;
}

button {
	border-radius: 20px;
	border: 1px solid #3f4d71;
	background-color: #3f4d71;
	color: #FFFFFF;
	font-size: 12px;
	font-weight: bold;
	padding: 12px 45px;
	letter-spacing: 1px;
	text-transform: uppercase;
	transition: transform 80ms ease-in;
}

button:active {
	transform: scale(0.95);
}

button:focus {
	outline: none;
}

button.ghost {
	background-color: transparent;
	border-color: #FFFFFF;
}

form {
	background-color: #FFFFFF;
	display: flex;
	align-items: center;
	justify-content: center;
	flex-direction: column;
	padding: 0 50px;
	height: 100%;
	text-align: center;
}

input {
	background-color: #eee;
	border: none;
	padding: 12px 15px;
	margin: 8px 0;
	width: 100%;
}

.container {
	background-color: #fff;
	border-radius: 10px;
  	box-shadow: 0 14px 28px rgba(0,0,0,0.25), 
			0 10px 10px rgba(0,0,0,0.22);
	position: relative;
	overflow: hidden;
	width: 768px;
	max-width: 100%;
	min-height: 480px;
}

.form-container {
	position: absolute;
	top: 0;
	height: 100%;
	transition: all 0.6s ease-in-out;
}

.sign-in-container {
	left: 0;
	width: 50%;
	z-index: 2;
}

.container.right-panel-active .sign-in-container {
	transform: translateX(100%);
}

.sign-up-container {
	left: 0;
	width: 50%;
	opacity: 0;
	z-index: 1;
}

.container.right-panel-active .sign-up-container {
	transform: translateX(100%);
	opacity: 1;
	z-index: 5;
	animation: show 0.6s;
}

@keyframes show {
	0%, 49.99% {
		opacity: 0;
		z-index: 1;
	}
	
	50%, 100% {
		opacity: 1;
		z-index: 5;
	}
}

.overlay-container {
	position: absolute;
	top: 0;
	left: 50%;
	width: 50%;
	height: 100%;
	overflow: hidden;
	transition: transform 0.6s ease-in-out;
	z-index: 100;
}

.container.right-panel-active .overlay-container{
	transform: translateX(-100%);
}

.overlay {
	background: #FF416C;
	background: -webkit-linear-gradient(to right, #fd7792, #ffac8e);
	background: linear-gradient(to right, #fd7792,#ffac8e);
	background-repeat: no-repeat;
	background-size: cover;
	background-position: 0 0;
	color: #FFFFFF;
	position: relative;
	left: -100%;
	height: 100%;
	width: 200%;
  	transform: translateX(0);
	transition: transform 0.6s ease-in-out;
}

.container.right-panel-active .overlay {
  	transform: translateX(50%);
}

.overlay-panel {
	position: absolute;
	display: flex;
	align-items: center;
	justify-content: center;
	flex-direction: column;
	padding: 0 40px;
	text-align: center;
	top: 0;
	height: 100%;
	width: 50%;
	transform: translateX(0);
	transition: transform 0.6s ease-in-out;
}

.overlay-left {
	transform: translateX(-20%);
}

.container.right-panel-active .overlay-left {
	transform: translateX(0);
}

.overlay-right {
	right: 0;
	transform: translateX(0);
}

.container.right-panel-active .overlay-right {
	transform: translateX(20%);
}

.social-container {
	margin: 20px 0;
}

.social-container a {
	border: 1px solid #DDDDDD;
	border-radius: 50%;
	display: inline-flex;
	justify-content: center;
	align-items: center;
	margin: 0 5px;
	height: 40px;
	width: 40px;
}

footer {
    background-color: #222;
    color: #fff;
    font-size: 14px;
    bottom: 0;
    position: fixed;
    left: 0;
    right: 0;
    text-align: center;
    z-index: 999;
}

footer p {
    margin: 10px 0;
}

footer i {
    color: red;
}

footer a {
    color: #3c97bf;
    text-decoration: none;
}
</style>