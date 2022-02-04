<template>
  <section>
    <div class="displayHeader">
      <span class="title">검사 항목 목록</span>
      <div class="control">
        <button
          id="uploadBtn"
          onclick="document.getElementById('getFile').click()"
        >
          업로드
        </button>
        <input id="getFile" type="file" />
        <button @click="deleteItem(checkedList)">삭제</button>
      </div>
    </div>
    <div class="displayBody">
      <table>
        <thead>
          <tr>
            <th>
              <input
                class="form-check-input"
                type="checkbox"
                v-model="checked"
              />
            </th>
            <th>모델명</th>
            <th>검사 항목</th>
            <th>생성일</th>
            <th>상세</th>
          </tr>
        </thead>
        <tfoot>
          <tr>
            <th></th>
            <td colspan="5">&nbsp;</td>
          </tr>
        </tfoot>
        <tbody>
          <tr v-for="(item, idx) of inspectionList" :key="idx">
            <td>
              <input
                class="form-check-input"
                type="checkbox"
                :value="idx"
                v-model="checkedList"
              />
            </td>
            <td v-for="(value, key) in item" :key="key">
              <div :class="key === '검사 항목' ? 'oval' : ''">
                {{ value }}
              </div>
            </td>
            <td>
              <button>돋보기</button>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
  </section>
</template>

<script>
import axios from "axios";
import { ref, watch } from "vue";

export default {
  setup() {
    let inspectionList = ref([]);
    let checkedList = ref([]);
    let checked = ref(false);

    async function $api(url, method, data) {
      return (
        await axios({ method: method, url, data }).catch((e) => {
          console.log(e);
        })
      ).data;
    }

    $api(
      "https://03f968e1-7e6f-40ce-8ff4-dbfebcf63498.mock.pstmn.io/inspectionList",
      "get"
    ).then((v) => {
      inspectionList.value = v;
      console.log(inspectionList.value);
    });

    function deleteItem(checkeditems) {
      checkeditems.reverse();

      for (let item of checkeditems) {
        inspectionList.value.splice(item, 1);
      }

      checkedList.value = [];
      checked.value = false;
    }

    watch(checked, () => {
      if (checked.value === true) {
        checkedList.value = [...Array(inspectionList.value.length).keys()];
      } else {
        checkedList.value = [];
      }
    });

    return {
      deleteItem,
      inspectionList,
      checkedList,
      checked,
    };
  },
};
</script>

<style>
* {
  padding: 0px;
  margin: 0px;
}

section {
  width: 500px;
  height: 850px;

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
  align-items: center;
}

.title {
  width: 25%;
}

.control {
  width: 20%;
}

#uploadBtn {
  margin-right: 3px;
}

#getFile {
  display: none;
}

.displayBody {
  width: 100%;
  padding: 5px;

  display: flex;
  flex-direction: column;
  align-items: flex-start;
}

table {
  width: 100%;
  margin-top: 0px;

  border-top: 1px solid #444444;
  border-collapse: collapse;
}

thead,
tfoot {
  background-color: lightgray;
}

th,
td {
  border-bottom: 1px solid #444444;
  padding: 5px;
}

.oval {
  width: 75%;
  /* height: 100%; */
  background: #444444;
  border-radius: 30px;
  margin: auto;
  color: beige;
  font-size: 90%;
  text-align: center;
  /* line-height: 100%; */
}
</style>