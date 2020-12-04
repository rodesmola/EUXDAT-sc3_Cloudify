<template>
    <div>
    
      <v-flex xs12 pl-2 row>
        <v-form ref="agroClimaticDataForm" v-model="agroClimaticDataValid">
            <v-layout row wrap class="pl-2 pr-2"> 
              
              <v-flex xs12 class="pl-1 pr-1">
                <UserPolygons/>
              </v-flex>

              <v-flex xs12 sm12 md12 lg12 class="mb-3">                
                <v-divider ></v-divider>
              </v-flex>

              <v-flex xs6 class="pl-3 pr-3">
                <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="agroClimaticData.hour_start" 
                    :value="agroClimaticData.hour_start" label="Start hour"  
                    @input="$v.agroClimaticData.hour_start.$touch()" @blur="$v.agroClimaticData.hour_start.$touch()"
                    :error-messages="hour_startErrors">
                </v-text-field>                         
              </v-flex>
              <v-flex xs6 class="pl-3 pr-3">
                <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="agroClimaticData.hour_end" 
                    :value="agroClimaticData.hour_end" label="End hour" 
                    @input="$v.agroClimaticData.hour_end.$touch()" @blur="$v.agroClimaticData.hour_end.$touch()"
                    :error-messages="hour_endErrors">
                </v-text-field>                            
              </v-flex>
  
              <v-flex xs6 class="pl-3 pr-3">
                <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="agroClimaticData.years_start" 
                    :value="agroClimaticData.years_start" label="Start year" 
                    @input="$v.agroClimaticData.years_start.$touch()" @blur="$v.agroClimaticData.years_start.$touch()"
                    :error-messages="years_startErrors">
                </v-text-field>
              </v-flex>
              <v-flex xs6 class="pl-3 pr-3">
                <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="agroClimaticData.years_end" 
                    :value="agroClimaticData.years_end" label="End year" 
                    @input="$v.agroClimaticData.years_end.$touch()" @blur="$v.agroClimaticData.years_end.$touch()"
                    :error-messages="years_endErrors">
                </v-text-field>                        
              </v-flex>

              <v-flex xs6 class="pl-3 pr-3">
                <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="agroClimaticData.day_row" 
                    :value="agroClimaticData.day_row" label="Day in row" 
                    @input="$v.agroClimaticData.day_row.$touch()" @blur="$v.agroClimaticData.day_row.$touch()"
                    :error-messages="day_rowErrors">
                </v-text-field>   
              </v-flex>

              <v-flex xs6 class="pl-3 pr-3">
                <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="agroClimaticData.frost_degree" 
                    :value="agroClimaticData.frost_degree" label="Frost degree" 
                    @input="$v.agroClimaticData.frost_degree.$touch()" @blur="$v.agroClimaticData.frost_degree.$touch()"
                    :error-messages="frost_degreeErrors">
                </v-text-field>  
              </v-flex>
              <v-flex xs6 class="pl-3 pr-3">
                <v-text-field hide-no-data hide-selected dense color="#77b942" type="text" v-model="agroClimaticData.probability" 
                    :value="agroClimaticData.probability" label="Probability (%)" 
                    @input="$v.agroClimaticData.probability.$touch()" @blur="$v.agroClimaticData.probability.$touch()"
                    :error-messages="probabilityErrors">
                </v-text-field> 
              </v-flex>

              <v-flex xs12 sm12 md12 lg12>
                <small v-if="!isOutput">* Indicates required field</small>
                <v-divider ></v-divider>
              </v-flex>

              <v-flex xs12 sm12 md12 lg12 v-if="!isSelected && !isOutput" class="green panel-chip">
                <span color="#4ba64f" label>Please select a polygon to start the service.</span>
              </v-flex>

              <v-flex xs12 class="text-xs-right pr-3 hidden-lg-and-down" style="padding: 0px; margin-bottom: 5px;">
                <v-btn small round color="#27304c" :disabled="!agroClimaticDataValid" :loading="isLoading" dark @click="runService()" title="Run service" >
                RUN
                </v-btn>
            </v-flex> 

            </v-layout>
        </v-form> 
      </v-flex>

    </div>
</template>

