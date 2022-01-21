<template>
  <section class="container">
    <div class="displayHeader">
      <span class="title">모델 검색</span>
      <div>
        <button type="button" @click="this.isExposed['group'] = true">
          그룹 생성
        </button>
        <button type="button" @click="deleteItem">삭제</button>
      </div>
    </div>
    <div class="displayBody">
      <div class="searchBar">
        <input
          type="search"
          name="q"
          placeholder="모델 검색"
          v-model="inputQuery"
          @keyup.enter="searchItem(inputQuery)"
        />
        <button type="button" for="inputQuery" @click="searchItem(inputQuery)">
          검색
        </button>
      </div>

      <div class="menuCanvas">
        <ul>
          <input
            type="text"
            v-if="isExposed['group'] == true"
            v-model="groupName"
            @keyup.enter="
              [addItem(groupName), (this.isExposed['group'] = false)]
            "
          />
          <li class="li1" :key="i" v-for="(familyInfo, i) in renderProductList">
            <span
              @click="
                [
                  scrollDown(familyInfo.family),
                  exclusiveSelected(familyInfo.family),
                ]
              "
              :class="{ colored: isColored[familyInfo.family] == true }"
            >
              {{ familyInfo.family }}
            </span>
            <transition>
              <ul v-if="isShown[familyInfo.family] == true">
                <li
                  class="li2"
                  :key="j"
                  v-for="(lineInfo, j) in familyInfo.sub"
                >
                  <span
                    @click="
                      [
                        scrollDown(lineInfo.line),
                        exclusiveSelected(lineInfo.line),
                      ]
                    "
                    :class="{ colored: isColored[lineInfo.line] == true }"
                  >
                    {{ lineInfo.line }}
                  </span>
                  <transition>
                    <ul v-if="isShown[lineInfo.line] == true">
                      <li
                        class="li3"
                        :key="k"
                        v-for="(modelName, k) in lineInfo.model"
                      >
                        <span
                          @click.stop="exclusiveSelected(modelName)"
                          :class="{
                            colored: isColored[modelName] == true,
                          }"
                        >
                          {{ modelName }}
                        </span>
                      </li>
                    </ul>
                  </transition>
                </li>
              </ul>
            </transition>
          </li>
        </ul>
      </div>
    </div>
  </section>
</template>

<script>
export default {
  data() {
    return {
      isShown: {},
      isColored: {},
      isExposed: {},
      productList: [],
      modelList: {},
      inputQuery: "",
      seletedItem: "",
    };
  },

  created() {
    this.getList();
  },

  computed: {
    renderProductList() {
      return this.productList;
    },

    renderModelList() {
      return this.modelList;
    },
  },

  methods: {
    async getList() {
      this.productList = await this.$api(
        // 나중에 비식별화
        "https://03f968e1-7e6f-40ce-8ff4-dbfebcf63498.mock.pstmn.io/list",
        "get"
      );

      this.modelList = await this.$api(
        "https://03f968e1-7e6f-40ce-8ff4-dbfebcf63498.mock.pstmn.io/search",
        "get"
      );
    },

    scrollDown(category) {
      this.isShown[category] = !this.isShown[category];
    },

    exclusiveSelected(modelName) {
      this.isColored = {};
      this.isColored[modelName] = true;
      this.seletedItem = Object.keys(this.isColored)[0];
    },

    searchItem(inputQuery) {
      this.isShown = {};
      this.isColored = {};

      this.isColored[inputQuery] = true;
      let child = inputQuery;
      while (!this.renderModelList["root"].includes(child)) {
        // 최상위에 다다르지 않는 동안 작동
        console.log(child, 1);
        child = this.searchParent(child);
        console.log(child, 2);
        this.isShown[child] = true;
      }
    },
    searchParent(child) {
      let graph = this.modelList;
      let visited = [];
      let needVisit = [];
      let parent = "";

      needVisit.push("root"); // root
      while (needVisit.length != 0) {
        let node = needVisit.shift();

        if (!visited.includes(node)) {
          // 방문한 적 없으면,
          visited.push(node); // 기록하고,

          for (const subNode of graph[node]) {
            // 자식 노드 순회하면서 찾는 단어가 있나 보고,
            if (subNode == child) {
              console.log("발견", subNode); // 찾은 노드 출력해주고
              parent = node; // 찾았다면 부모 지정하고
              return parent;
            }
          } // 부모 수준에서 순회를 하면서 탐색하기 때문에 자식없는 노드 도달이 불가능

          needVisit = [...needVisit, ...graph[node]];
        }
      }
    },

    addItem(item) {
      this.productList.push({ family: item });
      console.log(this.productList);

      this.modelList[item] = [];
      console.log(this.modelList);
    },

    deleteItem() {
      let productList = this.productList;
      productList.forEach((item, idx) => {
        console.log(item, idx);
        if (item.family == this.seletedItem) {
          this.productList.splice(idx, 1);
        }
      });

      delete this.modelList[this.seletedItem];
    },
  },
};
</script>

<style scoped>
.abc {
  display: none;
}

* {
  padding: 0;
  margin: 0;
}

section {
  width: 300px;
  height: 600px;
}

li {
  display: flex;
  justify-content: flex-start;
  align-items: flex-start;
  flex-direction: column;

  cursor: pointer;
  padding: 2px;
  list-style-type: none;
}

.li1 {
  margin-left: 10px;
  padding-left: 10px;
  padding-right: 10px;
}

.li2 {
  margin-left: 10px;
  padding-left: 10px;
  padding-right: 10px;
}

.li3 {
  margin-left: 10px;
  padding-left: 10px;
  padding-right: 10px;
}

.colored {
  background-color: green;
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

.displayHeader {
  margin: 5px;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.groupInput {
  display: none;
  width: 100px;
}

.displayBody {
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