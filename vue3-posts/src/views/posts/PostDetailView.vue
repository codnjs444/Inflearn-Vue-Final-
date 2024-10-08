<template>
  <AppLoading v-if="loading" />

  <AppError v-else-if="error" :message="error.message" />
  <div v-else>
    <h2>{{ post.title }}</h2>
    <p>{{ post.content }}</p>
    <p class="text-muted">
      {{ $dayjs(post.createdAt).format('YYYY.MM.DD HH:mm:ss') }}
    </p>
    <hr class="my-4" />
    <AppError v-if="removeError" :message="removeError.message" />
    <div class="row g-2">
      <div class="col-auto">
        <button class="btn btn-outline-dark">이전글</button>
      </div>
      <div class="col-auto">
        <button class="btn btn-outline-dark">다음글</button>
      </div>
      <div class="col-auto me-auto"></div>
      <div class="col-auto">
        <button class="btn btn-outline-dark" @click="goListPage">목록</button>
      </div>
      <div class="col-auto">
        <button class="btn btn-outline-primary" @click="goEditPage(post.id)">
          수정
        </button>
      </div>
      <div class="col-auto">
        <button
          class="btn btn-outline-danger"
          @click="remove"
          :disabled="removeLoading"
        >
          <template v-if="removeLoading">
            <span
              class="spinner-grow spinner-grow-sm"
              aria-hidden="true"
            ></span>
            <span class="visually-hidden" role="status">Loading...</span>
          </template>
          <template v-else>삭제</template>
        </button>
      </div>
    </div>
  </div>
</template>

<script setup>
import { useRouter } from 'vue-router'
import { useAlert } from '../../composables/alert'
import { useAxios } from '@/hooks/useAxios'

const props = defineProps({
  id: [String, Number]
})

const router = useRouter()
// const route = useRoute()
// const id = route.params.id

const { vAlert, vSuccess } = useAlert()

const { error, loading, data: post } = useAxios(`/posts/${props.id}`)

const {
  error: removeError,
  loading: removeLoading,
  execute
} = useAxios(
  `/posts/${props.id}`, // 백틱 사용으로 템플릿 리터럴 적용
  { method: 'DELETE' }, // HTTP 메서드는 대문자로 표기하는 것이 일반적입니다.
  {
    immediate: false,
    onSuccess: () => {
      vSuccess('삭제가 완료되었습니다.')
      router.push({ name: 'PostList' })
    },
    onError: (err) => {
      vAlert(err.message) // err를 제대로 받아서 사용
    }
  }
)

const remove = async () => {
  if (!confirm('삭제 하시겠습니까?')) {
    return
  }
  execute()
}

const goListPage = () => {
  router.push({
    name: 'PostList'
  })
}

const goEditPage = (id) => {
  router.push({
    name: 'PostEdit',
    params: { id }
  })
}
</script>

<style lang="scss" scoped></style>
