<script>
  import fastapi from '../lib/api'
  import { link } from 'svelte-spa-router'

  let list_qna = []
  let list_community = []


  function get_list_qna() {
    fastapi('get', '/api/board/list/qna', {}, (json) => {
      list_qna = json.post_list
    })
  }


  function get_list_community() {
    fastapi('get', '/api/board/list/community', {}, (json) => {
      list_community = json.post_list
    })
  }

  get_list_qna()
  get_list_community()
</script>

<ul>
  {#each list_qna as post}
    <li>
      {post.Category.category}
      <a use:link href="/post/{post.Post.id}">{post.Content.subject}</a><br>
      {post.Content.content}
      {post.User.username}
      {post.Post.date_create}
    </li>
  {/each}
</ul>

<ul>
  {#each list_community as post}
    <li>
      {post.Category.category}
      <a use:link href="/post/{post.Post.id}">{post.Content.subject}</a><br>
      {post.Content.content}
      {post.User.username}
      {post.Post.date_create}
    </li>
  {/each}
</ul>