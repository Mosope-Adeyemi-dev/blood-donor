<!-- donorSideBar.vue -->
<template>
    <div class="dashboard">
        <donorSideBar />
        <slot class="nuxt-slot"></slot>
    </div>
</template>

<script>
    export default {
        head: {
            link: [
                {
                    rel: "stylesheet",
                    href: "https://fonts.googleapis.com/css2?family=DM+Sans:ital,wght@0,400;0,500;0,700;1,400&family=Noto+Sans+Tangsa:wght@400;500;600;700&display=swap",
                },
            ],
        },
        mounted () {
            const token = localStorage.getItem("token")
            if(!token) {
                this.$router.push('/donor')
            }
        }
    }
</script>

<style>
 @import url('https://fonts.googleapis.com/css2?family=DM+Sans:ital,wght@0,400;0,500;0,700;1,400&family=Noto+Sans+Tangsa:wght@400;500;600;700&display=swap');
html {
  font-family: 'DM Sans', sans-serif;
  font-size: 16px;
  word-spacing: 1px;
  -ms-text-size-adjust: 100%;
  -webkit-text-size-adjust: 100%;
  -moz-osx-font-smoothing: grayscale;
  -webkit-font-smoothing: antialiased;
  box-sizing: border-box;
  scroll-behavior: smooth;
  -webkit-user-select: none;
  -khtml-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  -o-user-select: none;
  user-select: none;
}
button {
  -webkit-appearance: none;
  -moz-appearance: none;
  appearance: none;
}
::-webkit-scrollbar {
  display: none;
}
*,
*::before,
*::after {
  box-sizing: border-box;
  margin: 0;
}
a, a::before, a::after{
  text-decoration: none;
}
</style>