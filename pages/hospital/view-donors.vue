<template>
    <div v-if="showDetailModel" class="donor-whole-modal">
        <div class="modal-top">
            <img src="@/assets/icons/close.svg" alt="close btn" class="close" @click="closeDetailsModal()">
        </div>
        <div v-show="requestSent == false" class="modal">
            <img :src="spotlightDonor.photo" alt="donor profile " class="modal-pic">
            <p class="donor-name">{{ spotlightDonor.fullname }}</p>
            <hr class="modal-divider">
            <p class="donor-detail modal-type">
                Blood Group - <span class="value">{{  spotlightDonor.bloodGroup }}</span>
            </p>
            <p class="donor-detail modal-type">
                Genotype - <span class="value">{{  spotlightDonor.genotype }}</span>
            </p>
            <p class="donor-detail modal-type">
                Location - <span class="value">{{  spotlightDonor.location || "Nil" }}</span>
            </p>
            <p class="donor-detail modal-type">
                Email Address - <span class="value">{{  spotlightDonor.email || "Nil" }}</span>
            </p>
            <p class="donor-detail modal-type">
                Phone Number - <span class="value">{{  spotlightDonor.phoneNumber || "Nil" }}</span>
            </p>
            <!-- <p class="donor-detail modal-type">
                    Donations left - <span class="value">{{  spotlightDonor.donationsLeft }}</span>
                </p> -->
            <button v-if="!isLoading" class="org-btn" @click="requestDonation()">Request Blood</button>
            <button v-if="isLoading" class="org-btn">Loading...</button>
        </div>
        <div v-show="requestSuccessful == true && spotlightDonor !== undefined" class="modal">
            <img src="@/assets/icons/check-mark.svg" alt="success" class="modal-pic">
            <p class="donor-name">Request Sent Successfully</p>
            <p class="donor-detail">An email notification has been sent to the Donor to alert them of the request.</p>
            <button class="org-btn" @click="closeDetailsModal()">Close</button>
        </div>
    </div>
    <div class="whole-container">
        <div class="top-sect">
            <h2>Registered Donors</h2>
            <div class="top-flex">
                <h1>View the list of available donors and request for blood donations. <br> Remember not to spam requests!</h1>
                <input type="Search" placeholder="Search" name="" id="">
            </div>
        </div>
        <div class="donor-sect" v-if="donors.length > 0">
            <div class="donor-card" v-for="donor in donors" :key="donor._id">
                <img :src="donor.photo" alt="" class="donor-pic">
                <div class="donor-info">
                    <p class="donor-name">
                        {{ donor.fullname }}
                    </p>
                    <p class="donor-detail">
                        Blood Group - <span class="value">{{  donor.bloodGroup }}</span>
                    </p>
                    <p class="donor-detail">
                        Genotype - <span class="value">{{ donor.genotype }}</span>
                    </p>
                    <button @click="showDetails(donor._id)">View Details</button>
                </div>
            </div>
        </div>
        <div class="no-table-data" v-if="donors.length < 1">
            <p v-if="isLoading == false ">No records found</p>
            <p v-if="isLoading == true">Loading...</p>
            <button v-if="!isLoading" @click="getDonors()" class="reload-btn">
                Reload
            </button>
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
                showDetailModel: false,
                requestSent: false,
                requestSuccessful: false,
                donors: [],
                spotlightDonor: undefined,
                isLoading: false
            }
        },
        mounted() {
            this.getDonors()
        },
        methods: {
            requestDonation() {
                this.requestSent = true
                this.requestSuccessful = true
            },
            showDetails(id) {
                this.showDetailModel = true
                this.spotlightDonor = this.donors.filter((obj) => obj._id == id)[0]
                console.log(this.spotlightDonor)
            },
            closeDetailsModal() {
                this.showDetailModel = false,
                    this.requestSent = false,
                    this.requestSuccessful = false,
                    this.spotlightDonor = undefined
            },
            async getDonors() {
                this.isLoading = true
                this.data = await $fetch('https://api-blood-donor.onrender.com/api/v1/hospital/donors/list', {
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
                        console.log(onfulfilled)
                        this.donors = onfulfilled.data
                        console.log(this.donors)
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
                                stopOnFocus: true, // Prevents dismissing of toast on hover
                                style: {
                                    background: "rgba(255, 75, 38, 0.85)",
                                },
                            }).showToast();
                        }
                        this.isLoading = false
                    })
            },
            async requestDonation() {
                this.isLoading = true
                this.data = await $fetch('https://api-blood-donor.onrender.com/api/v1/hospital/donation/request', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: {
                            donor: this.spotlightDonor._id
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        this.isLoading = false
                        console.log(onfulfilled)
                        this.requestSent = true
                        this.requestSuccessful = true
                        Toastify({
                            text: onfulfilled.data.message || "Request Sent!",
                            duration: 3000,
                            close: true,
                            gravity: "top",
                            position: "right",
                            stopOnFocus: true, 
                            style: {
                                background: "rgba(255, 75, 38, 0.85)",
                            },
                        }).showToast();
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
                                stopOnFocus: true, // Prevents dismissing of toast on hover
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
    .donor-whole-modal {
        position: absolute;
        z-index: 3;
        /* position: fixed; */
        width: 100%;
        height: 100vh;
        background: rgba(0, 0, 0, 0.713);
        display: flex;
        justify-content: center;
        align-items: center;
        flex-direction: column;
    }
    .donor-whole-modal .modal {
        background: #FFFFFF;
        box-shadow: 0px 44px 44px -7px rgba(0, 0, 0, 0.08);
        border-radius: 10px;
        width: 410px;
        min-height: fit-content;
        text-align: center;
        position: relative;
        padding: 0 20px;
    }
    .donor-whole-modal .modal-top {
        width: 100%;
        width: 410px;
        margin-bottom: 15px;
        /* background: rebeccapurple; */
    }
    .donor-whole-modal .modal-top img {
        float: right;
        cursor: pointer;
        transition: all .3s;
    }
    .donor-whole-modal .modal-top img:hover {
        background: gray;
        border-radius: 10px;
    }
    .modal-pic {
        width: 80px;
        height: 80px;
        border-radius: 40px;
        margin-top: 35px;
        object-fit: cover;
        object-position: center;
    }
    .modal-divider {
        border: 1px solid #E2E2E2;
        width: 80%;
        margin: 15px auto;
    }
    .org-btn {
        width: 142px;
        height: 35px;
        background: #167EE6;
        border-radius: 5px;
        border: 0;
        color: white;
        margin: 20px 0 30px;
        cursor: pointer;
    }
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
    .donor-sect {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-around;
        align-items: center;
        /* background: beige; */
        margin: 50px 0 0;
    }
    .donor-card {
        width: 450px;
        display: flex;
        margin: 0 0 50px;
        position: relative;
    }
    .donor-pic {
        height: 200px;
        width: 200px;
        object-fit: cover;
        border-radius: 5px;
        background: rgba(211, 211, 211, 0.971);
    }
    .donor-info {
        position: relative;
        margin-left: 20px;
    }
    .donor-name {
        font-style: normal;
        font-weight: 600;
        font-size: 24px;
        line-height: 32px;
        color: #101828;
    }
    .donor-detail {
        margin-top: 5px;
        font-style: normal;
        font-weight: 400;
        font-size: 16px;
        line-height: 24px;
        color: #167EE6;
    }
    .donor-detail.modal-type {
        font-size: 18px;
        margin: 10px 0 15px;
        color: black;
        font-weight: 500;
    }
    .donor-detail .value {
        color: #101828;
        font-weight: 400;
    }
    .donor-info button {
        position: absolute;
        bottom: 10px;
        width: 100%;
        background: #167EE6;
        border-radius: 5px;
        width: 142px;
        height: 35px;
        border: 0;
        cursor: pointer;
        color: white;
        font-size: 16px;
    }
    .no-table-data {
        display: flex;
        flex-direction: column;
        align-items: center;
        justify-content: center;
        font-size: 20px;
        color: black;
        font-weight: 500;
        /* background: rgba(211, 211, 211, 0.305); */
        height: 300px;
        margin-top: 20px;
    }
    .reload-btn {
        /* position: absolute; */
        margin-top: 50px;
        bottom: 10px;
        width: 100%;
        background: #167EE6;
        border-radius: 5px;
        width: 142px;
        height: 35px;
        border: 0;
        cursor: pointer;
        color: white;
        font-size: 16px;
    }
</style>