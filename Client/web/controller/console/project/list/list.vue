<template>
    <el-row class="row">
        <template v-if="bEmpty">
            <el-row class="row" style="text-align: center;margin-top: 100px;color: gray">
                <i class="fa fa-list-alt" style="font-size: 60px"></i><br><br>
                <span style="font-size: 14px">还没有项目，点击下方按钮新增或者导入项目</span><br><br>
                <el-dropdown  v-if="session.teamId">
                    <el-button type="primary" size="small" style="margin-left: 20px;">
                        <i class="el-icon-plus" style="font-weight:900"></i>&nbsp;新增项目
                    </el-button>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item @click.native="showAdd=true">新项目</el-dropdown-item>
                        <el-dropdown-item @click.native="addExistProject">已有项目</el-dropdown-item>
                    </el-dropdown-menu>
                </el-dropdown>
                <el-button type="primary" size="small" @click="showAdd=true" v-else>
                    <i class="el-icon-plus" style="font-weight:900"></i>&nbsp;新增项目
                </el-button>
                <el-button size="small" style="margin-left: 10px;" @click="importProject">
                    <i class="el-icon-download" style="font-weight:900"></i>&nbsp;导入项目
                </el-button>
            </el-row>
        </template>
        <tempalte v-else>
            <el-row class="row" style="height: 50px;line-height: 50px;padding-right: 20px">
                <el-dropdown  v-if="session.teamId">
                    <el-button type="primary" size="small" style="margin-left: 20px;">
                        <i class="el-icon-plus" style="font-weight:900"></i>&nbsp;新增项目
                    </el-button>
                    <el-dropdown-menu slot="dropdown">
                        <el-dropdown-item @click.native="showAdd=true">新项目</el-dropdown-item>
                        <el-dropdown-item @click.native="addExistProject">已有项目</el-dropdown-item>
                    </el-dropdown-menu>
                </el-dropdown>
                <el-button type="primary" size="small" style="margin-left: 20px;" @click="showAdd=true" v-else>
                    <i class="el-icon-plus" style="font-weight:900"></i>&nbsp;新增项目
                </el-button>
                <el-button size="small" style="margin-left: 10px;" @click="importProject">
                    <strong><i class="el-icon-download" style="font-weight:900"></i></strong>&nbsp;导入项目
                </el-button>
                <el-button size="small" type="text" style="float: right;margin-right: 10px;margin-top: 15px;color: gray">
                    <div style="display: inline-block;height: 10px;width: 10px;border-radius: 5px;background-color: #67C23A;"></div>&nbsp;观察者
                </el-button>
                <el-button size="small" type="text" style="float: right;margin-right: 10px;margin-top: 15px;color: gray">
                    <div style="display: inline-block;height: 10px;width: 10px;border-radius: 5px;background-color: #17b9e6;"></div>&nbsp;管理员
                </el-button>
            </el-row>
            <el-row class="row" style="margin: 10px 20px 10px 20px;width: calc(100% - 40px);height: calc(100vh - 120px);overflow-y: auto">
                <template v-if="!session.teamId">
                    <expand :expand="1" style="background-color: white;border-radius: 5px;box-shadow: 0 2px 4px 0 rgba(0,0,0,.12), 0 0 6px 0 rgba(0,0,0,.04);" v-if="arrCreate.length>0">
                        <div slot="title" style="font-size: 14px">
                            我创建的项目
                        </div>
                        <el-radio-group v-model="$store.state.projectCreateSort" size="mini" slot="append" style="margin-right: 20px" @change="changeCreateSort">
                            <el-radio-button :label="0">时间&nbsp;↓</el-radio-button>
                            <el-radio-button :label="1">名称&nbsp;↑</el-radio-button>
                        </el-radio-group>
                        <el-row class="row">
                            <list type="create" ref="createList" key="create"></list>
                        </el-row>
                    </expand>
                    <expand :expand="1" style="background-color: white;margin-top: 20px;border-radius: 5px;box-shadow: 0 2px 4px 0 rgba(0,0,0,.12), 0 0 6px 0 rgba(0,0,0,.04);" v-if="arrJoin.length>0">
                        <div slot="title" style="font-size: 14px">
                            我加入的项目
                        </div>
                        <el-radio-group v-model="$store.state.projectJoinSort" size="mini" slot="append" style="margin-right: 20px" @change="changeJoinSort">
                            <el-radio-button :label="0">时间&nbsp;↓</el-radio-button>
                            <el-radio-button :label="1">名称&nbsp;↑</el-radio-button>
                        </el-radio-group>
                        <el-row class="row">
                            <list type="join" ref="joinList" key="join"></list>
                        </el-row>
                    </expand>
                    <expand :expand="1" style="background-color: white;margin-top: 20px;border-radius: 5px;box-shadow: 0 2px 4px 0 rgba(0,0,0,.12), 0 0 6px 0 rgba(0,0,0,.04);" v-if="arrPublic.length>0">
                        <div slot="title" style="font-size: 14px">
                            公开的项目
                        </div>
                        <el-radio-group v-model="$store.state.projectPublicSort" size="mini" slot="append" style="margin-right: 20px" @change="changePublicSort">
                            <el-radio-button :label="0">时间&nbsp;↓</el-radio-button>
                            <el-radio-button :label="1">名称&nbsp;↑</el-radio-button>
                        </el-radio-group>
                        <el-row class="row">
                            <list type="public" ref="publicList" key="public"></list>
                        </el-row>
                    </expand>
                </template>
                <expand :expand="1" style="background-color: white;border-radius: 5px;box-shadow: 0 2px 4px 0 rgba(0,0,0,.12), 0 0 6px 0 rgba(0,0,0,.04);" v-else>
                    <div slot="title" style="font-size: 14px">
                        团队中的项目
                    </div>
                    <el-radio-group v-model="$store.state.projectTeamSort" size="mini" slot="append" style="margin-right: 20px" @change="changeTeamSort">
                        <el-radio-button :label="0">时间&nbsp;↓</el-radio-button>
                        <el-radio-button :label="1">名称&nbsp;↑</el-radio-button>
                    </el-radio-group>
                    <el-row class="row">
                        <list type="team" ref="teamList" key="team"></list>
                    </el-row>
                </expand>
            </el-row>
        </tempalte>
        <el-dialog title="新建项目" :visible.sync="showAdd" width="50%" append-to-body>
            <el-form label-position="top" ref="form" label-width="100px">
                <el-form-item label="名称">
                    <el-input  style="width: 100%"  v-model="name" placeholder="请输入新项目的名称"></el-input>
                </el-form-item>
                <el-form-item label="描述">
                    <el-input type="textarea" :rows="2"  style="width: 100%"  v-model="dis" placeholder="请输入新项目的简介"></el-input>
                </el-form-item>
            </el-form>
            <span slot="footer" class="dialog-footer">
            <el-button @click="showAdd = false">取 消</el-button>
            <el-button type="primary" @click="addProject" :loading="addPending">确 定</el-button>
        </span>
        </el-dialog>
    </el-row>
