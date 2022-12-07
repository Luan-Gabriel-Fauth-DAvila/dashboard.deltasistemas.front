<template>
    <div id="main">
        <section id="navbar">
            <div id="nav-desktop">
            <div id="nav-left">
                <div id="image">
                    <img :src="hostBack+ '/static/public/logo-oxi.png'" alt="logo" id="image_logo">
                </div>
                <div id="menu">
                        <div id="div-menu_link" v-if="$route.path == '/painel/comercial/'"><a href="/painel/comercial/" id="menu_link">Comercial</a><div id="down-bar"></div></div>
                        <div id="div-menu_link" v-else><a href="/painel/comercial/" id="menu_link" style="color:#aaaaaa;">Comercial</a></div>

                        <div id="div-menu_link" v-if="$route.path == '/painel/financeiro/'"><a href="/painel/financeiro/" id="menu_link">Financeiro</a><div id="down-bar"></div></div>
                        <div id="div-menu_link" v-else><a href="/painel/financeiro/" id="menu_link" style="color:#aaaaaa;">Financeiro</a></div>

                        <div id="div-menu_link" v-if="$route.path == '/painel/estoque/'"><a href="/painel/estoque/" id="menu_link">Estoque</a><div id="down-bar"></div></div>
                        <div id="div-menu_link" v-else><a href="/painel/estoque/" id="menu_link" style="color:#aaaaaa;">Estoque</a></div>    

                        <div id="div-menu_link" v-if="$route.path == '/painel/status/'"><a href="/painel/status/" id="menu_link">Status</a><div id="down-bar"></div></div>
                        <div id="div-menu_link" v-else><a href="/painel/status/" id="menu_link" style="color:#aaaaaa;">Status</a></div>                    
                </div>
            </div>
            <div id="perfil">
                <label>{{ user }}</label>
                <img :src="hostBack+'/static/public/perfil_default.png'" alt="" id="menu_perfil">
            </div>
            </div>


            <!-- NAV-MOBILE -->


            <div id="nav-mobile">
                <div id="menu-d">
                    <div id="menu-drop">
                    <button type="button" id="drop"><span class="material-icons-outlined">menu</span></button>
                    <div id="drop-content">
                        <div id="div-menu_link" v-if="$route.path == '/painel/comercial/'"><a href="/painel/comercial/" id="menu_link">Comercial</a><div id="down-bar"></div></div>
                        <div id="div-menu_link" v-else><a href="/painel/comercial/" id="menu_link" style="color:#aaaaaa;">Comercial</a></div>

                        <div id="div-menu_link" v-if="$route.path == '/painel/financeiro/'"><a href="/painel/financeiro/" id="menu_link">Financeiro</a><div id="down-bar"></div></div>
                        <div id="div-menu_link" v-else><a href="/painel/financeiro/" id="menu_link" style="color:#aaaaaa;">Financeiro</a></div>

                        <div id="div-menu_link" v-if="$route.path == '/painel/estoque/'"><a href="/painel/estoque/" id="menu_link">Estoque</a><div id="down-bar"></div></div>
                        <div id="div-menu_link" v-else><a href="/painel/estoque/" id="menu_link" style="color:#aaaaaa;">Estoque</a></div> 
                    </div>
                    </div>
                </div>
                <div id="image">
                    <img :src="hostBack+'/static/public/logo-oxi.png'" alt="logo" id="image_logo">
                </div>
                <div id="perfil">
                    <img :src="hostBack+'/static/public/perfil_default.png'" alt="" id="menu_perfil">
                    <label>{{ user }}</label>
                </div>
            </div>
        </section>
    </div>
</template>

<script>
export default {
    name: 'Navbar',
    data () {
        return {
            user: null,
        }
    },
    props: {
        hostBack: String,
    },
    methods: {
        getUsername () {
            this.user = sessionStorage.getItem('username')
        },
    },
    mounted () {
        this.getUsername()
    }
}
</script>

<style>
@import url('https://fonts.googleapis.com/css2?family=Poppins:wght@700&display=swap');
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
    font-size: 2vh;
    font-family: 'Poppins', sans-serif;
}
html, body {
    width: 100%;
    height: 100%;
    background-color: #eeeeee;
    /* overflow-x: hidden; */
}
#main {
    position: sticky;
    top: 0;
    left: 0;
    right: 0;
    z-index: 9999;
}
#navbar {
    display: flex;

    position: sticky;
    top: 0;
    left: 0;
    right: 0;
    padding: 0 10%;
    height: 10vh;

    justify-content: space-between;
    align-items: center;
    background-color: #ffffff;
}
#nav-left {
    width: 50%;
    
    display: flex;
    justify-content: space-between;
}
#nav-desktop {
    display: flex;
    justify-content: space-between;
    align-items: center;
    
    width: 100%;
}
#nav-mobile {
    display: none;
}
#image {
    display: flex;
    justify-content: center;
    align-items: center;
}
#image_logo {
    background-size: cover;
    background-position: center;
}

#menu {
    grid-area: menu;
    
    display: flex;
    justify-content: space-between;
    align-items: center;
}
#div-menu_link {
    display: flex;
    flex-direction: column;
    justify-content: space-between;
    align-items: center;

    margin: 0 2vh;
    color: #1B2559;
}
#menu_link {
    display: flex;
    justify-content: space-between;
    align-items: center;

    text-decoration: unset;
    font-weight: 800;
    color: #1B2559;
}
#down-bar {
    width: 2vh;
    height: .5vh;
    background-color: #1B2559;
    border-radius: 5px;
    transform: translateY(3.2vh);
}
#perfil {
    display: flex;
    justify-content: center;
    align-items: center;
}
#perfil label {
    font-size: 2vh;
    margin: 2vh;
    color: #1B2559;
}
@media only screen and (max-width: 630px) {
    #perfil {
        flex-direction: column;
    }
    #perfil label {
        font-size: 2vh;
        margin: 0;
        color: #1B2559;
    }
}
#menu_perfil {
    width: 8vh;
    border-radius: 3vh;
}

#menu-drop {
    display: none;
}
@media only screen and (max-width: 630px) {
    #navbar {
        padding: 0 2%;
    }
    #nav-desktop {
        display: none;
    }
    #nav-mobile {
        display: flex;
        justify-content: space-between;
        align-items: center;
        
        width: 100%;
    }
    #menu-d {
        display: flex;
        justify-content: center;
        align-items: center;
    }
    #menu-drop {
        display: block;
    }
    
    #menu-drop #drop {
        display: flex;
        justify-content: center;
        align-items: center;

        border: none;
        cursor: pointer;
        background-color: white;
        padding: 10px;
        color: #1B2559;
        width: 8vh;
        height: 8vh;
        border-radius: 1vh; 
    }
    #menu-drop #div-menu_link {
        display: none;
    }
    #menu-drop:hover #div-menu_link {
        display: block;
    }
    #menu-drop #drop-content {
        position: absolute;
        height: 0;
        line-height: 8vh;
        border-radius: 1vh;
        background-color: rgba(255, 255, 255, 0); 
        transition: .3s ease-in-out;
    }
    #menu-drop:hover #drop-content {
        height: 24vh;
        background-color: white; 
        color: white;
    }
}
</style>