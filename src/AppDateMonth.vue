<template>
  <div>
    <div ref="chart"></div>
    <FullCalendar
      :plugins="calendarPlugins"
      :initial-view="'timelineMonth'"
      :header="calendarHeader"
      :events="calendarEvents"
      @viewRender="updateChart"
    />
  </div>
</template>

<script>
import FullCalendar from "@fullcalendar/vue3";
import timeGridPlugin from "@fullcalendar/timegrid";
import Chart from "chart.js";

export default {
  components: {
    FullCalendar,
  },
  data() {
    return {
      calendarPlugins: [timeGridPlugin],
      calendarHeader: {
        left: "prev,next today",
        center: "title",
        right: "timelineMonth,timeGridWeek,timeGridDay",
      },
      calendarEvents: [
        {
          title: "Event 1",
          start: "2023-03-21T08:00:00",
          end: "2023-03-21T10:00:00",
        },
        {
          title: "Event 2",
          start: "2023-03-22T10:00:00",
          end: "2023-03-22T12:00:00",
        },
        {
          title: "Event 3",
          start: "2023-03-23T14:00:00",
          end: "2023-03-23T16:00:00",
        },
      ],
      chart: null,
    };
  },
  mounted() {
    this.createChart();
  },
  methods: {
    createChart() {
      const ctx = this.$refs.chart.getContext("2d");
      this.chart = new Chart(ctx, {
        type: "bar",
        data: {
          labels: [],
          datasets: [
            {
              label: "Events",
              data: [],
              backgroundColor: "rgba(54, 162, 235, 0.2)",
              borderColor: "rgba(54, 162, 235, 1)",
              borderWidth: 1,
            },
          ],
        },
        options: {
          scales: {
            yAxes: [
              {
                ticks: {
                  beginAtZero: true,
                },
              },
            ],
          },
        },
      });
    },
    updateChart() {
      const events = this.$refs.fullCalendar.getApi().getEvents();
      const eventDataByDate = {};
      events.forEach((event) => {
        const date = event.startStr.split("T")[0];
        if (!eventDataByDate[date]) {
          eventDataByDate[date] = {
            date,
            count: 0,
          };
        }
        eventDataByDate[date].count++;
      });
      const labels = Object.keys(eventDataByDate);
      const data = Object.values(eventDataByDate).map(
        (eventData) => eventData.count
      );
      this.chart.data.labels = labels;
      this.chart.data.datasets[0].data = data;
      this.chart.update();
    },
  },
};
</script>
