<template>
  <div class="section">
    <div class="container">
      <nuxt-link to="/" class="button is-info is-small is-pulled-right">Back to Search Page</nuxt-link>
      <label class="label">Search Result : </label>
      <div class="box" v-if="users.id > 0">
        <article class="media">
          <div class="media-left">
            <figure class="image is-128x128">
              <img class="is-rounded" :src=" users.avatar_url ">
            </figure>
          </div>
          <div class="media-content">
            <div class="content">
              <div>
                <strong>{{users.name}}</strong> <br>
                <small>@{{users.login}}</small>
                <p class="is-marginless"> Location: {{users.location}}</p>
                <p class="is-marginless"> Follower: {{users.followers}}</p>
                <p class="is-marginless"> Public Repos: {{users.public_repos}}</p>
              </div>
            </div>
          </div>
        </article>
      </div>
      <div v-else>
        username doesn't exist
      </div>
      <table v-if="repos.length > 0" class="table is-striped is-narrow is-hoverable is-fullwidth">
        <tr>
          <th>No.</th>
          <th>Repo Name</th>
          <th>Forked</th>
          <th>Issue</th>
          <th>Action</th>
        </tr>
        <tr v-for="(repo, key) in repos" :key="key">
          <td> {{key+1}} </td>
          <td> {{repo.name}}</td>
          <td> {{repo.forks}}</td>
          <td> {{repo.open_issues}}</td>
          <td>
              <a :href="'https://raw.githubusercontent.com/'+ users.login + '/' + repo.name + '/master/README.md'"
              class="button is-primary is-small" target='_blank'>Readme</a>
          </td>
        </tr>
      </table>
      <div v-else>
        No repos yet
      </div>
    </div>
    <div v-if="loadedUser == false || loadedRepo == false">
      <loading />
    </div>
  </div>
</template>
<script>
  import loading from '../components/loading.vue';
  export default {
    components: {
      loading,
    },
    data() {
      return {
        users: '',
        repos: '',
        username_query: this.$route.query.username,
        loadedUser: false,
        loadedRepo: false,
      }
    },
    mounted() {
      this.doLoading();
      if (this.username_query != null) {
        this.getData();
      }
    },
    methods: {
      getData() {
        this.username = this.username_query;
        this.$axios.get('https://api.github.com/users/' + this.username)
          .then(response => {
            this.users = response.data;
            this.loadedUser = true;
          })
          .catch(e => {
            console.log(e)
          });

        this.$axios.get('https://api.github.com/users/' + this.username + '/repos?per_page=100')
          .then(response => {
            this.repos = response.data;
            this.loadedRepo = true;
          })
          .catch(e => {
            console.log(e)
          });

        this.doLoading();
      },

      doLoading() {
        this.loadedUser = false;
        this.loadedRepo = false;
        setTimeout(() => {
          this.loadedUser = true;
          this.loadedRepo = true;
        }, 1000)
      },
    },
  }

</script>
