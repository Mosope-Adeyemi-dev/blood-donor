<template>
    <div class="whole-container">
        <div class="lhs">
            <div class="signup-progress">
                <div class="progress-item" @click="activeSect = 'signup'">
                    <span :class="['item-num', activeSect === 'signup' ? 'active' : '']">
                            1
                                    </span>
                    <p class="item-name">
                        Create
                    </p>
                </div>
                <img src="@/assets/icons/divider.svg" alt="" class="progress-divider">
                <div class="progress-item" @click="activeSect = 'details'">
                    <span class="item-num" :class="['item-num', activeSect === 'details' ? 'active' : '']">
                            2
                        </span>
                    <p class="item-name">
                        Details
                    </p>
                </div>
                <img src="@/assets/icons/divider.svg" alt="" class="progress-divider">
                <div class="progress-item">
                    <span class="item-num">
                            3
                        </span>
                    <p class="item-name">
                        Finish
                    </p>
                </div>
            </div>
            <div class="form-sect">
                <div class="heading">
                    <h1>Join Blood Donation</h1>
                    <h2 v-show="activeSect === 'signup'">We are glad to have you join us. Get started donating for a greater cause!</h2>
                </div>
                <form v-show="activeSect === 'signup'" class="signup-sect" @submit.prevent="signup">
                    <div class="input-sect">
                        <label for="email">Email</label>
                        <input type="email" name="email" v-model="email" placeholder="Email address" required>
                    </div>
                    <div class="input-sect">
                        <label for="password">Password</label>
                        <input type="password" name="password" v-model="password" placeholder="Password (min. 8 characters)" required>
                    </div>
                    <div class="input-sect">
                        <label for="password">Confirm Password</label>
                        <input type="password" name="password" v-model="confirmPassword" placeholder="Password (min. 8 characters)" required>
                    </div>
                    <button v-if="!isLoading" class="btn-org">
                                Continue
                            </button>
                    <button v-if="isLoading" class="btn-org">
                                Loading...
                            </button>
                </form>
                <form v-show="activeSect === 'details'" @submit.prevent="uploadDetails()" enctype="multipart/form-data" class="details-sect">
                    <div class="input-sect">
                        <label>Full Name (Last name, First name)</label>
                        <input type="text" v-model="fullname" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Date of Birth</label>
                        <input type="date" v-model="dob" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Blood Group</label>
                        <select v-model="bloodGroup" required>
                                    <option value="A+">A+</option>
                                    <option value="B+">B+</option>
                                    <option value="AB+">AB+</option>
                                    <option value="O+">O+</option>
                                    <option value="O-">O-</option>
                                    <option value="A-">A-</option>
                                    <option value="B-">B-</option>
                                    <option value="AB-">AB-</option>
                                </select>
                    </div>
                    <div class="input-sect">
                        <label>Genotype</label>
                        <select v-model="genotype" required>
                                    <option value="AA">AA</option>
                                    <option value="AS">AS</option>
                                    <option value="AC">AC</option>
                                    <option value="SS">SS</option>
                                    <option value="SC">SC</option>
                                    <option value="CC">CC</option>
                                </select>
                    </div>
                    <div class="input-sect">
                        <label>Next of Kin</label>
                        <input type="email" v-model="nextOfKin" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Address</label>
                        <input type="text" v-model="address" placeholder="" required>
                    </div>
                    <!-- <div class="input-sect">
                            <label>Photo</label>
                            <input type="file" id="profilePhoto" name="profilePhoto" required>
                        </div> -->
                    <button v-if="!isLoading" class="btn-org">
                                Finish
                            </button>
                    <button v-if="isLoading" class="btn-org">
                                Loading...
                            </button>
                </form>
                <p class="account-prompt">
                    Already have a account ?
                    <NuxtLink to="/donor">Login now</NuxtLink>
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
                activeSect: 'signup',
                isLoading: false,
                email: '',
                password: '',
                confirmPassword: '',
                fullname: '',
                dob: '',
                bloodGroup: '',
                genotype: '',
                nextOfKin: '',
                address: '',
            }
        },
        methods: {
            async signup() {
                this.isLoading = true
                if (this.password != this.confirmPassword) {
                    Toastify({
                        text: 'Passwords do not match',
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
                } else {
                    this.data = await $fetch('https://api-blood-donor.onrender.com/api/v1/auth/donor/signup', {
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
                            this.isLoading = false
                            this.activeSect = 'details'
                        }).catch((onrejected) => {
                            console.log(onrejected)
                            if (typeof onrejected.response._data.message !== 'string') {
                                for (const x in onrejected.response._data.message) {
                                    Toastify({
                                        text: onrejected.response._data.message || 'An error occurred, try again.',
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
                                    text: onrejected.response._data.message || 'An error occurred, try again.',
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
            },
            async uploadDetails() {
                this.isLoading = true
                this.data = await $fetch('https://api-blood-donor.onrender.com/api/v1/donor/profile/setup', {
                        method: 'PUT',
                        headers: {
                            'content-type': "Application/json",
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        },
                        body: JSON.stringify({
                            fullname: this.fullname,
                            location: this.address,
                            dob: this.dob,
                            bloodGroup: this.bloodGroup,
                            genotype: this.genotype,
                            nextOfKinEmail: this.nextOfKin,
                        })
                    }).then((onfulfilled) => {
                        console.log(onfulfilled)
                        this.isLoading = false
                        this.$router.push("/donor")
                    }).catch((onrejected) => {
                        console.log(onrejected)
                        if (typeof onrejected.response._data.message !== 'string') {
                            for (const x in onrejected.response._data.message) {
                                Toastify({
                                    text: onrejected.response._data.message || 'An error occurred, try again.',
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
                        } else {
                            Toastify({
                                text: onrejected.response._data.message || 'An error occurred, try again.',
                                duration: 3000,
                                close: true,
                                gravity: "top",
                                position: "left",
                                stopOnFocus: true,
                                style: {
                                    background: "rgba(255, 75, 38, 0.85)",
                                },
                            }).showToast();
                        }
                        this.isLoading = false
                    })
            },
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
        margin: 10vh auto;
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
    .input-sect input,
    .input-sect select {
        height: 55px;
        width: 100%;
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
        background: #167EE6;
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