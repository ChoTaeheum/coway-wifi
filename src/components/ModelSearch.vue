<template>
  <section class="container">
    <span class="title">모델 검색</span>
    <div class="display">
      <div class="searchBar">
        <input type="search" />
        <button type="button">검색(돋보기)</button>
      </div>

      <div class="menuCanvas">
        <ul>
          <li @click="isShow1 = !isShow1">정수기</li>
          <transition>
            <ul class="modelGroup" v-if="isShow1">
              <li @click="isShow11 = !isShow11">M1</li>
              <transition>
                <ul class="modelDetail" v-if="isShow11">
                  <li>M1-102</li>
                  <li>M1-103</li>
                  <li>M1-107</li>
                  <li>M1-306</li>
                  <li>M1-310</li>
                </ul>
              </transition>
              <li @click="isShow12 = !isShow12">M2</li>
              <transition>
                <ul class="modelDetail" v-if="isShow12">
                  <li>M2-102</li>
                  <li>M2-103</li>
                  <li>M2-107</li>
                  <li>M2-306</li>
                  <li>M2-310</li>
                </ul>
              </transition>
            </ul>
          </transition>
          <li>공기청정기</li>
          <li>비데</li>
        </ul>
      </div>

      
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      isShow1: true,
      isShow11: true,
      isShow12: true,
      productList: [],
    };
  },
  methods: {
    async getList() {
      this.productList = await this.$api(
        "https://f10eeb57-5665-43eb-a78a-b78f131c7c0d.mock.pstmn.io/EnterLeaveTransition",
        "get"
      );
    },
  },
};
</script>

<style scoped>
* {
  padding: 0;
  margin: 0;
}

section {
  width: 300px;
  height: 600px;
}

ul {
  cursor: pointer;
  padding: 2px;
  list-style-type: none;
}

.title {
  width: 30%;
}

.container {
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-content: flex-start;
}
.display {
  background-color: lightgray;
  width: 300px;
  height: 600px;
  padding: 5px;

  display: flex;
  flex-direction: column;
  align-items: center;
}

.searchBar {
  margin: 10px;
  width: 90%;
}
.menuCanvas {
  background-color: white;
  width: 90%;
  height: 90%;
  padding: 5px;
  /* padding: 25px; */

  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
}
</style>