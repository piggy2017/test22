<template>
    <div class="testComponent">
        <div class="question-box">
            <div class="question-box-top">
                <div class="question-box-top-type" v-if="testInfo.type==1">
                    单项选择题
                </div>
                <div class="question-box-top-type" v-else-if="testInfo.type==2">
                    多项多选题
                </div>
                <div class="question-box-top-type" v-else-if="testInfo.type==3">
                    判断题
                </div>
                <div class="question-box-top-type" v-else-if="testInfo.type==4">
                    问答题
                </div>
                <p class="question-box-top-text">
                    {{testInfo.content}}
                </p>
            </div>
            <!--testInfo.type==1单项选择题-->
            <div class="answer-btn-box" v-if="testInfo.type==1">
                <div v-for="(item,index) in testInfo.options" class="check-btn-item"
                     :class="[todo==item? 'check-btn-item-active':'']"
                     @click.stop="choose(testInfo.id,item)"
                     v-show="item"
                >
                    <span>{{replaceText(index+1)}}</span>
                    <span>{{item}}</span>
                </div>
            </div>
            <!--testInfo.type==3 判断题-->
            <div class="answer-btn-box" v-else-if="testInfo.type==3">
                <div class="answer-btn-item text-center" :class="[judge==1?'answer-btn-item-active':'']" @click="chooseType(1,testInfo.id)">正确</div>
                <div class="answer-btn-item text-center" :class="[judge==0?'answer-btn-item-active':'']" @click="chooseType(0,testInfo.id)">错误</div>
            </div>
            <!--testInfo.type==2 多项选择题-->
            <div class="answer-btn-box" v-else-if="testInfo.type==2">
                <div v-for="(item,index) in testInfo.options" class="answer-btn-item"
                     v-show="item"
                     @click.stop="check($event,testInfo.id,item)">
                    <p class="xxxx">
                        <span>{{replaceText(index+1)}}</span>
                        <span>{{item}}</span>
                    </p>
                </div>
            </div>
            <!--testInfo.type==4  问答题-->
            <div class="answer-btn-box" v-else-if="testInfo.type==4">
                <textarea autofocus name="" v-model="textValue" id="" v-on:blur.lazy="getValue(testInfo.id)" class="text-area" rows="10" placeholder="请在这儿输入答案">
                </textarea>
            </div>
        </div>
    </div>
