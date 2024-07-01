<script lang="ts">
  
  import { getContext } from "svelte"	
  import '@fullcalendar/core/locales-all'
  import FullCalendar from 'svelte-fullcalendar';
  import daygridPlugin from '@fullcalendar/daygrid';
  import timeGridPlugin from '@fullcalendar/timegrid';
  import listPlugin from '@fullcalendar/list';
  import { onMount } from "svelte";
  import {langs, codeLang} from "./lang"
 
  export let language
  export let calendarEvent
  export let prevDateEvent
  export let nextDateEvent
  export let initialDate
  console.log(initialDate)
  
  export let mappingTitle
  export let mappingDate
  export let mappingStart
  export let mappingEnd
 
  export let dataProvider

  export let mappingColor

  export let allday
  
  export let headerOptionsStart
  export let headerOptionsCenter
  export let headerOptionsEnd


  let calendarComponentRef

  let eventsList = []
  onMount(()=>{
    console.log(calendarComponentRef.getAPI())
    
    if(eventsList.length > 0){
      eventsList = []
    }
    if(dataProvider.rows){
      dataProvider.rows.forEach(event => {
        let eventColor = mappingColor ?? '#313131'           
        eventsList.push({ id:event.id, title: event[mappingTitle], date: event[mappingDate], start: event[mappingStart], end: event[mappingEnd], color: eventColor, event: event, allDay: allday, color: mappingColor, display: 'block'  })        
      });
    }
    eventsList = eventsList
  })

  let options  = {
    viewDidMount: (e) => {
      console.log("mount:",e)
    },
    viewWillUnmount: (e) => {
      console.log("Unmount", e)
    },
    headerToolbar: {
      left: "myMonthButton,myWeekButton,myDayButton,myListButton",
      center: 'title',
      end: "myCustomPrev today myCustomNext"
    },
    customButtons: {
      myCustomNext: {
        text: ' > ',
        click: handleNext
      },
      myCustomPrev: {
        text: ' < ',
        click: handlePrev
      },
      myMonthButton: {
        text: "Mes",
        click: () => handleChangeView('dayGridMonth')
      },
      myWeekButton: {
        text: "Semana",
        click: () => handleChangeView('dayGridWeek')
      },
      myDayButton: {
        text: "Dia",
        click: () => handleChangeView('timeGridDayCustom')
      },
      myListButton: {
        text: "List",
        click: () => handleChangeView('listWeek')
      }
    },
    plugins: [daygridPlugin, listPlugin, timeGridPlugin],
    initialDate:  new Date(initialDate),
    locale: language,
    navLinks: true,
    dayMaxEvents: true,
    eventClick: (event)=>{
      calendarEvent({
        value: event.event
      })
      console.log(event.event.title)
    },
    navLinkDayClick: handleDayClick, //Function
    events:eventsList,
    eventColor: '#378006',
    eventTimeFormat: { // like '14:30:00'
      hour: '2-digit',
      minute: '2-digit',
      hour12: false
    },
    views: {
      timeGridDayCustom: {
        type: 'timeGrid',
        slotMinTime: "06:00:00",
        duration: {days: 1},
        slotDuration: "00:15:00",
        slotLabelInterval: "01:00:00"
      }
    },
    // eventTextColor: '#ff0000',
    ...langs[codeLang(language)]
  }
  const { styleable } = getContext("sdk") 
  const component = getContext("component")


  function handleNext(e) {
    const api = calendarComponentRef.getAPI();
    api.next()
    nextDateEvent({newDate: api.currentData.currentDate.toISOString()})
  }

  function handlePrev() {
    const api = calendarComponentRef.getAPI();
    api.prev()
    prevDateEvent({newDate: api.currentData.currentDate.toISOString()})
  }

  function handleChangeView(view) {
    const api = calendarComponentRef.getAPI();
    api.changeView(view)
  }

  function handleDayClick(date) {
    const api = calendarComponentRef.getAPI();
    api.gotoDate(date)
    handleChangeView('timeGridDayCustom')

  }

</script>

<div use:styleable={$component.styles}>
  <FullCalendar bind:this={calendarComponentRef} {options} />
</div>