<template>
  <div class="SearchBar">
    <loading :active.sync="showBlockUi" 
      :can-cancel="false"
      transition="fade"
      :is-full-page="true"
      color="#303f9f"
      >
    </loading>

    <b-input-group>
      <b-form-input placeholder="Please enter your search term" v-model="jobSearch.Keywords"></b-form-input>
      <b-input-group-append>
        <b-button @click="submitSearchRequest" variant="primary"><b>Search</b></b-button>
      </b-input-group-append>
    </b-input-group>
  </div>
</template>

<script lang="ts">
  import { Component, Prop, Vue } from 'vue-property-decorator';
  import { SearchBindingModel } from '../interfaces/bindingModels';
  import { ApiRoutes } from '../interfaces/apiRoutes';
  import Loading from 'vue-loading-overlay';
  import 'vue-loading-overlay/dist/vue-loading.css';
  import axios from 'axios';

  @Component({
    components: {
      Loading
    }
  })
  export default class SearchBar extends Vue {
    @Prop() private jobSearch!: SearchBindingModel;
    @Prop() private jwtToken!: string;

    showBlockUi = false;

    //TODO Error handling of token expiring etc
    async submitSearchRequest(): Promise<void> {
      // if token is undefined or empty
      // it means that they haven't logged in
      // emit event to request login
      if (!this.jwtToken) {
        this.$emit('login');
      }

      const jobRouteUrl = ApiRoutes.jobsRoute;
      try 
      {
        this.showBlockUi = true;
        const response = await axios.post(jobRouteUrl, 
          this.jobSearch, 
          {params: {"token": this.jwtToken}}
        );
        this.$emit('jobs', response.data.jobs);
      } 
      // should handle error in token expiring here
      catch (error) 
      {
        console.log(error);
      }
      this.showBlockUi = false;
    }    
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .SearchBar {
    margin-top: 30px;
    margin-bottom: 30px;
  }
</style>
