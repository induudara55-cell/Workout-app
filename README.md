 <!DOCTYPE html>
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>Aesthetic + Arm Wrestling Trainer</title>
<style>
  * { box-sizing: border-box; margin: 0; padding: 0; }
  html, body { height: 100%; font-family: -apple-system, system-ui, sans-serif;
    background: #0a0e17; color: #e6edf3; overflow: hidden; }
  #app { display: flex; flex-direction: column; height: 100%; }
  header { padding: 12px 16px; background: linear-gradient(180deg,#131a26,#0a0e17);
    border-bottom: 1px solid #1e2a3a; display: flex; justify-content: space-between; align-items: center; }
  header h1 { font-size: 13px; font-weight: 700; letter-spacing: 1px; color: #7ee0ff; }
  .week-badge { background: #1e2a3a; padding: 6px 12px; border-radius: 20px;
    font-size: 12px; color: #7ee0ff; font-weight: 600; }
  #canvas-wrap { flex: 1; position: relative;
    background: radial-gradient(circle at 50% 40%, #1a2436 0%, #0a0e17 70%); }
  canvas { display: block; width: 100%; height: 100%; }
  .ex-name { position: absolute; top: 12px; left: 12px; font-size: 13px;
    color: #7ee0ff; background: rgba(10,14,23,.8); padding: 8px 12px;
    border-radius: 8px; border: 1px solid #1e2a3a; font-weight: 600; }
  .reps-info { position: absolute; top: 12px; right: 12px; font-size: 11px;
    color: #b8c7da; background: rgba(10,14,23,.8); padding: 8px 12px;
    border-radius: 8px; border: 1px solid #1e2a3a; text-align: right; }
  .reps-info .big { font-size: 22px; color: #7ee0ff; font-weight: 800;
    font-variant-numeric: tabular-nums; }
  .reps-info .diff { font-size: 10px; color: #ffb454; text-transform: uppercase;
    letter-spacing: 1px; margin-top: 2px; }
  .timer-overlay { position: absolute; inset: 0; display: none; align-items: center;
    justify-content: center; background: rgba(10,14,23,.9); z-index: 10;
    flex-direction: column; gap: 16px; }
  .timer-overlay.show { display: flex; }
  .timer-big { font-size: 96px; font-weight: 900; color: #7ee0ff;
    font-variant-numeric: tabular-nums; line-height: 1; }
  .timer-label { font-size: 14px; color: #b8c7da; text-transform: uppercase;
    letter-spacing: 4px; }
  footer { padding: 8px 8px 14px; background: #131a26; border-top: 1px solid #1e2a3a; }
  .days { display: flex; gap: 6px; overflow-x: auto; padding: 4px 8px 8px;
    scrollbar-width: none; }
  .days::-webkit-scrollbar { display: none; }
  .day-btn { flex: 0 0 auto; padding: 8px 14px; background: #1a2436;
    border: 1px solid #26344a; border-radius: 8px; color: #b8c7da;
    font-size: 12px; cursor: pointer; white-space: nowrap; font-weight: 600; }
  .day-btn.active { background: #1e3a52; border-color: #7ee0ff; color: #7ee0ff; }
  .day-btn.rest { opacity: .45; }
  .ex-list { display: flex; gap: 6px; overflow-x: auto; padding: 4px 8px 8px;
    scrollbar-width: none; min-height: 42px; }
  .ex-list::-webkit-scrollbar { display: none; }
  .ex-chip { flex: 0 0 auto; padding: 8px 12px; background: #1a2436;
    border: 1px solid #26344a; border-radius: 8px; color: #b8c7da;
    font-size: 11px; cursor: pointer; white-space: nowrap; }
  .ex-chip.active { background: #1e3a52; border-color: #7ee0ff; color: #7ee0ff; }
  .ex-chip.done { background: #16301f; border-color: #4ade80; color: #4ade80; }
  .set-tracker { display: flex; gap: 8px; justify-content: center; padding: 6px 8px; }
  .set-dot { width: 34px; height: 34px; border-radius: 50%;
    border: 2px solid #26344a; background: transparent; display: flex;
    align-items: center; justify-content: center; font-size: 11px;
    color: #6b7d94; cursor: pointer; font-weight: 700; transition: .2s; }
  .set-dot.done { background: #4ade80; border-color: #4ade80; color: #0a0e17; }
  .controls { display: flex; gap: 8px; padding: 6px 8px; align-items: center;
    justify-content: center; }
  .btn { padding: 10px 16px; background: #1e3a52; color: #7ee0ff;
    border: 1px solid #2a5878; border-radius: 8px; font-size: 13px;
    cursor: pointer; font-weight: 700; transition: .15s; }
  .btn:active { transform: scale(.96); }
  .btn.primary { background: #7ee0ff; color: #0a0e17; border-color: #7ee0ff; }
  .btn.ghost { background: transparent; color: #b8c7da; border-color: #26344a; }
  .info-bar { display: flex; justify-content: space-between; padding: 6px 16px 0;
    font-size: 10px; color: #6b7d94; text-transform: uppercase; letter-spacing: 1px; }
  .info-bar span { color: #7ee0ff; }
  .empty { color: #6b7d94; font-size: 12px; padding: 20px; text-align: center; }
</style>
</head>
<body>
<div id="app">
  <header>
    <h1>⚡ AESTHETIC + ARM WRESTLING</h1>
    <div class="week-badge" id="weekBadge">Week 1</div>
  </header>
  <div id="canvas-wrap">
    <canvas id="canvas"></canvas>
    <div class="ex-name" id="exName">—</div>
    <div class="reps-info" id="repsInfo">
      <div class="big" id="repsBig">—</div>
      <div class="diff" id="diffLbl">—</div>
    </div>
    <div class="timer-overlay" id="timerOverlay">
      <div class="timer-label">REST</div>
      <div class="timer-big" id="timerBig">60</div>
      <button class="btn ghost" onclick="skipRest()">Skip Rest</button>
    </div>
  </div>
  <footer>
    <div class="days" id="daysBar"></div>
    <div class="ex-list" id="exList"></div>
    <div class="set-tracker" id="setTracker"></div>
    <div class="controls">
      <button class="btn ghost" onclick="prevEx()">‹</button>
      <button class="btn primary" onclick="completeSet()">SET DONE</button>
      <button class="btn ghost" onclick="nextEx()">›</button>
    </div>
    <div class="info-bar">
      <div>Week <span id="weekInfo">1</span>/24</div>
      <div>Diff: <span id="diffInfo">Bodyweight</span></div>
      <div><span id="progressInfo">0</span>% done</div>
    </div>
  </footer>
</div>

<script src="https://cdn.jsdelivr.net/npm/three@0.140.0/build/three.min.js"></script>
<script>
// ============ PLAN ============
const PLAN = {
  Mon: { name:'Push + Neck', exercises:[
    {id:'neck_ext', name:'Neck Extensions', min:10, max:15, sets:3, anim:'neck_ext'},
    {id:'neck_flex', name:'Neck Flexion', min:10, max:12, sets:3, anim:'neck_flex'},
    {id:'pushup', name:'Push-ups', min:8, max:15, sets:3, anim:'pushup'},
    {id:'pike', name:'Pike Push-ups', min:6, max:12, sets:3, anim:'pike'},
    {id:'decline', name:'Decline Push-ups', min:8, max:15, sets:3, anim:'decline'},
    {id:'lat_raise', name:'Lateral Raises', min:12, max:20, sets:3, anim:'lat_raise'},
    {id:'diamond', name:'Diamond Push-ups', min:6, max:12, sets:2, anim:'diamond'},
  ]},
  Tue: { name:'Pull + Arm Wrestling', exercises:[
    {id:'shrug', name:'Trap Shrugs', min:12, max:20, sets:3, anim:'shrug'},
    {id:'rows', name:'Backpack Rows', min:8, max:15, sets:3, anim:'row'},
    {id:'pullups', name:'Pull-ups', min:5, max:12, sets:3, anim:'pullup'},
    {id:'hammer', name:'Hammer Curls', min:10, max:15, sets:3, anim:'curl'},
    {id:'reardelt', name:'Rear-Delt Fly', min:12, max:20, sets:3, anim:'reardelt'},
    {id:'wrist', name:'Wrist Curls', min:15, max:20, sets:2, anim:'wrist'},
    {id:'rwrist', name:'Reverse Wrist Curls', min:15, max:20, sets:2, anim:'rwrist'},
    {id:'grip', name:'Grip Hold', min:20, max:30, sets:2, unit:'sec', anim:'grip'},
  ]},
  Wed: { name:'Legs + Core', exercises:[
    {id:'squat', name:'Squats', min:12, max:20, sets:3, anim:'squat'},
    {id:'rlunge', name:'Reverse Lunges', min:8, max:12, sets:3, anim:'lunge', perSide:'/ leg'},
    {id:'bulgarian', name:'Bulgarian Split Squat', min:8, max:12, sets:2, anim:'lunge', perSide:'/ leg'},
    {id:'calf', name:'Calf Raises', min:15, max:25, sets:3, anim:'calf'},
    {id:'rcrunch', name:'Reverse Crunch', min:10, max:15, sets:3, anim:'crunch'},
    {id:'plank', name:'Plank', min:30, max:60, sets:3, unit:'sec', anim:'plank'},
    {id:'dragon', name:'Dragon Flag', min:5, max:10, sets:3, anim:'dragon'},
  ]},
  Thu: { name:'Rest / Mobility', rest:true, exercises:[] },
  Fri: { name:'Upper Body + Neck', exercises:[
    {id:'neck_ext', name:'Neck Extensions', min:10, max:15, sets:3, anim:'neck_ext'},
    {id:'neck_flex', name:'Neck Flexion', min:10, max:12, sets:3, anim:'neck_flex'},
    {id:'pushup', name:'Push-ups', min:8, max:15, sets:3, anim:'pushup'},
    {id:'rows', name:'Backpack Rows', min:8, max:15, sets:3, anim:'row'},
    {id:'pike', name:'Pike Push-ups', min:6, max:12, sets:3, anim:'pike'},
    {id:'decline', name:'Decline Push-ups', min:8, max:15, sets:3, anim:'decline'},
    {id:'lat_raise', name:'Lateral Raises', min:12, max:20, sets:3, anim:'lat_raise'},
    {id:'bcurl', name:'Backpack Curls', min:10, max:15, sets:3, anim:'curl'},
    {id:'diamond', name:'Diamond Push-ups', min:6, max:12, sets:2, anim:'diamond'},
  ]},
  Sat: { name:'Legs + Core + AW', exercises:[
    {id:'shrug', name:'Trap Shrugs', min:12, max:20, sets:3, anim:'shrug'},
    {id:'squat', name:'Squats', min:12, max:20, sets:3, anim:'squat'},
    {id:'rlunge', name:'Reverse Lunges', min:8, max:12, sets:3, anim:'lunge', perSide:'/ leg'},
    {id:'bulgarian', name:'Bulgarian Split Squat', min:8, max:12, sets:2, anim:'lunge', perSide:'/ leg'},
    {id:'calf', name:'Calf Raises', min:15, max:25, sets:3, anim:'calf'},
    {id:'bicycle', name:'Bicycle Crunches', min:12, max:20, sets:3, anim:'bicycle', perSide:'/ side'},
    {id:'plank', name:'Plank', min:30, max:60, sets:3, unit:'sec', anim:'plank'},
    {id:'dragon', name:'Dragon Flag', min:5, max:10, sets:3, anim:'dragon'},
    {id:'wrist', name:'Wrist Curls', min:15, max:20, sets:2, anim:'wrist'},
    {id:'rwrist', name:'Reverse Wrist Curls', min:15, max:20, sets:2, anim:'rwrist'},
    {id:'grip', name:'Grip Hold', min:20, max:30, sets:2, unit:'sec', anim:'grip'},
    {id:'hammer', name:'Hammer Curls', min:10, max:15, sets:2, anim:'curl'},
  ]},
  Sun: { name:'Rest', rest:true, exercises:[] }
};
const DAY_ORDER = ['Mon','Tue','Wed','Thu','Fri','Sat','Sun'];
const DAY_LABEL = {Mon:'MON',Tue:'TUE',Wed:'WED',Thu:'THU',Fri:'FRI',Sat:'SAT',Sun:'SUN'};

// ============ STATE ============
const state = {
  day: 'Mon',
  week: 1,
  exIndex: 0,
  completed: JSON.parse(localStorage.getItem('wk_completed') || '{}'),
  restTimerId: null,
  restRemaining: 0
};

function saveCompleted() {
  localStorage.setItem('wk_completed', JSON.stringify(state.completed));
}
function setKey(day, week, exId) { return `${day}_${week}_${exId}`; }

// ============ PROGRESSION ============
function getProgression(ex, week) {
  const span = ex.max - ex.min + 1;
  const cycle = Math.floor((week - 1) / span);
  const pos = (week - 1) % span;
  const reps = ex.min + pos;
  let diff = 'Bodyweight';
  if (cycle === 1) diff = '+ Backpack';
  else if (cycle === 2) diff = 'Harder Variation';
  else if (cycle >= 3) diff = 'Max Resistance';
  return { reps, diff, cycle };
}

// ============ 3D SCENE ============
const canvas = document.getElementById('canvas');
const renderer = new THREE.WebGLRenderer({canvas, antialias:true, alpha:true});
renderer.setPixelRatio(Math.min(devicePixelRatio, 2));
const scene = new THREE.Scene();
const camera = new THREE.PerspectiveCamera(42, 1, 0.1, 100);
camera.position.set(1.6, 1.2, 2.6);
camera.lookAt(0.4, 0.7, 0);
scene.add(new THREE.HemisphereLight(0x88bbff, 0x223344, 1.3));
const dir = new THREE.DirectionalLight(0xffffff, 1.1);
dir.position.set(2, 4, 3); scene.add(dir);
const dir2 = new THREE.DirectionalLight(0x7ee0ff, 0.5);
dir2.position.set(-3, 2, -2); scene.add(dir2);

// Ground
const ground = new THREE.Mesh(
  new THREE.CircleGeometry(5, 40),
  new THREE.MeshStandardMaterial({color:0x1a2436, transparent:true, opacity:0.35})
);
ground.rotation.x = -Math.PI/2; ground.position.y = -0.005; scene.add(ground);

// Body materials
const BODY = new THREE.MeshStandardMaterial({color:0x7ee0ff, roughness:0.45, metalness:0.35, emissive:0x1a3a52, emissiveIntensity:0.5});
const HEAD = new THREE.MeshStandardMaterial({color:0xfff0d0, roughness:0.35, metalness:0.15, emissive:0x3a2a1a, emissiveIntensity:0.3});
const ACCENT = new THREE.MeshStandardMaterial({color:0xffb454, roughness:0.4, metalness:0.4, emissive:0x553300, emissiveIntensity:0.4});

// Pivot (for whole-body rotations)
const pivot = new THREE.Group(); scene.add(pivot);
const bodyRoot = new THREE.Group(); pivot.add(bodyRoot);

// Pelvis
const pelvis = new THREE.Group(); pelvis.position.y = 0.9; bodyRoot.add(pelvis);

// Torso
const torso = new THREE.Mesh(new THREE.BoxGeometry(0.42, 0.55, 0.22), BODY);
torso.position.y = 0.275; pelvis.add(torso);
// Chest accent
const chest = new THREE.Mesh(new THREE.BoxGeometry(0.36, 0.20, 0.24), ACCENT);
chest.position.set(0, 0.38, 0.01); pelvis.add(chest);

// Neck
const neckGroup = new THREE.Group(); neckGroup.position.y = 0.55; pelvis.add(neckGroup);
const neckMesh = new THREE.Mesh(new THREE.CylinderGeometry(0.065, 0.075, 0.14, 12), BODY);
neckMesh.position.y = 0.07; neckGroup.add(neckMesh);

// Head
const headGroup = new THREE.Group(); headGroup.position.y = 0.14; neckGroup.add(headGroup);
const headMesh = new THREE.Mesh(new THREE.SphereGeometry(0.13, 20, 20), HEAD);
headGroup.add(headMesh);
// Face
const face = new THREE.Mesh(new THREE.BoxGeometry(0.06, 0.03, 0.02), new THREE.MeshStandardMaterial({color:0x1a2436}));
face.position.set(0, 0.02, 0.13); headGroup.add(face);

// Arms
function makeArm(side) {
  const shoulder = new THREE.Group();
  shoulder.position.set(side*0.235, 0.5, 0); pelvis.add(shoulder);
  const upperGeo = new THREE.CylinderGeometry(0.052, 0.046, 0.28, 10);
  upperGeo.translate(0, -0.14, 0);
  shoulder.add(new THREE.Mesh(upperGeo, BODY));
  const elbow = new THREE.Group(); elbow.position.y = -0.28; shoulder.add(elbow);
  const lowerGeo = new THREE.CylinderGeometry(0.042, 0.036, 0.26, 10);
  lowerGeo.translate(0, -0.13, 0);
  elbow.add(new THREE.Mesh(lowerGeo, BODY));
  const hand = new THREE.Mesh(new THREE.SphereGeometry(0.052, 10, 10), ACCENT);
  hand.position.y = -0.28; elbow.add(hand);
  return {shoulder, elbow, hand, baseX: side*0.235};
}
const leftArm = makeArm(-1);
const rightArm = makeArm(1);

// Legs
function makeLeg(side) {
  const hip = new THREE.Group();
  hip.position.set(side*0.10, 0, 0); pelvis.add(hip);
  const thighGeo = new THREE.CylinderGeometry(0.072, 0.062, 0.42, 10);
  thighGeo.translate(0, -0.21, 0);
  hip.add(new THREE.Mesh(thighGeo, BODY));
  const knee = new THREE.Group(); knee.position.y = -0.42; hip.add(knee);
  const shinGeo = new THREE.CylinderGeometry(0.056, 0.046, 0.42, 10);
  shinGeo.translate(0, -0.21, 0);
  knee.add(new THREE.Mesh(shinGeo, BODY));
  const foot = new THREE.Mesh(new THREE.BoxGeometry(0.09, 0.055, 0.20), ACCENT);
  foot.position.set(0, -0.42, 0.05); knee.add(foot);
  return {hip, knee, foot, baseX: side*0.10};
}
const leftLeg = makeLeg(-1);
const rightLeg = makeLeg(1);

// ============ POSE RESET ============
const BASE_PELVIS_Y = 0.9;
function resetPose() {
  pivot.position.set(0, 0, 0);
  pivot.quaternion.set(0, 0, 0, 1);
  pelvis.position.set(0, BASE_PELVIS_Y, 0);
  pelvis.rotation.set(0, 0, 0);
  torso.rotation.set(0, 0, 0);
  neckGroup.rotation.set(0, 0, 0);
  headGroup.rotation.set(0, 0, 0);
  for (const arm of [leftArm, rightArm]) {
    arm.shoulder.position.set(arm.baseX, 0.5, 0);
    arm.shoulder.rotation.set(0, 0, 0);
    arm.elbow.rotation.set(0, 0, 0);
  }
  for (const leg of [leftLeg, rightLeg]) {
    leg.hip.position.set(leg.baseX, 0, 0);
    leg.hip.rotation.set(0, 0, 0);
    leg.knee.rotation.set(0, 0, 0);
  }
}

// ============ PRONE ============
const ARM_LEN = 0.54;
const FEET_LOCAL_Y = 0.035;
function proneTiltFromArm(armLen) {
  return Math.asin(armLen / 1.365);
}
function applyProne(theta) {
  const c = Math.cos(theta), s = Math.sin(theta);
  // R = [0 c s; 0 s -c; -1 0 0]
  const m = new THREE.Matrix4();
  m.set(
    0, c, s, 0,
    0, s, -c, 0,
    -1, 0, 0, 0,
    0, 0, 0, 1
  );
  pivot.quaternion.setFromRotationMatrix(m);
  pivot.position.y = -FEET_LOCAL_Y * Math.sin(theta);
  pivot.position.x = -0.7; // center the prone body
}
function proneArms(theta) {
  const sh = theta - Math.PI/2;
  leftArm.shoulder.rotation.x = sh;
  rightArm.shoulder.rotation.x = sh;
}

// ============ ANIMATIONS ============
const TAU = Math.PI * 2;
function osc(t, speed) { return (Math.sin(t*speed) * 0.5) + 0.5; }

const ANIM = {
  neck_ext(t) {
    const a = osc(t, 1.4);
    neckGroup.rotation.x = -a * 0.75;
    headGroup.rotation.x = -a * 0.30;
  },
  neck_flex(t) {
    const a = osc(t, 1.4);
    neckGroup.rotation.x = a * 0.55;
    headGroup.rotation.x = a * 0.25;
  },
  shrug(t) {
    const a = osc(t, 1.8);
    const lift = a * 0.14;
    leftArm.shoulder.position.y = 0.5 + lift;
    rightArm.shoulder.position.y = 0.5 + lift;
    pelvis.position.y = BASE_PELVIS_Y + lift * 0.25;
  },
  pushup(t) {
    const phase = osc(t, 1.2);
    const theta = proneTiltFromArm(ARM_LEN);
    applyProne(theta);
    proneArms(theta);
    const bend = phase * 1.35;
    leftArm.elbow.rotation.x = -bend;
    rightArm.elbow.rotation.x = -bend;
    pivot.position.y -= phase * 0.22;
    // Slight leg bend
    leftLeg.knee.rotation.x = 0.05;
    rightLeg.knee.rotation.x = 0.05;
  },
  pike(t) {
    const phase = osc(t, 1.2);
    const theta = 0.75; // more upright
    applyProne(theta);
    proneArms(theta);
    const bend = phase * 1.35;
    leftArm.elbow.rotation.x = -bend;
    rightArm.elbow.rotation.x = -bend;
    // Bend hips forward for pike
    leftLeg.hip.rotation.x = -0.6;
    rightLeg.hip.rotation.x = -0.6;
    pivot.position.y -= phase * 0.15;
  },
  decline(t) {
    // Like pushup but feet elevated
    const phase = osc(t, 1.2);
    const theta = proneTiltFromArm(ARM_LEN) + 0.35;
    applyProne(theta);
    proneArms(theta);
    const bend = phase * 1.35;
    leftArm.elbow.rotation.x = -bend;
    rightArm.elbow.rotation.x = -bend;
    pivot.position.y -= phase * 0.22;
  },
  diamond(t) {
    // pushups with arms closer together (bring shoulders in)
    const phase = osc(t, 1.2);
    const theta = proneTiltFromArm(ARM_LEN);
    applyProne(theta);
    proneArms(theta);
    leftArm.shoulder.position.x = -0.08;
    rightArm.shoulder.position.x = 0.08;
    const bend = phase * 1.4;
    leftArm.elbow.rotation.x = -bend;
    rightArm.elbow.rotation.x = -bend;
    pivot.position.y -= phase * 0.20;
  },
  lat_raise(t) {
    const a = osc(t, 1.6);
    const angle = a * Math.PI * 0.48;
    leftArm.shoulder.rotation.z = angle;
    rightArm.shoulder.rotation.z = -angle;
    leftArm.elbow.rotation.x = -0.15;
    rightArm.elbow.rotation.x = -0.15;
  },
  row(t) {
    const a = osc(t, 1.5);
    // Bend forward slightly
    pelvis.rotation.x = 0.35;
    // Arms pull back with elbow bend
    const bend = a * 1.4;
    leftArm.elbow.rotation.x = -bend;
    rightArm.elbow.rotation.x = -bend;
    // Slight shoulder back
    leftArm.shoulder.rotation.x = -a * 0.3;
    rightArm.shoulder.rotation.x = -a * 0.3;
    // Weight (backpack) in hands — just visual via hand accent already
  },
  pullup(t) {
    const a = osc(t, 1.1);
    // Arms up, then bend to pull body
    const armUp = Math.PI * 0.95;
    leftArm.shoulder.rotation.x = -armUp + a * 0.7;
    rightArm.shoulder.rotation.x = -armUp + a * 0.7;
    leftArm.elbow.rotation.x = -a * 1.6;
    rightArm.elbow.rotation.x = -a * 1.6;
    // Body rises
    pelvis.position.y = BASE_PELVIS_Y + a * 0.15;
    // Legs slightly bent
    leftLeg.knee.rotation.x = 0.15;
    rightLeg.knee.rotation.x = 0.15;
  },
  curl(t) {
    const a = osc(t, 1.8);
    const bend = a * Math.PI * 0.7;
    leftArm.elbow.rotation.x = -bend;
    rightArm.elbow.rotation.x = -bend;
  },
  reardelt(t) {
    const a = osc(t, 1.5);
    // Arms out to sides and back
    leftArm.shoulder.rotation.z = a * Math.PI * 0.5;
    rightArm.shoulder.rotation.z = -a * Math.PI * 0.5;
    leftArm.shoulder.rotation.y = -a * 0.5;
    rightArm.shoulder.rotation.y = a * 0.5;
  },
  wrist(t) {
    const a = osc(t, 2.2);
    // Forearms out front, hand rotates (use hand position or elbow rotation)
    leftArm.shoulder.rotation.x = -Math.PI * 0.55;
    rightArm.shoulder.rotation.x = -Math.PI * 0.55;
    leftArm.elbow.rotation.x = -Math.PI * 0.5;
    rightArm.elbow.rotation.x = -Math.PI * 0.5;
    // Hand "curl" simulated by elbow micro-motion
    leftArm.elbow.rotation.x += a * 0.3;
    rightArm.elbow.rotation.x += a * 0.3;
  },
  rwrist(t) {
    const a = 1 - osc(t, 2.2);
    leftArm.shoulder.rotation.x = -Math.PI * 0.55;
    rightArm.shoulder.rotation.x = -Math.PI * 0.55;
    leftArm.elbow.rotation.x = -Math.PI * 0.5;
    rightArm.elbow.rotation.x = -Math.PI * 0.5;
    leftArm.elbow.rotation.x += a * 0.3;
    rightArm.elbow.rotation.x += a * 0.3;
  },
  grip(t) {
    // Static hold — arms forward, slight grip tension pulse
    const a = osc(t, 4.0);
    leftArm.shoulder.rotation.x = -Math.PI * 0.5;
    rightArm.shoulder.rotation.x = -Math.PI * 0.5;
    leftArm.elbow.rotation.x = -Math.PI * 0.3 + a * 0.1;
    rightArm.elbow.rotation.x = -Math.PI * 0.3 + a * 0.1;
    pelvis.position.y = BASE_PELVIS_Y - 0.05;
  },
  squat(t) {
    const a = osc(t, 1.3);
    const depth = a * 0.55;
    pelvis.position.y = BASE_PELVIS_Y - depth;
    leftLeg.hip.rotation.x = -a * 1.0;
    rightLeg.hip.rotation.x = -a * 1.0;
    leftLeg.knee.rotation.x = a * 1.4;
    rightLeg.knee.rotation.x = a * 1.4;
    // Slight arm forward for balance
    leftArm.shoulder.rotation.x = -a * 0.6;
    rightArm.shoulder.rotation.x = -a * 0.6;
  },
  lunge(t) {
    const a = osc(t, 1.1);
    // Right leg back
    rightLeg.hip.rotation.x = a * 1.0;
    rightLeg.knee.rotation.x = -a * 1.0;
    leftLeg.hip.rotation.x = -a * 0.6;
    leftLeg.knee.rotation.x = a * 1.0;
    pelvis.position.y = BASE_PELVIS_Y - a * 0.32;
    pelvis.position.z = a * 0.15;
  },
  calf(t) {
    const a = osc(t, 2.0);
    pelvis.position.y = BASE_PELVIS_Y + a * 0.10;
    // Feet pitch (heel up)
    leftLeg.foot.rotation.x = -a * 0.5;
    rightLeg.foot.rotation.x = -a * 0.5;
  },
  crunch(t) {
    // Lying on back, knees pull toward chest
    pivot.rotation.set(-Math.PI/2, 0, 0); // lie on back
    pivot.position.y = 0.3;
    pivot.position.z = -0.5;
    const a = osc(t, 1.2);
    leftLeg.hip.rotation.x = -a * 1.5;
    rightLeg.hip.rotation.x = -a * 1.5;
    leftLeg.knee.rotation.x = a * 0.9;
    rightLeg.knee.rotation.x = a * 0.9;
  },
  plank(t) {
    const theta = proneTiltFromArm(ARM_LEN * 0.95);
    applyProne(theta);
    // Forearms on ground (bend elbow so hands tuck under)
    leftArm.shoulder.rotation.x = theta - Math.PI/2;
    rightArm.shoulder.rotation.x = theta - Math.PI/2;
    leftArm.elbow.rotation.x = -Math.PI * 0.5;
    rightArm.elbow.rotation.x = -Math.PI * 0.5;
    // micro pulse
    const p = osc(t, 0.6) * 0.01;
    pivot.position.y += p;
  },
  dragon(t) {
    // Lying on back, body lifts up from shoulders
    pivot.rotation.set(-Math.PI/2, 0, 0);
    pivot.position.y = 0.3;
    pivot.position.z = -0.5;
    const a = osc(t, 0.9);
    pivot.rotation.x = -Math.PI/2 + a * 0.9;
    // Legs together extended
    leftLeg.hip.rotation.x = 0.05;
    rightLeg.hip.rotation.x = 0.05;
  },
  bicycle(t) {
    pivot.rotation.set(-Math.PI/2, 0, 0);
    pivot.position.y = 0.3;
    pivot.position.z = -0.5;
    const a = osc(t, 1.6);
    leftLeg.hip.rotation.x = -a * 1.3;
    rightLeg.hip.rotation.x = -(1 - a) * 1.3;
    leftLeg.knee.rotation.x = a * 1.0;
    rightLeg.knee.rotation.x = (1 - a) * 1.0;
    // torso twist
    torso.rotation.y = (a - 0.5) * 0.4;
  },
};

// ============ ANIMATION LOOP ============
let lastT = performance.now();
let clockT = 0;
function loop(now) {
  const dt = Math.min((now - lastT)/1000, 0.1);
  lastT = now;
  clockT += dt;

  resetPose();
  const ex = currentExercise();
  if (ex && ANIM[ex.anim]) {
    ANIM[ex.anim](clockT);
  } else if (ex) {
    // Fallback: gentle idle
    const a = osc(clockT, 1.0);
    pelvis.position.y = BASE_PELVIS_Y + a * 0.02;
  }

  renderer.render(scene, camera);
  requestAnimationFrame(loop);
}

function resize() {
  const wrap = document.getElementById('canvas-wrap');
  const w = wrap.clientWidth, h = wrap.clientHeight;
  renderer.setSize(w, h, false);
  camera.aspect = w / h;
  camera.updateProjectionMatrix();
}
window.addEventListener('resize', resize);
resize();
requestAnimationFrame(loop);

// ============ UI LOGIC ============
function currentExercise() {
  const p = PLAN[state.day];
  if (!p || p.rest || !p.exercises.length) return null;
  if (state.exIndex >= p.exercises.length) state.exIndex = 0;
  return p.exercises[state.exIndex];
}

function renderDays() {
  const bar = document.getElementById('daysBar');
  bar.innerHTML = '';
  DAY_ORDER.forEach(d => {
    const b = document.createElement('div');
    b.className = 'day-btn' + (state.day === d ? ' active' : '') +
      (PLAN[d].rest ? ' rest' : '');
    b.textContent = DAY_LABEL[d];
    b.onclick = () => { state.day = d; state.exIndex = 0; renderAll(); };
    bar.appendChild(b);
  });
}

function renderExList() {
  const list = document.getElementById('exList');
  const p = PLAN[state.day];
  list.innerHTML = '';
  if (p.rest) {
    const e = document.createElement('div');
    e.className = 'empty';
    e.textContent = 'Rest day — recovery, mobility, stretching.';
    list.appendChild(e);
    return;
  }
  p.exercises.forEach((ex, i) => {
    const c = document.createElement('div');
    const doneSets = state.completed[setKey(state.day, state.week, ex.id)] || 0;
    c.className = 'ex-chip' + (i === state.exIndex ? ' active' : '') +
      (doneSets >= ex.sets ? ' done' : '');
    c.textContent = ex.name;
    c.onclick = () => { state.exIndex = i; renderAll(); };
    list.appendChild(c);
  });
}

function renderSetTracker() {
  const t = document.getElementById('setTracker');
  t.innerHTML = '';
  const ex = currentExercise();
  if (!ex) return;
  const done = state.completed[setKey(state.day, state.week, ex.id)] || 0;
  for (let i = 0; i < ex.sets; i++) {
    const d = document.createElement('div');
    d.className = 'set-dot' + (i < done ? ' done' : '');
    d.textContent = i + 1;
    d.onclick = () => {
      const k = setKey(state.day, state.week, ex.id);
      state.completed[k] = (i + 1 === done) ? i : i + 1;
      saveCompleted();
      renderAll();
    };
    t.appendChild(d);
  }
}

function renderInfo() {
  const ex = currentExercise();
  const p = PLAN[state.day];
  document.getElementById('weekBadge').textContent = 'Week ' + state.week;
  document.getElementById('weekInfo').textContent = state.week;
  document.getElementById('exName').textContent = p.name || '—';

  if (!ex) {
    document.getElementById('repsBig').textContent = 'REST';
    document.getElementById('diffLbl').textContent = 'Recovery';
    document.getElementById('diffInfo').textContent = '—';
    document.getElementById('repsInfo').style.display = 'block';
    document.getElementById('progressInfo').textContent = '100';
    return;
  }
  const prog = getProgression(ex, state.week);
  const unit = ex.unit === 'sec' ? 's' : '';
  const per = ex.perSide ? ' ' + ex.perSide : '';
  document.getElementById('repsBig').textContent = `${ex.sets} × ${prog.reps}${unit}${per}`;
  document.getElementById('diffLbl').textContent = prog.diff;
  document.getElementById('diffInfo').textContent = prog.diff;

  // progress
  let totalSets = 0, doneSets = 0;
  p.exercises.forEach(e => {
    totalSets += e.sets;
    const d = state.completed[setKey(state.day, state.week, e.id)] || 0;
    doneSets += Math.min(d, e.sets);
  });
  document.getElementById('progressInfo').textContent =
    totalSets ? Math.round(doneSets / totalSets * 100) : 0;
}

function renderAll() {
  renderDays();
  renderExList();
  renderSetTracker();
  renderInfo();
  renderExList(); // re-render to update active/done
}

// ============ CONTROLS ============
function nextEx() {
  const p = PLAN[state.day];
  if (!p || p.rest) { nextDay(); return; }
  state.exIndex = (state.exIndex + 1) % p.exercises.length;
  renderAll();
}
function prevEx() {
  const p = PLAN[state.day];
  if (!p || p.rest) { prevDay(); return; }
  state.exIndex = (state.exIndex - 1 + p.exercises.length) % p.exercises.length;
  renderAll();
}
function nextDay() {
  const i = DAY_ORDER.indexOf(state.day);
  state.day = DAY_ORDER[(i + 1) % DAY_ORDER.length];
  state.exIndex = 0;
  renderAll();
}
function prevDay() {
  const i = DAY_ORDER.indexOf(state.day);
  state.day = DAY_ORDER[(i - 1 + DAY_ORDER.length) % DAY_ORDER.length];
  state.exIndex = 0;
  renderAll();
}

function completeSet() {
  const ex = currentExercise();
  if (!ex) { nextDay(); return; }
  const k = setKey(state.day, state.week, ex.id);
  const done = state.completed[k] || 0;
  if (done < ex.sets) {
    state.completed[k] = done + 1;
    saveCompleted();
  }
  // If all sets done for this exercise -> next exercise
  if (state.completed[k] >= ex.sets) {
    const p = PLAN[state.day];
    const allDone = p.exercises.every(e =>
      (state.completed[setKey(state.day, state.week, e.id)] || 0) >= e.sets
    );
    if (allDone) {
      // advance week if all done
      if (state.week < 24) {
        state.week++;
        // clear completion for new week
        state.exIndex = 0;
      }
    } else {
      state.exIndex = (state.exIndex + 1) % p.exercises.length;
    }
  } else {
    // start rest timer between sets
    startRest(60);
  }
  renderAll();
}

// ============ REST TIMER ============
function startRest(seconds) {
  const overlay = document.getElementById('timerOverlay');
  const big = document.getElementById('timerBig');
  state.restRemaining = seconds;
  big.textContent = seconds;
  overlay.classList.add('show');
  if (state.restTimerId) clearInterval(state.restTimerId);
  state.restTimerId = setInterval(() => {
    state.restRemaining--;
    big.textContent = state.restRemaining;
    if (state.restRemaining <= 0) {
      skipRest();
    }
  }, 1000);
}
function skipRest() {
  const overlay = document.getElementById('timerOverlay');
  overlay.classList.remove('show');
  if (state.restTimerId) clearInterval(state.restTimerId);
  state.restTimerId = null;
}

// ============ INIT ============
renderAll();

// Long-press to reset a week (for testing)
let pressTimer;
document.getElementById('weekBadge').addEventListener('pointerdown', () => {
  pressTimer = setTimeout(() => {
    const w = parseInt(prompt('Jump to week (1-24):', state.week), 10);
    if (w >= 1 && w <= 24) { state.week = w; renderAll(); }
  }, 800);
});
document.getElementById('weekBadge').addEventListener('pointerup', () => {
  clearTimeout(pressTimer);
});
document.getElementById('weekBadge').addEventListener('pointerleave', () => {
  clearTimeout(pressTimer);
});
</script>
</body>
</html>
