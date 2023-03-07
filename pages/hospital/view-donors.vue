<template>
    <div v-if="showDetailModel" class="donor-whole-modal">
        <div class="modal-top">
            <img src="@/assets/icons/close.svg" alt="close btn" class="close" @click="showDetailModel = false">
        </div>
        <div class="modal">
            <img src="@/assets/images/donor.png" alt="donor profile " class="modal-pic">
            <p class="donor-name">Oliva Rhye</p>
            <hr class="modal-divider">
            <p class="donor-detail modal-type">
                Blood Group - <span class="value">O Negative</span>
            </p>
            <p class="donor-detail modal-type">
                Genotype - <span class="value">AA</span>
            </p>
            <p class="donor-detail modal-type">
                Address - <span class="value">Ajah Lagos</span>
            </p>
            <p class="donor-detail modal-type">
                Email Address - <span class="value">olivehye@gmail.com</span>
            </p>
            <p class="donor-detail modal-type">
                Phone Number - <span class="value">07082632715</span>
            </p>
            <p class="donor-detail modal-type">
                Donations left - <span class="value">3</span>
            </p>
            <button class="org-btn">Request Blood</button>
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
        <div class="donor-sect">
            <div class="donor-card">
                <img src="@/assets/images/donor.png" alt="" class="donor-pic">
                <div class="donor-info">
                    <p class="donor-name">
                        Olivia Rhye
                    </p>
                    <p class="donor-detail">
                        Blood Group - <span class="value">O Negative</span>
                    </p>
                    <p class="donor-detail">
                        Genotype - <span class="value">AA</span>
                    </p>
                    <button @click="showDetailModel = true">View Details</button>
                </div>
            </div>
            <div class="donor-card">
                <img src="@/assets/images/donor.png" alt="" class="donor-pic">
                <div class="donor-info">
                    <p class="donor-name">
                        Olivia Rhye
                    </p>
                    <p class="donor-detail">
                        Blood Group - <span class="value">O Negative</span>
                    </p>
                    <p class="donor-detail">
                        Genotype - <span class="value">AA</span>
                    </p>
                    <button @click="showDetailModel = true">View Details</button>
                </div>
            </div>
            <div class="donor-card">
                <img src="@/assets/images/donor.png" alt="" class="donor-pic">
                <div class="donor-info">
                    <p class="donor-name">
                        Olivia Rhye
                    </p>
                    <p class="donor-detail">
                        Blood Group - <span class="value">O Negative</span>
                    </p>
                    <p class="donor-detail">
                        Genotype - <span class="value">AA</span>
                    </p>
                    <button @click="showDetailModel = true">View Details</button>
                </div>
            </div>
            <div class="donor-card">
                <img src="@/assets/images/donor.png" alt="" class="donor-pic">
                <div class="donor-info">
                    <p class="donor-name">
                        Olivia Rhye
                    </p>
                    <p class="donor-detail">
                        Blood Group - <span class="value">O Negative</span>
                    </p>
                    <p class="donor-detail">
                        Genotype - <span class="value">AA</span>
                    </p>
                    <button @click="showDetailModel = true">View Details</button>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        setup() {
            definePageMeta({
                layout: 'dashboard'
            })
        },
        data () {
            return {
                showDetailModel: false
            }
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
    width: 359px;
    min-height: fit-content;
    text-align: center;
    position: relative;
}
.donor-whole-modal .modal-top {
    width: 100%;
    width: 359px;
    margin-bottom: 15px;
    /* background: rebeccapurple; */
}
.donor-whole-modal .modal-top img {
    float: right;
    cursor: pointer;
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
}
.modal-divider {
    border: 1px solid #E2E2E2;
    width: 80%;
    margin: 15px auto;
}
.org-btn {
    width: 142px;
    height: 35px;
    background: #FF4B26;
    border-radius: 5px;
    border: 0;
    color: white;
    margin: 20px 0 30px;
}


.whole-container {
  min-height: 100vh;
  /* background: red; */
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
    border-radius: 5px;
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
    color: #FF4B26;
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