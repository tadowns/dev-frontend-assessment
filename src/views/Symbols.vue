<template>
    <div>
      <div class="primary-heading-con">
          <div class="heading">
              <div class="title">Symbols/Tickers</div>
          </div>
      </div>

      <div class="columns m-lg m-b-n">
        <div class="column is-half">
          <div class="columns">
            <div class="column">
              <div class="field">
                <label class="label">Search</label>
                <div class="control">
                  <input class="input" type="text" v-model="search" placeholder="Company or Symbol ">
                </div>
              </div>
            </div>

            <div class="column">
              <div class="field">
                <label class="label">Filter</label>
                <div class="control">
                  <div class="select">
                    <select v-model="filter">
                      <option selected value=null>No filter</option>
                      <option value="open">Open</option>
                      <option value="close">Close</option>
                    </select>
                  </div>
                </div>
              </div>
            </div>

            <div class="column">
              <div class="field">
                <label class="label">From</label>
                <div class="control">
                  <input class="input" type="number" v-model="filterFrom" placeholder="Number">
                </div>
              </div>
            </div>
            <div class="column">
              <div class="field">
                <label class="label">To</label>
                <div class="control">
                  <input class="input" type="number" v-model="filterTo" placeholder="Number">
                </div>
              </div>
            </div>
            <div class="column">
              <div class="field">
                <label class="label" style="height:19px;">  </label>
                <div class="control">
                  <button class="button" v-on:click="clearFilters">Clear Filters</button>
                </div>
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="page-content-con">
          <loading v-if="loading"></loading>
          <b-table v-else
            :data="filteredCompanies"
            :paginated="isPaginated"
            :per-page="perPage"
            :current-page.sync="currentPage"
            :pagination-simple="isPaginationSimple"
            :default-sort="defaultSort"
            :default-sort-direction="defaultSortDirection">
            <template slot-scope="props">
                <b-table-column field="symbol" label="Symbol" sortable>
                    {{ props.row.symbol }}
                </b-table-column>

                <b-table-column field="companyName" label="Company Name" sortable>
                    {{ props.row.companyName }}
                </b-table-column>

                <b-table-column field="open" label="Open" sortable>
                    <money :value="props.row.open"></money>
                </b-table-column>

                <b-table-column field="close" label="Close" sortable>
                    <money :value="props.row.close"></money>
                </b-table-column>

                <b-table-column field="changePercent" label="Performance" sortable>
                    <ticker-change :value="props.row.changePercent" ></ticker-change>
                </b-table-column>
            </template>
          </b-table>
      </div>

    </div>
</template>

<script>
import API from '../api/IEX';

import Vue from 'vue'
import Buefy from 'buefy'
import 'buefy/dist/buefy.css'

import Money from '../elements/Money'
import TickerChange from '../elements/TickerChange'

Vue.use(Buefy)

export default {
    name : "Symbols",
    data () {
        return {
            loading : true,
            search: null,
            filter: null,
            filterFrom: null,
            filterTo: null,
            isPaginated: true,
            isPaginationSimple: false,
            defaultSortDirection: 'asc',
            currentPage: 1,
            perPage: 20,
            defaultSort: "companyName",
            companies : []
        };
    },
    beforeMount () {
        API.getCompanies().then(response => {
            this.companies = response.data;
        }).finally(() => {
            this.loading = false;
        });
    },
    computed: {
      filteredCompanies () {
        if (!this.companies.length) {
          return [];
        }

        this.filterFrom = this.filterFrom == "" ? null : this.filterFrom;
        this.filterTo = this.filterTo == "" ? null : this.filterTo;
        this.search = this.search == "" ? null : this.search;

        var companies = this.companies;

        if(this.search != null && this.search != ""){
          companies = companies.filter(company => {
            return company.companyName.toLowerCase().includes(this.search) || company.symbol.toLowerCase().includes(this.search);
          });
        }

        if(this.filter != null && (this.filterTo != null || this.filterFrom != null)){
          companies = companies.filter(company => {
            if(company[this.filter] == null){
              return false;
            }
            return (this.filterFrom == null ? true : this.filterFrom <= company[this.filter]) && (this.filterTo == null ? true : this.filterTo >= company[this.filter]);
          });
        }

        return companies;
      }
    },
    methods: {
      clearFilters (){
        this.search = null;
        this.filter = null;
        this.filterFrom = null;
        this.filterTo = null;
      }
    }
}
</script>

<style lang="scss" scoped>
    @import '../assets/css/_theme';
    .company {
        margin-bottom:10px;
        padding-bottom:10px;
        border-bottom:1px solid $white-ter;
    }
</style>
