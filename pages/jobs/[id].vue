<template>
  ID：{{ paramId }}
  <ul>
    <li v-for="j in jobsData" :key="j.id">
      {{ j.title }}
    </li>
  </ul>
</template>

<script lang="ts">
interface Job {
  cover: string
  createdAt: Date
  description: string
  id: string
  title: string
}

export default {
  async setup() {
    const route = useRoute()
    const paramId = ref(route.params.id)
    const jobsData = ref<Job[]>([])

    const { data: jobs } = await useFetch<Job[]>(
      `https://62fe320941165d66bfbabe7e.mockapi.io/apt/v1/jobs/`,
      {
        server: false,
      }
    )

    jobsData.value = jobs.value || []

    return {
      paramId,
      jobsData,
    }
  },
}
</script>
