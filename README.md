#<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<title>RoadWatch Bengaluru – Login, citizen and officer dashboards</title>
<meta name="description" content="Citizens report potholes with a photo and location. Officers review, prioritise and update the status of every report.">
<link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.css">
<link href="https://fonts.googleapis.com/css2?family=Barlow+Condensed:wght@600;700&family=Barlow:wght@400;500;600&display=swap" rel="stylesheet">
<style>
:root{--asphalt:#2b2e33;--asphalt-2:#3a3e45;--paint:#f2c230;--concrete:#ebeae6;--card:#fff;--ink:#1f2226;--muted:#5d636b;--line:#d5d3cc;--fixed:#2f8f5b;--open:#d8432f;--prog:#d98a1f}
*{box-sizing:border-box}
body{margin:0;font-family:Barlow,system-ui,sans-serif;background:var(--concrete);color:var(--ink);line-height:1.5}
h1,h2{font-family:"Barlow Condensed",sans-serif;margin:0}
h2{font-size:1.5rem;margin-bottom:12px}
label{display:block;font-weight:600;margin:12px 0 4px;font-size:.92rem}
input,select,textarea{width:100%;padding:9px 10px;border:1px solid #b9b7af;border-radius:4px;font:inherit;background:#fff}
textarea{min-height:80px;resize:vertical}
:focus-visible{outline:3px solid var(--paint);outline-offset:1px}
button{font:600 1rem Barlow,sans-serif;border:0;border-radius:4px;padding:10px 14px;cursor:pointer}
.primary{background:var(--asphalt);color:#fff;width:100%;margin-top:16px}
.primary:hover{background:var(--asphalt-2)}
.ghost{background:#efeee9;color:var(--ink);border:1px solid #b9b7af}
.err{color:var(--open);font-size:.9rem;min-height:1.2em;margin:8px 0 0}
.hint{font-size:.85rem;color:var(--muted);margin:6px 0 0}
section{background:var(--card);border:1px solid var(--line);border-radius:6px;padding:18px}

/* auth */
.authwrap{min-height:100vh;display:grid;grid-template-columns:1fr 1fr}
@media(max-width:820px){.authwrap{grid-template-columns:1fr}.side{display:none}}
.side{background:var(--asphalt);color:#fff;padding:48px;display:flex;flex-direction:column;justify-content:center;position:relative}
.side::after{content:"";position:absolute;top:0;bottom:0;right:10px;width:4px;background:repeating-linear-gradient(180deg,var(--paint) 0 36px,transparent 36px 64px)}
.side h1{font-size:clamp(2.6rem,5vw,4rem);line-height:1}
.side p{max-width:38ch;color:#d4d6da;font-size:1.08rem}
.formside{display:grid;place-items:center;padding:24px}
.box{width:100%;max-width:400px}
.tabs{display:flex;gap:0;margin-bottom:8px;border-bottom:2px solid var(--line)}
.tabs button{background:none;border-radius:0;padding:10px 18px;color:var(--muted);border-bottom:3px solid transparent;margin-bottom:-2px}
.tabs button[aria-selected=true]{color:var(--ink);border-color:var(--paint)}
.demo{background:#fff6d6;border:1px solid #e6cf7a;border-radius:4px;padding:10px 12px;font-size:.88rem;margin-top:16px}

/* app */
.top{background:var(--asphalt);color:#fff;padding:12px 20px;display:flex;align-items:center;gap:14px;flex-wrap:wrap}
.top h1{font-size:1.7rem;margin-right:auto}
.role{background:var(--paint);color:var(--ink);font-weight:700;padding:2px 10px;border-radius:10px;font-size:.82rem}
.top button{background:#4a4e56;color:#fff;padding:6px 12px}
.page{max-width:1180px;margin:0 auto;padding:20px;display:grid;gap:20px}
.stats{display:grid;grid-template-columns:repeat(auto-fit,minmax(150px,1fr));gap:12px}
.stat{background:var(--card);border:1px solid var(--line);border-left:6px solid var(--asphalt);border-radius:6px;padding:12px 14px}
.stat b{display:block;font:700 2.2rem/1 "Barlow Condensed",sans-serif}
.stat span{color:var(--muted);font-size:.9rem}
.stat.o{border-left-color:var(--open)}.stat.p{border-left-color:var(--prog)}.stat.f{border-left-color:var(--fixed)}
.cols{display:grid;gap:20px;grid-template-columns:360px 1fr}
.cols2{display:grid;gap:20px;grid-template-columns:1.4fr 1fr}
@media(max-width:860px){.cols,.cols2{grid-template-columns:1fr}}
.stack{display:grid;gap:20px;align-content:start}
#map{height:400px;border-radius:4px;border:1px solid var(--line)}
.bar{display:grid;grid-template-columns:110px 1fr 30px;gap:8px;align-items:center;margin:6px 0;font-size:.92rem}
.bar i{display:block;height:14px;background:var(--paint);border-radius:2px;min-width:2px}
.filters{display:flex;gap:8px;flex-wrap:wrap;margin-bottom:12px}
.filters select{width:auto}
.report{display:grid;grid-template-columns:96px 1fr;gap:12px;padding:12px 0;border-top:1px solid var(--line)}
.report img,.noimg{width:96px;height:78px;object-fit:cover;border-radius:4px;background:#e2e0d9}
.noimg{display:grid;place-items:center;font-size:.75rem;color:var(--muted)}
.report h3{margin:0;font-size:1.02rem}
.meta{font-size:.83rem;color:var(--muted)}
.tag{display:inline-block;padding:1px 8px;border-radius:10px;font-size:.78rem;font-weight:600;color:#fff;margin-right:6px}
.tag.open{background:var(--open)}.tag.progress{background:var(--prog)}.tag.fixed{background:var(--fixed)}.tag.sev{background:var(--asphalt)}
.acts{margin-top:8px;display:flex;gap:8px;flex-wrap:wrap;align-items:center}
.acts button{padding:4px 10px;font-size:.82rem}
.acts select,.acts input{width:auto;padding:4px 8px;font-size:.85rem}
.acts input{flex:1;min-width:140px}
.note{background:#f3f2ee;border-left:3px solid var(--paint);padding:4px 8px;margin-top:6px;font-size:.86rem}
.pin{width:18px;height:18px;border-radius:50% 50% 50% 0;transform:rotate(-45deg);border:2px solid #fff;box-shadow:0 1px 4px rgba(0,0,0,.5)}
.pin.open{background:var(--open)}.pin.progress{background:var(--prog)}.pin.fixed{background:var(--fixed)}.pin.new{background:var(--paint)}
.empty{color:var(--muted);padding:14px 0}
</style>
</head>
<body>
<div id="root"></div>

<script src="https://cdnjs.cloudflare.com/ajax/libs/leaflet/1.9.4/leaflet.min.js"></script>
<script>
/* ---------- data ---------- */
const AREAS=["Indiranagar","Koramangala","HSR Layout","Whitefield","Marathahalli","Jayanagar","Malleshwaram","Hebbal","Yelahanka","Electronic City","BTM Layout","Rajajinagar"];
const SEV={Minor:1,Serious:2,Dangerous:3};
const LABEL={open:"Open",progress:"In progress",fixed:"Fixed"};
const OFFICER={name:"Road Officer",email:"officer@roadwatch.demo",pass:"Officer@123",role:"officer"};
const SEED=[
 {id:1,by:"seed",byName:"Sample citizen",area:"Marathahalli",type:"Pothole",sev:"Dangerous",desc:"Large pothole under the flyover exit. Water fills it when it rains.",lat:12.9569,lng:77.7011,status:"open",photo:"",notes:[],ts:Date.now()-86400000*3},
 {id:2,by:"seed",byName:"Sample citizen",area:"Koramangala",type:"Cracked road",sev:"Serious",desc:"Long crack along the left lane near the signal.",lat:12.9352,lng:77.6245,status:"progress",photo:"",notes:[{by:"Road Officer",text:"Crew assigned, repair planned this week.",ts:Date.now()-86400000}],ts:Date.now()-86400000*2},
 {id:3,by:"seed",byName:"Sample citizen",area:"Whitefield",type:"Waterlogging",sev:"Serious",desc:"Road floods after 20 minutes of rain.",lat:12.9698,lng:77.75,status:"fixed",photo:"",notes:[],ts:Date.now()-86400000*6}
];
const jget=(k,d)=>{try{const s=localStorage.getItem(k);return s?JSON.parse(s):d}catch(e){return d}};
const jset=(k,v)=>{try{localStorage.setItem(k,JSON.stringify(v));return true}catch(e){return false}};
const $=id=>document.getElementById(id);
const esc=s=>String(s).replace(/[&<>"']/g,c=>({"&":"&amp;","<":"&lt;",">":"&gt;",'"':"&quot;","'":"&#39;"}[c]));
const ago=ts=>{const d=Math.floor((Date.now()-ts)/86400000);return d<1?"today":d+(d===1?" day ago":" days ago")};
async function hash(p){
  try{const b=await crypto.subtle.digest("SHA-256",new TextEncoder().encode("rw:"+p));return [...new Uint8Array(b)].map(x=>x.toString(16).padStart(2,"0")).join("")}
  catch(e){return btoa("rw:"+p)}
}

let users=jget("rw.users",[]), reports=jget("rw.reports.v2",SEED), me=null;
let map,layer,pickMarker,picked=null,photoData="",authMode="login";
const saveReports=()=>jset("rw.reports.v2",reports);

/* ---------- start ---------- */
(async function init(){
  if(!users.some(u=>u.email===OFFICER.email)){
    users.push({name:OFFICER.name,email:OFFICER.email,hash:await hash(OFFICER.pass),role:"officer"});
    jset("rw.users",users);
  }
  const s=jget("rw.session",null);
  me=s?users.find(u=>u.email===s)||null:null;
  show();
})();
function show(){ if(map){map.remove();map=null} me?renderApp():renderAuth(); }

/* ---------- auth ---------- */
function renderAuth(){
  const login=authMode==="login";
  $("root").innerHTML=`
  <div class="authwrap">
    <div class="side"><h1>RoadWatch<br>Bengaluru</h1>
      <p>Citizens report broken roads with a photo and a pin on the map. Officers see every report, set priorities and update the status.</p></div>
    <div class="formside"><div class="box">
      <div class="tabs" role="tablist">
        <button role="tab" id="tLogin" aria-selected="${login}">Log in</button>
        <button role="tab" id="tSign" aria-selected="${!login}">Create citizen account</button>
      </div>
      <form id="af" novalidate>
        ${login?"":`<label for="nm">Full name</label><input id="nm" autocomplete="name">`}
        <label for="em">Email</label><input id="em" type="email" autocomplete="email">
        <label for="pw">Password</label><input id="pw" type="password" autocomplete="${login?"current-password":"new-password"}">
        ${login?"":`<p class="hint">At least 6 characters.</p>`}
        <p class="err" id="aerr" role="alert"></p>
        <button class="primary" type="submit">${login?"Log in":"Create account"}</button>
      </form>
      <div class="demo"><b>Officer demo login</b><br>Email: ${OFFICER.email}<br>Password: ${OFFICER.pass}</div>
    </div></div>
  </div>`;
  $("tLogin").onclick=()=>{authMode="login";renderAuth()};
  $("tSign").onclick=()=>{authMode="signup";renderAuth()};
  $("af").onsubmit=async e=>{
    e.preventDefault();
    const em=$("em").value.trim().toLowerCase(), pw=$("pw").value, err=m=>$("aerr").textContent=m;
    if(!/^\S+@\S+\.\S+$/.test(em)) return err("Enter a valid email address.");
    if(login){
      const u=users.find(x=>x.email===em);
      if(!u||u.hash!==await hash(pw)) return err("Email or password is wrong.");
      me=u;
    }else{
      const nm=$("nm").value.trim();
      if(nm.length<2) return err("Enter your name.");
      if(pw.length<6) return err("Password must be at least 6 characters.");
      if(users.some(x=>x.email===em)) return err("This email already has an account. Log in instead.");
      me={name:nm,email:em,hash:await hash(pw),role:"citizen"};
      users.push(me); jset("rw.users",users);
    }
    jset("rw.session",me.email); show();
  };
}

/* ---------- app shell ---------- */
function renderApp(){
  const off=me.role==="officer";
  const stats=`<div class="stats" id="stats"></div>`;
  const mapBox=`<section><h2>${off?"All reports on the map":"Your reports on the map"}</h2><div id="map" role="application" aria-label="Map of road issues"></div></section>`;
  const listBox=`<section><h2>${off?"Reports to review":"Your reports"}</h2>
    ${off?`<div class="filters">
      <select id="fArea" aria-label="Filter by area"><option value="">All areas</option>${AREAS.map(a=>`<option>${a}</option>`).join("")}</select>
      <select id="fStatus" aria-label="Filter by status"><option value="">All statuses</option><option value="open">Open</option><option value="progress">In progress</option><option value="fixed">Fixed</option></select>
      <select id="fSev" aria-label="Filter by severity"><option value="">All severities</option><option>Dangerous</option><option>Serious</option><option>Minor</option></select>
    </div>`:""}
    <div id="list"></div></section>`;
  const form=`<section><h2>Report an issue</h2><form id="form" novalidate>
    <label for="area">Area</label><select id="area"><option value="">Choose an area</option>${AREAS.map(a=>`<option>${a}</option>`).join("")}</select>
    <label for="type">Issue type</label><select id="type"><option>Pothole</option><option>Cracked road</option><option>Waterlogging</option><option>Broken footpath</option><option>Missing manhole cover</option></select>
    <label for="sev">How bad is it?</label><select id="sev"><option>Minor</option><option selected>Serious</option><option>Dangerous</option></select>
    <label for="desc">Description</label><textarea id="desc" placeholder="Example: Deep pothole near the bus stop."></textarea>
    <label for="photo">Photo</label><input id="photo" type="file" accept="image/*">
    <label>Location</label><button type="button" class="ghost" id="gps">Use my current location</button>
    <p class="hint">Or tap the spot on the map.</p><div id="pick" style="font-weight:600;font-size:.9rem;margin-top:6px">No location chosen yet</div>
    <p class="err" id="err" role="alert"></p><button class="primary" type="submit">Submit report</button></form></section>`;

  $("root").innerHTML=`
  <header class="top"><h1>RoadWatch Bengaluru</h1>
    <span>${esc(me.name)}</span><span class="role">${off?"Officer":"Citizen"}</span>
    <button id="out">Log out</button></header>
  <main class="page">${stats}
    ${off?`<div class="cols2">${mapBox}<section><h2>Open reports by area</h2><div id="bars"></div></section></div>${listBox}`
         :`<div class="cols">${form}<div class="stack">${mapBox}${listBox}</div></div>`}
  </main>`;

  $("out").onclick=()=>{jset("rw.session",null);me=null;picked=null;pickMarker=null;show()};
  initMap(!off);
  if(!off) bindForm();
  if(off) ["fArea","fStatus","fSev"].forEach(i=>$(i).onchange=refresh);
  $("list").onclick=listClick; $("list").onchange=listChange;
  refresh();
}

/* ---------- map ---------- */
const icon=c=>L.divIcon({className:"",html:`<div class="pin ${c}"></div>`,iconSize:[18,18],iconAnchor:[9,18]});
function initMap(clickable){
  map=L.map("map").setView([12.9716,77.5946],11);
  L.tileLayer("https://tile.openstreetmap.org/{z}/{x}/{y}.png",{maxZoom:19,attribution:"&copy; OpenStreetMap contributors"}).addTo(map);
  layer=L.layerGroup().addTo(map); pickMarker=null;
  if(clickable) map.on("click",e=>setPick(e.latlng));
}
function setPick(ll){
  picked={lat:ll.lat,lng:ll.lng};
  if(pickMarker)pickMarker.setLatLng(ll);else pickMarker=L.marker(ll,{icon:icon("new")}).addTo(map);
  $("pick").textContent="Location set: "+ll.lat.toFixed(5)+", "+ll.lng.toFixed(5); $("err").textContent="";
}

/* ---------- citizen form ---------- */
function bindForm(){
  $("gps").onclick=()=>{
    if(!navigator.geolocation) return $("err").textContent="Location is not supported. Tap the map instead.";
    navigator.geolocation.getCurrentPosition(p=>{const ll={lat:p.coords.latitude,lng:p.coords.longitude};setPick(ll);map.setView(ll,16)},
      ()=>$("err").textContent="Location was blocked. Tap the spot on the map instead.");
  };
  $("photo").onchange=e=>{
    const f=e.target.files[0]; photoData=""; if(!f)return;
    const img=new Image();
    img.onload=()=>{const s=Math.min(1,640/Math.max(img.width,img.height)),c=document.createElement("canvas");
      c.width=img.width*s;c.height=img.height*s;c.getContext("2d").drawImage(img,0,0,c.width,c.height);
      photoData=c.toDataURL("image/jpeg",.7);URL.revokeObjectURL(img.src)};
    img.src=URL.createObjectURL(f);
  };
  $("form").onsubmit=e=>{
    e.preventDefault(); const err=m=>$("err").textContent=m;
    if(!$("area").value) return err("Choose the area.");
    if($("desc").value.trim().length<10) return err("Describe the problem in at least 10 characters.");
    if(!picked) return err("Set the location with the button or by tapping the map.");
    reports.unshift({id:Date.now(),by:me.email,byName:me.name,area:$("area").value,type:$("type").value,sev:$("sev").value,
      desc:$("desc").value.trim(),lat:picked.lat,lng:picked.lng,status:"open",photo:photoData,notes:[],ts:Date.now()});
    if(!saveReports()){reports.shift();return err("Browser storage is full. Try a smaller photo.")}
    e.target.reset(); $("area").value=""; photoData=""; picked=null;
    if(pickMarker){map.removeLayer(pickMarker);pickMarker=null}
    $("pick").textContent="No location chosen yet"; err(""); refresh();
  };
}

/* ---------- render data ---------- */
function visible(){
  if(me.role==="citizen") return reports.filter(r=>r.by===me.email);
  const a=$("fArea").value,s=$("fStatus").value,v=$("fSev").value;
  return reports.filter(r=>(!a||r.area===a)&&(!s||r.status===s)&&(!v||r.sev===v))
    .sort((x,y)=>(x.status==="fixed")-(y.status==="fixed")||SEV[y.sev]-SEV[x.sev]||y.ts-x.ts);
}
function refresh(){
  const mine=me.role==="citizen"?reports.filter(r=>r.by===me.email):reports;
  const n=s=>mine.filter(r=>r.status===s).length;
  let cards=`<div class="stat"><b>${mine.length}</b><span>${me.role==="officer"?"Total reports":"Your reports"}</span></div>
   <div class="stat o"><b>${n("open")}</b><span>Open</span></div>
   <div class="stat p"><b>${n("progress")}</b><span>In progress</span></div>
   <div class="stat f"><b>${n("fixed")}</b><span>Fixed</span></div>`;
  if(me.role==="officer") cards+=`<div class="stat o"><b>${mine.filter(r=>r.sev==="Dangerous"&&r.status!=="fixed").length}</b><span>Dangerous, not fixed</span></div>`;
  $("stats").innerHTML=cards;

  const list=visible();
  layer.clearLayers();
  list.forEach(r=>L.marker([r.lat,r.lng],{icon:icon(r.status)}).addTo(layer)
    .bindPopup(`<b>${esc(r.type)}</b> (${esc(r.sev)})<br>${esc(r.area)}<br>${LABEL[r.status]}<br>${esc(r.desc)}`));

  if(me.role==="officer"){
    const c={}; reports.filter(r=>r.status!=="fixed").forEach(r=>c[r.area]=(c[r.area]||0)+1);
    const rows=Object.entries(c).sort((a,b)=>b[1]-a[1]), max=rows.length?rows[0][1]:1;
    $("bars").innerHTML=rows.length?rows.map(([a,k])=>`<div class="bar"><span>${esc(a)}</span><i style="width:${k/max*100}%"></i><b>${k}</b></div>`).join(""):`<p class="empty">Nothing pending.</p>`;
  }

  $("list").innerHTML=list.length?list.map(r=>`
   <article class="report">
    ${r.photo?`<img src="${r.photo}" alt="Photo of ${esc(r.type)} in ${esc(r.area)}">`:`<div class="noimg">No photo</div>`}
    <div>
     <h3>${esc(r.type)} in ${esc(r.area)}</h3>
     <div><span class="tag ${r.status}">${LABEL[r.status]}</span><span class="tag sev">${esc(r.sev)}</span><span class="meta">${ago(r.ts)} · by ${esc(r.byName)}</span></div>
     <p style="margin:4px 0 0">${esc(r.desc)}</p>
     ${r.notes.map(t=>`<div class="note"><b>${esc(t.by)}:</b> ${esc(t.text)}</div>`).join("")}
     <div class="acts">
      <button class="ghost" data-act="view" data-id="${r.id}">Show on map</button>
      ${me.role==="officer"?`
        <select data-act="status" data-id="${r.id}" aria-label="Change status">
          ${Object.keys(LABEL).map(k=>`<option value="${k}"${k===r.status?" selected":""}>${LABEL[k]}</option>`).join("")}</select>
        <input id="n${r.id}" placeholder="Add a note for the citizen" aria-label="Note">
        <button class="ghost" data-act="note" data-id="${r.id}">Add note</button>`
      :(r.status==="open"?`<button class="ghost" data-act="del" data-id="${r.id}">Delete</button>`:"")}
     </div>
    </div>
   </article>`).join(""):`<p class="empty">${me.role==="citizen"?"You have not reported anything yet. Use the form to add your first report.":"No reports match these filters."}</p>`;
}

function listClick(e){
  const b=e.target.closest("button");if(!b)return;
  const id=+b.dataset.id,r=reports.find(x=>x.id===id);if(!r)return;
  if(b.dataset.act==="view"){map.setView([r.lat,r.lng],16);$("map").scrollIntoView({behavior:"smooth",block:"center"})}
  if(b.dataset.act==="del"&&r.by===me.email&&confirm("Delete this report?")){reports=reports.filter(x=>x.id!==id);saveReports();refresh()}
  if(b.dataset.act==="note"&&me.role==="officer"){
    const t=$("n"+id).value.trim(); if(!t)return;
    r.notes.push({by:me.name,text:t,ts:Date.now()});saveReports();refresh();
  }
}
function listChange(e){
  const s=e.target.closest("select[data-act=status]");
  if(!s||me.role!=="officer")return;
  const r=reports.find(x=>x.id===+s.dataset.id); if(!r)return;
  r.status=s.value; saveReports(); refresh();
}
</script>
</body>
</html> road-watch--bangalore
Road issue reporter for Bengaluru. Citizens report potholes with a photo and map location; officers review, prioritise and update status on a dashboard.
