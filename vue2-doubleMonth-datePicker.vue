<template>
	<div class="datepicker-days" ref="datePicker">
		<table v-for="(month,$index) in tableData" >
			<thead>
				<tr>
					<th >
						<i v-show="$index==0" @click="monthIndex-=1"><</i>
					</th>
					<th colspan="5">{{getNormalMonth(Number($index)+Number(monthIndex))}} </th>
					<th >
						<i v-show="$index==1" @click="monthIndex+=1">></i>
					</th>
				</tr>
			</thead>
            <thead>
                <tr>
                    <th>日</th>
                    <th>一</th>
                    <th>二</th>
                    <th>三</th>
                    <th>四</th>
                    <th>五</th>
                    <th>六</th>
                </tr>
            </thead>
            <tbody>
            	<tr v-for="week in month">
            		<td 
						:class="
						day&&isActive(getNormalMonth(monthIndex+Number($index))+'-'+(day<10?'0'+day:day))
						?'active':
						(isInRange(getNormalMonth(monthIndex+Number($index))+'-'+(day<10?'0'+day:day))
							?'range':
							isDisable(getNormalMonth(monthIndex+Number($index))+'-'+(day<10?'0'+day:day))?'disable':'')
						"
            		    v-for="day in week" @click="select(getNormalMonth(monthIndex+Number($index))+'-'+(day<10?'0'+day:day),day)">
            			{{day}}
            			
            		</td>
            	</tr>
            </tbody>
		</table>
		
	</div>
</template>

