<template>
  <div class="header d-flex align-items-center">
    <div
      @click="$router.back()"
    >
      <img src="../assets/arrow.png" class="back-arrow">
    </div>
    <div>
      <router-link 
        :to="{name: 'user', params: {id: userId}}"
      >
        <h1 class="header-name">{{userName}}</h1>
      </router-link>
      <p class="header-post-counts">{{userTweetsCount}}推文</p>
    </div>
  </div>
</template>


<script>
export default {
  props: {
    userId: {
      type: Number,
      required: true
    },
    userName: {
      type: String,
      required: true
    },
    userTweetsCount: {
      type: Number,
      required: true
    }
  }
}
</script>



<style scoped>
 .back-arrow {
    width: 20px;
    height: 20px;
    margin-right: 45px;
    cursor: pointer;
  }

  .header-name {
    margin-bottom: 0px;
    font-size: 19px;
    font-weight: 900;
    line-height: 28px;
    color: #1C1C1C;
  }

  .header-post-counts {
    font-size: 13px;
    font-weight: 500;
    line-height: 19px;
    color: #657786;
  }
</style>