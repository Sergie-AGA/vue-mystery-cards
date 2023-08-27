<template>
  <section class="container">
    <div :class="{ vanish: highlightCard }">
      <header class="header">
        <div class="header__left">
          <div
            class="header__status"
            :class="{
              'header__status--completed': cards
                .filter((obj) => !obj.hasOwnProperty('type'))
                .every((obj) => obj.active === true),
            }"
          >
            <div class="header__status-image-container">
              <img
                class="header__status-image"
                src="https://www.freeiconspng.com/thumbs/hd-tickets/download-ticket-ticket-free-entertainment-icon-orange-ticket-design-0.png"
                alt=""
              />
            </div>
            <div class="header__status-right">
              <span class="header__status-number"
                >{{
                  cards.filter(
                    (obj) => !obj.hasOwnProperty("type") && obj.active == true
                  ).length
                }}
                /
                {{
                  cards.filter((obj) => !obj.hasOwnProperty("type")).length
                }}</span
              >
              <span class="header__status-title">{{ settings.basicName }}</span>
            </div>
          </div>

          <div
            class="header__status"
            :class="{
              'header__status--completed': cards
                .filter((obj) => obj.type === 'silver')
                .every((obj) => obj.active === true),
            }"
          >
            <div class="header__status-image-container">
              <img
                class="header__status-image"
                src="https://cdn-icons-png.flaticon.com/512/2827/2827014.png"
                alt=""
              />
            </div>
            <div class="header__status-right">
              <span class="header__status-number"
                >{{
                  cards.filter(
                    (obj) => obj.type == "silver" && obj.active == true
                  ).length
                }}
                /
                {{ cards.filter((obj) => obj.type == "silver").length }}</span
              >
              <span class="header__status-title">{{
                settings.silverName
              }}</span>
            </div>
            <div v-if="silverLock" class="header__status-overlay">
              <ph-lock-key
                class="header__status-lock lock-icon--silver"
                :size="60"
              />
            </div>
          </div>

          <div
            class="header__status"
            :class="{
              'header__status--completed': cards
                .filter((obj) => obj.type === 'gold')
                .every((obj) => obj.active === true),
            }"
          >
            <div class="header__status-image-container">
              <img
                class="header__status-image"
                src="https://cdn-icons-png.flaticon.com/512/5448/5448224.png"
                alt=""
              />
            </div>
            <div class="header__status-right">
              <span class="header__status-number"
                >{{
                  cards.filter(
                    (obj) => obj.type == "gold" && obj.active == true
                  ).length
                }}
                /
                {{ cards.filter((obj) => obj.type == "gold").length }}</span
              >
              <span class="header__status-title">{{ settings.goldName }}</span>
            </div>
            <div v-if="goldLock" class="header__status-overlay">
              <ph-lock-key
                class="header__status-lock lock-icon--gold"
                :size="60"
              />
            </div>
          </div>
        </div>

        <div class="header__right">
          <div class="header__item">
            <ph-key
              class="header__item-icon header__item-icon--silver"
              :size="28"
            />
            <span class="header__item-number">{{ silverKeys }}</span>
          </div>
          <div class="header__item">
            <ph-key
              class="header__item-icon header__item-icon--gold"
              :size="28"
            />
            <span class="header__item-number">{{ goldKeys }}</span>
          </div>
        </div>
      </header>

      <div v-if="shuffledCards" class="cards">
        <article
          @click="flip(index)"
          v-for="(card, index) in shuffledCards"
          class="card"
          :key="index"
          :id="`card${index}`"
          :class="{ 'pre-selected': selected.includes(card.id) }"
        >
          <div class="card__side card--front">
            <ph-lock-key
              v-if="card.type"
              v-show="card.showLock"
              :class="`lock-icon--${card.type}`"
              class="lock-icon"
              :size="32"
            />
          </div>
          <div
            class="card__side card--back"
            :style="`background-image: url('${card.url}')`"
          >
            <h2 class="title">{{ card.name }}</h2>
          </div>
        </article>
      </div>
    </div>
    <OverlayAnimation
      v-if="highlightCard"
      :data="highlightCard"
      @dismiss="highlightCard = null"
    />
  </section>
