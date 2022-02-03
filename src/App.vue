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
    <div class="uk-text-center uk-margin" v-on:click="addCard()">
      <button class="uk-button uk-button-primary">追加</button>
    </div>
    <ul
      class="uk-grid-small uk-child-width-1-2 uk-child-width-1-1@s"
      uk-sortable="handle: .uk-sortable-handle"
      uk-grid
    >
      <li v-for="card in cards" :key="card.Id">
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
    <div class="uk-text-center uk-margin" v-on:click="addCard()">
      <button class="uk-button uk-button-primary">追加</button>
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
      this.$data.cards.forEach((value) => {
        const reg = new RegExp(value.Before, "g");
        this.$data.after = this.$data.before.replace(reg, value.After);
      });
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