</template>

<script>
    var store=require("../../store")._modulesNamespaceMap["project/list/"].context;
    var sessionChange=require("common/mixins/session");
    var refresh=require("common/mixins/refresh");
    var expand=require("component/expand.vue");
    var list=require("./component/list.vue");
    var importProject=require("./component/import.vue")
    module.exports={
        data:function () {
            return {
                showAdd:false,
                name:"",
                dis:"",
                addPending:false,
            }
        },
        mixins:[sessionChange,refresh],
        store:store,
        watch:{

        },
        components:{
            "expand":expand,
            "list":list
        },
        computed:{
            bEmpty:function () {
                if(this.session.teamId)
                {
                    if(store.state.projectTeamList.length==0)
                    {
                        return true;
                    }
                    else
                    {
                        return false;
                    }
                }
                else
                {
                    if(store.state.projectCreateList.length==0 && store.state.projectJoinList.length==0 && store.state.projectPublicList.length==0)
                    {
                        return true;
                    }
                    else
                    {
                        return false;
                    }
                }
            },
            arrCreate:function () {
                return store.state.projectCreateList
            },
            arrJoin:function () {
                return store.state.projectJoinList
            },
            arrPublic:function () {
                return store.state.projectPublicList
            }
        },
        methods:{
            changeCreateSort:function () {
                this.$refs.createList.changeSort();
            },
            changeJoinSort:function () {
                this.$refs.joinList.changeSort();
            },
            changePublicSort:function () {
                this.$refs.publicList.changeSort();
            },
            changeTeamSort:function () {
                this.$refs.teamList.changeSort();
            },
            addProject:function () {
                if(!this.name)
                {
                    this.$message.error("请输入名称");
                    return;
                }
                var _this=this;
                this.addPending=true;
                store.dispatch("addProject",{
                    name:this.name,
                    dis:this.dis
                }).then(function (data) {
                    _this.addPending=false;
                    _this.name="";
                    _this.dis=""
                    if(data.code==200)
                    {
                        $.notify("创建成功",1);
                        _this.showAdd=false;
                    }
                    else
                    {
                        $.notify(data.msg,0);
                    }
                })
            },
            importProject:function () {
                $.showBox(this,importProject)
            },
            addExistProject:function () {
                $.input("请输入已有项目的项目ID",function (val) {
                    if(!val.value)
                    {
                        $.tip("请输入项目ID",0);
                        return false
                    }
                    $.startHud();
                    net.put("/team/pullproject",{
                        id:session.get("teamId"),
                        project:val.value
                    }).then(function (data) {
                        $.stopHud();
                        if(data.code==200)
                        {
                            $.notify("请求已发出，等待项目管理员响应",1);
                        }
                        else
                        {
                            $.notify(data.msg,0);
                        }
                    })
                })
            }
        }
    }
</script>