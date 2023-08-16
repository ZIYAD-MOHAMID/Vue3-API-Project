<!-- eslint-disable vue/no-export-in-script-setup -->
<script setup>
import { ref } from 'vue'

// import { RouterLink } from 'vue-router'
// import { useWindowScroll } from '@vueuse/core'
// import { useVirtualList, useInfiniteScroll } from '@vueuse/core'

import LOGINMODAL from '../components/header.vue'
import Loder from '../components/loder.vue'

import axios from 'axios'
const link = 'https://tarmeezacademy.com/api/v1'
const postsCard = ref()
let show = false
function getpost() {
  show = true
  axios
    .get(`${link}/posts?limit=10&page=1`)
    .then((response) => {
      // console.log(response.data)
      postsCard.value = response.data.data
      console.log(postsCard.value)
    })
    .finally(() => {
      show = false
    })
}
getpost()

import { useRouter } from 'vue-router'
const router = useRouter()
function postfukeF(postId) {
  // alert(postId)
  router.push(`/post?postId=${postId}`)
}
function headerClicked(postId) {
  // alert(postId)
  router.push(`/profile?postId=${postId}`)
}

let infoOfposForEdi = ''
function getIdOfPostForEdi(post) {
  infoOfposForEdi = post
}
let editeImgInput = ref()
let titleedite = ref()
let postBody = ref()
function editePostClick() {
  console.log(infoOfposForEdi)
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
    .post(`${link}/posts/${infoOfposForEdi.id}`, formData, {
      headers: header
    })
    .then(() => {
      // localStorage.setItem('token', response.data.token)
      // localStorage.setItem('user', JSON.stringify(response.data.user))
      showAlertHome('You Jast Update a Post', 'success')
      // window.location.reload()
    })
    .catch((error) => {
      const massage = error.response.data.message
      showAlertHome(massage, 'danger')
    })
    .finally(() => {
      show = false
    })
}

let infoOfposForDel = ''
function getIdOfPostForDel(post) {
  infoOfposForDel = post
}
function deletePostIsDone() {
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
      showAlertHome('You Jast Deleted Your Post', 'success')
      window.location.reload()
    })
    .catch((error) => {
      const massage = error.response.data.message
      showAlertHome(massage, 'danger')
    })
    .finally(() => {
      show = false
    })
}

const alertPlaceholderhome = ref()
function showAlertHome(customMessage, type = 'success') {
  const alert = (message, type) => {
    const wrapper = document.createElement('div')
    wrapper.innerHTML = [
      `<div class="alert alert-${type} alert-dismissible" role="alert">`,
      `   <div>${message}</div>`,
      '   <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>',
      '</div>'
    ].join('')

    alertPlaceholderhome.value.append(wrapper)
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
// console.log(dataUser.value.id)

// watch(() => console.log(window.scrollY))
// let x = ref()
// watch(x, (newX) => {
//   window.scrollY
//   console.log(`x is ${newX}`)
// })

// // getter
// // watch(
// //   () => x.value + y.value,
// //   (sum) => {
// //     console.log(`sum of x + y is: ${sum}`)
// //   }
// // )
// const { y } = useWindowScroll()

// onMounted(
//   Window.addEventListener('scroll', function () {
//     console.log('scroll')
//   })
// )

// const data = ref(Array.from(Array(50).keys(), () => 'Lorem ipsum'))
// const { list, containerProps, wrapperProps } = useVirtualList(data, {
//   itemHeight: 96
// })
// useInfiniteScroll(
//   containerProps.ref,
//   () => {
//     data.value.push(...Array.from(Array(10).keys, () => 'More Lorem Ipsum'))
//   },
//   { distance: 10 }
// )

// const token = localStorage.getItem('token')
</script>

<template>
  <main>
    <!-- Header -->
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
              @click="deletePostIsDone()"
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
              @click="editePostClick()"
              class="btn btn-primary"
              data-bs-dismiss="modal"
            >
              edite
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- MAIN CONTENT -->
    <div class="container" style="height: 2000px">
      <!-- POSTS -->
      <div class="d-flex justify-content-center mt-5">
        <div class="col-9">
          <div>
            <!-- POST -->
            <div
              class="card shadow"
              v-for="post of postsCard"
              :key="post.id"
              @click="postClicked = post.id"
            >
              <div class="card-header" @click="headerClicked(`${post.author.id}`)">
                <img
                  class="rounded-circle border border-2"
                  :src="post.author.profile_image"
                  alt=""
                  style="width: 40px; height: 40px"
                />
                <b>{{ post.author.username || '' }}</b>
              </div>
              <div class="edate" v-if="post.author.id === dataUser.id">
                <div
                  data-bs-toggle="modal"
                  data-bs-target="#edite-post"
                  @click="getIdOfPostForEdi(post)"
                >
                  Edite
                </div>
              </div>
              <div class="delet" v-if="post.author.id === dataUser.id">
                <div
                  data-bs-toggle="modal"
                  data-bs-target="#delete-post"
                  @click="getIdOfPostForDel(post)"
                >
                  Delete
                </div>
              </div>
              <div class="card-body" @click="postfukeF(post.id)">
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
      <!--// POSTS //-->
    </div>

    <!-- alert -->
    <div
      ref="alertPlaceholderhome"
      id="success-alert"
      class="show fade"
      style="position: fixed; z-index: 999999; width: 30%; bottom: 0; right: 0"
    ></div>

    <!-- <div class="float my">y: {{ y }}</div>
    <div class="float my" ref="x">y: {{ newX }}</div>

    <div v-bind="containerProps" class="h-screen p-2 rounded">
      <div v-bind="wrapperProps" class="max-w-sm mx-auto">
        <div
          v-for="{ index, data } in list"
          :key="index"
          class="rounded-lg h-[80px] flex flex-col px-4 justify-center bg-neutal-800 mb-4 border-neutral-600"
        >
          <h2 class="mb-2 text-2xl">Item #{{ index }}</h2>
          <p class="text-sm">{{ data }}</p>
        </div>
      </div>
    </div> -->
  </main>
</template>

<style scoped lang="scss">
.card-header {
  cursor: pointer;
}
.my {
  height: 70px;
  width: 70px;
  right: 27%;
  bottom: 5%;
  position: fixed;
}

.delet-style-f {
  .delet-style {
    display: flex;
  }
}

.edate {
  height: 4%;
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
    align-items: center;
    justify-content: center;
  }
}

.delet {
  height: 4%;
  background-color: rgba(249, 28, 28, 0.54);
  position: absolute;
  top: 10px;
  right: 99px;
  padding: 10px 20px 20px;
  border-radius: 10px;
  cursor: pointer;
  div {
    display: flex;
    align-items: center;
    justify-content: center;
    color: #000;
    font-weight: bold;
    font-style: italic;
  }
}
</style>

<style scoped>
.showLogin {
  visibility: hidden;
}
.showReg {
  visibility: hidden;
}
.no {
  visibility: visible;
}
body {
  background: #f0ecff;
}

.card {
  margin-top: 30px;
}

/* ========== LOADER STYLES ========= */
</style>
