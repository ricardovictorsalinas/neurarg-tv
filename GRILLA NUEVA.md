<!DOCTYPE html>
<html>
<head>
<meta charset="UTF-8">
<title>NEURARG TV</title>

<style>
body{
  margin:0;
  background:#0b0b0b;
  color:white;
  font-family:Arial;
}

.header{
  padding:15px;
  font-size:22px;
  background:#111;
}

.timeline{
  display:flex;
  margin-left:150px;
  color:#aaa;
  font-size:12px;
  padding:5px;
}

.row{
  display:flex;
  margin-bottom:6px;
}

.channel{
  width:150px;
  padding:5px;
  font-weight:bold;
  background:#111;
}

.program{
  width:200px;
  background:#1a1a1a;
  margin-right:4px;
  padding:6px;
  border-radius:6px;
  font-size:12px;
  transition:0.2s;
}

.program:hover{
  background:#333;
}
</style>
</head>

<body>

<div class="header">📺 NEURARG TV - Programación en vivo</div>

<div class="timeline" id="timeline"></div>
<div id="grid"></div>

<script>
const canales = [
  {id:"Telefe.ar", nombre:"Telefe"},
  {id:"ElTrece.ar", nombre:"El Trece"},
  {id:"AmericaTV.ar", nombre:"América TV"},
  {id:"Canal9.ar", nombre:"El Nueve"},
  {id:"TVPublica.ar", nombre:"TV Pública"},
  {id:"C5N.ar", nombre:"C5N"},
  {id:"TN.ar", nombre:"TN"},
  {id:"CronicaTV.ar", nombre:"Crónica TV"}
];

function generarTimeline(){
  let now = new Date();
  let html="";
  for(let i=0;i<6;i++){
    let h = new Date(now.getTime()+i*3600000);
    let hora = h.getHours().toString().padStart(2,'0')+":00";
    html += `<div style="width:200px;">${hora}</div>`;
  }
  document.getElementById("timeline").innerHTML = html;
}

async function cargar(){
  try{
    let res = await fetch("https://api.allorigins.win/raw?url=https://iptv-org.github.io/epg/guides/ar.xml");
    let text = await res.text();
    let xml = new DOMParser().parseFromString(text,"text/xml");

    let html="";

    canales.forEach(c=>{
      let progs = [...xml.querySelectorAll(`programme[channel="${c.id}"]`)].slice(0,6);

      html += `<div class="row">`;
      html += `<div class="channel">${c.nombre}</div>`;

      progs.forEach(p=>{
        let titulo = p.querySelector("title")?.textContent || "Sin info";
        html += `<div class="program">${titulo}</div>`;
      });

      html += `</div>`;
    });

    document.getElementById("grid").innerHTML = html;

  }catch(e){
    document.getElementById("grid").innerHTML = "Error cargando programación";
  }
}

generarTimeline();
cargar();
setInterval(cargar,60000);
</script>

</body>
</html>
