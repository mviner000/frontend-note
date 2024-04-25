<template>
  <div>
    <h1 class="text-3xl font-bold mb-4">All Notes</h1>
    <div v-if="error" class="error-message mb-4">{{ error }}</div>
    <table class="w-full border-collapse">
      <thead>
        <tr>
          <th class="border border-gray-400 px-4 py-2">Title</th>
          <th class="border border-gray-400 px-4 py-2">Content</th>
          <th class="border border-gray-400 px-4 py-2">Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="note in notes" :key="note.id">
          <td class="border border-gray-400 px-4 py-2">{{ note.title }}</td>
          <td class="border border-gray-400 px-4 py-2">{{ note.content }}</td>
          <td class="border border-gray-400 px-4 py-2">
            <button @click="deleteNote(note.id)" class="bg-red-500 hover:bg-red-700 text-white font-bold py-2 px-4 rounded">Delete</button>
          </td>
        </tr>
      </tbody>
    </table>
    <form @submit.prevent="createNote" class="mt-4">
      <div class="flex mb-4">
        <input type="text" v-model="newNote.title" placeholder="Title" class="border border-gray-400 px-4 py-2 mr-2 flex-1" required>
        <textarea v-model="newNote.content" placeholder="Content" class="border border-gray-400 px-4 py-2 flex-1" required></textarea>
      </div>
      <button type="submit" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">Add Note</button>
    </form>
  </div>
</template>

<script setup lang="ts">
import { ref, onMounted } from 'vue';
import axios from 'axios';

// Define the type for a note
interface Note {
  id: number;
  title: string;
  content: string;
}

// Define a reactive variable to store the notes
const notes = ref<Note[]>([]);
const error = ref<string | null>(null);

// Fetch notes from the API
const fetchNotes = async () => {
  try {
    const response = await axios.get<Note[]>('https://gjclibrary.com/api/notes/');
    notes.value = response.data;
  } catch (error) {
    console.error('Error fetching notes:', error);
    setError('Error fetching notes');
  }
};

// Fetch notes when the component is mounted
onMounted(fetchNotes);

// Function to create a new note
const createNote = async () => {
  try {
    const response = await axios.post<Note>('https://gjclibrary.com/api/notes/', newNote.value);
    notes.value.push(response.data);
    newNote.value.title = ''; // Clear input fields
    newNote.value.content = ''; // Clear input fields
    setError(null); // Clear error
  } catch (error) {
    console.error('Error creating note:', error);
    setError('Error creating note');
  }
};

// Function to delete a note
const deleteNote = async (noteId: number) => {
  try {
    await axios.delete(`https://gjclibrary.com/api/notes/${noteId}`);
    notes.value = notes.value.filter(note => note.id !== noteId);
    setError(null); // Clear error
  } catch (error) {
    console.error('Error deleting note:', error);
    setError('Error deleting note');
  }
};


// Define the new note object
const newNote = ref<Note>({
  id: 0,
  title: '',
  content: '',
});

// Function to set error
const setError = (errorMsg: string | null) => {
  error.value = errorMsg;
};
</script>

<style scoped>
.error-message {
  color: red;
}
</style>
