<template>
  <div class="app-container">
    <div class="container">
      <form @submit.prevent="save" class="film-form">
        <input type="text" v-model="form.judul" placeholder="Judul" />
        <textarea v-model="form.genre" placeholder="Genre"></textarea>
        <button type="submit">Save</button>
      </form>

      <button @click="load" class="load-button">Load</button>

      <div class="table-wrapper">
        <table class="film-table">
          <thead>
            <tr>
              <th>Judul</th>
              <th>Genre</th>
              <th>Actions</th>
            </tr>
          </thead>
          <tbody>
            <tr v-for="film in films" :key="film.id">
              <td>{{ film.judul }}</td>
              <td>{{ film.genre }}</td>
              <td>
                <button @click="edit(film)" class="edit-button">Edit</button>
                <button @click="del(film.id)" class="delete-button">Delete</button>
              </td>
            </tr>
          </tbody>
        </table>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, reactive } from 'vue';
import axios from 'axios';

export default {
  setup() {
    const form = reactive({
      id: null,
      judul: '',
      genre: '',
    });

    const films = ref([]);
    const originalFilms = ref([]);

    async function load() {
      try {
        const response = await axios.get('http://localhost:3000/films');
        films.value = response.data;
        originalFilms.value = [...response.data]; // Save the original data
      } catch (error) {
        console.error('Error loading films', error);
      }
    }

    async function save() {
      try {
        const url = form.id
          ? `http://localhost:3000/films/${form.id}`
          : 'http://localhost:3000/films';
        const method = form.id ? 'put' : 'post';
        const response = await axios[method](url, form);

        if (form.id) {
          films.value = films.value.map(film =>
            film.id === response.data.id ? response.data : film
          );
        } else {
          films.value.push(response.data);
        }

        form.id = null;
        form.judul = '';
        form.genre = '';
      } catch (error) {
        console.error('Error saving film', error);
      }
    }

    async function del(id) {
      try {
        await axios.delete(`http://localhost:3000/films/${id}`);
        films.value = films.value.filter(film => film.id !== id);
      } catch (error) {
        console.error('Error deleting film', error);
      }
    }

    function edit(film) {
      form.id = film.id;
      form.judul = film.judul;
      form.genre = film.genre;
    }

    onMounted(load);

    return { form, films, save, edit, del, load };
  },
};
</script>

<style>
:root {
  --background-color: #121212;
  --primary-color: #1f1f1f;
  --secondary-color: #282828;
  --text-color: #e0e0e0;
  --button-color: transparent;
  --button-hover-color: #000000;
  --edit-button-color: #03dac6;
  --edit-button-hover-color: #018786;
  --delete-button-color: #cf6679;
  --delete-button-hover-color: #b00020;
  --table-header-bg: #444444;
  --table-border: #555555;
  --table-row-even-bg: #1c1c1c;
  --table-row-odd-bg: #262626;
  --table-hover-bg: #333333;
}

* {
  font-family:'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
}

body {
  background-color: var(--background-color);
  color: var(--text-color);
}

.app-container {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
  background-color: var(--background-color);
}

.container {
  background-color: var(--primary-color);
  padding: 20px;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.5);
  width: 100%;
  max-width: 800px;
}

.film-form {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

.film-form input,
.film-form textarea {
  margin-bottom: 10px;
  padding: 10px;
  font-size: 16px;
  border: 1px solid #333;
  border-radius: 4px;
  background-color: var(--secondary-color);
  color: var(--text-color);
}

.film-form input::placeholder,
.film-form textarea::placeholder {
  color: #888;
}

.film-form button {
  padding: 10px 20px;
  font-size: 16px;
  background-color: var(--button-color);
  color: whitesmoke;
  border: solid 1px whitesmoke;
  border-radius: 5px;
  cursor: pointer;
}

.film-form button:hover {
  background-color: var(--button-hover-color);
}

.load-button {
  margin-bottom: 20px;
  padding: 10px 20px;
  font-size: 16px;
  background-color: var(--button-color);
  color: whitesmoke;
  border: solid 1px whitesmoke;
  border-radius: 5px;
  cursor: pointer;
}

.load-button:hover {
  background-color: var(--button-hover-color);
}

.table-wrapper {
  overflow-x: auto;
}

.film-table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
  background-color: var(--primary-color);
  color: var(--text-color);
}

.film-table thead th {
  border-bottom: 1px solid var(--table-border);
  padding: 10px;
  text-align: left;
  background-color: transparent;
  color: var(--text-color);
}

.film-table tbody td {
  border-bottom: 1px solid var(--table-border);
  padding: 10px;
}

.film-table tbody tr:nth-child(even) {
  background-color: var(--table-row-even-bg);
}

.film-table tbody tr:nth-child(odd) {
  background-color: var(--table-row-odd-bg);
}

.film-table tbody tr:hover {
  background-color: var(--table-hover-bg);
}

.edit-button,
.delete-button {
  margin-right: 5px;
  padding: 5px 10px;
  font-size: 14px;
  border: none;
  border-radius: 4px;
  cursor: pointer;
  color: var(--primary-color);
}

.edit-button {
  background-color: transparent;
  color: whitesmoke;
  border: solid 1px whitesmoke;
  border-radius: 5px;
  cursor: pointer;
}

.edit-button:hover {
  background-color: var(--edit-button-hover-color);
}

.delete-button {
  background-color: transparent;
  color: whitesmoke;
  border: solid 1px whitesmoke;
  border-radius: 5px;
  cursor: pointer;
}

.delete-button:hover {
  background-color: var(--delete-button-hover-color);
}
</style>
