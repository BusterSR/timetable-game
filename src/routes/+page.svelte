<script>
	import { onMount, afterUpdate, onDestroy } from 'svelte';
	import Chart from 'chart.js/auto';
	import { Chart as SvelteChart, Line, CategoryScale } from 'svelte-chartjs';
	import { LineController, PointElement } from 'chart.js';




	let chartData = {
	  labels: [],
	  datasets: [
	    {
	      label: 'Questions Answered',
	      data: [],
	      fill: false,
	      borderColor: 'rgb(75, 192, 192)',
	      tension: 0.1,
	    },
	  ],
	};

	function updateChartData() {
	  chartData.labels = xValues;
	  chartData.datasets[0].data = yValues;
	}
	
	let chartInstance;

	
	onMount(() => {
	  const canvas = document.getElementById('lineChart');
	  const context = canvas.getContext('2d');
	  SvelteChart.register(Line, PointElement, CategoryScale);

	  chartInstance = new Chart(context, {
	    type: 'line',
	    data: chartData,
	    options: {
	      responsive: true,
	    },
	  });
	});

	afterUpdate(() => {
	  updateChartData();
	  chartInstance.update();
	});

	onDestroy(() => {
	  chartInstance.destroy();
	});


	import Timer from './Timer.svelte';
	let open = false;
	let ended = false;
	let seconds = 60;
	let correct = 0;
	let incorrect = 0;
	let guess = ""
	let xValues = [0]
	let yValues = [0]
	const all_timetables = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20]
	let timetables = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20]
	let data = [{time: 0, value: 0}]
	function toggleStart(){
		if (open == true){
			open = false;
		} else {
			ended = false;
			open = true;
			correct = 0;
			incorrect = 0;
			guess = "";
			seconds = 60;
		}
	}
	function handleTick(){
		data.push({time: 60-seconds, value: correct})
		data = data;
		xValues.push(60-seconds)
		xValues = xValues
		yValues.push(correct)
		yValues = yValues
		seconds -= 1
		if (seconds == 0){
			toggleStart();
			ended = true;
		}
	}
	$: num1 = timetables[Math.floor(Math.random() * timetables.length)];
	$: num2 = Math.floor(Math.random() * 12) + 1;
	$: answer = num1 * num2
	$: percentage = Math.floor((correct/(correct+incorrect))*100)
	$: speed = Math.floor(60/correct*100)/100
	function handleSubmit() {
		if (ended == false){
			if (parseInt(guess) == answer){
				correct += 1
			} else {
				incorrect += 1
			}
			guess = ""
			num1 = timetables[Math.floor(Math.random() * timetables.length)];
			num2 = Math.floor(Math.random() * 12) + 1;
		}
	}

	/**
     * @param {{ keyCode: number; }} e
     */
	function onKeyDown(e) {
		if (e.keyCode > 47 && e.keyCode < 58){
			let inputNum = e.keyCode - 48
			guess += inputNum.toString()
		} else if (e.keyCode == 8){
			guess = guess.slice(0, -1)
		} else if (e.keyCode == 13){
			handleSubmit()
		}
	}
	/**
     * @param {number} timetable
     */
	function toggleTimetable(timetable){
		if (timetables.includes(timetable)){
			const index = timetables.indexOf(timetable);
			timetables.splice(index, 1);
			timetables = timetables
		} else {
			timetables.push(timetable)
			timetables = timetables
		}
	}


</script>

<style>

.time {
	border-radius: 50%;
	padding: 7.5px;
	color: white;
	background-color: blue;
}

.correct {
	padding: 7.5px;
	background-color: green;
	color: white;
}

.incorrect {
	padding: 7.5px;
	background-color: red;
	color: white;
}

.question {
	display: inline;
}

#keypad {
	display: flex;
	flex-wrap: wrap; 
 	align-items: center;
	justify-content: center;
}

#keypad button {
	flex-grow: 1;
	width: 30%;
	padding-top: 20px;
	padding-bottom: 20px;
	margin: 5px;
}

.guess {
	padding: 10px;
	width: 90%;
	margin: 10px;
	color: black;
	border: 1px solid black;
	text-align: center;
	font-weight: bold;
	opacity: 1;
	background-color: white;
}

.timetable {
	color: white;
	margin: 5px;
}

.inactive {
	color: red;
	text-decoration: line-through;
}
</style>
<svelte:head>
	<title>Timetable Game</title>
</svelte:head>
<div>
	
	
	{#if ended}
		<p>The game has ended! You got <span class="correct">{correct}</span> questions correct and <span class="incorrect">{incorrect}</span> questions incorrect!</p>
		<p>You got {percentage}% of questions correct!</p>
		<p>Your speed was {speed} seconds per question.</p>
		<p>{xValues}</p>
		<p>{yValues}</p>
	

		<canvas id="lineChart"></canvas>
	{/if}
	<button on:click={toggleStart}>{open ? 'End' : 'Start'} Game</button>
	{#if open}
		<br/><span class="time">{seconds}</span>
		<Timer callback={handleTick} />
		<p class="question">What is {num1} x {num2}?</p>
		<span class="correct">{correct}</span>
		<span class="incorrect">{incorrect}</span><br/>
		<input class="guess" bind:value={guess} disabled/>
		<div id="keypad">
			<button on:click={() => guess += "1"}>1</button>
			<button on:click={() => guess += "2"}>2</button>
			<button on:click={() => guess += "3"}>3</button>
			<button on:click={() => guess += "4"}>4</button>
			<button on:click={() => guess += "5"}>5</button>
			<button on:click={() => guess += "6"}>6</button>
			<button on:click={() => guess += "7"}>7</button>
			<button on:click={() => guess += "8"}>8</button>
			<button on:click={() => guess += "9"}>9</button>
			<button on:click={() => guess = guess.slice(0, -1)}>Delete</button>
			<button on:click={() => guess += "0"}>0</button>
			<button on:click={handleSubmit}>Enter</button>
		</div>
	{:else}
		<h3>Timetables that will appear</h3>
		{#each all_timetables as timetable}
			<button on:click={() => toggleTimetable(timetable)} class="timetable {timetables.includes(timetable)?"":"inactive"}">{timetable}</button>
		{/each}
	{/if}
</div>
<svelte:window on:keydown|preventDefault={onKeyDown} />
