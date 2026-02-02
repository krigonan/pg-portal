<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale=1.0, user-scalable=no">
    <title>OT Notes - Mobile</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; -webkit-tap-highlight-color: transparent; }
        .procedure-card { transition: transform 0.1s active; }
        .procedure-card:active { transform: scale(0.98); }
        .no-scrollbar::-webkit-scrollbar { display: none; }
        #procedureModal { transition: transform 0.3s ease-in-out; }
        .step-number { background: linear-gradient(135deg, #4f46e5 0%, #7c3aed 100%); }
        .pb-safe { padding-bottom: calc(80px + env(safe-area-inset-bottom)); }
    </style>
</head>
<body class="bg-gray-50 text-gray-900 pb-safe">

    <header class="bg-white border-b sticky top-0 z-30 px-4 py-4">
        <div class="flex justify-between items-center">
            <div>
                <h1 class="text-xl font-bold bg-clip-text text-transparent bg-gradient-to-r from-blue-600 to-purple-600">OT Notes</h1>
                <p class="text-[10px] text-gray-400 font-bold uppercase tracking-tighter">Surgery Residency Portal</p>
            </div>
            <div id="totalProcedures" class="bg-blue-100 text-blue-700 px-3 py-1 rounded-full text-xs font-bold">0</div>
        </div>
    </header>

    <div class="flex overflow-x-auto no-scrollbar gap-3 px-4 py-4">
        <div class="flex-shrink-0 bg-blue-500 text-white p-4 rounded-2xl w-28 shadow-sm">
            <div id="emergencyCount" class="text-xl font-bold">0</div>
            <div class="text-[10px] opacity-80 font-semibold uppercase">Emerg</div>
        </div>
        <div class="flex-shrink-0 bg-purple-500 text-white p-4 rounded-2xl w-28 shadow-sm">
            <div id="laparoscopicCount" class="text-xl font-bold">0</div>
            <div class="text-[10px] opacity-80 font-semibold uppercase">Lapa</div>
        </div>
        <div class="flex-shrink-0 bg-orange-500 text-white p-4 rounded-2xl w-28 shadow-sm">
            <div id="categoriesCount" class="text-xl font-bold">0</div>
            <div class="text-[10px] opacity-80 font-semibold uppercase">Cats</div>
        </div>
    </div>

    <div class="px-4 mb-4">
        <input type="text" id="searchInput" placeholder="Search procedure, organ, or tag..." class="w-full bg-white border border-gray-200 rounded-xl px-4 py-3 shadow-sm focus:ring-2 focus:ring-blue-500 outline-none">
    </div>

    <div id="categoryTags" class="flex overflow-x-auto no-scrollbar gap-2 px-4 mb-6"></div>

    <div id="proceduresGrid" class="px-4 space-y-3"></div>

    <div id="noResults" class="hidden text-center py-20 text-gray-400"><p>No procedures found.</p></div>

    <div class="fixed bottom-0 left-0 right-0 bg-white border-t px-4 py-3 flex gap-2 z-40 shadow-[0_-4px_10px_rgba(0,0,0,0.05)]">
        <select id="categoryFilter" class="flex-1 bg-gray-100 border-none rounded-lg px-3 py-2 text-sm font-semibold text-gray-700">
            <option value="">All Systems</option>
        </select>
        <select id="anesthesiaFilter" class="flex-1 bg-gray-100 border-none rounded-lg px-3 py-2 text-sm font-semibold text-gray-700">
            <option value="">Anesthesia</option>
            <option value="General">GA</option>
            <option value="Spinal">SAB</option>
            <option value="Local">LA</option>
        </select>
    </div>

    <div id="procedureModal" class="hidden fixed inset-0 z-50 bg-white flex flex-col">
        <div class="bg-blue-600 text-white px-4 py-4 flex items-center justify-between shadow-md">
            <button onclick="closeModal()" class="p-2">
                <svg class="w-6 h-6" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M15 19l-7-7 7-7"></path></svg>
            </button>
            <h2 id="modalTitle" class="text-lg font-bold truncate flex-1 px-2 text-center"></h2>
            <button onclick="printProcedure()" class="p-2">🖨️</button>
        </div>
        <div id="modalMeta" class="bg-blue-50 px-4 py-2 flex overflow-x-auto no-scrollbar gap-3 text-[10px] font-bold text-blue-700 uppercase"></div>
        <div id="modalContent" class="flex-1 overflow-y-auto p-5 pb-10"></div>
    </div>

    <script>
        const proceduresData = {
            "categories": [
                { "name": "Hepatobiliary Surgery", "color": "#96CEB4" },
                { "name": "Colorectal Surgery", "color": "#45B7D1" }
            ],
            "procedures": [
                {
                    "id": "proc_026",
                    "name": "Laparoscopic Cholecystectomy with Drain",
                    "anesthesia": "General anesthesia with endotracheal intubation",
                    "category": "Hepatobiliary Surgery",
                    "subcategory": "Gallbladder",
                    "position": "Reverse Trendelenburg with right side elevated 15-20 degrees",
                    "incision": {
                        "type": "Multi-port Laparoscopic",
                        "description": "10mm umbilical, 10mm epigastric, and two 5mm right subcostal ports"
                    },
                    "steps": [
                        { "stepNumber": 1, "title": "Pneumoperitoneum", "description": "Veress needle insertion at umbilicus. CO2 insufflation to 12-14 mmHg." },
                        { "stepNumber": 2, "title": "Port Insertion", "description": "10mm umbilical trocar for camera. Three additional ports inserted under direct vision." },
                        { "stepNumber": 3, "title": "Patient Positioning", "description": "Shifted to Reverse Trendelenburg with right side elevation." },
                        { "stepNumber": 4, "title": "Gallbladder Retraction", "description": "Fundus retracted cephalad. Hartmann's pouch retracted laterally to expose Calot's triangle." },
                        { "stepNumber": 5, "title": "Calot's Triangle Dissection", "description": "Clearance of fibro-fatty tissue to achieve the Critical View of Safety." },
                        { "stepNumber": 6, "title": "Clipping and Division", "description": "Cystic artery and duct secured with clips and divided." },
                        { "stepNumber": 7, "title": "Gallbladder Dissection", "description": "Separation from liver bed using hook diathermy." },
                        { "stepNumber": 8, "title": "Hemostasis and Irrigation", "description": "Inspection of liver bed and irrigation of subhepatic space." },
                        { "stepNumber": 9, "title": "Drain Placement", "description": "16F tube drain placed in Morrison's pouch." },
                        { "stepNumber": 10, "title": "Specimen Extraction", "description": "Retrieved through epigastric port in an endobag." },
                        { "stepNumber": 11, "title": "Closure", "description": "Fascial and skin closure of port sites." }
                    ],
                    "criticalPoints": [
                        "Attainment of the Critical View of Safety (CVS)",
                        "Proper clip orientation to avoid CBD tenting",
                        "Hemostasis check at 8 mmHg",
                        "Drain placement if difficult"
                    ],
                    "tags": ["laparoscopic", "cholecystectomy", "minimally invasive", "CVS"]
                }
            ]
        };

        function init() {
            if (!proceduresData.procedures.length) return;
            loadCategories();
            updateStats();
            renderProcedures(proceduresData.procedures);
            setupEventListeners();
        }

        function loadCategories() {
            const select = document.getElementById('categoryFilter');
            const tags = document.getElementById('categoryTags');
            proceduresData.categories.forEach(cat => {
                const opt = new Option(cat.name, cat.name);
                select.add(opt);
                const btn = document.createElement('button');
                btn.className = "flex-shrink-0 px-4 py-2 rounded-full text-xs font-bold text-white shadow-sm";
                btn.style.backgroundColor = cat.color;
                btn.textContent = cat.name;
                btn.onclick = () => { select.value = cat.name; filterProcedures(); };
                tags.appendChild(btn);
            });
        }

        function updateStats() {
            document.getElementById('totalProcedures').textContent = proceduresData.procedures.length;
            document.getElementById('emergencyCount').textContent = proceduresData.procedures.filter(p => p.tags?.includes('emergency')).length;
            document.getElementById('laparoscopicCount').textContent = proceduresData.procedures.filter(p => p.tags?.includes('laparoscopic')).length;
            document.getElementById('categoriesCount').textContent = proceduresData.categories.length;
        }

        function renderProcedures(data) {
            const grid = document.getElementById('proceduresGrid');
            const noRes = document.getElementById('noResults');
            grid.innerHTML = data.map(proc => `
                <div class="bg-white p-4 rounded-xl shadow-sm border border-gray-100 flex justify-between items-center" onclick="openModal('${proc.id}')" style="border-left: 4px solid ${getCategoryColor(proc.category)}">
                    <div class="flex-1 pr-4">
                        <div class="text-[10px] font-bold text-gray-400 uppercase mb-0.5">${proc.category}${proc.subcategory ? ' • ' + proc.subcategory : ''}</div>
                        <h3 class="font-bold text-gray-800 leading-snug">${proc.name}</h3>
                        <div class="flex items-center gap-2 mt-1.5">
                            <span class="text-[10px] font-bold bg-gray-100 text-gray-600 px-2 py-0.5 rounded">${proc.anesthesia.split(' ')[0]}</span>
                        </div>
                    </div>
                    <svg class="w-5 h-5 text-gray-300" fill="none" stroke="currentColor" viewBox="0 0 24 24"><path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M9 5l7 7-7 7"></path></svg>
                </div>
            `).join('');
            data.length ? noRes.classList.add('hidden') : noRes.classList.remove('hidden');
        }

        function getCategoryColor(catName) {
            return proceduresData.categories.find(c => c.name === catName)?.color || '#94a3b8';
        }

        function openModal(id) {
            const p = proceduresData.procedures.find(proc => proc.id === id);
            if (!p) return;
            document.getElementById('modalTitle').textContent = p.name;
            document.getElementById('modalMeta').innerHTML = `<span>${p.category}</span><span>•</span><span>${p.anesthesia.split(' ')[0]}</span>`;
            
            let content = `
                <div class="mb-6">
                    <h4 class="text-xs font-bold text-gray-400 uppercase mb-2 tracking-widest">Setup</h4>
                    <p class="text-sm text-gray-700"><strong>Position:</strong> ${p.position}</p>
                    ${p.incision ? `<p class="text-sm text-gray-700 mt-1"><strong>Incision:</strong> ${p.incision.type} - ${p.incision.description}</p>` : ''}
                </div>
                <h4 class="text-xs font-bold text-gray-400 uppercase mb-4 tracking-widest">Technique</h4>
                <div class="space-y-6">
                    ${p.steps.map(s => `
                        <div class="flex gap-4">
                            <div class="step-number flex-shrink-0 w-7 h-7 rounded-full text-white text-xs flex items-center justify-center font-bold">${s.stepNumber}</div>
                            <div>
                                <h5 class="font-bold text-gray-800 text-sm mb-1">${s.title}</h5>
                                <p class="text-sm text-gray-600 leading-relaxed">${s.description}</p>
                            </div>
                        </div>
                    `).join('')}
                </div>
            `;
            
            if (p.criticalPoints) {
                content += `
                    <div class="mt-8 p-4 bg-red-50 rounded-xl border border-red-100">
                        <h4 class="text-red-600 font-bold text-xs uppercase mb-2">⚠️ Critical Points</h4>
                        <ul class="text-sm text-red-800 space-y-1">${p.criticalPoints.map(cp => `<li>• ${cp}</li>`).join('')}</ul>
                    </div>
                `;
            }

            document.getElementById('modalContent').innerHTML = content;
            document.getElementById('procedureModal').classList.remove('hidden');
            document.body.style.overflow = 'hidden';
        }

        function closeModal() {
            document.getElementById('procedureModal').classList.add('hidden');
            document.body.style.overflow = 'auto';
        }

        function filterProcedures() {
            const s = document.getElementById('searchInput').value.toLowerCase();
            const c = document.getElementById('categoryFilter').value;
            const a = document.getElementById('anesthesiaFilter').value;
            const filtered = proceduresData.procedures.filter(p => {
                return (p.name.toLowerCase().includes(s) || p.category.toLowerCase().includes(s)) &&
                       (!c || p.category === c) &&
                       (!a || p.anesthesia.includes(a));
            });
            renderProcedures(filtered);
        }

        function setupEventListeners() {
            document.getElementById('searchInput').addEventListener('input', filterProcedures);
            document.getElementById('categoryFilter').addEventListener('change', filterProcedures);
            document.getElementById('anesthesiaFilter').addEventListener('change', filterProcedures);
        }

        function printProcedure() { window.print(); }
        document.addEventListener('DOMContentLoaded', init);
    </script>
</body>
</html>