</template>

<script>
import { PhLockKey, PhKey } from "phosphor-vue";
import cardData from "@/config/cardData.js";
import settings from "@/config/settings.js";
import OverlayAnimation from "./OverlayAnimation.vue";

export default {
  name: "HelloWorld",

  components: {
    PhLockKey,
    PhKey,
    OverlayAnimation,
  },

  data: () => ({
    cards: [],
    shuffledCards: null,
    selected: [],
    silverLock: true,
    goldLock: true,
    settings: null,
    silverKeys: 0,
    goldKeys: 0,
    keyPoints: 0,
    silverAudio: new Audio(require("@/assets/ffxii.mp3")),
    goldAudio: new Audio(require("@/assets/zelda.mp3")),
    highlightCard: null,
  }),

  created() {
    this.cards = cardData.gameCards;
    this.settings = settings;
    this.shuffledCards = this.cards.sort(() => Math.random() - 0.5);
  },

  methods: {
    flip(index) {
      if (this.cards[index].type == "silver") {
        this.silverLock = false;
        if (this.silverKeys) {
          this.cards[index].showLock = false;
          this.silverKeys--;
        } else {
          this.cards[index].showLock = true;
          this.$forceUpdate();
          return;
        }
      } else if (this.cards[index].type == "gold") {
        this.goldLock = false;
        if (this.goldKeys) {
          this.cards[index].showLock = false;
          this.goldKeys--;
        } else {
          this.cards[index].showLock = true;
          this.$forceUpdate();
          return;
        }
      }

      this.keyPoints++;

      if (this.settings.silverRequirement.includes(this.keyPoints)) {
        this.silverKeys++;
      }
      if (this.settings.goldRequirement.includes(this.keyPoints)) {
        this.goldKeys++;
      }

      this.cards[index].active = true;
      let card = document.getElementById(`card${index}`);

      if (this.cards[index].type == "silver") {
        this.silverAudio.play();
        setTimeout(() => {
          this.silverAudio.pause();
          this.silverAudio.currentTime = 0;
        }, 11550);
        card.classList.add("flipped--silver");
      } else if (this.cards[index].type == "gold") {
        this.goldAudio.play();
        this.startGoldenAnimation(card, index);
      } else {
        card.classList.add("flipped");
      }

      this.$forceUpdate();
    },

    startGoldenAnimation(card, index) {
      let vm = this;
      if (!this.goldAudio.paused) {
        card.classList.add("flipped--gold");
        this.highlightCard = this.cards[index];
      } else {
        setTimeout(vm.startAnimation, 200);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.container {
  padding: 20px 40px;
}

.header {
  margin-bottom: 40px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  &__left {
    display: flex;
    align-items: center;
  }
  &__status {
    padding: 4px 10px;
    margin-right: 10px;
    background: #15202b;
    border-radius: 8px;
    color: white;
    width: 150px;
    display: flex;
    align-items: center;
    text-align: center;
    overflow: hidden;
    position: relative;
    &--completed {
      background-color: goldenrod;
    }
  }
  &__status-right {
    margin-bottom: 5px;
    display: flex;
    flex-direction: column;
    align-items: center;
    flex: 0 0 50%;
  }
  &__image-container {
    flex: 0 0 50%;
  }
  &__status-image {
    width: 100%;
    height: auto;
    margin-right: 10px;
  }
  &__status-title {
    font-size: 12px;
    text-transform: uppercase;
    text-align: center;
  }
  &__status-number {
    font-size: 30px;
    text-align: center;
    font-weight: bold;
    margin-bottom: 5px;
  }
  &__status-overlay {
    left: 0;
    position: absolute;
    height: 100%;
    width: 100%;
    background: #15202b;
    display: flex;
    align-items: center;
    justify-content: center;
  }

  &__status-lock {
  }

  &__right {
    display: flex;
    flex-direction: column;
  }

  &__item {
    padding: 2px 6px;
    background: #15202b;
    border-radius: 8px;
    color: white;
    display: flex;
    align-items: center;

    &:first-child {
      margin-bottom: 5px;
    }
  }
  &__item-icon {
    margin-right: 5px;
    &--silver {
      color: #bec2cb;
    }
    &--gold {
      color: goldenrod;
    }
  }
  &__item-number {
    font-size: 24px;
  }
}

.cards {
  display: flex;
  justify-content: space-between;
  gap: 20px 10px;
  flex-wrap: wrap;
}

.card {
  height: 234px;
  width: 180px;
  position: relative;
  border-radius: 8px;
  overflow: hidden;

  &__side {
    -webkit-backface-visibility: hidden;
    backface-visibility: hidden;
    width: 100%;
    height: 100%;
    transition: 0.5s;
    background: #15202b;
    background-position: center;
    background-repeat: no-repeat;
    background-size: contain;
  }

  &.flipped {
    .card {
      &--front {
        transform: rotateY(180deg);
      }
      &--back {
        transform: rotateY(0);
      }
    }
  }

  &.flipped--silver {
    transition: 2s;
    transition-delay: 4.4s;
    box-shadow: 0 0 10px 10px silver;
    .card {
      &--front {
        animation: silver-flip-font 4s ease-in-out 0.4s forwards;
      }
      &--back {
        animation: silver-flip-back 4s ease-in-out 0.4s forwards;
      }
    }
  }

  @keyframes silver-flip-font {
    0% {
      transform: rotateY(0);
    }
    50% {
      background-color: silver;
      transform: rotateY(0);
    }
    100% {
      background-color: silver;
      transform: rotateY(180deg);
    }
  }
  @keyframes silver-flip-back {
    0% {
      transform: rotateY(180deg);
    }
    50% {
      transform: rotateY(180deg);
      background-color: silver;
    }
    100% {
      background-color: silver;
      transform: rotateY(0);
    }
  }

  &.flipped--gold {
    transition: 2s;
    transition-delay: 20s;
    position: relative;
    box-shadow: 0 0 10px 10px goldenrod;
    animation: gold-move 10s ease-in-out 2.4s forwards;
    .card {
      &--front {
        animation: gold-flip-font 20s ease-in-out 0.4s forwards;
      }
      &--back {
        animation: gold-flip-back 20s ease-in-out 0.4s forwards;
      }
    }
  }

  @keyframes gold-flip-font {
    0% {
      transform: rotateY(0);
    }
    10% {
      background-color: goldenrod;
      transform: rotateY(0);
    }
    90% {
      transform: rotateY(0);
    }
    100% {
      background-color: goldenrod;
      transform: rotateY(180deg);
    }
  }
  @keyframes gold-flip-back {
    0% {
      transform: rotateY(180deg);
    }
    10% {
      transform: rotateY(180deg);
      background-color: goldenrod;
    }
    90% {
      transform: rotateY(180deg);
    }
    100% {
      background-color: goldenrod;
      transform: rotateY(0);
    }
  }

  @keyframes gold-move {
    0% {
      bottom: 0;
    }
    20% {
      bottom: 100vh;
    }
    90% {
      bottom: 100vh;
    }
    100% {
      bottom: 0;
    }
  }

  &--front {
    position: absolute;
    top: 0;
    left: 0;
    background-image: url("../assets/2754.png");
    cursor: pointer;
  }

  &--back {
    position: absolute;
    transform: rotateY(180deg);
  }

  .title {
    background: rgba($color: #000000, $alpha: 0.7);
    position: absolute;
    bottom: 0;
    left: 0;
    width: 100%;
    padding: 10px;
    color: white;
    font-size: 18px;
    text-align: center;
  }
}

.lock-icon {
  position: absolute;
  top: 14px;
  right: 14px;
  &--silver {
    color: #bec2cb;
  }
  &--gold {
    color: goldenrod;
  }
}

.vanish {
  transition: 3s;
  transition-delay: 3s;
  opacity: 0;
  pointer-events: none;
}

.pre-selected:not(.flipped):not(.flipped--silver):not(.flipped--gold) {
  .card--front {
    background-color: teal;
  }
}
</style>
