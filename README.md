<!doctype html>
<html>
<head>
<meta charset="utf-8"/>
<title>YouTube Trending Dashboard</title>
<style>
body{font-family:Arial;background:#f0f2f5;padding:20px;}
.filters{margin-bottom:20px;}
select{padding:6px 10px;margin-right:10px;}
.grid{display:grid;grid-template-columns:repeat(auto-fill,280px);gap:18px;}
.card{background:white;border-radius:10px;box-shadow:0 3px 6px rgba(0,0,0,0.1);}
.thumb{width:100%;height:160px;object-fit:cover;border-radius:10px 10px 0 0;}
.info{padding:10px}
.title{font-weight:bold;height:40px;overflow:hidden;}
.meta{color:#666;font-size:12px;margin-top:6px;}
</style>
</head>
<body>

<h1>YouTube Trending Dashboard</h1>

<div class="filters">
  <select id="channelFilter">
    <option value="">All Channels</option>
  </select>
  <select id="regionFilter">
    <option value="">All Regions</option>
  </select>
</div>

<div id="grid" class="grid">Loading…</div>

<script>
const API_URL = "https://script.google.com/macros/s/xxxxxx/exec"; // Apps Script API

function thumb(id){ return `https://i.ytimg.com/vi/${id}/hqdefault.jpg`; }

fetch(API_URL).then(r=>r.json()).then(data=>{
  const grid = document.getElementById("grid");
  grid.innerHTML = "";

  // 填入筛选器
  const channels = [...new Set(data.map(x=>x.ChannelName))];
  const regions  = [...new Set(data.map(x=>x.Region))];
  channels.forEach(c=>{
    const o=document.createElement("option");o.value=c;o.textContent=c;
    channelFilter.appendChild(o);
  });
  regions.forEach(r=>{
    const o=document.createElement("option");o.value=r;o.textContent=r;
    regionFilter.appendChild(o);
  });

  function render(){
    const cf = channelFilter.value;
    const rf = regionFilter.value;
    grid.innerHTML="";

    data.filter(r=>{
      return (!cf || r.ChannelName===cf) &&
             (!rf || r.Region===rf);
    }).sort((a,b)=>a.Rank-b.Rank).forEach(r=>{
      const card=document.createElement("div");
      card.className="card";
      card.innerHTML=`
        <img class="thumb" src="${thumb(r.VideoID)}">
        <div class="info">
          <div class="title">${r.Title}</div>
          <div class="meta">#${r.Rank} ｜ ${r.ChannelName}<br>${r.Region}</div>
        </div>
      `;
      grid.appendChild(card);
    });
  }

  channelFilter.onchange = render;
  regionFilter.onchange  = render;
  render();
});
</script>
</body>
</html>
