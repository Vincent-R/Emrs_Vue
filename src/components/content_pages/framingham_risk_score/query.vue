<template>
    <div class="frs-query">
        <div class="page-bar">
            <ul class="page-breadcrumb">
                <li>
                    <span>Framingham Risk Score Calculator</span>
                    <i class="fa fa-circle"></i>
                </li>
                <li>
                    <router-link to="/home/frs_query">Result List</router-link>
                </li>
            </ul>                     
        </div>
        <h1 class="page-title"> Framingham Risk Score Calculator
            <small>Dispaly the result list below</small>        
        </h1>
        <div class="portlet light">
            <div class="portlet-title">
                <div class="caption font-dark">
                    <i class="icon-list font-dark"></i>
                    <span class="caption-subject bold uppercase"> Result List </span>
                </div>
            </div>
            <div class="portlet-body">
                <table class="table table-striped">
                    <thead>
                        <tr>
                            <th colspan="2">Index</th>
                            <th class="col-md-6">Patient Information</th>
                            <th class="col-md-4">Result Data</th>
                            <th>Creation Time</th>
                        </tr>
                    </thead>
                    <tbody class="text-center" v-if="recordList.length>0">
                        <tr v-for="(item, index) in recordList" :key="item.id">
                            <td>{{index+1}}</td>
                            <td>
                                <span @click="toggleCollapse(index+1)">
                                    <i title="collapse" class="fa fa-plus-square-o" aria-hidden="true" v-if="!checkCollapseStatus(index+1)"></i>
                                    <i title="collapse" class="fa fa-minus-square-o" aria-hidden="true" v-else></i>
                                </span>
                            </td>
                            <td v-show="!checkCollapseStatus(index+1)"><span class="bold">Patiend ID:</span> {{item.patientInfo.id}} &nbsp;&nbsp; <span class="bold">Name:</span> {{item.patientInfo.name}}</td>
                            <td colspan="1" v-show="!checkCollapseStatus(index+1)" class="font-blue">Click the collapse button left to view detail information</td>
                            <td v-show="checkCollapseStatus(index+1)">
                                <table class="table table-bordered table-hover">
                                    <tbody>
                                        <tr>
                                            <td class="col-md-2">Patient ID</td>
                                            <td class="col-md-2">{{item.patientInfo.id}}</td>
                                            <td class="col-md-2">Name</td>
                                            <td class="col-md-2">{{item.patientInfo.name}}</td>
                                        </tr>
                                        <tr>
                                            <td class="col-md-2">Age</td>
                                            <td class="col-md-2">{{item.patientInfo.age}}</td>
                                            <td class="col-md-2">Sex</td>
                                            <td class="col-md-2">{{item.patientInfo.sex}}</td>
                                        </tr>
                                        <tr>
                                            <td class="col-md-2">Phone Number</td>
                                            <td class="col-md-2">{{item.patientInfo.phone}}</td>
                                            <td class="col-md-2">Smoke</td>
                                            <td class="col-md-2">{{item.patientInfo.smoke === '1'? 'Yes':'No'}}</td>
                                        </tr>
                                        <tr>
                                            <td class="col-md-2">TC</td>
                                            <td class="col-md-2">{{item.patientInfo.TC}}<span> mg/dL</span></td>
                                            <td class="col-md-2">HDL-C</td>
                                            <td class="col-md-2">{{item.patientInfo.HDL_C}}<span> mg/dL</span></td>
                                        </tr>
                                        <tr>
                                            <td class="col-md-2">SBP</td>
                                            <td class="col-md-2">{{item.patientInfo.SBP}}<span> mmHg</span></td>
                                            <td class="col-md-2">Is SBP treated</td>
                                            <td class="col-md-2">{{item.patientInfo.SBP_Treated === '1'? 'Yes':'No'}}</td>
                                        </tr>
                                        <tr>
                                            <td class="col-md-2">address</td>
                                            <td class="col-md-2">{{item.patientInfo.address}}</td>
                                            <td class="col-md-2">other</td>
                                            <td class="col-md-2">{{item.patientInfo.other}}</td>
                                        </tr>
                                    </tbody>
                                </table>                           
                            </td>
                            <td v-show="checkCollapseStatus(index+1)">
                                <table class="table table-bordered table-hover">
                                    <tbody>
                                        <tr><td>Age Score</td><td>{{item.resultData.ageScore}}</td></tr>
                                        <tr><td>TC Score</td><td>{{item.resultData.TCScore}}</td></tr>
                                        <tr><td>Smoke Score</td><td>{{item.resultData.smokeScore}}</td></tr>
                                        <tr><td>HDL-C Score</td><td>{{item.resultData.HDL_CScore}}</td></tr>
                                        <tr><td>SBP Score</td><td>{{item.resultData.SBPScore}}</td></tr>
                                        <tr><td>Total Score</td><td>{{item.resultData.totalScore}}</td></tr>
                                        <tr><td class="font-red">Framingham Risk Score</td><td>{{item.resultData.FRScore}}</td></tr>
                                    </tbody>
                                </table> 
                            </td>
                            <td>{{item.createTime}}</td>
                        </tr>
                    </tbody>
                    <tbody v-if="recordList.length==0">
                        <tr><td colspan="6">No data available!</td></tr>
                    </tbody>
                </table>
            </div>
        </div>
        <sweet-modal ref="errorModal" icon="error" :blocking="true" :hide-close-button="false">
            {{errorMessage}}
        </sweet-modal>
  </div>
