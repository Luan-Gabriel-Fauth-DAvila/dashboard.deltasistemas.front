<template> 
    <section id="login">
        <section id="login_info">
            <img :src="hostBack + '/static/public/login/image.png'" alt="image" id="image">
            <img :src="hostBack + '/static/public/login/title.png'" alt="title" id="title">
        </section>
        <section id="login_screen">
            <img :src="hostBack + '/static/public/delta-logo.png'" alt="logo" id="delta_logo">
            <form method="POST">
                <input type="hidden" name="next" v-model="next">
                <div v-show="validLogin" class="input-field">
                    <p id="incorrect_pass">Email ou Usuário inválido</p>
                </div>
                <div class="input-field">
                    <p for="username">Email ou Usuário</p>
                    <input type="text" id="username" name="username" placeholder=" seuemail@email.com" v-model="username">
                </div>
                <div class="input-field">
                    <p for="password">Senha</p>
                    <input type="password"  id="password" name="password" autocomplete='false' v-model="password">
                </div>
                <div class="checkbox-field">
                    <input type="checkbox" id="checkbox" v-model="remember">
                    <label for="checkbox">Lembrar-me</label>
                </div>
                <input type="submit" id="submit" @click="login($event)">
            </form>
        </section>
    </section>
</template> 

<script>
export default {
    name: 'Login',
    components: {
    },
    data () {
        return {
            username: '',
            password: '',
            remember: '',
            next: '',
            // hostBack: 'http://188.166.65.228:8000',
            // hostFront: 'http://188.166.65.228:3000',
            hostBack: 'http://127.0.0.1:8000',
            hostFront: 'http://127.0.0.1:3000',
            validLogin: null
        }
    },
    methods: {
        async login (e) {
            e.preventDefault()
            let data = {
                username: this.username,
                password: this.password
            }
            const req = await fetch(this.hostBack+'/jwt/create/',{
                method: 'POST',
                body: JSON.stringify(data),
                headers: {'Content-Type': 'application/json'}
            })
            const res = await req.json()
            
            if (req.status == '200') {
                sessionStorage.setItem('access', res.access)
                sessionStorage.setItem('refresh', res.refresh)
                sessionStorage.setItem('username', this.username)
                this.validLogin = false
                // console.log(sessionStorage.getItem('access'))
                window.location.href = (this.hostFront+'/painel/comercial/')
            }else {
                this.validLogin = true;
                // this.username = '';
                this.password = ''
            }
        },
        async verifyLogin () {
            try {
                var token = sessionStorage.getItem('access')
            }catch (e) {
                var token = ''
            }
            let data = {
                token: token
            }
            const req = await fetch(this.hostBack+'/jwt/verify/',{
                method: 'POST',
                body: JSON.stringify(data),
                headers: {'Content-Type': 'application/json'}
            })
            const res = await req.json()
            
            if (req.status == '200') {
                let data = {
                    token: sessionStorage.getItem('refresh')
                }
                const req = await fetch(this.hostBack+'/jwt/verify/',{
                    method: 'POST',
                    body: JSON.stringify(data),
                    headers: {'Content-Type': 'application/json'}
                })
                window.location.href = (this.hostFront+'/painel/comercial/')
            }
        }
    },
    created () {
        this.verifyLogin()
    }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@300&display=swap');
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
  font-size: 2vh;
  font-family: 'Poppins', sans-serif;
  /* border: 1px solid salmon; */
}
small {
    line-height: 2.5vh;
    font-size: 1vh;
}
label {
    margin: unset;
    padding: unset;
    line-height: 2.5vh;
    font-size: 1.3vh;

    display: flex;
    align-items: center;
}
h2 {
    font-size: 3vh;
    line-height: 3vh;
}
h4 {
    line-height: 2.5vh;
}
h6 {
    margin-left: 1vh;
    font-size: 1.5vh;
    font-weight: bold;
}
html, body {
    width: 100vw;
    height: 100vh;
}
#login {
    width: 100%;
    height: 100%;
    
    display: grid;
    grid-template-columns: 4fr 2fr;
    grid-template-rows: 100vh;
    grid-template-areas: "info login"
}

/**************************************/

#login_info {
    grid-area: info;

    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}
@media only screen and (max-width:630px) {
    #login {
        grid-template-areas: "login login"
    }
    #login_info {
        display: none;
    }
    #login_screen {
    background: linear-gradient(180deg,#6452FF,#3b2cc2);
    }
}
#image {
    height: 35%;
}
#title {
    height: 10%;
    margin-top: 5%;
}

/**************************************/

#login_screen {
    grid-area: login;

    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    background-color: #6452FF;
}
#delta_logo {
    height: 25%;
    margin-bottom: 10%;
}
form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;

    width: 100%;
    height: 30%;
    margin-bottom: 35%;
}
#incorrect_pass {
    color: #fff ; 
    border: 1px solid #fff; 
    border-radius: 2vh;
    line-height: 5vh;
    text-align: center;
}
.input-field {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    
    width: 100%;
    height: 30%;
}
.checkbox-field {
    display: flex;
    justify-content: center;
    align-items: center;
    
    width: 100%;
    height: 25%;
}
.checkbox-field label {
    width: 100%;
    margin-left: 20%;
}
#checkbox {
    display: none;
}
#checkbox + label:before {
    content: '';
    display: inline-block;
    font-size: 1.5vh;
    width: 1em;
    height: 1em;
    border-radius: 1vh;
    margin-right: 5px;
    border: 0.1vh solid white;
    margin-bottom: -2px;
}
#checkbox:checked + label:before {
    content: "\2713";
}
input {
    width: 60%;
    line-height: 4vh;
    background-color: white;
    border: unset;
    border: 1px solid #eaeaea;
    border-radius: 1vh;
}
p {
    width: 60%;
    font-size: 1.5vh;
    text-align: left;
    color: white;
}
input:focus-visible {
    outline: unset;
}
label {
    color: white;
}
#submit {
    background-color: #05CD99;
    border: 2px solid #05CD99;
    transition: .3s ease-in-out;
}
#submit:hover {
    background-color: #04a67b;
    border: 2px solid #04a67b;
    color: white;
}
#submit:active {
    background-color: #048f6a;
    border: 2px solid #048f6a;
    color: white;
}
</style>