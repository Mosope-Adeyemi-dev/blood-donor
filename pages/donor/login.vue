<template>
    <div class="whole-container">
        <div class="login-section">
            <h2>Login to Blood Donations</h2>
            <h1>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Turpis morbi pulvinar venenatis non.</h1>
            <form action="" class="form-sect">

                <div class="input-sect">
                    <label for="email">Email</label>
                    <input type="email" name="email" placeholder="Email address" required>
                </div>
                <div class="input-sect">
                    <label for="password">Password</label>
                    <input type="password" name="password" placeholder="Password (min. 8 characters)" required>
                    <NuxtLink class="forgot-password" to="#">Forgot Password</NuxtLink>
                </div>
                <button class="login-blk-btn" @click="$router.push('/donor/requests')">
                    Sign In
                </button>
            </form>
            <p class="account-prompt">
                Don't have a account ? <NuxtLink to="/donor/signup">Sign up now</NuxtLink>
            </p>
            <p class="account-prompt">
                Are you a Hospital ? <NuxtLink to="/hospital/signup">Click Here</NuxtLink>
            </p>
        </div>
    </div>
</template>

<script>
    export default {
        
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
}
.form-sect {
    margin: 50px auto;
    /* background: red; */
    /* text-align: center; */
    width: 60%;
}
.input-sect {
    display: flex;
    flex-direction: column;
    margin: 0 0 25px;
}
.input-sect label {
    margin-bottom: 10px;
    color: white;
    font-weight: 700;
    font-size: 13px;
}
.input-sect input {
    height: 55px;
    width: 100%;
    background: #FFFFFF;
    border: 1px solid #D4D4D8;
    border-radius: 8px;
    padding-left: 15px;
}
.account-prompt {
    margin-bottom: 15px;
    text-align: center;
    color: #FFFFFF;
}
.account-prompt a {
    color: #FFFFFF;
    font-weight: 600;
}
.forgot-password {
    color: #FFFFFF;
    text-align: right;
    font-weight: 600;
    margin-top: 10px;
}
</style>