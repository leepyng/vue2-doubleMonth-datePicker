# vue2-doubleMonth-datePicker
easy web double date selector of a vue 2.0 component
<p align="center">


<img src="https://github.com/leepyng/vue2-doubleMonth-datePicker/blob/master/git/QQ20180629-173252.gif" alt="Coverage Status">


</p>

# install
	
	npm install vue2-doubleMonth-datePicker --save-dev
	

# how to use
	template:
		<transition  name="fade" >
			<DatePicker  @select="calendar.select" v-show="calendar.show" :start="calendar.start"  :beginDate="calendar.begin" :endDate="calendar.end"></DatePicker>
		</transition>
	
	script:
		//import
		import DatePicker from 'vue2-doubleMonth-datePicker'
		//define
		components:{
			DatePicker
		},
		//set default data
		data(){
			return{
				calendar:{
					show:false,
					start:'2018-08-01',
					begin:'2018-08-02',
					end:'2018-08-03',
					select:(begin,end)=>{
						this.calendar.show=false;
						this.calendar.begin=begin;
						this.calendar.end=end;
					}
				},
			}
		}
		
	
# props description
	start:the calendar's begining date
	last:the calendar's last date
	beginDate: begin of the select
	endDate:end of the select
