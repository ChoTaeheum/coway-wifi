<template>
  <section>
    <div class="displayHeader">
      <span>모델정보</span>
    </div>
    <div class="displayBody" v-for="(value, key) in infoList" :key="key">
      <form>
        <label for="infoValue">{{ key }}</label>
        <input type="text" :value="value" disabled="disabled" id="infoValue" />
      </form>
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      infoList: {},
    };
  },

  created() {
    this.getList();
  },

  methods: {
    async getList() {
      this.infoList = await this.$api(
        "https://03f968e1-7e6f-40ce-8ff4-dbfebcf63498.mock.pstmn.io/infoList",
        "get"
      );
    },
  },

  setup() {},
};
</script>

<style>
section {
  width: 300px;
  height: 600px;

  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-content: flex-start;
}

.displayHeader {
  margin: 5px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.displayBody {
  background-color: lightgray;
  width: 300px;
  padding: 5px;

  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

form {
  display: flex;
  flex-direction: row;
  align-items: center;
}

label {
  margin-right: 15px;
  margin-left: 5px;
  width: 70px;
  display: flex;
  justify-content: flex-start;
}

input {
  width: 70%;
}
</style>