<template>
    <!-- <nav>
        <img src="" alt="" class="logo">
        <div class="nav-buttons">
            <button @click="$router.push('/donor')">For Donors</button>
            <button @click="$router.push('/hospital')">For Hospitals</button>
            <button @click="$router.push('/admin')">For Admin</button>
        </div>
    </nav> -->
    <div class="whole-container">
        <div class="content">
            <h1>Welcome to Blood Donation!<br></h1>

            <div class="navigation-buttons">
                <button @click="$router.push('/donor')">For Donors</button>
                <button @click="$router.push('/hospital')">For Hospitals</button>
                <button @click="$router.push('/admin')">For Admin</button>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                leaderBoard: []
            }
        },
        mounted () {
            this.getLeaderBoard()
        },
        methods: {
            async getLeaderBoard() {
                this.isLoading = true
                this.data = await $fetch('https://donorly-api-tax9.onrender.com/api/v1//donors/leaders/dashboard', {
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
        justify-content: center;
    }
    .navigation-buttons {
        margin: 40px auto 0;
    }
    .navigation-buttons button {
        height: 70px;
        width: 400px;
        border: 0;
        border-radius: 10px;
        margin: 0 20px;
        font-size: 22px;
        cursor: pointer;
    }
    .content h1 {
        text-align: center;
        color: white;
    }
</style>