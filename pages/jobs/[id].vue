<template>
  ID：{{ paramId }}

  <ul v-if="currentId">
    <li v-for="q in questionsData" :key="q.id" style="list-style-type: none">
      <div v-if="q.id === currentId">
        <div>
          {{ q.title }}
          <br />
          {{ q.description }}
        </div>
        <textarea v-model="q.answer"> </textarea>
        <button v-if="currentId !== questionsData[0].id" @click="toBack(q.id)">前へ</button>
        <button @click="toNext(q.id)">次へ</button>
        <hr />
      </div>
    </li>
  </ul>
</template>

<script lang="ts">
interface Question {
  cover: string
  createdAt: Date
  description: string
  id: string
  title: string
}

interface QuestionWithAnswer extends Question {
  answer: string | string[]
}

export default {
  async setup() {
    const route = useRoute()
    const paramId = ref(route.params.id)
    const currentId = ref<string>("")
    const questionsData = ref<QuestionWithAnswer[]>([])
    const toNext = (id: string) => {
      // TODO 最後のidがcurrentの場合を考慮
      console.log(`onClick Next!!${id}`)
      const qurrentQuestion = questionsData.value.find((q) => q.id === id)
      console.log(`qurrentQuestionAnswer: ${qurrentQuestion?.answer}`)
      const index = questionsData.value.findIndex((q) => q.id === currentId.value)
      const nextQuestion = questionsData.value[index + 1]
      currentId.value = nextQuestion.id
    }

    const toBack = (id: string) => {
      // TODO 最初のidがcurrentの場合を考慮
      console.log(`onClick Back!!${id}`)
      const index = questionsData.value.findIndex((q) => q.id === currentId.value)
      const nextQuestion = questionsData.value[index - 1]
      currentId.value = nextQuestion.id
    }

    const { data: questions } = await useFetch<Question[]>(
      `https://62fe320941165d66bfbabe7e.mockapi.io/apt/v1/jobs/`,
      {
        server: false,
      }
    )

    if (questions.value) {
      currentId.value = questions.value[0].id
    }
    questionsData.value = questions.value
      ? questions.value.map((q) => {
          return {
            ...q,
            answer: "",
          }
        })
      : []

    return {
      paramId,
      questionsData,
      currentId,
      toNext,
      toBack,
    }
  },
}
</script>
