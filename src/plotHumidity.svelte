<script>
	// Create a client instance
	import {version} from './stores';
	var wsbroker = "wondrous-lifeguard.cloudmqtt.com";  //mqtt websocket enabled broker
	var wsport = 443 // port for above
	var client = new Paho.MQTT.Client(wsbroker, wsport, "myclientid_" + parseInt(Math.random() * 100, 10));
	
	// set callback handlers
	client.onConnectionLost = onConnectionLost;
	client.onMessageArrived = onMessageArrived;
	var options = {
	timeout: 3,
	onSuccess: onConnect,
	userName : "air-heritage",
	password : "new-age",
	useSSL: true,
	onFailure: doFail
}

// connect the client
client.connect(options);

// called when the client connects
function onConnect() {
	// Once a connection has been made, make a subscription and send a message.
	console.log("onConnectiii");
	// client.subscribe("/AirHeritage/"+ServerName+"/#");
	client.subscribe("GreenhouseKenia");
}

function doFail(e){
	console.log(e);
}

// called when the client loses its connection
function onConnectionLost(responseObject) {
	if (responseObject.errorCode !== 0) {
		console.log("onConnectionLost:"+responseObject.errorMessage);
	}
}

// called when a message arrives
function onMessageArrived(message) {
	console.log("onMessageArrived:"+message.payloadString);
	const obj = JSON.parse(message.payloadString);
	if(cnt == 0) {
		Plotly.plot('RHchart',[{
			y:[obj.BME280_RH],
			type:'line'
		}]);					
		} else {
		Plotly.extendTraces('RHchart',{ y:[[obj.BME280_RH]]}, [0]);
	}
	cnt++;
	if(cnt > 30) {
		Plotly.relayout('RHchart',{
			xaxis: {
				range: [cnt-30,cnt]
			}
		});
	}
}	

			
var cnt = 0;


</script>

<section id="PlotSection" class="visible">
<h1>Sensor Monitoring {$version}</h1>
<h2>RH %</h2>
<div id="RHchart"></div>
</section>
