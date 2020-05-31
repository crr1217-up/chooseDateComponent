<template>
    <div class="chooseDate" v-clickshow>
        <div class="clickAppear">
            <span class="iconfont">&#xe677;</span>
            <input type="text" :value="handleDate">
        </div>
        <div class="datePannel" v-if="isShow">
            <div class="date-head">
                <span class="prev-year iconfont">&#xe637;</span>
                <span class="prev-month iconfont">&#xe51c;</span>
                <span class="showDate">{{curShowDate.year}}年{{curShowDate.month+1}}月</span>
                <span class="next-month iconfont">&#xe507;</span>
                <span class="next-year iconfont">&#xe63c;</span>
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
                            'isSelect':'judgeClass(day).select',
                            'isToday':'judgeClass(day).today',
                            'other-month':'!judgeClass(day).otherMonth'
                        }"
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
    props:{
        date:{
            type:Date,
            default:()=>new Date()
        }
    },
    directives:{
        clickshow:{
            inserted(el,binding,vnode){
                // const isShow=this.isShow;
                const vm = vnode.context;
                document.onclick=(e)=>{
                    
                    if(el.contains(e.target)&&!vm.isShow){
                        vm.showPanel(true);
                        // console.log("show");
                    }else if(!el.contains(e.target)&&vm.isShow){
                        vm.showPanel(false);
                        // console.log("hide");

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
        this.isCurday()
        // console.log(this.date)
    },
    computed:{
        handleDate(){

            const {year,month,day}=this.getDateObj(this.date);
            return `${year}-${month+1}-${day}`;
        },
        handleDayArr(){
            const {year,month}=this.curShowDate;
            let nowDate=new Date(year,month,1);
            const week = nowDate.getDay();
            const firstDay = nowDate - week*24*60*60*1000;
            var dayArr=[];
            for (let i = 0; i < 42; i++) {
                dayArr.push(new Date(firstDay+i*24*60*60*1000));           
            }
            // console.log(dayArr);
            return dayArr;
        },
        
    },
    methods:{
        showPanel(ishide){
            this.isShow = ishide;
        },
        getDateObj(date){
            // console.log(date);
            const year=date.getFullYear();
            const month=date.getMonth();
            const day=date.getDate();
            // console.log(year,month,day);
            return {
                year,
                month,
                day
            }
        },
        isCurday(){
            const {year,month,day}=this.getDateObj(this.date);
            this.curShowDate={
                year,
                month,
                day
            }

        },
        judgeClass(date){
            const chooseDate = new Date(this.handleDate);
            const {year:chooseYear,month:chooseMonth,day:chooseDay}=this.getDateObj(chooseDate);
            const {year:showYear,month:showMonth}=this.curShowDate;
            const {year:curYear,month:curMonth,day:curDay}=this.getDateObj(new Date());
            const {year,month,day}=this.getDateObj(date);
            return {
                today:year === curYear && month === curMonth && day === curDay,
                select:year === chooseYear && month === chooseMonth && day === chooseDay,
                otherMonth:year === showYear && month === showMonth
            }
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
    .datePannel .days .date-day.isSelect{
        color:#fff;
        background-color: #409eff;
        border-radius: 50%;
    }
    .datePannel .days .date-day.isToday{
        color:#409eff;
        font-weight: 700;
    }
     .datePannel .days .date-day.other-month {
        color: #c0c4cc;
    }
    
</style>