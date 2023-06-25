<script>
  import fastapi from "../lib/api"
  export let params = {}
  let id_post = params.id_post
  let post = {}
  let content = {}
  let category = {}
  let user = {}
  let list_comment = []

  function get_post() {
    fastapi("get", "/api/board/post/detail", {id_post}, (json) => {
      post = json.post_detail.post,
      content = json.post_detail.content,
      user = json.post_detail.user,
      category = json.post_detail.category
      list_comment = json.comment_list
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
        {comment.content.content}
        {comment.comment.date_create}
        {comment.content.date_upd}
        {comment.user.username}
      </li>
    {/each}
  </ul>
</div>