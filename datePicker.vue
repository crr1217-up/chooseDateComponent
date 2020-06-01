<template>
    <div class="chooseDate" v-clickshow>
        <div class="clickAppear">
            <span class="iconfont">&#xe677;</span>
            <input type="text" :value="handleDate">
        </div>
        <div class="datePannel" v-if="isShow">
            <div class="arrow"></div>
            <div class="date-head">
                <span class="prev-year iconfont"
                    @click="changeYear('prev')"
                >&#xe637;</span>
                <span class="prev-month iconfont"
                    @click="changeMonth('prev')"
                >&#xe51c;</span>
                <span class="showDate">{{curShowDate.year}}年{{curShowDate.month+1}}月</span>
                <span class="next-month iconfont"
                    @click="changeMonth('next')"
                >&#xe507;</span>
                <span class="next-year iconfont"
                    @click="changeYear('next')"
                >&#xe63c;</span>
            </div>
            <div class="date-body">
                <div class="weeks">
                     <div class="date-week"
                        v-for="week in ['日','一','二','三','四','五','六']"
                        :key="week"
                    >{{week}}</div>
                </div>
               <div class="days">
                   <div class="date-day"
                        v-for="day in handleDayArr"
                        :key="day.getTime()"
                        :class="{
                            'isSelect':judgeClass(day).select,
                            'isToday':judgeClass(day).today,
                            'other-month':!judgeClass(day).otherMonth
                        }"
                        @click="changeChooseDate(day)"
                    >
                    {{day.getDate()}}
                    </div>
               </div>
                
            </div>
        </div>
    </div>
</template>

<script>
export default {
    props:{//接收父组件传过来的日期，并设置默认值
        date:{
            type:Date,
            default:()=>new Date()
        }
    },
    model:{
        prop:"date",
        event:"choose-date"
    },
    directives:{//用于控制日历面板隐藏于展示情况的指令，用于根元素，点击的是其子元素就展示，不是就收起
        clickshow:{
            inserted(el,binding,vnode){
                const vm = vnode.context;
                document.onclick=(e)=>{
                    if(el.contains(e.target)&&!vm.isShow){//判断是否是其子元素直接用contains()
                        vm.showPanel(true);
                    }else if(!el.contains(e.target)&&vm.isShow){
                        vm.showPanel(false);
                    }
                }
            }
        }
    },
    data(){
        return {
            isShow:false,            
            curShowDate:{
                year:0,
                month:0,
                day:0
            }
        }
    },
    created(){
        this.isCurday(this.date)
       
    },
    computed:{
        handleDate(){//用于处理选择的日期展示格式
            const {year,month,day}=this.getDateObj(this.date);
            return `${year}-${month+1}-${day}`;
        },
        handleDayArr(){//把整个日历面板所要展示的日期都放到一个数组中去
            const {year,month}=this.curShowDate;
            let nowDate=new Date(year,month,1);//日历面板头部所展示的日期当月的1号
            const week = nowDate.getDay();//判断这一天是周几,从而可以判断出展示的日期每一天的样式以及这个月该从哪个位置开始展示
            const firstDay = nowDate - week*24*60*60*1000;//根据周几决定前面有几天上个月的日期，相应的减去几天的毫秒数
            var dayArr=[];
            for (let i = 0; i < 42; i++) {//日历面板一次展示42天
                dayArr.push(new Date(firstDay+i*24*60*60*1000));           
            }
            return dayArr;
        },
        
    },
    methods:{
        showPanel(ishide){//用来控制日历面板的展示
            this.isShow = ishide;
        },
        getDateObj(date){//专门用来把日期转换成年，月，日
            const year=date.getFullYear();
            const month=date.getMonth();
            const day=date.getDate();
            return {
                year,
                month,
                day
            }
        },
        isCurday(date){//处理日历头部展示的日期
            const {year,month,day}=this.getDateObj(date);
            this.curShowDate={
                year,
                month,
                day
            }

        },
        judgeClass(date){//判断每个日期那一天，从而展示不同的样式
            const chooseDate = new Date(this.handleDate);//输入框选择的日期
            const {year:chooseYear,month:chooseMonth,day:chooseDay}=this.getDateObj(chooseDate);
            const {year:showYear,month:showMonth}=this.curShowDate;//日历面板头部展示日期
            const {year:curYear,month:curMonth,day:curDay}=this.getDateObj(new Date());//当前日期
            const {year,month,day}=this.getDateObj(date);//所判断的日期
            return {
                today:year === curYear && month === curMonth && day === curDay,
                select:year === chooseYear && month === chooseMonth && day === chooseDay,
                otherMonth:year === showYear && month === showMonth
            }
        },
        changeChooseDate(date){//改变在输入框中选择的日期
            this.$emit("choose-date",date);//改变所选择的日期，因为这是从父组件中传过来的日期，所以修改也是传到父组件进行修改
            this.isCurday(date);//选择的日期变了，同样日历头部展示的如期也要变
            this.showPanel(false);//选择完日期收起日历面板
        },
        changeYear(flag){//切换年份
            // let {year}=this.curShowDate;
            const cha = flag=="prev"?-1:1;
            this.curShowDate.year += cha;
        },
        changeMonth(flag){//切换月份
            const cha = flag=="prev"?-1:1;
            const {year,month,day}=this.curShowDate;
            const showDate = new Date(year,month,day);
            showDate.setMonth(month+cha);//直接设置月份，这样年份会自动改变
            const {year:nowShowYear,month:nowShowMonth}=this.getDateObj(showDate);
            this.curShowDate.year=nowShowYear;
            this.curShowDate.month=nowShowMonth;
        }
    },
}
</script>

