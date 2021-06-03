<template>
<!--  
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
Authors: Shamal Faily 
-->

  <div class="usecasemodel">
    <b-card no-body>
      <side-bar :dimension="theSelectedDimension" :panelParameters="panelParameters" :panelObject="theSelectedObject" />
      <b-container fluid>
        <b-row>
          <b-col>
            <b-form-group label="Environment" label-for="usecaseModelEnvironment" >
              <dimension-select ref="usecaseModelEnvironment" id="usecaseModelEnvironment" dimension="environment" v-on:dimension-select-change="environmentSelected" v-on:dimension-items-updated="environmentsLoaded" />
            </b-form-group>
          </b-col>
          <b-col v-if="theEnvironmentName != ''">
            <b-form-group label="Usecase" label-for="usecaseModelusecase">
              <dimension-select id="usecaseModelusecase" ref="usecaseModelusecase" dimension="usecase" initial="all" :environment="theEnvironmentName" includeall v-on:dimension-select-change="usecaseSelected" v-on:dimension-items-updated="usecaseLoaded" />
            </b-form-group>
          </b-col>
          <b-col v-if="theEnvironmentName != ''">
            <b-form-group label="Refresh" label-for="amRefresh" >
              <font-awesome-icon id="amRefresh" icon="sync" @click.stop="refreshModel" />
            </b-form-group>
          </b-col>
        </b-row>
      </b-container>
    </b-card>
    <b-container fluid>
      <graphical-model v-if="theEnvironmentName != ''" ref="graphicalModel" :api="usecaseModelURI" :parameters="concernsParameter" v-on:graphical-model-url="nodeClicked"/>
    </b-container> 
  </div>
</template>

<script>
import axios from 'axios';
import GraphicalModel from '@/components/GraphicalModel.vue';
import DimensionSelect from '@/components/DimensionSelect.vue';
import SideBar from '@/components/SideBar.vue';
import EventBus from '../utils/event-bus';
export default {
  computed : {
    usecaseModelURI() {
      return "/api/usecase/model/environment/" + this.theEnvironmentName + "/usecase/" + this.usecase ;
    },
    usecase() {
      return this.theUsecaseName == undefined || this.theUsecaseName.length == 0 ? 'all' : this.theUsecaseName;
    },
    panelParameters() {
      return this.theEnvironmentName != '' ? {'environment' : this.theEnvironmentName} : undefined;
    }
  },
  data() {
    return {
      theEnvironmentName : '',
      theUsecaseName : 'all',
      theSelectedObject: null,
      theSelectedDimension : ''
    }
  },
  components : {
    GraphicalModel,
    DimensionSelect,
    SideBar
  },
  methods : {
    nodeClicked(url) {
      this.theSelectedDimension = url.slice(5).substring(0, url.slice(5).indexOf('/'))
      if (['usecases','personas'].indexOf(this.theSelectedDimension) == -1) {
        return;
      }
      axios.get(url,{
        baseURL : this.$store.state.url,
        params : {'session_id' : this.$store.state.session}
      })
      .then(response => {
        this.theSelectedObject = response.data;
      })
      .catch((error) => {
        EventBus.$emit('operation-failure',error)
      })
    },
    environmentSelected(envName) {
      this.theEnvironmentName = envName;
      this.$refs.usecaseModelEnvironment.selected = envName;
      this.theUsecaseName = 'all';
    },
    environmentsLoaded(envName) {
      this.theEnvironmentName = envName;
      this.$refs.usecaseModelEnvironment.selected = envName;
      this.theUsecaseName = 'all';
    },
    usecaseSelected(usecaseName) {
      this.theUsecaseName = usecaseName;
    },
    usecaseLoaded(usecaseName) {
      this.theUsecaseName = usecaseName;
    },
    refreshModel() {
      this.$refs.graphicalModel.loadModel();
    }
  }
}
</script>
