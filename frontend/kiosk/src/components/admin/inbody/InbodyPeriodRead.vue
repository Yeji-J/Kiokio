<template>
  <div>
    <InbodyHeader
      @search-by-class="searchByClass"
      @search-by-name="searchByName"
    />

    <div v-if="inbodyStudents">
      <div style="text-align: left">
        * 이름 클릭 시, 학생의 전체 기록으로 이동합니다.
      </div>
    </div>
    <div id="admin-scroll-box" style="height: 55vh">
      <table style="margin-bottom: 0.5vh">
        <InbodyPeriodLabel />
        <InbodyPeriodItem
          v-for="(student, index) in inbodyStudents"
          :key="student.id"
          :student="student"
          :index="index"
          @change-mode-student="changeModeStudent"
          @change-mode-detail="changeModeDetail"
        />
      </table>
    </div>
  </div>
</template>

<script>
import InbodyHeader from '@/components/admin/inbody/InbodyHeader.vue'
import InbodyPeriodLabel from '@/components/admin/inbody/InbodyPeriodLabel.vue'
import InbodyPeriodItem from '@/components/admin/inbody/InbodyPeriodItem.vue'

export default {
  name: 'InbodyPeriodRead',
  components: {
    InbodyHeader,
    InbodyPeriodLabel,
    InbodyPeriodItem,
  },
  ata() {
    return {
      student: null,
      inbody: null,
      studentIndex: null,
      inbodyIndex: null,
    }
  },
  computed: {
    inbodyStudents() {
      return this.$store.state.inbodyStudents
    },
    query() {
      return this.$store.state.query
    },
  },
  methods: {
    // Read
    searchByClass(startDate, endDate, grade, room) {
      if (!startDate) {
        startDate = '2022-01-01'
      }
      if (!endDate) {
        const date = new Date()
        const year = date.getFullYear()
        const month = ('0' + (1 + date.getMonth())).slice(-2)
        const day = ('0' + date.getDate()).slice(-2)
        endDate = year + '-' + month + '-' + day
      }
      const url = `students/${startDate}/${endDate}/${grade}/${room}/inbody/admin/`
      this.$store.dispatch('getInbodyStudents', url)

      this.$store.commit('SAVE_QUERY', {
        startDate,
        endDate,
        grade,
        room,
      })

      this.$router.push({ name: 'inbodyPeriod', query: this.query })
    },
    searchByName(startDate, endDate, name) {
      if (!startDate) {
        startDate = '2022-01-01'
      }
      if (!endDate) {
        const date = new Date()
        const year = date.getFullYear()
        const month = ('0' + (1 + date.getMonth())).slice(-2)
        const day = ('0' + date.getDate()).slice(-2)
        endDate = year + '-' + month + '-' + day
      }
      const url = `students/${startDate}/${endDate}/${name}/inbody/admin/`
      this.$store.dispatch('getInbodyStudents', url)

      this.$store.commit('SAVE_QUERY', {
        startDate,
        endDate,
        name,
      })

      this.$router.push({ name: 'inbodyPeriod', query: this.query })
    },
    //change mode
    changeModeDefault() {
      this.mode = 'Default'
    },
    changeModeStudent(studentIndex) {
      this.mode = 'Student'
      this.studentIndex = studentIndex
      this.student = this.inbodyStudents[studentIndex]
    },
    changeModeDetail(studentIndex, inbodyIndex) {
      this.mode = 'Detail'
      this.studentIndex = studentIndex
      this.inbodyIndex = inbodyIndex
      this.student = {
        name: this.inbodyStudents[studentIndex].name,
        grade: this.inbodyStudents[studentIndex].grade,
        room: this.inbodyStudents[studentIndex].room,
        number: this.inbodyStudents[studentIndex].number,
        gender: this.inbodyStudents[studentIndex].gender,
      }
      this.inbody = this.inbodyStudents[studentIndex].inbody_set[inbodyIndex]
    },
  },
}
</script>

<style></style>
