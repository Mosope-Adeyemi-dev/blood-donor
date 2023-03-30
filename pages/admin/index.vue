<template>
    <div class="whole-container">
        <div class="login-section">
            <h2>Admin Login to Blood Donations</h2>
            <h1>Lorem ipsum dolor sit amet, consectetur adipiscing elit. Turpis morbi pulvinar venenatis non.</h1>
            <form action="" class="form-sect" @submit.prevent="login()">
                <div class="input-sect">
                    <label for="email">Email</label>
                    <input type="email" name="email" placeholder="Email address" v-model="email" required>
                </div>
                <div class="input-sect">
                    <label for="password">Password</label>
                    <input type="password" name="password" placeholder="Password (min. 8 characters)" v-model="password" required>
                    <NuxtLink class="forgot-password" to="#">Forgot Password</NuxtLink>
                </div>
                <button v-if="!isLoading" class="login-blk-btn">
                    Sign In
                </button>
                <button v-if="isLoading" class="login-blk-btn">
                    Loading...
                </button>
            </form>
        </div>
    </div>
</template>

<script>
    import Toastify from 'toastify-js'
    import "toastify-js/src/toastify.css"
    export default {
        data() {
            return {
                email: '',
                password: '',
                isLoading: false
            }
        },
        methods: {
            async login() {
                this.isLoading = true
                this.data = await $fetch('https://api-blood-donor.onrender.com/api/v1/auth/admin/login', {
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
                        Toastify({
                            text: 'Login successful',
                            duration: 3000,
                            close: true,
                            gravity: "top", // `top` or `bottom`
                            position: "right", // `left`, `center` or `right`
                            stopOnFocus: true, // Prevents dismissing of toast on hover
                            style: {
                                background: "rgba(255, 75, 38, 0.85)",
                            },
                        }).showToast();
                        this.isLoading = false
                        this.$router.push('/admin/users')
                    }).catch((onrejected) => {
                        console.log(onrejected)
                        if (typeof onrejected.response._data.error !== 'string') {
                            for (const x in onrejected.response._data.error) {
                                Toastify({
                                    text: onrejected.response._data.error || 'An error occurred, try again.',
                                    duration: 3000,
                                    close: true,
                                    gravity: "top", // `top` or `bottom`
                                    position: "left", // `left`, `center` or `right`
                                    stopOnFocus: true, // Prevents dismissing of toast on hover
                                    style: {
                                        background: "rgba(255, 75, 38, 0.85)",
                                    },
                                }).showToast();
                                // toast.error(onrejected.response._data.error || 'An error occurred, try again.')
                            }
                        } else {
                            Toastify({
                                text: onrejected.response._data.error || 'An error occurred, try again.',
                                duration: 3000,
                                close: true,
                                gravity: "top", // `top` or `bottom`
                                position: "left", // `left`, `center` or `right`
                                stopOnFocus: true, // Prevents dismissing of toast on hover
                                style: {
                                    background: "rgba(255, 75, 38, 0.85)",
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