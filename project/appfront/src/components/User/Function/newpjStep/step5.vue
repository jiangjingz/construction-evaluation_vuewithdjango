<template>
    <div>
        <el-row>
            <el-button size="small" class='btn' @click="next">下一步</el-button>
            <el-button size="small" class='btn' @click="back">上一步</el-button>
            <!-- <el-button size="small" class='btn' @click="save5">保存地震信息</el-button> -->
        </el-row>
        <el-row class="step5" :gutter="20">
            <el-col :span="6">
                <div style="width:100%">
                    <span class="label">设防烈度</span>
                    <el-select style="display:block" size="small" @change="change_level" v-model="defense_intensity" placeholder="请选择">
                        <el-option
                        v-for="item in defense_in_option"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                        </el-option>
                    </el-select>
                </div>
                <span class="label">场地类别</span>
                <el-select size="small" style="width:90%" v-model="site_type" placeholder="请选择">
                    <el-option
                    v-for="item in site_type_option"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                    </el-option>
                </el-select>
            </el-col>
            <el-col :span='6'>
                <span class="label">地震波数</span>
                <el-input size="small" @change="set_num" style="" v-model="number" placeholder="请输入内容"></el-input>
                <span class="label">地震水准</span>
                    <el-select size="small" style="" @change="change_level" v-model="earthquake_level" placeholder="请选择">
                        <el-option
                        v-for="item in earth_level_option"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value">
                        </el-option>
                    </el-select>
                
                
            </el-col>
            <el-col :span='6'>
                <span class="label">峰值加速度</span>
                    <el-input size="small" :disabled="true" v-model="peak_acceleration" placeholder="请输入内容"></el-input>
            </el-col>
            <el-col :span='6'>
                <span class="label">地震分组</span>
                <el-select size="small" v-model="group" placeholder="请选择">
                    <el-option
                    v-for="item in group_option"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value">
                    </el-option>
                </el-select>
                
            </el-col>
        </el-row>
        <!--<el-col :span="1" style="color:transparent">''</el-col>-->
        <el-row>
            <el-col :span="24">
            <el-table :data="earthquake_info" border class="tb-edit" style="width:100%" highlight-current-row width="500" max-height="350" @row-click="handleCurrentChange">
                <el-table-column prop="earthquake_no" label="地震波编号" width="140">
                    <template slot-scope="scope">
                        <el-input size="small" v-model="scope.row.earthquake_no" @change="handleEdit(scope.$index, scope.row)"></el-input><span>{{scope.row.earthquake_no}}</span>
                    </template>
                </el-table-column>
                <el-table-column prop="name" label="地震波名称">
                    <template slot-scope="scope">
                        <el-input size="small" v-model="scope.row.name" @change="handleEdit(scope.$index, scope.row)"></el-input><span>{{scope.row.name}}</span>
                    </template>
                </el-table-column>
                <el-table-column prop="peak" label="地震波峰值">
                    <template slot-scope="scope">
                        <el-input size="small" v-model="scope.row.peak" @change="handleEdit(scope.$index, scope.row)"></el-input><span>{{scope.row.peak}}</span>
                    </template>
                </el-table-column>
                <el-table-column prop="file" label="地震波文件">
                    <template slot-scope="scope">
                        <el-upload
                            class="upload-demo"
                            action="http://localhost:8000/api/step5-save-wave_file"
                            :data='upload_data'
                            :on-preview="handlePreview"
                            :on-remove="handleRemove"
                            :on-success="handleResponse"
                            :file-list="fileList[scope.$index]"
                            name='test'>
                            <el-button slot="trigger" size="small" type="primary" @click="get_num(scope.$index)">选取文件</el-button>
                            <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button>
                        </el-upload>
                    </template>
                </el-table-column>
                <!--<el-table-column
                    fixed="right"
                    label="操作"
                    width="120">
                    <template slot-scope="scope">
                        <el-button
                        @click.native.prevent="deleteRow(scope.$index, earthquake_info)"
                        type="text"
                        size="small">
                        移除
                        </el-button>
                    </template>
                </el-table-column>-->
            </el-table> 
            <!--<el-button @click="newEarthquake">新增地震波</el-button>-->
            <!-- <el-button @click="saveWaves">保存所有地震波</el-button> -->
        </el-col>
        </el-row>
        
    </div>
