<template>
    <div class="sidebar">
        <div class="nav-items">
            <div class="nav-item name">
                <p>{{ name.toUpperCase() }}</p>
            </div>
            <NuxtLink to="/hospital/request-history" :class="['nav-item', $route.fullPath.includes('request-history') ? 'active' : '']">
                <img src="@/assets/icons/requests.svg" alt="">
                <p>Request History</p>
            </NuxtLink>
            <NuxtLink to="/hospital/view-donors" :class="['nav-item', $route.fullPath.includes('view-donors') ? 'active' : '']">
                <img src="@/assets/icons/users.svg" alt="">
                <p>Donor</p>
            </NuxtLink>
            <!-- <NuxtLink to="/hospital/subscription" :class="['nav-item', $route.fullPath.includes('subscription') ? 'active' : '']">
                <img src="@/assets/icons/subscription.png" alt="">
                <p>Subscription</p>
            </NuxtLink> -->
        </div>
        <div class="nav-item">
            <button @click="logout()" class="logout-btn">Log Out</button>
        </div>
    </div>
</template>

<script>

    export default {
        data () {
            return {
                name: ''
            }
        },
        mounted () {
            this.name = localStorage.getItem('name' || 'fullname')
        },
        methods: {
            logout() {
                localStorage.removeItem("token");
                localStorage.removeItem("isLoggedIn")
                localStorage.removeItem("email")
                localStorage.removeItem("name" || "fullname")

                this.$router.push('/hospital')
            }
        }
    }
</script>

<style scoped>
.logout-btn {
    width: 100%;
    height: 40px;
    background: white;
    color: black;
    border: 0;
    border-radius: 5px;
    font-weight: 600;
    font-size: 16px;
    cursor: pointer;
}
.sidebar {
    width: 250px;
    height: 100vh;    
    position: fixed;
    background: #167EE6;
}
.nav-items {
    margin-top: 40px;
}
.nav-item {
    width: 90%;
    margin: 0 auto 20px;
    height: 50px;
    display: flex;
    align-items: center;
    border-radius: 8px;
    cursor: pointer;
    padding: 0 20px;
    transition: all .2s;
}
.nav-item.name {
    font-size: 22px;
    font-weight: 600;
    margin-bottom: 30px;
    cursor: default;
}
.nav-item.name:hover {
    background: #64abf1;
}
.nav-item img {
    margin: 0 15px 0 0;
    width: 30px;
}
.nav-item p {
    font-style: normal;
    font-weight: 500;
    font-size: 16px;
    line-height: 24px;
    color: #FFFFFF;
}
.nav-item:hover {
    background: #64abf1;
}
.nav-item.active {
    background: #64abf1;
}
</style>