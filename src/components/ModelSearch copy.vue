<template>
  <section class="container">
    <span class="title">모델 검색</span>
    <div class="display">
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
          <li :key="i" v-for="(familyInfo, i) in productList">
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
              <ul v-if="isShow[familyInfo.family] == true">
                <li :key="j" v-for="(lineInfo, j) in familyInfo.sub">
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
                    <ul v-if="isShow[lineInfo.line] == true">
                      <li :key="k" v-for="(modelName, k) in lineInfo.model">
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
      // 나중에 동적으로 생성
      isShow: {},
      isColored: {},
      productList: [],
      inputQuery: "",
      active: 0,
      modelList: {
        root: ["정수기", "공기청정기", "비데"],
        정수기: ["m1", "m2", "m3"],
        공기청정기: ["g7", "g9"],
        비데: ["b22"],
        m1: ["m1-102", "m1-103", "m1-123"],
        m2: ["m2-105", "m2-125", "m2-823", "m2-323"],
        m3: ["m3-593", "m3-231"],
        g7: ["g7-182", "g7-003", "g7-292"],
        g9: ["g9-105"],
        b22: ["b22-1112", "b22-z304", "b22-ke300"],
      },
    };
  },

  created() {
    this.getList();
  },

  methods: {
    async getList() {
      this.productList = await this.$api(
        "https://f10eeb57-5665-43eb-a78a-b78f131c7c0d.mock.pstmn.io/EnterLeaveTransition",
        "get"
      );
    },

    scrollDown(category) {
      this.isShow[category] = !this.isShow[category];
    },

    exclusiveSelected(modelName) {
      this.isColored = { m2: false };
      this.isColored[modelName] = true;
      console.log(this.isColored);
    },

    searchItem(inputQuery) {
      this.isShow = {};
      this.isColored = {};

      this.isColored[inputQuery] = true;
      let child = inputQuery;
      while (!this.modelList["root"].includes(child)) {
        // 최상위에 다다르지 않는 동안 작동
        console.log(child, 1);
        child = this.searchParent(child);
        console.log(child, 2);
        this.isShow[child] = true;
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
          // console.log(needVisit, "needVisit");
        }
      }
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

ul {
  cursor: pointer;
  padding: 2px;
  list-style-type: none;
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