<script>
import { required, numeric, between, decimal } from 'vuelidate/lib/validators'
import UserPolygons from '@/components/UserPolygons.vue'
import CONST from "../const";
export default {
    name: "AgroclimaticZones",
    components: {
        UserPolygons
    },
    props: {},
    data: () => ({
      euxdatURL: CONST.euxdatURL,      
      agroClimaticData: {
        hour_start: 0,
        hour_end: 23,
        years_start: 2016,
        years_end: 2018,
        day_row: 1,
        frost_degree: 0,
        probability: 10
      }
     
    }),
    methods: {
      
    },
    validations: {
      agroClimaticData:{               
        hour_start: {numeric, required, between: between(0, 24)},  
        hour_end: {numeric, required, between: between(0, 24)},
        years_start: {numeric, required, between: between(1985, 2020)},
        years_end: {numeric, required, between: between(1985, 2020)},
        day_row: {numeric, required, between: between(1, 365)},
        frost_degree: {decimal, required, between: between(-15, 50)},       
        probability: {numeric, required, between: between(0, 100)},   
      }
    },    
    computed: {
      hour_startErrors () {
        const errors = []
        if (!this.$v.agroClimaticData.hour_start.$dirty) return errors
          !this.$v.agroClimaticData.hour_start.required && errors.push('Mandatory field')
          !this.$v.agroClimaticData.hour_start.between && errors.push('Values from 0 to 24')
          !this.$v.agroClimaticData.hour_start.numeric && errors.push('Insert a number')
          return errors
        },   
      hour_endErrors () {
        const errors = []
        if (!this.$v.agroClimaticData.hour_end.$dirty) return errors
          !this.$v.agroClimaticData.hour_end.required && errors.push('Mandatory field')
          !this.$v.agroClimaticData.hour_end.between && errors.push('Values from 0 to 24')
          !this.$v.agroClimaticData.hour_end.numeric && errors.push('Insert a number')
          return errors
        },
      years_startErrors () {
        const errors = []
        if (!this.$v.agroClimaticData.years_start.$dirty) return errors
          !this.$v.agroClimaticData.years_start.required && errors.push('Mandatory field')
          !this.$v.agroClimaticData.years_start.between && errors.push('Values from 1985 to 2020')
          !this.$v.agroClimaticData.years_start.numeric && errors.push('Insert a number')
          return errors
        },        
      years_endErrors () {
        const errors = []
        if (!this.$v.agroClimaticData.years_end.$dirty) return errors
          !this.$v.agroClimaticData.years_end.required && errors.push('Mandatory field')
          !this.$v.agroClimaticData.years_end.between && errors.push('Values from 1985 to 2020')
          !this.$v.agroClimaticData.years_end.numeric && errors.push('Insert a number')
          return errors
        },    
      day_rowErrors () {
        const errors = []
        if (!this.$v.agroClimaticData.day_row.$dirty) return errors
          !this.$v.agroClimaticData.day_row.required && errors.push('Mandatory field')
          !this.$v.agroClimaticData.day_row.between && errors.push('Values from 1 to 365')
          !this.$v.agroClimaticData.day_row.numeric && errors.push('Insert a number')
          return errors
        },    
      frost_degreeErrors () {
        const errors = []
        if (!this.$v.agroClimaticData.frost_degree.$dirty) return errors
          !this.$v.agroClimaticData.frost_degree.required && errors.push('Mandatory field')
          !this.$v.agroClimaticData.frost_degree.between && errors.push('Values from -15 to 50')
          !this.$v.agroClimaticData.frost_degree.decimal && errors.push('Insert a number')
          return errors
        },  
      probabilityErrors () {
        const errors = []
        if (!this.$v.agroClimaticData.probability.$dirty) return errors
          !this.$v.agroClimaticData.probability.required && errors.push('Mandatory field')
          !this.$v.agroClimaticData.probability.between && errors.push('Values from 0 to 100')
          !this.$v.agroClimaticData.probabilitye.numeric && errors.push('Insert a number')
          return errors
        },                     
      },
    watch: {

    },
    created(){
   
    }
};
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
.v-input {
	font-size: 12px;
	text-align: left;
}
.v-text-field {
	padding-top: 0px;
	margin-top: 4px;
}

.v-btn--small {
	font-size: 12px;
	height: 20px;
	padding: 0 8px;
  min-width: 58px;
}

.panel-chip {
  padding: 0px;
  text-align: center;
  margin: 5px;
  padding: 2px;
  border-radius: 2px;
  justify-content:
  space-between;
  font-family: 'Roboto', sans-serif;
  font-size: 12px;
}
</style>


