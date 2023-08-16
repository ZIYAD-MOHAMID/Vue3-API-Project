<script setup>
import { ref } from 'vue'
import { RouterLink } from 'vue-router'
// eslint-disable-next-line no-unused-vars
import LOGINMODAL from '../components/header.vue'
import Loder from '../components/loder.vue'
let show = false

import axios from 'axios'
const link = 'https://tarmeezacademy.com/api/v1'
const urlParams = new URLSearchParams(window.location.search)
const id = urlParams.get('postId')

const post = ref()
const comments = ref()
const autor = ref()
function getThePost() {
  show = true
  axios
    .get(`${link}/posts/${id}`)
    .then((response) => {
      post.value = response.data.data
      comments.value = post.value.comments
      autor.value = post.value.author
      // console.log(post.value)
      // console.log(comments.value)
      console.log(autor.value)
    })
    .finally(() => {
      show = false
    })
}
getThePost()

let inputComment = ''
function AddComment() {
  let param = {
    body: inputComment
  }
  let token = localStorage.getItem('token')
  const link = `https://tarmeezacademy.com/api/v1/posts/${id}/comments`
  console.log(token)
  console.log(inputComment)
  show = true
  axios
    .post(link, param, {
      headers: {
        authorization: `Bearer ${token}`
      }
    })
    .then((response) => {
      console.log(response.data)
      window.location.reload()
    })
    .catch((response) => {
      console.log(response)
    })
    .finally(() => {
      show = false
    })
}
</script>

<template>
  <main class="main">
    <LOGINMODAL />
    <Loder />

    <div class="container">
      <div class="row d-flex justify-content-center mt-5">
        <div class="col-9">
          <h1>
            <span> {{ autor.name }}'s </span>
            Post
          </h1>
        </div>
      </div>
      <div class="d-flex justify-content-center mt-5">
        <div class="col-9">
          <div>
            <!-- POST -->
            <div class="card shadow">
              <div class="card-header">
                <img
                  class="rounded-circle border border-2"
                  :src="autor.profile_image"
                  alt=""
                  style="width: 40px; height: 40px"
                />
                <b>{{ autor.username || '' }}</b>
              </div>
              <div class="card-body">
                <img class="w-100" :src="post.image" alt="" />
                <h6 style="color: rgb(193, 193, 193)" class="mt-1">{{ post.created_at }}</h6>
                <h5>{{ post.title || '' }}</h5>
                <p>
                  {{ post.body || '' }}
                </p>
                <hr />
                <div>
                  <svg
                    xmlns="http://www.w3.org/2000/svg"
                    width="16"
                    height="16"
                    fill="currentColor"
                    class="bi bi-pen"
                    viewBox="0 0 16 16"
                  >
                    <path
                      d="m13.498.795.149-.149a1.207 1.207 0 1 1 1.707 1.708l-.149.148a1.5 1.5 0 0 1-.059 2.059L4.854 14.854a.5.5 0 0 1-.233.131l-4 1a.5.5 0 0 1-.606-.606l1-4a.5.5 0 0 1 .131-.232l9.642-9.642a.5.5 0 0 0-.642.056L6.854 4.854a.5.5 0 1 1-.708-.708L9.44.854A1.5 1.5 0 0 1 11.5.796a1.5 1.5 0 0 1 1.998-.001zm-.644.766a.5.5 0 0 0-.707 0L1.95 11.756l-.764 3.057 3.057-.764L14.44 3.854a.5.5 0 0 0 0-.708l-1.585-1.585z"
                    />
                  </svg>
                  <span> ({{ post.comments_count }}) Comments </span>
                </div>
              </div>
            </div>
            <!--// POST //-->
          </div>
        </div>
      </div>
      <div class="input-group mb-3">
        <input
          type="text"
          placeholder="Add your comment here..."
          class="addComt"
          v-model="inputComment"
        />
        <button class="btn btn-outline-primary clik" type="button" @click="AddComment()">
          Add
        </button>
      </div>
      <div
        class="col-9 mt-5 p-3 rounded-3 mt-3 comnt"
        style="background: rgb(241, 246, 255)"
        v-for="comment in comments"
        :key="comment.id"
      >
        <div class="heeader">
          <img
            :src="comment.author.profile_image"
            class="img-thumbnail rounded-5"
            style="width: 40px; height: 40px"
          />
          <b>{{ comment.author.name }}</b>
        </div>

        <div class="body">
          {{ comment.body }}
          <!-- Lorem ipsum dolor sit amet consectetur -->
        </div>
      </div>
    </div>
    <router-link class="go-back" to="/">Go Back</router-link>
  </main>
</template>

<style scoped lang="scss">
.main {
  height: 120vh;

  .comnt {
    margin: auto;
    .heeader {
      width: 100%;
    }
    .body {
      width: 100%;
    }
  }

  .input-group {
    position: relative;
    display: flex;
    flex-wrap: wrap;
    width: 100%;
    margin: 50px 0;
    justify-content: center;
    align-content: center;
    .addComt {
      padding: 25px;
      margin: 0px 0 0px;
      outline: none;
      width: 65%;
    }
    .clik {
      width: 10%;
    }
  }

  .go-back {
    display: flex;
    align-items: center;
    justify-content: center;
    margin: 25px 0;
  }
}
</style>
