<template>
    <div class="whole-container">
        <div class="content">
            <h1>Welcome to Donorly!<br></h1>
            <div class="navigation-buttons">
                <button @click="$router.push('/donor')">For Donors</button>
                <button @click="$router.push('/hospital')">For Hospitals</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
            }
        },
    }
</script>

<style scoped>
    .whole-container {
        width: 100%;
        min-height: 100vh;
        background: url('@/assets/images/login-bg.png');
        background-size: cover;
        background-repeat: no-repeat;
        box-shadow: inset 0 0 0 2000px rgba(255, 75, 38, 0.85);
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .navigation-buttons {
        margin: 40px auto 0;
    }
    .navigation-buttons button {
        height: 70px;
        width: 400px;
        border: 0;
        border-radius: 10px;
        margin: 0 20px;
        font-size: 22px;
        cursor: pointer;
    }
    .content h1 {
        text-align: center;
        color: white;
    }
</style>