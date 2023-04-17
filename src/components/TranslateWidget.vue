<template>
  <div class="translation-widget">
    <!-- <h1 v-t="'welcome'"></h1> -->
    <div class="container">
      <WidgetHeader></WidgetHeader>
      <div class="columns">
        <div class="column">
          <div class="tranlation-box">
            <div class="languages is-flex">
              <div
                class="language"
                v-for="(lang, i) in validLanguages"
                :key="i"
              >
                <button
                  class="button zis-warning mr-1 lang-btn"
                  :class="{
                    'is-primary': sourceLang.code === lang.code,
                    'is-warning': sourceLang.code !== lang.code,
                  }"
                  @click="setSouceLang(lang)"
                  v-t="lang.code"
                ></button>
              </div>
            </div>
            <div class="xbox mt-3">
              <div class="area is-relative">
                <textarea
                  v-model="source"
                  class="textarea"
                  :placeholder="$t('Type something to translate...')"
                ></textarea>

                <span
                  v-if="sourceLang.code === 'lad' && source"
                  @click="voice(source)"
                  class="voice material-icons"
                  :class="{
                    clickable: !playing,
                    'has-text-grey-darker': !playing,
                    'has-text-grey-light': playing,
                  }"
                  >record_voice_over</span
                >
              </div>
              <button
                class="button is-primary mt-3"
                @click="random"
                v-t="'Random'"
              ></button>
            </div>
          </div>
        </div>
        <div class="column is-1">
          <div class="actions">
            <button class="button is-primary" @click="translate">
              <span class="material-icons" v-if="!translating"
                >keyboard_arrow_right</span
              >
              <span class="material-icons spin" v-if="translating"
                >autorenew</span
              >
            </button>
          </div>
        </div>
        <div class="column">
          <div class="tranlation-box">
            <div class="languages is-flex">
              <div
                class="language"
                v-for="(lang, i) in validLanguages"
                :key="i"
              >
                <button
                  :disabled="!isTargetLangEnabled(lang.code)"
                  class="button zis-warning mr-1 lang-btn"
                  :class="{
                    'is-primary': targetLang.code === lang.code,
                    'is-warning': targetLang.code !== lang.code,
                  }"
                  @click="setTargetLang(lang)"
                  v-t="lang.code"
                ></button>
              </div>
            </div>
            <div class="xbox mt-3">
              <div class="area is-relative">
                <textarea
                  v-model="target"
                  class="textarea"
                  placeholder=""
                  :readonly="!contribute"
                ></textarea>

                <span
                  v-if="targetLang.code === 'lad' && target"
                  @click="voice(target)"
                  class="voice material-icons"
                  :class="{
                    clickable: !playing,
                    'has-text-grey-darker': !playing,
                    'has-text-grey-light': playing,
                  }"
                  >record_voice_over</span
                >
              </div>

              <div class="buttons is-flex">
                <button
                  class="button is-primary mt-3 mr-1"
                  @click="contribute = !contribute"
                  :disabled="!source"
                  v-if="!contribute"
                  v-t="'Contribute'"
                ></button>

                <button
                  class="button is-primary mt-3 mr-1"
                  @click="contribute = !contribute"
                  :disabled="!source"
                  v-if="contribute"
                  v-t="'Cancel'"
                ></button>

                <span
                  class="
                    material-icons
                    has-text-grey-darker
                    clickable
                    js-modal-trigger
                  "
                  data-target="modal-contribution"
                  >help</span
                >

                <button
                  class="button is-primary mt-3 push"
                  @click="submit"
                  v-if="contribute"
                  :disabled="!contribute || !target"
                  v-t="'Submit'"
                ></button>
              </div>
            </div>
          </div>
        </div>
      </div>
      <WidgetFooter></WidgetFooter>      
    </div>
    <div id="modal-contribution" class="modal">
      <div class="modal-background"></div>

      <div class="modal-content">
        <div class="box">
          <img :src="require(`@/assets/contributionhelp-${$i18n.locale}.png`)" class="help-image" />
        </div>
      </div>

      <button class="modal-close is-large" aria-label="close"></button>
    </div>
  </div>
