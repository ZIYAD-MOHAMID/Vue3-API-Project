<script setup>
import { ref } from 'vue'
// eslint-disable-next-line no-unused-vars
import LOGINMODAL from '../components/header.vue'
import Loder from '../components/loder.vue'
let show = false

import axios from 'axios'
const link = 'https://tarmeezacademy.com/api/v1'
const urlParams = new URLSearchParams(window.location.search)
const id = urlParams.get('postId')
// const userId = 2262
const userId = id

let responseData = ref()
function getUser() {
  show = true
  axios
    .get(`${link}/users/${userId}`)
    .then((response) => {
      responseData.value = response.data.data
      // console.log(response)
    })
    .finally(() => {
      show = false
    })
}

let posts = ref()
function getPostUser() {
  show = true

  axios
    .get(`${link}/users/${userId}/posts`)
    .then((response) => {
      posts.value = response.data.data
      console.log(response)
    })
    .finally(() => {
      show = false
    })
}

import { useRouter } from 'vue-router'
const router = useRouter()
function postClicked(postId) {
  // alert(postId)
  router.push(`/post?postId=${postId}`)
}
function headerClicked(postId) {
  // alert(postId)
  router.push(`/profile?postId=${postId}`)
}

let infoOfposEdi = ''
function getIdOfPostForEd(post) {
  infoOfposEdi = post
}
let editeImgInput = ref()
let titleedite = ref()
let postBody = ref()
function ediPostClick() {
  console.log(infoOfposEdi)
  const token = localStorage.getItem('token')

  let formData = new FormData()
  formData.append('body', postBody.value.value)
  formData.append('title', titleedite.value.value)
  formData.append('image', editeImgInput.value.files[0])
  formData.append('_method', 'put')

  let header = {
    'Content-Type': 'multipart/form-data',
    authorization: `Bearer ${token}`
  }
  show = true

  // 'put' in real word = 'post'
  axios
    .post(`${link}/posts/${infoOfposEdi.id}`, formData, {
      headers: header
    })
    .then(() => {
      // localStorage.setItem('token', response.data.token)
      // localStorage.setItem('user', JSON.stringify(response.data.user))
      window.location.reload()
      showAlertProfile('You Jast Update a Post', 'success')

      // window.location.reload()
    })
    .catch((error) => {
      const massage = error.response.data.message
      showAlertProfile(massage, 'danger')
    })
    .finally(() => {
      show = false
    })
}

let infoOfposForDel = ''
function getIdPostForDel(post) {
  infoOfposForDel = post
}
function deletePostDone() {
  console.log(infoOfposForDel.id)

  const token = localStorage.getItem('token')

  let header = {
    authorization: `Bearer ${token}`
  }
  show = true

  axios
    .delete(`${link}/posts/${infoOfposForDel.id}`, {
      headers: header
    })
    .then(() => {
      showAlertProfile('You Jast Deleted Your Post', 'success')
      window.location.reload()
    })
    .catch((error) => {
      const massage = error.response.data.message
      showAlertProfile(massage, 'danger')
    })
    .finally(() => {
      show = false
    })
}

const alertPlaceholde = ref()
function showAlertProfile(customMessage, type = 'success') {
  const alert = (message, type) => {
    const wrapper = document.createElement('div')
    wrapper.innerHTML = [
      `<div class="alert alert-${type} alert-dismissible" role="alert">`,
      `   <div>${message}</div>`,
      '   <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>',
      '</div>'
    ].join('')

    alertPlaceholde.value.append(wrapper)
  }

  return alert(customMessage, type)

  // todo: hide the alert
  //   setTimeout(() => {
  //     // const alertToHide = bootstrap.Alert.getOrCreateInstance('#success-alert')
  //     // document.getElementById("success-alert").hide();
  //     // const alert = document.getElementById("success-alert")
  //     // const modalAlert = bootstrap.Alert.getInstance(alert)
  //     // modalAlert.hide()
  //   }, 2000)
}

let dataUser = ''
const storageUser = localStorage.getItem('user')
let getCurrentUser = JSON.parse(storageUser)
dataUser = getCurrentUser

getPostUser()
getUser()
</script>

