<template>
    <div>
        <div v-if="showDetailModel" class="donor-whole-modal">
            <div class="modal-top">
                <img src="@/assets/icons/close.svg" alt="close btn" class="close" @click="closeDetailsModal()">
            </div>
            <div v-show="requestSent == false" class="modal">
                <p class="card-title">Donation Request</p>
                <p class="card-subtext">New request for blood donation. Feel free to accept or decline as you choose.</p>
                <p class="card-text">
                    Hospital Name - <span class="value">{{  spotlightDonor.hospital[0].name }}</span>
                </p>
                <p class="card-text">
                    Location - <span class="value">{{  spotlightDonor.hospital[0].location || "Nil" }}</span>
                </p>
                <p class="card-text">
                    Date - <span class="value">{{  new Date(spotlightDonor.createdAt).toDateString() || "Nil" }}</span>
                </p>
                <div class="actions-btns">
                    <button class="outline-btn" @click="respondToRequest(spotlightDonor._id, false)">Decline</button>
                    <button class="org-btn" @click="respondToRequest(spotlightDonor._id, true)">Accept</button>
                </div>
            </div>
        </div>
        <div class="whole-container">
            <div class="top-sect">
                <h2>Donation Requests</h2>
                <div class="top-flex">
                    <h1>View the all request for blood donations you have received. <br>Help keep our platform safe by reporting spam requests.</h1>
                </div>
                <div class="request-sect">
                    <div class="table-sect">
                        <table v-if="requestHistory.length > 0">
                            <tr>
                                <th>ID</th>
                                <th>Hospital Name
                                    <p></p>
                                </th>
                                <th>Date</th>
                                <th>Location</th>
                                <th>Status</th>
                            </tr>
                            <tr v-for="request in requestHistory" :key="request._id">
                                <td>
                                    <p class="opener" @click="showDetails(request._id)">{{ request._id.slice(0, 8) }}</p>
                                </td>
                                <td>
                                    <p class="bold-tb-txt">{{ request.hospital[0].name }}</p>
                                </td>
                                <td>
                                    <p>{{ new Date(request.createdAt).toDateString() }}</p>
                                </td>
                                <td>
                                    <p>{{ request.hospital[0].location }}</p>
                                </td>
                                <td>
                                    <p>{{ request.status }}</p>
                                </td>
                                <td class="action-td">
                                    <div class="table-action">
                                        <img src="@/assets/icons/table-menu.svg" alt="actions" @click="activeMenu = request._id">
                                    </div>
                                    <div class="menu" v-show="activeMenu == request._id" @click="activeMenu = ''">
                                        <div class="close-sect">
                                            <img src="@/assets/icons/close.svg" alt="" @click="activeMenu = ''">
                                        </div>
                                        <p class="delete" @click="reportSpam(request._id, request.hospital[0]._id)">Report Spam</p>
                                        <p class="" @click="showDetails(request._id)">Manage Request</p>
                                    </div>
                                </td>
                            </tr>
                        </table>
                        <div class="no-table-data" v-if="requestHistory.length < 1">
                            <p v-if="isLoading == false ">No records found</p>
                            <p v-if="isLoading == true">Loading...</p>
                            <button v-if="!isLoading" @click="getRequests()" class="reload-btn">
                                            Reload
                                        </button>
                        </div>
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
                layout: 'donor'
            })``
        },
        data() {
            return {
                requestHistory: [
                ],
                activeMenu: '',
                showDetailModel: false,
                requestSent: false,
                requestSuccessful: false,
                spotlightDonor: undefined,
                isLoading: false,
            }
        },
        mounted() {
            this.getRequests()
        },
        methods: {
            sortAscending() {
                this.requestHistory.sort((a, b) => a.name.localeCompare(b.name));
            },
            sortDescending() {
                this.requestHistory.sort((a, b) => b.name.localeCompare(a.name));
            },
            showDetails(id) {
                this.showDetailModel = true
                this.spotlightDonor = this.requestHistory.filter((obj) => obj._id == id)[0]
                console.log(this.spotlightDonor)
            },
            respondToRequest(requestId, isAccepted) {
                if (isAccepted == false) {
                    //decline request
                    this.updateRequest(requestId, 'rejected')
                    this.showDetailModel = false,
                        this.requestSent = false,
                        this.requestSuccessful = false,
                        this.spotlightDonor = undefined
                } else {
                    this.updateRequest(requestId, 'accepted')
                    this.showDetailModel = false,
                        this.requestSent = false,
                        this.requestSuccessful = false,
                        this.spotlightDonor = undefined
                }
            },
            closeDetailsModal() {
                this.showDetailModel = false,
                    this.requestSent = false,
                    this.requestSuccessful = false,
                    this.spotlightDonor = undefined
            },
            async getRequests() {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api-tax9.onrender.com/api/v1/donor/donation/pending', {
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
                        this.requestHistory = onfulfilled.data
                    }).catch((onrejected) => {
                        console.log(onrejected)
                        if (typeof onrejected.response._data.error !== 'string') {
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
            async reportSpam(requestId, reportedHospital) {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api-tax9.onrender.com/api/v1/donor/donation/report', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: {
                            requestId,
                            reportedHospital
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        this.isLoading = false
                        console.log(onfulfilled)
                        Toastify({
                                    text: onfulfilled.data.message || "Spam successfully reported",
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
                                // toast.error(onrejected.response._data.error || 'An error occurred, try again.')
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
            async updateRequest(requestId, status) {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api-tax9.onrender.com/api/v1/donor/donation/update', {
                        method: 'PATCH',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: {
                            requestId,
                            status,
                        },
                        headers: {
                            Authorization: `Bearer ${localStorage.getItem("token")}`
                        }
                    })
                    .then((onfulfilled) => {
                        this.isLoading = false
                        console.log(onfulfilled)
                        Toastify({
                                    text: onfulfilled.data.message || `Request ${status} successfully`,
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
                        console.log(onrejected.response)
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
        /* text-align: center; */
        position: relative;
        padding: 20px 20px 0;
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
    .card-title {
        font-style: normal;
        font-weight: 700;
        font-size: 21px;
        line-height: 32px;
        color: #18181B;
    }
    .card-subtext {
        margin: 6px 0 0;
        font-style: normal;
        font-weight: 400;
        font-size: 15px;
        line-height: 22px;
        color: #71717A;
    }
    .card-text {
        margin-top: 10px;
        font-weight: 400;
        font-size: 16px;
        line-height: 22px;
        color: #71717A;
    }
    .card-text .value {
        color: black;
        font-weight: 700;
    }
    .actions-btns {
        margin-top: 20px;
        display: flex;
        justify-content: space-between;
    }
    .outline-btn {
        background: white;
        width: 142px;
        height: 40px;
        border-radius: 5px;
        border: 1px solid gray;
        color: black;
        margin: 20px 0 30px;
        cursor: pointer;
    }
    .org-btn {
        width: 200px;
        height: 40px;
        /* background: #FF4B26; */
        background: #7C00B7;
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
        padding: 20px 0;
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
    .opener {
        text-decoration: underline;
        color: black;
        cursor: pointer;
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
    .table-action {
        display: flex;
        justify-content: center;
        cursor: pointer;
    }
    .action-td {
        position: relative;
    }
    .menu.show {
        display: block;
    }
    .menu.hide {
        display: none;
    }
    .menu {
        position: absolute;
        height: fit-content;
        min-width: 180px;
        padding: 10px;
        background: white;
        top: -10px;
        left: -100px;
        border-radius: 10px;
        border: 1px solid lightgray;
        z-index: 2;
    }
    .menu p {
        font-size: 14px;
        font-weight: 500;
        /* margin: 10px 0; */
        cursor: pointer;
        padding: 5px 10px;
        color: black;
        transition: all .3s
    }
    .menu .delete {
        color: red;
    }
    .menu p:hover {
        background: rgba(211, 211, 211, 0.355);
    }
    .close-sect {
        width: 100%;
        display: flex;
        justify-content: flex-end;
        margin-bottom: 10px;
        cursor: pointer;
    }
    .close-sect img {
        background: black;
        width: 20px;
        padding: 2px;
        border-radius: 18px;
    }
    .bold-tb-txt {
        color: black;
        font-weight: 500;
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
    }
    .reload-btn {
        /* position: absolute; */
        margin-top: 50px;
        bottom: 10px;
        width: 100%;
        /* background: #FF4B26; */
        background: #7C00B7;
        border-radius: 5px;
        width: 142px;
        height: 35px;
        border: 0;
        cursor: pointer;
        color: white;
        font-size: 16px;
    }
</style>