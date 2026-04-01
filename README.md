<!doctype html>
<html lang="id" class="h-full">
 <head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>List Seserahan Pernikahan</title>
  <script src="https://cdn.tailwindcss.com/3.4.17"></script>
  <script src="https://cdn.jsdelivr.net/npm/lucide@0.263.0/dist/umd/lucide.min.js"></script>
  <link href="https://fonts.googleapis.com/css2?family=Playfair+Display:wght@400;600;700&amp;family=Nunito:wght@400;500;600;700&amp;display=swap" rel="stylesheet">
  <style>
    .check-item { transition: all 0.3s ease; }
    .check-item.checked .item-text { text-decoration: line-through; opacity: 0.5; }
    .check-item:hover { transform: translateX(4px); }
    .category-card { animation: fadeUp 0.5s ease forwards; opacity: 0; }
    @keyframes fadeUp { from { opacity:0; transform:translateY(16px); } to { opacity:1; transform:translateY(0); } }
    .progress-fill { transition: width 0.6s cubic-bezier(0.4,0,0.2,1); }
    .checkmark { transition: all 0.25s ease; }
    .checkmark.active { transform: scale(1.15); }
  </style>
  <style>body { box-sizing: border-box; }</style>
  <script src="/_sdk/data_sdk.js" type="text/javascript"></script>
  <script src="/_sdk/element_sdk.js" type="text/javascript"></script>
 </head>
 <body class="h-full">
  <div id="app" class="w-full h-full overflow-auto" style="background:#fdf6f0; font-family:'Nunito',sans-serif;">
   <!-- Header -->
   <div style="background:linear-gradient(135deg,#c9a87c 0%,#b8926a 100%);" class="px-6 py-10 text-center relative overflow-hidden">
    <div style="position:absolute;top:-30px;right:-30px;width:120px;height:120px;border-radius:50%;background:rgba(255,255,255,0.08);"></div>
    <div style="position:absolute;bottom:-20px;left:-20px;width:80px;height:80px;border-radius:50%;background:rgba(255,255,255,0.06);"></div>
    <div class="text-4xl mb-2">
     💍
    </div>
    <h1 id="main-title" style="font-family:'Playfair Display',serif;color:#fff;font-size:28px;font-weight:700;letter-spacing:0.5px;">List Seserahan &amp; Lamaran Pernikahan</h1>
    <p id="subtitle" style="color:rgba(255,255,255,0.85);font-size:14px;margin-top:6px;">Persiapan dengan penuh cinta</p>
    <p id="last-updated" style="color:rgba(255,255,255,0.7);font-size:12px;margin-top:4px;">Terakhir diperbarui: -</p><!-- Progress -->
    <div class="mt-6 mx-auto" style="max-width:320px;">
     <div class="flex justify-between text-xs mb-1" style="color:rgba(255,255,255,0.8);">
      <span id="progress-label">0 dari 20 item</span> <span id="progress-pct">0%</span>
     </div>
     <div style="background:rgba(255,255,255,0.25);border-radius:99px;height:8px;overflow:hidden;">
      <div id="progress-bar" class="progress-fill" style="height:100%;width:0%;background:#fff;border-radius:99px;"></div>
     </div>
    </div>
   </div><!-- Tabs Navigation -->
   <div class="sticky top-0 z-10 flex gap-0 px-4 pt-4" style="background:#fdf6f0;border-bottom:1px solid #e8ddd3;"><button id="tab-seserahan" onclick="switchTab('seserahan')" class="tab-btn px-4 py-3 font-semibold text-sm rounded-t-lg" style="color:#c9a87c;border-bottom:3px solid #c9a87c;">📦 Seserahan</button> <button id="tab-lamaran" onclick="switchTab('lamaran')" class="tab-btn px-4 py-3 font-semibold text-sm rounded-t-lg" style="color:#b8926a;opacity:0.6;">💐 Lamaran</button>
   </div><!-- Seserahan Tab -->
   <div id="tab-content-seserahan" class="px-4 py-6 mx-auto" style="max-width:600px;">
    <div class="mb-6 rounded-2xl p-5" style="background:#fff;box-shadow:0 2px 12px rgba(0,0,0,0.06);">
     <h3 style="font-family:'Playfair Display',serif;font-size:16px;font-weight:600;color:#3d3530;margin-bottom:4px;">Visualisasi Progress</h3>
     <div class="flex items-center justify-center gap-8 py-4">
      <canvas id="pieChart" width="150" height="150" style="max-width:150px;"></canvas>
      <div style="flex:1;">
       <div class="flex items-center gap-2 mb-2">
        <div style="width:12px;height:12px;border-radius:2px;background:#7c9a6e;"></div><span style="font-size:13px;color:#3d3530;">Selesai</span>
       </div>
       <div class="flex items-center gap-2">
        <div style="width:12px;height:12px;border-radius:2px;background:#e8ddd3;"></div><span style="font-size:13px;color:#3d3530;">Belum</span>
       </div>
      </div>
     </div>
    </div>
    <div id="categories-container"></div>
   </div><!-- Lamaran Tab -->
   <div id="tab-content-lamaran" class="px-4 py-6 mx-auto" style="max-width:600px;display:none;">
    <div id="lamaran-container"></div>
   </div><!-- Footer -->
   <div class="text-center py-6 px-4" style="color:#b8926a;font-size:12px;opacity:0.7;">
    <span>✨ Semoga pernikahan penuh berkah ✨</span>
   </div>
  </div>
  <script>
