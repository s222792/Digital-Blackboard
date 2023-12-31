<template>

  <CustomCard
      :item="item"
      :basicInfos="basicInfos"
      :extraInfos="extraInfos"
      class="text-left"
      action="Kontaktieren"
      @action-clicked="showDialogContactInfo=true"
      @editAdClicked="openDialogEditAd"
      @deleteAd="showDialogConfirm=true"
  ></CustomCard>

  <v-dialog
      v-model="showDialogContactInfo"
      transition="dialog-bottom-transition"
      max-width="1200px"
      class="justify-center"
  >
    <ContactCard
        :avatarSrc="item.images[0]"
        :name="item.name"
        :phone="item.phone"
        :email="item.email"
        @close-dialog="showDialogContactInfo=false"
    ></ContactCard>

  </v-dialog>

  <v-dialog
      v-model="showDialogEditAd"
      transition="dialog-bottom-transition"
      class="justify-center"
      :style="{ maxWidth: mobile ? '100%' : '60%' }"
  >
    <EditAppartmentDialog
        :item="item"
        @exit-dialog="exitDialogEditAd"
        @close-dialog="closeDialogEditAd"
    ></EditAppartmentDialog>

  </v-dialog>

  <v-dialog
      v-model="showDialogConfirm"
      transition="dialog-bottom-transition"
      class="justify-center"
      max-width="500px"
  >
    <ConfirmDialog
        :title="item.title"
        @confirmedDeletion="deleteAdClicked"
        @cancelDeletion="exitDialogConfirm"
    ></ConfirmDialog>

  </v-dialog>

  <v-snackbar v-model="snackbar" :timeout="timeout">
    {{ snackbarText }}

    <template v-slot:actions>

      <v-btn
          color="#eb1b2a"
          variant="text"
          float-right
          size="small"
          class="mr-1"
          @click="closeSnackbar"
      >
        Schließen
      </v-btn>

    </template>

  </v-snackbar>

</template>

<script setup>
import CustomCard from "@/components/util/CustomCard.vue"
import ContactCard from "@/components/util/ContactCard.vue";
import EditAppartmentDialog from "@/components/dualLiving/EditAppartmentDialog.vue";
import ConfirmDialog from "@/components/util/ConfirmDialog.vue";

import {useDisplay} from "vuetify";

const {mobile} = useDisplay()
</script>

<script>
import {deleteAd, editAdDualLiving} from "@/db.js"

export default {

  props: {
    item: Object,
  },

  data() {
    return {
      snackbarText: "",
      snackbar: false,
      timeout: 3000,

      showDialogConfirm: false,
      showDialogContactInfo: false,
      showDialogEditAd: false,

      basicInfosKeywords: [
        "availability", "area", "rooms", "price"
      ],

      extraInfosKeywords: [
        "description", "location", "furniture", "community"
      ],

      dictionary: {
        "availability": "Zeitraum",
        "area": "Wohnfläche in m²",
        "rooms": "Zimmer",
        "price": "monatliche Miete in €",
        "description": "Beschreibung",
        "location": "Ort / Stadtteil",
        "furniture": "möbliert",
        "community": "WG-Zimmer",
      },
    };
  },

  methods: {
    /**
     * Method to close the Snackbar.
     *
     * @method
     */
    closeSnackbar() {
      this.snackbar = false;
    },

    /**
     * Method to open the edit ad dialog for dual Housing.
     *
     * @method
     */
    openDialogEditAd() {
      this.showDialogEditAd = true;
    },

    /**
     * Asynchronously exits the edit ad dialog.
     *
     * @method
     */
    async exitDialogEditAd() {
      this.showDialogEditAd = false;
    },

    /**
     * Asynchronously closes the edit ad dialog, displays a success message in a Snackbar,
     * and emits an event to trigger the items to reload after change.
     *
     * @method
     */
    async closeDialogEditAd(formData, images, contactData) {
      this.showDialogEditAd = false;

      await editAdDualLiving(this.item.id, formData, images, contactData)

      this.snackbarText = "Ihr Inserat wurde erfolgreich geändert!";
      this.snackbar = true;

      this.$emit("itemsChanged")
    },

    /**
     * Asynchronously exits the confirm dialog.
     *
     * @method
     */
    async exitDialogConfirm() {
      this.showDialogConfirm = false;
    },

    async deleteAdClicked() {
      this.showDialogConfirm = false;
      await deleteAd("dual-living", this.item.id);

      this.snackbarText = "Ihr Inserat wurde erfolgreich gelöscht!";
      this.snackbar = true;

      this.$emit("itemsChanged")

    }
  },
  computed: {
    /**
     * Computed property that generates an array of basic information objects for display.
     *
     * @computed
     * @returns {Object[]} - An array of basic information objects with label and value properties.
     */
    basicInfos() {
      return this.basicInfosKeywords.map((attribute) => ({
        label: this.dictionary[attribute],
        value: this.item[attribute],
      }));
    },

    /**
     * Computed property that generates an array of extra information objects for display.
     *
     * @computed
     * @returns {Object[]} - An array of extra information objects with label and value properties.
     */
    extraInfos() {
      return this.extraInfosKeywords.map((attribute) => ({
        label: this.dictionary[attribute],
        value: this.item[attribute],
      }));
    },
  },
};
</script>

<style>

</style>
