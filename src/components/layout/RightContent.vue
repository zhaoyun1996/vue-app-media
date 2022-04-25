<template lang="">
    <div id="right-content">
        <Image v-if="false" />
        <Audio />
    </div>
</template>
<script>
import Image from '../media/Image.vue'
import Audio from '../media/Audio.vue'

export default {
    name: "RightContent",
    components: {
        Image,
        Audio
    }
}
</script>
<style lang="css" scoped>
#right-content {
    width: 85%;
    background-color: pink;
    overflow: auto;
    overflow: overlay;
}
</style>