const CATEGORIES = [
  { id:'ibadah', icon:'🕌', title:'Perlengkapan Ibadah', color:'#7c9a6e',
    items:[{id:'sajadah',name:'Sajadah'},{id:'mukena',name:'Mukena'}]},
  { id:'pakaian', icon:'👗', title:'Pakaian & Perlengkapan Pribadi', color:'#c9789a',
    items:[{id:'gamis',name:'Gamis + Kerudung'},{id:'baju_tidur',name:'Baju Tidur'},{id:'tas_sepatu',name:'Tas & Sepatu'},{id:'pakaian_dalam',name:'Pakaian Dalam'}]},
  { id:'kosmetik', icon:'💄', title:'Kosmetik', color:'#d4856a',
    items:[{id:'makeup',name:'Make-up Dasar'},{id:'skincare',name:'Skincare (1 paket)'},{id:'parfum',name:'Parfum'},{id:'handbody',name:'Hand Body'}]},
  { id:'rumah', icon:'🏠', title:'Peralatan Rumah Tangga Utama', color:'#6a8fc9',
    items:[{id:'sprimbet',name:'Sprimbet Kasur'},{id:'lemari',name:'Lemari Pakaian'},{id:'bedcover',name:'Bedcover + Bantal & Guling'},{id:'selimut_handuk',name:'Selimut & Handuk'},{id:'set_piring',name:'Set Piring, Mangkok, Sendok, Garpu, Gelas'},{id:'panci_set',name:'Panci, Wajan, Teflon (1 set)'},{id:'kipas',name:'Kipas Angin'}]},
  { id:'makanan', icon:'🎁', title:'Makanan Hantaran', color:'#c9a244',
    items:[{id:'kue_tradisional',name:'Kue Tradisional'},{id:'roti_bolu',name:'Roti Modern / Bolu'},{id:'parcel_buah',name:'Parcel Buah-buahan'}]}
];

const LAMARAN_ITEMS = [
  { id:'mahar', name:'Mahar/Mas Kawin', checked:false },
  { id:'cincin', name:'Cincin Emas/Perak', checked:false },
  { id:'perhiasan', name:'Perhiasan Lainnya', checked:false },
  { id:'kain', name:'Kain (untuk calon istri)', checked:false },
  { id:'buah', name:'Buah-buahan', checked:false },
  { id:'kue', name:'Kue/Pastry', checked:false }
];

const ALL_ITEMS = CATEGORIES.flatMap(c => c.items.map(i => i.id));
const TOTAL = ALL_ITEMS.length;
let checkedState = {};
let lamaranState = {};

// Build Seserahan UI
const container = document.getElementById('categories-container');
CATEGORIES.forEach((cat, ci) => {
  const card = document.createElement('div');
  card.className = 'category-card rounded-2xl p-5 mb-4';
  card.style.cssText = `background:#fff;box-shadow:0 2px 12px rgba(0,0,0,0.06);animation-delay:${ci*0.08}s;`;
  let itemsHTML = '';
  cat.items.forEach(item => {
    itemsHTML += `
    <div class="check-item flex items-center gap-2 py-3 px-2 rounded-lg" data-id="${item.id}">
      <div id="box-${item.id}" class="checkmark flex-shrink-0 w-6 h-6 rounded-md border-2 flex items-center justify-center cursor-pointer" style="border-color:${cat.color};background:transparent;transition:all 0.25s ease;" onclick="toggleItem('${item.id}')">
        <svg id="svg-${item.id}" width="14" height="14" viewBox="0 0 14 14" fill="none" style="opacity:0;transition:opacity 0.2s;"><path d="M2.5 7.5L5.5 10.5L11.5 3.5" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>
      </div>
      <span class="item-text flex-1" id="text-${item.id}" style="font-size:15px;color:#3d3530;font-weight:500;">${item.name}</span>
      <input type="date" id="date-${item.id}" class="date-input flex-shrink-0 px-2 py-1 rounded-lg border text-sm" style="border-color:${cat.color}22;background:#fff;color:#3d3530;font-size:13px;width:130px;" onchange="setItemDate('${item.id}')">
    </div>`;
  });
  card.innerHTML = `
    <div class="flex items-center gap-2 mb-3">
      <span class="text-xl">${cat.icon}</span>
      <h2 style="font-family:'Playfair Display',serif;font-size:17px;font-weight:600;color:#3d3530;">${cat.title}</h2>
      <span id="count-${cat.id}" class="ml-auto text-xs font-bold rounded-full px-2 py-0.5" style="background:${cat.color}20;color:${cat.color};">0/${cat.items.length}</span>
    </div>
    <div class="space-y-1">${itemsHTML}</div>`;
  container.appendChild(card);
});

