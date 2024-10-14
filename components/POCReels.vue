<script setup lang="ts">
const props = defineProps<{
    currentVideoId: number;
    videoList: Array<{
        id: number;
        name: string;
        avatar: string;
        video: string;
        subtitle: string;
    }>;
}>();

const orderedVideoList = ref(props.videoList);
const isLoading = ref(true)
const currentlyPlaying = ref<number>(0)
let observer: IntersectionObserver | null = null;

onBeforeMount(() => {
    const currentVideoIndex = props.videoList.findIndex((video) => video.id === props.currentVideoId);
    const currentVideo = props.videoList[currentVideoIndex];
    orderedVideoList.value.splice(currentVideoIndex, 1);
    orderedVideoList.value.unshift(currentVideo);
})

onMounted(() => {
    const videoList = document.querySelectorAll('#reel');

    observer = new IntersectionObserver((entries) => {
        entries.forEach((entry) => {
            if (entry.isIntersecting) {
                (entry.target as HTMLVideoElement).play();
                currentlyPlaying.value = Array.from(videoList).findIndex((video) => video === entry.target);

                if (currentlyPlaying.value < videoList.length - 1) {
                    (videoList[currentlyPlaying.value + 1] as HTMLVideoElement).preload = 'auto';
                }
            } else {
                (entry.target as HTMLVideoElement).pause();
            }
        });
    });

    videoList.forEach((video) => {
        if (observer) {
            observer.observe(video);
        }
    });
});

onUnmounted(() => {
    if (observer) {
        observer.disconnect();
    }
});

function handleLoadedStart() {
    isLoading.value = true;
}

function pauseVideo(event: MouseEvent | TouchEvent) {
    const video = event.target as HTMLVideoElement;
    video.pause();
}

function resumeVideo(event: MouseEvent | TouchEvent) {
    const video = event.target as HTMLVideoElement;
    video.play();
}

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
                <video id="reel" :src="video.video" :muted="true" playsinline preload="none"
                    @loadstart="handleLoadedStart" @loadeddata="isLoading = false" @mousedown="pauseVideo"
                    @mouseup="resumeVideo" @touchstart="pauseVideo" @touchend="resumeVideo"></video>
                <div v-if="isLoading" class="loading">Carregando...</div>

                <div class="video__user">
                    <p class="user-name">{{ video.name }}</p>
                </div>

                <div class="video__actions">
                    <img :src="video.avatar" alt="avatar" />
                    <div class="like">
                        <IconHeart />
                        <span>1,2 mil</span>
                    </div>
                    <IconShare />
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
    bottom: 0;
    right: 0;
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
            cursor: pointer;
        }
    }

    .video-container {
        display: flex;
        flex-direction: column;
        scroll-snap-type: y mandatory;
        overflow-y: scroll;
        scroll-behavior: smooth;
        height: 100vh;
        height: 100dvh;
        min-height: -webkit-fill-available;
        scrollbar-width: none;
        -ms-overflow-style: none;

        @media (min-width: 768px) {
            max-width: 600px;
            margin: 0 auto;
        }

        &__wrapper {
            position: relative;

            &::after {
                content: '';
                position: absolute;
                left: 0;
                right: 0;
                bottom: 0;
                background: linear-gradient(0deg, rgba(0, 0, 0, 0.5) 0%, rgba(0, 0, 0, 0) 100%);
                z-index: 10;
                height: 68px;
            }
        }

        video {
            height: 100vh;
            height: 100dvh;
            width: 100%;
            object-fit: cover;
            scroll-snap-align: start;
            background-color: black;
        }

        .loading {
            position: absolute;
            top: 50%;
            left: 50%;
            transform: translate(-50%, -50%);
            color: white;
            font-size: 24px;
            font-weight: 700;
            z-index: 2000;
        }

        .video {
            &__user {
                position: absolute;
                left: 42px;
                bottom: 56px;
                z-index: 11;

                @media(max-width: 768px) {
                    left: 16px;
                }

                .user-name {
                    color: white;
                    font-size: 16px;
                    font-weight: 700;
                    margin: 0;
                }
            }

            &__actions {
                position: absolute;
                right: 42px;
                bottom: 56px;
                z-index: 11;
                display: flex;
                flex-direction: column;
                justify-content: center;
                align-items: center;
                gap: 26px;

                @media(max-width: 768px) {
                    right: 16px;
                }

                img {
                    width: 48px;
                    height: 48px;
                    border-radius: 50%;
                    border: 1px solid white;
                }

                .like {
                    display: flex;
                    flex-direction: column;
                    gap: 1px;
                    justify-content: center;
                    align-items: center;

                    span {
                        color: white;
                        font-size: 14px;
                        font-weight: 700;
                    }
                }

                svg {
                    width: 28px;
                    height: 28px;

                    path {
                        fill: white;
                    }
                }
            }
        }

    }
}
</style>