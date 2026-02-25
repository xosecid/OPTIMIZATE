/* script.js — lógica del quiz adaptada al nuevo layout
   Mantiene 9 preguntas, scoring y lead capture.
*/

// ====== Preguntas y resultados (mismo mapping que antes) ======
const questions = [
  { q: "¿Cuál es tu edad?", options: [
      {t:"18–25", s:[0,0,1,1]},
      {t:"26–35", s:[1,1,1,1]},
      {t:"36–45", s:[1,2,0,1]},
      {t:"46+", s:[2,2,0,0]}
  ]},
  { q: "¿Cómo describirías tu nivel actual de energía?", options: [
      {t:"Alto y estable todo el día", s:[0,0,0,2]},
      {t:"Bien por la mañana, bajo por la tarde", s:[1,1,1,1]},
      {t:"Cansancio constante", s:[2,2,0,0]},
      {t:"Dependo de café/estimulantes", s:[1,2,1,0]}
  ]},
  { q: "¿Qué te preocupa más actualmente?", options: [
      {t:"Pérdida de masa muscular", s:[2,0,1,1]},
      {t:"Grasa abdominal", s:[1,1,1,1]},
      {t:"Estrés y falta de concentración", s:[0,2,0,1]},
      {t:"Piel apagada / acné adulto / arrugas", s:[0,1,2,0]}
  ]},
  { q: "¿Cuántas veces entrenas por semana?", options: [
      {t:"5+ veces", s:[0,0,0,2]},
      {t:"3–4 veces", s:[1,1,1,1]},
      {t:"1–2 veces", s:[1,0,2,0]},
      {t:"No entreno actualmente", s:[2,1,2,0]}
  ]},
  { q: "¿Cómo describirías tu piel?", options: [
      {t:"Grasa / con brotes", s:[0,1,1,0]},
      {t:"Seca / sensible", s:[1,1,1,0]},
      {t:"Mixta", s:[1,1,1,1]},
      {t:"Nunca he usado productos específicos", s:[1,0,2,0]}
  ]},
  { q: "¿Duermes bien?", options: [
      {t:"7–8h profundas", s:[0,0,1,2]},
      {t:"Me cuesta dormir", s:[1,2,0,0]},
      {t:"Duermo pero me levanto cansado", s:[2,1,0,0]},
      {t:"Duermo poco por trabajo", s:[1,2,1,0]}
  ]},
  { q: "¿Tu nivel de estrés?", options: [
      {t:"Bajo", s:[0,0,1,2]},
      {t:"Moderado", s:[1,1,1,1]},
      {t:"Alto", s:[1,2,0,0]},
      {t:"Vivo en modo presión constante", s:[1,2,0,0]}
  ]},
  { q: "¿Usas actualmente suplementos?", options: [
      {t:"Sí, varios", s:[0,0,0,2]},
      {t:"Solo proteína", s:[1,0,1,1]},
      {t:"He probado algunos", s:[1,1,1,1]},
      {t:"Ninguno", s:[1,1,2,0]}
  ]},
  { q: "¿Qué objetivo te define mejor?", options: [
      {t:"Rendimiento máximo", s:[0,0,0,2]},
      {t:"Recuperar mi mejor versión", s:[2,0,1,1]},
      {t:"Empezar a cuidarme en serio", s:[0,0,2,0]},
      {t:"Optimizar cada variable biológica", s:[0,0,0,2]}
  ]}
];

