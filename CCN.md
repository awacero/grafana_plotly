# Chiles Cerro Negro 

## Javascript code to generate the following image

![This is an image](/images/ccn_lat_lon.png)

```
//console.log(data)
//var st_code =  Array.from(data.series[2].fields[0].values._chunk.data.values)
var st_code = ['CERN','ICAN','ICHI','IMOR','IPAN',
              'ITER','CHL1','CHL2','CHMA','ECEN','LGNL'] 
var st_net =  Array.from(data.series[2].fields[1].values._chunk.data.values)
var st_lat = Array.from(data.series[2].fields[2].values._chunk.data.values)
var st_lon = Array.from(data.series[2].fields[3].values._chunk.data.values)
var st_elev =  Array.from(data.series[2].fields[4].values._chunk.data.values)

var lon = Array.from(data.series[0].fields[0].values._chunk.data.values)
var lat = Array.from(data.series[0].fields[1].values._chunk.data.values)
var elev = Array.from(data.series[0].fields[2].values._chunk.data.values)
var elevation = elev.map(function(x) {return x/1000});

var latitude = data.series[1].fields[1].values.buffer
var longitude = data.series[1].fields[2].values.buffer
var depth = data.series[1].fields[3].values.buffer
var mag_value= data.series[1].fields[4].values.buffer
var mag_size = data.series[1].fields[5].values.buffer
var ev_id = data.series[1].fields[6].values.buffer
var time_from = variables.__from
var time_to = variables.__to
var time_temp = data.series[1].fields[0].values.buffer
var time_stamp = time_temp.map(function(x){return ( time_to -x )/604800000;})
var time = data.series[1].fields[7].values.buffer

var scale = variables.scale
var size_dict = {'weeks':time_stamp,'depth':depth}
var color_dict={'weeks':'Viridis','depth':'Cividis'}

var info = []
ev_id.forEach(
  (ev,i)=> info[i]= '<br>'+ev  + '<br> '+ time[i]
          + '<br>' + mag_value[i]
          +'<br>'+ latitude[i] +'; ' 
          + longitude[i] + '; ' + depth[i] )


var tr_stations ={
  x:st_lon,
  y:st_lat,
  name:'',
  text:st_code,
  hovertemplate:'%{text}',
  type:"scatter",
  mode:'markers',
  marker:{
    symbol:'square-open',
    size:12
  }
}


var tr_sismos = {
  x:longitude,
  y:latitude,
  z:depth,
  name:'',
  type:'scatter',
  mode:'markers',
  text:info,
  hovertemplate:'%{text}',
  marker:{
    line:{width:0},
    size:mag_size,
    color:size_dict[scale],
    colorscale:color_dict[scale],
    reversescale:true,
    //colorbar:{x:-5}
  }

}


var tr_chiles = {
  x: lon,
  y: lat,
  z: elevation,
  text:"",
  hoverinfo:'skip',
  type:'contour',
  opacity:0.48,
  color:'rgb(163,78,30)'

};
  
return {data:[tr_chiles,tr_sismos,tr_stations],layout:{hovermode:'closest',

mapbox:{center:{lat:0.80,lon:-77.95},zoom:11}}};
```
