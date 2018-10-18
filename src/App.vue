<template>
  <v-app>
    <v-responsive class="table-responsive" height="100%">
      <v-card v-if="error"> {{ error }} </v-card>
      <v-container fluid fill-height>
        <v-flex xs12 sm6 offset-sm3>
          <v-card>
            <v-data-table
            v-if="csvData"
            :headers="headings"
            :items="csvData"
            hide-actions
            class="elevation-1"
          >
            <template slot="items" slot-scope="props">
              <td class="text-xs-right">{{ props.item.first_name }}</td>
              <td class="text-xs-right">{{ props.item.sur_name }}</td>
              <td class="text-xs-right">{{ props.item.issue_count }}</td>
              <td class="text-xs-right">{{ props.item.date_of_birth }}</td>
            </template>
            </v-data-table>
            <label class="text-reader">
              <input type="file" @change="loadCSVFromFile">
            </label>
          </v-card>
        </v-flex>
      </v-container>
    </v-responsive>
  </v-app>
</template>

<script>
import Papa from 'papaparse'

  export default {
    data () {
      return {
        headings: [],
        csvData: '',
        error: ''
      }
    },
      methods: {
        loadCSVFromFile(ev) {
          const file = ev.target.files[0];
          const reader = new FileReader();
          if (file.type === 'text/csv') {
            reader.onload = e => this.$emit(
              "load", 
              this.csvData = 
              Papa.parse(e.target.result, { header: true, delimiter: ';' }).data, 
              this.getHeaders(this.csvData));
              this.error = ''
          reader.readAsText(file);
          } else {
            this.error = 'Please upload a csv'
          }
        },
        getHeaders(data) {
            let headers = Object.keys(data[0])
            headers.forEach(header => {
              this.headings.push({text: header, value: header})
            })
        }
      }
  }
</script>


<style scoped>
.table-responsive {
  background-image: url("assets/deskbg.jpeg");
}
</style>