</template>

<script>
import SweetButton from 'sweet-modal-vue/docs/components/Button';
import utils from '@/utils/utils';
import config from '@/config';
export default {
    data () {
        return {
            collapseList: [1],
            recordList: [],
            errorMessage: ''
        }
    },
    methods: {
        toggleCollapse(i){
            let eleInList = false;
            let eleIndex = null;
            this.collapseList.forEach(function(ele, index){
                if(ele === i){
                    eleIndex = index;
                    eleInList = true;
                }
            });
            
            if(eleInList){
                this.collapseList.splice(eleIndex, 1);
            }else{
                this.collapseList.push(i);
            }
        },
        checkCollapseStatus(i){
            let eleInList = false;
            this.collapseList.forEach(function(ele){
                if(ele === i){
                    eleInList = true;
                }
            })
            return eleInList;
        },
        getRecordList(vm){
            vm.$http.post(config.apiHost + '/dp/getAllFRSPredictionsByUserId', {userId: JSON.parse(sessionStorage.getItem('user'))._id}).then(response => {
                let responseData = response.body;
                if (responseData.status) {            
                    vm.recordList=responseData.data;
                    console.log(vm.recordList);
                } else {
                    vm.errorMessage = 'Get the result data failed, please try again!';
                    vm.$refs.errorModal.open();
                }
            }, response => {
                vm.errorMessage = 'Get the result data failed, please try again!';
                vm.$refs.errorModal.open();
            });
        }
    },
    watch: {
        '$route' () {
            this.getRecordList(this);
        }
    },
    created () {
        this.getRecordList(this);
    },
    components: {
        SweetButton
    },
    mounted () {
        utils.handleSidebarAndContentHeight();
        utils.handleGoTop();
    }
}
</script>

<style lang="stylus" scoped>
    .frs-query
        table         
            thead,th
                vertical-align middle
                font-weight bold
                color #000
            thead,th
            tbody,td                
                    text-align center   
                    .fa
                        font-size 16px
                        cursor pointer                        
                    
        .table-hover
            td:nth-child(1)                
                font-weight bold
                color #666
            td:nth-child(2)                
                color #000
                span 
                    color #666
            td:nth-child(3)                
                font-weight bold
                color #666
            td:nth-child(4)                
                color #000
                span 
                    color #666           
            img
                max-width:100%
</style>