const results = [
  { title:"El Atleta Oxidado", diagnosis:"Has sido consistente en el pasado y ahora la biología no responde igual. La recuperación se alarga, la fuerza y la masa se resienten. No es falta de ganas: es falta de soporte mitocondrial, antiinflamatorio y recuperación efectiva.", supplements:[{name:"Creatina Monohidratada", why:"Recupera fuerza y volumen celular."},{name:"Omega-3 alta pureza", why:"Reduce inflamación y acelera recuperación."},{name:"Magnesio Glicinato", why:"Mejora sueño y función muscular."}], skincare:[{name:"Limpiador facial suave", why:"Elimina sudor y residuos post-entreno."},{name:"Retinol (noche)", why:"Estimula colágeno y combate envejecimiento."}] },
  { title:"El Ejecutivo Estresado", diagnosis:"Estás en modo exigencia constante: cortisol elevado, sueño fragmentado y piel apagada. Hay que calmar el sistema nervioso y recuperar sueño profundo.", supplements:[{name:"Magnesio Glicinato", why:"Disminuye activación nerviosa."},{name:"Omega-3", why:"Reduce inflamación por estrés."},{name:"Nootrópico (L-Teanina)", why:"Foco sin ansiedad."}], skincare:[{name:"Hidratante con SPF", why:"Protección diaria."},{name:"Limpiador suave", why:"Retira polución urbana."}] },
  { title:"El Principiante Consciente", diagnosis:"No necesitas rutinas complejas: simplicidad y consistencia. Empieza por la base y verás resultados rápidos.", supplements:[{name:"Creatina Monohidratada", why:"Base para fuerza."},{name:"Omega-3", why:"Soporte general."},{name:"Magnesio Glicinato", why:"Mejora descanso."}], skincare:[{name:"Limpiador facial", why:"Primer paso obligatorio."},{name:"Hidratante con SPF", why:"Protección diaria."}] },
  { title:"El Biohacker", diagnosis:"Tienes disciplina y conocimiento. Ahora se trata de afinar: mitocondrias, cognición y anti-aging con métricas claras.", supplements:[{name:"Creatina Monohidratada", why:"Rendimiento físico y cognitivo."},{name:"Omega-3 concentrado", why:"Soporte neuronal potente."},{name:"Nootrópico premium", why:"Enfoque sostenido."}], skincare:[{name:"Retinol (noche)", why:"Regeneración celular."},{name:"Hidratante con SPF amplio", why:"Protección anti-aging."}] }
];

// ===== State =====
let current = 0;
const answers = new Array(questions.length).fill(null);

// ===== DOM =====
const startBtn = document.getElementById('startBtn');
const startBtnHeader = document.getElementById('startBtnHeader');
const quizArea = document.getElementById('quizArea');
const questionCard = document.getElementById('questionCard');
const progressLabel = document.getElementById('progressLabel');
const progressFill = document.getElementById('progressFill');
const prevBtn = document.getElementById('prevBtn');
const nextBtn = document.getElementById('nextBtn');

const leadSection = document.getElementById('leadSection');
const leadForm = document.getElementById('leadForm');
const emailInput = document.getElementById('emailInput');
const skipEmail = document.getElementById('skipEmail');

const resultSection = document.getElementById('resultSection');
const resultContent = document.getElementById('resultContent');
const downloadBtn = document.getElementById('downloadBtn');
const affiliate1 = document.getElementById('affiliate1');
const affiliate2 = document.getElementById('affiliate2');

const mockFill = document.querySelector('.mock-inner .bar .fill');

// ===== Events =====
startBtn && startBtn.addEventListener('click', startQuiz);
startBtnHeader && startBtnHeader.addEventListener('click', startQuiz);
prevBtn.addEventListener('click', ()=> { if(current>0){ current--; renderQuestion(); }});
nextBtn.addEventListener('click', nextStep);

leadForm && leadForm.addEventListener('submit', (e)=> {
  e.preventDefault();
  const email = (emailInput.value||'').trim();
  if(!validateEmail(email)){ flash('Email inválido'); return; }
  // Replace with backend integration
  console.log('Lead:', email);
  showResult(email);
});
skipEmail && skipEmail.addEventListener('click', ()=> showResult(null));
downloadBtn && downloadBtn.addEventListener('click', downloadResult);

// ===== Functions =====
function startQuiz(){
  // scroll to quiz area & show
  window.scrollTo({ top: 200, behavior:'smooth' });
  quizArea.hidden = false;
  quizArea.setAttribute('aria-hidden','false');
  leadSection.hidden = true;
  resultSection.hidden = true;
  current = 0;
  answers.fill(null);
  renderQuestion();
}

function renderQuestion(){
  const item = questions[current];
  // Update mock device fill for hero (visual)
  const pct = Math.round((current / (questions.length-1)) * 100);
  if(mockFill) mockFill.style.width = `${pct}%`;

  questionCard.innerHTML = `
    <h3>Pregunta ${current+1}. ${item.q}</h3>
    <div class="options" id="options"></div>
  `;

  const optionsDiv = document.getElementById('options');
  item.options.forEach((opt, i) => {
    const div = document.createElement('div');
    div.className = 'option';
    div.tabIndex = 0;
    div.innerText = opt.t;
    if(answers[current] === i) div.classList.add('selected');
    div.addEventListener('click', ()=> {
      answers[current] = i;
      document.querySelectorAll('.option').forEach((el, idx)=> el.classList.toggle('selected', idx===i));
    });
    div.addEventListener('keydown', (e)=> { if(e.key==='Enter') div.click(); });
    optionsDiv.appendChild(div);
  });

  updateProgressUI();
  prevBtn.disabled = current === 0;
  nextBtn.innerText = current === questions.length - 1 ? 'Ver resultado' : 'Siguiente';
  // ensure elements visible
  quizArea.scrollIntoView({behavior:'smooth', block:'center'});
}