</template>
<script>
export default {
    beforeRouteLeave(to, from, next){
        let defense_intensity = JSON.stringify(this.defense_intensity)
        localStorage.setItem('defense_intensity', defense_intensity)
        let site_type = JSON.stringify(this.site_type)
        localStorage.setItem('site_type', site_type)
        let number = this.number
        localStorage.setItem('number', this.number)
        let group = JSON.stringify(this.group)
        localStorage.setItem('group', group)
        let peak_acceleration = JSON.stringify(this.peak_acceleration)
        localStorage.setItem('peak_acceleration', peak_acceleration)
        let earthquake_level = JSON.stringify(this.earthquake_level)
        localStorage.setItem('earthquake_level', earthquake_level)
        let earthquake_info = JSON.stringify(this.earthquake_info)
        localStorage.setItem('earthquake_info', earthquake_info)
        next()
    },
    created(){
      //从localStorage中读取条件并赋值给查询表单
        let defense_intensity = localStorage.getItem('defense_intensity')
        if (defense_intensity != null) {
            this.defense_intensity = JSON.parse(defense_intensity)
        }
        let site_type = localStorage.getItem('site_type')
        if (site_type != null) {
            this.site_type = JSON.parse(site_type)
        }
        let number = localStorage.getItem('number')
        if (number != null) {
            this.number = JSON.parse(number)
        }
        let group = localStorage.getItem('group')
        if (group != null) {
            this.group = JSON.parse(group)
        }
        let peak_acceleration = localStorage.getItem('peak_acceleration')
        if (peak_acceleration != null) {
            this.peak_acceleration = JSON.parse(peak_acceleration)
        }
        let earthquake_level = localStorage.getItem('earthquake_level')
        if (earthquake_level != null) {
            this.earthquake_level = JSON.parse(earthquake_level)
        }
        let earthquake_info = localStorage.getItem('earthquake_info')
        console.log(earthquake_info)
        if (earthquake_info != null) {
            this.earthquake_info = JSON.parse(earthquake_info)
            console.log(this.earthquake_info)
        }
        //上传文件部分改动this.fileList,格式为
        var item = {
            name: 'hhh'
        }
        //this.fileList.push(item)
    },

    methods:{
        // saveWaves(){
        //     let _this=this;
        //     var project=localStorage.getItem('project')
        //     console.log(this.earthquake_info)
        //     this.$ajax({
        //         method:'get',
        //         url:'step5-save-waves',
        //         params:{
        //             earthquake_info:this.earthquake_info,
        //             project:project,
        //             test:this.test,
        //             number:this.number,
        //             username:localStorage.getItem('phone'),
        //         },
        //     })
        //     .then(function(response){
        //         console.log(response)
        //         var res = response.data
        //         console.log(res)
        //         if (res.error_num == 0) {
        //             console.log(res['msg'])
        //         } 
        //         else {
        //             _this.$message.error('存储地震波信息失败')
        //             console.log(res['msg'])
        //         }
        //     })
        //     .catch(function(err){
        //             console.log(err);
        //             });
        // },
        // save5(){
        //     let _this=this;
        //     console.log(this.defense_intensity)
        //     var project=localStorage.getItem('project')
        //     this.$ajax({
        //         method:'get',
        //         url:'step5',
        //         params:{
        //             project:project,
        //             defense_intensity:this.defense_intensity,
        //             site_type:this.site_type,
        //             number:this.number,
        //             group:this.group,
        //             peak_acceleration:this.peak_acceleration,
        //             earthquake_level:this.earthquake_level,
        //             earthquake_info:this.earthquake_info,
        //         },
        //         headers:{"Content-Type": "application/json"}
        //     })
        //     .then(function(response){
        //         console.log(response)
        //         var res=response.data
        //         if(res.error_num==0){
        //             localStorage.setItem('number',_this.number)
        //             _this.$message.success(res['msg'])
        //             console.log(res['msg'])
        //         }
        //         else {
        //             _this.$message.error('存储地震信息失败')
        //             console.log(res['msg'])
        //         }
        //     })
        //     .catch(function(err){
        //             console.log(err);
        //             });
        // },
        /*
        deleteRow(index, rows) {
            rows.splice(index, 1);
        },*/
        next(){
            let _this=this;
            console.log(this.defense_intensity)
            var project=localStorage.getItem('project')
            this.$ajax({
                method:'get',
                url:'step5',
                params:{
                    project:project,
                    defense_intensity:this.defense_intensity,
                    site_type:this.site_type,
                    number:this.number,
                    group:this.group,
                    peak_acceleration:this.peak_acceleration,
                    earthquake_level:this.earthquake_level,
                    earthquake_info:this.earthquake_info,
                },
                headers:{"Content-Type": "application/json"}
            })
            .then(function(response){
                console.log(response)
                var res=response.data
                if(res.error_num==0){
                    localStorage.setItem('number',_this.number)
                    _this.$message.success(res['msg'])
                    console.log(res['msg'])
                    setTimeout(()=>{
                                _this.$emit('next','');
                            },500)
                }
                else {
                    _this.$message.error(res['msg'])
                    console.log(res['msg'])
                }
            })
            .catch(function(err){
                    console.log(err);
                    });

            //this.$emit('next','');
        },
        back(){
            this.$emit('back','');
        },
        change_level(){
            //console.log('!')
            var array = new Array([0.18,0.5,1.25],[0.35,1,2.2],[0.5,1.5,3.1],[0.7,2,4],[1.1,3,5.1],[1.4,4,6.2])
            if(this.defense_intensity !== '' && this.earthquake_level !== null){
                console.log(this.defense_intensity)
                console.log(this.earthquake_level)
                this.peak_acceleration = array[this.defense_intensity-1][this.earthquake_level]
                console.log(this.peak_acceleration)
            }
        },
        submitUpload() {           
            this.$refs.upload.submit();
            console.log(this.fileList);
        },
        handleRemove(file, fileList) {
            //发送请求后台删除文件
            this.$ajax({
                method: 'get',
                url: '',
                params: ''
            }).then(function(response){

            }).catch(function(response){

            })
            console.log(file, fileList);
        },
        handlePreview(file, fileList) {
            console.log(file, fileList);
        },
        handleResponse(response, file, fileList){
            console.log(response)
        },
        get_num(index){
            localStorage.setItem('index',index)
        },
        set_num(){
            this.earthquake_info = [{
                earthquake_no: 1,
                name: '',
                peak:'',
                file:'',
            }]
            for(var i = 0; i < this.number-1; i++){
                //this.earthquake_info[i]=temp
                this.earthquake_info.push({
                    earthquake_no: i+2,
                    name: '',
                    peak:'',
                    file:''
                })    
            }
            console.log(this.earthquake_info)

        },
        handleCurrentChange(row, event, column) {
                console.log(row, event, column, event.currentTarget)
            },
        handleEdit(index, row) {
                console.log(index, row);
            },
        handleDelete(index, row) {
                console.log(index, row);
            }

    },
    data(){
        return{
            upload_data:{
                username:localStorage.getItem('phone'),
                project:1,//localStorage.getItem('project')
                //wave_no:row.earthquake_no,
                num:1,
            },
            fileList: [],
            test:"test",
            defense_intensity:'',
            defense_in_option:[{
                    value: 1,
                    label: '6度'
                }, {
                    value: 2,
                    label: '7度(0.1g)'
                }, {
                    value: 3,
                    label: '8度(0.15g)'
                }, {
                    value: 4,
                    label: '8度(0.2g)'
                }, {
                    value: 5,
                    label: '8度(0.3g)'
                },{
                    value: 6,
                    label: '9度'
                },
            ],
            site_type:null,
            site_type_option:[{
                    value: 1,
                    label: 'Ⅰ_0'
                }, {
                    value: 2,
                    label: 'Ⅰ_1'
                }, {
                    value: 3,
                    label: 'Ⅱ'
                }, {
                    value: 4,
                    label: 'Ⅲ'
                }, {
                    value: 5,
                    label: 'Ⅳ'
                }],
            number:null,
            group:null,
            group_option:[{
                    value: 1,
                    label: '第一组'
                }, {
                    value: 2,
                    label: '第二组'
                }, {
                    value: 3,
                    label: '第三组'
                }],
            peak_acceleration:null,
            earthquake_level:null,
            earth_level_option:[{
                    value: 1,
                    label: '多遇地震'
                }, {
                    value: 2,
                    label: '设防地震'
                }, {
                    value: 3,
                    label: '罕遇地震'
            }],
            earthquake_info: [{
                earthquake_no: '',
                name: '',
                peak:'',
                file:'',
            }]
        }
    }
}
</script>

<style scoped>

    body {
        font-family: Helvetica Neue, Helvetica, PingFang SC, Hiragino Sans GB, Microsoft YaHei, SimSun, sans-serif;
        overflow: auto;
        font-weight: 400;
        -webkit-font-smoothing: antialiased;
    }
    .tb-edit .el-input {
        display: none
    }
    .tb-edit .current-row .el-input {
        display: block
    }
    .tb-edit .current-row .el-input+span {
        display: none
    }
    .step5 .label{
        display: inline-block;
        /* width:80px; */
        color:#606266;
        font-size:14px;
        padding-bottom: 3px
        
    }
    .step5{
        padding: 10px 0
    }
</style>
