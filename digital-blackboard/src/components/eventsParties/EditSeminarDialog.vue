<template>
  <v-stepper
      v-model="step"
      alt-labels
  >
    <v-stepper-header class="fixed-header">
      <v-stepper-item
          :title="mobile ? '' : 'Angaben zur Seminar'"
          :icon="mobile ? 'mdi-text-box-outline' : ''"
          :value="1"
      ></v-stepper-item>

      <v-divider></v-divider>

      <v-stepper-item
          :title="mobile ? '' : 'Fotos'"
          :subtitle="mobile ? '' : 'Optional'"
          :icon="mobile ? 'mdi-image-outline' : ''"
          :value="2"
      ></v-stepper-item>

      <v-divider></v-divider>

      <v-stepper-item
          :title="mobile ? '' : 'Zusammenfassung'"
          :icon="mobile ? 'mdi-checkbox-marked-circle-outline' : ''"
          :value="3"
      ></v-stepper-item>
    </v-stepper-header>

    <v-stepper-window>
      <v-card-title
          class="text-h6 font-weight-regular justify-space-between pa-2"
      >
        <span>
          {{ currentTitle }}
        </span>
      </v-card-title>

      <v-window
          v-model="step"
      >
        <v-window-item
            :value="1"
        >
          <v-form
              @submit.prevent>
            <!-- Form für den Input des Users -> v-model, und Eingabehinweise -> prefix, rules, placeholder -->
            <v-card-text>
              <v-text-field
                  label="Titel des Seminars *"
                  variant="outlined"
                  class="mt-2"
                  maxlength="50"
                  v-model="seminarData.title"
                  :rules="titleRules"
                  counter
                  required
              ></v-text-field>

              <v-text-field
                  label="Beschreibung"
                  variant="outlined"
                  maxlength="200"
                  v-model="seminarData.description"
                  counter
              ></v-text-field>

              <v-text-field
                  label="Datum *"
                  placeholder="TT.MM.JJJJ"
                  variant="outlined"
                  type="date"
                  :rules="dateRules"
                  v-model="seminarData.date"
              ></v-text-field>

              <v-text-field
                  label="Ort *"
                  placeholder="Coblitzallee 1-9, 68163 Mannheim"
                  variant="outlined"
                  v-model="seminarData.location"
                  :rules="generalRules"
              ></v-text-field>

              <v-text-field
                  label="Preis p.p. *"
                  variant="outlined"
                  prefix="€"
                  :rules="numRules"
                  v-model="seminarData.price"
              ></v-text-field>

              <v-text-field
                  label="Zielgruppe *"
                  variant="outlined"
                  v-model="seminarData.community"
                  :rules="generalRules"
              ></v-text-field>

              <v-text-field
                  label="maximale Teilnehmeranzahl"
                  variant="outlined"
                  v-model="seminarData.maxParticipantsLimit"
              ></v-text-field>
            </v-card-text>
            <v-card-actions>
              <v-btn
                  color="#eb1b2a"
                  class="mr-2 mb-2"
                  variant="outlined"
                  @click="exitDialog()"
              >
                Schließen
              </v-btn>
              <v-spacer></v-spacer>
              <v-btn
                  color="#eb1b2a"
                  class="mr-2 mb-2"
                  type="submit"
                  variant="outlined"
                  @click="validateDataForm()"
              >
                Nächste
              </v-btn>
            </v-card-actions>
          </v-form>
        </v-window-item>

        <v-window-item
            :value="2"
        >
          <UploadImagesStep
              ref="uploadImagesForm"
              :preloadImages="item.images"
          ></UploadImagesStep>
          <v-card-actions>
            <v-btn
                variant="outlined"
                @click="step--"
            >
              Zurück
            </v-btn>

            <v-spacer></v-spacer>
            <v-btn
                color="#eb1b2a"
                class="float right"
                type="submit"
                variant="outlined"
                @click="step++"
            >
              Nächste
            </v-btn>
          </v-card-actions>
        </v-window-item>

        <v-window-item :value="3">
          <div
              class="pa-4 text-center"
          >
            <v-img
                class="mb-4"
                contain
                height="128"
                src="https://firebasestorage.googleapis.com/v0/b/digital-blackboard-dhbw.appspot.com/o/dhbw-logo-small.jpg?alt=media"
            ></v-img>
            <h3 class="text-h6 font-weight-light mb-2">
              Noch ein letzter Check das alles passt!
            </h3>
            <span
                class="text-caption text-grey"
            >Danke, dass Sie das "Digital Blackboard" nutzen!</span>
          </div>

          <v-card
              class="ma-1"
              variant="outlined"
          >
            <v-card-title>Angaben zum Seminar</v-card-title>

            <v-card-text>
              <v-row
                  v-for="item in eventInfos"
                  :key="item.label"
                  no-gutters
                  class="mt-1"
              >
                <v-col>
                  <p>
                    {{ item.label }}
                  </p>
                </v-col>

                <v-col>
                  <p>
                    {{ item.value }}
                  </p>

                </v-col>
              </v-row>
            </v-card-text>
            <v-card-actions>
              <v-btn
                  variant="outlined"
                  @click="step--"
              >
                Zurück
              </v-btn>

              <v-spacer></v-spacer>
              <!--Nur sichtbar solange man sich auf der letzten Seite befindet, übergibt die Inputdaten -->
              <v-btn
                  color="#eb1b2a"
                  class="float right"
                  variant="outlined"
                  type="submit"
                  @click="closeDialog"
              >
                Seminar teilen
              </v-btn>
            </v-card-actions>
          </v-card>
        </v-window-item>
      </v-window>
    </v-stepper-window>
  </v-stepper>