function updateProgressUI(){
  const pct = Math.round(((current) / (questions.length-1)) * 100);
  progressLabel.innerText = `Paso ${current+1} de ${questions.length} — ${pct}% completado`;
  progressFill.style.width = `${pct}%`;
}

function nextStep(){
  if(answers[current] === null){
    flash('Selecciona una opción para continuar.');
    return;
  }
  if(current < questions.length - 1){
    current++;
    renderQuestion();
  } else {
    // show lead capture
    quizArea.hidden = true;
    leadSection.hidden = false;
    leadSection.setAttribute('aria-hidden','false');
    leadSection.scrollIntoView({behavior:'smooth', block:'center'});
  }
}

function validateEmail(email){ return /\S+@\S+\.\S+/.test(email); }

function flash(msg){
  const el = document.createElement('div');
  el.innerText = msg;
  el.style.position = 'fixed';
  el.style.left = '50%';
  el.style.top = '18px';
  el.style.transform = 'translateX(-50%)';
  el.style.background = '#0b1220';
  el.style.padding = '10px 14px';
  el.style.border = '1px solid rgba(255,255,255,0.06)';
  el.style.borderRadius = '8px';
  el.style.zIndex = '9999';
  document.body.appendChild(el);
  setTimeout(()=> el.remove(), 1800);
}

// Calculate scoring and show result
function showResult(email){
  leadSection.hidden = true;
  resultSection.hidden = false;
  resultSection.setAttribute('aria-hidden','false');

  const totals = [0,0,0,0];
  answers.forEach((ans, qIdx) => {
    const s = questions[qIdx].options[ans].s;
    for(let i=0;i<4;i++) totals[i]+= s[i];
  });

  const max = Math.max(...totals);
  const idx = totals.indexOf(max);
  const res = results[idx];

  // Build html
  resultContent.innerHTML = `
    <h2 class="result-title">${res.title}</h2>
    <p class="lead">${res.diagnosis}</p>
    <div class="grid">
      <div>
        <h4>Suplementos</h4>
        <ul>
          ${res.supplements.map(s=> `<li><strong>${s.name}</strong> — ${s.why}</li>`).join('')}
        </ul>
      </div>
      <div>
        <h4>Skin-care</h4>
        <ul>
          ${res.skincare.map(s=> `<li><strong>${s.name}</strong> — ${s.why}</li>`).join('')}
        </ul>
      </div>
    </div>
    <p class="small muted">Perfil generado automáticamente — enlaces con afiliado.</p>
  `;

  // Set placeholders for affiliate links (add your urls)
  affiliate1.href = "https://tu-afiliado.example.com/suplementos?utm_source=quiz&utm_profile="+encodeURIComponent(res.title);
  affiliate2.href = "https://tu-afiliado.example.com/skincare?utm_source=quiz&utm_profile="+encodeURIComponent(res.title);

  if(email){
    const p = document.createElement('p');
    p.className = 'small muted';
    p.innerText = `Enviado a ${email}. Revisa tu bandeja de entrada (y spam).`;
    resultContent.appendChild(p);
  }

  resultSection.scrollIntoView({behavior:'smooth', block:'center'});
}

// Simple TXT "PDF"
function downloadResult(){
  const title = resultContent.querySelector('.result-title')?.innerText || 'Diagnostico';
  const diag = resultContent.querySelector('.lead')?.innerText || '';
  const lists = Array.from(resultContent.querySelectorAll('ul')).map(ul=>
    Array.from(ul.querySelectorAll('li')).map(li=> li.innerText).join('\n')
  ).join('\n\n');

  const text = `${title}\n\n${diag}\n\n${lists}\n\nProtocolo de Optimización Biológica`;
  const blob = new Blob([text], {type:'text/plain;charset=utf-8'});
  const url = URL.createObjectURL(blob);
  const a = document.createElement('a');
  a.href = url; a.download = `${title.replace(/\s+/g,'_')}.txt`;
  document.body.appendChild(a); a.click(); a.remove(); URL.revokeObjectURL(url);
}

// Start hidden
quizArea.hidden = true;
leadSection.hidden = true;
resultSection.hidden = true;
