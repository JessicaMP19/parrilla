<!--<template>
    <calendar style="height: 800px;"
        :calendars="calendarList"
        :schedules="scheduleList"
        :view="view"
        :taskView="taskView"
        :scheduleView="scheduleView"
        :theme="theme"
        :week="week"
        :month="month"
        :timezones="timezones"
        :disableDblClick="disableDblClick"
        :isReadOnly="isReadOnly"
        :template="template"
        :useCreationPopup="useCreationPopup"
        :useDetailPopup="useDetailPopup"
        @afterRenderSchedule="onAfterRenderSchedule"
        @beforeCreateSchedule="onBeforeCreateSchedule"
        @beforeDeleteSchedule="onBeforeDeleteSchedule"
        @beforeUpdateSchedule="onBeforeUpdateSchedule"
        @clickDayname="onClickDayname"
        @clickSchedule="onClickSchedule"
        @clickTimezonesCollapseBtn="onClickTimezonesCollapseBtn"
    />
</template>
<script> 
import 'tui-calendar/dist/tui-calendar.css'
import { Calendar } from '@toast-ui/vue-calendar';

export default {
    name: 'myCalendar',
    components: {
        'calendar': Calendar
    },
    data() {
        return {
            calendarList: [
                {
                    id: '0',
                    name: 'home'
                },
                {
                    id: '1',
                    name: 'office'
                }
            ],
            scheduleList: [
                {
                    id: '1',
                    calendarId: '1',
                    title: 'my schedule',
                    category: 'time',
                    dueDateClass: '',
                    start: '2018-10-18T22:30:00+09:00',
                    end: '2018-10-19T02:30:00+09:00'
                },
                {
                    id: '2',
                    calendarId: '1',
                    title: 'second schedule',
                    category: 'time',
                    dueDateClass: '',
                    start: '2018-10-18T17:30:00+09:00',
                    end: '2018-10-19T17:31:00+09:00'
                }
            ],
            view: 'week',//Cuantos dias mostrara el horario
            taskView: false,
            scheduleView: ['time'],//muestra todos los dias
            theme: {
                'month.dayname.height': '30px',
                'month.dayname.borderLeft': '1px solid #ff0000',
                'month.dayname.textAlign': 'center',
                'week.today.color': '#333',
                'week.daygridLeft.width': '100px',
                'week.timegridLeft.width': '100px'
            },
            week: {
                daynames: ['Mon', 'Tue', 'Wed', 'Thu', 'Fri', 'Sat', 'Sun'],
                narrowWeekend: true,
                showTimezoneCollapseButton: true,
                timezonesCollapsed: false
            },
            month: {
                visibleWeeksCount: 6,
                startDayOfWeek: 1
            },
            timezones: [{
                timezoneOffset: 540,
                displayLabel: 'GMT+09:00',
                tooltip: 'Seoul'
            }, {
                timezoneOffset: -420,
                displayLabel: 'GMT-08:00',
                tooltip: 'Los Angeles'
            }],
            disableDblClick: true,
            isReadOnly: false,//No se podra agregar mas horarios
            template: {
                milestone: function(schedule) {
                    return `<span style="color:red;">${schedule.title}</span>`;
                },
                milestoneTitle: function() {
                    return 'MILESTONE';
                },
            },
            useCreationPopup: true,
            useDetailPopup: false,
        }
    },
    methods: {
        onAfterRenderSchedule(e) {
            // implement your code
        },
        onBeforeCreateSchedule(e) {
            // implement your code
        },
        onBeforeDeleteSchedule(e) {
            // implement your code
        },
        onBeforeUpdateSchedule(e) {
            // implement your code
        },
        onClickDayname(e) {
            // implement your code
            console.log('Hola day')
        },
        onClickSchedule(e) {
            // implement your code
        },
        onClickTimezonesCollapseBtn(e) {
            // implement your code
        }
    },
    
}
</script> -->

<template>
    <div ref="tuiCalendar">
    </div>
