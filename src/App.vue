<template>
  <div class="uk-margin">
    変換前
    <textarea
      class="uk-textarea"
      rows="5"
      placeholder="before"
      v-model="before"
    ></textarea>
  </div>
  <div class="uk-text-center uk-margin" v-on:click="trans()">
    <button class="uk-button uk-button-secondary">変換</button>
  </div>
  <div class="uk-margin">
    変換後
    <textarea
      class="uk-textarea"
      rows="5"
      placeholder="after"
      v-text="after"
    ></textarea>
  </div>
  <div>
    左上のアイコンをドラッグで並び替える。変換は上から順番に実行される。
    <div class="uk-text-center uk-margin">
      <button class="uk-button uk-button-primary" v-on:click="addCard()">
        追加
      </button>
      <button
        class="uk-button uk-button-default uk-margin-left"
        uk-toggle="target: #hukkatsu-modal"
        v-on:click="this.$data.jumon = JSON.stringify(this.$data.cards)"
      >
        復活の呪文
      </button>
    </div>
    <ul
      class="uk-grid-small uk-child-width-1-2 uk-child-width-1-1@s"
      uk-sortable="handle: .uk-sortable-handle"
      uk-grid
    >
      <li v-for="card in cards" :key="card.Id" :id="card.Id">
        <div class="uk-card uk-card-default uk-card-body uk-card-small">
          <div>
            <p class="uk-sortable-handle" uk-icon="icon: table; ratio: 1.5"></p>
            <p
              class="uk-margin-left"
              uk-icon="icon: trash; ratio: 1.5"
              v-on:click="deleteCard(card.Id)"
            ></p>
          </div>
          <input
            class="uk-margin-small-top uk-input"
            type="text"
            placeholder="検索対象の正規表現"
            v-model="card.Before"
          />
          <input
            class="uk-margin-small-top uk-input"
            type="text"
            placeholder="変換後文字列"
            v-model="card.After"
          />
        </div>
      </li>
    </ul>
    <div class="uk-text-center uk-margin">
      <button class="uk-button uk-button-primary" v-on:click="addCard()">
        追加
      </button>
    </div>
  </div>
  <div id="hukkatsu-modal" uk-modal>
    <div class="uk-modal-dialog uk-modal-body">
      <h2 class="uk-modal-title">Headline</h2>
      <textarea
        class="uk-textarea"
        rows="5"
        placeholder="復活の呪文"
        v-model="jumon"
      ></textarea>
      <p class="uk-text-right">
        <button
          class="uk-button uk-button-default uk-modal-close"
          type="button"
        >
          閉じる
        </button>
        <button
          class="uk-button uk-button-primary uk-margin-left"
          type="button"
          v-on:click="chant()"
        >
          唱える
        </button>
      </p>
    </div>
  </div>
</template>

<script lang="ts">
import { defineComponent } from "vue";

interface RegexCard {
  Before: string;
  After: string;
  Id: string;
}

function getUniqueStr(): string {
  var strong = 1000;
  return (
    new Date().getTime().toString(16) +
    Math.floor(strong * Math.random()).toString(16)
  );
}

export default defineComponent({
  name: "App",
  components: {},
  data: () => {
    return {
      cards: [] as RegexCard[],
      before: "",
      after: "",
      jumon: "",
    };
  },
  methods: {
    addCard: function () {
      this.$data.cards.push({
        Before: "",
        After: "",
        Id: getUniqueStr(),
      });
    },
    deleteCard: function (id: string) {
      const idx = this.$data.cards.findIndex((value) => {
        return value.Id === id;
      });
      this.$data.cards.splice(idx, 1);
    },
    trans: function () {
      let tmp = this.$data.cards;
      tmp = tmp.sort((a, b) => {
        const rect1 = document.getElementById(a.Id)?.getBoundingClientRect();
        const rect2 = document.getElementById(b.Id)?.getBoundingClientRect();
        if (rect1 === undefined || rect2 === undefined) {
          return 1;
        }
        return rect1.top > rect2.top ? 1 : -1;
      });
      this.$data.after = this.$data.before;
      tmp.forEach((value) => {
        const reg = new RegExp(value.Before, "g");
        this.$data.after = this.$data.after.replace(reg, value.After);
      });
    },
    chant: function () {
      this.$data.cards = JSON.parse(this.$data.jumon) as RegexCard[];
    },
  },
});
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  color: #2c3e50;
  margin: 20px;
}
</style>