// Build Lamaran UI
const lamaranContainer = document.getElementById('lamaran-container');
LAMARAN_ITEMS.forEach((item, idx) => {
  const card = document.createElement('div');
  card.className = 'rounded-2xl p-4 mb-3 flex items-center gap-3';
  card.style.cssText = `background:#fff;box-shadow:0 2px 8px rgba(0,0,0,0.06);transition:all 0.2s;`;
  card.id = 'lamaran-card-' + item.id;
  card.innerHTML = `
    <div id="lamaran-box-${item.id}" onclick="toggleLamaran('${item.id}')" style="width:24px;height:24px;border-radius:6px;border:2px solid #c9a87c;background:transparent;display:flex;align-items:center;justify-content:center;flex-shrink:0;transition:all 0.2s;cursor:pointer;">
      <svg id="lamaran-svg-${item.id}" width="12" height="12" viewBox="0 0 14 14" fill="none" style="opacity:0;transition:opacity 0.2s;"><path d="M2.5 7.5L5.5 10.5L11.5 3.5" stroke="#fff" stroke-width="2" stroke-linecap="round" stroke-linejoin="round"/></svg>
    </div>
    <span id="lamaran-text-${item.id}" style="font-size:15px;color:#3d3530;font-weight:500;flex:1;">${item.name}</span>
    <input type="date" id="lamaran-date-${item.id}" class="lamaran-date-input flex-shrink-0 px-2 py-1 rounded-lg border text-sm" style="border-color:#c9a87c22;background:#fff;color:#3d3530;font-size:13px;width:130px;" onchange="setLamaranDate('${item.id}')">`;
  lamaranContainer.appendChild(card);
});

function switchTab(tab) {
  const seserahanTab = document.getElementById('tab-seserahan');
  const lamaranTab = document.getElementById('tab-lamaran');
  const seserahanContent = document.getElementById('tab-content-seserahan');
  const lamaranContent = document.getElementById('tab-content-lamaran');
  
  if(tab === 'seserahan') {
    seserahanTab.style.cssText = 'color:#c9a87c;border-bottom:3px solid #c9a87c;opacity:1;';
    lamaranTab.style.cssText = 'color:#b8926a;border-bottom:none;opacity:0.6;';
    seserahanContent.style.display = 'block';
    lamaranContent.style.display = 'none';
    drawPieChart();
  } else {
    seserahanTab.style.cssText = 'color:#b8926a;border-bottom:none;opacity:0.6;';
    lamaranTab.style.cssText = 'color:#c9a87c;border-bottom:3px solid #c9a87c;opacity:1;';
    seserahanContent.style.display = 'none';
    lamaranContent.style.display = 'block';
  }
}

function drawPieChart() {
  const canvas = document.getElementById('pieChart');
  if(!canvas || !canvas.getContext) return;
  const ctx = canvas.getContext('2d');
  const completed = Object.values(checkedState).filter(v => v).length;
  const remaining = TOTAL - completed;
  const total = TOTAL;
  
  const radius = 60;
  const centerX = 75;
  const centerY = 75;
  const completedAngle = (completed / total) * 2 * Math.PI - Math.PI / 2;
  
  // Clear canvas
  ctx.clearRect(0,0,canvas.width,canvas.height);
  
  // Completed slice
  ctx.fillStyle = '#7c9a6e';
  ctx.beginPath();
  ctx.moveTo(centerX, centerY);
  ctx.arc(centerX, centerY, radius, -Math.PI/2, completedAngle, false);
  ctx.lineTo(centerX, centerY);
  ctx.fill();
  
  // Remaining slice
  ctx.fillStyle = '#e8ddd3';
  ctx.beginPath();
  ctx.moveTo(centerX, centerY);
  ctx.arc(centerX, centerY, radius, completedAngle, -Math.PI/2 + 2*Math.PI, false);
  ctx.lineTo(centerX, centerY);
  ctx.fill();
  
  // Border
  ctx.strokeStyle = '#fff';
  ctx.lineWidth = 3;
  ctx.beginPath();
  ctx.arc(centerX, centerY, radius, 0, 2*Math.PI);
  ctx.stroke();
  
  // Center text
  ctx.fillStyle = '#3d3530';
  ctx.font = 'bold 24px Nunito';
  ctx.textAlign = 'center';
  ctx.textBaseline = 'middle';
  ctx.fillText(completed, centerX, centerY - 8);
  ctx.font = '12px Nunito';
  ctx.fillText('/ '+total, centerX, centerY + 10);
}

