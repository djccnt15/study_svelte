<script>
  import fastapi from "../lib/api"

  let question_list = []

  function get_question_list() {
    fastapi('get', '/api/question/list', {}, (json) => {
      question_list = json
    })
  }

  get_question_list()
</script>

<ul>
  {#each question_list as question}
    <li>{question.subject} <br> {question.content} {question.user.username} {question.date_create}</li>
     <ul>
        {#each question.answers as answers}
          <li>{answers.content} {answers.user.username} {answers.date_create}</li>
        {/each}
     </ul>
  {/each}
</ul>