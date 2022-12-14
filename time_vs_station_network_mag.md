### Javascript code to generate the following image

![This is an image](/images/time_vs_station_network_mag.png)



```
console.log(data)

var  shakemap = [
    ['0.0',            'rgb(128,128,255)'],
    ['0.111111111111', 'rgb(191,204,255)'],
    ['0.222222222222', 'rgb(160,230,255)'],
    ['0.333333333333', 'rgb(128,255,255)'],
    ['0.444444444444', 'rgb(122,255,147)'],
    ['0.555555555556', 'rgb(255,255,0)'],
    ['0.666666666667', 'rgb(255,200,0)'],
    ['0.777777777778', 'rgb(255,145,0)'],
    ['0.888888888889', 'rgb(255,0,0)'],
    ['1.0', 'rgb(200,0,0)']
  ]
var s_author =  data.series[0].fields[1].values.buffer
var s_mag =  data.series[0].fields[2].values.buffer
var s_id =  data.series[0].fields[3].values.buffer
var s_mag_type =  data.series[0].fields[4].values.buffer
var s_modiff_time =  data.series[0].fields[5].values.buffer

var net_mag = data.series[1].fields[1].values.buffer
var net_modiff_time = data.series[1].fields[2].values.buffer
var net_author =  data.series[1].fields[5].values.buffer
var net_type =  data.series[1].fields[4].values.buffer
var net_count =  data.series[1].fields[3].values.buffer

var info = []
net_mag.forEach(
  (n_m,i)=> info[i]= '<br>'+net_author[i]
          +'<br>'+ net_type[i] +': ' + net_mag[i]
          +'<br> station count: '+ net_count[i]
          )

var s_info = []
s_mag.forEach(
  (s_m,i)=> s_info[i]= '<br>'+s_author[i]
          +'<br>'+ s_mag_type[i] 
          +'<br>' + s_id[i]+ ': ' + s_m
          
          )




var tr_net = {
  x:net_mag,
  y: net_modiff_time,
  name:'net mag',
  text: info,
  hovertemplate:'%{text}',
  type:'scatter',
  mode:'markers',
  marker:{
  symbol:'square-open',
  size:net_count
  }
}

var tr_s_m = {
  x: s_mag,
  y: s_modiff_time,
  name:"station mag",
  text:s_info,
  hovertemplate:'%{text}',
  type:'scatter',
  mode:'markers',
   marker:{
        color:s_mag,
        colorscale: shakemap,
        size:s_mag,
        symbol:'circle-open',
        colorbar:{x:-5,title:{text:'Mag val'}}, 
        } 
};
  
return {data:[tr_s_m,tr_net],
layout:{
  hovermode:'closest',
  yaxis:{title:{text:'Modification time'}},
  xaxis:{title:{text:'Magnitude value'}},
  legend:{ y:-0.2, 'orientation':'h'}
  }
};

```

## INFLUX QUERIES
### Query A
```
SELECT "author", "mag", "station_id", "station_magnitude_type", "modification_time" FROM "magnitude_variation" WHERE ("event_id" =~ /^$event_id$/) AND $timeFilter

```

### Query B
```
SELECT "mag", "modification_time", "station_count", "magnitude_type", "author" FROM "network_magnitude_variation" WHERE ("event_id" =~ /^$event_id$/) AND $timeFilter
``` 
