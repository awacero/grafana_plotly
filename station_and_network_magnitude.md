### Code to generate the following image


![This is an image](/images/sc3_stats_station_network.png)


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

var mag = data.series[1].fields[2].values.buffer;
var s_m_type = data.series[1].fields[1].values.buffer;
var station_id = data.series[1].fields[3].values.buffer;
var mag_diff = data.series[1].fields[4].values.buffer;
var s_m_author = data.series[1].fields[5].values.buffer;
var info = []
station_id.forEach(
  (st_id,i)=> info[i]= '<br>'+s_m_author[i]
          + '<br>' + st_id 
          +'<br>'+ s_m_type[i] +': ' + mag[i])

var tr_box = {
  x: s_m_type,
  y: mag,
  jitter:0.5,
  type: 'box',
  name:'station_mag_boxplot'
 
};

var tr_s_m ={
  x: s_m_type,
  y: mag,
  text: station_id,
  hovertemplate: '%{text}: '+'%{y:.2f}',
  type:'scatter',
  mode: 'markers',
  name:'station magnitudes',
   marker:{
     color:mag,
        colorscale: shakemap,
        size:mag_diff,
        symbol:'circle-open-dot',
        colorbar:{x:-5, title:{text:'M'}}
   }
}
var n_mag_type = data.series[0].fields[1].values.buffer
var n_mag = data.series[0].fields[2].values.buffer

var tr_n_m ={ 
  x: n_mag_type,
  y: n_mag,
  type:'scatter',
  mode:'markers',
  marker:{ symbol: 'square-open',
            color:'black',
           size:15 } ,
  name:"network_mag"
}


return {data:[tr_s_m,tr_n_m,tr_box],layout:
{hovermode:'closest',
 legend:{'orientation':'h'}
 }
 };
```
