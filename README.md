<!doctype html>
<html>
<head>
  <meta charset="utf-8"/>
  <title>YouTube Trending Monitor</title>
  <style>
    body { font-family: Arial; margin: 20px; background: #f5f5f5; }
    h1 { margin-bottom: 10px; }
    #grid { display: grid; grid-template-columns: repeat(auto-fill, 260px); gap: 16px; }
    .card {
      background: #fff; border-radius: 10px; overflow: hidden;
      box-shadow: 0 2px 8px rgba(0,0,0,0.1);
    }
    .thumb { width: 100%; height: 150px; object-fit: cover; }
    .info { padding: 10px; }
    .title { font-weight: bold; font-size: 14px; margin-bottom: 6px; height: 40px; overflow: hidden; }
    .meta { color: #666; font-size: 12px; }
  </style>
</head>
<body>
  <h1>YouTube Trending Monitoring</h1>
  <div>Loading…</div>
  <div id="grid"></div>

  <script>
    // 这是你提供的 CSV 链接：
    const CSV_URL = "https://docs.google.com/spreadsheets/d/e/2PACX-1vQOykfixUCNnmqDjMceIiotauSroDzane0R9FXj9DvUJZcdRPta0_pq7pn5AtusU8h79YLN7P0c9iAv/pub?gid=0&single=true&output=csv";

    function parseCSV(text){
      const lines = text.trim().split('\\n');
      const headers = lines[0].split(',');
      return lines.slice(1).map(line=>{
        const cols = line.split(',');
        const obj = {};
        headers.forEach((h,i)=>{
          obj[h.trim()] = cols[i] ? cols[i].trim() : '';
        });
        return obj;
      });
    }

    function thumbURL(id){
      return id ? `https://i.ytimg.com/vi/${id}/hqdefault.jpg` : '';
    }

    fetch(CSV_URL)
      .then(r => {
        if (!r.ok) throw new Error('无法获取 CSV: ' + r.status);
        return r.text();
      })
      .then(text => {
        document.querySelector('div').innerText = '';
        const rows = parseCSV(text);
        rows.sort((a, b) => (Number(a.Rank) || 9999) - (Number(b.Rank) || 9999));

        const grid = document.getElementById('grid');
        rows.forEach(r => {
          const id = r.VideoID;
          const card = document.createElement('div');
          card.className = "card";
          card.innerHTML = `
            <img class="thumb" src="${thumbURL(id)}" alt="thumb">
            <div class="info">
              <div class="title">${r.Title}</div>
              <div class="meta">#${r.Rank} · ${r.ChannelName} · ${r.Region}</div>
            </div>
          `;
          grid.appendChild(card);
        });
      })
      .catch(err => {
        document.querySelector('div').innerText = 'Error: ' + err.message;
        console.error(err);
      });
  </script>
</body>
</html>
