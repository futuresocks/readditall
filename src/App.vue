<template>
  <div id="app">
    <h1>readditor</h1>
    <input v-model="subreddit" type="text" name="subreddit">
    <button v-on:click="getPosts"type="button" name="button"></button>
    <div id="feeds">
      <div v-for="(posts, sub) in posts" class="feed">
        <h1>r/{{sub}} <button v-on:click="update(sub)">Refresh</button></h1>
        <ul>
          <li v-for="post in posts"><strong>{{post.title}}</strong><button v-on:click="selectPost(post)">Read This</button></li>
        </ul>
      </div>
    </div>
  </div>
</template>

<script>

export default {
  name: 'app',
  data(){
    return {
      subreddit: "",
      posts: {},
      selectedPost: null
    }
  },
  methods: {
    update: function(sub){
      this.subreddit = sub
      this.getPosts()
    },
    selectPost: function(post){
      this.selectedPost = post
    },
    getPosts: function(){
      fetch(`https://www.reddit.com/r/${this.subreddit}.json`)
      .then(res => res.json())
      .then(posts => {
        const customPosts = posts.data.children.map(post => {
          return {
            title: post.data.title,
            text: post.data.selftext,
            user: post.data.author,
            url: post.data.url
          }
        })
        this.posts[this.subreddit] = customPosts
        this.subreddit = ""
      })
    }
  }
}
</script>

<style>
 #feeds {
   display: flex;
   flex-direction: wrap;
 }
</style>
