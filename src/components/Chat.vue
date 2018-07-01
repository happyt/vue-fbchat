<template>
  <v-container fluid>
    <v-slide-y-transition mode="out-in">
      <v-layout column align-center>
         <blockquote>
          &#8220;First, solve the problem. Then, write the code.&#8221;
          <footer>
            <small>
              <em>&mdash;Chat room</em>
            </small>
          </footer>
        </blockquote>
            <v-data-table
                :headers="headers"
                :items="this.messages"
                hide-actions
                class="elevation-1"
            >
                <template slot="items" slot-scope="props">
                    <td>{{ props.item.name }}</td>
                    <td class="text-xs-right">
                        <a @click.prevent="deleteMessage(props.item.id)" href="#" class="card-link">Delete</a>
                    </td>
                    <td class="text-xs-right">
                        <a @click.prevent="editMessage(props.item)" href="#" class="card-link">Edit</a>
                    </td>
                    <td v-if="props.item !== editingMessage"  class="text-xs-right">{{ props.item.text }}</td>
                   <div v-else>
                     <textarea v-model="messageText"></textarea>
                     <td class="text-xs-right">
                        <a @click.prevent="cancelEditing" href="#" class="card-link">Cancel</a>
                    </td>
                    <td class="text-xs-right">
                        <a @click.prevent="updateMessage" href="#" class="card-link">Update</a>
                    </td>
                   </div>
                   
                </template>
            </v-data-table>

          <v-form v-if="!editingMessage" @submit.prevent="storeMessage">
            <v-flex xs8>
              <v-text-field v-model="messageText" label="Message"></v-text-field>
            </v-flex>
            <v-flex xs8>
              <v-text-field v-model="nickname" label="Nickname"></v-text-field>
            </v-flex> 
            <v-btn class="info" type="submit">Send</v-btn>
          </v-form>
      </v-layout>
    </v-slide-y-transition>
  </v-container>
</template>

<script>
import * as firebase from 'firebase/app'
import 'firebase/database'

// Initialize Firebase
var config = require(`../../fbconfig.js`)

firebase.initializeApp(config)

const db = firebase.database()
// db.ref('messages').on('value', snapshot => console.log(snapshot.val()))

export default {
  data () {
    return {
      messages: [],
      messageText: '',
      nickname: 'ianm',
      editingMessage: null,
      headers: [
        {
          text: 'Message',
          align: 'left',
          sortable: false,
          value: 'text'
        },
        { text: 'Who', value: 'name' }
      ]
    }
  },
  methods: {
    storeMessage () {
      if (this.nickname && this.messageText) {
        db.ref('messages').push({name: this.nickname, text: this.messageText})
        this.messageText = ''
      } else {
        const errorOption = {type: 'error'}
        this.$toasted.show('both message and nickname should be filled in!', errorOption)
      }
    },
    deleteMessage (id) {
      console.log(db.ref('messages'))
      db.ref('messages').child(id).remove()
    },
    editMessage (id) {
      this.editingMessage = id
      this.messageText = id.text
    },
    cancelEditing () {
      this.editingMessage = null
      this.messageText = ''
    },
    updateMessage () {
      db.ref('messages').child(this.editingMessage.id).update({ text: this.messageText })
      this.cancelEditing()
    }
  },
  created () {
    this.$toasted.show('hello billo')
    db.ref('messages').on('child_added', snapshot => this.messages.push({...snapshot.val(), id: snapshot.key}))
    db.ref('messages').on('child_removed', snapshot => {
      const deletedMessage = this.messages.find(message => message.id === snapshot.key)
      const index = this.messages.indexOf(deletedMessage)
      this.messages.splice(index, 1)
    })
    db.ref('messages').on('child_changed', snapshot => {
      const updatedMessage = this.messages.find(message => message.id === snapshot.key)
      updatedMessage.text = snapshot.val().text
    })
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h1, h2 {
  font-weight: normal;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
