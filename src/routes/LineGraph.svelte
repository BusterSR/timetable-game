<script>
  import { onMount } from 'svelte';
  import Chart from 'chart.js/auto';
  import { Chart as SvelteChart, LineController, LineElement, LinearScale, PointElement, Title } from 'svelte-chartjs';

  // Define your chart data
  const chartData = {
    labels: ['January', 'February', 'March', 'April', 'May', 'June'],
    datasets: [
      {
        label: 'Data',
        data: [65, 59, 80, 81, 56, 55],
        fill: false,
        borderColor: 'rgb(75, 192, 192)',
        tension: 0.1,
      },
    ],
  };

  let chartInstance;

  // Create the chart when the component mounts
  onMount(() => {
    const canvas = document.getElementById('lineChart');
    const context = canvas.getContext('2d');
    SvelteChart.register(Chart, [
      LineController,
      LineElement,
      PointElement,
      LinearScale,
      Title,
    ]);
    chartInstance = new Chart(context, {
      type: 'line',
      data: chartData,
      options: {
        responsive: true,
      },
    });
  });
</script>

<canvas id="lineChart"></canvas>