</template>
<script>
    import VueCookies from 'Vue-cookies'
    export default {
        data(){
            return{
                todo:-1,
                checkList:[],
                textValue:"",
                judge:""
            }
        },
        props:{
            testInfo:{
                type:Object
            }
        },
        methods:{
            replaceText(_index){
                if(_index==1){
                    return "A："
                } else if(_index==2){
                    return "B："
                } else if(_index==3){
                    return "C："
                } else if(_index==4){
                    return "D："
                } else if(_index==5){
                    return "E："
                } else if(_index==6){
                    return "F："
                } else if(_index==7){
                    return "G："
                } else if(_index==8){
                    return "H："
                }
            },
            choose(parentId,text){
                console.log(text);
                this.todo=text;
                console.log(text);
                VueCookies.set(parentId,text);
            },
            chooseType(type,parentId){
                this.judge=type;
                VueCookies.set(parentId,type);
            },
            check(e,parentId,self){
                e.cancelBubble = true;
                console.log(parentId,self);
                console.log(e.currentTarget.classList);
                let classBox=e.currentTarget.classList;
                if(classBox.length==2){
                    console.log("取消class")
                    e.currentTarget.classList.remove('check-btn-item-active');
                    let _this=this;
                    for(var i=0;i<_this.checkList.length;i++){
                        if(_this.checkList[i]==self){
                            _this.checkList.splice(i,1);
                        }
                    }
                    console.log(_this.checkList);
                }else if(classBox.length==1){
                    e.currentTarget.classList.add('check-btn-item-active');
                    this.checkList.push(self);
                    console.log(this.checkList);
                }else if(classBox.length==0){
                    console.log(3333)
                }
                console.log(this.checkList);
                VueCookies.set(parentId,JSON.stringify(this.checkList));
            },
            getValue(parentId){
                console.log(this.textValue);
                if(this.textValue){
                    VueCookies.set(parentId,this.textValue);
                }
            }
        },
        watch:{
            testInfo(newValue,oldValue){
                console.log(newValue);
                console.log(oldValue);
                if(newValue.type==1){  // 单选
                    this.todo=-1;
                    let hasChoose=VueCookies.get(newValue.id);
                    console.log(hasChoose);
                    this.$nextTick(()=>{
                        this.todo=hasChoose;
                        console.log(this.todo);
                    })
                    return
                }else if(newValue.type==3){  // 判断
                    this.judge="";
                    let hasJudge=VueCookies.get(newValue.id);
                    console.log(hasJudge);
                    this.$nextTick(()=>{
                        this.judge=hasJudge;
                        console.log(this.judge);
                    })
                }else if(newValue.type==2){ // 多选
                    let hasChek= JSON.parse(VueCookies.get(newValue.id));
                    console.log(hasChek)
                    let nodeList=document.getElementsByClassName("answer-btn-item");
                    console.log(nodeList);
                    if(hasChek){
                        this.checkList=hasChek;
                        console.log("test");
                        this.$nextTick(()=>{
                            for(var i=0;i<hasChek.length;i++) {
                                for(var j=0;j<nodeList.length;j++){
                                    if(hasChek[i]==nodeList[j].children[0].children[1].innerText){
                                        nodeList[j].classList.add('check-btn-item-active');
                                    }
                                }
                            }
                        })
                    }else{
                        this.checkList=[];
                        for(let i=0;i<nodeList.length;i++){
                            nodeList[i].classList.remove("check-btn-item-active");
                        }
                    }
                }else if(newValue.type==4){ // 问答
                    console.log(444)
                    let getText=VueCookies.get(newValue.id);
                    console.log(getText);
                    if(getText){
                        this.textValue=getText;
                    }else{
                        this.textValue="";
                    }
                }
            }
        },
        mounted(){
            //console.log(123123123);
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
.testComponent{
    .question-box{
        padding 0.5rem
        .question-box-top{
            background: #FFFFFF;
            box-shadow: 0 2px 4px 0 rgba(20,98,200,0.22);
            border-radius: 8px;
            font-family: PingFangSC-Regular;
            font-size 0.64rem
            padding 0.5rem
            .question-box-top-type{
                color: #2F86F7;
                font-size 0.68rem
                position relative
                padding-left 0.6rem
                &:before{
                    position absolute
                    content ""
                    left 0
                    top 0.3rem
                    width 8px
                    height 8px
                    border-radius 50%
                    background: #2F86F7;
                }
            }
            .question-box-top-text{
                color: #555555;
                line-height 0.95rem
                padding-top 0.3rem
            }
        }
        .answer-btn-box{
            margin-top 0.3rem
            .answer-btn-item{
                background: #FFFFFF;
                box-shadow: 0 2px 4px 0 rgba(20,98,200,0.22);
                border-radius: 6px;
                padding 0.5rem
                font-family: PingFangSC-Regular;
                font-size: 0.68rem;
                color: #222222;
                margin-top 0.5rem
            }
            .check-btn-item{
                background: #FFFFFF;
                box-shadow: 0 2px 4px 0 rgba(20,98,200,0.22);
                border-radius: 6px;
                padding 0.5rem
                font-family: PingFangSC-Regular;
                font-size: 0.68rem;
                color: #222222;
                margin-top 0.5rem
                position relative
            }
            .check-btn-item-active{
                color: #FFFFFF;
                background: #2F86F7;
                position relative
                &:after{
                    position absolute
                    content ""
                    right .5rem
                    background-image url("../../assets/image/checked.png")
                    background-size 1.28rem 1.28rem
                    bottom 0.3rem
                    width 1.28rem
                    height 1.28rem
                }
            }
            .text-center{
                text-align center
            }
            .answer-btn-item-active{
                color: #FFFFFF;
                background: #2F86F7;
                position relative
                &:after{
                    position absolute
                    content ""
                    right .5rem
                    background-image url("../../assets/image/checked.png")
                    background-size 1.28rem 1.28rem
                    bottom 0.3rem
                    width 1.28rem
                    height 1.28rem
                }
            }
            .text-area{
                width 14rem
                background: #FFFFFF;
                box-shadow: 0 2px 4px 0 rgba(20,98,200,0.22);
                border-radius: 6px;
                border none
                padding 0.5rem
                font-family: PingFangSC-Regular;
                font-size: 0.64rem;
                color: #AAAAAA;
            }
        }
    }
}
</style>
