<script setup lang="ts">
import users from '@/constants/users';

const isReelsOpen = ref<boolean>(false);
const currentVideoId = ref<number>(0);

const openReels = (id: number) => {
  currentVideoId.value = id;
  isReelsOpen.value = true;
};
</script>

<template>
  <div class="feed">
    <h1 class="feed__title"> Fotos de vídeos de Reels - POC</h1>

    <div class="feed__container">
      <div class="post" v-for="post in users">
        <div class="post__header">
          <img :src="post.avatar" alt="avatar" />
          <div class="name-container">
            <span>{{ post.name }}</span>
            <span>Cidade - XX</span>
          </div>
        </div>

        <div class="post__video">
          <video width="100%" muted :controls="false" :playsinline="true" preload="none" @click="openReels(post.id)">
            <source :src="post.video" type="video/mp4" />
          </video>
        </div>
      </div>
    </div>

    <POCReels v-if="isReelsOpen" :currentVideoId="currentVideoId" :videoList="users" @close="isReelsOpen = false" />
  </div>
</template>

<style lang="scss">
@import url('https://fonts.googleapis.com/css2?family=Montserrat:ital,wght@0,100..900;1,100..900&display=swap');

* {
  font-family: 'Montserrat', sans-serif;
}

body {
  background-color: rgb(236, 239, 241);
  margin: 0;
  padding: 0;
}

.feed {
  max-width: 780px;
  margin: 0 auto;

  &__title {
    font-size: 32px;
    font-weight: 700;
    color: rgb(38, 50, 56);

    @media(max-width:767px) {
      padding: 20px;
    }
  }

  &__container {
    display: flex;
    flex-direction: column;
    gap: 24px;
  }

  .actions {
    display: flex;
    gap: 10px;
    margin-bottom: 24px;

    .option-toggle {
      background-color: transparent;
      border: 1px solid rgb(38, 50, 56);
      color: rgb(38, 50, 56);
      border-radius: 24px;
      font-size: 14px;
      height: 32px;
      padding: 0 12px;
      cursor: pointer;

      &.active {
        background-color: rgb(38, 50, 56);
        color: white;
      }
    }
  }
}

.post {
  &__header {
    display: flex;
    align-items: center;
    gap: 12px;
    background-color: white;
    padding: 12px;

    img {
      width: 48px;
      height: 48px;
      border-radius: 50%;
    }

    .name-container {
      display: flex;
      flex-direction: column;
      justify-content: center;
      gap: 4px;

      :first-child {
        font-weight: 600;
        color: rgb(38, 50, 56);
      }

      :nth-child(2) {
        font-size: 14px;
        color: rgb(38, 50, 56);
      }
    }
  }

  &__video {
    video {
      width: 100%;
      background-color: black;
      max-height: 80vh;
      object-fit: cover;
    }
  }
}
</style>
