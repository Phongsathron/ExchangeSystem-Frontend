<template>
  <div id="feedback-list">
    <div class="header">
      <b-container>
        <h1>รายงานปัญหาการใช้งาน</h1>
      </b-container>
    </div>
    <div class="wrapper-container">
        <b-container>
            <div class="feedback-card" v-for="feedback in feedbacks" :key="feedback.id">
                <h3>{{ feedback.title }}</h3>
                <p>{{ feedback.detail }}</p>
                <footer class="blockquote-footer">วันที่สร้าง {{ feedback.created_date | moment("calendar") }}</footer>
            </div>
        </b-container>
    </div>
  </div>
</template>

<script>
export default {
  data () {
    return {
      feedbacks: []
    }
  },
  mounted () {
    this.loadFeedback()
  },
  methods: {
    async loadFeedback () {
      let response = await this.$axios.get('/feedback/')
      this.feedbacks = response.data.results
    }
  }
}
</script>

<style lang="scss">
.feedback-card{
    border-bottom: 1px solid #e0e0e0;
    padding: 20px;
}
</style>