</template>

<script setup>
import {useDisplay} from "vuetify";

const {mobile} = useDisplay()
</script>

<script>
import UploadImagesStep from "@/components/util/UploadImagesStep.vue";

export default {
  props: {
    item: Object,
  },

  components: {
    UploadImagesStep
  },

  data: () => ({
    step: 1,
    selectedImages: [],

    infosEvent: [
      "title", "description", "date", "location", "price", "community"
    ],

    // regel zum erzwingen von gewünschten Werten
    titleRules: [
      (value) => value ? true : 'Bitte gebe einen Titel für dein Inserat an!',
      (value) => value.length >= 3 ? true : 'Der Name muss mindestens 3 Zeichen lang sein!',
    ],

    generalRules: [
      (value) => value ? true : 'Bitte gebe weitere Informationen an!'
    ],

    dateRules: [
      (value) => value ? true : 'Bitte wähle ein Datum aus!'
    ],

    numRules: [
      (value) => value ? true : 'Bitte gebe weitere Informationen an!',
      (value) => /\d+$/.test(value) ? true : 'Die angegebene Information darf nur Zahlen (0-9) beinhalten!',
    ],

    seminarData: {
      title: '',
      description: '',
      date: '',
      location: '',
      price: '',
      community: '',
      maxParticipantsLimit: '',
      category: 'Seminare',
      userId: 1,
    },

    dictionary: {
      "title": "Titel:",
      "description": "Beschreibung:",
      "date": "Wann:",
      "location": "Wo:",
      "price": "Preis in €:",
      "maxParticipantsLimit": "Anzahl Teilnehmer:",
      "community": "Zielgruppe:",
    },
  }),

  methods: {

    validateDataForm() {
      // kritische Eventdaten werden durch rules validiert, wenn alle felder richtig ausgefüllt werden kann die nächste seite erreich werden
      const isValid = this.validateFields([
        {value: this.seminarData.title, rules: this.titleRules},
        {value: this.seminarData.location, rules: this.generalRules},
        {value: this.seminarData.price, rules: this.numRules},
        {value: this.seminarData.community, rules: this.generalRules},
      ]);
      if (isValid) {
        return this.step++
      }
    },

    validateFields(fields) {
      // Überprüfe jede Regel für jedes Feld
      for (const field of fields) {
        for (const rule of field.rules) {
          const isValid = rule(field.value);
          if (isValid !== true) {
            // Wenn die Regel nicht erfüllt ist, zeige die Fehlermeldung an
            console.error(isValid);
            return false;
          }
        }
      }
      return true; // Alle Regeln wurden erfüllt => nächste Seite
    },

    onFileChange() {
      this.selectedImages = this.selectedImages.map((file) => ({
        file,
        url: URL.createObjectURL(file),
      }));
    },

    deleteImage(index) {
      this.selectedImages.splice(index, 1);
    },

    uploadImages() {
      this.selectedImages.forEach((image) => {
        if (image.file instanceof Blob) {
          console.log('Uploading:', image.url);
        }
      });
    },

    exitDialog() {
      // der das Dialogfenster wird geschlossen, das close-Dialog Event des Parent wird ausgeführt, Nutzerdaten/ -bilder werden übergeben
      this.$emit("exit-dialog")
    },

    closeDialog() {
      // der das Dialogfenster wird geschlossen, das close-Dialog Event des Parent wird ausgeführt, Nutzerdaten/ -bilder werden übergeben
      this.$emit("close-dialog", this.$refs.uploadImagesForm.imagePreviews, this.seminarData)
    }
  },

  computed: {
    currentTitle() {
      // einzelnen Schritte des Steppers
      switch (this.step) {
        case 1:
          return 'Angaben zum Seminar';
        case 2:
          return 'Fotos';
        case 3:
          return 'Zusammenfassung';
        default:
          return 'Seminar wurde erfolgreich geteilt!';
      }
    },

    eventInfos() {
      // iteriert über alle seminarData Attribute und deren titel aus dictionary um diese als Preview anzuzeigen
      let eventInfos = [];
      for (const attribute of this.infosEvent) {
        let value = this.seminarData[attribute];
        eventInfos.push({label: this.dictionary[attribute], value: value});
      }
      return eventInfos;
    },
  },

  mounted() {
    let partsFrom = this.item.date.split(".")

    this.seminarData = {
      title: this.item.title,
      description: this.item.description,
      date: partsFrom[2] + "-" + partsFrom[1] + "-" + partsFrom[0],
      location: this.item.location,
      price: this.item.price,
      community: this.item.community,
      maxParticipantsLimit: this.item.max_participants_limit,
      images: this.item.images,
      date_created: this.item.date_created,
      userId: this.item.userId,
    }
  },
};
</script>

<style scoped>
.fixed-header {
  position: fixed;
  top: 0;
  width: 100%;
  z-index: 1000;
  background-color: white;
  box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
}
</style>
