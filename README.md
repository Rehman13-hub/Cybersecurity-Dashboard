# Cybersecurity-Dashboard

<!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>OCULR — Security Operations Console</title>
<link rel="preconnect" href="https://fonts.googleapis.com">
<link href="https://fonts.googleapis.com/css2?family=Space+Grotesk:wght@400;500;600;700&family=Inter:wght@400;500;600;700;800&family=JetBrains+Mono:wght@400;500;600&display=swap" rel="stylesheet">
<script src="https://cdnjs.cloudflare.com/ajax/libs/Chart.js/4.4.0/chart.umd.min.js"></script>
<style>
/* ============================= TOKENS ============================= */
:root{
  --bg:#0A0E16;
  --bg-elevated:#0F1420;
  --surface:#131A28;
  --surface-2:#19212F;
  --surface-hover:#1D2635;
  --border:#212B3D;
  --border-strong:#2C3850;
  --text:#E8ECF6;
  --text-muted:#8B96AC;
  --text-faint:#5A6478;
  --accent:#33D6C3;
  --accent-dim:#25a89a;
  --accent-soft:rgba(51,214,195,0.12);
  --accent-soft-strong:rgba(51,214,195,0.22);
  --violet:#8E7CF6;
  --violet-soft:rgba(142,124,246,0.14);
  --critical:#FF5E7E;
  --critical-soft:rgba(255,94,126,0.13);
  --high:#FF9E4A;
  --high-soft:rgba(255,158,74,0.13);
  --medium:#F2CF52;
  --medium-soft:rgba(242,207,82,0.13);
  --low:#5AD98A;
  --low-soft:rgba(90,217,138,0.13);
  --radius-sm:8px;
  --radius-md:14px;
  --radius-lg:20px;
  --radius-xl:28px;
  --font-display:'Space Grotesk', sans-serif;
  --font-body:'Inter', sans-serif;
  --font-mono:'JetBrains Mono', monospace;
  --shadow-card: 0 1px 0 rgba(255,255,255,0.03) inset, 0 12px 24px -12px rgba(0,0,0,0.55);
  --sidebar-w: 248px;
}
[data-theme="light"]{
  --bg:#EEF1F7;
  --bg-elevated:#FFFFFF;
  --surface:#FFFFFF;
  --surface-2:#F5F7FB;
  --surface-hover:#EBEFF6;
  --border:#DEE3ED;
  --border-strong:#C8D0E0;
  --text:#121826;
  --text-muted:#5B6577;
  --text-faint:#8A93A6;
  --accent:#0EA895;
  --accent-dim:#0b8779;
  --accent-soft:rgba(14,168,149,0.10);
  --accent-soft-strong:rgba(14,168,149,0.18);
  --shadow-card: 0 1px 0 rgba(255,255,255,0.6) inset, 0 10px 20px -14px rgba(30,40,70,0.25);
}
*{box-sizing:border-box; margin:0; padding:0;}
html,body{height:100%;}
body{
  background:var(--bg);
  color:var(--text);
  font-family:var(--font-body);
  font-size:14px;
  line-height:1.5;
  -webkit-font-smoothing:antialiased;
  overflow-x:hidden;
  transition:background .3s ease, color .3s ease;
}
::selection{background:var(--accent-soft-strong); color:var(--text);}
::-webkit-scrollbar{width:9px; height:9px;}
::-webkit-scrollbar-track{background:transparent;}
::-webkit-scrollbar-thumb{background:var(--border-strong); border-radius:8px;}
a{color:inherit; text-decoration:none;}
button{font-family:inherit; cursor:pointer;}
input,select,textarea{font-family:inherit;}
.hidden{display:none !important;}
.mono{font-family:var(--font-mono);}
:focus-visible{outline:2px solid var(--accent); outline-offset:2px; border-radius:4px;}

/* ============================= ICONS ============================= */
.icon{width:18px; height:18px; flex-shrink:0; stroke:currentColor; fill:none; stroke-width:1.8; stroke-linecap:round; stroke-linejoin:round;}

