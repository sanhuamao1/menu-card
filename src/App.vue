<template>
  <div id="app" class="container">
    <!-- 左侧菜单栏 -->
    <div ref="leftMenu" class="leftMenu">
        <ul>
            <li
                v-for="(item, index) in foodList"     
                :key="index"
                :class="{ active: currentIndex === index }"
                @click="selectLeft(index)"
            >
                {{ item.name }}
            </li>
        </ul>
    </div>
    <!-- 右侧内容栏 -->
    <div ref="rigntMenu" :style="'height:' + scrollH + 'px'" style="overflow: hidden" class="rightMenu">
        <ul>
            <li v-for="(item, index) in foodList"  :key="index"  ref="rightItem">
                <span class="rightTitle">{{ item.name }}</span>
                <div v-for="(food, key) in item.foods" :key="key" class="rightDetail">
                    {{ food }}
                </div>
            </li>
        </ul>
    </div>
  </div>
</template>

<script>
import Bscroll from "better-scroll";
import './index.css'
export default {
  name: 'App',
  data(){
      return {
          scrollH:'',//滚轮窗口的高度
          heightList:[],//储存位置
          scrollY:'',//滚动距离
          //菜品信息-例子
          foodList:[
              {name:'菜类',foods:['娃娃菜','大白菜','西洋菜','茄子','南瓜','黄瓜','冬瓜','娃娃菜','大白菜','西洋菜','茄子','南瓜','黄瓜','冬瓜']},
              {name:'肉类',foods:['猪肉','牛肉','鸡肉','鸭肉','羊肉','鹅肉']},
              {name:'水果',foods:['苹果','西瓜','李子','番茄','柚子','番石榴']},
              {name:'甜品',foods:['奶茶','华夫饼','鸡蛋仔','慕斯','奶茶','华夫饼','鸡蛋仔','慕斯']},
          ],
      };
  },
  methods:{
    //创建滚动窗口
    scrollInit() {
        //创建左边菜单栏滚动窗口
        this.leftMenu = new Bscroll(this.$refs.leftMenu, {
            click: true,//允许点击
        });
        //创建右边内容栏滚动窗口
        this.rigntMenu = new Bscroll(this.$refs.rigntMenu, {
            probeType: 3, //在 rigntMenu 滚动时触发 scroll 事件
            click: true,
        });
        //当右侧滚动时，监听滚动距离，将滚动距离储存在scrollY中
        this.rigntMenu.on("scroll", (pos) => {
            this.scrollY = Math.abs(Math.round(pos.y));
        });
    },

    //获取右侧内容栏高度
    getListHeight(){
        const lis=this.$refs.rightItem   //获取每项的DOM
        this.heightList=[]	//储存每项的顶部位置
        let height=0  
        this.heightList.push(height)  //第一项的顶部垂直位置为0
        lis.map(item=>{
            height+=item.clientHeight //第二项顶部位置=第一项顶部位置+第二项的内容高度，以此类推
            this.heightList.push(height)
        })
        console.log('this.heightList',this.heightList)
    },

    //点击左侧菜单
    selectLeft(index) {
      console.log('index',index)
      console.log('this.currentIndex',this.currentIndex)
      let rightItem = this.$refs.rightItem; 
      let el = rightItem[index]; //根据索引获取对应dom
      this.rigntMenu.scrollToElement(el, 1000); //将右侧滚动窗口滚动至对应dom
    },
},

computed:{
    currentIndex() {
        //根据当前滚轮滑动的距离 判断左侧菜单现处位置
        const index = this.heightList.findIndex((item, index) => {
            return  (this.scrollY >= this.heightList[index] &&
                    this.scrollY < this.heightList[index + 1])
        });
        return index > 0 ? index : 0;
    },
},

mounted(){
    this.scrollH= 180
    this.$nextTick(() => {
        this.scrollInit()
        this.getListHeight()
    })
}

}
</script>

<style>
</style>
