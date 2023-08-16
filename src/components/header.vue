<!-- eslint-disable vue/multi-word-component-names -->
<script setup>
import axios from 'axios'
import { ref, onMounted } from 'vue'
import { RouterLink } from 'vue-router'

onMounted(() => {
  setupUI()
})

const link = 'https://tarmeezacademy.com/api/v1'

import { useRouter } from 'vue-router'
const router = useRouter()
function ProfileClicked() {
  // alert(postId)
  router.push(`/profile?postId=2262`)
}

let passwordInput = 'yarob99ssgk'
let usernameInput = '123456555&&'
let nameLoginInput = 'Ziad .M'
let imgLoginInput = ref()
function logIn() {
  // console.log(passwordInput.value)
  //yarob99ssgk
  // console.log(usernameInput.value)
  //123456555&&

  // let params = {
  //   username: passwordInput.value,
  //   password: usernameInput.value,
  //   name: nameLoginInput.value
  // }

  let formData = new FormData()
  formData.append('username', passwordInput)
  formData.append('password', usernameInput)
  formData.append('name', nameLoginInput)
  formData.append('image', imgLoginInput.value.files[0])

  let header = {
    'content-Type': 'multipart/form-data'
  }

  axios
    .post(`${link}/login`, formData, {
      headers: header
    })
    .then((response) => {
      // console.log(response.data)
      localStorage.setItem('token', response.data.token)
      localStorage.setItem('user', JSON.stringify(response.data.user))
      setupUI()
      showAlert('LOGIN Success', 'success')
      // console.log(response)
    })
    .catch((error) => {
      const massage = error.response.data.message
      showAlert(massage, 'danger')
    })
}

let passwordreg = 'yarob99df'
let usernamereg = '123456555xc'
let regnameInput = 'Yarob'
let regImgInput = ref()
function regClick() {
  let formData = new FormData()
  formData.append('username', passwordreg)
  formData.append('password', usernamereg)
  formData.append('name', regnameInput)
  formData.append('image', regImgInput.value.files[0])

  let header = {
    'content-Type': 'multipart/form-data'
  }
  axios
    .post(`${link}/register`, formData, {
      headers: header
    })
    .then((response) => {
      console.log(response.data)
      localStorage.setItem('token', response.data.token)
      localStorage.setItem('user', JSON.stringify(response.data.user))
      setupUI()
      showAlert('REGISTER Success', 'success')
    })
    .catch((error) => {
      const massage = error.response.data.message
      showAlert(massage, 'danger')
    })
}

function logOut() {
  localStorage.removeItem('token')
  localStorage.removeItem('user')
  setupUI()
  showAlert('LogOut is Done', 'danger')
}

const postTitle = ref()
const postBody = ref()
const postImage = ref()
function createPost() {
  const token = localStorage.getItem('token')

  let formData = new FormData()
  formData.append('body', postBody.value.value)
  formData.append('title', postTitle.value.value)
  formData.append('image', postImage.value.files[0])

  let header = {
    'Content-Type': 'multipart/form-data',
    authorization: `Bearer ${token}`
  }

  axios
    .post(`${link}/posts`, formData, {
      headers: header
    })
    .then(() => {
      // localStorage.setItem('token', response.data.token)
      // localStorage.setItem('user', JSON.stringify(response.data.user))
      showAlert('You Jast Create a Post', 'success')
      window.location.reload()
    })
    .catch((error) => {
      const massage = error.response.data.message
      // window.location.reload()
      showAlert(massage, 'danger')
    })
}

const addBtn = ref()
const loginDiv = ref()
const logoutDiv = ref()
let dataUser = ref()
function setupUI() {
  const token = localStorage.getItem('token')

  if (token == null) {
    // user is guest (not logged in)
    if (addBtn.value != null) {
      addBtn.value.style.setProperty('display', 'none', 'important')
    }
    loginDiv.value.style.setProperty('display', 'flex', 'important')
    logoutDiv.value.style.setProperty('display', 'none', 'important')
  } else {
    // for logged in user
    if (addBtn.value != null) {
      addBtn.value.style.setProperty('display', 'block', 'important')
    }
    loginDiv.value.style.setProperty('display', 'none', 'important')
    logoutDiv.value.style.setProperty('display', 'flex', 'important')
    const storageUser = localStorage.getItem('user')
    let getCurrentUser = JSON.parse(storageUser)

    dataUser.value = getCurrentUser
    console.log(dataUser.value)
    // console.log(dataUser.value.profile_image[0])
    // console.log(dataUser.value.username)
  }
}

