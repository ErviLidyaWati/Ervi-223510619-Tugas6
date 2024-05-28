<template>
  <div class="container">
    <div class="form-container-fixed">
      <form @submit.prevent="save" class="form">
        <input type="text" v-model="form.title" placeholder="Title" class="form-input" /><br />
        <textarea v-model="form.content" placeholder="Content" class="form-input"></textarea><br />
        <button type="submit" class="btn btn-save">{{ form.id ? 'Update' : 'Save' }}</button>
      </form>
      <button @click="toggleLoad" class="btn btn-load">{{ loaded ? 'Hide' : 'Load' }}</button>
    </div>
    <ul v-if="loaded" class="articles-list">
      <li v-for="article in articles" :key="article.id" class="article-item">
        <div class="article">
          <h3>{{ article.title }}</h3>
          <p>{{ article.content }}</p>
          <button @click="edit(article)" class="btn btn-edit">Edit</button>
          <button @click="remove(article.id)" class="btn btn-delete">Delete</button>
        </div>
      </li>
    </ul>
  </div>
</template>

<script>
export default {
  data() {
    return {
      articles: [],
      form: {
        id: null,
        title: '',
        content: ''
      },
      loaded: false
    };
  },
  methods: {
    async save() {
      if (this.form.id) {
        const index = this.articles.findIndex(article => article.id === this.form.id);
        if (index !== -1) {
          this.articles.splice(index, 1, { ...this.form });
          // Ensure reactivity
          this.articles = [...this.articles];
        }
      } else {
        const newArticle = { ...this.form, id: Date.now().toString() };
        this.articles.push(newArticle);
      }
      this.resetForm();
    },
    edit(article) {
      this.form = { ...article };
    },
    remove(id) {
      const index = this.articles.findIndex(article => article.id === id);
      if (index !== -1) {
        this.articles.splice(index, 1);
        // Ensure reactivity
        this.articles = [...this.articles];
      }
    },
    async load() {
      try {
        const response = await fetch('/db.json');
        const data = await response.json();
        this.articles = data.articles;
      } catch (error) {
        console.error('Error loading articles:', error);
      }
    },
    toggleLoad() {
      if (!this.loaded) {
        this.load();
      }
      this.loaded = !this.loaded;
    },
    resetForm() {
      this.form = {
        id: null,
        title: '',
        content: ''
      };
    }
  },
  mounted() {
    this.load();
  }
};
</script>




<style scoped>
.container {
  width: 1200px;
  margin: 0 auto;
  padding: 40px;
  font-family: Arial, sans-serif;
  background-color: #f9dccb6c;
  border-radius: 10px;
  box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
  overflow: hidden;
}

.form-container-fixed {
  position: sticky;
  top: 0;
  background-color: f9edc7;
  z-index: 10;
  padding-bottom: 20px;
}

.form {
  display: flex;
  flex-direction: column;
  margin-bottom: 20px;
}

.form-input {
  width: 100%;
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #f8ead0;
  border-radius: 5px;
  font-size: 16px;
}

.btn {
  padding: 10px 20px;
  border: none;
  border-radius: 5px;
  font-size: 16px;
  cursor: pointer;
  transition: background-color 0.3s;
}

.btn:hover {
  background-color: #9d8c6e;
}

.btn:focus {
  outline: none;
}

.btn-save {
  background-color: #a78969;
  color: #ffffff;
}

.btn-edit {
  background-color: #c78f20;
  color: #fff;
  margin-right: 10px;
}

.btn-delete {
  background-color: #7e6103;
  color: #fff;
}

.btn-load {
  background-color: #b5865a;
  color: #fff;
}

.articles-list {
  list-style: none;
  padding: 0;
  max-height: 300px; /* Limit the height of the articles list */
  overflow-y: auto; /* Add vertical scroll */
  margin-top: 20px;
}

.article-item {
  margin-bottom: 20px;
}

.article {
  padding: 20px;
  background-color: #f9dccb6c;
  border-radius: 5px;
  box-shadow: 0 0 5px rgba(0, 0, 0, 0.1);
}

.article h3 {
  margin-top: 0;
  color: #333;
}

.article p {
  color: #666;
}
</style>
