<template>
    <div class="home">
        <div  class="course-item" v-for="item in comCourse" :key="item.id">
            <div class="course-item-box">
                <router-link :to="'/test/'+item.id" tag="div" class="course-link">
                    <div class="course-item-img-box">
                        <img :src="item.img" alt="">
                    </div>
                    <div class="course-item-content">
                         <p class="course-item-name">{{item.name}}</p>
                         <p class="course-item-time">
                             <img src="../assets/image/time.png" alt="">
                             <span v-if="item.fileType==3">无时长</span>
                              <span v-else-if="item.fileType==2">{{item.onlineCourseHours*60}}分钟</span>
                             <span v-else-if="item.fileType==1">无时长</span>
                         </p>
                        <div class="course-item-label">
                            <span class="course-item-label-item">选修</span>
                            <span class="course-item-label-item" v-if="item.fileType==2">视频</span>
                         <span class="course-item-label-item" v-else-if="item.fileType==1">PDF文件</span>
                        </div>
                     </div>
                 </router-link>
                <div class="course-item-right">
                    <img class="delete-img" src="../assets/image/delete.png" @click="deleteId(item.id)" alt="">
                </div>
        </div>
      </div>
    </div>
</template>
<script>
    export default {
        data(){
            return{
                comCourse:[
                    {id:"a1",img:"../../static/001.jpg",name:"zcxasdvda",fileType:1},
                    {id:"a2",img:"../../static/001.jpg",name:"hggfhjfghgfhf",fileType:1}
                ]
            }
        }
    }
</script>
<style lang="stylus" rel="stylesheet/stylus">
  .course-item {
    background-color #fff
    padding 0.5rem
    margin-top 0.4rem
    border-radius 4px
    .course-item-box{
      display flex
      flex-flow row nowrap
      .course-link{
        display flex
        flex-flow row nowrap
        .course-item-img-box{
          width 3.4rem
          height 3.4rem
          border-radius 5px
          overflow hidden
          img{
            width 100%
            height 100%
          }
        }

        .course-item-content{
          width 7.5rem
          padding-left 0.4rem
          position relative
          .course-item-name{
            font-family: PingFangSC-Medium;
            font-size: 0.68rem;
            color: #333333;
            width 100%
            text-overflow ellipsis
            display -webkit-box
            -webkit-line-clamp 1
            -webkit-box-orient vertical
            overflow hidden
          }
          .course-item-time{
            font-family: PingFangSC-Regular;
            font-size: 0.6rem;
            color: #999999;
            display flex
            margin-top 0.1rem
            align-items center
            img{
              width 0.65rem;
              height 0.65rem
            }
            span{
              margin-left 0.5rem
            }
          }
          .course-item-label{
            position absolute
            bottom 0.1rem
            .course-item-label-item{
              font-family: PingFangSC-Regular;
              font-size: 0.57rem;
              color: #999999;
              background: #FFFFFF;
              border: 1px solid #DDDDDD;
              border-radius: 2px;
              padding 0 3px;
              padding-top 1px;
              &:not(:first-child){
                margin-left 0.2rem
              }
            }
          }
        }
      }
      .course-item-right{
        font-family: PingFangSC-Regular;
        font-size: 0.57rem;
        width 3rem
        text-align right
        line-height: 1.1rem;
        /*margin-left 0.45rem*/
        position relative
        .delete-img{
          position absolute
          bottom 0.2rem
          right 0.1rem
          width 0.85rem
          height 0.85rem
          z-index 99
        }
        .pass-img{
          width 2.8rem;
          height 2.5rem;
        }
        .yellow{
          color: #FFA736;
        }
        .red{
          color: #F72F2F;
        }
        .blue{
          color: #2F86F7;
        }
      }
    }
  }
</style>
