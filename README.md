# AI-Calorie-Tracker
AI Calorie Tracker — веб-приложение для отслеживания калорий с использованием искусственного интеллекта. Позволяет анализировать блюда и продукты, считать калории и следить за рационом в удобном и простом интерфейсе.
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1" />
  <title>AI Calorie Tracker</title>
  <style>
    :root{
      --bg:#0b0c10; --panel:#111319; --muted:#1a1d26; --text:#e6e7eb; --sub:#a4a7b0; --brand:#6ee7b7; --brand-2:#60a5fa; --warn:#f59e0b;
      --ok:#10b981; --err:#ef4444; --shadow:0 10px 25px rgba(0,0,0,.35);
      --radius:18px;
    }
    *{box-sizing:border-box}
    html,body{height:100%}
    body{margin:0;background:radial-gradient(80vw 80vh at 80% -10%,rgba(96,165,250,.08),transparent),
                    radial-gradient(80vw 80vh at -20% 110%,rgba(110,231,183,.06),transparent),var(--bg);
         color:var(--text);font:16px/1.4 system-ui, -apple-system, Segoe UI, Roboto, Ubuntu, Cantarell, "Helvetica Neue", Arial, "Noto Sans", "Apple Color Emoji","Segoe UI Emoji"}
    header{max-width:980px;margin:24px auto 8px;padding:0 16px}
    .title{display:flex;gap:12px;align-items:center}
    .logo{width:40px;height:40px;border-radius:12px;background:linear-gradient(135deg,var(--brand),var(--brand-2));display:grid;place-items:center;box-shadow:var(--shadow)}
    .logo svg{filter:drop-shadow(0 4px 8px rgba(0,0,0,.35))}
    h1{margin:0;font-size:clamp(20px,5vw,28px);letter-spacing:.2px}
    p.subtitle{margin:6px 0 0;color:var(--sub)}

    .app{max-width:980px;margin:18px auto 48px;padding:0 16px;display:grid;grid-template-columns:1.1fr .9fr;gap:18px}
    @media (max-width:860px){.app{grid-template-columns:1fr;}}

    .card{background:linear-gradient(180deg,rgba(255,255,255,.02),rgba(255,255,255,.01));border:1px solid rgba(255,255,255,.08);
          border-radius:var(--radius);box-shadow:var(--shadow)}
    .card > .inner{padding:18px}

    .row{display:flex;gap:12px;align-items:center;flex-wrap:wrap}
    label{font-size:.85rem;color:var(--sub)}
    input[type="password"], input[type="text"]{width:100%;padding:12px 14px;border-radius:12px;border:1px solid rgba(255,255,255,.12);
      background:var(--muted);color:var(--text);outline:none}
    input[type="file"]{display:none}
    .btn{appearance:none;border:0;border-radius:14px;padding:12px 16px;background:linear-gradient(135deg,var(--brand),var(--brand-2));
      color:#0b1220;font-weight:700;cursor:pointer;transition:transform .06s ease, filter .2s ease;box-shadow:0 8px 18px rgba(96,165,250,.25)}
    .btn:active{transform:translateY(1px)}
    .btn.secondary{background:transparent;color:var(--text);border:1px solid rgba(255,255,255,.12);box-shadow:none}
    .switch{display:flex;align-items:center;gap:8px;cursor:pointer}
    .switch input{appearance:none;width:40px;height:24px;background:#2a2e3a;border-radius:20px;position:relative;outline:none;transition:.2s}
    .switch input:checked{background:linear-gradient(135deg,var(--brand),var(--brand-2))}
    .switch input::after{content:"";position:absolute;inset:3px auto 3px 4px;width:18px;border-radius:50%;background:#fff;transition:.2s}
    .switch input:checked::after{left:18px}

    .uploader{display:grid;place-items:center;border:2px dashed rgba(255,255,255,.12);border-radius:14px;min-height:220px;
      background:repeating-linear-gradient( 135deg, rgba(255,255,255,.03), rgba(255,255,255,.03) 8px, rgba(255,255,255,.02) 8px, rgba(255,255,255,.02) 16px );
      text-align:center;padding:20px}
    .uploader.drag{border-color:var(--brand)}
    .uploader .hint{color:var(--sub);font-size:.95rem}
    .uploader .actions{margin-top:12px}

    .preview{display:grid;grid-template-columns:1fr 1fr;gap:12px}
    .preview img{width:100%;border-radius:14px;border:1px solid rgba(255,255,255,.12);object-fit:cover;max-height:260px}
    @media (max-width:640px){.preview{grid-template-columns:1fr}}

    .result-grid{display:grid;grid-template-columns:1fr 1fr;gap:16px}
    @media (max-width:780px){.result-grid{grid-template-columns:1fr}}

    .metrics{display:grid;grid-template-columns:repeat(3,1fr);gap:10px}
    .metric{background:rgba(255,255,255,.02);border:1px solid rgba(255,255,255,.08);border-radius:14px;padding:12px}
    .metric .k{font-size:1.3rem;font-weight:800}
    .metric .l{color:var(--sub);font-size:.8rem}

    .vis{display:grid;place-items:center;padding:10px}
    canvas{width:100%;max-width:380px;height:auto}

    .notes{color:var(--sub);font-size:.95rem}
    .small{font-size:.85rem;color:var(--sub)}
    .footer{max-width:980px;margin:0 auto 60px;padding:0 16px;color:var(--sub);text-align:center}

    .pill{display:inline-flex;gap:8px;align-items:center;padding:6px 10px;border-radius:999px;background:rgba(255,255,255,.05);
      border:1px solid rgba(255,255,255,.08);font-size:.85rem}

    .loader{display:inline-block;width:14px;height:14px;border:2px solid rgba(255,255,255,.4);border-top-color:var(--text);border-radius:50%;animation:spin 1s linear infinite}
    @keyframes spin{to{transform:rotate(360deg)}}

    .error{color:var(--err)}
    .success{color:var(--ok)}
    .muted{color:var(--sub)}
  </style>
</head>
<body>
  <header>
    <div class="title">
      <div class="logo" aria-hidden="true">
        <svg width="22" height="22" viewBox="0 0 24 24" fill="none" xmlns="http://www.w3.org/2000/svg" aria-hidden="true">
          <path d="M12 3c4.97 0 9 4.03 9 9 0 2.21-.81 4.23-2.15 5.79-.41.46-1.12.49-1.58.07-.45-.41-.49-1.11-.08-1.57A6.99 6.99 0 0019 12a7 7 0 10-7 7c1.87 0 3.56-.76 4.78-1.99.43-.44 1.13-.44 1.56 0 .44.43.44 1.13 0 1.56A8.97 8.97 0 0112 21C7.03 21 3 16.97 3 12S7.03 3 12 3z" fill="white"/>
          <path d="M12 7c.55 0 1 .45 1 1v3.17l2.59 2.58a1 1 0 01-1.42 1.42L11 12.59V8c0-.55.45-1 1-1z" fill="white"/>
        </svg>
      </div>
      <div>
        <h1>AI Calorie Tracker</h1>
        <p class="subtitle">Estimate calories and macros from a meal photo — privately, in your browser.</p>
      </div>
    </div>
  </header>

  <main class="app" role="main">
    <!-- Left: Controls -->
    <section class="card" aria-label="Controls and upload">
      <div class="inner" style="display:flex;flex-direction:column;gap:16px">
        <div class="row" style="justify-content:space-between">
          <div style="flex:1;min-width:220px">
            <label for="apiKey">OpenAI API key</label>
            <input id="apiKey" type="password" placeholder="sk-..." autocomplete="off" aria-label="OpenAI API key" />
            <div class="row" style="justify-content:space-between;margin-top:6px">
              <label class="switch small" title="Store key in this browser (localStorage)">
                <input id="rememberKey" type="checkbox" /> <span>Remember on this device</span>
              </label>
              <button class="btn secondary" id="clearKey" type="button">Clear</button>
            </div>
          </div>
        </div>

        <div id="uploader" class="uploader" tabindex="0" role="button" aria-label="Upload meal photo by clicking or dragging a file">
          <div>
            <div class="pill" style="margin-bottom:10px"><span>📷</span> <strong>Upload a meal photo</strong></div>
            <div class="hint">Drag & drop an image here, or</div>
            <div class="actions" style="margin-top:8px">
              <label class="btn" for="file">Choose image</label>
              <input id="file" type="file" accept="image/*" />
            </div>
            <div class="small" style="margin-top:8px">Your image is sent only to OpenAI for analysis. No server in between.</div>
          </div>
        </div>

        <div id="preview" class="preview" hidden>
          <img id="img" alt="Meal photo preview" />
          <div style="display:flex;flex-direction:column;gap:10px">
            <div class="small">Optional details (improves accuracy):</div>
            <input id="notes" type="text" placeholder="e.g., Chicken bowl with rice and avocado, ~500g" />
            <div class="row" style="justify-content:space-between;align-items:center">
              <div class="small muted" id="imgMeta">—</div>
              <button id="analyze" class="btn" type="button">Analyze Photo</button>
            </div>
            <div id="status" class="small muted"></div>
          </div>
        </div>
      </div>
    </section>

    <!-- Right: Results -->
    <section class="card" aria-label="Results">
      <div class="inner" style="display:flex;flex-direction:column;gap:16px">
        <div class="row" style="justify-content:space-between;align-items:center">
          <div class="pill"><span>🧮</span> <strong>Estimated Nutrition</strong></div>
          <div id="confidencePill" class="pill" hidden></div>
        </div>

        <div id="emptyState" class="small" style="text-align:center;color:var(--sub);padding:10px 6px 14px">
          Upload a meal photo to see results here.
        </div>

        <div id="results" class="result-grid" hidden>
          <div>
            <div class="metrics">
              <div class="metric">
                <div class="l">Total Calories</div>
                <div class="k" id="calories">—</div>
              </div>
              <div class="metric">
                <div class="l">Protein</div>
                <div class="k" id="protein">—</div>
              </div>
              <div class="metric">
                <div class="l">Fat</div>
                <div class="k" id="fat">—</div>
              </div>
              <div class="metric">
                <div class="l">Carbs</div>
                <div class="k" id="carbs">—</div>
              </div>
            </div>
            <div class="notes" id="extraNotes" style="margin-top:12px"></div>
          </div>
          <div class="vis">
            <canvas id="chart" width="420" height="420" aria-label="Macros donut chart" role="img"></canvas>
          </div>
        </div>

        <div class="small" style="opacity:.9">⚠️ Estimates are approximate and not medical advice. For allergies or conditions, consult a professional.</div>
      </div>
    </section>
  </main>

  <div class="footer small">
    <span>Made with the OpenAI GPT API. Your key and images stay on this device, only sent directly to OpenAI for analysis.</span>
  </div>

  <script>
    const els = {
      apiKey: document.getElementById('apiKey'),
      remember: document.getElementById('rememberKey'),
      clearKey: document.getElementById('clearKey'),
      file: document.getElementById('file'),
      uploader: document.getElementById('uploader'),
      preview: document.getElementById('preview'),
      img: document.getElementById('img'),
      imgMeta: document.getElementById('imgMeta'),
      notes: document.getElementById('notes'),
      analyze: document.getElementById('analyze'),
      status: document.getElementById('status'),
      emptyState: document.getElementById('emptyState'),
      results: document.getElementById('results'),
      calories: document.getElementById('calories'),
      protein: document.getElementById('protein'),
      fat: document.getElementById('fat'),
      carbs: document.getElementById('carbs'),
      chart: document.getElementById('chart'),
      confidencePill: document.getElementById('confidencePill'),
      extraNotes: document.getElementById('extraNotes'),
    };

    // Persist key (optional)
    (function initKey(){
      const saved = localStorage.getItem('aiCalorieTracker.key');
      if(saved){ els.apiKey.value = saved; els.remember.checked = true; }
      els.remember.addEventListener('change', ()=>{
        if(els.remember.checked && els.apiKey.value) localStorage.setItem('aiCalorieTracker.key', els.apiKey.value);
        else localStorage.removeItem('aiCalorieTracker.key');
      });
      els.apiKey.addEventListener('input', ()=>{
        if(els.remember.checked){ localStorage.setItem('aiCalorieTracker.key', els.apiKey.value); }
      });
      els.clearKey.addEventListener('click', ()=>{
        els.apiKey.value=''; localStorage.removeItem('aiCalorieTracker.key'); els.remember.checked=false;
      });
    })();

    // Drag & drop uploader
    ;['dragenter','dragover'].forEach(ev=>els.uploader.addEventListener(ev, e=>{e.preventDefault(); e.stopPropagation(); els.uploader.classList.add('drag');}));
    ;['dragleave','drop'].forEach(ev=>els.uploader.addEventListener(ev, e=>{e.preventDefault(); e.stopPropagation(); els.uploader.classList.remove('drag');}));
    els.uploader.addEventListener('drop', e=>{ const f = e.dataTransfer.files?.[0]; if(f) onFile(f); });
    els.uploader.addEventListener('click', ()=> els.file.click());
    els.file.addEventListener('change', e=>{ const f = e.target.files?.[0]; if(f) onFile(f); });

    async function onFile(file){
      if(!file || !file.type.startsWith('image/')){ toast('Please select an image file.', true); return; }
      const url = URL.createObjectURL(file);
      els.img.src = url;
      els.preview.hidden = false; els.emptyState.hidden = true; els.results.hidden = true; els.status.textContent='';
      await new Promise(r=> els.img.onload = r);
      const info = `${Math.round(file.size/1024)} KB • ${els.img.naturalWidth}×${els.img.naturalHeight}`;
      els.imgMeta.textContent = info;
      // keep a base64 for API call
      const dataUrl = await fileToDataURL(file);
      els.img.dataset.dataUrl = dataUrl;
    }

    function toast(msg, isError=false){
      els.status.innerHTML = (isError? '❌ ' : '✅ ') + msg;
      els.status.className = 'small ' + (isError? 'error' : 'success');
    }

    function setBusy(b){
      els.analyze.disabled = b; els.analyze.innerHTML = b? '<span class="loader"></span> Analyzing…' : 'Analyze Photo';
    }

    function fileToDataURL(file){
      return new Promise((resolve, reject)=>{
        const reader = new FileReader();
        reader.onload = ()=> resolve(reader.result);
        reader.onerror = reject;
        reader.readAsDataURL(file);
      });
    }

    els.analyze.addEventListener('click', ()=> analyze());

    async function analyze(){
      const key = els.apiKey.value.trim();
      if(!key){ toast('Add your OpenAI API key to continue.', true); return; }
      const imageDataUrl = els.img.dataset.dataUrl;
      if(!imageDataUrl){ toast('Please upload an image first.', true); return; }
      setBusy(true); toast('Sending image to GPT…');
      try{
        const payload = buildOpenAIRequest(imageDataUrl, els.notes.value.trim());
        const res = await fetch('https://api.openai.com/v1/chat/completions',{
          method:'POST',
          headers:{'Content-Type':'application/json','Authorization':`Bearer ${key}`},
          body: JSON.stringify(payload)
        });
        if(!res.ok){
          const text = await res.text();
          throw new Error(`API error ${res.status}: ${text}`);
        }
        const data = await res.json();
        const raw = data?.choices?.[0]?.message?.content || '';
        const parsed = parseJsonSafe(raw);
        if(!parsed){ throw new Error('Could not parse model response.'); }
        renderResults(parsed);
        toast('Analysis complete.');
      }catch(err){ console.error(err); toast(err.message || 'Something went wrong.', true); }
      finally{ setBusy(false); }
    }

    function buildOpenAIRequest(imageDataUrl, extraNotes){
      const systemPrompt = `You are a nutrition analyst. Analyze a photo of a single meal and estimate total calories and grams of protein, fat, and carbohydrates. 
Return ONLY strict JSON (no code fences, no commentary) matching this schema:
{
  "total_calories": number,              // kcal for the pictured portion
  "protein_g": number,                   // grams
  "fat_g": number,                       // grams
  "carbs_g": number,                     // grams
  "serving_description": string,         // brief note on portion size assumption
  "confidence": number,                  // 0-100
  "notes": string                        // short reasoning, ingredients, caveats
}
Rules: If multiple items, include all in totals. Prefer realistic ranges; pick the midpoint. If unsure, be conservative. Use common macro kcal conversions (P=4, C=4, F=9).`;

      const userText = `Please estimate calories and macros for this meal${extraNotes?` with context: ${extraNotes}`:''}.`;

      return {
        model: 'gpt-4o-mini',
        temperature: 0.2,
        messages: [
          { role:'system', content: systemPrompt },
          { role:'user', content: [
            { type:'text', text: userText },
            { type:'image_url', image_url: { url: imageDataUrl } }
          ]}
        ]
      };
    }

    function parseJsonSafe(text){
      if(!text) return null;
      // try direct parse
      try{ return JSON.parse(text); }catch(e){}
      // try to extract first JSON block
      const m = text.match(/\{[\s\S]*\}/);
      if(m){ try{ return JSON.parse(m[0]); }catch(e){} }
      return null;
    }

    function renderResults(r){
      const kcal = clamp2(r.total_calories);
      const p = clamp2(r.protein_g);
      const f = clamp2(r.fat_g);
      const c = clamp2(r.carbs_g);
      els.calories.textContent = isFinite(kcal)? `${kcal} kcal` : '—';
      els.protein.textContent = isFinite(p)? `${p} g` : '—';
      els.fat.textContent = isFinite(f)? `${f} g` : '—';
      els.carbs.textContent = isFinite(c)? `${c} g` : '—';
      els.extraNotes.textContent = r.notes || '';

      // Confidence pill
      if(typeof r.confidence === 'number'){
        els.confidencePill.hidden = false;
        els.confidencePill.textContent = `Confidence: ${Math.round(r.confidence)}%`;
      }else{
        els.confidencePill.hidden = true;
      }

      drawDonut(els.chart, {protein:p, fat:f, carbs:c});
      els.results.hidden = false; els.emptyState.hidden = true;
    }

    function clamp2(v){ v = Number(v); return Math.round((isFinite(v)? v : 0)*10)/10; }

    function drawDonut(canvas, grams){
      const cal = {
        protein: Math.max(0, grams.protein||0) * 4,
        carbs:   Math.max(0, grams.carbs||0) * 4,
        fat:     Math.max(0, grams.fat||0) * 9,
      };
      const parts = [
        {k:'protein', v:cal.protein, label:'Protein'},
        {k:'carbs', v:cal.carbs, label:'Carbs'},
        {k:'fat', v:cal.fat, label:'Fat'},
      ];
      const total = parts.reduce((a,b)=>a+b.v,0) || 1;
      const pct = parts.map(p=>({label:p.label, pct:(p.v/total)}));

      const ctx = canvas.getContext('2d');
      const dpr = window.devicePixelRatio || 1; canvas.width = 420*dpr; canvas.height = 420*dpr; canvas.style.width='100%';
      const W = canvas.width, H = canvas.height; const cx = W/2, cy = H/2; const R = Math.min(W,H)*0.36; const r = R*0.62;
      ctx.clearRect(0,0,W,H);

      // colors (no external libs)
      const colors = {
        protein: '#93c5fd', // blue-300
        carbs:   '#6ee7b7', // emerald-300
        fat:     '#fbbf24'  // amber-400
      };

      // Donut segments
      let start = -Math.PI/2;
      parts.forEach(seg=>{
        const angle = (seg.v/total)*Math.PI*2;
        ctx.beginPath(); ctx.arc(cx, cy, R, start, start+angle); ctx.arc(cx, cy, r, start+angle, start, true); ctx.closePath();
        ctx.fillStyle = colors[seg.k]; ctx.fill();
        start += angle;
      });

      // Center label
      ctx.fillStyle = '#e6e7eb'; ctx.textAlign='center'; ctx.textBaseline='middle';
      ctx.font = `${24*dpr}px system-ui, sans-serif`; ctx.fillText('P • C • F', cx, cy-10*dpr);
      ctx.font = `${18*dpr}px system-ui, sans-serif`; ctx.fillText('calorie share', cx, cy+16*dpr);

      // Legend
      const legendY = H - 60*dpr; const box = 14*dpr; const gap = 10*dpr; const spacing = 120*dpr;
      ['protein','carbs','fat'].forEach((k,i)=>{
        const x = cx - spacing + i*spacing;
        ctx.fillStyle = colors[k]; ctx.fillRect(x, legendY, box, box);
        ctx.fillStyle = '#e6e7eb'; ctx.font = `${16*dpr}px system-ui, sans-serif`; ctx.textAlign='left';
        const percent = Math.round((cal[k]/total)*100);
        ctx.fillText(`${k.charAt(0).toUpperCase()+k.slice(1)} ${percent}%`, x + box + gap, legendY + box/2);
      });
    }
  </script>
</body>
</html>
