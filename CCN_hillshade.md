### JS code to generate a hillshade

***data must be in a CSV file***

```javascript
let volcan_longitude = data.series[0].fields[0].values.buffer;
let volcan_latitude = data.series[0].fields[1].values.buffer;
let volcan_elevation = data.series[0].fields[2].values.buffer;

let hillshade_dict = { 'gray': 'https://raw.githubusercontent.com/awacero/grafana_plotly/main/images/chiles_hillshade_gray.jpeg', 
'color': 'https://raw.githubusercontent.com/awacero/grafana_plotly/main/images/chiles_hillshade_color.jpeg_' } 

let curve_level_dict = { 'show': 'https://raw.githubusercontent.com/awacero/grafana_plotly/main/images/chiles_curves_100.png', 
'hide': ''} 

let row = data.series[5].fields[0].values.buffer
let column = data.series[5].fields[1].values.buffer
let r = data.series[5].fields[2].values.buffer
let g = data.series[5].fields[3].values.buffer
let b = data.series[5].fields[4].values.buffer


const x_length = 792
const y_length = 1080
const hillshadeData = new Array(x_length);
for(let i=0; i<x_length; i++ ){
  hillshadeData[i] = new Array(y_length);
  for(let j=0;j<y_length;j++){
    hillshadeData[i][j]=[r[y_length*i + j],g[y_length*i + j],b[y_length*i + j]]

  }
  
}

var data_rgb=
  {
  "dx":0.0002777777777778146,"dy":-0.0002777777777778146,"x0":-78.10013888887539,"y0":0.9201388888810627,
  z:hillshadeData,
  type:'image',
  hoverinfo:'none'
  }


var volcan_trace = {
  x: volcan_longitude,
  y: volcan_latitude,
  z: volcan_elevation,
  text:"",
  hoverinfo:'skip',
  type:'contour',
  opacity:0.48,
  showscale:true,
  colorbar:{
    title:{text:'height'},
    x:0.9,
    y:0.3,
    xanchor:"right",
    yanchor:"bottom",
    len:0.70,
    thickness:0.02,
    thicknessmode:'fraction',
    }
  //color:'rgb(163,78,30)',
  
};


let station_net = data.series[1].fields[0].values.buffer;
let station_code = data.series[1].fields[1].values.buffer;
let station_longitude = data.series[1].fields[2].values.buffer;
let station_latitude = data.series[1].fields[3].values.buffer;
let station_elevation = data.series[1].fields[4].values.buffer;

var stations_trace ={
  x:station_longitude,
  y:station_latitude,
  name:'',
  text:station_code,
  hovertemplate:'%{text}',
  hoverinfo:'none',
  type:"scatter",
  mode:'markers+text',
  marker:{
    symbol:'square',
    size:13,
    line:{width:1},
    color:'#FFCBDB',
    opacity:0.5
  },
  textfont:{size:17, color:'black', family:'Arial Black,sans-serif',weight:'bold'},
  textposition:'top-left',
  textemplate:'%{text}',
}
let zoom_factor=6
var event_latitude = data.series[4].fields[1].values.buffer
var event_latitude_error = data.series[4].fields[2].values.buffer
var event_latitude_zoom = event_latitude_error.map(function(x){return x*zoom_factor})

var event_longitude = data.series[4].fields[3].values.buffer
var event_longitude_error = data.series[4].fields[4].values.buffer
var event_longitude_zoom = event_longitude_error.map(function(x){return x*zoom_factor})

var event_depth = data.series[4].fields[5].values.buffer
var event_depth_error = data.series[4].fields[6].values.buffer
var event_depth_zoom = event_depth_error.map(function(x){return x*zoom_factor})



var magnitude= data.series[4].fields[7].values.buffer
var magnitude_zoom = magnitude.map(function(x){return x*zoom_factor})
var rms = data.series[4].fields[8].values.buffer
var rms_zoom = rms.map(function(x){return x*zoom_factor})
var event_id = data.series[4].fields[9].values.buffer

var time_from = variables.__from
var time_to = variables.__to /1000;
var time_temp = data.series[4].fields[0].values.buffer
var time_stamp = time_temp.map(function(x){return ( time_to -x )/86400;})


var event_type = data.series[4].fields[10].values.buffer
var event_author = data.series[4].fields[13].values.buffer
var event_time = data.series[4].fields[14].values.buffer

var type_dict ={'VT':'violet', 'VTD':'darkviolet', 
                'HB':'orange','EXP':'green',
                'LP':'red','VLP':'darkred'}
var type_symbol_dict = {'VT':'diamond','VTD':'diamond-open',
                          'HB':'circle','EXP':'circle',
                      'LP':'circle','VLP':'circle-open'}

var event_color = [];
var event_symbol = [];

event_type.forEach((e_t,i) =>{
  if (type_dict[e_t]){
    event_color[i]=type_dict[e_t]
  }
  else{event_color[i]='green'}
})

event_type.forEach((e_t,i) =>{
  if (type_dict[e_t]){
    event_symbol[i]=type_symbol_dict[e_t]
  }
  else{event_symbol[i]='cross'}
})

var color_scale = variables.color_scale
var size = variables.size
var hillshade = variables.hillshade
var curve_level = variables.curve_level

var color_scale_dict = {'dias':time_stamp,'prof.':event_depth,'tipo_evento': event_color}
var color_map_dict={'dias':'Inferno','prof.':'Viridis','tipo_evento':event_color}
var size_dict ={'fixed':10,'magnitude':magnitude_zoom,'rms':rms_zoom,
                'lat_e':event_latitude_zoom,'lon_e':event_longitude_zoom,'depth_e':event_depth_zoom}

var info = []
event_id.forEach(
  (ev,i)=> info[i]= '<br>'+ev  
          +'<br>'+event_type[i]
          +'<br>'+event_author[i]
          + '<br> '+ event_time[i]
          + '<br>mag: ' + ( magnitude[i] !== null ? magnitude[i].toFixed(3) : 'N/D')
          + '<br>rms:' + rms[i]
          + '<br>lat: ' + (event_latitude[i] !== null ? event_latitude[i].toFixed(3) : 'N/D')  
          + '; +/- ' + (event_latitude_error[i] !== null ? event_latitude_error[i].toFixed(3) : 'N/D')
          + '<br>lon: ' + (event_longitude[i] !== null ? event_longitude[i].toFixed(3) : 'N/D') 
          + '; +/- ' + (event_longitude_error[i] !== null ? event_longitude_error[i].toFixed(3) : 'N/D')
          + '<br>depth: ' + (event_depth[i] !== null ? event_depth[i] : 'N/D') 
          + '; +/- ' + (event_depth_error[i] !== null ? event_depth_error[i].toFixed(3) : 'N/D')
           )


var sismos_trace = {
  x:event_longitude,
  y:event_latitude,
  z:event_depth,
  name:'',
  type:'scatter',
  mode:'markers',
  text:info,
  hovertemplate:'%{text}',
  hoverlabel:{
    font:{
      size:20
    }
    },
  marker:{
    line:{width:0},
    size:size_dict[size],
    symbol:event_symbol,
    color:color_scale_dict[color_scale],
    colorscale:color_map_dict[color_scale],
    //reversescale:true,
    colorbar:{
      title:{
        text:variables.color_scale,
        font:{size:25},
        //reversescale: true         
        },
      x:1.,
      y:0.3,
      xanchor: "right",
      yanchor:"bottom",
      len:0.70,
      thickness:0.02,
      thicknessmode:'fraction',
      },
    opacity:1.0,
    line:{width:1, color:"#444444"}
  }
};



var layout = {
  font:{color:'black'},
  margin:{t:40,r:0,b:50,l:70},
  title:{   text: "Eventos sísmicos en Chiles Cerro Negro", 
            font:{size:25}  },
  showlegend:false,
  hovermode: 'closest',
  xaxis: {
    constrain:"domain",
    constraintoward:"left",
    //scaleanchor:"y",
    //scaleration:1.9,
    type:'linear',
    autorange:true,
    domain:[0,1],
    mirror: 'ticks',
    nticks: 7,
    tickmode:'auto',
    tickfont:{size:15},
    ticks:'outside',
    range: [-78.1, -77.8],
    title: { text: 'Longitud (º)', font:{size:25} },
    ///fixedrange: true // Deshabilita el zoom y pan en el eje X
  },
  yaxis: {
    scaleanchor:"y",
    scaleration:1.9,
    automargin:false,
    autorange:false,
    range:[0.7,0.92],
    tickfont:{size:15},
    ticks:'outside',
    title: {
      text: 'Latitud (º)',
      font: {
        size:25
      }
    },
    type:'linear',
    ///fixedrange: true,


  }
};


//stations_trace, sismos_trace,

return { data: [ stations_trace,sismos_trace,data_rgb],  layout:layout};

console.log(info)


```
