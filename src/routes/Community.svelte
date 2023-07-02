<script>
  import fastapi from "../lib/api"
  import { link } from "svelte-spa-router"

  let list_post = []


  function get_list_post() {
    fastapi("get", "/api/board/list/community", {}, (json) => {
      list_post = json.post_list
    })
  }


  get_list_post()
</script>

<div class="container my-3">
  <table class="table">
    <thead>
      <tr class="table-dark">
        <th>번호</th>
        <th>제목</th>
        <th>작성일시</th>
        <th>작성자</th>
      </tr>
    </thead>
    <tbody>
      {#each list_post as post, i}
        <tr>
          <td>{i+1}</td>
          <td>
            <a use:link href="/post/{post.post.id}">{post.content.subject}</a>
          </td>
          <td>{post.post.date_create}</td>
          <td>{post.user.username}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>