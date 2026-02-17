<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Salem OmniCalc: Physics & Mathematics Engine</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <link href="https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@400;700&family=Inter:wght@300;400;600;800&display=swap" rel="stylesheet">
    <style>
        body {
            font-family: 'Inter', sans-serif;
            background-color: #0f172a;
            color: #e2e8f0;
            overflow-x: hidden;
        }
        .mono { font-family: 'JetBrains Mono', monospace; }
        ::-webkit-scrollbar { width: 8px; }
        ::-webkit-scrollbar-track { background: #1e293b; }
        ::-webkit-scrollbar-thumb { background: #475569; border-radius: 4px; }
        .glass {
            background: rgba(30, 41, 59, 0.7);
            backdrop-filter: blur(12px);
            -webkit-backdrop-filter: blur(12px);
            border: 1px solid rgba(255, 255, 255, 0.1);
        }
        .glow-text { text-shadow: 0 0 10px rgba(56, 189, 248, 0.5); }
        .glow-border:focus-within {
            box-shadow: 0 0 15px rgba(56, 189, 248, 0.3);
            border-color: #38bdf8;
        }
        .btn-press:active { transform: scale(0.95); }
        .bg-grid {
            background-size: 40px 40px;
            background-image: linear-gradient(to right, rgba(255, 255, 255, 0.05) 1px, transparent 1px),
                              linear-gradient(to bottom, rgba(255, 255, 255, 0.05) 1px, transparent 1px);
            animation: moveGrid 20s linear infinite;
        }
        @keyframes moveGrid {
            0% { background-position: 0 0; }
            100% { background-position: 40px 40px; }
        }
        .canvas-container {
            position: relative;
            width: 100%;
            height: 250px;
            overflow: hidden;
            border-radius: 0.5rem;
            background: #020617;
        }
    </style>
</head>
<body class="min-h-screen flex flex-col bg-grid relative">
    <div class="fixed top-0 left-0 w-full h-full overflow-hidden -z-10 pointer-events-none">
        <div class="absolute top-[-10%] left-[-10%] w-[40%] h-[40%] bg-blue-600/20 rounded-full blur-[120px]"></div>
        <div class="absolute bottom-[-10%] right-[-10%] w-[40%] h-[40%] bg-purple-600/20 rounded-full blur-[120px]"></div>
    </div>

    <header class="w-full p-6 flex justify-between items-center glass sticky top-0 z-50 border-b border-slate-700">
        <div class="flex items-center gap-3">
            <div class="w-10 h-10 bg-gradient-to-br from-cyan-500 to-blue-600 rounded-lg flex items-center justify-center shadow-lg shadow-cyan-500/20">
                <span class="text-xl font-bold text-white">Ω</span>
            </div>
            <div>
                <h1 class="text-2xl font-bold tracking-tight text-white glow-text">Salem OmniCalc</h1>
                <p class="text-xs text-slate-400 uppercase tracking-widest">Physics & Math Engine</p>
            </div>
        </div>
        <div class="flex gap-4">
            <button onclick="switchMode('calc')" id="btn-calc" class="px-4 py-2 rounded-md text-sm font-semibold transition-all bg-cyan-500/20 text-cyan-400 border border-cyan-500/50 hover:bg-cyan-500/30">Calculator</button>
            <button onclick="switchMode('physics')" id="btn-physics" class="px-4 py-2 rounded-md text-sm font-semibold transition-all text-slate-400 hover:text-white hover:bg-slate-700">Physics Lab</button>
        </div>
    </header>

    <main class="flex-grow p-4 md:p-8 max-w-7xl mx-auto w-full grid grid-cols-1 lg:grid-cols-12 gap-6">
        <!-- CALCULATOR PANEL -->
        <div id="calculator-panel" class="lg:col-span-5 flex flex-col gap-4 transition-all duration-500">
            <div class="glass rounded-2xl p-6 flex flex-col justify-end h-40 shadow-2xl relative overflow-hidden glow-border transition-all">
                <div class="absolute top-2 right-4 text-xs text-slate-500 mono" id="mode-indicator">DEG</div>
                <div id="history-display" class="text-slate-400 text-sm text-right mb-1 mono h-6 overflow-hidden"></div>
                <div id="main-display" class="text-4xl md:text-5xl font-bold text-white text-right mono break-all">0</div>
            </div>

            <div class="glass rounded-2xl p-4 grid grid-cols-5 gap-2 md:gap-3 select-none">
                <!-- Row 1 -->
                <button onclick="handleFunc('sin')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-sm font-bold mono">sin</button>
                <button onclick="handleFunc('cos')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-sm font-bold mono">cos</button>
                <button onclick="handleFunc('tan')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-sm font-bold mono">tan</button>
                <button onclick="insertVal('(')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-slate-300 font-bold mono">(</button>
                <button onclick="insertVal(')')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-slate-300 font-bold mono">)</button>

                <!-- Row 2 -->
                <button onclick="handleFunc('asin')" class="btn-press p-3 rounded-lg bg-slate-800/50 hover:bg-slate-700 text-slate-400 text-xs font-bold mono">sin⁻¹</button>
                <button onclick="handleFunc('acos')" class="btn-press p-3 rounded-lg bg-slate-800/50 hover:bg-slate-700 text-slate-400 text-xs font-bold mono">cos⁻¹</button>
                <button onclick="handleFunc('atan')" class="btn-press p-3 rounded-lg bg-slate-800/50 hover:bg-slate-700 text-slate-400 text-xs font-bold mono">tan⁻¹</button>
                <button onclick="insertConst('pi')" class="btn-press p-3 rounded-lg bg-slate-800/50 hover:bg-slate-700 text-purple-300 text-sm font-bold mono">π</button>
                <button onclick="insertConst('e')" class="btn-press p-3 rounded-lg bg-slate-800/50 hover:bg-slate-700 text-purple-300 text-sm font-bold mono">e</button>

                <!-- Row 3 -->
                <button onclick="insertVal('^')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 font-bold mono">x^y</button>
                <button onclick="handleFunc('sqrt')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-sm font-bold mono">√</button>
                <button onclick="insertVal('!')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-sm font-bold mono">n!</button>
                <button onclick="insertVal('%')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 font-bold mono">%</button>
                <button onclick="deleteLast()" class="btn-press p-3 rounded-lg bg-red-500/20 hover:bg-red-500/30 text-red-400 font-bold mono">DEL</button>

                <!-- Row 4 -->
                <button onclick="clearAll()" class="btn-press p-3 rounded-lg bg-red-500/20 hover:bg-red-500/30 text-red-400 font-bold mono col-span-2">AC</button>
                <button onclick="toggleAngleMode()" id="angle-btn" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-slate-300 text-sm font-bold mono">DEG</button>
                <button onclick="insertVal('/')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-xl font-bold">÷</button>
                <button onclick="insertVal('*')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-xl font-bold">×</button>

                <!-- Row 5 -->
                <button onclick="insertVal('7')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">7</button>
                <button onclick="insertVal('8')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">8</button>
                <button onclick="insertVal('9')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">9</button>
                <button onclick="insertVal('-')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-xl font-bold">−</button>
                <button onclick="insertVal('log(')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-slate-400 text-sm font-bold mono">log</button>

                <!-- Row 6 -->
                <button onclick="insertVal('4')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">4</button>
                <button onclick="insertVal('5')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">5</button>
                <button onclick="insertVal('6')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">6</button>
                <button onclick="insertVal('+')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-cyan-300 text-xl font-bold">+</button>
                <button onclick="insertVal('ln(')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-slate-400 text-sm font-bold mono">ln</button>

                <!-- Row 7 -->
                <button onclick="insertVal('1')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">1</button>
                <button onclick="insertVal('2')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">2</button>
                <button onclick="insertVal('3')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">3</button>
                <button onclick="calculate()" class="btn-press row-span-2 p-4 rounded-lg bg-gradient-to-b from-cyan-500 to-blue-600 hover:from-cyan-400 hover:to-blue-500 text-white text-2xl font-bold shadow-lg shadow-cyan-500/30 flex items-center justify-center">=</button>
                <button onclick="insertVal('^2')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-slate-400 text-sm font-bold mono">x²</button>

                <!-- Row 8 -->
                <button onclick="insertVal('0')" class="btn-press col-span-2 p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">0</button>
                <button onclick="insertVal('.')" class="btn-press p-4 rounded-lg bg-slate-800 hover:bg-slate-700 text-white text-xl font-bold shadow-lg">.</button>
                <button onclick="insertVal('10^')" class="btn-press p-3 rounded-lg bg-slate-700/50 hover:bg-slate-600 text-slate-400 text-xs font-bold mono">EXP</button>
            </div>
        </div>

        <!-- PHYSICS PANEL -->
        <div id="physics-panel" class="lg:col-span-7 flex flex-col gap-6 hidden lg:flex">
            <div class="glass rounded-2xl p-6">
                <h2 class="text-xl font-bold text-white mb-4 flex items-center gap-2">
                    <span class="w-2 h-6 bg-purple-500 rounded-full"></span>
                    Physics Solver
                </h2>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <!-- SHM Card -->
                    <div onclick="loadPhysicsMode('shm')" class="cursor-pointer group p-4 rounded-xl bg-slate-800/50 border border-slate-700 hover:border-purple-500 transition-all hover:bg-slate-800">
                        <div class="flex justify-between items-start mb-2">
                            <h3 class="font-bold text-purple-300">Simple Harmonic Motion</h3>
                            <span class="text-xs bg-purple-500/20 text-purple-300 px-2 py-1 rounded">Oscillation</span>
                        </div>
                        <p class="text-xs text-slate-400 mb-2">x(t) = A·cos(ωt + φ)</p>
                        <div class="h-1 w-full bg-slate-700 rounded overflow-hidden">
                            <div class="h-full bg-purple-500 w-0 group-hover:w-full transition-all duration-500"></div>
                        </div>
                    </div>

                    <!-- Kinematics Card -->
                    <div onclick="loadPhysicsMode('kinematics')" class="cursor-pointer group p-4 rounded-xl bg-slate-800/50 border border-slate-700 hover:border-cyan-500 transition-all hover:bg-slate-800">
                        <div class="flex justify-between items-start mb-2">
                            <h3 class="font-bold text-cyan-300">Kinematics</h3>
                            <span class="text-xs bg-cyan-500/20 text-cyan-300 px-2 py-1 rounded">Motion</span>
                        </div>
                        <p class="text-xs text-slate-400 mb-2">v = u + at, s = ut + ½at²</p>
                        <div class="h-1 w-full bg-slate-700 rounded overflow-hidden">
                            <div class="h-full bg-cyan-500 w-0 group-hover:w-full transition-all duration-500"></div>
                        </div>
                    </div>

                    <!-- Energy Card -->
                    <div onclick="loadPhysicsMode('energy')" class="cursor-pointer group p-4 rounded-xl bg-slate-800/50 border border-slate-700 hover:border-green-500 transition-all hover:bg-slate-800">
                        <div class="flex justify-between items-start mb-2">
                            <h3 class="font-bold text-green-300">Energy & Work</h3>
                            <span class="text-xs bg-green-500/20 text-green-300 px-2 py-1 rounded">Conservation</span>
                        </div>
                        <p class="text-xs text-slate-400 mb-2">KE = ½mv², PE = mgh</p>
                        <div class="h-1 w-full bg-slate-700 rounded overflow-hidden">
                            <div class="h-full bg-green-500 w-0 group-hover:w-full transition-all duration-500"></div>
                        </div>
                    </div>

                    <!-- Waves Card -->
                    <div onclick="loadPhysicsMode('waves')" class="cursor-pointer group p-4 rounded-xl bg-slate-800/50 border border-slate-700 hover:border-yellow-500 transition-all hover:bg-slate-800">
                        <div class="flex justify-between items-start mb-2">
                            <h3 class="font-bold text-yellow-300">Wave Equation</h3>
                            <span class="text-xs bg-yellow-500/20 text-yellow-300 px-2 py-1 rounded">EM/Mechanical</span>
                        </div>
                        <p class="text-xs text-slate-400 mb-2">v = fλ</p>
                        <div class="h-1 w-full bg-slate-700 rounded overflow-hidden">
                            <div class="h-full bg-yellow-500 w-0 group-hover:w-full transition-all duration-500"></div>
                        </div>
                    </div>
                </div>
            </div>

            <!-- Physics Workspace -->
            <div id="physics-workspace" class="glass rounded-2xl p-6 flex-grow flex flex-col hidden">
                <div class="flex justify-between items-center mb-6">
                    <h3 id="phy-title" class="text-2xl font-bold text-white"></h3>
                    <button onclick="closePhysics()" class="text-slate-400 hover:text-white">✕ Close</button>
                </div>

                <div class="grid grid-cols-1 md:grid-cols-2 gap-8">
                    <!-- Inputs -->
                    <div class="space-y-4">
                        <div id="input-container" class="space-y-3"></div>
                        <button onclick="solvePhysics()" class="w-full py-3 bg-gradient-to-r from-purple-600 to-blue-600 rounded-lg font-bold text-white shadow-lg hover:shadow-purple-500/25 transition-all">Calculate & Visualize</button>
                        
                        <div id="result-box" class="mt-4 p-4 bg-slate-900/50 rounded-lg border border-slate-700 hidden">
                            <p class="text-sm text-slate-400">Result:</p>
                            <p id="phy-result" class="text-2xl font-mono text-cyan-400 font-bold"></p>
                        </div>
                    </div>

                    <!-- Visualization -->
                    <div class="bg-slate-900 rounded-xl p-4 border border-slate-700 relative canvas-container">
                        <canvas id="simCanvas" width="400" height="250"></canvas>
                        <div class="absolute bottom-2 right-2 text-xs text-slate-500">Live Simulation</div>
                    </div>
                </div>
            </div>

            <!-- Empty State -->
            <div id="physics-empty" class="glass rounded-2xl p-10 flex flex-col items-center justify-center text-center flex-grow border-dashed border-2 border-slate-700">
                <div class="w-16 h-16 bg-slate-800 rounded-full flex items-center justify-center mb-4 text-2xl">🚀</div>
                <h3 class="text-xl font-bold text-white mb-2">Select a Physics Module</h3>
                <p class="text-slate-400 max-w-md">Choose an equation from above to solve for variables, visualize graphs, and simulate physical phenomena.</p>
            </div>
        </div>
    </main>

    <script>
        // ==================== CALCULATOR ====================
        let currentInput = '0';
        let history = '';
        let isDegree = true;
        
        const display = document.getElementById('main-display');
        const historyDisplay = document.getElementById('history-display');
        const angleBtn = document.getElementById('angle-btn');
        const modeIndicator = document.getElementById('mode-indicator');

        function updateDisplay() {
            display.innerText = currentInput;
            historyDisplay.innerText = history;
        }

        function insertVal(val) {
            if (currentInput === '0' && val !== '.') {
                currentInput = val;
            } else {
                const lastChar = currentInput.slice(-1);
                const operators = ['+', '-', '*', '/', '^'];
                if (operators.includes(lastChar) && operators.includes(val)) {
                    currentInput = currentInput.slice(0, -1) + val;
                } else {
                    currentInput += val;
                }
            }
            updateDisplay();
        }

        function insertConst(type) {
            if (currentInput === '0') currentInput = '';
            if (type === 'pi') currentInput += 'π';
            if (type === 'e') currentInput += 'e';
            updateDisplay();
        }

        function handleFunc(func) {
            if (currentInput === '0') currentInput = '';
            currentInput += func + '(';
            updateDisplay();
        }

        function clearAll() {
            currentInput = '0';
            history = '';
            updateDisplay();
        }

        function deleteLast() {
            if (currentInput.length === 1) {
                currentInput = '0';
            } else {
                currentInput = currentInput.slice(0, -1);
            }
            updateDisplay();
        }

        function toggleAngleMode() {
            isDegree = !isDegree;
            angleBtn.innerText = isDegree ? 'DEG' : 'RAD';
            modeIndicator.innerText = isDegree ? 'DEG' : 'RAD';
        }

        function calculate() {
            try {
                let expression = currentInput;
                history = expression + ' =';

                expression = expression.replace(/π/g, 'Math.PI');
                expression = expression.replace(/e/g, 'Math.E');
                expression = expression.replace(/\^/g, '**');
                expression = expression.replace(/%/g, '/100');
                expression = expression.replace(/(\d+)!/g, (match, num) => factorial(parseInt(num)));

                const scope = {
                    sin: (x) => isDegree ? Math.sin(x * Math.PI / 180) : Math.sin(x),
                    cos: (x) => isDegree ? Math.cos(x * Math.PI / 180) : Math.cos(x),
                    tan: (x) => isDegree ? Math.tan(x * Math.PI / 180) : Math.tan(x),
                    asin: (x) => isDegree ? Math.asin(x) * 180 / Math.PI : Math.asin(x),
                    acos: (x) => isDegree ? Math.acos(x) * 180 / Math.PI : Math.acos(x),
                    atan: (x) => isDegree ? Math.atan(x) * 180 / Math.PI : Math.atan(x),
                    sqrt: Math.sqrt,
                    log: Math.log10,
                    ln: Math.log,
                    abs: Math.abs
                };

                const func = new Function(...Object.keys(scope), `return ${expression}`);
                let result = func(...Object.values(scope));

                if (!isFinite(result) || isNaN(result)) throw new Error("Invalid");
                
                result = Math.round(result * 10000000000) / 10000000000;
                currentInput = result.toString();
                updateDisplay();

            } catch (e) {
                currentInput = "Error";
                updateDisplay();
                setTimeout(() => {
                    currentInput = "0";
                    updateDisplay();
                }, 1500);
            }
        }

        function factorial(n) {
            if (n < 0) return NaN;
            if (n === 0 || n === 1) return 1;
            let result = 1;
            for (let i = 2; i <= n; i++) result *= i;
            return result;
        }

        // ==================== PHYSICS ====================
        let currentPhysicsMode = null;
        let animationId = null;
        const canvas = document.getElementById('simCanvas');
        const ctx = canvas.getContext('2d');

        function switchMode(mode) {
            const calcPanel = document.getElementById('calculator-panel');
            const physicsPanel = document.getElementById('physics-panel');
            const btnCalc = document.getElementById('btn-calc');
            const btnPhy = document.getElementById('btn-physics');

            if (mode === 'calc') {
                calcPanel.classList.remove('hidden');
                physicsPanel.classList.add('hidden');
                physicsPanel.classList.remove('flex');
                btnCalc.className = "px-4 py-2 rounded-md text-sm font-semibold transition-all bg-cyan-500/20 text-cyan-400 border border-cyan-500/50 hover:bg-cyan-500/30";
                btnPhy.className = "px-4 py-2 rounded-md text-sm font-semibold transition-all text-slate-400 hover:text-white hover:bg-slate-700";
                stopAnimation();
            } else {
                calcPanel.classList.add('hidden');
                physicsPanel.classList.remove('hidden');
                physicsPanel.classList.add('flex');
                btnPhy.className = "px-4 py-2 rounded-md text-sm font-semibold transition-all bg-purple-500/20 text-purple-400 border border-purple-500/50 hover:bg-purple-500/30";
                btnCalc.className = "px-4 py-2 rounded-md text-sm font-semibold transition-all text-slate-400 hover:text-white hover:bg-slate-700";
            }
        }

        function loadPhysicsMode(mode) {
            currentPhysicsMode = mode;
            document.getElementById('physics-empty').classList.add('hidden');
            document.getElementById('physics-workspace').classList.remove('hidden');
            document.getElementById('result-box').classList.add('hidden');
            
            const container = document.getElementById('input-container');
            const title = document.getElementById('phy-title');
            container.innerHTML = '';

            if (mode === 'shm') {
                title.innerText = "Simple Harmonic Motion";
                title.className = "text-2xl font-bold text-purple-300";
                createInput(container, 'A', 'Amplitude A (m)', 1);
                createInput(container, 'omega', 'Angular Frequency ω (rad/s)', 1);
                createInput(container, 'phi', 'Phase φ (rad)', 0);
                createInput(container, 't', 'Time t (s)', 0);
                createDropdown(container, 'solveFor', 'Solve For:', [
                    {val: 'x', text: 'Displacement x(t)'},
                    {val: 'v', text: 'Velocity v(t)'},
                    {val: 'a', text: 'Acceleration a(t)'}
                ]);
            } else if (mode === 'kinematics') {
                title.innerText = "Kinematics (Linear Motion)";
                title.className = "text-2xl font-bold text-cyan-300";
                createInput(container, 'u', 'Initial Velocity u (m/s)', 0);
                createInput(container, 'v', 'Final Velocity v (m/s)', 10);
                createInput(container, 'a', 'Acceleration a (m/s²)', 2);
                createInput(container, 't', 'Time t (s)', 5);
                createDropdown(container, 'solveFor', 'Solve For:', [
                    {val: 's', text: 'Displacement s'},
                    {val: 'v', text: 'Final Velocity v'},
                    {val: 't', text: 'Time t'}
                ]);
            } else if (mode === 'energy') {
                title.innerText = "Energy Conservation";
                title.className = "text-2xl font-bold text-green-300";
                createInput(container, 'm', 'Mass m (kg)', 2);
                createInput(container, 'v', 'Velocity v (m/s)', 5);
                createInput(container, 'h', 'Height h (m)', 10);
                createDropdown(container, 'solveFor', 'Solve For:', [
                    {val: 'ke', text: 'Kinetic Energy KE'},
                    {val: 'pe', text: 'Potential Energy PE'},
                    {val: 'te', text: 'Total Energy TE'}
                ]);
            } else if (mode === 'waves') {
                title.innerText = "Wave Properties";
                title.className = "text-2xl font-bold text-yellow-300";
                createInput(container, 'v', 'Wave Velocity v (m/s)', 343);
                createInput(container, 'f', 'Frequency f (Hz)', 440);
                createInput(container, 'lambda', 'Wavelength λ (m)', 0);
                createDropdown(container, 'solveFor', 'Solve For:', [
                    {val: 'lambda', text: 'Wavelength λ'},
                    {val: 'f', text: 'Frequency f'},
                    {val: 'v', text: 'Velocity v'}
                ]);
            }
        }

        function createInput(parent, id, label, defaultVal) {
            const div = document.createElement('div');
            div.className = "flex flex-col";
            div.innerHTML = `
                <label class="text-xs text-slate-400 mb-1">${label}</label>
                <input type="number" id="in-${id}" value="${defaultVal}" step="any" class="bg-slate-800 border border-slate-600 rounded px-3 py-2 text-white focus:border-cyan-500 focus:outline-none transition-colors">
            `;
            parent.appendChild(div);
        }

        function createDropdown(parent, id, label, options) {
            const div = document.createElement('div');
            div.className = "flex flex-col";
            let opts = options.map(o => `<option value="${o.val}">${o.text}</option>`).join('');
            div.innerHTML = `
                <label class="text-xs text-slate-400 mb-1">${label}</label>
                <select id="sel-${id}" class="bg-slate-800 border border-slate-600 rounded px-3 py-2 text-white focus:border-cyan-500 focus:outline-none transition-colors">
                    ${opts}
                </select>
            `;
            parent.appendChild(div);
        }

        function closePhysics() {
            document.getElementById('physics-workspace').classList.add('hidden');
            document.getElementById('physics-empty').classList.remove('hidden');
            stopAnimation();
        }

        function solvePhysics() {
            const resultBox = document.getElementById('result-box');
            const resultText = document.getElementById('phy-result');
            resultBox.classList.remove('hidden');

            if (currentPhysicsMode === 'shm') {
                const A = parseFloat(document.getElementById('in-A').value);
                const w = parseFloat(document.getElementById('in-omega').value);
                const phi = parseFloat(document.getElementById('in-phi').value);
                const t = parseFloat(document.getElementById('in-t').value);
                const solve = document.getElementById('sel-solveFor').value;

                let res = 0;
                let label = '';
                if (solve === 'x') { res = A * Math.cos(w * t + phi); label = 'x'; }
                if (solve === 'v') { res = -A * w * Math.sin(w * t + phi); label = 'v'; }
                if (solve === 'a') { res = -A * w * w * Math.cos(w * t + phi); label = 'a'; }
                
                resultText.innerText = `${label} = ${res.toFixed(4)}`;
                startSHMAnimation(A, w, phi, solve);

            } else if (currentPhysicsMode === 'kinematics') {
                let u = parseFloat(document.getElementById('in-u').value);
                let v = parseFloat(document.getElementById('in-v').value);
                let a = parseFloat(document.getElementById('in-a').value);
                let t = parseFloat(document.getElementById('in-t').value);
                const solve = document.getElementById('sel-solveFor').value;

                let res = 0;
                let unit = '';
                if (solve === 's') { res = u*t + 0.5*a*t*t; unit = 'm'; }
                if (solve === 'v') { res = u + a*t; unit = 'm/s'; }
                if (solve === 't') { res = (v - u) / a; unit = 's'; }

                resultText.innerText = `${solve} = ${res.toFixed(4)} ${unit}`;
                startKinematicsAnimation(u, a, t);

            } else if (currentPhysicsMode === 'energy') {
                const m = parseFloat(document.getElementById('in-m').value);
                const v = parseFloat(document.getElementById('in-v').value);
                const h = parseFloat(document.getElementById('in-h').value);
                const solve = document.getElementById('sel-solveFor').value;

                let res = 0;
                if (solve === 'ke') res = 0.5 * m * v * v;
                if (solve === 'pe') res = m * 9.81 * h;
                if (solve === 'te') res = (0.5 * m * v * v) + (m * 9.81 * h);

                resultText.innerText = `${solve.toUpperCase()} = ${res.toFixed(4)} J`;
                drawEnergyBars(m, v, h);

            } else if (currentPhysicsMode === 'waves') {
                let v = parseFloat(document.getElementById('in-v').value);
                let f = parseFloat(document.getElementById('in-f').value);
                let lambda = parseFloat(document.getElementById('in-lambda').value);
                const solve = document.getElementById('sel-solveFor').value;

                let res = 0;
                let unit = '';
                if (solve === 'lambda') { res = v / f; unit = 'm'; }
                if (solve === 'f') { res = v / lambda; unit = 'Hz'; }
                if (solve === 'v') { res = f * lambda; unit = 'm/s'; }

                resultText.innerText = `${solve} = ${res.toFixed(4)} ${unit}`;
                startWaveAnimation(v, f);
            }
        }

        function stopAnimation() {
            if (animationId) cancelAnimationFrame(animationId);
            ctx.fillStyle = '#020617';
            ctx.fillRect(0, 0, canvas.width, canvas.height);
        }

        function startSHMAnimation(A, w, phi, type) {
            stopAnimation();
            let time = 0;
            
            function animate() {
                ctx.fillStyle = '#020617';
                ctx.fillRect(0, 0, canvas.width, canvas.height);
                
                ctx.strokeStyle = '#334155';
                ctx.beginPath();
                ctx.moveTo(0, canvas.height/2);
                ctx.lineTo(canvas.width, canvas.height/2);
                ctx.stroke();

                const t = time * 0.05;
                let val = 0;
                let color = '#38bdf8';
                let scale = 50;
                
                if (type === 'x') {
                    val = A * Math.cos(w * t + phi);
                    color = '#c084fc';
                } else if (type === 'v') {
                    val = -A * w * Math.sin(w * t + phi);
                    color = '#38bdf8';
                    scale = 30;
                } else {
                    val = -A * w * w * Math.cos(w * t + phi);
                    color = '#f472b6';
                    scale = 15;
                }

                const y = canvas.height/2 - (val * scale);
                const x = canvas.width/2;

                // Draw spring
                ctx.strokeStyle = '#94a3b8';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.moveTo(50, y);
                for(let i=50; i<x-20; i+=15) {
                    ctx.lineTo(i, y + ((i/15)%2 === 0 ? 15 : -15));
                }
                ctx.lineTo(x-20, y);
                ctx.stroke();
                ctx.lineWidth = 1;

                // Draw block
                ctx.fillStyle = color;
                ctx.shadowBlur = 20;
                ctx.shadowColor = color;
                ctx.fillRect(x-20, y-20, 40, 40);
                ctx.shadowBlur = 0;

                // Draw info
                ctx.fillStyle = '#fff';
                ctx.font = '14px monospace';
                ctx.fillText(`t=${t.toFixed(2)}s`, 10, 30);
                ctx.fillText(`${type}=${val.toFixed(3)}`, 10, 50);

                time++;
                animationId = requestAnimationFrame(animate);
            }
            animate();
        }

        function startKinematicsAnimation(u, a, t_total) {
            stopAnimation();
            let startTime = Date.now();
            
            function animate() {
                let elapsed = (Date.now() - startTime) / 1000;
                if (elapsed > t_total) elapsed = t_total;

                ctx.fillStyle = '#020617';
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                const s = u * elapsed + 0.5 * a * elapsed * elapsed;
                const pixelX = 50 + s * 20;
                const groundY = canvas.height - 50;

                // Ground
                ctx.strokeStyle = '#334155';
                ctx.lineWidth = 2;
                ctx.beginPath();
                ctx.moveTo(0, groundY);
                ctx.lineTo(canvas.width, groundY);
                ctx.stroke();

                // Car
                ctx.fillStyle = '#22d3ee';
                ctx.fillRect(pixelX, groundY - 60, 50, 30);
                
                // Wheels
                ctx.fillStyle = '#fff';
                ctx.beginPath();
                ctx.arc(pixelX + 12, groundY - 15, 10, 0, Math.PI*2);
                ctx.arc(pixelX + 38, groundY - 15, 10, 0, Math.PI*2);
                ctx.fill();

                // Velocity vector
                const v = u + a * elapsed;
                if (Math.abs(v) > 0.1) {
                    ctx.strokeStyle = '#facc15';
                    ctx.lineWidth = 3;
                    ctx.beginPath();
                    ctx.moveTo(pixelX + 25, groundY - 80);
                    ctx.lineTo(pixelX + 25 + v * 3, groundY - 80);
                    ctx.stroke();
                    ctx.lineWidth = 1;
                }

                // Info
                ctx.fillStyle = '#fff';
                ctx.font = '14px monospace';
                ctx.fillText(`t=${elapsed.toFixed(2)}s`, 10, 30);
                ctx.fillText(`s=${s.toFixed(2)}m`, 10, 50);
                ctx.fillText(`v=${v.toFixed(2)}m/s`, 10, 70);

                if (elapsed < t_total) {
                    animationId = requestAnimationFrame(animate);
                }
            }
            animate();
        }

        function drawEnergyBars(m, v, h) {
            stopAnimation();
            const ke = 0.5 * m * v * v;
            const pe = m * 9.81 * h;
            const max = Math.max(ke, pe, 1);

            ctx.fillStyle = '#020617';
            ctx.fillRect(0, 0, canvas.width, canvas.height);

            const barWidth = 80;
            const maxHeight = 150;
            
            // KE Bar
            const h1 = (ke/max) * maxHeight;
            ctx.fillStyle = '#22d3ee';
            ctx.fillRect(80, 200 - h1, barWidth, h1);
            ctx.fillStyle = '#fff';
            ctx.font = '16px sans-serif';
            ctx.fillText("KE", 105, 220);
            ctx.font = '12px monospace';
            ctx.fillText(`${ke.toFixed(1)}J`, 85, 200 - h1 - 10);

            // PE Bar
            const h2 = (pe/max) * maxHeight;
            ctx.fillStyle = '#a3e635';
            ctx.fillRect(240, 200 - h2, barWidth, h2);
            ctx.fillStyle = '#fff';
            ctx.font = '16px sans-serif';
            ctx.fillText("PE", 265, 220);
            ctx.font = '12px monospace';
            ctx.fillText(`${pe.toFixed(1)}J`, 245, 200 - h2 - 10);
        }

        function startWaveAnimation(v, f) {
            stopAnimation();
            let time = 0;
            const lambda = v / f;
            
            function animate() {
                ctx.fillStyle = '#020617';
                ctx.fillRect(0, 0, canvas.width, canvas.height);

                ctx.strokeStyle = '#facc15';
                ctx.lineWidth = 3;
                ctx.beginPath();
                
                for (let x = 0; x < canvas.width; x++) {
                    const y = canvas.height/2 + 50 * Math.sin((x / lambda * 2 * Math.PI) - (time * 0.1));
                    if (x === 0) ctx.moveTo(x, y);
                    else ctx.lineTo(x, y);
                }
                ctx.stroke();

                // Info
                ctx.fillStyle = '#fff';
                ctx.font = '14px monospace';
                ctx.fillText(`v=${v}m/s`, 10, 30);
                ctx.fillText(`f=${f}Hz`, 10, 50);
                ctx.fillText(`λ=${lambda.toFixed(3)}m`, 10, 70);

                time++;
                animationId = requestAnimationFrame(animate);
            }
            animate();
        }

        // Keyboard Support
        document.addEventListener('keydown', (e) => {
            const key = e.key;
            if (/[0-9.]/.test(key)) insertVal(key);
            if (['+', '-', '*', '/', '(', ')', '^', '%'].includes(key)) insertVal(key);
            if (key === 'Enter') calculate();
            if (key === 'Backspace') deleteLast();
            if (key === 'Escape') clearAll();
        });
    </script>
</body>
</html>
حاسبة فيزيائية ورياضية متطورة
