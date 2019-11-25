<template>
  <div class="q-pa-md">
    <q-toolbar class="bg-blue text-white q-my-md shadow-2">
      <q-btn flat round dense icon="menu" class="q-mr-sm" />
      <q-space />
      <q-btn icon="add" label="Add Component" stack color="grey" size="10px"/>
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
      <q-btn :disable="undoStack.length === 0" @click="undo; $q.notify({message:'Previous action undone.', icon:'undo'})" icon="undo" label="Undo" stack color="grey" size="10px"/>
      <q-space />
      <q-btn :disable="redoStack.length === 0" @click="redo; $q.notify({message:'Previous action redone.', icon:'redo'})" icon="redo" label="Redo" stack color="grey" size="10px"/>
      <q-space />
      <q-btn label="Undo/Redo test textfield" size="10px">
        <q-popup-proxy>
          <div class="row justify-around items-center q-mt-md">
            <div
              ref="editor"
              v-mutation="handler"
              contentEditable="true"
              class="editable rounded-borders q-pa-sm overflow-auto"
            >Type here</div>
          </div>
        </q-popup-proxy>
      </q-btn>
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
    undo () {
      // shift the stack
      const data = this.undoStack.shift()
      if (data !== void 0) {
        // block undo from receiving its own data
        this.undoBlocked = true
        this.$refs.editor.innerText = data
      }
    },

    redo () {
      // shift the stack
      const data = this.redoStack.shift()
      if (data !== void 0) {
        // unblock undo from receiving redo data
        this.undoBlocked = false
        this.$refs.editor.innerText = data
      }
    },

    handler (mutationRecords) {
      mutationRecords.forEach(record => {
        if (record.type === 'characterData') {
          this.undoStack.unshift(record.oldValue)
          this.checkStack(this.undoStack)
          this.clearStack(this.redoStack)
        } else if (record.type === 'childList') {
          record.removedNodes.forEach(node => {
            if (this.undoBlocked === false) {
              // comes from redo
              this.undoStack.unshift(node.textContent)
            } else {
              // comes from undo
              this.redoStack.unshift(node.textContent)
            }
          })

          // check stacks
          this.checkStack(this.undoStack)
          this.checkStack(this.redoStack)
          this.undoBlocked = false
        }
      })
    },

    checkStack (stack) {
      if (stack.length > this.maxStack) {
        stack.splice(this.maxStack)
      }
    },

    clearStack (stack) {
      stack.splice(0)
    }
  }
}
</script>