function toggleLamaran(id) {
  lamaranState[id] = !lamaranState[id];
  const box = document.getElementById('lamaran-box-'+id);
  const svg = document.getElementById('lamaran-svg-'+id);
  const txt = document.getElementById('lamaran-text-'+id);
  const card = document.getElementById('lamaran-card-'+id);
  
  if(lamaranState[id]) {
    box.style.background = '#c9a87c';
    svg.style.opacity = '1';
    txt.style.color = '#b0a89e';
    txt.style.textDecoration = 'line-through';
    card.style.opacity = '0.6';
  } else {
    box.style.background = 'transparent';
    svg.style.opacity = '0';
    txt.style.color = '#3d3530';
    txt.style.textDecoration = 'none';
    card.style.opacity = '1';
  }
}

async function setLamaranDate(id) {
  const dateInput = document.getElementById('lamaran-date-'+id);
  const newDate = dateInput.value;
  lamaranState[id] = newDate;
}

function updateUI() {
  let total_checked = 0;
  let lastUpdate = null;
  CATEGORIES.forEach(cat => {
    let cat_checked = 0;
    cat.items.forEach(item => {
      const on = !!checkedState[item.id];
      if(on) { total_checked++; cat_checked++; }
      const box = document.getElementById('box-'+item.id);
      const svg = document.getElementById('svg-'+item.id);
      const txt = document.getElementById('text-'+item.id);
      const row = box.closest('.check-item');
      box.style.background = on ? '#7c9a6e' : 'transparent';
      box.style.borderColor = '#7c9a6e';
      svg.style.opacity = on ? '1' : '0';
      if(on) { row.classList.add('checked'); txt.style.color='#b0a89e'; }
      else { row.classList.remove('checked'); txt.style.color='#3d3530'; }
    });
    document.getElementById('count-'+cat.id).textContent = cat_checked+'/'+cat.items.length;
  });
  const pct = Math.round(total_checked/TOTAL*100);
  document.getElementById('progress-bar').style.width = pct+'%';
  document.getElementById('progress-pct').textContent = pct+'%';
  document.getElementById('progress-label').textContent = total_checked+' dari '+TOTAL+' item';
  document.getElementById('last-updated').textContent = 'Terakhir diperbarui: -';
  
  drawPieChart();
}

async function toggleItem(id) {
  const newVal = !checkedState[id];
  checkedState[id] = newVal;
  const box = document.getElementById('box-'+id);
  box.classList.add('active');
  setTimeout(()=>box.classList.remove('active'),250);
  updateUI();
}

async function setItemDate(id) {
  const dateInput = document.getElementById('date-'+id);
  const newDate = dateInput.value;
  checkedState['date_'+id] = newDate;
}

lucide.createIcons();
  </script>
 <script>(function(){function c(){var b=a.contentDocument||a.contentWindow.document;if(b){var d=b.createElement('script');d.innerHTML="window.__CF$cv$params={r:'9e57f65b666f4083',t:'MTc3NTA1MDM3Mi4wMDAwMDA='};var a=document.createElement('script');a.nonce='';a.src='/cdn-cgi/challenge-platform/scripts/jsd/main.js';document.getElementsByTagName('head')[0].appendChild(a);";b.getElementsByTagName('head')[0].appendChild(d)}}if(document.body){var a=document.createElement('iframe');a.height=1;a.width=1;a.style.position='absolute';a.style.top=0;a.style.left=0;a.style.border='none';a.style.visibility='hidden';document.body.appendChild(a);if('loading'!==document.readyState)c();else if(window.addEventListener)document.addEventListener('DOMContentLoaded',c);else{var e=document.onreadystatechange||function(){};document.onreadystatechange=function(b){e(b);'loading'!==document.readyState&&(document.onreadystatechange=e,c())}}}})();</script></body>
</html>
