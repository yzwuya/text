<template>
  <div>
    <div class="allleft">
      <h1 style="text-align: center;">效果演示图</h1>
      <div v-for="(row,m) in minesArray" class="row-cells" :key="m">
        <div v-for="(cell,n) in row" class="cell" :key="n">
            {{ cell }}
        </div>
      </div>
    </div>
    
    <div class="forinput">
      <h2 style="text-align: center;">输入数据</h2>
      <p>请输入世界规格：<input v-model="num" placeholder="请输入数字"></p>
      <p>请输入僵尸位置：<input v-model="zposition" placeholder="请输入位置(x,y)"></p>
      <p>请输入生物位置：<input v-model="cposition" placeholder="请输入位置(x,y)"></p>
      <p>请输入行动方式：<input v-model="action" placeholder="上U,下D,左L,右R"></p>
      <button @click="startNum">开始规划</button>
      <button @click="startz">放置僵尸</button>
      <button @click="startc">放置生物</button>
      <button @click="startAction">开始行动</button>
      <input type="button" onclick="javascript:location.reload();" value="刷新">
    </div>  
    <div class="foroutput">
      <h2 style="text-align: center;">输出数据</h2>
      <p>僵尸位置:</p>
      <div v-for="(item,index) in lastzpo" :key="index">{{item}}</div>
      <p>生物位置:</p>
      <div>
      <div v-for="(item,index) in lastcpo" :key="index">{{item}}</div>
      <div v-if="lastcpo.length==0&&show==true">没有一个</div>
      </div>
    </div>
  </div>
  
</template>

