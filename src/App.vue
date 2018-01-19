<template>
  <div id="app">
    <nav class="navbar navbar-dark bg-primary mb-3">
      <div class="container">
        <a href="/" class="navbar-brand text-white">Server Articles</a>
      </div>
    </nav>
    <div class="container postsContainer">
      <div class="card card-body card-form">
        <h1>Add New Article</h1>
        <p class="lead">What's going on there</p>
        <div class="form-group">
          <input type="text" v-model="blog.title" class="form-control" placeholder="Post Title">
        </div>

        <div class="form-group">
          <textarea class="form-control" v-model="blog.body" placeholder="Post Body"></textarea>
        </div>
        <br>
        <div v-if="!submitted">
          <input type="submit" @click.prevent="submitPost()" class="btn btn-primary btn-lg">  
          <input type="button" @click.prevent="clearField()" class="btn btn-muted btn-lg" value="Clear">  
        </div>
        <div v-if="submitted">
          
          <input type="button" @click.prevent="updatePost(blog.id)" class="btn btn-warning btn-lg" value="Update Post">  
          <input type="button" @click.prevent="cancelEdit()" class="btn btn-muted btn-lg" value="Cancel Edit">  
        </div>
        <br>
          <input type="hidden" class="form-control" placeholder="ID" v-model="blog.id">
        
       <br>

        <div id="posts">
          <div class="card mb-3" v-for="post in posts" :key="post.id">
            <div class="card-body">
              <h4 class="card-title"> {{post.title}} </h4>
              <p class="card-text"> {{post.body}} </p>
              <a href="#" class="edit card-link" :data-id="post.id">
                <button class="btn btn-warning btn-sm" @click.prevent="editPost(post.id)">Edit</button>
              </a>
              <a href="#" class="delete card-link" :data-id="post.id">
                 <button class="btn btn-danger btn-sm" @click.prevent="deletePost(post.id)">Delete</button>
              </a>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const axios = require('axios')

  export default {
    data() {
      return {
        posts: [],
        blog: {
          title:'',
          body:'',
          id:''
        },
        submitted: false
      }
    },
    created() {
      this.getPost()
    },
    methods: {
      getPost() {
        axios.get('http://localhost:3000/posts')
        .then(data => this.posts = data.data)
        .catch(error => console.log(error))
      },
      
      submitPost () {
        if(this.blog.title === '' || this.blog.body === ''){
          alert('Please fill all the forms')
        }else{
          const postData = {
          title: this.blog.title,
          body: this.blog.body,
          id: this.blog.id  
          }
        axios.post('http://localhost:3000/posts', postData)
          .then(response =>{
            this.getPost()
            this.clearField()
            alert('New post added')
          })
          .catch(error => console.log(error))
        }
      },

      deletePost (id) {
        let confirmBox = confirm('Are you sure?')
        
        if(confirmBox) {
          axios.delete(`http://localhost:3000/posts/${id}`)
          .then(data => this.getPost())
          .catch(error => console.log(error))
        }
      },

      cancelEdit() {
        this.submitted = false
        this.clearField()
      },

      clearField(){
        this.blog.title = ''
        this.blog.body = ''
        this.blog.id = ''
      },

      editPost (id) {
        axios.get(`http://localhost:3000/posts/${id}`)
        .then(data => {
          this.blog.title = data.data.title
          this.blog.body = data.data.body
          this.blog.id = data.data.id
          this.submitted = true
          })
      },
      
      updatePost(id){
        axios.put(`http://localhost:3000/posts/${id}`, {
         title: this.blog.title,
         body: this.blog.body,
         id
        }).then(data => {
          this.getPost()
          this.clearField()
          alert('Post Updated')
          this.submitted = false
        })
      }      
    }
  }

</script>
