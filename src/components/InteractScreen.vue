<template>
  <div class="screen">
    <h1>Interact Screen here</h1>
    <div
      class="screen__inner"
      :style="{
        width: `${
          ((((920 - 16 * 4) / Math.sqrt(cardsContext.length) - 16) * 3) / 4 +
            16) *
          Math.sqrt(cardsContext.length)
        }px`,
      }"
    >
      <!-- v for để render ra tất cả thằng card  -->
      <CardFlip
        v-for="(card, index) in cardsContext"
        v-bind:key="index"
        :imgBackFaceUrl="`image/${card}.png`"
        :card="{ index: index, value: card }"
        :ref="`card-${index}`"
        :rules="rules"
        :cardsContext="cardsContext"
        @onFlip="checkRule($event)"
      />
    </div>
  </div>
</template>
<script>
import CardFlip from "./Card.vue";
export default {
  props: {
    cardsContext: {
      type: Array,
      default: function () {
        return [];
      },
    },
  },
  components: {
    CardFlip,
  },
  data() {
    return {
      rules: [],
    };
  },
  methods: {
    checkRule(card) {
      console.log("check card", card);
      if (this.rules.length === 2) return false;
      this.rules.push(card);
      if (
        this.rules.length === 2 &&
        this.rules[0].value === this.rules[1].value
      ) {
        // thêm class disabale
        this.$refs[`card-${this.rules[0].index}`][0].onEnabledDisabledMode();
        this.$refs[`card-${this.rules[1].index}`][0].onEnabledDisabledMode();
        // reset rules []
        this.rules = [];
        const disabledElements = document.querySelectorAll(
          ".screen .card.disabled"
        );
        if (
          disabledElements &&
          disabledElements.length === this.cardsContext.length - 2
        ) {
          setTimeout(() => {
            this.$emit("onFinish");
          }, 920);
        }
      } else if (
        this.rules.length === 2 &&
        this.rules[0].value !== this.rules[1].value
      ) {
        // đóng cả 2 mảng và reset cái mảng rules về []
        setTimeout(() => {
          this.$refs[`card-${this.rules[0].index}`][0].onFlipBackCard();
          this.$refs[`card-${this.rules[1].index}`][0].onFlipBackCard();
          this.rules = [];
        }, 920);
      } else {
        return false;
      }
    },
  },
};
</script>
<style>
.screen {
  width: 100%;
  height: 100vh;
  position: absolute;
  top: 0;
  left: 0;
  z-index: 2;
  background: var(--dark);
  color: var(--light);
}
.screen__inner {
  width: 424px;
  display: flex;
  flex-wrap: wrap;
  margin: 2rem;
}
</style>