/* ============================= LOGIN SCREEN ============================= */
#login-screen{
  min-height:100vh;
  display:grid;
  grid-template-columns: 1.15fr 1fr;
  background:var(--bg);
}
.login-hero{
  position:relative;
  background:
    radial-gradient(ellipse 80% 60% at 30% 20%, rgba(51,214,195,0.14), transparent 60%),
    radial-gradient(ellipse 60% 50% at 80% 80%, rgba(142,124,246,0.10), transparent 60%),
    linear-gradient(180deg, #080B12 0%, #0A0E16 100%);
  display:flex;
  flex-direction:column;
  justify-content:space-between;
  padding:56px 64px;
  overflow:hidden;
  border-right:1px solid var(--border);
}
.login-hero::before{
  content:'';
  position:absolute; inset:0;
  background-image:
    linear-gradient(rgba(255,255,255,0.025) 1px, transparent 1px),
    linear-gradient(90deg, rgba(255,255,255,0.025) 1px, transparent 1px);
  background-size:42px 42px;
  mask-image: radial-gradient(ellipse 70% 70% at 50% 40%, black 20%, transparent 75%);
  pointer-events:none;
}
.brand-mark{display:flex; align-items:center; gap:11px; position:relative; z-index:2;}
.brand-mark .mark{
  width:34px; height:34px; border-radius:10px;
  background:linear-gradient(135deg, var(--accent), #1a9c8f);
  display:flex; align-items:center; justify-content:center;
  box-shadow:0 6px 18px -4px rgba(51,214,195,0.5);
}
.brand-mark .mark svg{width:19px;height:19px;stroke:#06120f;stroke-width:2.1;}
.brand-mark span{font-family:var(--font-display); font-weight:600; font-size:19px; letter-spacing:0.3px; color:#F2F5FA;}

.radar-wrap{position:relative; z-index:2; display:flex; flex-direction:column; align-items:center; justify-content:center; flex:1; padding:20px 0;}
.radar{position:relative; width:340px; height:340px;}
.radar svg{width:100%; height:100%;}
.radar-ring{fill:none; stroke:rgba(51,214,195,0.22); stroke-width:1;}
.radar-sweep-group{transform-origin:170px 170px; animation:spin 4.5s linear infinite;}
@keyframes spin{ to{ transform:rotate(360deg); } }
.radar-blip{fill:var(--accent); filter:drop-shadow(0 0 6px rgba(51,214,195,0.9));}
.radar-blip.b2{fill:#FF9E4A; filter:drop-shadow(0 0 6px rgba(255,158,74,0.9));}
.radar-blip.b3{fill:#FF5E7E; filter:drop-shadow(0 0 6px rgba(255,94,126,0.9));}
.radar-center{fill:var(--accent);}
.radar-caption{margin-top:26px; text-align:center;}
.radar-caption .big{font-family:var(--font-display); font-size:15px; color:#F2F5FA; font-weight:500;}
.radar-caption .small{font-family:var(--font-mono); font-size:12px; color:var(--text-faint); margin-top:6px;}

.hero-copy{position:relative; z-index:2; max-width:420px;}
.hero-copy h1{font-family:var(--font-display); font-size:27px; font-weight:600; line-height:1.32; color:#F2F5FA; letter-spacing:-0.2px;}
.hero-copy p{margin-top:12px; color:var(--text-muted); font-size:14.5px; line-height:1.6; max-width:380px;}
.hero-stats{display:flex; gap:28px; margin-top:26px; position:relative; z-index:2;}
.hero-stats div b{display:block; font-family:var(--font-mono); font-size:19px; color:var(--accent);}
.hero-stats div span{font-size:12px; color:var(--text-faint);}

.login-panel{
  display:flex; align-items:center; justify-content:center;
  padding:40px; background:var(--bg-elevated);
}
.login-box{width:100%; max-width:378px;}
.login-box .eyebrow{color:var(--accent); font-size:13px; font-weight:600; font-family:var(--font-mono);}
.login-box h2{font-family:var(--font-display); font-size:26px; font-weight:600; margin-top:10px; color:var(--text);}
.login-box .sub{color:var(--text-muted); font-size:13.5px; margin-top:8px; margin-bottom:30px;}

.field{margin-bottom:16px;}
.field label{display:block; font-size:12.5px; color:var(--text-muted); margin-bottom:7px; font-weight:500;}
.input-wrap{position:relative; display:flex; align-items:center;}
.input-wrap .icon{position:absolute; left:13px; color:var(--text-faint);}
.field input[type="text"], .field input[type="email"], .field input[type="password"]{
  width:100%; padding:12px 14px 12px 40px;
  background:var(--surface-2); border:1px solid var(--border);
  border-radius:var(--radius-sm); color:var(--text); font-size:13.5px;
  transition:border-color .15s ease, background .15s ease;
}
.field input:focus{border-color:var(--accent); background:var(--surface);}
.input-wrap .toggle-eye{position:absolute; right:12px; color:var(--text-faint); background:none; border:none; display:flex;}
.input-wrap .toggle-eye:hover{color:var(--text-muted);}

.row-between{display:flex; align-items:center; justify-content:space-between; margin:18px 0 22px;}
.checkbox-line{display:flex; align-items:center; gap:8px; color:var(--text-muted); font-size:13px;}
.checkbox-line input{width:15px; height:15px; accent-color:var(--accent);}
.link-muted{color:var(--accent); font-size:13px; font-weight:500;}
.link-muted:hover{text-decoration:underline;}

.btn{
  display:inline-flex; align-items:center; justify-content:center; gap:8px;
  border:none; border-radius:var(--radius-sm); font-weight:600; font-size:13.5px;
  padding:12px 18px; transition:transform .1s ease, filter .15s ease, background .15s ease;
}
.btn:active{transform:scale(0.98);}
.btn-primary{background:linear-gradient(135deg, var(--accent), var(--accent-dim)); color:#052420; width:100%;}
.btn-primary:hover{filter:brightness(1.08);}
.btn-ghost{background:var(--surface-2); color:var(--text); border:1px solid var(--border);}
.btn-ghost:hover{background:var(--surface-hover);}
.btn-outline{background:transparent; border:1px solid var(--border-strong); color:var(--text-muted);}
.btn-outline:hover{border-color:var(--accent); color:var(--accent);}
.btn-sm{padding:8px 13px; font-size:12.5px; border-radius:8px;}
.btn-danger-o{background:transparent; border:1px solid rgba(255,94,126,0.4); color:var(--critical);}
.btn-danger-o:hover{background:var(--critical-soft);}

.divider-or{display:flex; align-items:center; gap:12px; margin:22px 0; color:var(--text-faint); font-size:12px;}
.divider-or::before, .divider-or::after{content:''; flex:1; height:1px; background:var(--border);}

.twofa-box{display:flex; gap:9px; margin:20px 0;}
.twofa-box input{
  width:44px; height:52px; text-align:center; font-size:19px; font-family:var(--font-mono);
  background:var(--surface-2); border:1px solid var(--border); border-radius:10px; color:var(--text);
}
.twofa-box input:focus{border-color:var(--accent);}

/* ============================= APP SHELL ============================= */
#app{display:flex; min-height:100vh;}
.sidebar{
  width:var(--sidebar-w); flex-shrink:0; background:var(--bg-elevated); border-right:1px solid var(--border);
  display:flex; flex-direction:column; position:fixed; top:0; bottom:0; left:0; z-index:40;
  transition:transform .25s ease;
}
.sidebar-head{padding:22px 22px 18px; display:flex; align-items:center; gap:10px; border-bottom:1px solid var(--border);}
.sidebar-head .mark{width:30px;height:30px;border-radius:9px;background:linear-gradient(135deg, var(--accent), #1a9c8f); display:flex;align-items:center;justify-content:center; flex-shrink:0;}
.sidebar-head .mark svg{width:17px;height:17px;stroke:#06120f;stroke-width:2.1;}
.sidebar-head span{font-family:var(--font-display); font-weight:600; font-size:16.5px;}
.nav-group{padding:16px 12px;}
.nav-label{font-size:11px; text-transform:none; color:var(--text-faint); padding:6px 10px 8px; letter-spacing:0.2px;}
.nav-item{
  display:flex; align-items:center; gap:11px; padding:9px 12px; border-radius:10px; color:var(--text-muted);
  font-size:13.5px; font-weight:500; margin-bottom:2px; position:relative; transition:background .12s ease, color .12s ease;
}
.nav-item .icon{width:17px; height:17px;}
.nav-item:hover{background:var(--surface-2); color:var(--text);}
.nav-item.active{background:var(--accent-soft); color:var(--accent);}
.nav-item .count{margin-left:auto; font-family:var(--font-mono); font-size:10.5px; background:var(--critical-soft); color:var(--critical); padding:2px 6px; border-radius:20px;}
.sidebar-foot{margin-top:auto; padding:16px; border-top:1px solid var(--border);}
.mini-profile{display:flex; align-items:center; gap:10px; padding:9px; border-radius:12px; cursor:pointer;}
.mini-profile:hover{background:var(--surface-2);}
.avatar{width:32px;height:32px;border-radius:9px; background:linear-gradient(135deg,#8E7CF6,#5847c9); display:flex;align-items:center;justify-content:center; font-family:var(--font-display); font-size:12.5px; font-weight:600; color:#fff; flex-shrink:0;}
.mini-profile .name{font-size:12.5px; font-weight:600; color:var(--text);}
.mini-profile .role{font-size:11px; color:var(--text-faint);}

.main{flex:1; margin-left:var(--sidebar-w); display:flex; flex-direction:column; min-width:0;}
.topbar{
  height:64px; border-bottom:1px solid var(--border); display:flex; align-items:center; gap:16px;
  padding:0 26px; position:sticky; top:0; background:rgba(10,14,22,0.86); backdrop-filter:blur(10px); z-index:30;
}
[data-theme="light"] .topbar{background:rgba(255,255,255,0.86);}
.menu-btn{display:none; background:none; border:none; color:var(--text-muted);}
.topbar h1{font-family:var(--font-display); font-size:18px; font-weight:600;}
.topbar .subtitle{font-size:12px; color:var(--text-faint); margin-top:1px;}
.search-box{
  margin-left:auto; display:flex; align-items:center; gap:8px; background:var(--surface-2);
  border:1px solid var(--border); border-radius:10px; padding:8px 12px; width:260px; color:var(--text-faint);
}
.search-box input{background:none; border:none; color:var(--text); font-size:13px; width:100%;}
.search-box input:focus{outline:none;}
.icon-btn{
  width:36px; height:36px; border-radius:10px; background:var(--surface-2); border:1px solid var(--border);
  display:flex; align-items:center; justify-content:center; color:var(--text-muted); position:relative; flex-shrink:0;
}
.icon-btn:hover{color:var(--text); border-color:var(--border-strong);}
.icon-btn .dot{position:absolute; top:6px; right:7px; width:6px; height:6px; border-radius:50%; background:var(--critical); border:1.5px solid var(--bg-elevated);}
.topbar-right{display:flex; align-items:center; gap:10px;}

.content{padding:26px; flex:1;}
.view{display:none;}
.view.active{display:block; animation:fadein .25s ease;}
@keyframes fadein{ from{opacity:0; transform:translateY(4px);} to{opacity:1; transform:translateY(0);} }

.page-head{display:flex; align-items:flex-end; justify-content:space-between; margin-bottom:22px; flex-wrap:wrap; gap:14px;}
.page-head h2{font-family:var(--font-display); font-size:22px; font-weight:600;}
.page-head p{color:var(--text-muted); font-size:13px; margin-top:5px;}
.page-actions{display:flex; gap:10px;}

/* ============================= GRID / CARDS ============================= */
.grid{display:grid; gap:18px;}
.grid-4{grid-template-columns:repeat(4,1fr);}
.grid-3{grid-template-columns:2fr 1fr;}
.grid-2{grid-template-columns:1.3fr 1fr;}
.grid-widgets{grid-template-columns:repeat(3,1fr);}

.card{
  background:var(--surface); border:1px solid var(--border); border-radius:var(--radius-md);
  padding:20px; box-shadow:var(--shadow-card);
}
.card-lg{border-radius:var(--radius-lg);}
.card-head{display:flex; align-items:center; justify-content:between; justify-content:space-between; margin-bottom:16px;}
.card-head h3{font-size:14px; font-weight:600;}
.card-head .muted{font-size:11.5px; color:var(--text-faint); font-family:var(--font-mono);}

.kpi-card{display:flex; flex-direction:column; gap:10px;}
.kpi-top{display:flex; align-items:center; justify-content:space-between;}
.kpi-icon{width:38px; height:38px; border-radius:11px; display:flex; align-items:center; justify-content:center;}
.kpi-icon svg{width:19px; height:19px;}
.kpi-icon.teal{background:var(--accent-soft); color:var(--accent);}
.kpi-icon.red{background:var(--critical-soft); color:var(--critical);}
.kpi-icon.green{background:var(--low-soft); color:var(--low);}
.kpi-icon.violet{background:var(--violet-soft); color:var(--violet);}
.kpi-trend{font-size:11.5px; font-family:var(--font-mono); display:flex; align-items:center; gap:3px; padding:3px 7px; border-radius:20px;}
.kpi-trend.up-bad{color:var(--critical); background:var(--critical-soft);}
.kpi-trend.up-good{color:var(--low); background:var(--low-soft);}
.kpi-trend.down-good{color:var(--low); background:var(--low-soft);}
.kpi-trend svg{width:12px; height:12px;}
.kpi-value{font-family:var(--font-display); font-size:29px; font-weight:600;}
.kpi-label{color:var(--text-muted); font-size:12.5px;}

.badge{display:inline-flex; align-items:center; gap:5px; font-size:11px; font-weight:600; padding:4px 9px; border-radius:20px; font-family:var(--font-mono);}
.badge::before{content:''; width:6px; height:6px; border-radius:50%; background:currentColor;}
.badge.critical{background:var(--critical-soft); color:var(--critical);}
.badge.high{background:var(--high-soft); color:var(--high);}
.badge.medium{background:var(--medium-soft); color:var(--medium);}
.badge.low{background:var(--low-soft); color:var(--low);}
.badge.info{background:var(--accent-soft); color:var(--accent);}
.badge.neutral{background:var(--surface-2); color:var(--text-muted); border:1px solid var(--border);}

.status-pill{display:inline-flex; align-items:center; gap:6px; font-size:12px; font-weight:500;}
.status-pill .dot{width:7px; height:7px; border-radius:50%;}
.status-pill.online .dot{background:var(--low); box-shadow:0 0 0 3px var(--low-soft);}
.status-pill.offline .dot{background:var(--text-faint);}
.status-pill.warn .dot{background:var(--high); box-shadow:0 0 0 3px var(--high-soft);}

.progress-track{height:6px; border-radius:20px; background:var(--surface-2); overflow:hidden; border:1px solid var(--border);}
.progress-fill{height:100%; border-radius:20px;}

/* Score ring */
.score-ring-wrap{position:relative; width:132px; height:132px; margin:0 auto;}
.score-ring-wrap svg{transform:rotate(-90deg);}
.score-ring-bg{fill:none; stroke:var(--surface-2); stroke-width:10;}
.score-ring-fg{fill:none; stroke:var(--accent); stroke-width:10; stroke-linecap:round; transition:stroke-dashoffset 1s ease;}
.score-ring-label{position:absolute; inset:0; display:flex; flex-direction:column; align-items:center; justify-content:center;}
.score-ring-label b{font-family:var(--font-display); font-size:27px;}
.score-ring-label span{font-size:11px; color:var(--text-faint);}

/* Tables */
.table-wrap{overflow-x:auto;}
table{width:100%; border-collapse:collapse;}
thead th{text-align:left; font-size:11.5px; color:var(--text-faint); font-weight:600; padding:0 14px 10px; border-bottom:1px solid var(--border); white-space:nowrap;}
tbody td{padding:13px 14px; border-bottom:1px solid var(--border); font-size:13px; vertical-align:middle; white-space:nowrap;}
tbody tr{transition:background .12s ease; cursor:pointer;}
tbody tr:hover{background:var(--surface-2);}
tbody tr:last-child td{border-bottom:none;}
.cell-main{display:flex; align-items:center; gap:10px;}
.cell-icon{width:30px; height:30px; border-radius:9px; background:var(--surface-2); display:flex; align-items:center; justify-content:center; color:var(--text-muted); flex-shrink:0;}
.cell-icon svg{width:15px;height:15px;}
.cell-title{font-weight:600; color:var(--text);}
.cell-sub{font-size:11.5px; color:var(--text-faint); font-family:var(--font-mono);}

.filter-bar{display:flex; align-items:center; gap:10px; margin-bottom:18px; flex-wrap:wrap;}
.select-chip{
  display:flex; align-items:center; gap:7px; background:var(--surface-2); border:1px solid var(--border);
  border-radius:9px; padding:8px 11px; font-size:12.5px; color:var(--text-muted);
}
.select-chip select{background:none; border:none; color:var(--text); font-size:12.5px;}
.select-chip select:focus{outline:none;}
.chip-filter{
  display:flex; align-items:center; gap:6px; padding:7px 12px; border-radius:20px; border:1px solid var(--border);
  font-size:12px; color:var(--text-muted); background:var(--surface-2);
}
.chip-filter.active{border-color:var(--accent); color:var(--accent); background:var(--accent-soft);}
.chip-filter .dot{width:7px;height:7px;border-radius:50%;}

/* Widgets misc */
.mini-row{display:flex; align-items:center; justify-content:space-between; padding:10px 0; border-bottom:1px solid var(--border);}
.mini-row:last-child{border-bottom:none;}
.mini-left{display:flex; align-items:center; gap:10px;}
.mini-icon{width:32px;height:32px;border-radius:9px;display:flex;align-items:center;justify-content:center;}
.mini-icon svg{width:15px;height:15px;}

.legend-row{display:flex; align-items:center; gap:8px; font-size:12px; color:var(--text-muted);}
.legend-dot{width:8px;height:8px;border-radius:50%;}

/* Timeline (incident details) */
.timeline{position:relative; padding-left:26px;}
.timeline::before{content:''; position:absolute; left:8px; top:4px; bottom:4px; width:1px; background:var(--border);}
.timeline-item{position:relative; padding-bottom:22px;}
.timeline-item:last-child{padding-bottom:0;}
.timeline-dot{position:absolute; left:-26px; top:2px; width:17px; height:17px; border-radius:50%; background:var(--surface); border:2px solid var(--accent); display:flex; align-items:center; justify-content:center;}
.timeline-dot.done{background:var(--accent); border-color:var(--accent);}
.timeline-time{font-family:var(--font-mono); font-size:11px; color:var(--text-faint); margin-bottom:3px;}
.timeline-title{font-weight:600; font-size:13.5px;}
.timeline-desc{font-size:12.5px; color:var(--text-muted); margin-top:3px;}

/* Modal */
.modal-overlay{position:fixed; inset:0; background:rgba(6,9,15,0.62); backdrop-filter:blur(3px); display:flex; align-items:center; justify-content:center; z-index:100;}
.modal-box{width:100%; max-width:440px; background:var(--surface); border:1px solid var(--border); border-radius:var(--radius-lg); padding:26px; box-shadow:0 30px 60px -20px rgba(0,0,0,0.6);}
.modal-head{display:flex; align-items:center; justify-content:space-between; margin-bottom:6px;}
.modal-head h3{font-family:var(--font-display); font-size:17px; font-weight:600;}
.modal-close{background:none; border:none; color:var(--text-faint); width:28px; height:28px; display:flex; align-items:center; justify-content:center; border-radius:8px;}
.modal-close:hover{background:var(--surface-2); color:var(--text);}

/* Toast */
#toast{
  position:fixed; bottom:24px; left:50%; transform:translateX(-50%) translateY(20px); opacity:0;
  background:var(--surface); border:1px solid var(--border-strong); color:var(--text); padding:12px 18px;
  border-radius:12px; font-size:13px; display:flex; align-items:center; gap:9px; z-index:200; box-shadow:0 20px 40px -14px rgba(0,0,0,0.5);
  transition:opacity .25s ease, transform .25s ease; pointer-events:none;
}
#toast.show{opacity:1; transform:translateX(-50%) translateY(0);}
#toast .icon{color:var(--accent);}

/* Toggle switch */
.switch{position:relative; width:38px; height:22px; flex-shrink:0;}
.switch input{opacity:0; width:0; height:0;}
.switch-track{position:absolute; inset:0; background:var(--surface-2); border:1px solid var(--border); border-radius:20px; transition:.2s;}
.switch-track::before{content:''; position:absolute; width:16px; height:16px; left:2px; top:2px; background:var(--text-faint); border-radius:50%; transition:.2s;}
.switch input:checked + .switch-track{background:var(--accent-soft-strong); border-color:var(--accent);}
.switch input:checked + .switch-track::before{transform:translateX(16px); background:var(--accent);}

/* Settings */
.settings-tabs{display:flex; gap:6px; border-bottom:1px solid var(--border); margin-bottom:22px;}
.settings-tab{padding:10px 4px; margin-right:22px; font-size:13.5px; font-weight:500; color:var(--text-faint); border-bottom:2px solid transparent; position:relative; top:1px;}
.settings-tab.active{color:var(--text); border-color:var(--accent);}
.settings-panel{display:none;}
.settings-panel.active{display:block; animation:fadein .2s ease;}
.form-row{display:grid; grid-template-columns:1fr 1fr; gap:14px; margin-bottom:14px;}
.form-field label{display:block; font-size:12px; color:var(--text-muted); margin-bottom:6px;}
.form-field input, .form-field select{
  width:100%; padding:10px 12px; background:var(--surface-2); border:1px solid var(--border);
  border-radius:9px; color:var(--text); font-size:13px;
}
.form-field input:focus, .form-field select:focus{border-color:var(--accent);}
.pref-row{display:flex; align-items:center; justify-content:space-between; padding:14px 0; border-bottom:1px solid var(--border);}
.pref-row:last-child{border-bottom:none;}
.pref-row .t{font-size:13.5px; font-weight:500;}
.pref-row .d{font-size:12px; color:var(--text-faint); margin-top:2px;}
.theme-options{display:flex; gap:12px;}
.theme-opt{border:1px solid var(--border); border-radius:14px; padding:10px; cursor:pointer; width:110px;}
.theme-opt.selected{border-color:var(--accent); background:var(--accent-soft);}
.theme-opt .swatch{height:56px; border-radius:9px; margin-bottom:8px;}
.theme-opt .swatch.dark{background:linear-gradient(180deg,#131A28,#0A0E16);}
.theme-opt .swatch.light{background:linear-gradient(180deg,#FFFFFF,#EEF1F7); border:1px solid #DEE3ED;}
.theme-opt span{font-size:12px; font-weight:500;}

/* Users */
.role-badge{padding:3px 10px; border-radius:20px; font-size:11px; font-weight:600;}
.role-badge.admin{background:var(--violet-soft); color:var(--violet);}
.role-badge.analyst{background:var(--accent-soft); color:var(--accent);}
.role-badge.viewer{background:var(--surface-2); color:var(--text-muted); border:1px solid var(--border);}

/* Responsive */
@media (max-width:1180px){
  .grid-4{grid-template-columns:repeat(2,1fr);}
  .grid-widgets{grid-template-columns:repeat(2,1fr);}
  .grid-3, .grid-2{grid-template-columns:1fr;}
  .search-box{width:180px;}
}
@media (max-width:860px){
  .sidebar{transform:translateX(-100%);}
  .sidebar.open{transform:translateX(0);}
  .main{margin-left:0;}
  .menu-btn{display:flex;}
  #login-screen{grid-template-columns:1fr;}
  .login-hero{display:none;}
  .grid-4{grid-template-columns:1fr 1fr;}
  .grid-widgets{grid-template-columns:1fr;}
  .form-row{grid-template-columns:1fr;}
}
</style>
</head>
<body data-theme="dark">

<!-- ============================= LOGIN SCREEN ============================= -->
<div id="login-screen">
  <div class="login-hero">
    <div class="brand-mark">
      <div class="mark"><svg viewBox="0 0 24 24" class="icon"><path d="M12 2l8 3.5v6c0 5-3.4 8.7-8 10.5-4.6-1.8-8-5.5-8-10.5v-6L12 2z"/><path d="M9 12l2 2 4-4"/></svg></div>
      <span>OCULR</span>
    </div>

    <div class="radar-wrap">
      <div class="radar">
        <svg viewBox="0 0 340 340">
          <circle class="radar-ring" cx="170" cy="170" r="150"/>
          <circle class="radar-ring" cx="170" cy="170" r="112"/>
          <circle class="radar-ring" cx="170" cy="170" r="74"/>
          <circle class="radar-ring" cx="170" cy="170" r="36"/>
          <line x1="20" y1="170" x2="320" y2="170" stroke="rgba(51,214,195,0.14)" stroke-width="1"/>
          <line x1="170" y1="20" x2="170" y2="320" stroke="rgba(51,214,195,0.14)" stroke-width="1"/>
          <g class="radar-sweep-group">
            <defs>
              <linearGradient id="sweepGrad" x1="0" y1="0" x2="1" y2="0">
                <stop offset="0%" stop-color="#33D6C3" stop-opacity="0"/>
                <stop offset="100%" stop-color="#33D6C3" stop-opacity="0.35"/>
              </linearGradient>
            </defs>
            <path d="M170 170 L170 20 A150 150 0 0 1 240 42 Z" fill="url(#sweepGrad)"/>
          </g>
          <circle class="radar-blip" cx="230" cy="120" r="4"/>
          <circle class="radar-blip b2" cx="120" cy="230" r="4"/>
          <circle class="radar-blip b3" cx="240" cy="230" r="5"/>
          <circle class="radar-blip" cx="110" cy="110" r="3.5"/>
          <circle class="radar-center" cx="170" cy="170" r="5"/>
        </svg>
      </div>
      <div class="radar-caption">
        <div class="big">Scanning network perimeter</div>
        <div class="small">4 devices flagged · last sweep 00:02s ago</div>
      </div>
    </div>

    <div class="hero-copy">
      <h1>Every signal, one console.</h1>
      <p>OCULR unifies threat detection, device telemetry and incident response so your team sees risk the moment it appears.</p>
      <div class="hero-stats">
        <div><b>1.2M+</b><span>events / day</span></div>
        <div><b>99.98%</b><span>uptime</span></div>
        <div><b>&lt;40s</b><span>avg. detection</span></div>
      </div>
    </div>
  </div>

  <div class="login-panel">
    <div class="login-box" id="login-form-wrap">
      <div class="eyebrow">Security Operations Console</div>
      <h2>Welcome back</h2>
      <div class="sub">Sign in to monitor threats and manage incidents.</div>

      <form id="login-form" onsubmit="return handleLogin(event)">
        <div class="field">
          <label>Email or username</label>
          <div class="input-wrap">
            <svg class="icon" viewBox="0 0 24 24"><path d="M4 4h16v16H4z" opacity="0"/><path d="M3 6l9 7 9-7"/><rect x="3" y="5" width="18" height="14" rx="2"/></svg>
            <input type="text" placeholder="you@company.com" value="a.rahman" required style="padding-left:40px;">
          </div>
        </div>
        <div class="field">
          <label>Password</label>
          <div class="input-wrap">
            <svg class="icon" viewBox="0 0 24 24"><rect x="4" y="10" width="16" height="10" rx="2"/><path d="M8 10V7a4 4 0 0 1 8 0v3"/></svg>
            <input id="pw-field" type="password" placeholder="••••••••••" value="••••••••••" required style="padding-left:40px; padding-right:38px;">
            <button type="button" class="toggle-eye" onclick="togglePw()">
              <svg class="icon" viewBox="0 0 24 24"><path d="M1 12s4-7 11-7 11 7 11 7-4 7-11 7-11-7-11-7z"/><circle cx="12" cy="12" r="3"/></svg>
            </button>
          </div>
        </div>

        <div class="row-between">
          <label class="checkbox-line"><input type="checkbox" checked> Remember me</label>
          <a class="link-muted" href="#" onclick="showToast('Password reset link sent'); return false;">Forgot password?</a>
        </div>

        <button type="submit" class="btn btn-primary">Sign in to console</button>
      </form>

      <div class="divider-or">two-factor verification enabled</div>
      <button class="btn btn-outline" style="width:100%;" onclick="showToast('SSO is configured by your organization admin')">
        <svg class="icon" viewBox="0 0 24 24" style="width:15px;height:15px;"><circle cx="12" cy="12" r="9"/><path d="M12 3v18M3 12h18"/></svg>
        Continue with company SSO
      </button>
    </div>

    <!-- 2FA step -->
    <div class="login-box hidden" id="twofa-wrap">
      <div class="eyebrow">Step 2 of 2</div>
      <h2>Verify it's you</h2>
      <div class="sub">Enter the 6-digit code from your authenticator app.</div>
      <div class="twofa-box">
        <input maxlength="1" class="tfa-in" value="4">
        <input maxlength="1" class="tfa-in" value="1">
        <input maxlength="1" class="tfa-in" value="9">
        <input maxlength="1" class="tfa-in" value="2">
        <input maxlength="1" class="tfa-in" value="0">
        <input maxlength="1" class="tfa-in" value="7">
      </div>
      <button class="btn btn-primary" style="width:100%;" onclick="completeLogin()">Verify &amp; continue</button>
      <div style="text-align:center; margin-top:16px;">
        <a class="link-muted" href="#" onclick="showToast('New code sent'); return false;">Resend code</a>
      </div>
    </div>
  </div>
</div>

<!-- ============================= APP ============================= -->
<div id="app" class="hidden">
  <aside class="sidebar" id="sidebar">
    <div class="sidebar-head">
      <div class="mark"><svg viewBox="0 0 24 24" class="icon"><path d="M12 2l8 3.5v6c0 5-3.4 8.7-8 10.5-4.6-1.8-8-5.5-8-10.5v-6L12 2z"/><path d="M9 12l2 2 4-4"/></svg></div>
      <span>OCULR</span>
    </div>
    <div class="nav-group" style="flex:1; overflow-y:auto;">
      <div class="nav-label">Overview</div>
      <div class="nav-item active" data-view="dashboard" onclick="switchView('dashboard')">
        <svg class="icon" viewBox="0 0 24 24"><rect x="3" y="3" width="7" height="9" rx="1.5"/><rect x="14" y="3" width="7" height="5" rx="1.5"/><rect x="14" y="12" width="7" height="9" rx="1.5"/><rect x="3" y="16" width="7" height="5" rx="1.5"/></svg>
        Dashboard
      </div>
      <div class="nav-item" data-view="threats" onclick="switchView('threats')">
        <svg class="icon" viewBox="0 0 24 24"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg>
        Threat Monitoring
        <span class="count">8</span>
      </div>
      <div class="nav-item" data-view="incident" onclick="switchView('incident')">
        <svg class="icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="9"/><path d="M12 8v4l3 2"/></svg>
        Incidents
      </div>
      <div class="nav-item" data-view="devices" onclick="switchView('devices')">
        <svg class="icon" viewBox="0 0 24 24"><rect x="2" y="3" width="20" height="13" rx="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="17" x2="12" y2="21"/></svg>
        Devices &amp; Network
      </div>
      <div class="nav-label" style="margin-top:10px;">Insights</div>
      <div class="nav-item" data-view="reports" onclick="switchView('reports')">
        <svg class="icon" viewBox="0 0 24 24"><path d="M3 3v18h18"/><path d="M7 14l4-4 3 3 5-6"/></svg>
        Reports &amp; Analytics
      </div>
      <div class="nav-item" data-view="users" onclick="switchView('users')">
        <svg class="icon" viewBox="0 0 24 24"><circle cx="9" cy="8" r="3.5"/><path d="M2.5 20c1-4 3.6-6 6.5-6s5.5 2 6.5 6"/><circle cx="18" cy="9" r="2.5"/><path d="M15.5 14c2.4.3 4 2 4.8 5"/></svg>
        User Management
      </div>
      <div class="nav-label" style="margin-top:10px;">System</div>
      <div class="nav-item" data-view="settings" onclick="switchView('settings')">
        <svg class="icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="3"/><path d="M19.4 15a1.7 1.7 0 0 0 .3 1.9l.1.1a2 2 0 1 1-2.8 2.8l-.1-.1a1.7 1.7 0 0 0-1.9-.3 1.7 1.7 0 0 0-1 1.6V21a2 2 0 1 1-4 0v-.1a1.7 1.7 0 0 0-1-1.6 1.7 1.7 0 0 0-1.9.3l-.1.1a2 2 0 1 1-2.8-2.8l.1-.1a1.7 1.7 0 0 0 .3-1.9 1.7 1.7 0 0 0-1.6-1H3a2 2 0 1 1 0-4h.1a1.7 1.7 0 0 0 1.6-1 1.7 1.7 0 0 0-.3-1.9l-.1-.1a2 2 0 1 1 2.8-2.8l.1.1a1.7 1.7 0 0 0 1.9.3H9a1.7 1.7 0 0 0 1-1.6V3a2 2 0 1 1 4 0v.1a1.7 1.7 0 0 0 1 1.6 1.7 1.7 0 0 0 1.9-.3l.1-.1a2 2 0 1 1 2.8 2.8l-.1.1a1.7 1.7 0 0 0-.3 1.9V9a1.7 1.7 0 0 0 1.6 1H21a2 2 0 1 1 0 4h-.1a1.7 1.7 0 0 0-1.6 1z"/></svg>
        Settings
      </div>
    </div>
    <div class="sidebar-foot">
      <div class="mini-profile" onclick="switchView('settings')">
        <div class="avatar">AR</div>
        <div style="flex:1;">
          <div class="name">Sheikh Rehman</div>
          <div class="role">Security Analyst</div>
        </div>
        <svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;color:var(--text-faint);"><polyline points="9 18 15 12 9 6"/></svg>
      </div>
    </div>
  </aside>

  <div class="main">
    <header class="topbar">
      <button class="menu-btn icon-btn" onclick="document.getElementById('sidebar').classList.toggle('open')">
        <svg class="icon" viewBox="0 0 24 24"><line x1="3" y1="6" x2="21" y2="6"/><line x1="3" y1="12" x2="21" y2="12"/><line x1="3" y1="18" x2="21" y2="18"/></svg>
      </button>
      <div>
        <h1 id="topbar-title">Dashboard</h1>
        <div class="subtitle" id="topbar-sub">Wednesday, September 9 · Real-time overview</div>
      </div>
      <div class="search-box">
        <svg class="icon" viewBox="0 0 24 24" style="width:15px;height:15px;"><circle cx="11" cy="11" r="7"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>
        <input type="text" placeholder="Search threats, devices, IPs…">
      </div>
      <div class="topbar-right">
        <button class="icon-btn" onclick="toggleTheme()" title="Toggle theme">
          <svg class="icon" id="theme-icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="4"/><line x1="12" y1="1" x2="12" y2="3"/><line x1="12" y1="21" x2="12" y2="23"/><line x1="4.2" y1="4.2" x2="5.6" y2="5.6"/><line x1="18.4" y1="18.4" x2="19.8" y2="19.8"/><line x1="1" y1="12" x2="3" y2="12"/><line x1="21" y1="12" x2="23" y2="12"/><line x1="4.2" y1="19.8" x2="5.6" y2="18.4"/><line x1="18.4" y1="5.6" x2="19.8" y2="4.2"/></svg>
        </button>
        <button class="icon-btn" onclick="showToast('3 unread notifications')">
          <svg class="icon" viewBox="0 0 24 24"><path d="M18 8a6 6 0 0 0-12 0c0 7-3 9-3 9h18s-3-2-3-9"/><path d="M13.7 21a2 2 0 0 1-3.4 0"/></svg>
          <span class="dot"></span>
        </button>
        <div class="avatar" style="cursor:pointer;" onclick="switchView('settings')">AR</div>
      </div>
    </header>

    <div class="content">

      <!-- ============================= DASHBOARD ============================= -->
      <section class="view active" id="view-dashboard">
        <div class="page-head">
          <div>
            <h2>Good afternoon, Sheikh Rehman</h2>
            <p>Here's what's happening across your environment right now.</p>
          </div>
          <div class="page-actions">
            <button class="btn btn-ghost btn-sm" onclick="switchView('reports')">
              <svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><path d="M3 3v18h18"/><path d="M7 14l4-4 3 3 5-6"/></svg>
              View analytics
            </button>
            <button class="btn btn-primary btn-sm" onclick="switchView('threats')">
              <svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/></svg>
              Review threats
            </button>
          </div>
        </div>

        <div class="grid grid-4" style="margin-bottom:18px;">
          <div class="card kpi-card">
            <div class="kpi-top">
              <div class="kpi-icon teal"><svg class="icon" viewBox="0 0 24 24"><path d="M18 8a6 6 0 0 0-12 0c0 7-3 9-3 9h18s-3-2-3-9"/></svg></div>
              <div class="kpi-trend up-bad"><svg viewBox="0 0 24 24"><polyline points="17 7 7 17"/><polyline points="7 7 17 7 17 17"/></svg>+12</div>
            </div>
            <div class="kpi-value">247</div>
            <div class="kpi-label">Total security alerts today</div>
          </div>
          <div class="card kpi-card">
            <div class="kpi-top">
              <div class="kpi-icon red"><svg class="icon" viewBox="0 0 24 24"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg></div>
              <div class="kpi-trend up-bad"><svg viewBox="0 0 24 24"><polyline points="17 7 7 17"/><polyline points="7 7 17 7 17 17"/></svg>+3</div>
            </div>
            <div class="kpi-value">8</div>
            <div class="kpi-label">Active threats · 3 critical</div>
          </div>
          <div class="card kpi-card">
            <div class="kpi-top">
              <div class="kpi-icon green"><svg class="icon" viewBox="0 0 24 24"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><polyline points="22 4 12 14.01 9 11.01"/></svg></div>
              <div class="kpi-trend down-good"><svg viewBox="0 0 24 24"><polyline points="7 17 17 7"/><polyline points="7 7 17 7 17 17" transform="rotate(90 12 12)"/></svg>stable</div>
            </div>
            <div class="kpi-value">98.2%</div>
            <div class="kpi-label">System health status</div>
          </div>
          <div class="card kpi-card">
            <div class="kpi-top">
              <div class="kpi-icon violet"><svg class="icon" viewBox="0 0 24 24"><path d="M12 2l8 3.5v6c0 5-3.4 8.7-8 10.5-4.6-1.8-8-5.5-8-10.5v-6L12 2z"/></svg></div>
              <div class="kpi-trend up-good"><svg viewBox="0 0 24 24"><polyline points="7 17 17 7"/><polyline points="7 7 17 7 17 17"/></svg>Medium</div>
            </div>
            <div class="kpi-value">62<span style="font-size:15px; color:var(--text-faint);">/100</span></div>
            <div class="kpi-label">Overall risk score</div>
          </div>
        </div>

        <div class="grid grid-widgets" style="margin-bottom:18px;">
          <div class="card card-lg" style="grid-column:span 2;">
            <div class="card-head">
              <h3>Threat detection — last 24 hours</h3>
              <span class="muted">auto-refresh 30s</span>
            </div>
            <canvas id="chart-threat-detect" height="130"></canvas>
          </div>
          <div class="card card-lg">
            <div class="card-head"><h3>Security score</h3></div>
            <div class="score-ring-wrap">
              <svg width="132" height="132" viewBox="0 0 132 132">
                <circle class="score-ring-bg" cx="66" cy="66" r="56"/>
                <circle class="score-ring-fg" cx="66" cy="66" r="56" stroke-dasharray="352" stroke-dashoffset="90"/>
              </svg>
              <div class="score-ring-label"><b>74</b><span>Good</span></div>
            </div>
            <div style="display:flex; justify-content:center; gap:16px; margin-top:14px; font-size:11.5px; color:var(--text-faint);">
              <span>▲ 4 pts this week</span>
            </div>
          </div>
        </div>

        <div class="grid grid-widgets" style="margin-bottom:18px;">
          <div class="card">
            <div class="card-head"><h3>Firewall status</h3><span class="badge low">active</span></div>
            <div class="mini-row"><div class="mini-left"><div class="mini-icon" style="background:var(--low-soft); color:var(--low);"><svg class="icon" viewBox="0 0 24 24"><path d="M3 4l9-1 9 1v7c0 5-4 8.5-9 10-5-1.5-9-5-9-10V4z"/></svg></div><div><div style="font-weight:600; font-size:13px;">Inbound rules</div><div style="font-size:11.5px; color:var(--text-faint);">312 active</div></div></div><span style="font-family:var(--font-mono); font-size:12px; color:var(--low);">OK</span></div>
            <div class="mini-row"><div class="mini-left"><div class="mini-icon" style="background:var(--critical-soft); color:var(--critical);"><svg class="icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><line x1="4.9" y1="4.9" x2="19.1" y2="19.1"/></svg></div><div><div style="font-weight:600; font-size:13px;">Blocked today</div><div style="font-size:11.5px; color:var(--text-faint);">1,842 attempts</div></div></div><span class="mono" style="font-size:12px; color:var(--text-muted);">+6.2%</span></div>
            <div class="mini-row"><div class="mini-left"><div class="mini-icon" style="background:var(--accent-soft); color:var(--accent);"><svg class="icon" viewBox="0 0 24 24"><path d="M12 2l8 3.5v6c0 5-3.4 8.7-8 10.5-4.6-1.8-8-5.5-8-10.5v-6L12 2z"/></svg></div><div><div style="font-weight:600; font-size:13px;">Last policy update</div><div style="font-size:11.5px; color:var(--text-faint);">2 hours ago</div></div></div></div>
          </div>

          <div class="card">
            <div class="card-head"><h3>Network activity</h3></div>
            <canvas id="chart-network" height="150"></canvas>
          </div>

          <div class="card">
            <div class="card-head"><h3>Device status</h3></div>
            <div style="display:flex; align-items:center; gap:18px;">
              <canvas id="chart-devices" width="120" height="120" style="max-width:120px;"></canvas>
              <div style="flex:1; display:flex; flex-direction:column; gap:10px;">
                <div class="legend-row"><span class="legend-dot" style="background:var(--accent);"></span> Online <b style="margin-left:auto; font-family:var(--font-mono);">142</b></div>
                <div class="legend-row"><span class="legend-dot" style="background:var(--high);"></span> Idle <b style="margin-left:auto; font-family:var(--font-mono);">14</b></div>
                <div class="legend-row"><span class="legend-dot" style="background:var(--text-faint);"></span> Offline <b style="margin-left:auto; font-family:var(--font-mono);">8</b></div>
              </div>
            </div>
          </div>
        </div>

        <div class="grid grid-2">
          <div class="card">
            <div class="card-head"><h3>Recent incidents</h3><a class="link-muted" onclick="switchView('threats')" style="cursor:pointer;">View all</a></div>
            <div class="table-wrap">
              <table>
                <thead><tr><th>Incident</th><th>Severity</th><th>Status</th><th>Detected</th></tr></thead>
                <tbody id="recent-incidents-body"></tbody>
              </table>
            </div>
          </div>
          <div class="card">
            <div class="card-head"><h3>User activity</h3></div>
            <div id="user-activity-list"></div>
          </div>
        </div>
      </section>

      <!-- ============================= THREAT MONITORING ============================= -->
      <section class="view" id="view-threats">
        <div class="page-head">
          <div><h2>Threat monitoring</h2><p>8 active threats across your environment. Sorted by severity.</p></div>
          <div class="page-actions">
            <button class="btn btn-ghost btn-sm"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><path d="M21 21l-4.35-4.35"/><circle cx="10" cy="10" r="7"/></svg>Advanced search</button>
            <button class="btn btn-primary btn-sm" onclick="showToast('Threat feed refreshed')"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><polyline points="23 4 23 10 17 10"/><path d="M20.49 15a9 9 0 1 1-2.12-9.36L23 10"/></svg>Refresh feed</button>
          </div>
        </div>

        <div class="filter-bar">
          <div class="chip-filter active" onclick="filterThreats(this,'all')"><span class="dot" style="background:var(--text-faint);"></span> All (8)</div>
          <div class="chip-filter" onclick="filterThreats(this,'critical')"><span class="dot" style="background:var(--critical);"></span> Critical (2)</div>
          <div class="chip-filter" onclick="filterThreats(this,'high')"><span class="dot" style="background:var(--high);"></span> High (3)</div>
          <div class="chip-filter" onclick="filterThreats(this,'medium')"><span class="dot" style="background:var(--medium);"></span> Medium (2)</div>
          <div class="chip-filter" onclick="filterThreats(this,'low')"><span class="dot" style="background:var(--low);"></span> Low (1)</div>
          <div class="select-chip" style="margin-left:auto;">
            <svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><polygon points="22 3 2 3 10 12.46 10 19 14 21 14 12.46 22 3"/></svg>
            <select><option>Category: All</option><option>Malware</option><option>Phishing</option><option>Intrusion</option><option>DDoS</option></select>
          </div>
          <div class="select-chip">
            <svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg>
            <select><option>Last 24 hours</option><option>Last 7 days</option><option>Last 30 days</option></select>
          </div>
        </div>

        <div class="card" style="margin-bottom:18px;">
          <div class="card-head"><h3>Detection timeline</h3><span class="muted">by hour</span></div>
          <canvas id="chart-timeline" height="70"></canvas>
        </div>

        <div class="card">
          <div class="table-wrap">
            <table>
              <thead><tr><th>Threat</th><th>Category</th><th>Severity</th><th>Source IP</th><th>Affected devices</th><th>Detected</th><th>Status</th></tr></thead>
              <tbody id="threats-body"></tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- ============================= INCIDENT DETAILS ============================= -->
      <section class="view" id="view-incident">
        <div class="page-head">
          <div>
            <div style="display:flex; align-items:center; gap:10px; margin-bottom:6px;">
              <span class="badge critical" id="inc-severity">critical</span>
              <span class="badge neutral" id="inc-status">In progress</span>
              <span class="mono" style="font-size:12px; color:var(--text-faint);" id="inc-id">INC-2026-0847</span>
            </div>
            <h2 id="inc-title">Unusual outbound traffic from finance subnet</h2>
            <p id="inc-desc" style="max-width:640px;">A workstation in the finance VLAN began transmitting encrypted traffic to an unrecognized external endpoint outside business hours, consistent with data-exfiltration behavior.</p>
          </div>
          <div class="page-actions">
            <button class="btn btn-ghost btn-sm" onclick="showToast('Note added to incident')"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><path d="M12 20h9"/><path d="M16.5 3.5a2.1 2.1 0 0 1 3 3L7 19l-4 1 1-4L16.5 3.5z"/></svg>Add note</button>
            <button class="btn btn-outline btn-sm" onclick="showToast('Escalated to Tier 2 response')">Escalate</button>
            <button class="btn btn-primary btn-sm" onclick="showToast('Incident marked as resolved')"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><polyline points="20 6 9 17 4 12"/></svg>Mark resolved</button>
          </div>
        </div>

        <div class="grid grid-3">
          <div class="card card-lg">
            <div class="card-head"><h3>Activity timeline</h3></div>
            <div class="timeline">
              <div class="timeline-item"><div class="timeline-dot done"></div><div class="timeline-time">14:02:11</div><div class="timeline-title">Anomaly detected by NDR sensor</div><div class="timeline-desc">Outbound TLS session flagged — destination not in allow-list, 340MB transferred.</div></div>
              <div class="timeline-item"><div class="timeline-dot done"></div><div class="timeline-time">14:03:47</div><div class="timeline-title">Auto-triage assigned severity: Critical</div><div class="timeline-desc">Correlated with 2 prior alerts on the same host in the last 72 hours.</div></div>
              <div class="timeline-item"><div class="timeline-dot done"></div><div class="timeline-time">14:06:20</div><div class="timeline-title">Assigned to Sheikh Rehman</div><div class="timeline-desc">Incident routed to on-call analyst per finance-subnet escalation policy.</div></div>
              <div class="timeline-item"><div class="timeline-dot"></div><div class="timeline-time">14:18:03</div><div class="timeline-title">Host isolated from network</div><div class="timeline-desc">Endpoint FIN-WKS-014 quarantined pending forensic review.</div></div>
              <div class="timeline-item"><div class="timeline-dot"></div><div class="timeline-time">now</div><div class="timeline-title">Awaiting forensic report</div><div class="timeline-desc">Memory capture in progress — estimated completion in 12 minutes.</div></div>
            </div>
          </div>

          <div style="display:flex; flex-direction:column; gap:18px;">
            <div class="card">
              <div class="card-head"><h3>Details</h3></div>
              <div class="mini-row"><span style="color:var(--text-faint); font-size:12.5px;">Reported</span><span class="mono" style="font-size:12.5px;">Sep 9, 2026 · 14:02</span></div>
              <div class="mini-row"><span style="color:var(--text-faint); font-size:12.5px;">Category</span><span style="font-size:12.5px;">Data exfiltration</span></div>
              <div class="mini-row"><span style="color:var(--text-faint); font-size:12.5px;">Assigned analyst</span><span style="display:flex; align-items:center; gap:6px; font-size:12.5px;"><span class="avatar" style="width:20px;height:20px; font-size:9px;">AR</span>Sheikh Rehman</span></div>
              <div class="mini-row"><span style="color:var(--text-faint); font-size:12.5px;">Affected host</span><span class="mono" style="font-size:12.5px;">FIN-WKS-014</span></div>
              <div class="mini-row"><span style="color:var(--text-faint); font-size:12.5px;">Source IP</span><span class="mono" style="font-size:12.5px;">10.4.22.108</span></div>
              <div class="mini-row"><span style="color:var(--text-faint); font-size:12.5px;">Destination IP</span><span class="mono" style="font-size:12.5px;">185.220.101.47</span></div>
            </div>
            <div class="card">
              <div class="card-head"><h3>Resolution progress</h3></div>
              <div class="progress-track" style="margin-bottom:8px;"><div class="progress-fill" style="width:60%; background:linear-gradient(90deg, var(--high), var(--critical));"></div></div>
              <div style="font-size:12px; color:var(--text-faint);">3 of 5 response steps complete</div>
            </div>
          </div>
        </div>
      </section>

      <!-- ============================= DEVICES & NETWORK ============================= -->
      <section class="view" id="view-devices">
        <div class="page-head">
          <div><h2>Devices &amp; network</h2><p>164 devices under management across 6 network segments.</p></div>
          <div class="page-actions"><button class="btn btn-primary btn-sm" onclick="showToast('Scanning network for new devices…')"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><circle cx="11" cy="11" r="7"/><line x1="21" y1="21" x2="16.65" y2="16.65"/></svg>Scan network</button></div>
        </div>

        <div class="grid grid-4" style="margin-bottom:18px;">
          <div class="card kpi-card"><div class="kpi-top"><div class="kpi-icon teal"><svg class="icon" viewBox="0 0 24 24"><rect x="2" y="3" width="20" height="13" rx="2"/></svg></div></div><div class="kpi-value">164</div><div class="kpi-label">Total connected devices</div></div>
          <div class="card kpi-card"><div class="kpi-top"><div class="kpi-icon green"><svg class="icon" viewBox="0 0 24 24"><path d="M22 11.08V12a10 10 0 1 1-5.93-9.14"/><polyline points="22 4 12 14.01 9 11.01"/></svg></div></div><div class="kpi-value">142</div><div class="kpi-label">Online now</div></div>
          <div class="card kpi-card"><div class="kpi-top"><div class="kpi-icon red"><svg class="icon" viewBox="0 0 24 24"><circle cx="12" cy="12" r="10"/><line x1="4.9" y1="4.9" x2="19.1" y2="19.1"/></svg></div></div><div class="kpi-value">8</div><div class="kpi-label">Offline / unreachable</div></div>
          <div class="card kpi-card"><div class="kpi-top"><div class="kpi-icon violet"><svg class="icon" viewBox="0 0 24 24"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/></svg></div></div><div class="kpi-value">11</div><div class="kpi-label">Devices with open vulnerabilities</div></div>
        </div>

        <div class="grid grid-widgets" style="margin-bottom:18px;">
          <div class="card" style="grid-column:span 2;">
            <div class="card-head"><h3>Network traffic — inbound vs outbound</h3></div>
            <canvas id="chart-net-traffic" height="130"></canvas>
          </div>
          <div class="card">
            <div class="card-head"><h3>Firewall</h3><span class="badge low">active</span></div>
            <div style="text-align:center; padding:8px 0 4px;">
              <div style="font-family:var(--font-display); font-size:30px;">1,842</div>
              <div style="font-size:12px; color:var(--text-faint);">threats blocked today</div>
            </div>
            <div class="progress-track" style="margin-top:14px;"><div class="progress-fill" style="width:82%; background:var(--accent);"></div></div>
            <div style="font-size:11.5px; color:var(--text-faint); margin-top:6px;">Rule coverage: 82% of traffic inspected</div>
          </div>
        </div>

        <div class="card">
          <div class="card-head"><h3>Connected devices</h3></div>
          <div class="table-wrap">
            <table>
              <thead><tr><th>Device</th><th>IP address</th><th>Status</th><th>Health</th><th>Vulnerabilities</th><th>Last seen</th></tr></thead>
              <tbody id="devices-body"></tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- ============================= REPORTS & ANALYTICS ============================= -->
      <section class="view" id="view-reports">
        <div class="page-head">
          <div><h2>Reports &amp; analytics</h2><p>Trends and statistics across the last 6 months.</p></div>
          <div class="page-actions">
            <div class="select-chip"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><rect x="3" y="4" width="18" height="18" rx="2"/><line x1="16" y1="2" x2="16" y2="6"/><line x1="8" y1="2" x2="8" y2="6"/><line x1="3" y1="10" x2="21" y2="10"/></svg><select><option>Last 6 months</option><option>Last 12 months</option><option>This year</option></select></div>
            <button class="btn btn-primary btn-sm" onclick="showToast('Preparing PDF export…')"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg>Export report</button>
          </div>
        </div>

        <div class="grid grid-4" style="margin-bottom:18px;">
          <div class="card kpi-card"><div class="kpi-value">1,486</div><div class="kpi-label">Total incidents (6 mo)</div></div>
          <div class="card kpi-card"><div class="kpi-value">92.4%</div><div class="kpi-label">Auto-resolved rate</div></div>
          <div class="card kpi-card"><div class="kpi-value">18m</div><div class="kpi-label">Avg. time to respond</div></div>
          <div class="card kpi-card"><div class="kpi-value">3.1%</div><div class="kpi-label">Repeat threat rate</div></div>
        </div>

        <div class="grid grid-widgets" style="margin-bottom:18px; grid-template-columns:1.4fr 1fr;">
          <div class="card">
            <div class="card-head"><h3>Threat trend over time</h3></div>
            <canvas id="chart-trend" height="120"></canvas>
          </div>
          <div class="card">
            <div class="card-head"><h3>Attacks by category</h3></div>
            <canvas id="chart-attack-cat" height="150"></canvas>
          </div>
        </div>

        <div class="card">
          <div class="card-head"><h3>Available reports</h3></div>
          <div class="table-wrap">
            <table>
              <thead><tr><th>Report</th><th>Period</th><th>Generated</th><th>Format</th><th></th></tr></thead>
              <tbody id="reports-body"></tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- ============================= USER MANAGEMENT ============================= -->
      <section class="view" id="view-users">
        <div class="page-head">
          <div><h2>User management</h2><p>12 users have access to this console.</p></div>
          <div class="page-actions"><button class="btn btn-primary btn-sm" onclick="openModal('modal-add-user')"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><path d="M16 21v-2a4 4 0 0 0-4-4H6a4 4 0 0 0-4 4v2"/><circle cx="9" cy="7" r="4"/><line x1="19" y1="8" x2="19" y2="14"/><line x1="16" y1="11" x2="22" y2="11"/></svg>Add user</button></div>
        </div>

        <div class="card">
          <div class="table-wrap">
            <table>
              <thead><tr><th>User</th><th>Role</th><th>Status</th><th>Last active</th><th></th></tr></thead>
              <tbody id="users-body"></tbody>
            </table>
          </div>
        </div>
      </section>

      <!-- ============================= SETTINGS ============================= -->
      <section class="view" id="view-settings">
        <div class="page-head"><div><h2>Settings</h2><p>Manage your profile, notifications and security preferences.</p></div></div>

        <div class="settings-tabs">
          <div class="settings-tab active" onclick="switchSettingsTab(this,'s-profile')">Profile</div>
          <div class="settings-tab" onclick="switchSettingsTab(this,'s-notif')">Notifications</div>
          <div class="settings-tab" onclick="switchSettingsTab(this,'s-appearance')">Appearance</div>
          <div class="settings-tab" onclick="switchSettingsTab(this,'s-security')">Security</div>
        </div>

        <div class="settings-panel active" id="s-profile">
          <div class="card" style="max-width:640px;">
            <div style="display:flex; align-items:center; gap:14px; margin-bottom:20px;">
              <div class="avatar" style="width:52px; height:52px; font-size:17px;">AR</div>
              <div><div style="font-weight:600;">Sheikh Rehman</div><div style="font-size:12px; color:var(--text-faint);">Security Analyst · Tier 2</div></div>
              <button class="btn btn-ghost btn-sm" style="margin-left:auto;">Change photo</button>
            </div>
            <div class="form-row">
              <div class="form-field"><label>Full name</label><input type="text" value="Sheikh Rehman"></div>
              <div class="form-field"><label>Job title</label><input type="text" value="Security Analyst"></div>
            </div>
            <div class="form-row">
              <div class="form-field"><label>Email</label><input type="email" value="rahman@oculr-corp.com"></div>
              <div class="form-field"><label>Phone</label><input type="text" value="+92 300 1234567"></div>
            </div>
            <button class="btn btn-primary btn-sm" onclick="showToast('Profile changes saved')">Save changes</button>
          </div>
        </div>

        <div class="settings-panel" id="s-notif">
          <div class="card" style="max-width:640px;">
            <div class="pref-row"><div><div class="t">Critical threat alerts</div><div class="d">Get notified immediately when a critical threat is detected</div></div><label class="switch"><input type="checkbox" checked><span class="switch-track"></span></label></div>
            <div class="pref-row"><div><div class="t">Incident status changes</div><div class="d">Notify when an assigned incident's status changes</div></div><label class="switch"><input type="checkbox" checked><span class="switch-track"></span></label></div>
            <div class="pref-row"><div><div class="t">Weekly summary email</div><div class="d">A digest of activity sent every Monday morning</div></div><label class="switch"><input type="checkbox"><span class="switch-track"></span></label></div>
            <div class="pref-row"><div><div class="t">New device connected</div><div class="d">Alert when an unrecognized device joins the network</div></div><label class="switch"><input type="checkbox" checked><span class="switch-track"></span></label></div>
          </div>
        </div>

        <div class="settings-panel" id="s-appearance">
          <div class="card" style="max-width:640px;">
            <div style="font-size:13px; font-weight:600; margin-bottom:12px;">Theme</div>
            <div class="theme-options">
              <div class="theme-opt selected" id="theme-opt-dark" onclick="setTheme('dark')"><div class="swatch dark"></div><span>Dark</span></div>
              <div class="theme-opt" id="theme-opt-light" onclick="setTheme('light')"><div class="swatch light"></div><span>Light</span></div>
            </div>
          </div>
        </div>

        <div class="settings-panel" id="s-security">
          <div class="card" style="max-width:640px; margin-bottom:16px;">
            <div class="pref-row"><div><div class="t">Two-factor authentication</div><div class="d">Require an authenticator code at every sign-in</div></div><label class="switch"><input type="checkbox" checked><span class="switch-track"></span></label></div>
            <div class="pref-row"><div><div class="t">Session timeout</div><div class="d">Automatically sign out after inactivity</div></div><div class="select-chip"><select><option>30 minutes</option><option>1 hour</option><option>4 hours</option></select></div></div>
          </div>
          <div class="card" style="max-width:640px;">
            <div style="font-size:13px; font-weight:600; margin-bottom:14px;">Change password</div>
            <div class="form-field" style="margin-bottom:12px;"><label>Current password</label><input type="password" value="••••••••••"></div>
            <div class="form-row">
              <div class="form-field"><label>New password</label><input type="password" placeholder="Enter new password"></div>
              <div class="form-field"><label>Confirm new password</label><input type="password" placeholder="Repeat new password"></div>
            </div>
            <div style="display:flex; gap:10px; margin-top:6px;">
              <button class="btn btn-primary btn-sm" onclick="showToast('Password updated')">Update password</button>
              <button class="btn btn-danger-o btn-sm" onclick="doLogout()"><svg class="icon" viewBox="0 0 24 24" style="width:14px;height:14px;"><path d="M9 21H5a2 2 0 0 1-2-2V5a2 2 0 0 1 2-2h4"/><polyline points="16 17 21 12 16 7"/><line x1="21" y1="12" x2="9" y2="12"/></svg>Log out</button>
            </div>
          </div>
        </div>
      </section>

    </div>
  </div>
</div>

<!-- Add user modal -->
<div class="modal-overlay hidden" id="modal-add-user">
  <div class="modal-box">
    <div class="modal-head"><h3>Add team member</h3><button class="modal-close" onclick="closeModal('modal-add-user')">
      <svg class="icon" viewBox="0 0 24 24"><line x1="18" y1="6" x2="6" y2="18"/><line x1="6" y1="6" x2="18" y2="18"/></svg></button></div>
    <div style="font-size:12.5px; color:var(--text-faint); margin-bottom:18px;">They'll receive an email invite to join the console.</div>
    <div class="field"><label>Full name</label><input type="text" placeholder="e.g. Farhan Ali" style="width:100%; padding:11px 13px; background:var(--surface-2); border:1px solid var(--border); border-radius:9px; color:var(--text); font-size:13px;"></div>
    <div class="field"><label>Email address</label><input type="email" placeholder="name@company.com" style="width:100%; padding:11px 13px; background:var(--surface-2); border:1px solid var(--border); border-radius:9px; color:var(--text); font-size:13px;"></div>
    <div class="field">
      <label>Role</label>
      <select style="width:100%; padding:11px 13px; background:var(--surface-2); border:1px solid var(--border); border-radius:9px; color:var(--text); font-size:13px;">
        <option>Analyst</option><option>Administrator</option><option>Viewer</option>
      </select>
    </div>
    <button class="btn btn-primary" style="width:100%; margin-top:6px;" onclick="closeModal('modal-add-user'); showToast('Invite sent')">Send invite</button>
  </div>
</div>

<div id="toast"><svg class="icon" viewBox="0 0 24 24"><polyline points="20 6 9 17 4 12"/></svg><span id="toast-msg"></span></div>

<script>
/* ============================= DATA ============================= */
const threats = [
  {name:'Ransomware payload detected', cat:'Malware', sev:'critical', ip:'10.4.22.108', devices:3, time:'2 min ago', status:'Active'},
  {name:'Unusual outbound traffic — finance subnet', cat:'Exfiltration', sev:'critical', ip:'10.4.22.108', devices:1, time:'12 min ago', status:'In progress'},
  {name:'Brute-force login attempts', cat:'Intrusion', sev:'high', ip:'203.0.113.44', devices:1, time:'34 min ago', status:'Active'},
  {name:'Phishing email campaign flagged', cat:'Phishing', sev:'high', ip:'—', devices:26, time:'1 hr ago', status:'Contained'},
  {name:'Suspicious PowerShell execution', cat:'Malware', sev:'high', ip:'10.4.8.19', devices:2, time:'2 hr ago', status:'Active'},
  {name:'Port scan from external host', cat:'Reconnaissance', sev:'medium', ip:'198.51.100.23', devices:1, time:'3 hr ago', status:'Monitoring'},
  {name:'Outdated TLS certificate detected', cat:'Configuration', sev:'medium', ip:'10.4.1.5', devices:1, time:'5 hr ago', status:'Monitoring'},
  {name:'New device joined guest network', cat:'Policy', sev:'low', ip:'10.9.0.44', devices:1, time:'6 hr ago', status:'Reviewed'},
];
const devices = [
  {name:'FIN-WKS-014', type:'laptop', ip:'10.4.22.108', status:'offline', health:22, vulns:3, seen:'Isolated'},
  {name:'HR-DESKTOP-02', type:'desktop', ip:'10.2.1.14', status:'online', health:96, vulns:0, seen:'Just now'},
  {name:'core-router-01', type:'router', ip:'10.0.0.1', status:'online', health:99, vulns:0, seen:'Just now'},
  {name:'db-server-prod', type:'server', ip:'10.1.5.2', status:'online', health:88, vulns:1, seen:'Just now'},
  {name:'MKT-LAPTOP-19', type:'laptop', ip:'10.3.4.61', status:'online', health:74, vulns:2, seen:'3 min ago'},
  {name:'iot-cam-lobby', type:'camera', ip:'10.9.2.11', status:'warn', health:41, vulns:4, seen:'11 min ago'},
  {name:'SALES-PHONE-07', type:'phone', ip:'10.3.9.30', status:'online', health:91, vulns:0, seen:'1 min ago'},
  {name:'print-svc-02', type:'printer', ip:'10.1.1.40', status:'offline', health:0, vulns:1, seen:'2 days ago'},
];
const users = [
  {name:'Alina', email:'alina@oculr-corp.com', role:'admin', status:'online', active:'Now'},
  {name:'Sara', email:'sara@oculr-corp.com', role:'analyst', status:'online', active:'Now'},
  {name:'Ayesha', email:'ayesha@oculr-corp.com', role:'analyst', status:'offline', active:'2 hr ago'},
  {name:'Sheikh Rehman', email:'rehman@oculr-corp.com', role:'viewer', status:'offline', active:'1 day ago'},
  {name:'Omar', email:'omar@oculr-corp.com', role:'analyst', status:'online', active:'Now'},
  {name:'Zara Malik', email:'z.malik@oculr-corp.com', role:'viewer', status:'offline', active:'3 days ago'},
];
const reports = [
  {name:'Monthly Threat Summary', period:'August 2026', gen:'Sep 1, 2026', fmt:'PDF'},
  {name:'Incident Response Report', period:'Q3 2026', gen:'Sep 5, 2026', fmt:'PDF'},
  {name:'Vulnerability Assessment', period:'August 2026', gen:'Aug 30, 2026', fmt:'XLSX'},
  {name:'Network Traffic Analysis', period:'Last 30 days', gen:'Sep 8, 2026', fmt:'PDF'},
  {name:'Compliance Audit Log', period:'Q3 2026', gen:'Sep 2, 2026', fmt:'CSV'},
];

const deviceIcons = {
  laptop:'<rect x="3" y="4" width="18" height="12" rx="2"/><line x1="2" y1="20" x2="22" y2="20"/>',
  desktop:'<rect x="4" y="3" width="16" height="12" rx="2"/><line x1="8" y1="21" x2="16" y2="21"/><line x1="12" y1="15" x2="12" y2="21"/>',
  router:'<rect x="2" y="9" width="20" height="7" rx="1.5"/><circle cx="7" cy="12.5" r="1"/><circle cx="11" cy="12.5" r="1"/>',
  server:'<rect x="3" y="4" width="18" height="6" rx="1.5"/><rect x="3" y="14" width="18" height="6" rx="1.5"/><circle cx="7" cy="7" r="0.8"/><circle cx="7" cy="17" r="0.8"/>',
  camera:'<path d="M23 19a2 2 0 0 1-2 2H3a2 2 0 0 1-2-2V8a2 2 0 0 1 2-2h4l2-3h6l2 3h4a2 2 0 0 1 2 2z"/><circle cx="12" cy="13" r="4"/>',
  phone:'<rect x="7" y="2" width="10" height="20" rx="2"/><line x1="11" y1="18" x2="13" y2="18"/>',
  printer:'<polyline points="6 9 6 2 18 2 18 9"/><path d="M6 18H4a2 2 0 0 1-2-2v-5a2 2 0 0 1 2-2h16a2 2 0 0 1 2 2v5a2 2 0 0 1-2 2h-2"/><rect x="6" y="14" width="12" height="8"/>',
};

function sevBadge(sev){ return `<span class="badge ${sev}">${sev}</span>`; }
function statusPillFor(s){
  if(s==='online') return `<span class="status-pill online"><span class="dot"></span>Online</span>`;
  if(s==='offline') return `<span class="status-pill offline"><span class="dot"></span>Offline</span>`;
  return `<span class="status-pill warn"><span class="dot"></span>Warning</span>`;
}
function healthColor(h){ if(h>=80) return 'var(--low)'; if(h>=45) return 'var(--high)'; return 'var(--critical)'; }

/* ============================= RENDER TABLES ============================= */
function renderThreats(filter){
  const body = document.getElementById('threats-body');
  const list = filter && filter!=='all' ? threats.filter(t=>t.sev===filter) : threats;
  body.innerHTML = list.map(t => `
    <tr onclick="openIncidentFromThreat('${t.name.replace(/'/g,"\\'")}','${t.sev}')">
      <td><div class="cell-main"><div class="cell-icon"><svg class="icon" viewBox="0 0 24 24"><path d="M10.29 3.86L1.82 18a2 2 0 0 0 1.71 3h16.94a2 2 0 0 0 1.71-3L13.71 3.86a2 2 0 0 0-3.42 0z"/><line x1="12" y1="9" x2="12" y2="13"/><line x1="12" y1="17" x2="12.01" y2="17"/></svg></div><div><div class="cell-title">${t.name}</div><div class="cell-sub">ID: THR-${(Math.random()*9000+1000).toFixed(0)}</div></div></div></td>
      <td>${t.cat}</td>
      <td>${sevBadge(t.sev)}</td>
      <td class="mono">${t.ip}</td>
      <td>${t.devices}</td>
      <td class="mono" style="color:var(--text-faint);">${t.time}</td>
      <td><span class="badge neutral">${t.status}</span></td>
    </tr>`).join('');
}
function renderRecentIncidents(){
  const body = document.getElementById('recent-incidents-body');
  body.innerHTML = threats.slice(0,5).map(t=>`
    <tr onclick="openIncidentFromThreat('${t.name.replace(/'/g,"\\'")}','${t.sev}')">
      <td><div class="cell-title" style="font-size:12.5px;">${t.name}</div></td>
      <td>${sevBadge(t.sev)}</td>
      <td><span class="badge neutral">${t.status}</span></td>
      <td class="mono" style="color:var(--text-faint); font-size:11.5px;">${t.time}</td>
    </tr>`).join('');
}
function renderDevices(){
  const body = document.getElementById('devices-body');
  body.innerHTML = devices.map(d=>`
    <tr>
      <td><div class="cell-main"><div class="cell-icon"><svg class="icon" viewBox="0 0 24 24">${deviceIcons[d.type]}</svg></div><div><div class="cell-title">${d.name}</div><div class="cell-sub">${d.type}</div></div></div></td>
      <td class="mono">${d.ip}</td>
      <td>${statusPillFor(d.status)}</td>
      <td><div style="display:flex; align-items:center; gap:8px;"><div class="progress-track" style="width:64px;"><div class="progress-fill" style="width:${d.health}%; background:${healthColor(d.health)};"></div></div><span class="mono" style="font-size:11px; color:var(--text-faint);">${d.health}%</span></div></td>
      <td>${d.vulns>0 ? `<span class="badge high">${d.vulns} found</span>` : `<span class="badge low">none</span>`}</td>
      <td style="color:var(--text-faint); font-size:12px;">${d.seen}</td>
    </tr>`).join('');
}
function renderUsers(){
  const body = document.getElementById('users-body');
  body.innerHTML = users.map(u=>`
    <tr>
      <td><div class="cell-main"><div class="avatar" style="width:30px;height:30px;font-size:11px;">${u.name.split(' ').map(n=>n[0]).join('')}</div><div><div class="cell-title">${u.name}</div><div class="cell-sub" style="font-family:var(--font-body);">${u.email}</div></div></div></td>
      <td><span class="role-badge ${u.role}">${u.role}</span></td>
      <td>${statusPillFor(u.status)}</td>
      <td style="color:var(--text-faint); font-size:12px;">${u.active}</td>
      <td><button class="btn btn-ghost btn-sm" onclick="event.stopPropagation(); showToast('Editing '+'${u.name}')">Edit</button></td>
    </tr>`).join('');
}
function renderReports(){
  const body = document.getElementById('reports-body');
  body.innerHTML = reports.map(r=>`
    <tr>
      <td><div class="cell-main"><div class="cell-icon"><svg class="icon" viewBox="0 0 24 24"><path d="M14 2H6a2 2 0 0 0-2 2v16a2 2 0 0 0 2 2h12a2 2 0 0 0 2-2V8z"/><polyline points="14 2 14 8 20 8"/></svg></div><div class="cell-title">${r.name}</div></div></td>
      <td style="color:var(--text-faint);">${r.period}</td>
      <td class="mono" style="color:var(--text-faint); font-size:12px;">${r.gen}</td>
      <td><span class="badge neutral">${r.fmt}</span></td>
      <td><button class="btn btn-ghost btn-sm" onclick="event.stopPropagation(); showToast('Downloading ${r.name}…')"><svg class="icon" viewBox="0 0 24 24" style="width:13px;height:13px;"><path d="M21 15v4a2 2 0 0 1-2 2H5a2 2 0 0 1-2-2v-4"/><polyline points="7 10 12 15 17 10"/><line x1="12" y1="15" x2="12" y2="3"/></svg>Download</button></td>
    </tr>`).join('');
}
function renderUserActivity(){
  const acts = [
    {name:'Alina', action:'resolved incident INC-0841', time:'4 min ago'},
    {name:'Sara', action:'logged in from new device', time:'22 min ago'},
    {name:'Ayesha', action:'updated firewall rule set', time:'1 hr ago'},
    {name:'Sheikh Rehman', action:'escalated INC-0847', time:'2 hr ago'},
  ];
  document.getElementById('user-activity-list').innerHTML = acts.map(a=>`
    <div class="mini-row">
      <div class="mini-left"><div class="avatar" style="width:28px;height:28px;font-size:10px;">${a.name.split(' ').map(n=>n[0]).join('')}</div>
      <div><div style="font-size:12.5px;"><b>${a.name}</b> ${a.action}</div><div style="font-size:11px; color:var(--text-faint);">${a.time}</div></div></div>
    </div>`).join('');
}

/* ============================= CHARTS ============================= */
Chart.defaults.font.family = "'Inter', sans-serif";
Chart.defaults.color = getComputedStyle(document.body).getPropertyValue('--text-muted') || '#8B96AC';
const gridColor = 'rgba(255,255,255,0.05)';
let chartInstances = [];

function buildCharts(){
  chartInstances.forEach(c=>c.destroy());
  chartInstances = [];
  const muted = getComputedStyle(document.body).getPropertyValue('--text-faint').trim();
  const grid = document.body.getAttribute('data-theme')==='light' ? 'rgba(0,0,0,0.06)' : 'rgba(255,255,255,0.06)';
  const accent = getComputedStyle(document.body).getPropertyValue('--accent').trim();
  const violet = getComputedStyle(document.body).getPropertyValue('--violet').trim();
  const critical = getComputedStyle(document.body).getPropertyValue('--critical').trim();
  const high = getComputedStyle(document.body).getPropertyValue('--high').trim();
  const low = getComputedStyle(document.body).getPropertyValue('--low').trim();

  const baseOpts = {
    responsive:true, maintainAspectRatio:false,
    plugins:{legend:{display:false}, tooltip:{backgroundColor:'#111826', titleColor:'#fff', bodyColor:'#cbd3e1', borderColor:'#2C3850', borderWidth:1, padding:10, boxPadding:4}},
    scales:{ x:{grid:{color:'transparent'}, ticks:{color:muted, font:{size:10.5}}}, y:{grid:{color:grid}, ticks:{color:muted, font:{size:10.5}}} }
  };

  // Threat detection (24h)
  chartInstances.push(new Chart(document.getElementById('chart-threat-detect'), {
    type:'line',
    data:{ labels:['00','02','04','06','08','10','12','14','16','18','20','22'],
      datasets:[
        {label:'Critical', data:[1,0,1,2,1,2,3,4,3,2,3,2], borderColor:critical, backgroundColor:'transparent', tension:.4, borderWidth:2, pointRadius:0},
        {label:'High', data:[3,2,4,3,5,6,5,7,6,8,7,6], borderColor:high, backgroundColor:'transparent', tension:.4, borderWidth:2, pointRadius:0},
        {label:'All alerts', data:[8,6,9,11,14,15,17,22,19,24,20,18], borderColor:accent, backgroundColor:(ctx)=>{const g=ctx.chart.ctx.createLinearGradient(0,0,0,160); g.addColorStop(0,'rgba(51,214,195,0.25)'); g.addColorStop(1,'rgba(51,214,195,0)'); return g;}, tension:.4, borderWidth:2.4, pointRadius:0, fill:true},
      ]},
    options: baseOpts
  }));

  // network activity (dashboard mini)
  chartInstances.push(new Chart(document.getElementById('chart-network'), {
    type:'line',
    data:{ labels:['','','','','','',''],
      datasets:[
        {data:[30,45,28,60,40,72,55], borderColor:accent, backgroundColor:'transparent', tension:.45, borderWidth:2.2, pointRadius:0},
        {data:[20,25,18,32,22,38,30], borderColor:violet, backgroundColor:'transparent', tension:.45, borderWidth:2.2, pointRadius:0},
      ]},
    options:{...baseOpts, scales:{x:{display:false},y:{display:false}}}
  }));

  // device donut
  chartInstances.push(new Chart(document.getElementById('chart-devices'), {
    type:'doughnut',
    data:{ labels:['Online','Idle','Offline'], datasets:[{data:[142,14,8], backgroundColor:[accent, high, muted], borderWidth:0}]},
    options:{ responsive:true, maintainAspectRatio:false, cutout:'72%', plugins:{legend:{display:false}, tooltip:baseOpts.plugins.tooltip} }
  }));

  // threat monitoring timeline
  const timelineEl = document.getElementById('chart-timeline');
  if(timelineEl){
    chartInstances.push(new Chart(timelineEl, {
      type:'bar',
      data:{ labels:['00','02','04','06','08','10','12','14','16','18','20','22'],
        datasets:[
          {label:'Critical', data:[0,0,1,0,1,0,1,2,1,0,1,1], backgroundColor:critical, borderRadius:4, maxBarThickness:16},
          {label:'High', data:[1,1,0,2,1,2,2,3,2,3,2,2], backgroundColor:high, borderRadius:4, maxBarThickness:16},
          {label:'Medium', data:[2,1,2,1,3,2,3,2,3,2,2,1], backgroundColor:document.querySelectorAll('.badge')[0]? getComputedStyle(document.body).getPropertyValue('--medium').trim():'#F2CF52', borderRadius:4, maxBarThickness:16},
        ]},
      options:{...baseOpts, scales:{x:{...baseOpts.scales.x, stacked:true}, y:{...baseOpts.scales.y, stacked:true}}}
    }));
  }

  // devices: network traffic in/out
  const netTraffic = document.getElementById('chart-net-traffic');
  if(netTraffic){
    chartInstances.push(new Chart(netTraffic, {
      type:'line',
      data:{ labels:['00','04','08','12','16','20','24'],
        datasets:[
          {label:'Inbound', data:[120,90,160,220,190,260,180], borderColor:accent, backgroundColor:(ctx)=>{const g=ctx.chart.ctx.createLinearGradient(0,0,0,160); g.addColorStop(0,'rgba(51,214,195,0.22)'); g.addColorStop(1,'rgba(51,214,195,0)'); return g;}, fill:true, tension:.4, borderWidth:2, pointRadius:0},
          {label:'Outbound', data:[60,50,70,110,95,130,88], borderColor:violet, backgroundColor:'transparent', tension:.4, borderWidth:2, pointRadius:0},
        ]},
      options: baseOpts
    }));
  }

  // reports: trend
  const trendEl = document.getElementById('chart-trend');
  if(trendEl){
    chartInstances.push(new Chart(trendEl, {
      type:'line',
      data:{ labels:['Apr','May','Jun','Jul','Aug','Sep'],
        datasets:[
          {label:'Critical', data:[12,18,14,22,19,26], borderColor:critical, backgroundColor:'transparent', tension:.35, borderWidth:2, pointRadius:3, pointBackgroundColor:critical},
          {label:'High', data:[40,38,52,49,60,55], borderColor:high, backgroundColor:'transparent', tension:.35, borderWidth:2, pointRadius:3, pointBackgroundColor:high},
          {label:'Total', data:[180,210,240,260,290,310], borderColor:accent, backgroundColor:'transparent', tension:.35, borderWidth:2.4, pointRadius:3, pointBackgroundColor:accent},
        ]},
      options:{...baseOpts, plugins:{...baseOpts.plugins, legend:{display:true, position:'bottom', labels:{color:muted, boxWidth:8, boxHeight:8, usePointStyle:true, font:{size:11}}}}}
    }));
  }

  // reports: attack categories
  const catEl = document.getElementById('chart-attack-cat');
  if(catEl){
    chartInstances.push(new Chart(catEl, {
      type:'bar',
      data:{ labels:['Phishing','Malware','Intrusion','DDoS','Exfil','Recon'],
        datasets:[{ data:[320,270,190,120,80,140], backgroundColor:[accent,violet,high,critical,low,muted], borderRadius:6, maxBarThickness:22 }]},
      options:{...baseOpts, indexAxis:'y', scales:{x:{grid:{color:grid}, ticks:{color:muted,font:{size:10.5}}}, y:{grid:{color:'transparent'}, ticks:{color:muted,font:{size:10.5}}}}}
    }));
  }
}

/* ============================= NAV / VIEW SWITCH ============================= */
const titles = {
  dashboard:['Dashboard','Wednesday, September 9 · Real-time overview'],
  threats:['Threat Monitoring','8 active threats detected across your environment'],
  incident:['Incident Details','INC-2026-0847 · Data exfiltration'],
  devices:['Devices & Network','164 devices across 6 network segments'],
  reports:['Reports & Analytics','Trends and statistics'],
  users:['User Management','12 users with console access'],
  settings:['Settings','Manage your account and preferences'],
};
function switchView(view){
  document.querySelectorAll('.view').forEach(v=>v.classList.remove('active'));
  document.getElementById('view-'+view).classList.add('active');
  document.querySelectorAll('.nav-item').forEach(n=>n.classList.toggle('active', n.dataset.view===view));
  document.getElementById('topbar-title').textContent = titles[view][0];
  document.getElementById('topbar-sub').textContent = titles[view][1];
  document.getElementById('sidebar').classList.remove('open');
  window.scrollTo(0,0);
  setTimeout(buildCharts, 30);
}
function openIncidentFromThreat(name, sev){
  document.getElementById('inc-title').textContent = name;
  document.getElementById('inc-severity').textContent = sev;
  document.getElementById('inc-severity').className = 'badge '+sev;
  switchView('incident');
}
function filterThreats(el, sev){
  document.querySelectorAll('.chip-filter').forEach(c=>c.classList.remove('active'));
  el.classList.add('active');
  renderThreats(sev);
}
function switchSettingsTab(el, id){
  document.querySelectorAll('.settings-tab').forEach(t=>t.classList.remove('active'));
  document.querySelectorAll('.settings-panel').forEach(p=>p.classList.remove('active'));
  el.classList.add('active');
  document.getElementById(id).classList.add('active');
}

/* ============================= THEME ============================= */
function setTheme(mode){
  document.body.setAttribute('data-theme', mode);
  document.getElementById('theme-opt-dark')?.classList.toggle('selected', mode==='dark');
  document.getElementById('theme-opt-light')?.classList.toggle('selected', mode==='light');
  buildCharts();
}
function toggleTheme(){
  const cur = document.body.getAttribute('data-theme');
  setTheme(cur==='dark' ? 'light' : 'dark');
}

/* ============================= LOGIN FLOW ============================= */
function togglePw(){
  const f = document.getElementById('pw-field');
  f.type = f.type==='password' ? 'text' : 'password';
}
function handleLogin(e){
  e.preventDefault();
  document.getElementById('login-form-wrap').classList.add('hidden');
  document.getElementById('twofa-wrap').classList.remove('hidden');
  return false;
}
function completeLogin(){
  document.getElementById('login-screen').style.opacity = '0';
  document.getElementById('login-screen').style.transition = 'opacity .35s ease';
  setTimeout(()=>{
    document.getElementById('login-screen').classList.add('hidden');
    document.getElementById('app').classList.remove('hidden');
    renderThreats('all'); renderRecentIncidents(); renderDevices(); renderUsers(); renderReports(); renderUserActivity();
    buildCharts();
  }, 350);
}
function doLogout(){
  document.getElementById('app').classList.add('hidden');
  document.getElementById('login-screen').classList.remove('hidden');
  document.getElementById('login-screen').style.opacity = '1';
  document.getElementById('twofa-wrap').classList.add('hidden');
  document.getElementById('login-form-wrap').classList.remove('hidden');
  showToast('Signed out');
}
// auto-advance 2FA inputs
document.addEventListener('input', function(e){
  if(e.target.classList.contains('tfa-in')){
    if(e.target.value.length===1){
      const next = e.target.nextElementSibling;
      if(next && next.classList.contains('tfa-in')) next.focus();
    }
  }
});

/* ============================= MODAL / TOAST ============================= */
function openModal(id){ document.getElementById(id).classList.remove('hidden'); }
function closeModal(id){ document.getElementById(id).classList.add('hidden'); }
let toastTimer;
function showToast(msg){
  const t = document.getElementById('toast');
  document.getElementById('toast-msg').textContent = msg;
  t.classList.add('show');
  clearTimeout(toastTimer);
  toastTimer = setTimeout(()=>t.classList.remove('show'), 2600);
}
</script>
</body>
</html>
