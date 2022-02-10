<template>
  <!-- <h1>hello world</h1> -->
  <MainScreen
    v-if="statusMatch === 'default'"
    @onStart="onHandleBeforeStart($event)"
  />
  <!-- cardsContext là props truyền xuống cho thằng interaceScreen hiển thị -->
  <InteractScreen
    v-if="statusMatch === 'match'"
    :cardsContext="settings.cardsContext"
    @onFinish="onGetResult"
  />
  <ResultScreen
    v-if="statusMatch === 'result'"
    :timer="timer"
    @onStartAgain="statusMatch = 'default'"
  />
</template>

<script>
import MainScreen from "./components/MainScreen.vue";
import InteractScreen from "./components/InteractScreen.vue";
import ResultScreen from "./components/ResultScreen.vue";
import { shuffled } from "./utils/array";
export default {
  name: "App",
  components: {
    MainScreen,
    InteractScreen,
    ResultScreen,
  },
  data() {
    return {
      settings: {
        totalOfBlocks: 0,
        cardsContext: [],
        startedAt: null,
      },
      statusMatch: "default",
      timer: 0,
    };
  },
  methods: {
    onHandleBeforeStart(config) {
      //config chính là tổng số card được truyền vào
      this.settings.totalOfBlocks = config.totalOfBlocks;
      // tạo ra một mảng gồm tổng số block / 2 và tăng dần thêm 1
      const firstCards = Array.from(
        { length: this.settings.totalOfBlocks / 2 },
        (_, i) => i + 1
      );
      const secondCards = [...firstCards];
      const cards = [...firstCards, ...secondCards];
      //Trộn mảng
      this.settings.cardsContext = shuffled(shuffled(shuffled(cards)));
      console.log(" this.settings.cardsContext", this.settings.cardsContext);
      this.statusMatch = "match";
      //lấy ra thời gian bắt đầu
      this.settings.startedAt = new Date().getTime();
      console.log("this.settings.startedAt ", this.settings.startedAt);
    },
    onGetResult() {
      this.timer = new Date().getTime() - this.settings.startedAt;
      this.statusMatch = "result";
    },
  },
};
</script>

<style></style>
