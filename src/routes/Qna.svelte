<script>
  import fastapi from "../lib/api"
  import { link } from "svelte-spa-router"
  import { page } from "../lib/store";
  import moment from "moment";
  moment.locale("ko")

  let list_post = []
  let size = 10
  let total = 0
  $: total_page = Math.ceil(total/size)

  function get_list_post(_page) {
    let params = {
      page: _page,
      size: size,
    }
    fastapi("get", "/api/board/list/qna", params, (json) => {
      list_post = json.post_list
      $page = _page
      total = json.total
    })
  }

  $: get_list_post($page)
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
          <td>{total - ($page * size) - i}</td>
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

  <!-- pagination start -->
  <ul class="pagination justify-content-center">
    <!-- first page -->
    <li class="page-item {$page <= 0 && "disabled"}">
      <button class="page-link" on:click="{() => get_list_post(0)}">처음</button>
    </li>
    <!-- previous page -->
    <li class="page-item {$page <= 0 && "disabled"}">
      <button class="page-link" on:click="{() => get_list_post($page-1)}">이전</button>
    </li>
    <!-- page num -->
    {#each Array(total_page) as _, loop_page}
      {#if loop_page >= $page-5 && loop_page <= $page+5}
      <li class="page-item {loop_page === $page && "active"}">
        <button on:click="{() => get_list_post(loop_page)}" class="page-link">{loop_page+1}</button>
      </li>
      {/if}
    {/each}
    <!-- next page -->
    <li class="page-item {$page >= total_page-1 && "disabled"}">
      <button class="page-link" on:click="{() => get_list_post($page+1)}">다음</button>
    </li>
    <li class="page-item {$page >= total_page-1 && "disabled"}">
      <button class="page-link" on:click="{() => get_list_post(total_page-1)}">끝</button>
    </li>
  </ul>
  <!-- pagination end -->

  <a use:link href="/question-create" class="btn btn-primary">질문 등록하기</a>
</div>