<template>
  <CustomCard
      :item="item"
      :basicInfos="basicInfos"
      :extraInfos="extraInfos"
  ></CustomCard>
</template>

<script setup>
import CustomCard from "@/components/util/CustomCard.vue"
</script>

<script>
export default {
  props: {
    item: Object,
  },
  data() {
    return {
      basicInfosKeywords: [
        "price", "description"
      ],
      extraInfosKeywords: [
        "location", "furniture", "community", "availability"
      ],
      dictionary: {
        "availability": "Maximale Anzahl Teilnehmer",
        "price" : "Monatliche Miete",
        "description": "Beschreibung",
        "location": "Wo?",
        "community": "Wer?",
      }
    };
  },
  computed: {
    basicInfos() {
      let basicInfos = [];
      for (const attribute of this.basicInfosKeywords) {
        let value = this.item[attribute]
        basicInfos.push({label: this.dictionary[attribute], value: value});
      }

      return basicInfos;
    },
    extraInfos() {
      let extraInfos = [];
      for (const attribute of this.extraInfosKeywords) {
        let value = this.item[attribute]
        extraInfos.push({label: this.dictionary[attribute], value: value});
      }

      return extraInfos;
    }
  }
};
</script>

<style>

</style>