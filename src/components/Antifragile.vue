<template>
  <div class="container">
    <b-card no-body>
      <b-tabs card>
        <b-tab title="Registrados" active>
          <b-card-text>
            <students-table />
          </b-card-text>
        </b-tab>
        <b-tab title="Registrar">
          <b-card-text>
            <students-form />
          </b-card-text>
        </b-tab>
      </b-tabs>
    </b-card>
  </div>
</template>

<script>
import StudentsForm from './widgets/StudentsForm.vue'
import StudentsTable from './widgets/StudentsTable.vue'
export default {
  components: { StudentsTable, StudentsForm },
  name: 'AntiFragile',
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
