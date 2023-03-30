<template>
    <div class="whole-container">
        <div class="top-sect">
            <h2>Request History</h2>
            <div class="top-flex">
                <h1>View the all request for blood donations you have made.</h1>
            </div>
            <div class="request-sect">
                <div class="table-sect" >
                    <table v-if="requestHistory.length > 0">
                        <tr>
                            <th>ID</th>
                            <th>Hospital Name <p></p></th>
                            <th>Date</th>
                            <th>Location</th>
                            <th>Status</th>
                        </tr>
                        <tr v-for="request in requestHistory" :key="request.id">
                            <td>
                                <p>{{ request.hospital[0]._id.slice(0, 8) }}</p>
                            </td>
                            <td>
                                <p class="bold-tb-txt">{{ request.hospital[0].name  }}</p>
                            </td>
                            <td>
                                <p>{{ new Date(request.createdAt).toLocaleString()  }}</p>
                            </td>
                            <td>
                                <p>{{ request.hospital[0].location }}</p>
                            </td>
                            <td>
                                <div class="flex-tb-cnt">
                                    <p :class="['status', request.status == 'completed' ? 'green' : request.status == 'pending' ? 'yellow' : request.status == 'rejected' ? 'red' : request.status == 'accepted' ? 'orange' : 'gray']"></p>
                                    <p class="bold-tb-txt">{{ request.status }}</p>
                                </div>
                            </td>
                        </tr>
                    </table>
                    <div class="no-table-data" v-if="requestHistory.length < 1">
                        <p v-if="isLoading == false ">No records found</p>
                        <p v-if="isLoading == true">Loading...</p>
                        <button v-if="!isLoading" @click="getRequestsHistory()" class="reload-btn">
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
                layout: 'donor'
            })
        },
        data() {
            return {
                requestHistory: [],
                isLoading: false
            }
        },
        mounted() {
            this.getRequestsHistory()
        },
        methods: {
            async getRequestsHistory() {
                this.isLoading = true
                this.data = await $fetch('https://localhost:8001/api/v1/donor/donation/list', {
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
                                    gravity: "top",
                                    position: "right", 
                                    stopOnFocus: true,
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
            },
        }
    }
</script>

<style scoped>
    .whole-container {
        min-height: 100vh;
        background: red;
        margin-left: 250px;
        padding: 50px;
    }
    .top-sect {

    }
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
        /* background: red; */
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
    .status.orange {
        background: #9567ff;
    }
    .status.red {
        background: red
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
    .table-sect td,  .table-sect th{
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