<script>
export default {	
	name:'datePicker',
	props:{
		beginText:{
			type:String,
			default:'入住'
		},
		endText:{
			type:String,
			default:'离店'
		},
		start:{
			type:String,
			default:''
		},
		last:{
			type:String,
			default:''
		},
		beginDate:{
			type:String,
			default:''
		},
		endDate:{
			type:String,
			default:''
		},

	},
	watch:{
		beginDate(val){
			this.begin=val
		},
		endDate(val){
			this.end=val
		},
		last(val){
			this.lastDate=val;
		},
		monthIndex(){
			this.initTable();
		},
	},
	data(){
		return{
			monthIndex:0,
			today:this.getDate(0),
			tableData:{},
			begin:'',
			end:'',
			lastDate:'',
		}
	},
	methods:{
		select(date,day){
			if(this.isDisable(date)||!day)return;
			if(!this.begin||(this.begin&&this.end)){
				this.begin=date;
				this.end='';
			}else if(this.begin&&!this.end){
				//反选日期
				if(this.isOpposite(date,this.begin)){
					this.end=this.begin;
					this.begin=date;
					
				}else{
					this.end=date;
				}
				//选中日期	
				this.$emit('select',this.begin,this.end)
			}
		},
		//查看是否是已经选中的日期区间中
		isInRange(day){
			if(!day)return;
			var begin=this.begin;
			var end=this.end;
			var s1 = (new Date(begin)).getTime();
           	var s2 = (new Date(end)).getTime();
           	var s = (new Date(day)).getTime();
           	if((s<s2)&&(s>s1))return true;
		},
		//是否为不可选择日期
		isDisable(day){
			var start=this.start;
			var lastDate=this.lastDate;
			var s1 = (new Date(start)).getTime();
			var s2 = (new Date(lastDate)).getTime();
           	var s = (new Date(day)).getTime();
           	return (s1>s||s2<s)?true:false;
		},
		//判断是否反转日期
		isOpposite(end,begin){
			var s1 = (new Date(begin)).getTime();
           	var s2 = (new Date(end)).getTime();
           	return s2<s1?true:false;
		},
		//是否选中
		isActive(day){

			var begin=this.begin;
			var end=this.end;
			var s1 = (new Date(begin)).getTime();
			var s2 = (new Date(end)).getTime();
           	var s = (new Date(day)).getTime();
           	return (s==s1||s==s2)?true:false
			
		},
		//是否为开始日期
		isBegin(day){
			var begin=this.begin;
			var end=this.end;
			var s1 = (new Date(begin)).getTime();
           	var s = (new Date(day)).getTime();
           	return s==s1?true:false
		},
		//是否为结束日期
		isEnd(day){
			var begin=this.begin;
			var end=this.end;
			var s1 = (new Date(end)).getTime();
           	var s = (new Date(day)).getTime();
           	return s==s1?true:false
		},
		/*获取一个月的天数 */
		getCountDays(date) {
		    var curDate = new Date(date);
		    var curMonth = curDate.getMonth();
		    curDate.setMonth(curMonth + 1);
		    curDate.setDate(0);
		       
			return curDate.getDate();
		},
        //获取日份,count=1为下一个月，count=-1为上一个月
        getNormalMonth(count) {
        	
            var date = new Date();
            date.setMonth(date.getMonth()+count);
            var year = date.getFullYear();
            var month = date.getMonth() + 1;
            if (month < 10) { month = '0' + month };
            var time = String(year) + '-' + String(month);

            return time;
        },
        //计算时间差
        getDaysSize (s1, s2) {
            var s1 = new Date(s1);
            var s2 = new Date(s2);

            var days = s2.getTime() - s1.getTime();
            var time = parseInt(days / (1000 * 60 * 60 * 24));
            return time;
        },
        //日期期格式化周几  
        getWeekday (date) {
            var nowDate=new Date();
            var days=this.getDaysSize(nowDate,date);
            var mydate=new Date(date); 
            var myday=mydate.getDay()//注:0-6对应为星期日到星期六 
            return myday;
        },
		//获取一个月份的每一天
		getEvryDay(index){

			var dayArry=[];
			var date=this.getNormalMonth(index);

			var day=this.getCountDays(date);
			for (var k = 1; k <= day; k++) {
				dayArry.push(k);
			}
			return dayArry;
		},
		//生成表格数据结构
		initTable(){
			var array={};
			
			for(var i=0;i<2;i++){

				var index=Number(this.monthIndex);
				var days=this.getEvryDay(i+index);
				
				var daysArray=days;	
			
				var temp=this.getFullfillDateTable(daysArray,this.getNormalMonth(index+i)+'-01');
				array[i]=temp;
			
			}
			this.tableData=array;
		},
   		//将数据格式化表格日期格式
        getFullfillDateTable(data,date){
            var data=data;
            var weekday=this.getWeekday(date);
            for(var i=0;i<weekday;i++){
                 data.unshift('');
            }
            var result = [];
            //补全日期前几天
            for(var i=0,len=data.length;i<len;i+=7){
               result.push(data.slice(i,i+7));
            }
            //补全日期后几天
            var length=result[(result.length-1)].length;
            if(length<7){
                for(var i=0;i<(7-length);i++){
                    result[(result.length-1)].push('');
                }
            }
            return result;
        },
        addCloseListener(){
			let that=this;
		    document.addEventListener('click',function(e){
		    	if(e.target.nodeName=='INPUT'||that.$refs.datePicker&&that.$refs.datePicker.contains(e.target))return;
				that.begin=that.beginDate;
			    that.end=that.endDate;
				that.$emit('select')
		    })
        }
	},
	mounted(){
		this.begin=this.beginDate;
		this.end=this.endDate;
		this.initTable();
		this.addCloseListener();


	}
}
</script>
<style scoped>
.datepicker-days{
    width:auto;
    border: 1px solid #ccc;
    padding:8px;
    background-color: #fff;
    position: absolute;
    z-index: 10;
}
.datepicker-days:after{
	content: '';
	display: block;
	height: 1px;
	clear: both;

}
.datepicker-days>table{
  
   	float:left;
    border-right: 1px solid #ccc;
    vertical-align: top;
}
.datepicker-days>table:last-child{
    border-right: 0px;
}
.datepicker-days>table>thead>tr>th,.datepicker-days>table>tbody>tr>td{
    width: 30px;
    height: 30px;
    padding:0;
    font-size: 12px;
    font-weight: 400;
    text-align: center;
    border-bottom: 0px;
    border-radius: 4px;
    
}

.datepicker-days>table>tbody>tr>.day{
    cursor: pointer;
}
.datepicker-days>table>tbody>tr>td.active{
    background-color: #ffc900;
    color: #444;
    text-shadow: 0 -1px 0 rgba(0, 0, 0, 0.25);

}
.datepicker-days>table>tbody>tr>td.range{
    background-color: #fff6d6;
    color: #444;
    text-shadow: 0 -1px 0 rgba(0, 0, 0, 0.25);

}
.datepicker-days>table>tbody>tr>td.disable{
    background-color: #fcfcfc;
    color: #ccc;
    cursor: not-allowed;
    text-shadow: 0 -1px 0 rgba(0, 0, 0, 0.25);

}
.datepicker-days>table>thead>tr>th{
    font-weight: bold;

}
.datepicker-days>table>thead>tr>th>i{
    cursor: pointer;
}
.datepicker-days table tr:last-child td{
    border-bottom: 0px;
}
 
</style>