<script>
export default {
  data(){
    return{
      num:'',//规格
      zposition:[],//僵尸位置
      cposition:[],//生物位置
      action:'',//行动方式
      minesArray: '',//图标
      zArray:'',//僵尸数组
      cArray:'',//生物数组
      lastzpo:[],//僵尸最后位置
      lastcpo:[],//生物最后位置
      delcpo:[],//生物清除位置
      show:false
    }
  },
  methods:{
    //制图
    startNum(){
      this.minesArray = new Array();
    for (let i = 0; i < this.num; i++) {
      this.minesArray[i] = new Array();
      for (let j = 0; j < this.num; j++) {
        this.minesArray[i][j] = '空地';
      }
    }
    },
    //放置僵尸
    startz(){
      let Row=''
      let Col=''
      for(let i=0;i<this.zposition.length;i++){
        //获取第一个值
        if(this.zposition[i]==='('||this.zposition[i]==='（'){
          for(let j=i+1;this.zposition[j]!==','&&this.zposition[j]!=='，';j++){
            Row=Row+this.zposition[j]
          }
        }
        //获取第二个值
        if(this.zposition[i]===','||this.zposition[i]==='，'){
          for(let t=i+1;this.zposition[t]!==')'&&this.zposition[t]!=='）';t++){
            Col=Col+(this.zposition[t])
          }
        }
      }
      //将僵尸位置设为-1
      let intRow=parseInt(Row)
      let intCol=parseInt(Col)
      this.minesArray[intCol][intRow]="僵尸"
      this.$set(this.minesArray,intCol, [...this.minesArray[intCol]]);
      //加入僵尸数组
      this.zArray=new Array()
      this.zArray[0]=new Array()
      this.zArray[0][0]=Row+','+Col
    },
    //放置生物
    startc(){
      let Row=''
      let Col=''
      var cnum=0//生物数量
      this.cArray=new Array()
      for(let i=0;i<this.cposition.length;i++){
        //获取第一个值
        if(this.cposition[i]==='('||this.cposition[i]==='（'){
          for(let j=i+1;this.cposition[j]!==','&&this.cposition[j]!=='，';j++){
            Row=Row+this.cposition[j]
          }
        }
        //获取第二个值
        if(this.cposition[i]===','||this.cposition[i]==='，'){
          for(let t=i+1;this.cposition[t]!==')'&&this.cposition[t]!=='）';t++){
            Col=Col+(this.cposition[t])
          }
          let intRow=parseInt(Row)
          let intCol=parseInt(Col)
          //放入生物数组
          this.cArray[cnum]=new Array()
          this.cArray[cnum]=Row+','+Col
          //生物数量增加
          cnum+=1
          this.minesArray[intCol][intRow]="生物"
          this.$set(this.minesArray,intCol, [...this.minesArray[intCol]]); 
          Row=''
          Col=''
        }
      }
    },
    //开始行动
    startAction(){
      var znum=1//僵尸数量
      var twoz=[]//同一位置存在多个僵尸
      for(let j=0;j<znum;j++){
      var getZ=this.zArray[j][0].split(',')
      var incol=parseInt(getZ[0])
      var inrow=parseInt(getZ[1])
      //如果多个僵尸在同一位置则不修改，否则将当前位置设为0
      if(twoz.length>0){
        for(let m=0;m<twoz.length;m++){
          if(twoz[m]==this.zArray[j][0]){break}
          if(m==twoz.length-1){
            this.minesArray[inrow][incol]='空地'
            this.$set(this.minesArray,inrow, [...this.minesArray[inrow]]); 
          }
        }
    }else{
      this.minesArray[inrow][incol]='空地'
      this.$set(this.minesArray,inrow, [...this.minesArray[inrow]]); 
    }
      for(let i=0;i<this.action.length;i++){
        //向上
        if(this.action[i]==='U'){
          if(inrow==0){
            inrow=this.num-1
          }else{
            inrow=inrow-1
          }
          if(this.minesArray[inrow][incol]==='生物'){
            this.zArray[znum]=new Array()
            this.zArray[znum][0]=incol+','+inrow
            this.minesArray[inrow][incol]="僵尸"
            this.$set(this.minesArray,inrow, [...this.minesArray[inrow]]); 
            for(let k=0;k<this.cArray.length;k++){
              let getC=this.cArray[k].split(',')
              if(incol===parseInt(getC[0])&&inrow===parseInt(getC[1])){
                this.delcpo.push(k)
              }
            }
            znum+=1
          }
        }
        //向下
        if(this.action[i]==='D'){
          if(inrow===this.num-1){
            inrow=0
          }else{
            inrow=inrow+1
          }
          if(this.minesArray[inrow][incol]==="生物"){
            this.zArray[znum]=new Array()
            this.zArray[znum][0]=incol+','+inrow
            this.minesArray[inrow][incol]='僵尸'
            this.$set(this.minesArray,inrow, [...this.minesArray[inrow]]); 
            for(let k=0;k<this.cArray.length;k++){
              let getC=this.cArray[k].split(',')
              if(incol===parseInt(getC[0])&&inrow===parseInt(getC[1])){
                this.delcpo.push(k)
              }
            }
            znum+=1
          }
        }
        //向左
        if(this.action[i]==='L'){
          if(incol===0){
            incol=this.num-1
          }else{
            incol=incol-1
          }
          if(this.minesArray[inrow][incol]==="生物"){
            this.zArray[znum]=new Array()
            this.zArray[znum][0]=incol+','+inrow
            this.minesArray[inrow][incol]='僵尸'
            this.$set(this.minesArray,inrow, [...this.minesArray[inrow]]); 
            for(let k=0;k<this.cArray.length;k++){
              let getC=this.cArray[k].split(',')
              if(incol===parseInt(getC[0])&&inrow===parseInt(getC[1])){
                this.delcpo.push(k)
              }
            } 
            znum+=1
          }
        }
        //向右
        if(this.action[i]==='R'){
          if(incol===this.num-1){
            incol=0
          }else{
            incol=incol+1
          }
          if(this.minesArray[inrow][incol]==="生物"){
            this.zArray[znum]=new Array()
            this.zArray[znum][0]=incol+','+inrow
            this.minesArray[inrow][incol]='僵尸'
            this.$set(this.minesArray,inrow, [...this.minesArray[inrow]]); 
            for(let k=0;k<this.cArray.length;k++){
              let getC=this.cArray[k].split(',')
              if(incol===parseInt(getC[0])&&inrow===parseInt(getC[1])){
                this.delcpo.push(k)
              }
            }
            znum+=1
          }
        }
        //最终位置
        if(i===this.action.length-1){
          //存在多个僵尸，放入twoz
          if(this.minesArray[inrow][incol]==='僵尸'){
            twoz.push(incol+','+inrow)
          }
          //放入最终僵尸位置
          this.minesArray[inrow][incol]='僵尸'
          this.$set(this.minesArray,inrow, [...this.minesArray[inrow]]); 
          this.lastzpo.push('('+incol+','+inrow+')')
        }
      }
      }
      //输出僵尸位置
      this.lastzpo.forEach(e=>{console.log(e);})
      //在生物数组中清除变成僵尸的
      if(this.delcpo.length==0){
          this.lastcpo.push(this.cposition)
      }else{
        for(let g=0;g<this.cArray.length;g++){
          for(let k=0;k<this.delcpo.length;k++){
          if(g==this.delcpo[k]){
            break
          }
          if(k==this.delcpo.length-1){
            this.lastcpo.push('('+this.cArray[g]+')')
          }
        }
      }
      }
      
      if(this.delcpo.length>=this.cArray.length){
        console.log('none');
      }
      //输出生物位置
      this.lastcpo.forEach(e=>{console.log(e);})
      this.show=true
    }
  },
}
</script>

<style type="less">
  .allleft{
    position: fixed;
    width: 800px;
    height: 800px;
    border: 2px solid black;
    text-align: center;
  }
  .forinput{
    padding-left: 10px;
    position: fixed;
    left: 808px;
    width: 490px;
    height: 400px;
    border: 2px solid black;
  }
  .foroutput{
    position: fixed;
    left: 808px;
    top: 408px;
    width: 500px;
    height: 400px;
    border: 2px solid black;
    text-align: center;
  }
  .cell {
    --cell-size: 40px;
  display: inline-block;
  width: var(--cell-size);
  height: var(--cell-size);
  line-height: var(--cell-size);
  border: 1px solid;
  text-align: center;
  vertical-align: middle;
  cursor: pointer;
}
.row-cells {
  font-size: 1px;
}

</style>