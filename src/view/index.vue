<template>
    <div class="index-page" ref="imageWrapper" >
        <div class="img-box">
            <div class="vue-baberrage">
                <vue-baberrage
                    :isShow= "barrageIsShow"
                    :barrageList = "barrageList"
                    :loop = "barrageLoop"
                    :throttleGap="10000"
                    :boxHeight="680"  
                    :messageHeight="60"
                    :maxWordCount="50"
                    @barrage-list-empty="sayHi"
                    style="margin-top:5%;height:85%;margin-left:-30%"
                >
                </vue-baberrage>
            </div>
            <div class="eq-code-box">
                <img src="@/assets/img/index/EQ-code.jpg" alt="" srcset="">
                <div class="saoma">扫码参与弹幕互动</div>
            </div>
        </div>
    </div>
</template>
<script>
import qs from 'qs'
import Vue from 'vue'
import { vueBaberrage } from 'vue-baberrage'
import html2canvas from "html2canvas"
Vue.use(vueBaberrage)
export default {
    data(){
        return{
            barrageIsShow: true, 
            barrageLoop: false,
            ticket:'0',
            barrageList:[],
            time:15,
            timer1:null,
            getImg:'',
            arrFloag:false,
            t:0
        }
    },
    mounted(){
        this.addToList(this.ticket);
    },
    created: function() {
        document.onkeydown = function(e){
            let key = window.event.keyCode;
            switch (key) {
                case 49:
                    window.location.href= 'https://bsd2021.lehuitech.com.cn/1211Screen/pc_1.html';
                    break;
                case 50:
                    window.location.href= 'https://bsd2021.lehuitech.com.cn/1211Screen/pc_2.html';
                    break;
                case 51:
                    window.location.href= 'https://bsd2021.lehuitech.com.cn/1211Screen/pc_3.html';
                    break;
                case 52:
                    window.location.href= 'https://bsd2021.lehuitech.com.cn/1211Screen/pc_4.html';
                    break;
                default:
                    break;
            }
        };
    },
    methods:{
        addToList (ticket){
            let that  = this;
            let promise = {
                meetingname:'bsd2021',
                ticket:ticket,
                top:'1000',//条数
            }
            //GetMessageList(循环) GetMessageListOnlyONE(不循环)
            that.axios.post('danmu/GetMessageList', qs.stringify(promise))
            .then(function (res) {
                if(res.status == 200 ){
                    if(res.data.data.list && res.data.data.list.length>0){
                        console.log('ticket'+that.ticket == res.data.data.ticket)
                        if(that.ticket != res.data.data.ticket){
                            that.barrageList = [];
                            that.ticket = res.data.data.ticket;
                            res.data.data.list.forEach(element => {
                                let obj = {
                                    id:element.Id,
                                    avatar:element.headimgurl,
                                    msg:element.message,
                                    time:parseInt(Math.random()*(25-22+1)+22,10),
                                    type: ''
                                }
                                that.barrageList.push(obj);
                            });
                        }else{
                            if(that.arrFloag){
                                that.barrageList = [];
                                res.data.data.list.forEach(element => {
                                    let obj = {
                                        id:element.Id,
                                        avatar:element.headimgurl,
                                        msg:element.message,
                                        time:parseInt(Math.random()*(25-22+1)+22,10),
                                        type: ''
                                    }
                                    that.barrageList.push(obj);
                                });
                            }
                        }
                    }else{
                        that.barrageList = [];
                    }
                    if(that.timer1){
                        clearTimeout(that.timer1);
                    };
                    that.timer1 = setTimeout(() => {
                        that.addToList(that.ticket);
                    }, 3000);
                    
                }else{
                    that.$message({
                    message: '服务异常请稍后重试！',
                    type: 'warning'
                    });
                }
            });
        },

        sayHi(){
            let that = this;
            this.t++;
            if(this.t>=3){
                this.arrFloag = true;
                this.addToList(this.ticket);
                setTimeout(() => {
                    that.t = 2;
                    that.arrFloag = false;
                }, 30000);
            }else{
                this.arrFloag = false;
            }
            console.log('arrFloag'+this.arrFloag);
        },
    },
    destroyed(){
        if(this.timer1){
            clearTimeout(this.timer1);
        }
    }

}
</script>
<style>
    .index-page{
        height: 100%;
        width: 100%;
        margin: 0 auto;
        overflow: hidden;
        color: #fff;
        background: url(../assets/img/index/BG-kv.png) center no-repeat;
        background-size: 100% 100%;
        z-index: 1;
    }
    .index-page .stopImg{
        width: 100%;
        height: 100%;
    }
    .img-box{
        width: 100%;
        height: 100%;
    }
    .vue-baberrage{
        height: 100%;
        width: 130%;
        box-sizing: border-box;
    }
    .baberrage-msg{
        font-size: 2vw;
    }
    .baberrage-avatar{
        width: 6vw;
        height: 6vw;
        border-radius: 100%;
        z-index: 1000;
        z-index: 100;
    }
     .baberrage-avatar img{
        width: 100%;
        height: 100%;
        border-radius: 100%;
        z-index: 1000;
    }
    .baberrage-item{
        z-index: 1000;
    }
    .vue-baberrage .baberrage-item .normal{
        padding:0px;
        padding: 5px;
        padding-right: 15px;
        box-sizing: border-box;
        z-index: 100;
    }
</style>
<style scoped>
.eq-code-box{
    width: 10vw;
    height: auto;
    position: fixed;
    left: 4vw;
    bottom: 4vh;
}
.eq-code-box .saoma{
    font-size: 1vw;
    color: red;
    font-weight: 700;
}
.eq-code-box img{
    width: 100%;
}
</style>
