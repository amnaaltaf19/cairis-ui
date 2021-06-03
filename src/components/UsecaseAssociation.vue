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
  <div class="usecaseassociation">
    <p v-if="errors.length">
      <b>Please correct the following error(s):</b>
      <ul>
        <li v-for="error in errors" :key="error">{{ error }}</li>
      </ul>
    </p>
    <b-form>
      <b-container fluid>
      <b-card bg-variant="light" no body> 
        <b-row>
          <b-col md=12>
            <b-container v-if="objt != undefined" fluid>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Environment" label-class="font-weight-bold text-md-left" label-for="theEnvironmentSelect" >
                    <dimension-select id="theEnvironmentSelect" ref="theEnvironmentSelect" dimension='noncomposite_environment' :initial="objt.theEnvironmentName" v-on:dimension-select-change="environmentSelected" v-on:dimension-items-updated="environmentsLoaded" />
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Head" label-class="font-weight-bold text-sm-left" label-for="theHeadUsecaseSelect" >
                    <dimension-select id="theHeadUsecaseSelect" ref="theHeadUsecaseSelect" dimension='usecase' :environment="objt.theEnvironmentName" :initial="objt.theHeadUsecase" v-on:dimension-select-change="headUsecaseSelected" v-on:dimension-items-updated="headUsecaseLoaded" />
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Navigation" label-class="font-weight-bold text-sm-left" label-for="theHeadNavRadio" >
                    <b-form-radio-group id="theHeadNavRadio" v-model="objt.theHeadNavigation">
                      <b-form-radio value=1>1</b-form-radio>
                      <b-form-radio value=0>0</b-form-radio>
                    </b-form-radio-group>
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Type" label-class="font-weight-bold text-md-left" label-for="theHeadTypeRadio" >
                    <b-form-radio-group id="theHeadTypeRadio" v-model="objt.theHeadType" required >
                      <b-form-radio value="Association">Association</b-form-radio>
                    </b-form-radio-group>
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Role" label-class="font-weight-bold text-md-left" label-for="theHeadRoleInput" >
                    <b-form-input id="theHeadRoleInput" v-model="objt.theHeadRole" type="text" />
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Role" label-class="font-weight-bold text-md-left" label-for="theTailRoleInput" >
                    <b-form-input id="theTailRoleInput" v-model="objt.theTailRole" type="text" />
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Type" label-class="font-weight-bold text-md-left" label-for="theTailTypeRadio" >
                    <b-form-radio-group id="theTailTypeRadio" v-model="objt.theTailType" required >
                      <b-form-radio value="Association">Association</b-form-radio>
                    </b-form-radio-group>
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Navigation" label-class="font-weight-bold text-sm-left" label-for="theTailNavRadio" >
                    <b-form-radio-group id="theTailNavRadio" v-model="objt.theTailNavigation">
                      <b-form-radio value=1>1</b-form-radio>
                      <b-form-radio value=0>0</b-form-radio>
                    </b-form-radio-group>
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Tail" label-class="font-weight-bold text-sm-left" label-for="theTailUsecaseSelect" >
                    <dimension-select id="theTailUsecaseSelect" ref="theTailUsecaseSelect" dimension='usecase' :environment="objt.theEnvironmentName" :initial="objt.theTailUsecase" v-on:dimension-select-change="tailUsecaseSelected" v-on:dimension-items-updated="tailUsecaseLoaded" />
                  </b-form-group>
                </b-col>
              </b-row>
              <b-row>
                <b-col md="12">
                  <b-form-group label="Rationale" label-class="font-weight-bold text-sm-left" label-for="theRationaleInput" >
                    <b-form-input id="theRationaleInput" size="md" v-model="objt.theRationale"></b-form-input>
                  </b-form-group>
                </b-col>
              </b-row>
            </b-container>
          </b-col>
        </b-row>
      </b-card> 
      </b-container>
      <b-container fluid>
        <b-form-row>
          <b-col md="4" offset-md="5" >
            <b-button type="submit" variant="primary" @click="onCommit">{{commitLabel}}</b-button>
            <b-button type="submit" variant="secondary" @click="onCancel">Cancel</b-button>
          </b-col>
        </b-form-row>
      </b-container> 
    </b-form>
  </div>
</template>


<script>

import objectMixin from '../mixins/objectMixin'
import DimensionSelect from '@/components/DimensionSelect.vue'

export default {
  name : 'usecase-association',
  props : {
    object : Object,
    label : String,
    isUpdating : Boolean
  },
  watch : {
    object : 'setObject'
  },
  mixins : [
    objectMixin
  ],
  components : {
    DimensionSelect
  },
  data() {
    return {
      errors : [],
      objt : this.object,
      commitLabel : 'Create'
    }
  },
  methods : {
    setObject() {
      this.objt = this.object;
      this.$refs.theEnvironmentSelect.selected = this.objt.theEnvironmentName;
      this.$refs.theHeadUsecaseSelect.selected = this.objt.theHeadUsecase;
      this.$refs.theTailUsecaseSelect.selected = this.objt.theTailUsecase;
      this.commitLabel = this.label;
    },
    onCommit(evt) {
      evt.preventDefault();
      if (this.checkForm()) {
        this.$emit('usecase-association-commit',this.objt);
      }
    },
    onCancel(evt) {
      evt.preventDefault();
      this.$router.push({ name: 'objectsview', params: {dimension: 'usecaseassociation'}});
    },
    checkForm() {
      this.errors = []
      if (this.objt.theEnvironmentName.length == 0) {
        this.errors.push('Environment is required');
      }
      if (this.objt.theHeadUsecase.length == 0) {
        this.errors.push('Head Usecase is required');
      }
      if (this.objt.theHeadNavigation.length == 0) {
        this.errors.push('Head navigation is required');
      }
      if (this.objt.theHeadType.length == 0) {
        this.errors.push('Head association type is required');
      }
      if (this.objt.theTailType.length == 0) {
        this.errors.push('Tail association type is required');
      }
      if (this.objt.theTailNavigation.length == 0) {
        this.errors.push('Tail navigation is required');
      }
      if (this.objt.theTailUsecase.length == 0) {
        this.errors.push('Tail Usecase is required');
      }
      if (this.objt.theRationale.length == 0) {
        this.errors.push('Rationale is required');
      }
      if (!this.errors.length) {
        return true;
      }
      else {
        return false;
      }
    },
    environmentSelected(item) {
      if (item != undefined) {
        this.objt.theEnvironmentName = item;
      }
    },
    environmentsLoaded(item) {
      this.objt.theEnvironmentName = item;
    },
    headUsecaseSelected(item) {
      if (item != undefined) {
        this.objt.theHeadUsecase = item;
      }
    },
    headUsecaseLoaded(item) {
      this.objt.theHeadUsecase = item;
    },
    tailUsecaseSelected(item) {
      if (item != undefined) {
        this.objt.theTailUsecase = item;
      }
    },
    tailUsecaseLoaded(item) {
      this.objt.theTailUsecase = item;
    }
  }
}
</script>