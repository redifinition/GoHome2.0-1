<!--
 * @Author: mount_potato
 * @Date: 2021-06-09 22:57:13
 * @LastEditTime: 2021-07-13 02:21:50
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \proto\src\views\favorites.vue
-->


<template>
    <div class="main">
        <!-- 引入动画 -->
        <link
            rel="stylesheet"
            href="https://cdnjs.cloudflare.com/ajax/libs/animate.css/4.1.1/animate.min.css"/>
        <!-- 心愿单标题与按钮 -->
        <el-row style="margin-bottom:20px;">
            <el-col float="left" span='20' offset='2' > 
                <h2 class="favor-main-title">{{user_name}} 的收藏夹</h2>
                <el-button type="primary" class="create-button" icon="el-icon-folder-add" @click="add_new">创建新的收藏夹</el-button>
            </el-col>        
        </el-row>

        <el-divider></el-divider>
        <!-- 收藏夹列表 -->
        <div v-if="this.favorite_list.length==0">
                <img class="empty-img" src="https://oliver-img.oss-cn-shanghai.aliyuncs.com/img/f7c588076d9dc58d2ce4170920301c90.png">
                <p>还没有创建收藏夹哦，快创建一个吧!</p>
        </div>
        <div v-else class="card-list">
            <el-row :gutter='30'>
                <el-col :span="6" v-for='(item,index) in favorite_list'
                                    :key='item.id' 
                                    :offset=" index %3==0 ? 1 : 2 "  
                                    style="margin-bottom:40px;" >
                    <el-card :body-style="{ padding: '0px' }" shadow="hover"  @click.native="jump_to_oneFav(item)">
                        <img v-if="item.imgUrl=='' || item.imgUrl==null " src="https://oliver-img.oss-cn-shanghai.aliyuncs.com/img/9074398134361ed01c5bd7c2d917934a.png" class="emp-image">
<!--                        https://oliver-img.oss-cn-shanghai.aliyuncs.com/img/f8cc5e654d8f69d1353e2a4833dd3a38.jpg-->
                        <img v-else :src="item.imgUrl" class="image">
                        <div style="padding: 14px;">

                            <div class="top-clearfix">
                                <span class="time">共有{{ item.totalStay }}个房源</span>
                            </div>
                            <span class="favor-title">{{item.name}}</span>
                            <div class="bottom clearfix">
                            
                            </div>
                        </div>
                    </el-card>
                </el-col>
            </el-row>
            <h3 style="margin-top:220px;">...</h3>
    
            <el-divider></el-divider>
        </div>
    </div>
</template>

<script>
import { GetFavorite,InsertFavorite,GetFavoriteImage } from '@/api/favorite';
export default {

    created:function(){
        let userId=localStorage.getItem('userId');
        if (userId == null || userId == ''){
            this.$message({
              message: "您尚未登录，请先登录",
              type: "warning",
            });
            this.$router.push({
                path: "/"
            })
            return;
        }
        GetFavorite().then(response=>{
            this.favorite_list=response.data;
            console.log(this.favorite_list)
        }).catch(error=>{
            console.log("fail");
            console.log(error);
            this.$message.error("网络异常，请稍后重试");
        })
    },

    methods:{
        add_new(){

            this.$prompt('新收藏夹名字', '新建收藏夹', {
            confirmButtonText: '确定',
            cancelButtonText: '取消',
            inputPlaceholder:"不超过10个字",
            inputValidator(value){
                if(value==undefined){
                    return '名字不能为空'
                }else if(value.length==0){
                    return '名字不能为空'
                }else if(!value.match('^[a-zA-Z0-9_\u4e00-\u9fa5]+$')){
                    return '不支持特殊字符'
                }else if(value.length>10){
                    return '请输入不超过10个字的名字'
                }
            },
            inputErrorMessage: '格式不正确',
            center: true
            }).then(({ value }) => {
                console.log("value为",value)
                //插入
                InsertFavorite({favoriteName:String(value)}).then(response=>{
                    this.$message({
                        type: 'success',
                        message: '新的收藏夹名字是: ' + value
                    });     
                    console.log("在此为ok")
                    GetFavorite().then(response=>{
                        console.log("在此为ok2")
                        this.favorite_list=response.data;
                    })
                }).catch(error=>{
                    console.log(error);
                    this.$message.error("错误:插入了重名的文件夹");
                })

            }).catch(() => {
                this.$message({
                    type: 'info',
                    message: '已取消'
                });       
            });
        },
        jump_to_oneFav(item){
            let n=item.name;
            let id=item.favoriteId;
            console.log(n,id);
            //通过路由的path+query传值，待改
            this.$router.push({
                path: '/oneFavPage',
                query: {
                    favName: n,
                    favID:id,
                }
            })
        }
    },

    data() {
    return {
        favorite_list: [],
        url_list:[],
        user_name:localStorage.getItem('userName'),
    };
  }
  
}
</script>

