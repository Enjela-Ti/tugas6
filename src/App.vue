<template>
  <div class="background">
  </div>
  <div>
    <Img />
    <Imge />
  </div>

  <div>
    <div class="inputan">
    <h1>Create New Post</h1>
    <label for="title">Titel</label>
    <input id="title" v-model="title" placeholder="Isi Titel" type="text" />

    <label for="body">Content</label>
    <textarea id="body" v-model="body" placeholder="Isi Konten"></textarea>
  </div>
  <div class="button">
    <button @click="savePost" >Save</button>
  </div>


    <h2>Posts</h2>
    <table>
      <thead>
        <tr>

          <th>Title</th>
          <th>Content</th>
          <th>Actions</th>
        </tr>
      </thead>
      <tbody>
        <tr v-for="post in posts" :key="post.id">

          <td>{{ post.title }}</td>
          <td>{{ post.body }}</td>
          <td>
            <div class="tombol">
            <button @click="editPost(post)" id="edit">Edit</button>
            <button @click="deletePost(post.id)"id="delete">Delete</button>
          </div>
          </td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script setup>
import { ref, onMounted } from 'vue';
import axios from 'axios';
import Swal from 'sweetalert2';
import Img from './components/images.vue';
import Imge from './components/image1.vue';

const title = ref('');
const body = ref('');
const posts = ref([]);

// Update the URL to point to json-server running on localhost:3000
const fetchPosts = async () => {
  try {
    const response = await axios.get('http://localhost:3000/posts');
    posts.value = response.data;
  } catch (error) {
    console.error('Error fetching posts:', error);
    Swal.fire('Error', 'Could not fetch posts', 'error');
  }
};

const savePost = async () => {
  if (title.value && body.value) {
    try {
      await axios.post('http://localhost:3000/posts', {
        title: title.value,
        body: body.value
      });
      title.value = '';
      body.value = '';
      fetchPosts();
      Swal.fire('Success', 'Post has been saved successfully!', 'success');
    } catch (error) {
      console.error('Error saving post:', error);
      Swal.fire('Error', 'Could not save post', 'error');
    }
  } else {
    Swal.fire('Error', 'Please enter title and content', 'error');
  }
};


const deletePost = async (id) => {
  const result = await Swal.fire({
    title: 'Are you sure?',
    text: "You won't be able to revert this!",
    icon: 'warning',
    showCancelButton: true,
    confirmButtonText: 'Yes, delete it!',
    cancelButtonText: 'No, cancel!'
  });

  if (result.isConfirmed) {
    try {
      await axios.delete(`http://localhost:3000/posts/${id}`);
      fetchPosts();
      Swal.fire('Deleted!', 'Your file has been deleted.', 'success');
    } catch (error) {
      console.error('Error deleting post:', error);
      Swal.fire('Error', 'Could not delete post', 'error');
    }
  }
};

const editPost = async (post) => {
  const { value: formValues } = await Swal.fire({
    title: 'Edit Post',
    html:
      `<input id="swal-input1" class="swal2-input" value="${post.title}">` +
      `<textarea id="swal-input2" class="swal2-textarea">${post.body}</textarea>`,
    focusConfirm: false,
    preConfirm: () => {
      return [
        document.getElementById('swal-input1').value,
        document.getElementById('swal-input2').value
      ];
    }
  });

  if (formValues) {
    try {
      await axios.put(`http://localhost:3000/posts/${post.id}`, {
        title: formValues[0],
        body: formValues[1]
      });
      fetchPosts();
      Swal.fire('Updated!', 'Your post has been updated.', 'success');
    } catch (error) {
      console.error('Error updating post:', error);
      Swal.fire('Error', 'Could not update post', 'error');
    }
  }
};

onMounted(fetchPosts);
</script>

<style scoped>
.inputan {
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 10px;
}
.inputan input,textarea{
  padding: 3px;
  background-color: transparent;
  backdrop-filter: blur(5px);
  border: 1px solid white;
}
.inputan textarea::placeholder{
  color: white;
}
.inputan input::placeholder{
  color: white;
}

.button button{
  background-color: green;
}

table {
  border-collapse: collapse;
  width: 100%;
}
tr, th, td {
  color: white;
  border: 1px solid #ddd;
  padding: 8px;
  text-align: left;
  backdrop-filter: blur(5px);
}

th{
  background-color: gray;
}

.tombol button {
  margin: 10px;
}

#edit{
  background-color: orange;
}

#delete{
  background-color: red;
}

.background{
  background-color: plum;
  position: fixed;
  top: 0;
  left: 0;
  min-height: 90%;
  min-width: 1500px;
  width: 100%;
  height: 100%;
  overflow: hidden;
  z-index: -2;
  object-fit: cover;
  -webkit-background-size:cover; -moz-background-size:cover; -o-background-size:cover; background-size: cover;
  filter: brightness(0.8);
}
</style>
