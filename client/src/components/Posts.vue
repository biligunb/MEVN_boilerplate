<template>
  <div class="posts">
    <h1>Posts</h1>
    <div v-if="posts.length > 0" class="table-wrap">
      <div>
        <router-link v-bind:to="{ name: 'addpost' }" class="">Add Post</router-link>
      </div>
      <div>
        <a href="#" @click="getAccessToken()">getAccessToken</a>
      </div>
      <table>
        <tr>
          <td>Title</td>
          <td width="550">Description</td>
          <td width="100" align="center">Action</td>
        </tr>
        <tr v-for="post in posts">
          <td>{{ post.title }}</td>
          <td>{{ post.description }}</td>
          <td align="center">
            <router-link v-bind:to="{ name: 'editpost', params: { id: post._id } }">Edit</router-link> |
            <a href="#" @click="deletePost(post._id)">Delete</a>
          </td>
        </tr>
      </table>
    </div>
    <div v-else> code {{this.code }}  <br />
    access-token {{this.access_token}}
    <br />

      <router-link v-bind:to="{ name: 'addpost' }" class="add_post_link">Add Post</router-link>
      <div id="getAccessToken">
      <!-- `greet` is the name of a method defined below -->
        <button v-on:click="getAccessToken">getAccessToken</button>
        <button v-on:click="getUserInfo">getUserInfo</button>
      </div>
    </div>
  </div>
</template>

<script>
import PostsService from '@/services/PostsService'
import axios from 'axios'
import Vue from 'vue'
const base = axios.create({
  baseURL: 'http://192.168.20.173:8080'
})

Vue.prototype.$http = base

export default {
  name: 'posts',
  data () {
    return {
      posts: [],
      code: '',
      access_token: '',
      request_params: {
        code: this.$route.fullPath.substring(7),
        redirect_uri: 'http://192.168.20.173:8080',
        client_id: '95_5vmtfb9d65k4o8ow4s8wsc0ckgk0c80c8c4c84ogoksw00cg00',
        client_secret: '1nj2rat4vysk404wsw0040cgc8owsc4ks4k448csk04c8ck80c',
        grant_type: 'authorization_code'
      }
    }
  },
  created: function () {
    console.log('created called.')
    this.code = this.$route.fullPath.substring(7)
    this.$http.get('https://webhook.site/96ae4c12-c12c-45c4-8238-0dd921b97d32').then((response) => {
      alert('gerelee')
    })
  .catch((e) => {
    console.error(e)
  })
  },
  mounted () {
    this.getPosts()
  },
  methods: {
    getAccessToken: function (event) {
    // alert(JSON.stringify(this.request_params))
      // this.$http.post('https://webhook.site/96ae4c12-c12c-45c4-8238-0dd921b97d32')
      this.$http.post('https://mgw.test.lending.mn/api/oauth/v2/token', {
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded'
        },
        body: JSON.stringify(this.request_params)
      })
        .then(response => {
          if (response.code === 0) {
            this.access_token = response.accessToken
            alert('Hello ' + this.accessToken + '!')
          } else {
            alert('failed')
          }
        })
      .catch(e => {
        this.errors.push(e)
      })
    },
    getUserInfo: function (event) {
      // alert(this.accessToken)
      this.$http.get('https://mgw.test.lending.mn/api/oauth/v2/token', {
        headers: {
          'Content-Type': 'application/x-www-form-urlencoded',
          'x-and-auth-token': this.accessToken
        }
      })
      .then(response => {
        if (response.code === 0) {
          this.access_token = response.accessToken
        //   "userId": "wPhXsPPsHteuk8kS5gf0hA==",
        // "firstName": "Ber-A-Ber",
        // "lastName": "Баярсайхан",
        // "phoneNumber": "80026446"
          alert('Hello ' + response + '!')
        } else {
          alert('failed')
        }
      })
    .catch(e => {
      this.errors.push(e)
    })
    },
    async getPosts () {
      const response = await PostsService.fetchPosts()
      this.posts = response.data.posts
    },
    async deletePost (id) {
      const $this = this
      $this.$swal({
        title: 'Are you sure?',
        text: "You won't be able to revert this!",
        type: 'warning',
        showCancelButton: true,
        confirmButtonColor: '#3085d6',
        cancelButtonColor: '#d33',
        confirmButtonText: 'Yes, delete it!'
      }).then(function () {
        PostsService.deletePost(id)
        $this.$router.go({
          path: '/'
        })
      })
    }
  }
}
</script>
<style type="text/css">
.table-wrap {
  width: 60%;
  margin: 0 auto;
  text-align: center;
}
table th, table tr {
  text-align: left;
}
table thead {
  background: #f2f2f2;
}
table tr td {
  padding: 10px;
}
table tr:nth-child(odd) {
  background: #f2f2f2;
}
table tr:nth-child(1) {
  background: #4d7ef7;
  color: #fff;
}
a {
  color: #4d7ef7;
  text-decoration: none;
}
a.add_post_link {
  background: #4d7ef7;
  color: #fff;
  padding: 10px 80px;
  text-transform: uppercase;
  font-size: 12px;
  font-weight: bold;
}
</style>
