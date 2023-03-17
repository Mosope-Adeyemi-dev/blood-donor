<template>
    <div class="sidebar">
        <div class="nav-items">
            <NuxtLink to="/hospital/request-history" :class="['nav-item', $route.fullPath.includes('request-history') ? 'active' : '']">
                <img src="@/assets/icons/requests.svg" alt="">
                <p>Request History</p>
            </NuxtLink>
            <NuxtLink to="/hospital/view-donors" :class="['nav-item', $route.fullPath.includes('view-donors') ? 'active' : '']">
                <img src="@/assets/icons/users.svg" alt="">
                <p>Donor</p>
            </NuxtLink>
        </div>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                // activeTab: ''
            }
        }
    }
</script>

<style scoped>
.sidebar {
    width: 250px;
    height: 100vh;
    position: fixed;
    background: #FF4B26;
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
    background: #FF8167;
}
.nav-item.active {
    background: #FF8167;
}
</style>