<template>
  <main>
    <LOGINMODAL />

    <Loder />

    <!-- delete model -->
    <div
      class="modal fade delet-style-f"
      id="delete-post"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Delete your Post</h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <form>
              <div class="mb-3 delet-style">
                <label class="col-form-label">You Wont to Delete This post?</label>
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-primary" data-bs-dismiss="modal">Close</button>
            <button
              type="button"
              @click="deletePostDone()"
              class="btn btn-danger"
              data-bs-dismiss="modal"
            >
              Delete
            </button>
          </div>
        </div>
      </div>
    </div>
    <!-- edite model -->
    <div
      class="modal fade"
      id="edite-post"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Edite your Post</h1>
            <button
              type="button"
              class="btn-close"
              data-bs-dismiss="modal"
              aria-label="Close"
            ></button>
          </div>
          <div class="modal-body">
            <form>
              <div class="mb-3">
                <label for="recipient-name" class="col-form-label">change the Title:</label>
                <input type="text" class="form-control" ref="titleedite" />
              </div>

              <div class="mb-3">
                <textarea
                  style="
                    width: 100%;
                    height: 100px;
                    border-color: gray;
                    border-radius: 10px;
                    resize: none;
                  "
                  ref="postBody"
                >
                </textarea>
              </div>

              <div class="mb-3">
                <label for="recipient-name" class="col-form-label">The Image:</label>
                <input type="file" class="form-control" ref="editeImgInput" />
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button
              type="button"
              @click="ediPostClick()"
              class="btn btn-primary"
              data-bs-dismiss="modal"
            >
              edite
            </button>
          </div>
        </div>
      </div>
    </div>

    <div class="container" style="height: 1000px">
      <div class="d-flex justify-content-center mt-5">
        <div class="col-9">
          <!-- MAIN INFO -->
          <div class="card shadow">
            <div class="card-body">
              <div class="row the-row">
                <div class="col-2">
                  <img
                    class="the-img"
                    id="main-info-image"
                    :src="responseData.profile_image"
                    alt=""
                    style="width: 120px; height: 120px; border-radius: 200px !important"
                  />
                </div>

                <div
                  id="main-info"
                  class="col-4 d-flex flex-column justify-content-evenly info-foile"
                  style="font-weight: 900"
                >
                  <div class="user-main-info" id="main-info-name">{{ responseData.name }}</div>
                  <div class="user-main-info" id="main-info-username">
                    {{ responseData.username }}
                  </div>
                  <div class="user-main-info" id="main-info-email">{{ responseData.email }}</div>
                </div>

                <div class="col-4 d-flex flex-column justify-content-evenly nam">
                  <div class="number-info">
                    <span id="posts-count">{{ responseData.posts_count }}</span> Posts
                  </div>
                  <div class="number-info">
                    <span id="comments-count">{{ responseData.comments_count }}</span> Comments
                  </div>
                </div>
              </div>
            </div>
          </div>

          <!-- POSTS TITLE -->
          <div class="row mt-5">
            <h1>
              <span id="name-posts">{{ responseData.name }}'s </span> Posts
            </h1>
          </div>

          <!-- POST -->
          <div class="card shadow row" v-for="post in posts" :key="post.id">
            <div @click="headerClicked(`${post.author.id}`)" class="card-header">
              <img
                class="rounded-circle border border-2"
                :src="post.author.profile_image"
                alt=""
                style="width: 40px; height: 40px"
              />
              <b>{{ post.author.name || '' }}</b>
            </div>

            <div class="edate" v-if="post.author.id === dataUser.id">
              <div
                data-bs-toggle="modal"
                data-bs-target="#edite-post"
                @click="getIdOfPostForEd(post)"
              >
                Edite
              </div>
            </div>

            <div class="delet" v-if="post.author.id === dataUser.id">
              <div
                data-bs-toggle="modal"
                data-bs-target="#delete-post"
                @click="getIdPostForDel(post)"
              >
                Delete
              </div>
            </div>

            <div class="card-body" @click="postClicked(`${post.id}`)" style="cursor: pointer">
              <img class="w-100" :src="post.image" alt="" />

              <h6 style="color: rgb(193, 193, 193)" class="mt-1">{{ post.created_at }}</h6>

              <h5>{{ post.title }}</h5>

              <p>{{ post.body }}</p>

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

                <span>
                  ({{ post.comments_count }}) Comments

                  <span class="" v-for="tag in post.tags" :key="tag">
                    <span class="tag">{{ tag.arabic_name }}</span>
                  </span>
                </span>
                <!-- id="post-tags-${post.id}" -->
              </div>
            </div>
          </div>
        </div>
      </div>
    </div>

    <!-- alert -->
    <div
      ref="alertPlaceholde"
      id="success-alert"
      class="show fade"
      style="position: fixed; z-index: 999999; width: 30%; bottom: 0; right: 0"
    ></div>
  </main>
</template>

<style scoped lang="scss">
.card-header {
  cursor: pointer;
}
.delet-style-f {
  .delet-style {
    display: flex;
  }
}

.edate {
  height: 4%;
  width: 13%;
  background-color: rgba(143, 40, 238, 0.54);
  position: absolute;
  top: 10px;
  right: 10px;
  padding: 10px 20px 20px;
  border-radius: 10px;
  cursor: pointer;
  div {
    color: #000;
    font-weight: bold;
    font-style: italic;
    display: flex;
    transform: translate(0, -6px);
    align-items: center;
    justify-content: center;
  }
}

.delet {
  height: 4%;
  width: 13%;
  max-height: 7%;
  background-color: rgba(249, 28, 28, 0.54);
  position: absolute;
  top: 10px;
  right: 145px;
  padding: 10px 20px 20px;
  border-radius: 10px;
  cursor: pointer;
  div {
    display: flex;
    transform: translate(0, -6px);
    align-items: center;
    justify-content: center;
    color: #000;
    font-weight: bold;
    font-style: italic;
  }
}
.the-row {
  justify-content: space-between;
  .the-img {
    border: 5px solid #0d6efd;
  }
  .info-foile {
    font-size: 20px;
    #main-info-name {
      font-weight: bold;
      font-size: 26px;
    }
  }
  .nam {
    font-style: italic;
    .number-info {
      font-weight: bold;
      #comments-count {
        font-size: 26px;
      }
      #posts-count {
        font-size: 26px;
      }
    }
  }
}
.card {
  margin: 40px 0;
}
.tag {
  background-color: #0d6efd;
  padding: 10px 20px;
  margin: 5px 10px;
  border-radius: 6px;
  color: cornsilk;
}
</style>
