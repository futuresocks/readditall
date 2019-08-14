<template>
  <div id="app">
    <h1>readditor</h1>
    <input v-model="subreddit" type="text" name="subreddit">
    <button v-on:click="getPosts"type="button" name="button">Subscribe</button>
    <div id="feeds">
      <div v-for="(posts, sub) in posts" class="feed">
        <h1>r/{{sub}}</h1>
        <span><button v-on:click="update(sub)">Refresh</button><button v-on:click="unsubscribe(sub)">Unsubscribe</button></span>
        <div class="posts">
          <ul>
            <li v-for="post in posts"><strong>{{post.data.title}}</strong>  <button v-on:click="selectPost(post)">View</button></li>
          </ul>
        </div>
      </div>
    </div>
    <modal v-if="selectedPost" name="post-container">
      {{selectedPost.data.selftext}}
      <button @click="hide('post-container')"type="button" name="button">Close</button>
    </modal>
    <modal name="loading">
      Loading...
    </modal>
  </div>
</template>

<script>

export default {
  name: 'app',
  data(){
    return {
      loading: false,
      subreddit: "",
      subreddits: [],
      posts: {},
      selectedPost: null
    }
  },
  methods: {
    show (modalName) {
      this.$modal.show(modalName);
    },
    hide (modalName) {
      this.$modal.hide(modalName);
    },
    update: function(sub){
      this.subreddit = sub
      this.getPosts()
    },
    selectPost: function(post){
      this.selectedPost = post
      this.show('post-container')
    },
    unsubscribe: function(sub){
      this.$delete(this.posts, sub)
    },
    getPosts: function(){
      this.loading = true
      fetch(`https://www.reddit.com/r/${this.subreddit}.json`)
      .then(res => res.json())
      .then(posts => {
        this.posts[this.subreddit] = posts.data.children
        this.subreddits.push(this.subreddit)
        this.subreddit = ""
        this.loading = false
        //TODO: Error handling
      })
    }
  },
  computed: {
    isLoading: function(){
      this.loading ? this.show('loading') : this.hide('loading')
    }
  }
}
</script>

<style>
#feeds {
  display: flex;
  flex-direction: wrap;
}

.feed {
  padding: 20px;
}

.posts {
  overflow-y: scroll;
  height: 300px;
  width: 300px;
}

</style>
