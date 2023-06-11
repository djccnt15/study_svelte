<script>
  import fastapi from '../lib/api'
  export let params = {}
  let id = params.id
  let post = {}
  let content = {}
  let category = {}
  let user = {}
  let list_comment = []

  function get_post() {
    fastapi("get", "/api/board/post", {id}, (json) => {
      post = json.post.Post,
      content = json.post.Content,
      user = json.post.User,
      category = json.post.Category
      list_comment = json.comment
    })
  }

  get_post()
</script>

<h1>{content.subject}</h1>
{post.date_create}
{content.date_upd}
{user.username}
{category.category}
<div>
  <h2>댓글</h2>
  <ul>
    {#each list_comment as comment}
      <li>
        {comment.Content.content}
        {comment.Comment.date_create}
        {comment.Content.date_upd}
        {comment.User.username}
      </li>
    {/each}
  </ul>
</div>