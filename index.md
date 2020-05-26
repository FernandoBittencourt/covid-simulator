<html>
	<head>
		<link href="https://cdnjs.cloudflare.com/ajax/libs/c3/0.4.10/c3.min.css" rel="stylesheet" />
		<script src="https://cdnjs.cloudflare.com/ajax/libs/d3/3.5.6/d3.min.js"></script>
		<script src="https://cdnjs.cloudflare.com/ajax/libs/c3/0.4.10/c3.min.js"></script>
	</head>
	<body>
		<h1>The COVID-19 Simulator</h1>
		<h2>A simulator of the impact of a disease, inspired by the situation of COVID-19 with the objective of raising people's awareness.</h2>
		
		<fieldset>
			<label>Population Total</label>
			<input id="population" type="number"></input>
			<span>The total population.</span>
		</fieldset>	
		<fieldset>
			<label>Initial Infected Rate</label>
			<input id="infectedRate" type="number"></input>
			<span>The Initial Infected Rate represents the percentage of infected people in relation to the total population.</span>
		</fieldset>		
		<fieldset>
			<label>Dead Rate</label>
			<input id="deadRate" type="number"></input>
			<span>The dead rate for a measure of time.</span>
		</fieldset>	
		<fieldset>
			<label>Recovery Time</label>
			<input id="rTime" type="number"></input>
			<span>The approximate time it takes an infected person to recover from the disease.</span>
		</fieldset>
		<fieldset>
			<label>Growth factor</label>
			<input id="gFactor" type="number"></input>
			<span>The growth factor is an infected person's ability to infect other people. That is, if the factor is 2, an infected person will infect 2 people for a measure of time.</span>
			<span>The growth rate in many countries that have had no action against the virus was 2 or 3.</span>
		</fieldset>	
		<button onclick="start()">Start</button>
		<button>Update</button>
		<div id="status"></div>
		<div id="chart"></div>
		<div id="board"></div>
		<footer>This simulator is for teaching purposes only, it cannot be used as a basis for any study or research.</footer>
		<script>
			var population=0;
			var infectedRate=0;
			var rTime=0;
			var gFactor=0;

			var infected = [];
			var recovered = [];
			var dead = [];
			var time=0;
			var deadRate=0;
			
			var chart;
			
			function start(){
				time=0;
				population=document.getElementById("population").value;
				infectedRate=document.getElementById("infectedRate").value;
				deadRate=document.getElementById("deadRate").value;
				rTime=document.getElementById("rTime").value;
				gFactor=document.getElementById("gFactor").value;
				var initial=population*infectedRate;	
				
				infected.push(calculateInfected(initial, gFactor, time));
				recovered.push(0);
				dead.push(0);

			
				console.log("infected: "+infected);
				console.log("recovered: "+recovered);
				console.log("dead: "+dead);
				setTimeout(updateData, 5000);



				var colInfected=['infected'];
				colInfected=colInfected.concat(infected);
				var colRecovered=['recovered'];
				colRecovered=colRecovered.concat(recovered);
				var colDead=['dead'];
				colDead=colDead.concat(dead);				
				chart = c3.generate({
					bindto: '#chart',
					data: {
						columns: [
							colInfected,
							colRecovered,
							colDead
						]
					}
				});		
			}
			
			function updateChart(){
				var colInfected=['infected'];
				colInfected=colInfected.concat(infected);
				var colRecovered=['recovered'];
				colRecovered=colRecovered.concat(recovered);
				var colDead=['dead'];
				colDead=colDead.concat(dead);
				
				chart.load({
						columns: [
							colInfected,
							colRecovered,
							colDead
						]
				});
			}
			
			function updateData(){	
				if(time>2 && infected[time-2]<=0){
					return;
				}
				time++;
				var deadPeople=0;
				if(time>1) {
					deadPeople=dead[time-1];
					if(infected[time-1]*deadRate>0){
						deadPeople=Math.floor(infected[time-1]*deadRate)+dead[time-1];
					}
				}
				dead.push(deadPeople);
				
				var recoveredPeople=0;
				if(time>rTime){
					recoveredPeople=recovered[time-1];
					if(infected[time-rTime]>0){
						recoveredPeople=Math.floor(infected[time-rTime]*(1-deadRate))+recovered[time-1];
						if(recoveredPeople>population-deadPeople){
							recoveredPeople=population-deadPeople;
						}
					}
				}				
				recovered.push(recoveredPeople);
			
				infectedPeople=calculateInfected(infected[time-1]-recovered[time-1]-dead[time-1], gFactor, time);				
				if(infectedPeople>population-recoveredPeople-deadPeople){
					infectedPeople=population-recoveredPeople-deadPeople;					
				}
				if(infectedPeople<0){
					infectedPeople=0;
				} 
				infected.push(infectedPeople);
								
				console.log("infected: "+infectedPeople);
				console.log("recovered: "+recoveredPeople);
				console.log("dead: "+deadPeople);
				setTimeout(updateData, 5000);
				updateChart();
			}
			
			function calculateInfected(xo, b, t) {
				return xo*Math.pow(b,t);
			}
		</script>
	</body>
</html>
