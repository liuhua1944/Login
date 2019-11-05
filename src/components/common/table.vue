<template>
    <div>
        <div class="table">
            <div class="container">
                <div class="toolbar" v-if='show_toolbar'>
                    <el-button type="primary" icon="el-icon-plus" class="handle-del mr10" @click="on_add"></el-button>
                    <div class='toolbar-right'>
                        <el-button type="primary"  icon="el-icon-search" size="small" @click="on_search"></el-button>
                    </div>

                    <slot name='search'></slot>
                </div>

                <el-table :summary-method="get_sum" :show-summary="show_summary" :max-height="height" @selection-change="on_select" highlight-current-row @current-change='on_rowchange' 	:data="rows" border class="table" ref="multipleTable" >
                    <el-table-column
                        type="selection"
                        width="50">
                    </el-table-column>
                    <el-table-column
                        fixed
                        type="index"
                        width="50">
                    </el-table-column>
                    <el-table-column v-for='f in fs_show'
                                     :prop=f.prop
                                     :label=f.label
                                     :sortable=f.sortable
                                     :fixed=f.fixed
                                     :width=f.width
                                     :formatter=f.formatter
                    >
                    </el-table-column>
                    <el-table-column type="expand" v-if='show_detail'>
                        <template slot-scope="props">
                            <el-form label-position="right" inline class="table-expand" width='200px' labelWidth='80px'>
                                <el-form-item v-for='f in fs'
                                              :label=f.label class="table-expand-label">
                                    <div class='table-extend-value'>{{ f.formatter(0,0,props.row[f.prop])}}</div>
                                </el-form-item>
                            </el-form>
                        </template>
                    </el-table-column>

                    <el-table-column v-if='show_op' label="操作" width="200" align="center">
                        <template slot-scope="scope">
                            <el-button v-if='show_limit' circle type="primary" icon="el-icon-lx-goods" @click="on_limit(scope.$index,scope.row)"></el-button>
                            <el-button circle  type="info" icon="el-icon-info" @click="on_info(scope.$index, scope.row)"></el-button>
                            <el-button v-if='show_edit' circle  type="warning" icon="el-icon-edit" @click="on_edit(scope.$index, scope.row)"></el-button>
                            <el-button v-if='show_edit' circle type="danger" icon="el-icon-delete"  @click="on_delete(scope.$index, scope.row)"></el-button>
                        </template>
                    </el-table-column>
                </el-table>
                <div class="pagination">
                    <el-pagination v-if='show_pager' background @current-change="on_page" @size-change='on_pagesize' layout="prev, pager, next,jumper,sizes,total" :total="total">
                    </el-pagination>
                </div>
            </div>

            <!-- 附件弹出框 -->
            <el-dialog title="附件" :visible.sync="a_visible" width="70%">
                <vattachment v-bind:uid='uid' :enable='a_disable'></vattachment>
                <span slot="footer" class="dialog-footer">
                <el-button type="primary" @click="a_visible = false">返回</el-button>
            </span>
            </el-dialog>

            <slot name='edit'></slot>

        </div>

    </div>
</template>

<script>
    import {mapState} from 'vuex'

    export default {

        props:{
            fs:{
                type:Array,
                required:true
            },
            rows:{
                type:Array,
                required:true
            },
            height:{
                type:Number
            },
            show_summary:{
                type:Boolean
            },
            get_sum:{
                type:Function
            },

            show_check:{
                type:Boolean
            },
            show_toolbar:{
                type:Boolean
            },
            show_pager:{
                type:Boolean
            },
            show_op:{
                type:Boolean
            },
            show_detail:{
                type:Boolean
            },
            show_limit:{
                type:Boolean,
                default:true
            },
            show_edit:{
                type:Boolean,
                default:true
            },


            total:{
                type:Number
            },
            pagesize:{
                type:Number
            }
        },
        watch:{
            rows:{
                handler(n,o){
                    this.rows = n
                },
                immediate: true
            },
            total:{
                handler(n,o){
                    this.total = n
                },
                immediate:true

            }
        },

        data() {
            return {
                //附件
                uid:"0",
                a_disable:false,
                a_visible:false,

                page:-1,
            }
        },
        computed:{
            fields(){
                return this.fs
            },
            fs_show(){
                return this.fs.filter(function(f){return f.show == true})
            },
        },
        methods: {

            on_select(val){
                this.$emit('select',val)
            },
            on_rowchange(r,o){
                this.$emit('rowchange',r,o)
            },
            on_search(){
                this.$emit('search',this.page,this.pagesize)
            },
            on_add(){
                this.$emit('add',this.page,this.pagesize)
            },
            on_info(index,row){
                this.$emit('info',index, row,this.page,this.pagesize)
            },
            on_edit(index,row){
                this.$emit('edit',index, row,this.page,this.pagesize)
            },
            async on_delete(index, row){
                let r = await this.$u.confirm('要删除该记录吗')
                if(r){
                    this.$emit('delete',index, row,this.page,this.pagesize)
                }
            },
            on_limit(index, row){
                this.$emit('limit',index, row,this.page,this.pagesize)

            },
            // 分页导航
            on_page(val) {
                this.page = val

                this.$emit('page',this.page,this.pagesize)
            },
            on_pagesize(val){
                this.pagesize = val
            }

        },
        created() {
        }
    }
</script>

<style scoped>
    .handle-box {

        margin-bottom: 10px;
        width: 100%;
        padding: 5px;
        background-color: #ffffff;
        overflow: hidden;
        line-height: 32px;
        border: 2px solid #e6ebf5;
    }

    .handle-select {
        width: 120px;
    }

    .handle-input {
        width: 200px;
        display: inline-block;

    }
    .del-dialog-cnt{
        font-size: 16px;
        text-align: center
    }
    .table{
        width: 100%;
        font-size: 14px;
    }
    .red{
        color: #ff0000;
    }

    .table-expand {
        font-size: 10;
    }
    .table-expand-label {
        width: 120px;
        color: #00aabf;

    }
    .table-extend-value{
        border-bottom: 2px outset rgba(72,124,164,0.51);
    }
    .table-expand .el-form-item {
        margin-right: 0;
        margin-bottom: 0;
        width: 50%;
    }
</style>
