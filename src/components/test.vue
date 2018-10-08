<template>
    <div class="test">
        <div class="test-title">
            <div class="test-title-top">
                <img src="../assets/image/26418816209132599.jpg" alt="">
            </div>
            <div class="test-title-right">考试中</div>
            <p class="test-num-id">第<span>{{numId}}</span>题</p>
            <div class="progress-div">
                <progress  :max="max" :value="numId"></progress>
            </div>
        </div>
        <test-component :testInfo="topicItems[firstIndex]" @danxuan="radioMethod"></test-component>
        <div class="test-bottom">
            <button class="test-bottom-btn btn-prev" @click="prev()" :disabled="reduceDisabled">上一题</button>
            <button class="test-bottom-btn btn-next" @click="next()" :disabled="addDisabled">下一题</button>
        </div>
    </div>
</template>
<script>
    import testComponent from  '@/components/testComponent/testComponent'
    import VueCookies from 'Vue-cookies'
    import { Toast,MessageBox } from 'mint-ui';
    import axios from 'axios'
    export default {
        components:{
            testComponent:testComponent
        },
        data(){
            return{
                courseId:"",
                postType:false,
                max:4,
                activityId:"",
                firstIndex:0,
                addDisabled:false,
                reduceDisabled:true,
                numId:1,
                todo:"",
                ids:[],
                topicItems:[
                    {
                        content: "阿萨德发斯蒂芬",
                        courseId: "q1",
                        id: "s1",
                        img: "/sqlj-erp/userfiles/1/程序附件/sys/file/images/9TGZA[0WA$UY_LRJM8QV9{J.png",
                        options:[
                            "2", "1", "3","4"
                        ],
                        type: 1,
                    },
                    {
                        content: "1234我发撒的发生发",
                        courseId: "q2",
                        id: "s2",
                        img: "/sqlj-erp/userfiles/1/程序附件/sys/file/images/9TGZA[0WA$UY_LRJM8QV9{J.png",
                        options:[
                            "a1","a2","a3","a4"
                        ],
                        type: 1,
                    },
                    {
                        content: "去玩儿",
                        courseId: "q3",
                        id: "s3",
                        img: "/sqlj-erp/userfiles/1/程序附件/sys/file/images/9TGZA[0WA$UY_LRJM8QV9{J.png",
                        options:[
                            "duo1","duo2","duo3","duo4","duo5","duo6"
                        ],
                        type: 2,
                    },
                    {
                        content: "去玩儿222325325",
                        courseId: "q4",
                        id: "s4",
                        img: "/sqlj-erp/userfiles/1/程序附件/sys/file/images/9TGZA[0WA$UY_LRJM8QV9{J.png",
                        options:[
                            "q1","q2","q3","q4","q5","q6"
                        ],
                        type: 2,
                    }
                ],
                topics:[],
                reCheck:false
            }
        },
        methods:{
            prev(){
                console.log(this);
                if(this.numId<2){
                    this.reduceDisabled=true
                }else{
                    this.numId-=1;
                    this.firstIndex-=1
                }
                console.log(this.numId);
            },
            next(){
                if(this.numId<this.max){
                    this.numId+=1;
                    this.firstIndex+=1
                    this.reduceDisabled=false;
                }else if(this.numId==this.max){
                    //this.addDisabled=true;
                    MessageBox({
                        title: '您确认提交试卷么？',
                        message: '提交试卷前最好检查一遍,保证万无一失',
                        showCancelButton: true,
                        confirmButtonText:"提交",
                        cancelButtonText:"检查一下"
                    }).then(action=>{
                        if(action==="confirm"){
                            let postId=JSON.parse(VueCookies.get("clearList"))
                            console.log(postId)
                            let postData=[];
                            if(postId.length>0){
                                for(var j=0;j<postId.length;j++){
                                    let obj={
                                        "questionId":postId[j],
                                        "answerId":VueCookies.get(postId[j])
                                    }
                                    postData.push(obj)
                                }
                                console.log(postData);
                                Toast({
                                    message: '提交成功',
                                    duration: 500
                                });
                                setTimeout(()=>{
                                    this.$router.go(-1);
                                },1000)
                            }
                        }
                    });
                }
                console.log(this.numId);
            },
            radioMethod(obj){
                console.log(obj);
                this.ids.push(obj);
                if(this.ids.length>0){
                    this.ids.find((n)=>n)
                }
                console.log(this.ids);
            },
            init(){
                let idArray=[];
                for(var x=0;x<this.topicItems.length;x++){
                    idArray.push(this.topicItems[x].id);
                }
                console.log(idArray);
                VueCookies.set("clearList",JSON.stringify(idArray))
            }
        },
        watch:{
            numId(oldValue,newValue){
                if(oldValue===1){
                    console.log(111);
                    this.reduceDisabled=true
                }else if(oldValue===this.max){
                    console.log(321);
                    //this.addDisabled=true
                }else{
                    this.reduceDisabled=false;
                    this.addDisabled=false
                }
            }
        },
        created(){
            this.init();
        },
        beforeRouteLeave: (to, from, next) => {
            console.log("准备离开路由模板");
            console.log(to);
            console.log(from);
            let getPost= VueCookies.get("postType");
            console.log(getPost);
            if(!getPost){  //  如果是中途退出考试
                MessageBox({
                    title: '您确认返回么？',
                    message: '考试当中，返回您的答案将会清零',
                    showCancelButton: true,
                    confirmButtonText:"确定",
                    cancelButtonText:"取消"
                }).then(action=>{
                    console.log(action)
                    if(action==="confirm"){
                        let clearData=JSON.parse(VueCookies.get("clearList"));
                        console.log(clearData);
                        if(clearData.length>=0){
                            for(var i=0;i<clearData.length;i++){
                                console.log(clearData[i]);
                                VueCookies.remove(clearData[i])
                            }
                        }
                        next();
                    }
                });
            }
//            if(to.name=="detail" && !getPost){  // 中途退出考试，并且返回到detail
//                MessageBox({
//                    title: '您确认返回么？',
//                    message: '考试当中，返回您的答案将会清零',
//                    showCancelButton: true,
//                    confirmButtonText:"确定",
//                    cancelButtonText:"取消"
//                }).then(action=>{
//                    console.log(action)
//                    if(action==="confirm"){
//                        let clearData=JSON.parse(VueCookies.get("clearList"));
//                        console.log(clearData);
//                        if(clearData.length>0){
//                            for(var i=0;i<clearData.length;i++){
//                                console.log(clearData[i]);
//                                VueCookies.remove(clearData[i])
//                            }
//                        }
//                        next();
//                    }
//                });
//            }else if(to.name=="detail" && getPost){ // 考完试，并且返回到detail
//                let clearData=JSON.parse(VueCookies.get("clearList"));
//                console.log(clearData);
//                if(clearData.length>0){
//                    for(var i=0;i<clearData.length;i++){
//                        console.log(clearData[i]);
//                        VueCookies.remove(clearData[i])
//                    }
//                }
//                next();
//            }
//            else if(to.name=="traningCourse" && getPost){
//                let clearData=JSON.parse(VueCookies.get("clearList"));
//                console.log(clearData);
//                if(clearData.length>0){
//                    for(var i=0;i<clearData.length;i++){
//                        console.log(clearData[i]);
//                        VueCookies.remove(clearData[i])
//                    }
//                }
//                next();
//            }else if(to.name=="grade" && getPost){
//
//            }
            //next();
        },
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
    .test{
        background-image url("../../static/kaoshizhong_bg.png")
        background-repeat no-repeat
        background-size 100% 7.67rem
        background-color #eff6ff
        width 100%
        height 100vh
        padding-bottom 3.5rem
        .test-title{
            width 100%
            position relative
            text-align center
            padding-top 0.5rem
            .test-title-top{
                width 1.5rem
                height 1.5rem
                border-radius 50%
                border: 2px solid rgba(255,255,255,0.56);
                overflow hidden
                text-align center
                margin 0 auto
                img{
                    height 100%
                    width 100%
                }
            }
            .test-num-id{
                margin-top 0.5rem
                font-family: PingFangSC-Regular;
                font-size: 0.64rem;
                color: #FFFFFF;
            }
            .progress-div{
                border 1px solid #fff;
                width: 15rem;
                height 10px
                box-sizing border-box
                margin 0.5rem auto
                border-radius 5px
                position relative
                background: #FFFFFF;
                progress{
                    position absolute
                    top 1px
                    left 0
                    width 100%
                    height: 6px;
                    opacity: 0.93;
                    background: #FFFFFF;
                    border-radius: 3px;
                    &::-webkit-progress-inner-element {
                        border-radius: 3px;
                    }
                    &::-webkit-progress-bar {
                        border-radius: 3px;
                        background-color: #fff;
                    }
                    &::-webkit-progress-value{
                        background: #13CAFF;
                        border-bottom-left-radius: 3px;
                        border-top-left-radius: 3px;
                        border-bottom-right-radius: 3px;
                        border-top-right-radius: 3px;
                    }
                }

            }
            .test-title-right{
                position absolute
                right 0.5rem
                top 1rem
                opacity: 0.5;
                font-family: PingFangSC-Regular;
                font-size: 0.6rem;
                color: #FFFFFF;
            }
        }
        .test-bottom{
            position fixed
            width 100%
            height 2.5rem
            bottom 0
            background-color #fff
            display flex
            flex-flow row nowrap
            z-index 99
            justify-content space-around
            align-items center
            .test-bottom-btn{
                width 7.25rem
                height 1.8rem
                border-radius: 4px;
                font-family: PingFangSC-Regular;
                font-size: 0.64rem;
                box-shadow none
            }
            .btn-prev{
                background: #FFFFFF;
                border: 1px solid #2F86F7;
                color: #2F86F7;
            }
            .btn-next{
                background: #2F86F7;
                color #fff
                border 1px solid #2F86F7;
            }
        }
    }
    .mint-msgbox-title{
        font-family: PingFangSC-Regular;
        font-size: 16px;
        color: #333333;
        letter-spacing: 0.5px;
    }
    .mint-msgbox-message{
        font-family: PingFangSC-Regular;
        font-size: 13px;
        color: #666666;
        letter-spacing: 0.41px;
    }
    .mint-msgbox-cancel{
        background: #F8F8F8;
        font-family: PingFangSC-Regular;
        font-size: 15px;
        color: #666666;
        letter-spacing: 0.47px;
    }
    .mint-msgbox-confirm{
        background: #2F86F7;
        font-family: PingFangSC-Medium;
        font-size: 15px;
        color: #FFFFFF;
        letter-spacing: 0.47px;
    }
</style>
