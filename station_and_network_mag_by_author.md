


##Javascript code to generate the following image

![This is an image](/images/station_network_magnitude_author.png)


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

var n_mag_type = data.series[0].fields[1].values.buffer
var n_mag = data.series[0].fields[2].values.buffer
var n_m_author = data.series[0].fields[3].values.buffer;

var s_m_type = data.series[1].fields[1].values.buffer;
var mag = data.series[1].fields[2].values.buffer;
var station_id = data.series[1].fields[3].values.buffer;
var mag_diff = data.series[1].fields[4].values.buffer;
var s_m_author = data.series[1].fields[5].values.buffer;

var s_m_auth =[]

s_m_author.forEach((a,i) => {
		if (a.indexOf('@') !== -1){
        s_m_auth[i] = a.substring(0,a.indexOf('@'))  }
        else{s_m_auth[i] = a }  })

var n_m_auth =[]
n_m_author.forEach((a,i) => {
		if (a.indexOf('@') !== -1){
        n_m_auth[i] = a.substring(0,a.indexOf('@'))  }
        else{n_m_auth[i] = a }  })

var info = []
station_id.forEach(
  (st_id,i)=> info[i]= '<br>'+s_m_author[i]
          + '<br>' + st_id 
          +'<br>'+ s_m_type[i] +': ' + mag[i])

var tr_n_m_author ={ 
  x: [n_m_auth,n_mag_type],
  y: n_mag,
  type:'scatter',
  name:"network_magnitude",
  mode:'markers',
  marker:{ 
          symbol: 'square-open',
          color:'black',
          size:15
            }
}

var tr_s_m_author ={ 
    x: [s_m_auth,s_m_type],
    y: mag,
    text:info,
    hovertemplate:'%{text}',
    type:'scatter',
    name:'station_magnitude',
    mode:'markers',
    marker:{
      color:mag,
      colorscale:shakemap,
      symbol:'circle-open',
      size:mag_diff,
      colorbar:{x:-5, title:{text:'M'}}
      }     
     }

return {data:[tr_s_m_author,tr_n_m_author],layout:{hovermode:'closest',
          legend:{ y:-0.1, 'orientation':'h'}}};

```


## INFLUX QUERIES

### QUERY A

```
SELECT "magnitude_type", "mag", "author" FROM "network_magnitude_variation" WHERE ("event_id" =~ /^$event_id$/) AND $timeFilter
```
### QUERY B

```
SELECT "station_magnitude_type", "mag", "station_id", "mag_diff"  *10, "author" FROM "magnitude_variation" WHERE ("event_id" =~ /^$event_id$/) AND $timeFilter

```



