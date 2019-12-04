<template>
  <div class="q-pa-md">
    <q-toolbar class="bg-blue text-white q-my-md shadow-2">
      <q-btn flat round dense icon="menu" class="q-mr-sm" />
      <q-space />
      <q-btn icon="add" label="Add Component" stack color="grey" size="10px">
        <q-popup-proxy>
        <q-card class="my-card bg-grey-6 text-white">
          <q-card-actions vertical>
            <q-btn flat @click="em">New Block</q-btn>
            <q-btn flat @click="em3">New Arrow</q-btn>
          </q-card-actions>
        </q-card>
        </q-popup-proxy>
      </q-btn>
      <q-space />
      <q-btn icon="add" label="Add Lane" stack color="grey" size="10px" @click="em2"/>
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
      <q-btn icon="folder_open" label="Open File" stack color="grey" size="10px">
        <q-popup-proxy>
          <div class="q-gutter-md row items-start">
            <q-uploader
              url="http://localhost:4444/upload"
              label="Upload your Image"
              multiple =  False
              accept=".jpg, image/*"
              style="max-width: 500px"
            />
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
      <q-btn icon="emoji_objects" label="Suggestions" stack color="grey" size="10px"/>
      <q-space />
      <q-btn icon="crop" label="Crop Image" stack color="grey" size="10px"/>
      <q-space />
      <q-btn icon="compare_arrows" label="Compare" stack color="grey" size="10px"/>
      <q-space />
      <q-btn @click="$q.notify({message:'Process saved.', icon:'bookmark'})" icon="bookmark" label="Save Process" stack color="grey" size="10px"/>
      <q-space />
      <q-btn icon="share" label="Insight" stack color="grey" size="10px"/>
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
      <q-btn :disable="undoStack.length === 0" @click="em4; $q.notify({message:'Previous action undone.', icon:'undo'})" icon="undo" label="Undo" stack color="grey" size="10px"/>
      <q-space />
      <q-btn :disable="redoStack.length === 0" @click="em5; $q.notify({message:'Previous action redone.', icon:'redo'})" icon="redo" label="Redo" stack color="grey" size="10px"/>
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
      help: false
    }
  },

  methods: {
    em: function () {
      this.$emit('addcomp')
    },

    em2: function () {
      this.$emit('addlane')
    },

    em3: function () {
      this.$emit('addArrow')
    },
    em4: function () {
      this.$emit('undoAction')
    },
    em5: function () {
      this.$emit('redoAction')
    }
  }
}
</script>
