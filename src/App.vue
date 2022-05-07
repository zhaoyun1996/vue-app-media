<template>
    <Header />
    <div class="content">
        <LeftContent />
        <RightContent />
    </div>
    <Footer />
</template>

<script>
import Header from "./components/layout/Header.vue";
import LeftContent from "./components/layout/LeftContent.vue";
import RightContent from "./components/layout/RightContent.vue";
import Footer from "./components/layout/Footer.vue";

export default {
    name: "App",
    components: {
        Header,
        LeftContent,
        RightContent,
        Footer,
    },
};
</script>

<style>
* {
    padding: 0;
    margin: 0;
    box-sizing: inherit;
}

#app {
    height: 100vh;
    width: 100vw;
}

.content {
    height: 85%;
    display: flex;
}

::-webkit-scrollbar {
    width: 6px;
}

::-webkit-scrollbar-thumb {
    border-radius: 4px;
    -webkit-box-shadow: inset 0 0 6px rgb(0 0 0 / 50%);
}

::-webkit-scrollbar-track {
    -webkit-box-shadow: inset #fff;
    border-radius: 4px;
}
</style>
