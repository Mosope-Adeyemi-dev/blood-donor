<template>
    <div class="whole-container">
        <div class="lhs">
            <div class="form-sect">
                <div class="heading">
                    <h1>Join Blood Donations 🤝</h1>
                    <h2>Lorem ipsum dolor sit amet consectetur adipisicing elit. Repudiandae harum totam doloribus quae voluptates incidunt inventore deserunt natus animi debitis.</h2>
                </div>
                <form action="" class="details-sect">
                    <div class="input-sect">
                        <label>Hospital Name</label>
                        <input type="text" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Phone Number</label>
                        <input type="text" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Email</label>
                        <input type="email" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Address</label>
                        <input type="text" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Password</label>
                        <input type="password" placeholder="" required>
                    </div>
                    <div class="input-sect">
                        <label>Confirm Password</label>
                        <input type="password" placeholder="" required>
                    </div>
                    <input type="checkbox" name="terms" class="terms-aggr">
                    <label for="terms">I agree to the Terms & Conditions</label>
                    <button class="btn-org">
                        Continue
                    </button>
                </form>
                <p class="account-prompt">
                    Already have a account ? <NuxtLink to="/hospital/login">Login now</NuxtLink>
                </p>
            </div>
        </div>
        <div class="rhs">

        </div>
    </div>
</template>

<script>
    export default {
        data () {
            return {
                activeSect: 'signup',
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