</template>
<script>
import Calendar from 'tui-calendar';
const calendarEvents = [
    'afterRenderSchedule',
    'beforeCreateSchedule',
    'beforeDeleteSchedule',
    'beforeUpdateSchedule',
    'clickDayname',
    'clickSchedule',
    'clickTimezonesCollapseBtn'
];
const scheduleNeedProp = [
    'start',
    'category'
];
export default {
    name: 'ToastUICalendar',
    props: {
        calendars: {
            type: Array,
            default() {
                return [];
            }
        },
        schedules: {
            type: Array,
            default() {
                return [];
            },
            validator(value) {
                let notHave = false;
                value.forEach(schedule => {
                    notHave = scheduleNeedProp.some(prop => !schedule.hasOwnProperty(prop));
                });
                return !notHave;
            }
        },
        view: {
            type: String,
            default() {
                return 'week';
            }
        },
        taskView: {
            type: [Boolean, Array],
            default() {
                return true;
            }
        },
        scheduleView: {
            type: [Boolean, Array],
            default() {
                return true;
            }
        },
        theme: {
            type: Object,
            default() {
                return {};
            }
        },
        template: {
            type: Object,
            default() {
                return {};
            }
        },
        week: {
            type: Object,
            default() {
                return {};
            }
        },
        month: {
            type: Object,
            default() {
                return {};
            }
        },
        useCreationPopup: {
            type: Boolean,
            default() {
                return true;
            }
        },
        useDetailPopup: {
            type: Boolean,
            default() {
                return true;
            }
        },
        timezones: {
            type: Array,
            default() {
                return [];
            }
        },
        disableDblClick: {
            type: Boolean,
            default() {
                return false;
            }
        },
        isReadOnly: {
            type: Boolean,
            default() {
                return false;
            }
        }
    },
    watch: {
        calendars(newValue) {
            this.calendarInstance.setCalendars(newValue);
        },
        schedules() {
            this.calendarInstance.clear();
            this.reflectSchedules();
        },
        view(newValue) {
            this.calendarInstance.changeView(newValue, true);
        },
        taskView(newValue) {
            this.calendarInstance.setOptions({taskView: newValue});
        },
        scheduleView(newValue) {
            this.calendarInstance.setOptions({scheduleView: newValue});
        },
        theme: {
            handler(newValue) {
                this.calendarInstance.setTheme(this.cloneData(newValue));
            },
            deep: true
        },
        week: {
            handler(newValue) {
                const silent = this.view !== 'week' && this.view !== 'day';
                this.calendarInstance.setOptions({week: this.cloneData(newValue)}, silent);
            },
            deep: true
        },
        month: {
            handler(newValue) {
                const silent = this.view !== 'month';
                this.calendarInstance.setOptions({month: this.cloneData(newValue)}, silent);
            },
            deep: true
        },
        timezones(newValue) {
            this.calendarInstance.setOptions({timezones: newValue});
        },
        disableDblClick(newValue) {
            this.calendarInstance.setOptions({disableDblClick: newValue});
        },
        isReadOnly(newValue) {
            this.calendarInstance.setOptions({isReadOnly: newValue});
        }
    },
    data() {
        return {
            calendarInstance: null
        };
    },
    mounted() {
        this.calendarInstance = new Calendar(this.$refs.tuiCalendar, {
            defaultView: this.view,
            taskView: this.taskView,
            scheduleView: this.scheduleView,
            theme: this.theme,
            template: this.template,
            week: this.week,
            month: this.month,
            calendars: this.calendars,
            useCreationPopup: this.useCreationPopup,
            useDetailPopup: this.useDetailPopup,
            timezones: this.timezones,
            disableDblClick: this.disableDblClick,
            isReadOnly: this.isReadOnly
        });
        this.addEventListeners();
        this.reflectSchedules();
    },
    destroyed() {
        calendarEvents.forEach(event => this.calendarInstance.off(event));
        this.calendarInstance.destroy();
    },
    methods: {
        cloneData(data) {
            return JSON.parse(JSON.stringify(data));
        },
        addEventListeners() {
            calendarEvents.forEach(event => {
                this.calendarInstance.on(event, (...args) => this.$emit(event, ...args));
            });
        },
        reflectSchedules() {
            if (this.schedules.length > 0) {
                this.invoke('createSchedules', this.schedules);
            }
        },
        getRootElement() {
            return this.$refs.tuiCalendar;
        },
        invoke(methodName, ...args) {
            let result;
            if (this.calendarInstance[methodName]) {
                result = this.calendarInstance[methodName](...args);
            }
            return result;
        }
    }
};
</script>