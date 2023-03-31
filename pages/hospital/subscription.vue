<template>
    <div class="whole-container">
        <div class="top-sect">
            <h2>Manage Your Subscription</h2>
            <div class="top-flex">
            </div>
            <div class="request-sect">
                <div class="no-table-data">
                    <p v-if="isLoading == true">Retrieving Subscription Details</p>
                    <div v-if="isSubscribed == true" class="sub-dialog">
                        <p>Your are subscribed for this month</p>
                        <div class="sub-info">
                            <p class="value">Subscription Date: <span class="info-side"> {{ new Date(subscriptionDetails.monthDate).toLocaleString() }}</span></p>
                        </div>
                    </div>
                    <div v-if="isSubscribed == false && !isLoading" class="sub-dialog">
                        <p>You have no active monthly subscription plan.</p>
                        <button @click="paySubscription()" class="reload-btn">
                            Pay Subscription
                        </button>
                    </div>
                    <div v-if="verifyingPay == true && !isLoading" class="sub-dialog">
                        <p>Verifying your subscription payment.</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import Toastify from 'toastify-js'
    import "toastify-js/src/toastify.css"
    export default {
        setup() {
            definePageMeta({
                layout: 'dashboard'
            })
        },
        data() {
            return {
                subscriptionDetails: {},
                isSubscribed: false,
                isLoading: false,
                verifyingPay: false
            }
        },
        mounted() {
            this.verifyPayment()
            this.getSubscriptionStatus()
        },
        methods: {
            async getSubscriptionStatus() {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api.onrender.com/api/v1/subscription/month', {
                        method: 'GET',
                        headers: {
                            'content-type': "Application/json"
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        this.isLoading = false
                        this.subscriptionDetails = onfulfilled.data
                        this.isSubscribed = true
                    }).catch((onrejected) => {
                        this.isSubscribed = false
                        console.log(onrejected)
                        if (typeof onrejected.response._data.message !== 'string') {
                            for (const x in onrejected.response._data.message) {
                                Toastify({
                                    text: onrejected.response._data.message || 'An error occurred, try again.',
                                    duration: 3000,
                                    close: true,
                                    gravity: "top", // `top` or `bottom`
                                    position: "right", // `left`, `center` or `right`
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
                                gravity: "top", // `top` or `bottom`
                                position: "right", // `left`, `center` or `right`
                                stopOnFocus: true,
                                style: {
                                    background: "rgba(255, 75, 38, 0.85)",
                                },
                            }).showToast();
                        }
                        this.isLoading = false
                    })
            },
            async paySubscription () {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api.onrender.com/api/v1/subscription/create', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        this.isLoading = false
                        console.log(onfulfilled.data)
                        localStorage.setItem("subReference", onfulfilled.data.reference)
                        window.location.href = `${onfulfilled.data.paystackUrl}`
                    }).catch((onrejected) => {
                        console.log(onrejected)
                        if (typeof onrejected.response._data.message !== 'string') {
                            for (const x in onrejected.response._data.message) {
                                Toastify({
                                    text: onrejected.response._data.message || 'An error occurred, try again.',
                                    duration: 3000,
                                    close: true,
                                    gravity: "top", // `top` or `bottom`
                                    position: "right", // `left`, `center` or `right`
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
                                gravity: "top", // `top` or `bottom`
                                position: "right", // `left`, `center` or `right`
                                stopOnFocus: true,
                                style: {
                                    background: "rgba(255, 75, 38, 0.85)",
                                },
                            }).showToast();
                        }
                        this.isLoading = false
                    })
            },
            async verifyPayment () {
                const reference = localStorage.getItem('subReference')
                if(reference != null) {
                    this.verifyingPay = true
                this.data = await $fetch(`https://donorly-api.onrender.com/api/v1/subscription/verify-transaction?reference=${reference}`, {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        this.verifyingPay = false
                        localStorage.removeItem('subReference')
                    }).catch((onrejected) => {
                        this.verifyingPay = false
                        console.log(onrejected)
                        if (typeof onrejected.response._data.message !== 'string') {
                            for (const x in onrejected.response._data.message) {
                                Toastify({
                                    text: onrejected.response._data.message || 'An error occurred, try again.',
                                    duration: 3000,
                                    close: true,
                                    gravity: "top", // `top` or `bottom`
                                    position: "right", // `left`, `center` or `right`
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
                                gravity: "top", // `top` or `bottom`
                                position: "right", // `left`, `center` or `right`
                                stopOnFocus: true,
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
    }
</script>

<style scoped>
    .whole-container {
        min-height: 100vh;
        /* background: red; */
        margin-left: 250px;
        padding: 50px;
    }
    .top-sect {}
    .top-sect h2 {
        font-weight: 600;
        font-size: 32px;
        line-height: 44px;
        color: #101828;
    }
    .top-flex {
        display: flex;
        justify-content: space-between;
        align-items: center;
        margin-top: 30px;
    }
    .top-flex h1 {
        font-style: normal;
        font-weight: 400;
        font-size: 16px;
        line-height: 24px;
        color: #667085;
    }
    .top-flex input {
        width: 231px;
        height: 44px;
        border: 1px solid #FF8167;
        filter: drop-shadow(0px 1px 2px rgba(16, 24, 40, 0.05));
        border-radius: 8px;
        padding: 15px;
    }
    .sub-info {
        margin-top: 15px;
        font-style: normal;
        font-weight: 400;
        font-size: 18px;
        line-height: 24px;
        color: #FF4B26;
    }
    .info-side {
        color: #FF4B26
    }
    .sub-info .value {
        color: #101828;
        font-weight: 600;
    }
    .no-table-data {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        font-size: 20px;
        color: red;
        font-weight: 500;
        background: rgba(211, 211, 211, 0.305);
        height: 300px;
    }
    .sub-dialog {
        width: 100%;
        text-align: center;
        /* margin: auto; */
    }
    .reload-btn {
        /* position: absolute; */
        margin: 50px auto 0;
        bottom: 10px;
        width: 100%;
        background: #FF4B26;
        border-radius: 5px;
        width: 240px;
        height: 50px;
        border: 0;
        cursor: pointer;
        color: white;
        font-weight: 600;
        font-size: 16px;
    }
</style>