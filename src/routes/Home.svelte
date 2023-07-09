<script>
  import fastapi from "../lib/api"
  import { link } from "svelte-spa-router"
  import moment from "moment";
  moment.locale("ko")

  let list_qna = []
  let list_community = []

  function get_list_qna() {
    fastapi("get", "/api/board/list/qna", {}, (json) => {
      list_qna = json.post_list
    })
  }

  function get_list_community() {
    fastapi("get", "/api/board/list/community", {}, (json) => {
      list_community = json.post_list
    })
  }

  get_list_qna()
  get_list_community()
</script>

<div class="container my-3">
  <table class="table">
    <thead>
      <tr class="table-dark">
        <th>제목</th>
        <th>작성일시</th>
        <th>작성자</th>
      </tr>
    </thead>
    <tbody>
      {#each list_qna as post, i}
        <tr>
          <td>
            <a use:link href="/post/{post.post.id}">{post.content.subject}</a>
            {#if post.count_comment != null}
              댓글: <span class="text-danger small mx-2">{post.count_comment}</span>
            {/if}
          </td>
          <td>{moment(post.post.date_create).format("YYYY년 MM월 DD일 hh:mm a")}</td>
          <td>{post.user.username}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>

<div class="container my-3">
  <table class="table">
    <thead>
      <tr class="table-dark">
        <th>제목</th>
        <th>작성일시</th>
        <th>작성자</th>
      </tr>
    </thead>
    <tbody>
      {#each list_community as post, i}
        <tr>
          <td>
            <a use:link href="/post/{post.post.id}">{post.content.subject}</a>
            {#if post.count_comment != null}
              댓글: <span class="text-danger small mx-2">{post.count_comment}</span>
            {/if}
          </td>
          <td>{moment(post.post.date_create).format("YYYY년 MM월 DD일 hh:mm a")}</td>
          <td>{post.user.username}</td>
        </tr>
      {/each}
    </tbody>
  </table>
</div>