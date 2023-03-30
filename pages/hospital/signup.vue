<template>
    <div class="whole-container">
        <div class="lhs">
            <div class="form-sect">
                <div class="heading">
                    <h1>Join Donorly 🤝</h1>
                    <h2>Register as a new hospital and start requesting blood donations for patients in need.</h2>
                </div>
                <form action="" @submit.prevent="signup()" class="details-sect">
                    <div class="input-sect">
                        <label>Hospital Name</label>
                        <input type="text" v-model="hospitalName" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Phone Number</label>
                        <input type="text" v-model="phoneNumber" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Email</label>
                        <input type="email" v-model="email" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Address</label>
                        <input type="text" v-model="location" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>License Number</label>
                        <input type="text" v-model="licenseNumber" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Password</label>
                        <input type="password" v-model="password" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Confirm Password</label>
                        <input type="password" v-model="confirmPassword" placeholder="" required>
                    </div>
                    <input type="checkbox" name="terms" class="terms-aggr">
                    <label for="terms">I agree to the Terms & Conditions</label>
                    <button v-if="!isLoading" class="btn-org" type="submit">
                            Continue
                        </button>
                    <button v-if="isLoading" class="btn-org" type="submit">
                            Loading...
                        </button>
                </form>
                <p class="account-prompt">
                    Already have a account ?
                    <NuxtLink to="/hospital/login">Login now</NuxtLink>
                </p>
            </div>
        </div>
        <div class="rhs">
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
                confirmPassword: '',
                hospitalName: '',
                phoneNumber: '',
                licenseNumber: '',
                location: '',
                email: '',
                isLoading: false
            }
        },
        methods: {
            async signup() {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api.onrender.com/api/v1/auth/hospital/signup', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: JSON.stringify({
                            email: this.email,
                            password: this.password,
                            name: this.hospitalName,
                            phoneNumber: this.phoneNumber,
                            licenseNumber: this.licenseNumber,
                            location: this.location,
                        })
                    })
                    .then((onfulfilled) => {
                        console.log(onfulfilled)
                        Toastify({
                            text: 'Signup successful',
                            duration: 3000,
                            close: true,
                            gravity: "top",
                            position: "right",
                            stopOnFocus: true,
                            style: {
                                background: "white",
                                color: "rgba(255, 75, 38, 0.85)"
                            },
                        }).showToast();
                        this.$router.push('/hospital/login')
                        this.isLoading = false
                    }).catch((onrejected) => {
                        console.log(onrejected)
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
                                        color: "rgba(255, 75, 38, 0.85)"
                                    },
                                }).showToast();
                            }
                        } else {
                            Toastify({
                                text: onrejected.response._data.message || 'An error occurred, try again.',
                                duration: 3000,
                                close: true,
                                gravity: "top",
                                position: "right",
                                stopOnFocus: true,
                                style: {
                                    background: "white",
                                    color: "rgba(255, 75, 38, 0.85)"
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
        display: flex;
    }
    .rhs {
        background: url('@/assets/images/signup-bg.png');
        background-size: cover;
        background-repeat: no-repeat;
        min-height: 100vh;
        width: 45vw;
    }
    .lhs {
        width: 55vw;
        min-height: 100vh;
        padding: 30px;
    }
    .signup-progress {
        width: 80%;
        margin: auto;
        display: flex;
        justify-content: space-between;
        position: static;
        /* background: rebeccapurple; */
    }
    .progress-divider {
        /* rotate: 90deg; */
        /* height: 50px; */
        width: 50px;
        /* height: 3px; */
    }
    .progress-item {
        display: flex;
        align-items: center;
        cursor: pointer;
    }
    .item-num {
        border: 1px solid lightgray;
        color: gray;
        height: 35px;
        width: 35px;
        display: flex;
        justify-content: center;
        align-items: center;
        border-radius: 35px;
    }
    .item-name {
        margin-left: 15px;
        color: gray;
    }
    .item-num.active {
        background: #000;
        color: white;
    }
    .form-sect {
        width: 60%;
        margin: 3vh auto;
    }
    .input-sect {
        display: flex;
        flex-direction: column;
        margin: 0 0 25px;
    }
    .input-sect label {
        margin-bottom: 10px;
        color: black;
        font-weight: 700;
        font-size: 13px;
    }
    .input-sect input {
        height: 55px;
        width: 100%;
        font-size: 18px;
        background: #FFFFFF;
        border: 1px solid #D4D4D8;
        border-radius: 8px;
        padding: 15px;
    }
    .heading {
        margin: 0 0 50px;
    }
    .heading h1 {
        font-style: normal;
        font-weight: 700;
        font-size: 28px;
        line-height: 38px;
        color: #18181B;
    }
    .heading h2 {
        font-style: normal;
        font-weight: 400;
        font-size: 16px;
        line-height: 22px;
        color: #52525B;
        margin-top: 20px;
    }
    .btn-org {
        background: #FF4B26;
        border-radius: 8px;
        width: 100%;
        height: 55px;
        color: white;
        font-size: 20px;
        margin-top: 34px;
        border: 0;
        cursor: pointer;
    }
    .terms-aggr {
        margin: 0 10px 0 0;
    }
    .account-prompt {
        margin: 50px 0 0;
        /* text-align: center; */
        color: black;
    }
    .account-prompt a {
        color: blue;
        font-weight: 600;
    }
</style>