<style scoped>
    @import "./assets/font.css";

    .chooseDate{
        /* background-color: red; */
         display: inline-block;
    }
    .clickAppear{
        position: relative;
       
    }
    .clickAppear input{
        border:1px solid #dcdfe6;
        height:40px;
        line-height:40px;
        padding:0 40px;
        border-radius: 4px;
        outline: none;
        cursor: pointer;
    }
     .clickAppear span{
         position: absolute;
         top:0;
         left:10px;
         width:30px;
         height:100%;
         line-height:40px;
         text-align: center;
         color: #c0c4cc;
         cursor: pointer;
         /* background-color: red; */
     }
    .datePannel{
        position: absolute;
        width: 322px;
        height: 329px;
        margin-top: 5px;
        border: 1px solid #e4e7ed;
        border-radius: 4px;
        box-shadow: 0 2px 12px 0 rgba(0, 0, 0, .1);
        background-color: #fff;
    }
    .datePannel .arrow{
        position: absolute;
        width:0;
        height:0;
        top:-12px;
        left:50px;
        /* display: inline-block; */
        border:6px solid transparent;
        border-bottom-color: #e4e7ed;
    }
    .datePannel .arrow::after{
        position: absolute;
        top:1px;
        left:-6px;
        content:"";
        width:0;
        height:0;
        border:6px solid transparent;
        border-bottom-color: #fff;
        border-top-width: 0;
    }
    .datePannel .date-head{
        text-align: center;
        padding:15px 0 10px;
        font-size:14px;
        display: flex;
        align-items: center;
        justify-content: center;
    }
    .datePannel .date-head .iconfont{
        color:#303133;
        font-size:12px;
        margin-right: 5px;
        margin-left: 5px;
        cursor: pointer;
    }
    .datePannel .date-head .showDate{
        margin-right: 60px;
        margin-left: 60px;
        user-select: none;
    }
    .datePannel .date-body{
         padding: 0 10px 10px 10px;
        color: #606266;
        user-select: none;
    }
    .datePannel .date-body .weeks{
        display: flex;
        justify-content: space-around;
        height: 40px;
        line-height: 40px;
        border-bottom: 1px solid #ebeef5;
    }
    .datePannel .days{
        display: flex;
        flex-wrap: wrap;
        justify-content: center;
        align-items: center;
        /* margin-top:10px; */
    }
    .datePannel .days .date-day{
        /* background-color:red; */
        width:30px;
        height: 30px;
        line-height: 30px;
        margin:4px 6px;
        font-size: 12px;
        text-align: center;
        cursor: pointer;
    }
    .datePannel .days .date-day:hover{
        color: #409eff;
    }
    .datePannel .days .date-day.isToday{
        color:#409eff;
        font-weight: 700;
    }
    .datePannel .days .date-day.isSelect{
        color:#fff;
        background-color: #409eff;
        border-radius: 50%;
    }
    
     .datePannel .days .date-day.other-month {
        color: #c0c4cc;
    }
    
</style>