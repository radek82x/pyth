  <div class="container">
    <h2>{{ chatTopic }}</h2>
    <div class="messages">
      <div v-for="(message, index) in messages" :key="index">
        <div v-if="message.sender_id === currentUser.id">
          <p>{{ message.content }}</p>
          <div v-if="message.file">File: {{ message.file }}</div>
          <div v-if="message.image">Image: {{ message.image }}</div>
          <div v-if="message.is_read">Read</div>
        </div>
        <div v-else>
          <p>{{ message.content }}</p>
          <div v-if="message.file">File: {{ message.file }}</div>
          <div v-if="message.image">Image: {{ message.image }}</div>
          <div v-if="message.is_read">Read</div>
          <button @click="requestHelp">Request Help</button>
        </div>
      </div>
    </div>
    <div>
      <input type="text" v-model="newMessage" placeholder="Type your message...">
      <input type="file" ref="fileInput" @change="handleFileUpload">
      <button @click="sendMessage">Send</button>
    </div>
  </div>
</template>

<script>
import axios from 'axios';

export default {
  data() {
    return {
      chatTopic: "",
      messages: [],
      newMessage: "",
      currentUser: {
        id: 1, // ID zalogowanego użytkownika
      },
      recipient_id: 2, // ID odbiorcy (innego użytkownika)
      chat_id: 1, // ID czatu
    };
  },
  created() {
    // Pobierz temat czatu
    this.getChatTopic();

    // Pobierz wiadomości czatu
    this.getChatMessages();
  },
  methods: {
    getChatTopic() {
      axios.get(`/api/chat/${this.chat_id}`)
        .then(response => {
          this.chatTopic = response.data.topic;
        })
        .catch(error => {
          console.error('Error fetching chat topic: ', error);
        });
    },
    getChatMessages() {
      axios.get(`/api/chat/${this.chat_id}/messages`)
        .then(response => {
          this.messages = response.data;
        })
        .catch(error => {
          console.error('Error fetching chat messages: ', error);
        });
    },
    sendMessage() {
      const formData = new FormData();
      formData.append('content', this.newMessage);
      formData.append('sender_id', this.currentUser.id);
      formData.append('receiver_id', this.recipient_id);
      formData.append('chat_id', this.chat_id);

      // Dodaj plik, jeśli został wybrany
      const file = this.$refs.fileInput.files[0];
      if (file) {
        formData.append('file', file);
      }

      axios.post(`/api/chat/${this.chat_id}/messages`, formData, {
        headers: {
          'Content-Type': 'multipart/form-data'
        }
      })
      .then(response => {
        // Dodaj nową wiadomość do lokalnej listy
        this.messages.push(response.data);
        // Wyczyść pole nowej wiadomości i input pliku
        this.newMessage = "";
        this.$refs.fileInput.value = null;
      })
      .catch(error => {
        console.error('Error sending message: ', error);
      });
    },
    handleFileUpload(event) {
      // Obsługa przesyłania plików
    },
    requestHelp() {
      const helpData = {
        chat_id: this.chat_id,
        sender_id: this.currentUser.id,
      };

      axios.post(`/api/help`, helpData)
        .then(response => {
          console.log('Help requested successfully');
        })
        .catch(error => {
          console.error('Error requesting help: ', error);
        });
    }
  }
}
</script>

<style scoped>
.container {
  margin-top: 50px;
}
.messages {
  margin-top: 20px;
}
</style>
