<script>
  import fastapi from "../lib/api"
  import Error from "../components/Error.svelte"

  export let params = {}

  let id_post = params.id_post
  let post_detail = {
    post: {},
    content: {},
    category: {},
    user: {},
  }
  let list_comment = []
  let comment_content = ""
  let error = {detail:[]}


  function get_post() {
    fastapi("get", "/api/board/post/detail", {id_post}, (json) => {
      post_detail = json.post_detail,
      list_comment = json.comment_list
    })
  }


  get_post()


  function create_comment(event) {
    event.preventDefault()
    let url = "/api/board/comment/create?id_post=" + id_post
    let params = {
      content: comment_content
    }
    fastapi("post", url, params,
      (json) => {
        comment_content = ""
        let error = {detail:[]}
        get_post()
    },
      (err_json) => {
        error = err_json
      }
    )
  }
</script>

<h1>{post_detail.content.subject}</h1>
{post_detail.post.date_create}
{post_detail.content.date_upd}
{post_detail.user.username}
{post_detail.category.category}

<Error error={error} />
<form method="post">
  <textarea rows="15" bind:value={comment_content}></textarea>
  <input type="submit" value="댓글 등록" on:click="{create_comment}">
</form>

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