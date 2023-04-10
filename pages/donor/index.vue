<template>
    <div class="whole-container">
        <div class="login-section">
            <h2>Login to Blood Donation</h2>
            <h1>Welcome back donor! We are glad to have you back..</h1>
            <form action="" class="form-sect" @submit.prevent="login()">
                <div class="input-sect">
                    <label for="email">Email</label>
                    <input type="email" name="email" v-model="email" placeholder="Email address" required>
                </div>
                <div class="input-sect">
                    <label for="password">Password</label>
                    <input type="password" name="password" v-model="password" placeholder="Password (min. 8 characters)" required>
                    <NuxtLink class="forgot-password" to="#">Forgot Password</NuxtLink>
                </div>
                <button v-if="!isLoading" class="login-blk-btn">
                    Sign In
                </button>
                <button v-if="isLoading" class="login-blk-btn">
                    Loading...
                </button>
            </form>
            <p class="account-prompt">
                Don't have a account ?
                <NuxtLink to="/donor/signup">Sign up now</NuxtLink>
            </p>
            <p class="account-prompt">
                Are you a Hospital ?
                <NuxtLink to="/hospital/signup">Click Here</NuxtLink>
            </p>
        </div>
    </div>
</template>

<script>
    import Toastify from 'toastify-js'
    import "toastify-js/src/toastify.css"
    export default {
        data() {
            return {
                isLoading: false,
                email: "",
                password: ""
            }
        },
        methods: {
            async login() {
                this.isLoading = true
                this.data = await $fetch('https://api-blood-donor.onrender.com/api/v1/auth/donor/login', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: JSON.stringify({
                            email: this.email,
                            password: this.password,
                        })
                    })
                    .then((onfulfilled) => {
                        console.log(onfulfilled)
                        localStorage.setItem("isLoggedIn", true)
                        localStorage.setItem("token", onfulfilled.data.token)
                        localStorage.setItem("email", onfulfilled.data.user.email)
                        localStorage.setItem("name", onfulfilled.data.user.fullname || '')
                        Toastify({
                            text: 'Login successful',
                            duration: 3000,
                            close: true,
                            gravity: "top",
                            position: "right",
                            stopOnFocus: true,
                            style: {
                                background: "rgba(255, 75, 38, 0.85)",
                            },
                        }).showToast();
                        this.isLoading = false
                        this.$router.push('/donor/requests')
                    }).catch((onrejected) => {
                        console.log(onrejected.response)
                        if (typeof onrejected.response._data.message !== 'string') {
                            for (const x in onrejected.response._data.message) {
                                Toastify({
                                    text: onrejected.response._data.message || 'An error occurred, try again.',
                                    duration: 3000,
                                    close: true,
                                    gravity: "top",
                                    position: "right",
                                    stopOnFocus: true,
                                    style: {
                                        background: "white",
                                        color: "red"
                                    },
                                }).showToast();
                            }
                        } else {
                            Toastify({
                                text: onrejected.response._data.message || 'An error occurred, try again.',
                                duration: 3000,
                                close: true,
                                gravity: "top", // `top` or `bottom`
                                position: "right", // `left`, `center` or `right`
                                stopOnFocus: true, // Prevents dismissing of toast on hover
                                style: {
                                    background: "white",
                                    color: "red"
                                },
                            }).showToast();
                        }
                        this.isLoading = false
                    })
            }
        }
    }
</script>

<style scoped>
    .whole-container {
        width: 100%;
        min-height: 100vh;
        background: url('@/assets/images/login-bg.png');
        background-size: cover;
        background-repeat: no-repeat;
        box-shadow: inset 0 0 0 2000px rgba(22, 126, 230, 0.85);
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