const alertPlaceholder = ref()
function showAlert(customMessage, type = 'success') {
  const alert = (message, type) => {
    const wrapper = document.createElement('div')
    wrapper.innerHTML = [
      `<div class="alert alert-${type} alert-dismissible" role="alert">`,
      `   <div>${message}</div>`,
      '   <button type="button" class="btn-close" data-bs-dismiss="alert" aria-label="Close"></button>',
      '</div>'
    ].join('')

    alertPlaceholder.value.append(wrapper)
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
</script>

<template>
  <main>
    <!-- NAVIGATION BAR CONT-->
    <div class="container">
      <div class="d-flex justify-content-center">
        <div class="col-9">
          <nav class="navbar navbar-expand-lg bg-light shadow rounded pt-3">
            <div class="container-fluid">
              <RouterLink to="/" class="navbar-brand">Tarmeez</RouterLink>
              <button
                class="navbar-toggler"
                type="button"
                data-bs-toggle="collapse"
                data-bs-target="#navbarNav"
                aria-controls="navbarNav"
                aria-expanded="false"
                aria-label="Toggle navigation"
              >
                <span class="navbar-toggler-icon"></span>
              </button>
              <div class="collapse navbar-collapse" id="navbarNav">
                <ul class="navbar-nav">
                  <li class="nav-item">
                    <RouterLink to="/" class="nav-link active" aria-current="page">Home</RouterLink>
                  </li>
                  <li class="nav-item">
                    <div @click="ProfileClicked()" class="nav-link" style="cursor: pointer">
                      Profile
                    </div>
                  </li>
                </ul>
              </div>

              <!-- NON-LOGGED IN USER -->
              <div class="d-flex w-100 justify-content-end">
                <div class="loginDiv" ref="loginDiv">
                  <button
                    type="button"
                    data-bs-toggle="modal"
                    data-bs-target="#login-modal"
                    class="btn btn-outline-success mx-2"
                  >
                    Login
                  </button>
                  <button
                    type="button"
                    data-bs-toggle="modal"
                    data-bs-target="#register-modal"
                    class="btn btn-outline-success"
                  >
                    Register
                  </button>
                </div>
                <div ref="logoutDiv" class="d-flex w-100 justify-content-end align-items-center">
                  <!-- <img
                    class="rounded-circle"
                    :src="dataUser.profile_image[0]"
                    style="width: 40px; height: 40px"
                    alt=""
                  /> -->
                  <!-- <b v-text="dataUser.value.name"></b> -->
                  <button
                    @click="logOut()"
                    id="logout-btn"
                    type="button"
                    data-bs-toggle="modal"
                    class="btn btn-outline-danger mx-2"
                  >
                    Logout
                  </button>
                </div>
              </div>
            </div>
          </nav>
        </div>
      </div>
    </div>

    <!-- ======= Add Post Button ======= -->
    <div
      ref="addBtn"
      class="bg-primary add-btn"
      data-bs-toggle="modal"
      data-bs-target="#creat-post-modal"
    >
      <span>+</span>
    </div>

    <!-- ======= Add Post Model ======= -->
    <div
      class="modal fade"
      id="creat-post-modal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="post-modal-title">Create A New Post</h1>
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
                <label for="recipient-name" class="col-form-label">Title</label>
                <input type="text" class="form-control" ref="postTitle" />
              </div>

              <div class="mb-3">
                <textarea
                  ref="postBody"
                  style="
                    width: 100%;
                    height: 100px;
                    border-color: gray;
                    border-radius: 10px;
                    resize: none;
                  "
                >
                </textarea>
              </div>

              <div class="mb-3">
                <label for="recipient-name" class="col-form-label">Image</label>
                <input type="file" class="form-control" ref="postImage" />
              </div>
            </form>
          </div>

          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button
              ref="postSubmitBtn"
              type="button"
              data-bs-dismiss="modal"
              class="btn btn-primary"
              @click="createPost()"
            >
              Create
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- LOGIN MODAL -->
    <div
      class="modal fade"
      id="login-modal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Login</h1>
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
                <label for="recipient-name" class="col-form-label">Username:</label>
                <input type="text" class="form-control" v-model="usernameInput" />
              </div>
              <div class="mb-3">
                <label for="message-text" class="col-form-label">Password:</label>
                <input type="password" class="form-control" v-model="passwordInput" />
              </div>
              <div class="mb-3">
                <label for="message-text" class="col-form-label">Name:</label>
                <input type="text" class="form-control" v-model="nameLoginInput" />
              </div>
              <div class="mb-3">
                <label for="message-text" class="col-form-label">Image:</label>
                <input type="file" class="form-control" ref="imgLoginInput" />
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button type="button" class="btn btn-primary" @click="logIn()" data-bs-dismiss="modal">
              Login
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- REGISTER MODAL -->
    <div
      class="modal fade"
      id="register-modal"
      tabindex="-1"
      aria-labelledby="exampleModalLabel"
      aria-hidden="true"
    >
      <div class="modal-dialog">
        <div class="modal-content">
          <div class="modal-header">
            <h1 class="modal-title fs-5" id="exampleModalLabel">Register A New User</h1>
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
                <label for="recipient-name" class="col-form-label">Profile Image:</label>
                <input type="file" class="form-control" ref="regImgInput" />
              </div>

              <div class="mb-3">
                <label for="recipient-name" class="col-form-label">Name:</label>
                <input v-model="regnameInput" type="text" class="form-control" />
              </div>

              <div class="mb-3">
                <label for="recipient-name" class="col-form-label">Username:</label>
                <input v-model="usernamereg" type="text" class="form-control" />
              </div>
              <div class="mb-3">
                <label for="message-text" class="col-form-label">Password:</label>
                <input v-model="passwordreg" type="password" class="form-control" />
              </div>
            </form>
          </div>
          <div class="modal-footer">
            <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Close</button>
            <button
              type="button"
              class="btn btn-primary"
              @click="regClick()"
              data-bs-dismiss="modal"
            >
              Register
            </button>
          </div>
        </div>
      </div>
    </div>

    <!-- alert -->
    <div
      ref="alertPlaceholder"
      id="success-alert"
      class="show fade"
      style="position: fixed; z-index: 999999; width: 30%; bottom: 0; right: 0"
    ></div>
  </main>
</template>
<style scoped lang="scss">
.add-btn {
  height: 70px;
  width: 70px;
  right: 7%;
  bottom: 5%;
  font-size: 50px;
  position: fixed;
  color: white;
  border-radius: 100px;
  cursor: pointer;
  box-shadow: 0px 10px 10px rgba(0, 0, 0, 0.3);
  span {
    display: flex;
    justify-content: center;
    transform: translate(0px, -7px);
  }
}
</style>
