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
						<v-card-title>
							CSV Data
							<v-spacer></v-spacer>
							<v-text-field
								v-model="search"
								append-icon="search"
								label="Search"
								single-line
								hide-details
								:disabled="disabled">
							</v-text-field>
						</v-card-title>
						<v-data-table
							v-if="csvData"
							:headers="headings"
							:items="csvData"
							:search="search"
							hide-actions
							class="elevation-1"
							:pagination.sync="pagination">
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
				search: '',
        headings: [],
        csvData: '',
        error: false,
        parseError: '',
				alert: false,
				disabled: true
      }
    },
		methods: {
			loadCSVFromFile(ev) {
				let file = ev.target.files[0]
				let reader = new FileReader()

				// Only allow csv files
				if (file.type === 'text/csv') {
					// Parse csv on load
					reader.onload = e => this.$emit( "load", this.parsedCsv = Papa.parse(e.target.result, { header: true }),
					this.csvData = this.parsedCsv.data,
					
					// Enable Searchfield
					this.disabled = false,
					// Handle Parsing errors
					this.parseError = this.parsedCsv.errors[2],

					// Invoke getheaders function and pass csv data
					this.getHeaders(this.csvData)),

					// Set error to default state
					this.error = '',
					this.alert = false

					reader.readAsText(file)
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