</template>

<script>
import { toast } from "bulma-toast";
import WidgetHeader from "./WidgetHeader.vue";
import WidgetFooter from "./WidgetFooter.vue";

export default {
  name: "TranslateWidget",
  components: { WidgetHeader, WidgetFooter },
  props: {
    msg: String,
  },
  data() {
    return {
      languages: [],
      models: {},
      sourceLang: "",
      targetLang: "",
      source: "",
      target: "",
      apiBase: process.env.VUE_APP_API,
      token: process.env.VUE_APP_API_TOKEN,
      contribute: false,
      translating: false,
      playing: false,
    };
  },
  computed: {
    postData() {
      const source = this.source.split("\n");
      return {
        src: this.sourceLang.code,
        tgt: this.targetLang.code,
        batch: source,
        token: this.token,
      };
    },
    validLanguages() {
      // TEMPORARY SOLUTION
      // return this.languages.filter((l) => l.code != "xx");
      return this.languages.filter((l) => l.code == "zgh" || l.code == "ca" || l.code == "ary" || l.code == "es" || l.code == "fr" || l.code == "en" || l.code == "ber");
    },
  },
  mounted() {
    this.getList();
    this.prepareModal();
    //document.title = "Traductor amazic";
    document.title = "Tamazight Translate" //TITLE HERE
    // toast({
    //     message: this.$t("Thank you for your contribution"),
    //     type: "is-primary",
    //     duration: 300000,
    //     position: "top-center",
    //     closeOnClick: true,
    //     opacity: 0.7
    //   });
  },
  methods: {
    isTargetLangEnabled(langCode) {
      if (this.sourceLang.code !== "zgh" && this.sourceLang.code !== "ber" &&
          langCode !== "zgh" && langCode !== "ber") {
        return false;
      }
      return this.models[this.sourceLang.code]
        ? this.models[this.sourceLang.code][langCode] !== undefined
        : false;
    },
    setSouceLang(lang) {
      this.sourceLang = lang;
      if (lang.code === "zgh" || this.targetLang === "zgh") {
          this.targetLang = this.languages.find((l) => l.code !== "zgh");
      }
      if (lang.code === "ber" || this.targetLang === "ber") {
          this.targetLang = this.languages.find((l) => l.code !== "ber");
      }
      if (lang.code !== "zgh" && lang.code !== "ber") {
        if (this.targetLang.code !== "zgh" && this.targetLang.code !== "ber" )
        this.targetLang = this.languages.find((l) => l.code === "zgh" || l.code === "ber");
      }
    },
    setTargetLang(lang) {
      this.targetLang = lang;
      if (lang.code === "zgh" && this.sourceLang.code === "zgh") {
        this.sourceLang = this.languages.find((l) => l.code !== "zgh");
      }
      if (lang.code === "ber" && this.sourceLang.code === "ber") {
        this.sourceLang = this.languages.find((l) => l.code !== "ber");
      }
      if (lang.code !== "zgh" && lang.code !== "ber") {
        if (this.sourceLang.code !== "zgh" && this.sourceLang.code !== "ber")
          this.sourceLang = this.languages.find((l) => l.code === "zgh");
      }
    },
    getList() {
      // this.axios.get(api).then((response) => {
      //   console.log(response.data)
      // })
      // or
      this.axios.get(`${this.apiBase}/translate`).then((response) => {
        this.models = JSON.parse(JSON.stringify(response.data.models));

        this.languages.push({ code: "zgh", name: "Tamazight" });
        this.languages.push({ code: "ber", name: "Berber" });
        this.languages.push({ code: "en", name: "English" });
        this.languages.push({ code: "ca", name: "Catalan" });
        this.languages.push({ code: "es", name: "Spanish" });
        this.languages.push({ code: "fr", name: "French" });
        // this.languages.push({ code: "en", name: "English" });
        this.languages.push({ code: "ary", name: "Arabic" });

        // for (var i in response.data.languages) {
        //   this.languages.push({ code: i, name: response.data.languages[i] });
        // }

        //this.languages.push({ code: "zgh", name: "Tamazight" });
        // this.languages.push({ code: "fr", name: "French" });
        // this.languages.push({ code: "ca", name: "Catalan" });
        // this.languages.push({ code: "ary", name: "Arabic" });

        // this.languages = response.data.languages;
        this.sourceLang = this.languages.find((l) => l.code === "en"); //Startup source language
        this.targetLang = this.languages.find((l) => l.code === "zgh");
        // this.targetLang =
      });
    },
    async translate() {
      // const post = null;
      this.translating = true;
      const data = (
        await this.axios.post(`${this.apiBase}/translate`, this.postData)
      ).data;
      this.target = "";
      if (data && data.translation) {
        data.translation.forEach((t) => {
          this.target += t + "\n";
        });
      }
      this.translating = false;
    },
    async random() {
      const data = (
        await this.axios.get(
          `${this.apiBase}/translate/random/${this.sourceLang.code}`
        )
      ).data;
      if (data.sentence) {
        this.source = data.sentence;
      }
    },
    async submit() {
      const postData = {
        src: this.sourceLang.code,
        tgt: this.targetLang.code,
        src_text: this.source,
        tgt_text: this.target,
        token: this.token,
      };
      // const data = (
      await this.axios.post(`${this.apiBase}/translate/contribute`, postData);
      // ).data;
      // console.log(data.message);

      toast({
        message: this.$t("Thank you for your contribution"),
        type: "is-primary",
        duration: 3000,
        position: "top-center",
        closeOnClick: true,
        opacity: 0.7,
      });
      this.contribute = false;
    },
    async voice(text) {
      if (this.playing || !text) {
        return;
      }
      this.playing = true;
      const postData = {
        voice: "karen",
        text: text,
        token: this.token,
      };
      const { data } = await this.axios.post(`${this.apiBase}/tts`, postData, {
        responseType: "arraybuffer",
        headers: {
          "Content-Type": "application/json",
        },
      });
      const blob = new Blob([data], {
        type: "audio/wav",
      });
      var blobUrl = URL.createObjectURL(blob);
      var audio = new Audio(blobUrl);
      audio.play();
      this.playing = false;

      // var audio = new Audio(blob);
      // audio.play();
      // console.log(data);
      // curl -L -X GET 'http://api.collectivat.cat/tts' -H 'Content-Type: application/json' --data-raw '{"voice": "karen", "text":"hello 123 este maraviyoza diya", "token":"XoSn5gFLGXwJtTGb1sQ9VoQi4X_EHIhR5b_YghczGV0"}'
    },

    prepareModal() {
      document.addEventListener("DOMContentLoaded", () => {
        // Functions to open and close a modal
        function openModal($el) {
          $el.classList.add("is-active");
        }

        function closeModal($el) {
          $el.classList.remove("is-active");
        }

        function closeAllModals() {
          (document.querySelectorAll(".modal") || []).forEach(($modal) => {
            closeModal($modal);
          });
        }

        // Add a click event on buttons to open a specific modal
        (document.querySelectorAll(".js-modal-trigger") || []).forEach(
          ($trigger) => {
            const modal = $trigger.dataset.target;
            const $target = document.getElementById(modal);
            console.log($target);

            $trigger.addEventListener("click", () => {
              openModal($target);
            });
          }
        );

        // Add a click event on various child elements to close the parent modal
        (
          document.querySelectorAll(
            ".modal-background, .modal-close, .modal-card-head .delete, .modal-card-foot .button"
          ) || []
        ).forEach(($close) => {
          const $target = $close.closest(".modal");

          $close.addEventListener("click", () => {
            closeModal($target);
          });
        });

        // Add a keyboard event to close all modals
        document.addEventListener("keydown", (event) => {
          const e = event || window.event;

          if (e.keyCode === 27) {
            // Escape key
            closeAllModals();
          }
        });
      });
    },
  },
};
</script>
<style scoped>
</style>
