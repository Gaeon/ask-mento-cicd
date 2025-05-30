<template>
  <v-container class="pt-0">
    <v-row align="start" justify="center">
      <v-col cols="12" sm="10" md="8" lg="6" class="text-center">
        <v-img
          src="../assets/logo.png"
          max-width="200"
          class="mx-auto mb-4"
        ></v-img>
        
        
        <div class="mt-2">
          <img src="../assets/학사모.png" style="width: 20%; height: auto; margin-top:10%">
          <h1 class="text-h3 mb-2">Ask anything!</h1>
          <p class="text-body-1 mb-8">당신의 회사 멘토들에게 무엇이든 물어보세요.</p>
        </div>
        
        
        <v-form @submit.prevent="handleSubmit" class="mb-16">
          <v-text-field
            v-model="question"
            label="질문을 입력하세요"
            @keyup.enter="handleSearch"
            variant="outlined"
            hide-details
            single-line
            class="search-field mb-4"
            autofocus
          >
            <template v-slot:prepend-inner>
              <v-icon>mdi-magnify</v-icon>
            </template>
          </v-text-field>
          
          <div class="d-flex justify-center">
            <v-btn
              color="primary"
              class="mr-2"
              @click="handleSearch"
              :loading="loading"
            >
              Ask Question
            </v-btn>
            <v-btn
              variant="outlined"
              @click="clearQuestion"
            >
              Clear
            </v-btn>
          </div>
        </v-form>

        <v-card
          v-if="recentQuestions.length > 0"
          class="mx-auto"
          max-width="600"
          variant="outlined"
        >
          <v-card-title class="text-body-1 py-2">Recent Questions</v-card-title>
          <v-list lines="one">
            <v-list-item
              v-for="q in recentQuestions.slice(0, 3)"
              :key="q.id"
              :title="q.title"
              :subtitle="new Date(q.timestamp).toLocaleDateString()"
              @click="router.push(`/history`)"
            ></v-list-item>
          </v-list>
          <v-card-actions>
            <v-btn
              variant="text"
              color="primary"
              block
              @click="router.push('/history')"
            >
              View All Questions
            </v-btn>
          </v-card-actions>
        </v-card>
      </v-col>
    </v-row>
  </v-container>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import SimilarQuestions from '../components/questions/SimilarQuestions.vue'
import axios from 'axios'

const router = useRouter()
const similarQuestionsDialog = ref(null)
const question = ref('')
const loading = ref(false)
const recentQuestions = ref([])

const checkSimilarQuestions = async () => {
  if (!question.value.trim()) return
  
  loading.value = true
  try {
    // TODO: API call to check similar questions
    await new Promise(resolve => setTimeout(resolve, 1000))
    similarQuestionsDialog.value.showDialog()
  } catch (error) {
    console.error('Error checking similar questions:', error)
  } finally {
    loading.value = false
  }
}

const useExistingAnswer = async (selectedQuestion) => {
  try {
    // DB에 질문-답변 쌍 저장
    await axios.post('/api/questions/reuse', { 
      originalQuestionId: selectedQuestion.id,
      newQuestion: question.value
    })
    question.value = ''
    // 성공 메시지 표시
  } catch (error) {
    console.error('Error reusing answer:', error)
  }
}

const submitNewQuestion = async () => {
  try {
    // AI Agent에 새로운 질문 전송
    const response = await axios.post('/api/questions/new', {
      question: question.value
    })
    question.value = ''
    // 답변 표시 또는 다른 페이지로 리다이렉트
  } catch (error) {
    console.error('Error submitting question:', error)
  }
}

const handleSubmit = async () => {
  if (question.value.trim()) {
    // 질문 저장
    localStorage.setItem('currentQuestion', question.value);
    
    // 유사 질문 페이지로 이동 (멘토 추천 과정은 아직 실행하지 않음)
    router.push({
      path: '/similar-questions',
      query: { question: question.value }
    });
  }
}

const handleSearch = async () => {
  if (question.value.trim()) {
    loading.value = true;
    try {
      const response = await axios.post('/api/questions/search', {
        question: question.value
      });
      
      // 유사 질문 페이지로 이동하면서 결과 전달
      router.push({
        path: '/similar-questions',
        query: { 
          question: question.value,
          similarQuestions: JSON.stringify(response.data.map(item => ({
            question: item.question,
            answer: item.answers,
            similarity: Math.round(parseFloat(item.similarity_score) * 100)
          })))
        }
      });
    } catch (error) {
      console.error('유사 질문 검색 중 오류 발생:', error);
      alert('유사 질문 검색 중 오류가 발생했습니다.');
    } finally {
      loading.value = false;
    }
  }
}

const clearQuestion = () => {
  question.value = ''
}
</script>

<style scoped>
.search-field :deep(.v-field) {
  border-radius: 10px !important;  /* Increased border radius */
  padding: 8px 16px;
  box-shadow: 0 2px 5px 1px rgba(64,60,67,.16);
}

.search-field :deep(.v-field:hover) {
  box-shadow: 0 2px 8px 1px rgba(64,60,67,.24);
}
</style>
