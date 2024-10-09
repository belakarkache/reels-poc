<script setup lang="ts">
const props = defineProps<{
    currentVideoId: number;
    videoList: Array<{
        id: number;
        name: string;
        avatar: string;
        video: string;
    }>;
}>();

const orderedVideoList = ref(props.videoList);
let observer: IntersectionObserver | null = null;

onBeforeMount(() => {
    const currentVideoIndex = props.videoList.findIndex((video) => video.id === props.currentVideoId);
    const currentVideo = props.videoList[currentVideoIndex];
    orderedVideoList.value.splice(currentVideoIndex, 1);
    orderedVideoList.value.unshift(currentVideo);
})

onMounted(() => {
    const videoList = document.querySelectorAll('video');
    observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
            if (entry.isIntersecting) {
                entry.target.play();
            } else {
                entry.target.pause();
            }
        });
    });

    videoList.forEach((video) => {
        observer.observe(video);
    });
});

onUnmounted(() => {
    if (observer) {
        observer.disconnect();
    }
});

</script>

<template>
    <div class="reels-container">
        <div class="reels-container__header">
            <svg xmlns="http://www.w3.org/2000/svg" width="1em" height="1em" viewBox="0 0 24 24"
                @click="$emit('close')">
                <path fill="none" stroke="white" stroke-dasharray="12" stroke-dashoffset="12" stroke-linecap="round"
                    stroke-linejoin="round" stroke-width="2" d="M8 12l7 -7M8 12l7 7">
                    <animate fill="freeze" attributeName="stroke-dashoffset" dur="0.3s" values="12;0" />
                </path>
            </svg>
        </div>

        <div class="video-container">
            <div v-for="video in orderedVideoList" :key="video.id" class="video-container__wrapper">
                <video :src="video.video" :muted="true" playsinline></video>

                <div class="video__user">
                    <img :src="video.avatar" alt="avatar" />
                    <span>{{ video.name }}</span>
                </div>
            </div>
        </div>
    </div>
</template>

<style lang="scss">
.reels-container {
    position: fixed;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-color: black;
    z-index: 10;

    &__header {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        background: linear-gradient(180deg, rgba(0, 0, 0, 0.5) 0%, rgba(0, 0, 0, 0) 100%);
        padding: 16px;
        z-index: 11;

        svg {
            width: 24px;
            height: 24px;
        }
    }

    .video-container {
        display: flex;
        flex-direction: column;
        height: 100%;
        scroll-snap-type: y mandatory;
        overflow-y: scroll;
        scroll-behavior: smooth;

        &__wrapper {
            position: relative;
        }

        video {
            width: 100%;
            height: 100vh;
            object-fit: contain;
            scroll-snap-align: start;
            background-color: black;
        }

        .video__user {
            position: absolute;
            bottom: 24px;
            left: 16px;
            display: flex;
            gap: 8px;
            align-items: center;

            img {
                width: 32px;
                height: 32px;
                border-radius: 50%;
            }

            span {
                color: white;
                font-size: 16px;
                font-weight: 700;
            }
        }
    }
}
</style>