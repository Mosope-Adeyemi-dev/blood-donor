<template>
    <div class="sidebar">
        <div class="nav-items">
            <NuxtLink to="/donor/requests" :class="['nav-item', $route.fullPath.includes('requests') ? 'active' : '']">
                <img src="@/assets/icons/requests.svg" alt="">
                <p>Requests</p>
            </NuxtLink>
            <NuxtLink to="/donor/history" :class="['nav-item', $route.fullPath.includes('history') ? 'active' : '']">
                <img src="@/assets/icons/requests.svg" alt="">
                <p>History</p>
            </NuxtLink>
            <!-- <NuxtLink to="/donor/leaderboard" :class="['nav-item', $route.fullPath.includes('leaderboard') ? 'active' : '']">
                <img src="@/assets/icons/requests.svg" alt="">
                <p>Leader Board</p>
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
            this.name = localStorage.getItem('name' || 'fullname') | ''
        },
        methods: {
            logout() {
                localStorage.removeItem("token");
                localStorage.removeItem("isLoggedIn")
                localStorage.removeItem("email")
                localStorage.removeItem("name" || "fullname")

                this.$router.push('/donor')
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
    /* background: #FF4B26; */
    background: #7C00B7;
}
.nav-items {
    margin-top: 40px;
}
.nav-item.name {
    font-size: 22px;
    font-weight: 600;
    margin-bottom: 30px;
    cursor: default;
}
.nav-item.name:hover {
    /* background: #FF4B26; */
    background: #d488f780;
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
.nav-item img {
    margin: 0 15px 0 0;
}
.nav-item p {
    font-style: normal;
    font-weight: 500;
    font-size: 16px;
    line-height: 24px;
    color: #FFFFFF;
}
.nav-item:hover {
    /* background: #FF8167; */
    background: #d488f780;
}
.nav-item.active {
    /* background: #FF8167; */
    background: #d488f780;
}
</style>