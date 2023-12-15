<template>
    <div class="whole-container">
        <div class="top-sect">
            <h2>Spam Reports</h2>
            <div class="top-flex">
                <h1>View and manage spam requests from donors on the platform. </h1>
                <!-- <input type="Search" placeholder="Search" name="" id=""> -->
            </div>
            <div class="request-sect">
                <div class="table-sect">
                    <table v-if="reports.length > 0">
                        <tr>
                            <th>Spam Reports</th>
                            <th>licenseNumber</th>
                            <!-- <th>Report Status</th> -->
                            <th>Location</th>
                            <th>Date</th>
                            <th></th>
                        </tr>
                        <tr v-for="report in reports" :key="report._id">
                            <td>
                                <p class="bold-tb-txt">{{ report.hospital[0].name }}</p>
                            </td>
                            <td>
                                <p>{{ report.hospital[0].licenseNumber }}</p>
                            </td>
                            <td>
                                <p>{{ report.hospital[0].location }}</p>
                            </td>
                            <td>
                                <p>{{ new Date(report.createdAt).toDateString() }}</p>
                            </td>
                            <td class="action-td">
                                <div class="table-action">
                                    <img src="@/assets/icons/table-menu.svg" alt="actions" @click="activeMenu = report._id">
                                </div>
                                <div class="menu" v-if="activeMenu == report._id" @click="activeMenu = ''">
                                    <div class="close-sect">
                                        <img src="@/assets/icons/close.svg" alt="" @click="activeMenu = ''">
                                    </div>
                                    <!-- <p class="">Delete</p> -->
                                    <p class="delete" @click="deactivateHospital(report._id, report.hospital[0]._id)">De-activate Hospital</p>
                                </div>
                            </td>
                        </tr>
                    </table>
                    <div class="no-table-data" v-if="reports.length < 1">
                        <p v-if="isLoading == false ">No records found</p>
                        <p v-if="isLoading == true">Loading...</p>
                        <button v-if="!isLoading" @click="getReports()" class="reload-btn">
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
                reports: [],
                isLoading: false,
                activeMenu: '',
            }
        },
        mounted() {
            this.getReports()
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
                this.spotlightDonor = this.requestHistory.filter((obj) => obj.id == id)[0]
                console.log(this.spotlightDonor)
            },
            respondToRequest(isAccepted) {
                if (!isAccepted) {
                    //decline request
                    this.showDetailModel = false,
                        this.requestSent = false,
                        this.requestSuccessful = false,
                        this.spotlightDonor = undefined
                } else {
                    //accept logic
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
            async getReports() {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api-tax9.onrender.com/api/v1/admin/reports/list', {
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
                        this.reports = onfulfilled.data
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
            async deactivateHospital(reportId, hospitalId) {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api-tax9.onrender.com/api/v1/admin/hospital/deactivate', {
                        method: 'POST',
                        headers: {
                            'content-type': "Application/json"
                        },
                        body: {
                            reportId,
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
        top: -20px;
        left: -100px;
        border-radius: 10px;
        border: 1px solid lightgray;
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
        background: black;
        border-radius: 5px;
        width: 142px;
        height: 35px;
        border: 0;
        cursor: pointer;
        color: white;
        font-size: 16px;
    }
</style>