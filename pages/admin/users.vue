<template>
    <div class="whole-container">
        <div class="top-sect">
            <h2>Active Users</h2>
            <div class="top-flex">
                <h1>View and manage all active donors and hospitals on the platform.</h1>
                <!-- <input type="Search" placeholder="Search" name="" id=""> -->
            </div>
            <div class="request-sect">
                <div class="slider">
                    <p :class="['slider-item', activeTable == 'donors' ? 'active' : '']" @click="activeTable = 'donors'">
                        Donors
                    </p>
                    <p :class="['slider-item', activeTable == 'hospitals' ? 'active' : '']" @click="activeTable = 'hospitals'">
                        Hospitals
                    </p>
                </div>
                <div class="table-sect">
                    <table v-if="users.length > 0 && activeTable == 'donors'">
                        <tr>
                            <th>Donor Name</th>
                            <th>Email Address</th>
                            <th>Phone Number</th>
                            <th>Join Date</th>
                            <th>Location</th>
                            <th></th>
                        </tr>
                        <tr v-for="user in users" :key="user.id">
                            <td>
                                <div class="flex-item">
                                    <img :src="user.photo" class="tb-user-photo">
                                    <p class="bold-tb-txt">{{ user.fullname }}</p>
                                </div>
                            </td>
                            <td>
                                <p class="bold-tb-txt">{{ user.email }}</p>
                            </td>
                            <td>
                                <div class="flex-item">
                                    <img src="@/assets/icons/phone-icon.png" alt="">
                                    <p class="bold-tb-txt">{{ user.phoneNumber }}</p>
                                </div>
                            </td>
                            <td>
                                <p>{{ new Date(user.createdAt).toLocaleString() }}</p>
                            </td>
                            <td>
                                <p>{{ user.location }}</p>
                            </td>
                            <td>
                                <button class="action-btn">
                                    De-ctivate
                                </button>
                            </td>
                        </tr>
                    </table>
                    <div class="no-table-data" v-if="users.length < 1 && activeTable == 'donors'">
                        <p v-if="isLoading == true">Loading...</p>
                        <p v-if="isLoading == false">No records found</p>
                        <button v-if="!isLoading" @click="getDonors()" class="reload-btn">
                                    Reload
                                </button>
                    </div>
                </div>
                <div class="table-sect" >
                    <table v-if="hospitals.length > 0 && activeTable == 'hospitals'">
                        <tr>
                            <th>Hospital Name</th>
                            <th>Email Address</th>
                            <th>Phone Number</th>
                            <th>Join Date</th>
                            <th>Location</th>
                            <th></th>
                        </tr>
                        <tr v-for="hospital in hospitals" :key="hospital._id">
                            <td>
                                <p class="bold-tb-txt">{{ hospital.name }}</p>
                            </td>
                            <td>
                                <p class="bold-tb-txt">{{ hospital.email }}</p>
                            </td>
                            <td>
                                <div class="flex-item">
                                    <img src="@/assets/icons/phone-icon.png" alt="">
                                    <p class="bold-tb-txt">{{ hospital.phoneNumber }}</p>
                                </div>
                            </td>
                            <td>
                                <p>{{ new Date(hospital.createdAt).toLocaleString() }}</p>
                            </td>
                            <td>
                                <p>{{ hospital.location }}</p>
                            </td>
                            <td>
                                <button v-if="hospital.isActive == true" class="action-btn" @click="deactivateHospital(hospital._id)">
                                            Deactivate
                                        </button>
                                        
                                    <button v-if="hospital.isActive == false" class="action-btn activate" @click="activateHospital(hospital._id)">
                                    Activate
                                </button>
                            </td>
                        </tr>
                    </table>
                    <div class="no-table-data" v-if="hospitals.length < 1 && activeTable == 'hospitals'">
                        <p v-if="isLoading == true">Loading...</p>
                        <p v-if="isLoading == false">No records found</p>
                        <button v-if="!isLoading" @click="getHospitals()" class="reload-btn">
                                    Reload
                                </button>
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
                layout: 'admin'
            })
        },
        data() {
            return {
                isLoading: false,
                users: [],
                hospitals: [
                ],
                activeTable: 'donors'
            }
        },
        mounted() {
            this.getDonors()
            this.getHospitals()
        },
        methods: {
            sortAscending() {
                this.requestHistory.sort((a, b) => a.fullname.localeCompare(b.fullname));
            },
            sortDescending() {
                this.requestHistory.sort((a, b) => b.fullname.localeCompare(a.fullname));
            },
            async getDonors() {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api.onrender.com/api/v1/admin/donors/list', {
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
                        this.users = onfulfilled.data
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
            },
            async getHospitals() {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api.onrender.com/api/v1/admin/hospitals/list', {
                        method: 'GET',
                        headers: {
                            'content-type': "Application/json"
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        console.log(onfulfilled)
                        this.isLoading = false
                        this.hospitals = onfulfilled.data
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
            },
            async deactivateHospital(hospitalId) {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api.onrender.com/api/v1/admin/hospital/deactivate', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: {
                            hospitalId
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        console.log(onfulfilled)
                        this.isLoading = false
                        Toastify({
                            text: onfulfilled.message,
                            duration: 3000,
                            close: true,
                            gravity: "top", // `top` or `bottom`
                            position: "right", // `left`, `center` or `right`
                            stopOnFocus: true, // Prevents dismissing of toast on hover
                            style: {
                                background: "rgba(255, 75, 38, 0.85)",
                            },
                        }).showToast();
                    }).catch((onrejected) => {
                        console.log(onrejected)
                        if (typeof onrejected.response._data.message !== 'string') {
                            for (const x in onrejected.response._data.error) {
                                Toastify({
                                    text: onrejected.response._data.error || 'An error occurred, try again.',
                                    duration: 3000,
                                    close: true,
                                    gravity: "top", // `top` or `bottom`
                                    position: "right", // `left`, `center` or `right`
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
            async activateHospital(hospitalId) {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api.onrender.com/api/v1/admin/hospital/reactivate', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: {
                            hospitalId
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        console.log(onfulfilled)
                        this.isLoading = false
                        Toastify({
                            text: onfulfilled.message,
                            duration: 3000,
                            close: true,
                            gravity: "top", // `top` or `bottom`
                            position: "right", // `left`, `center` or `right`
                            stopOnFocus: true, // Prevents dismissing of toast on hover
                            style: {
                                background: "rgba(255, 75, 38, 0.85)",
                            },
                        }).showToast();
                    }).catch((onrejected) => {
                        console.log(onrejected)
                        if (typeof onrejected.response._data.message !== 'string') {
                            for (const x in onrejected.response._data.error) {
                                Toastify({
                                    text: onrejected.response._data.error || 'An error occurred, try again.',
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
                                text: onrejected.response._data.error || 'An error occurred, try again.',
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
    .slider {
        display: flex;
        width: 90%;
        border-bottom: 1px solid lightgray;
        margin-bottom: 20px;
    }
    .slider-item {
        margin-right: 20px;
        padding: 15px;
        cursor: pointer;
        transition: all .2s;
    }
    .slider-item.active {
        border-bottom: 2px solid #FF4B26;
    }
    .flex-item {
        display: flex;
        align-items: center;
    }
    .tb-user-photo {
        height: 30px;
        width: 30px;
        border-radius: 30px;
        background: #FF4B26;
    }
    .flex-item img {
        margin-right: 10px
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
    .status {
        width: 15px;
        height: 15px;
        border-radius: 10px;
        background: red;
    }
    .status.yellow {
        background: yellow;
    }
    .status.gray {
        background: lightgray;
    }
    .status.green {
        background: rgb(2, 226, 2);
    }
    .request-sect {
        margin-top: 50px;
        /* background: rebeccapurple; */
    }
    .table-sect {
        width: 90%;
        /* background: red; */
        overflow-x: auto;
    }
    .table-sect table {
        border-collapse: collapse;
        width: 100%;
        text-align: left;
    }
    .table-sect tr {
        /* border-bottom: 1px solid lightgray; */
        box-shadow: 0px 1px 0px rgba(133, 133, 133, 0.15);
        color: #71717A;
    }
    .table-sect tr:hover {
        background: rgba(211, 211, 211, 0.187);
    }
    .table-sect th {
        color: #71717A;
        font-size: 16px;
    }
    .table-sect td,
    .table-sect th {
        padding: 18px;
    }
    .flex-tb-cnt {
        display: flex;
        align-items: center;
    }
    .flex-tb-cnt .status {
        margin-right: 10px
    }
    .bold-tb-txt {
        color: black;
        font-weight: 500;
    }
    .action-btn {
        background: white;
        border: 1px solid lightgray;
        height: 40px;
        border-radius: 5px;
        width: 130px;
        color: gray;
        cursor: pointer;
    }
    .action-btn.activate {
        background: #FF4B26;
        color: white;
        font-weight: 500;
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
    .reload-btn {
        /* position: absolute; */
        margin-top: 50px;
        bottom: 10px;
        width: 100%;
        background: #FF4B26;
        border-radius: 5px;
        width: 142px;
        height: 35px;
        border: 0;
        cursor: pointer;
        color: white;
        font-size: 16px;
    }
</style>