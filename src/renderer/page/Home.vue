<template>
    <div style="position: relative;">
        <img :src="imagepath" @click="handleImageClick($event)" style="width:360px;height: 640px;" alt="">
        <div style="width: 10px;height: 10px;background-color: red;position: absolute" :style="{top:start.y+'px',left:start.x+'px'}"></div>
        <div style="width: 10px;height: 10px;background-color: blue;position: absolute" :style="{top:end.y+'px',left:end.x+'px'}"></div>
        <span style="display:inline-block;vertical-align: top;width: 350px">
            <h3>
                控制区域
            </h3>
        开始:x:{{start.x}} y:{{start.y}}  结束:x:{{end.x}} y:{{end.y}} 距离：{{juli}}<br>
        <!--开始:x:{{start.x*3}} y:{{start.y*3}}  结束:x:{{end.x*3}} y:{{end.y*3}} 距离：{{juli*3}}<br>-->
        <button @click="getImage">刷新</button>
        <button @click="startExec">开始</button>
        <button @click="clearXY">清除坐标</button>
        <br>
        <br>
        <input type="text" v-model="xs" name="aaa"/>
            <br>
        当前系数{{xs}}（可以上下0.1微调）

            <h3>
                说明
            </h3>
            <p>
                作者邮箱：<a href="#" @click="openLink('mailTo:sailiy@126.com')">sailiy@126.com</a><br>
                使用文档：<a href="#" @click="openLink('http://www.rainx.org/2018/01/10/ttwg/')">点我跳转</a><br>
                GitHub地址：<a href="#" @click="openLink('http://www.rainx.org/2018/01/10/ttwg/')">点我跳转</a><br>
            </p>
            <h3>
                关于
            </h3>
            <p>

            </p>
        </span>
    </div>
</template>

<script>
    import {execShell} from '@/utils/utils'
    const shell = require('electron').shell

    import {resolve} from 'path'
    var r = (path) => resolve(process.execPath,'../', path)
    export default {
        components: {},
        data () {
            return {
            	xs:4.4,
                imagepath: '',
                start: {
                    isok:false,
                    x: -1,
                    y: -1
                },
                end: {
                    isok:false,
                    x: -1,
                    y: -1
                },
                juli:0
            }
        },
        mounted(){
            this.getImage()
        },
        methods: {
            getImage(){
                var imagepath = r("sc.png");
                let s = `adb exec-out screencap -p > ${imagepath}`;
                console.log(s)
                execShell(s).then(() => {
                    console.log(`执行完成`)
                    this.imagepath = "file://" + imagepath + "?" + parseInt(Math.random() * 10000000000000)
                });
            },
            clearXY(){
                var obj= {
                    isok:false,
                    x: -1,
                    y: -1
                };
                var obj2= {
                    isok:false,
                    x: -1,
                    y: -1
                };
                this.start=obj;
                this.end=obj2;
            },
            handleImageClick(event){
                console.log(event)
                if(!this.start.isok){
                    this.start.x=event.offsetX
                    this.start.y=event.offsetY
                    this.start.isok=!this.start.isok
                }else{
                    this.end.x=event.offsetX
                    this.end.y=event.offsetY
                    this.end.isok=!this.end.isok

                    var x=(this.end.x-this.start.x)*(this.end.x-this.start.x)+(this.end.y-this.start.y)*(this.end.y-this.start.y)
                    this.juli=Math.sqrt(x)

                    this.startExec()
                }
            },
            startExec(){
               var totalTime=Math.round(this.juli*this.xs);
                let s = `adb shell input swipe 500 500 600 600 ${totalTime}`;
                console.log(s)
                execShell(s).then(() => {
                    console.log(`执行完成`)
                    setTimeout(()=>{
                        this.clearXY()
                        this.getImage()
                    },1000)
                });
            },
            openLink(url){
                shell.openExternal(url)
            }
        }
    }
</script>

<style scoped>

</style>