<style scoped>

.main{
    /* background-image: url("../assets/pexels-jeffrey-czum-2904142-removebg.png");
    background-attachment:fixed;


    background-size: cover;
    -webkit-background-size: cover;
    -o-background-size: cover;
    top: 0;
    left: 0;
    width:100%;
    height:100%;
    background-size:100% 100%;

    background-color: rgba(255, 255, 255, 0.4); */
}

.main::after{
    opacity: 0.4;
}

.card-list{
    /* background-image: url("https://oliver-img.oss-cn-shanghai.aliyuncs.com/img/07e2ee2c5681b0312f77ca317938f25d.jpg");
     */
    background-image: url("https://oliver-img.oss-cn-shanghai.aliyuncs.com/img/svg(2).png");
    background-attachment:fixed;


    background-size: cover;
    -webkit-background-size: cover;
    -o-background-size: cover;
    top: 0;
    left: 0;
    width:100%;
    height:100%;
    background-size:100% 100%;

    background-color: rgba(255, 255, 255, 0.4);

    animation: fadeIn;
    animation-duration: 2s;
}

.time {
    
    font-size: 13px;
    color: #999;
    text-align: left;
    float:left;
    padding:3px;
}

.favor-main-title{
    font-size: 30px;
    float:left;
    text-align: left;
    font-weight: bold;
    font-family:"PingFang SC";

    animation: fadeInDown;
    animation-duration: 1s;
}


.create-button{
    float:right;
    background-color: black;
    margin-top:40px;
    animation: fadeInDown;
    animation-duration: 1s;
}
.create-button:hover{
    background-color: rgba(0, 0, 0, 0.205);
    /* margin-top:-0.1px; */
}

.create-button:focus{
    background-color: black;
}

.bottom {
    margin-top: 15px;
    line-height: 12px;
}

.button {
    padding: 0;
    float: right;
}

.image {
    height: 80%;
    width: 100%;
    width: 400px;
    height: 200px;  
    display:block;
    opacity: 0.8;
}

.emp-image{
    margin-left:80px;
    width: 200px;
    height: 200px;  
    display:block;
    opacity: 0.8;
}

.clearfix:before,
.clearfix:after {
    display: table;
    content: "";
}

.clearfix:after {
    clear: both
}

.el-divider{
    animation: fadeInDown;
    animation-duration: 1s;
}

.el-card{
    background-color: rgba(255, 255, 255, 1);
    border-radius: 15px;
    border: 3px solid #000000;
    height: 100%;
    width: 100%;
    vertical-align: top;
    cursor:pointer;

    animation: fadeInLeft;
    animation-duration: 1.5s;
}

.el-card:hover{
    box-shadow: 0 10px 10px 0 rgba(0, 0, 0, 0.8), 0 6px 20px 0 rgba(0, 0, 0, 0.79);

}

.el-card-define {
  min-height: 100%;
  height: 100%;
}
.el-card-define >>> .el-card__body {
  height: 100%;
}


.favor-title{

    float:right;
    font-size:20px;
    font-weight: bold;
    font-family:"PingFang SC";
    margin-top:10px;
}

</style>

<style>
    .el-message-box{
        width: 350px;
    }
</style>
