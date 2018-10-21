<template>
  <v-app>
    <v-toolbar>
     <v-toolbar-title>Select a CSV to Upload</v-toolbar-title>
     </v-toolbar>
    <v-responsive class="table-responsive">
      <v-card v-if="error">
              {{ parseError }}
             <v-alert v-show="alert" outline color="error" icon="warning" :value="true" dismissible v-model="alert">
              {{ error }}
            </v-alert>
      </v-card>
      <v-container>
        <v-flex>
          <v-card>
            <v-data-table
            v-if="csvData"
            :headers="headings"
            :items="csvData"
            hide-actions
            class="elevation-1"
            :pagination.sync="pagination"
          >
            <template slot="items" slot-scope="props">
              <td v-for="item in props.item" v-bind:key="item" class="text-xs-right">{{ item }}</td>
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
        pagination: {
          sortBy: 'sur_name'
        },
        headings: [],
        csvData: '',
        error: false,
        parseError: '',
        alert: false
      }
    },
      methods: {
        loadCSVFromFile(ev) {
          const file = ev.target.files[0];
          const reader = new FileReader();
          // Only allow csv files
          if (file.type === 'text/csv') {
            reader.onload = e => this.$emit( "load", 
              // Parse csv including headers
              this.csvData = Papa.parse(e.target.result, { header: true }).data,
              // Handle parsing errors
              this.parseError = Papa.parse(e.target.result, { header: true }).errors[2],
              // Invoke getheaders function
              this.getHeaders(this.csvData))
              // Set error to default state
              this.error = ''
              this.alert = false
          reader.readAsText(file);
          } else {
            this.error = 'Please upload a csv file'
            this.alert = true
          }
        },
        getHeaders(data) {
            let headers = Object.keys(data[0])
            headers.forEach(header => {
              // Construct object format required for Vuetify table component
              this.headings.push({text: header, value: header})
            })
        }
      }
  }
</script>


<style scoped>
.table-responsive {
  background-image: url("assets/bg.jpg");
}
  * {
    color: #707070;
  }
</style>