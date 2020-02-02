<template>
  <div class="q-pa-md">
    <q-toolbar class="bg-blue text-white q-my-md shadow-2">
      <q-btn flat round dense icon="menu" class="q-mr-sm" />
      <q-space />
      <q-btn icon="add" label="Add Component" stack color="grey" size="10px">
        <q-popup-proxy>
        <q-card class="my-card bg-grey-6 text-white">
          <q-card-actions vertical>
            <q-btn flat @click="em2">Add Lane</q-btn>
            <q-btn flat @click="em3">Binarization</q-btn>
            <q-btn flat @click="em4">Image Segmentation</q-btn>
            <q-btn flat @click="em5">OCR</q-btn>
          </q-card-actions>
        </q-card>
        </q-popup-proxy>
      </q-btn>
      <q-space />
      <q-btn icon="add" label="Own Image" stack color="grey" size="10px">
        <q-popup-proxy>
          <q-input color="black" v-model="url" placeholder="Enter your URL here" label="Image">
            <template v-slot:append>
                <q-icon v-if="url !== ''" name="close" @click="url = ''" class="cursor-pointer" />
            </template>
            <template v-slot:after>
              <q-btn round dense flat icon="done" @click="ownIMG" />
            </template>
          </q-input>
        </q-popup-proxy>
      </q-btn>
      <q-space />
      <q-btn icon="create_new_folder" label="New File" stack color="grey" size="10px">
        <q-popup-proxy>
          <q-card class="my-card bg-grey-6 text-white">
            <q-card-actions vertical>
              <q-btn flat>New Project</q-btn>
              <q-btn flat>New Process</q-btn>
            </q-card-actions>
          </q-card>
       </q-popup-proxy>
      </q-btn>
      <q-space />
      <q-btn icon="folder_open" label="Open Image" stack color="grey" size="10px">
        <q-popup-proxy>
          <div class="q-gutter-xs bg-grey-6 column items-start">
            <q-btn @click="image1">
              <q-avatar rounded size="80px">
                <q-img :src="'statics/img1.jpg'"/>
              </q-avatar>
            </q-btn>
            <q-btn @click="image2">
              <q-avatar rounded size="80px">
                <q-img :src="'statics/img2.jpg'"/>
              </q-avatar>
            </q-btn>
            <q-btn @click="image3">
              <q-avatar rounded size="80px">
                <q-img :src="'statics/img3.jpg'"/>
              </q-avatar>
            </q-btn>
          </div>
        </q-popup-proxy>
      </q-btn>
      <q-space />
      <q-btn @click="$q.notify({message:'File saved succesfully.', icon:'save'})" icon="save" label="Save File" stack color="grey" size="10px"/>
      <q-space />
      <q-btn @click="help = true" icon="live_help" label="Get Help" stack color="grey" size="10px"/>
      <q-dialog v-model="help">
       <q-card>
        <q-card-section>
           Hallöchen lieber Nutzer/liebe Nutzerin. Gehen Sie bitte auf google.com für Hilfe. Aber sagen Sie nicht, dass wir Sie geschickt haben.
        </q-card-section>
        <q-card-actions align="right">
          <q-btn flat label="OK" color="primary" @click="help = false" />
        </q-card-actions>
       </q-card>
      </q-dialog>
      <q-space />
      <q-btn disabled icon="emoji_objects" label="Suggestions" stack color="grey" size="10px"/>
      <q-space />
      <q-btn disabled icon="crop" label="Crop Image" stack color="grey" size="10px"/>
      <q-space />
      <q-btn disabled icon="compare_arrows" label="Compare" stack color="grey" size="10px"/>
      <q-space />
      <q-btn @click="$q.notify({message:'Process saved.', icon:'bookmark'})" icon="bookmark" label="Save Process" stack color="grey" size="10px"/>
      <q-space />
      <q-btn disabled icon="share" label="Insight" stack color="grey" size="10px"/>
      <q-space />
      <q-btn icon="settings_applications" label="Settings" stack color="grey" size="10px">
        <q-popup-proxy>
          <q-card class="my-card bg-grey-6 text-white">
            <q-card-actions vertical>
              <q-btn flat>Themes...</q-btn>
              <q-btn flat>Preferences</q-btn>
            </q-card-actions>
          </q-card>
       </q-popup-proxy>
      </q-btn>
      <q-space />
      <q-btn @click="undo" icon="undo" label="Undo" stack color="grey" size="10px"/>
      <q-space />
      <q-btn @click="redo" icon="redo" label="Redo" stack color="grey" size="10px"/>
    </q-toolbar>
  </div>
</template>

<style>
</style>

<script>

export default {
  data () {
    return {
      maxStack: 100,
      undoStack: [],
      redoStack: [],
      undoBlocked: false,
      help: false,
      url: ''
    }
  },

  methods: {
    em2: function () {
      this.$emit('addlane')
    },

    em3: function () {
      this.$emit('addBIN')
    },

    em4: function () {
      this.$emit('addIMGSEG')
    },

    em5: function () {
      this.$emit('addOCR')
    },

    image1: function () {
      this.$emit('image1')
    },

    image2: function () {
      this.$emit('image2')
    },

    image3: function () {
      this.$emit('image3')
    },

    ownIMG: function () {
      this.$emit('ownIMG', this.url)
    },

    undo () {
      this.$emit('undoAction')
    },

    redo () {
      this.$emit('redoAction')
    }
  }
}
</script>
