<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Paraguai - Controle de Estoque</title>
    <!-- Tailwind CSS -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- Google Fonts: Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700;800&display=swap" rel="stylesheet">
    <!-- Font Awesome for icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.5.1/css/all.min.css">
    <!-- Chart.js for charts -->
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body { 
            font-family: 'Inter', sans-serif; 
            background-color: #020617; /* bg-slate-950 */
            color: #e2e8f0; /* slate-200 */
        }
        @keyframes spin { to { transform: rotate(360deg); } }
        .loader { 
            border-top-color: #3b82f6; /* border-blue-600 */
            animation: spin 1s linear infinite; 
        }
        input, select, textarea {
            background-color: #1e293b; /* bg-slate-800 */
            border-color: #334155; /* border-slate-700 */
        }
        input:disabled, select:disabled, textarea:disabled {
            background-color: #0f172a;
            cursor: not-allowed;
            opacity: 0.7;
        }
        input[readonly] {
            background-color: #0f172a; /* bg-slate-900 */
            cursor: not-allowed;
        }
        input[type="date"]::-webkit-calendar-picker-indicator { 
            filter: invert(0.8) brightness(1.2);
            cursor: pointer;
        }
        .nav-button.active { 
            background-color: #2563eb; /* bg-blue-600 */
            color: white; 
            box-shadow: 0 4px 14px 0 rgb(37 99 235 / 39%);
        }
        .nav-button { 
            transition: all 0.3s ease; 
        }
        .card {
            background-color: rgba(30, 41, 59, 0.8); /* bg-slate-800 com transparência */
            backdrop-filter: blur(12px);
            border: 1px solid rgba(51, 65, 85, 0.5); /* border-slate-700 com transparência */
            border-radius: 0.75rem; /* rounded-xl */
            box-shadow: 0 10px 25px -5px rgba(0,0,0,0.2), 0 8px 10px -6px rgba(0,0,0,0.2);
        }
        .main-button {
            background: linear-gradient(to right, #2563eb, #3b82f6);
            transition: all 0.3s ease;
            box-shadow: 0 4px 14px 0 rgb(37 99 235 / 39%);
        }
        .main-button:hover {
            transform: translateY(-2px);
            box-shadow: 0 6px 20px 0 rgb(37 99 235 / 45%);
        }
        .main-button:disabled {
             background: #334155;
             cursor: not-allowed;
             transform: none;
             box-shadow: none;
        }
        select {
             -webkit-appearance: none;
            -moz-appearance: none;
            appearance: none;
            background-image: url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='%2394a3b8' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e");
            background-repeat: no-repeat;
            background-position: right 0.7rem center;
            background-size: 1.2em;
            padding-right: 2.5rem;
        }
        .title-gradient {
            background: -webkit-linear-gradient(#eee, #a7c0ff);
            -webkit-background-clip: text;
            -webkit-text-fill-color: transparent;
        }
    </style>
</head>
<body class="text-slate-200">

    <div class="container mx-auto p-4 md:p-8 max-w-7xl">
        <header class="text-center mb-10">
            <h1 class="title-gradient text-4xl md:text-5xl font-extrabold text-white">Paraguai - Controle de Estoque</h1>
        </header>

        <!-- Navegação Principal -->
        <nav class="flex justify-center mb-10 bg-slate-800/80 backdrop-blur-sm p-2 rounded-xl max-w-2xl mx-auto shadow-lg border border-slate-700/50">
            <button id="nav-dashboard" class="nav-button active flex-1 text-center font-semibold py-2.5 px-4 rounded-lg">
                <i class="fa-solid fa-chart-pie mr-2"></i>Dashboard
            </button>
            <button id="nav-inventory" class="nav-button flex-1 text-center font-semibold py-2.5 px-4 rounded-lg">
                <i class="fa-solid fa-warehouse mr-2"></i>Estoque
            </button>
            <button id="nav-stock" class="nav-button flex-1 text-center font-semibold py-2.5 px-4 rounded-lg">
                <i class="fa-solid fa-plus mr-2"></i>Estocar
            </button>
            <button id="nav-withdraw" class="nav-button flex-1 text-center font-semibold py-2.5 px-4 rounded-lg">
                <i class="fa-solid fa-truck-ramp-box mr-2"></i>Retirar
            </button>
            <button id="nav-import" class="nav-button flex-1 text-center font-semibold py-2.5 px-4 rounded-lg">
                <i class="fa-solid fa-upload mr-2"></i>Carga Inicial
            </button>
        </nav>
        
        <!-- View: Dashboard (Padrão) -->
        <div id="dashboard-view">
            <div id="loader" class="flex justify-center items-center my-12">
                <div class="loader ease-linear rounded-full border-4 border-t-4 h-14 w-14"></div>
            </div>
            <div id="dashboard-content" class="hidden">
                 <!-- Estado Vazio -->
                <div id="dashboard-empty-state" class="hidden text-center card p-10">
                    <i class="fas fa-box-open text-5xl text-slate-500 mb-4"></i>
                    <h3 class="text-2xl font-bold text-white mb-2">Seu estoque está vazio</h3>
                    <p class="text-slate-400 mb-6">Comece a estocar ou faça uma carga inicial de dados.</p>
                    <div class="flex gap-4">
                        <button id="go-to-stock-button" class="main-button text-white font-bold py-2 px-6 rounded-md flex-1">
                            <i class="fas fa-plus mr-2"></i>Estocar um Item
                        </button>
                        <button id="go-to-import-button" class="bg-teal-600 hover:bg-teal-700 text-white font-bold py-2 px-6 rounded-md flex-1">
                            <i class="fas fa-upload mr-2"></i>Carga Inicial
                        </button>
                    </div>
                </div>
                <!-- Conteúdo com Dados -->
                <div id="dashboard-data-state" class="hidden">
                    <div class="grid grid-cols-1 md:grid-cols-3 gap-6 mb-6">
                        <div class="card p-6 flex items-center">
                            <i class="fa-solid fa-tags text-blue-400 text-4xl mr-6"></i>
                            <div>
                                <p class="text-slate-400 text-sm font-medium">SKUs em Estoque</p>
                                <p id="total-skus" class="text-3xl font-bold text-white">0</p>
                            </div>
                        </div>
                        <div class="card p-6 flex items-center">
                            <i class="fa-solid fa-boxes-stacked text-blue-400 text-4xl mr-6"></i>
                            <div>
                                <p class="text-slate-400 text-sm font-medium">Total de Caixas</p>
                                <p id="total-boxes" class="text-3xl font-bold text-white">0</p>
                            </div>
                        </div>
                        <div class="card p-6 flex items-center">
                            <i class="fa-solid fa-grip text-blue-400 text-4xl mr-6"></i>
                            <div>
                                <p class="text-slate-400 text-sm font-medium">Vagas em Uso</p>
                                <p id="vagas-em-uso" class="text-3xl font-bold text-white">0 / 0</p>
                            </div>
                        </div>
                    </div>
                    <div class="card p-6 lg:p-8">
                        <h2 class="text-2xl font-bold mb-6 text-white">Total de Caixas por Produto</h2>
                        <canvas id="products-chart"></canvas>
                    </div>
                </div>
            </div>
        </div>

        <!-- View: Estoque -->
        <div id="inventory-view" class="hidden">
            <div class="card p-6 lg:p-8">
                 <h2 class="text-3xl font-bold mb-6 text-white">Estoque Atual</h2>
                 <div class="overflow-x-auto">
                    <table class="min-w-full text-left">
                        <thead class="border-b border-slate-700">
                            <tr>
                                <th class="p-4 text-sm font-semibold text-slate-400 uppercase tracking-wider">Vaga</th>
                                <th class="p-4 text-sm font-semibold text-slate-400 uppercase tracking-wider">Código</th>
                                <th class="p-4 text-sm font-semibold text-slate-400 uppercase tracking-wider">Descrição</th>
                                <th class="p-4 text-sm font-semibold text-slate-400 uppercase tracking-wider">Qtd.</th>
                                <th class="p-4 text-sm font-semibold text-slate-400 uppercase tracking-wider">Lote</th>
                                <th class="p-4 text-sm font-semibold text-slate-400 uppercase tracking-wider">Validade</th>
                            </tr>
                        </thead>
                        <tbody id="pallet-list" class="divide-y divide-slate-700"></tbody>
                    </table>
                </div>
            </div>
        </div>

        <!-- View: Estocar -->
        <div id="stock-view" class="hidden">
            <div class="card p-6 lg:p-8 max-w-4xl mx-auto">
                <h2 class="text-3xl font-bold mb-6 text-white">Estocar Novo Palete</h2>
                <p class="text-slate-400 mb-6 -mt-4">Selecione uma vaga para um único palete ou deixe em branco para alocação automática.</p>
                <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <select id="location-input" title="Vaga Disponível (Opcional)" class="text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" disabled>
                         <option value="">Carregando vagas...</option>
                    </select>
                    <input type="text" id="code-input" placeholder="Código do Produto" class="text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
                    <input type="text" id="description-input" placeholder="Descrição (automática)" class="md:col-span-2 text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" readonly/>
                    <input type="number" id="quantity-input" placeholder="Quantidade" min="1" class="text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
                    <input type="text" id="batch-input" placeholder="Lote" class="text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
                    <input type="date" id="expiration-input" title="Data de Validade" class="md:col-span-2 text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
                </div>
                <button id="add-pallet-button" class="mt-6 w-full main-button text-white font-bold py-3 px-4 rounded-md" disabled>
                    Adicionar ao Estoque
                </button>
            </div>
        </div>

        <!-- View: Retirar -->
        <div id="withdraw-view" class="hidden">
            <div class="card p-6 lg:p-8 mb-8 max-w-4xl mx-auto">
                <h2 class="text-3xl font-bold mb-6 text-white">Montar Lista de Retirada</h2>
                <div class="grid grid-cols-1 md:grid-cols-4 gap-4 items-end">
                    <input type="text" id="withdraw-code-input" placeholder="Código do Produto" class="md:col-span-2 text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
                    <input type="number" id="withdraw-quantity-input" placeholder="Quantidade" min="1" class="text-white border rounded-md p-3 focus:ring-2 focus:ring-blue-500 focus:outline-none" />
                    <button id="add-to-withdraw-list-button" class="bg-indigo-600 hover:bg-indigo-700 text-white font-bold py-3 px-4 rounded-md transition h-full">
                        <i class="fas fa-plus mr-2"></i>Adicionar
                    </button>
                </div>
            </div>
            <div id="withdraw-request-list-section" class="card p-6 lg:p-8 mb-8 max-w-4xl mx-auto">
                <h3 class="text-2xl font-bold mb-4 text-white">Itens para Retirar</h3>
                <ul id="withdraw-request-list" class="divide-y divide-slate-700">
                    <li id="empty-withdraw-list-item" class="text-center py-4 text-slate-400">Nenhum item na lista.</li>
                </ul>
                <button id="generate-list-button" class="hidden mt-6 w-full bg-green-600 hover:bg-green-700 text-white font-bold py-3 px-4 rounded-md transition duration-300">
                    Gerar Lista de Coleta
                </button>
            </div>
            <div id="pick-list-section" class="hidden card p-6 lg:p-8 w-full max-w-4xl mx-auto">
                 <h3 class="text-2xl font-bold mb-6 text-center text-white">Lista de Vagas para Coleta</h3>
                 <div id="pick-list-container" class="grid grid-cols-2 sm:grid-cols-3 md:grid-cols-4 lg:grid-cols-5 gap-4 text-center"></div>
                 <div id="pick-list-spinner" class="hidden flex justify-center items-center my-4">
                    <div class="loader ease-linear rounded-full border-4 border-t-4 h-8 w-8"></div>
                 </div>
                 <div id="pick-list-actions" class="hidden mt-8 flex flex-col md:flex-row gap-4">
                    <button id="print-pick-list-button" class="w-full md:w-auto flex-1 bg-gray-600 hover:bg-gray-700 text-white font-bold py-3 px-4 rounded-md transition duration-300">
                        <i class="fas fa-print mr-2"></i>Imprimir Lista
                    </button>
                    <button id="confirm-withdrawal-button" class="w-full md:w-auto flex-1 bg-red-600 hover:bg-red-700 text-white font-bold py-3 px-4 rounded-md transition duration-300">
                        Confirmar Retirada
                    </button>
                </div>
            </div>
        </div>
        
        <!-- View: Carga Inicial -->
        <div id="import-view" class="hidden">
             <div class="card p-6 lg:p-8 max-w-4xl mx-auto">
                <h2 class="text-3xl font-bold mb-4 text-white">Carga Inicial em Massa</h2>
                 <p class="text-slate-400 mb-2">Cole seus dados de estoque no campo abaixo. Use uma linha para cada palete.</p>
                 <p class="text-slate-400 mb-6">Formato: <code class="bg-slate-900 px-2 py-1 rounded">VAGA CODIGO QUANTIDADE VALIDADE(M/D/AAAA) LOTE</code></p>
                 <textarea id="import-data" class="w-full h-64 p-3 border rounded-md bg-slate-900 font-mono text-sm" placeholder="Ex: G.06.1.7 10329088 80 5/19/2027 139"></textarea>
                 <button id="import-button" class="mt-6 w-full main-button text-white font-bold py-3 px-4 rounded-md">
                    <i class="fas fa-upload mr-2"></i>Importar Dados
                </button>
            </div>
        </div>

        <!-- Modal de Mensagem -->
        <div id="message-modal" class="hidden fixed inset-0 bg-black bg-opacity-80 flex justify-center items-center p-4 z-50">
            <div class="card p-6 w-full max-w-sm text-center">
                <p id="modal-message" class="text-lg mb-6"></p>
                <button id="modal-close-button" class="main-button text-white font-bold py-2 px-8 rounded-md">OK</button>
            </div>
        </div>
    </div>
    
    <!-- Footer/Signature -->
    <footer class="text-center mt-12 mb-6">
        <p class="text-xs text-slate-500">GuilhermeBorges</p>
    </footer>


    <script type="module">
        import { initializeApp } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-app.js";
        import { getFirestore, collection, addDoc, onSnapshot, deleteDoc, doc, query, where, getDocs, Timestamp, updateDoc, writeBatch } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-firestore.js";
        import { getAuth, signInAnonymously, onAuthStateChanged, signInWithCustomToken } from "https://www.gstatic.com/firebasejs/11.6.1/firebase-auth.js";

        const firebaseConfig = typeof __firebase_config !== 'undefined' ? JSON.parse(__firebase_config) : { apiKey: "YOUR_API_KEY", authDomain: "YOUR_AUTH_DOMAIN", projectId: "YOUR_PROJECT_ID" };
        const appId = typeof __app_id !== 'undefined' ? __app_id : 'paraguai-estoque-app';

        const app = initializeApp(firebaseConfig);
        const db = getFirestore(app);
        const auth = getAuth(app);

        // --- Constantes e Variáveis Globais ---
        const SHARED_STOCK_ID = 'Guilherme'; 
        const PALLET_COLLECTION_NAME = `pallets_${SHARED_STOCK_ID}`; 
        const MASTER_LOCATIONS = (() => {
            const locations = [];
            const levels = [7, 6, 5, 4];
            for(const level of levels) {
                for (let i = 1; i <= 70; i++) {
                    locations.push(`G.06.${i}.${level}`);
                }
            }
            return locations;
        })();
        const PRODUCT_CATALOG = {
            '10101000': { description: 'LIMPA LIMO L VERDE REFIL 12X540ML', max_qty: 50 }, '10101001': { description: 'LIMPA LIMO L VERDE GATILHO 12X540ML', max_qty: 50 }, '10194000': { description: 'AGUA SANITARIA ECO 12X1L', max_qty: 50 }, '10194005': { description: 'HIPOCLORITO DE SODIO ECO CLORO 4X5L', max_qty: 50 }, '10229000': { description: 'ALCOOL EM GEL URCA 12X500G', max_qty: 50 }, '10229001': { description: 'ALCOOL EM GEL URCA 12X400G', max_qty: 50 }, '10229002': { description: 'ALCOOL EM GEL URCA 2X4,4 KG', max_qty: 50 }, '10229003': { description: 'ALCOOL EM GEL URCA 500G', max_qty: 50 }, '10309001': { description: 'AMAC COOP DOCE AMANHACER 6X2L (SALMAO)', max_qty: 50 }, '10309002': { description: 'AMAC COOP BRISA SUAVE 6X2L (AZUL)', max_qty: 50 }, '10309003': { description: 'AMAC COOP SIMPLES JARDIM 6X2L (ROSA)', max_qty: 50 }, '10309004': { description: 'AMAC COOP MAGIA DAS FLORES 6X2L (BRANCO)', max_qty: 50 }, '10309005': { description: 'AMAC COOP BRISA SUAVE 2X5L (AZUL)', max_qty: 50 }, '10309006': { description: 'AMAC COOP SIMPLES JARDIM 2X5L (ROSA)', max_qty: 50 }, '10323011': { description: 'AMAC RUTH CARE BABY AZUL 9X1L', max_qty: 50 }, '10323012': { description: 'AMAC RUTH CARE BABY BRANCO 9X1L', max_qty: 50 }, '10323016': { description: 'AMAC RUTH CARE DOCES SONHOS 12X1L (AZUL)', max_qty: 50 }, '10323017': { description: 'AMAC RUTH CARE COLINHO DA VOVO 12X1L', max_qty: 50 }, '10328005': { description: 'AMAC URCA LAVANDA 6X2L (LILAS)', max_qty: 50 }, '10329001': { description: 'AMAC URCA BRISA AZUL 2X5L', max_qty: 50 }, '10329002': { description: 'AMAC URCA FLORAL ROSA 2X5L', max_qty: 50 }, '10329003': { description: 'AMAC URCA BRISA 6X2L (AZUL)', max_qty: 50 }, '10329004': { description: 'AMAC URCA FLORAL 6X2L (ROSA)', max_qty: 50 }, '10329005': { description: 'AMAC URCA NATURAL VERDE 6X2L', max_qty: 50 }, '10329006': { description: 'AMAC URCA TERNURA 6X2L (BRANCO)', max_qty: 50 }, '10329007': { description: 'AMAC URCA PAIXÃO SEDUTORA 6X2L (VERMELHO)', max_qty: 50 }, '10329010': { description: 'AMAC URCA BRISA 6X2L (AZUL) L2L P1,8L', max_qty: 50 }, '10329011': { description: 'AMAC URCA ESTACAO LILAS 6X2L', max_qty: 50 }, '10329016': { description: 'AMAC URCA BRISA 2X5L (AZUL) L5L P4 5L', max_qty: 50 }, '10329017': { description: 'AMAC URCA BRISA AZUL 6X3L', max_qty: 50 }, '10329018': { description: 'AMAC URCA TERNURA BABY 6X3L (BRANCO)', max_qty: 50 }, '10329019': { description: 'AMAC URCA FLORAL L5P4,5 2X5L', max_qty: 50 }, '10329020': { description: 'AMAC CONCENTRADO URCA BRISA 12X1 (AZUL)', max_qty: 50 }, '10329021': { description: 'AMAC CONCENTRADO URCA PAIXÃO SEDUTORA 12X1 (VERDE)', max_qty: 50 }, '10329022': { description: 'AMAC CONCENTRADO URCA FLORAL BOUQUET 12X1L', max_qty: 50 }, '10329026': { description: 'AMAC URCA FLORAL ROSA 6X2 L L2 P1,8', max_qty: 50 }, '10329027': { description: 'AMAC CONCENTRADO URCA TERNURA 12X1 (BRANCO)', max_qty: 50 }, '10329029': { description: 'LR LIQUIDO URCA COCO', max_qty: 40 }, '10329030': { description: 'URCA FABRIC SOFTNER SPRING BREEZE (AZUL) 6X2L', max_qty: 50 }, '10329031': { description: 'URCA FABRIC SOFTNER FLORAL BOUQUET (ROSA) 6X2L', max_qty: 50 }, '10329032': { description: 'URCA FABRIC SOFTNER NATURAL INSPIRATION (VERDE) 6X2L', max_qty: 50 }, '10329033': { description: 'URCA FABRIC SOFTNER COCONUTS (BRANCO) 6X2L', max_qty: 50 }, '10329034': { description: 'AMAC URCA PAIXAO SEDUTORA 6X3L', max_qty: 50 }, '10329069': { description: 'AMAC CONC PAIXAO 6X1,5L', max_qty: 50 }, '10329070': { description: 'AMAC COMC URCA FLORAL 6X 1,5L', max_qty: 50 }, '10329088': { description: 'AMAC URCA LAVANDA 2X5L (LILAS)', max_qty: 80 }, '10337006': { description: 'AMAC BS TO CARINHO 6X2 (AZUL', max_qty: 50 }, '10337007': { description: 'AMAC BS TQ MACIEZ 6X2L (ROSA)', max_qty: 50 }, '10337008': { description: 'AMAC BS TQ ALEGRIA 6X2L (AMARELO)', max_qty: 50 }, '10337009': { description: 'AMAC BS TQ CUIDADE 6X2L (BRANCO)', max_qty: 50 }, '10337010': { description: 'AM BS TQ INFINITY CARE 6X2L', max_qty: 50 }, '10337011': { description: 'AMAC BS TQ ENVOLVENTE 6X2 (VERMELHO)', max_qty: 50 }, '10337012': { description: 'AM BS TQ SEDUTOR 6X2L (LILAS)', max_qty: 50 }, '10337013': { description: 'AMAC BS TQ CARINHO 2X5L', max_qty: 50 }, '10337014': { description: 'AM BS TQ SEDUTOR 2X5L', max_qty: 50 }, '10337015': { description: 'AMAC BS TQ CUIDADO 2X5L', max_qty: 50 }, '10337016': { description: 'AMAC BS TQ ENVOLVENTE 2X5L', max_qty: 50 }, '10337020': { description: 'AM BS TQ CARINHO -LEVE 5L E PAGUE 4,5L', max_qty: 50 }, '10337022': { description: 'AMAC BS TOQUE DE CARINHO L2L,PI,SL 62XL', max_qty: 50 }, '10337023': { description: 'AMAC BS CONC TQ CARINHO 12X500ML', max_qty: 50 }, '10337024': { description: 'AMAC BS CONC TQ ENVOLVENTE 12X500ML', max_qty: 50 }, '10337025': { description: 'AMAC BS CONC TQ SEDUTOR 12X500ML', max_qty: 50 }, '10337026': { description: 'AMAC BS CONC VELUDO AZUL 6X2L', max_qty: 50 }, '10337027': { description: 'AMAC BS TQ CARINHO 10 L', max_qty: 50 }, '10337030': { description: 'AMAC BS CARE INFANTIL LAVANDA E AMENDOA 12X1L', max_qty: 50 }, '10337031': { description: 'AMAC BS CARE INFANTIL CAMOMILA E FLOR 12X1L', max_qty: 50 }, '10337046': { description: 'AMAC BS CONC TOQUE DE CUIDADO 12X500ML', max_qty: 50 }, '10337049': { description: 'AMAC BS CONC TQ DE CARINHO 12X1L', max_qty: 50 }, '10337050': { description: 'AMAC BS CONC TQ DE CUIDADO 12X1L', max_qty: 50 }, '10337051': { description: 'AMAC BS CONC TQ ENVOLVENTE 12X1L', max_qty: 50 }, '10337052': { description: 'AMAC BS CONC TQ SEDUTOR 12X1L', max_qty: 50 }, '10337053': { description: 'AMAC BS TQ SEDUTOR 6X2L LEVE 2L E PAGUE 1,8L', max_qty: 50 }, '10337055': { description: 'AMAC BS INFINITY CARE 4X3L', max_qty: 50 }, '10337057': { description: 'AMAC BS INFINITY CARE MARSALA 4X3L', max_qty: 50 }, '10337058': { description: 'AMAC BS INFINITY CARE MARSALA 6X2L', max_qty: 50 }, '10337059': { description: 'AM BS CONC TQ DE CARINHO - AZUL 500ML', max_qty: 50 }, '10337060': { description: 'AM BS CONC TQ ENVOLVENTE - VERMELHO 500ML', max_qty: 50 }, '10337061': { description: 'AM BS CONC TQ SEDUTOR - ROXO 500ML', max_qty: 50 },
            '10337062': { description: 'AM BS COM TQ DE CUIDADO - BRANCO 500ML', max_qty: 50 }, '10337063': { description: 'AM BS CONC TQ DE CARINHO - AZUL 1L', max_qty: 50 }, '10337064': { description: 'AM BS CONC TQ DE CUIDADO - BRANCO 1L', max_qty: 50 }, '10337065': { description: 'AM BS CONC TQ DE ENVOLVENTE - VERMELHO 1L', max_qty: 50 }, '10337066': { description: 'AM BS CONC TQ SEDUTOR - LILAS 1L', max_qty: 50 }, '10337067': { description: 'AM BS CARE INFANTIL LAVANDA E AMENDOAS 1L', max_qty: 50 }, '10337068': { description: 'AM BS CARE INFANTIL CAMOMILA E FLOR DE ALGODAO 1L', max_qty: 50 }, '10337085': { description: 'AMACIANTE BABY SOFT TOQUE DE CARINHO REF 6X18L', max_qty: 50 }, '10337088': { description: 'AMAC COMC BS CARINHO E CUIDADO 6X1;5 (AZUL)', max_qty: 50 }, '10337089': { description: 'AMAC CONC BS PUREZA DELICADEZA 6X1,5L (BRANCO)', max_qty: 50 }, '10337090': { description: 'AMAC CONC DESEJO ENVOLVENTE 6X1L', max_qty: 50 }, '10337091': { description: 'AMAC CONC BS INSPIRAÇÃO 6X1,5L', max_qty: 50 }, '10342000': { description: 'AMAC PUBLIC FLORES DO CAMPO 6X2L', max_qty: 50 }, '10342001': { description: 'AMAC PUBLIC ROSA FLORAL 6X2L', max_qty: 50 }, '10342002': { description: 'AMAC PUBLIC FLORES DO CAMPO 2X5L', max_qty: 50 }, '10401003': { description: 'STOP MOFO AMAZON REFIL 12X500ML', max_qty: 50 }, '10401004': { description: 'STOP MOFO AMAZON GATILHO 12X500ML', max_qty: 50 }, '10401005': { description: 'LIMPA LIMO AMAZON REFIL 12X500ML', max_qty: 50 }, '10401006': { description: 'STOP MOFO AMAZON REFIL 500ML', max_qty: 50 }, '10401007': { description: 'STOP MOFO AMAZON GATILHO 12X500ML', max_qty: 50 }, '10609002': { description: 'DES COOP EUC FRESH 6X2L', max_qty: 50 }, '10609003': { description: 'DES COOP FLORAL 6X2L', max_qty: 50 }, '10609007': { description: 'DES COOP LAVANDA 6X2L', max_qty: 50 }, '10609008': { description: 'DES COOP LAVANDA 2X5L', max_qty: 50 }, '10609009': { description: 'DES COOP EUC FRESH 2X5L', max_qty: 50 }, '10628001': { description: 'DES UFE UFENOL 12X750ML', max_qty: 50 }, '10628002': { description: 'DES UFE UFENOL ORIGINAL 12X1L', max_qty: 50 }, '10628003': { description: 'DESINFETANTE UFENOL CITRICA 12X1L', max_qty: 50 }, '10628004': { description: 'DESINFETANTE UFENOL LAVANDA 12X1L', max_qty: 50 }, '10628005': { description: 'DESINFETANTE UFENOL TRADICIONAL 12X1L', max_qty: 50 }, '10629001': { description: 'DES URCA LAVANDA 6X2L', max_qty: 50 }, '10629002': { description: 'DES URCA EUCALIPTO 6X2L', max_qty: 50 }, '10629003': { description: 'DES URCA FLORAL 6X2L', max_qty: 50 }, '10629004': { description: 'DES URCA EUCALIPTOFRESH 6X2L', max_qty: 50 }, '10629005': { description: 'DES URCA PINHO 6X2L', max_qty: 50 }, '10629006': { description: 'DES URCA PINHO 2X5L', max_qty: 50 }, '10629007': { description: 'DES URCA LAVANDA 2X5L', max_qty: 50 }, '10629008': { description: 'DES URCA EUCALIPTOFRESH 12X500ML', max_qty: 50 }, '10629009': { description: 'DES URCA FLORAL 12X500ML', max_qty: 50 }, '10629011': { description: 'DES URCA LAVANDA 12X500ML', max_qty: 50 }, '10629012': { description: 'DES URCA PINHO 12X500ML', max_qty: 50 }, '10629015': { description: 'DES URCA LAVANDA 6X2L L2P1,8', max_qty: 50 }, '10629025': { description: 'DES URCA FLOR DE LAVANDA 6X2L', max_qty: 50 }, '10629026': { description: 'DES URCA FLOR DE LAVANDA 12X500ML', max_qty: 50 }, '10629027': { description: 'DES URCA FLORAL AZUL 6X2L L2P1,8', max_qty: 50 }, '10629029': { description: 'DES URCA PINHO 6X2 L2PG1,8L', max_qty: 50 }, '10629030': { description: 'DES URCA FLOR DE LAVANDA 2X5L', max_qty: 50 }, '10629045': { description: 'DES URCA LAVANDA 2X5L (LEVE5L E PAGUE 4,5L', max_qty: 50 }, '10639000': { description: 'DES SC EUCALIPTO 6X2L', max_qty: 50 }, '10639001': { description: 'DES SC LAVANDA 6X2L', max_qty: 50 }, '10639002': { description: 'DES SC FLORAL 6X2 (ROSA TR)', max_qty: 50 }, '10639004': { description: 'DES SC ROSA E JASMIM 6X2L (RO L)', max_qty: 50 }, '10639005': { description: 'DES SC FLORES DE LAVANDA 6X2L (LILAS L)', max_qty: 50 }, '10639007': { description: 'DES SC LAVANDA 2X5L', max_qty: 50 }, '10639008': { description: 'DES SC FLORAL 2X5L', max_qty: 50 }, '10642000': { description: 'DES PUBLIC LAVANDA 6X2L', max_qty: 50 }, '10642001': { description: 'DES PUBLIC PINHO 6X2L', max_qty: 50 }, '10694005': { description: 'DES ECO QUALY JASMIM 4X5L', max_qty: 50 }, '10694009': { description: 'DES ECO QUALY PEROLA 6X2L', max_qty: 50 }, '10694010': { description: 'DES ECO QUALY PEROLA 4X5L', max_qty: 50 }, '10694023': { description: 'DES ECO QUALY TALCO 2X5L', max_qty: 50 }, '10720000': { description: 'FAC QUALITA GATILHO 12X500ML', max_qty: 50 }, '10720001': { description: 'FAC QUALITA REFIL 12X500ML', max_qty: 50 }, '10720002': { description: 'FAC QUALITA 12X500ML POUCH', max_qty: 50 }, '10729002': { description: 'FACILITADOR DE PASSAR URCA 12X500ML', max_qty: 50 }, '10901005': { description: 'LAVA LOUCAS AMAZON NEUTRO 12X500ML', max_qty: 50 }, '10928000': { description: 'LAVA LOUCAS GEL UFE AGUA DE COCO 12X500G', max_qty: 50 }, '10928002': { description: 'LAVA LOUCAS GEL UFE COCO E LIMAO SICILIANO 12X500G', max_qty: 50 }, '10994002': { description: 'LAVA LOUÇAS ECOVILLE ZOOM NEUTRO 2X5L', max_qty: 50 }, '10996003': { description: 'LAVA LOUÇAS ECOVILLE ZOOM NEUTRO 24X500ML', max_qty: 50 }, '11001001': { description: 'LR PO AMAZON SACHE 20X1KG', max_qty: 50 }, '11001002': { description: 'LR PO AMAZ 6X2KG POTE', max_qty: 50 }, '11001015': { description: 'LR LIQUIDO COCO AMAZON 12X500ML', max_qty: 50 }, '11001017': { description: 'LR LIQUIDO COCO AMAZON 500ML', max_qty: 50 }, '11001018': { description: 'LR PO AMAZON 2KG POTE', max_qty: 50 }, '11009002': { description: 'LR LIQ COOP DELIC COCO 12X500ML', max_qty: 50 }, '11019001': { description: 'LR PO COCO POLAR CARTUCHO 12X500G', max_qty: 50 }, '11020013': { description: 'LR PO COCO QUALITA CARTUCHO 12X500G', max_qty: 50 }, '11020014': { description: 'LR PO COCO QUALITA SACHE 20X1KG', max_qty: 50 }, '11023002': { description: 'LR BABY SOFT CARE 12X1L', max_qty: 50 }, '11023003': { description: 'LR LIQUIDO COCO RUTH 12X500ML', max_qty: 50 }, '11023005': { description: 'LR LIQUIDO RUTH CARE BABY 9X1L', max_qty: 50 }, '11023006': { description: 'LR PO COCO RUTH CARTUCHO 12X500G', max_qty: 50 }, '11023008': { description: 'LR LIQUIDO COCO RUTH 12X1L', max_qty: 50 }, '11023013': { description: 'LR RUTH CARE XODOZINHO 12X1L', max_qty: 50 }, '11026001': { description: 'LR LIQUIDO SUMMER VERDE 12X1L', max_qty: 50 }, '11028001': { description: 'LR PO COCO UFE CARTUCHO 12X500G', max_qty: 50 }, '11028002': { description: 'LR LIQUIDO COCO UFE 12X500ML', max_qty: 50 }, '11028022': { description: 'LR LIQUIDO COCO UFE 12X1L', max_qty: 50 }, '11028023': { description: 'LR PO COCO UFE CARTUCHO 500G', max_qty: 50 }, '11028024': { description: 'LR LIQUIDO COCO UFE 500ML', max_qty: 50 }, '11028025': { description: 'LR LIQUIDO COCO UFE 1L', max_qty: 50 }, '11029001': { description: 'LR LIQUIDO COCO URCA 12X500ML', max_qty: 50 }, '11029002': { description: 'LR PO URCA MAXX SACHE 20X1KG', max_qty: 50 }, '11029003': { description: 'LR PO COCO URCA CARTUCHO 12X500G', max_qty: 50 }, '11029012': { description: 'LR PO COCO URCA 20X1KG SACHE', max_qty: 50 }, '11029021': { description: 'LR LIQUIDO URCA AZUL 12X1L', max_qty: 50 }, '11029022': { description: 'LR LIQUIDO URCA VERDE 12X1L', max_qty: 50 }, '11029023': { description: 'LR LIQUIDO URCA AZUL 2X5L', max_qty: 50 }, '11029024': { description: 'LR LIQUIDO URCA VERDE 2X5L', max_qty: 50 }, '11029029': { description: 'LR LIQUIDO COCO URCA 6X3L', max_qty: 40 }, '11029030': { description: 'LR LIQUIDO URCA AZUL 6X3L', max_qty: 50 }, '11029031': { description: 'LR LIQUIDO URCA VERDE 6X3L', max_qty: 40 }, '11029033': { description: 'LR PO URCA MAXX SACHE 4X5KG', max_qty: 50 }, '11029034': { description: 'LR PO URCA MAXX SACHE 6X3KG', max_qty: 50 }, '11029036': { description: 'LR PO URCA MAXX SACHE 6X3KG (BALDE)', max_qty: 50 }, '11029037': { description: 'LR PO URCA MAXX COCO SACHE 20X500G', max_qty: 50 }, '11029038': { description: 'LR PO URCA MAXX COCO SACHE 20X1KG', max_qty: 50 }, '11029039': { description: 'URCA LAUNDRY DETERGENT POWER 6X3KG', max_qty: 50 }, '11029040': { description: 'URCA LAUNDRY DETERGENT POWDER 20X1KG', max_qty: 50 }, '11029041': { description: 'URCA COCONUT LIQUID LAUNDRY DETERGENT 12X500ML', max_qty: 50 }, '11029042': { description: 'URCA COCONUT LIQUID LAUNDRY DETERGENT 6X3L', max_qty: 50 }, '11029043': { description: 'LR PO URCA MAXX LAVANDA SACHE 20X500G', max_qty: 50 }, '11029044': { description: 'LR PO URCA MAXX LAVANDA SACHE 20X1KG', max_qty: 50 }, '11029045': { description: 'LR LIQUIDO COCO URCA 12X1L', max_qty: 50 }, '11029046': { description: 'LR PO URCA MAXX SACHE 20X1KG LEVE GRATIS 100G', max_qty: 50 }, '11029047': { description: 'LR PO URCA MAXX SACHE 6X3KG LEVE GRATIS 300G', max_qty: 50 }, '11029048': { description: 'LR PO URCA MAXX SACHE 4X5KG LEVE GRATIS 500G', max_qty: 50 }, '11029071': { description: 'LR LIQUIDO AZUL (LEVE 3L, PAGUE 2L)', max_qty: 50 }, '11029072': { description: 'LR LIQUIDO URCA MAXX AZUL 2X5 L5 P4,5L', max_qty: 50 }, '11029074': { description: 'LR PO CASA E ROUPA 20X800G', max_qty: 50 }, '11029075': { description: 'LR PO CASA E ROUPA 10X1,6KG', max_qty: 50 }, '11029086': { description: 'LR PO URCA CONC. SACHE 20X800G', max_qty: 50 }, '11029087': { description: 'LR PO URCA CONC. LAVANDA SACHE 20X800G', max_qty: 50 }, '11029088': { description: 'LR PO URCA CONC. COCO SACHE 20X800G', max_qty: 50 }, '11029090': { description: 'LR PO URCA CONC. SACHE 6X2 4KG', max_qty: 50 }, '11029092': { description: 'LR PO URCA CONC. SACHE 4X4KG', max_qty: 50 }, '11037001': { description: 'LRD BS COCO 12X500ML', max_qty: 50 }, '11037009': { description: 'LR BS CARE INFANTIL LAVANDA E AMENDOA 12X1L', max_qty: 50 }, '11037010': { description: 'LAVA ROUPAS BABY SOFT COCO 12X1L', max_qty: 50 }, '11037012': { description: 'LAVA ROUPAS LIQUIDO BABY SOFT AZUL 12X1L', max_qty: 50 }, '11037013': { description: 'LR PO BS 20X1KG', max_qty: 50 }, '11037014': { description: 'LR PO BS 10X2KG - SACHE', max_qty: 50 }, '11037015': { description: 'LR PO BS 6X3KG - SACHE', max_qty: 50 }, '11037016': { description: 'LR PO BS 4X5KG - SACHE', max_qty: 50 }, '11037018': { description: 'LR LIQ BABY SOFT VERDE 2X5L', max_qty: 50 }, '11037019': { description: 'LAVA ROUPAS LIQUIDO BABY SOFT 4X3L', max_qty: 50 }, '11037020': { description: 'LAVA ROUPAS BABY SOFT INFINITY CARE 4X3L', max_qty: 50 }, '11037021': { description: 'LR LIQUIDO BS ROUPAS ESCURAS 12X1L', max_qty: 50 }, '11037022': { description: 'LR LIQUIDO BS ORIGINAL 12X1L', max_qty: 50 }, '11037023': { description: 'LR PO BS 20X1KG - SACHE', max_qty: 50 }, '11037024': { description: 'LAVA ROUPAS BABY SOFT AZUL 4X3L', max_qty: 50 }, '11037025': { description: 'LAVA ROUPAS LIQUIDO BABY SOFT LILAS 1L', max_qty: 50 }, '11037026': { description: 'LAVA ROUPAS LIQUIDO BABY SOFT VERDE 1L', max_qty: 50 }, '11037027': { description: 'LAVA ROUPAS LIQUIDO BABY SOFT AZUL 1L', max_qty: 50 }, '11037028': { description: 'LR BS CARE INFANTIL LAVANDA E AMENDOAS 1L', max_qty: 50 }, '11037053': { description: 'LR PO BS CONCENTRADO CARTUCHO 9X1,6KG', max_qty: 50 }, '11038000': { description: 'LAVA ROUPAS LIQUIDO BABY SOFT VERDE 12X1L', max_qty: 50 }, '11038002': { description: 'LAVA ROUPAS LIQUIDO BABY SOFT LILAS 12X1L', max_qty: 50 }, '11201002': { description: 'LIMPA VIDROS AMAZON REFIL 12X500ML', max_qty: 50 }, '11201003': { description: 'LIMPA VIDROS AMAZON GATILHO 12X500ML', max_qty: 50 }, '11201004': { description: 'LIMPA VIDROS AMAZON REFIL 500ML', max_qty: 50 }, '11201005': { description: 'LIMPA VIDROS AMAZON GATILHO 500ML', max_qty: 50 }, '11229004': { description: 'L VIDRO URCA 12X500ML SQUEEZE', max_qty: 50 }, '11290005': { description: 'LIMP PERF SUMMER HARMONIA 12X500ML', max_qty: 50 }, '11303001': { description: 'LIMP PERF URCA PURIFICANTE 12X1L AZ1', max_qty: 50 }, '11303002': { description: 'LIMP PERF URCA PURIFICANTE 12X500ML AZ1', max_qty: 50 }, '11303003': { description: 'LIMP PERF AROM AMOR LARANJA 12X1L', max_qty: 50 }, '11303004': { description: 'LIMP PERF URCA ENERGIZANTE 12X500ML PINK', max_qty: 50 }, '11303005': { description: 'LIMP PERF URCA ENERGIZANTE 12X1L PINK', max_qty: 50 }, '11303006': { description: 'LIMP PERF URCA REVIGORANTE 12X500ML VD1', max_qty: 50 }, '11303007': { description: 'LIMP PERF URCA RELAXANTE 12X1L LILAS', max_qty: 50 }, '11303008': { description: 'LIMP PERF URCA RELAXANTE 12X500ML LILAS', max_qty: 50 }, '11303009': { description: 'LIMP PERFUM AROMAS INSTINTO 12X1L ROSA', max_qty: 50 }, '11303011': { description: 'LIMP PERF URCA ESTIMULANTE 12X500ML VM1', max_qty: 50 }, '11303033': { description: 'LIMP PERF COOP NATUREZA SUAVE VERDE 12X500ML', max_qty: 50 }, '11303034': { description: 'LIMP PERF COOP INTENSIDADE FLORES VERMELHO 12X500ML', max_qty: 50 }, '11329006': { description: 'URCA ALL PURPOSE CLEANER HAPPY HOME (AZUL) 12X500ML', max_qty: 50 }, '11329007': { description: 'URCA ALL PURPOSE CLEANER SWEET LIFE (VERDE) 12X500ML', max_qty: 50 }, '11329008': { description: 'URCA ALL PURPOSE CLEANER HARMONY (LILAS) 12X500ML', max_qty: 50 }, '11329009': { description: 'URCA ALL PURPOSE CLEANER HAPPY HOME (AZUL) 12X1L', max_qty: 50 }, '11329010': { description: 'URCA ALL PURPOSE CLEANER SWEET LIFE (VERDE) 12X1L', max_qty: 50 }, '11329011': { description: 'URCA PURPOSE CLEANER HARMONY (LILAS) 12X1L', max_qty: 50 }, '11337012': { description: 'AMAC BS SEDUTOR 6X2L (ROXO)', max_qty: 50 }, '11342000': { description: 'LIMP PERF PUBLIC ALEGRIA 12X500ML', max_qty: 50 }, '11342001': { description: 'LIMP PERF PUBLIC HARMONIA 12X500ML', max_qty: 50 }, '11394000': { description: 'LIMPA PEDRA ECOVILLE 6X2L', max_qty: 50 }, '11394002': { description: 'LIMPA C/ ALCOOL ECOVILLE LAVANDA 6X2L', max_qty: 50 }, '11394003': { description: 'LIMPADOR C/ALCOOL LAVANDA 4X5L', max_qty: 50 }, '11394004': { description: 'LIMPADOR C/ALCOOL PEROLA 6X2L', max_qty: 50 }, '11497000': { description: 'VAMIX ALVEJANTE CLORO ATIVO PERFUMADO 4X5L', max_qty: 50 }, '11501005': { description: 'MULTIUSO AMAZON CONCENTRADO 12X1L', max_qty: 50 }, '11501006': { description: 'MULTIUSO AMAZON REFIL 12X500ML', max_qty: 50 }, '11501007': { description: 'MULTIUSO AMAZON GATILHO 12X500ML', max_qty: 50 }, '11501008': { description: 'LIMPA GRUDE AMAZON 40X50ML', max_qty: 50 }, '11501009': { description: 'MULTIUSO AMAZON CONCENTRADO 1L', max_qty: 50 }, '11501010': { description: 'MULTIUSO AMAZON REFIL 500ML', max_qty: 50 }, '11501011': { description: 'MULTIUSO AMAZON GATILHO 500ML', max_qty: 50 }, '11501012': { description: 'LIMPA GRUDE AMAZON 50ML', max_qty: 50 }, '11509000': { description: 'LIMP M USO COOP LAVANDA 12X500ML', max_qty: 50 }, '11509001': { description: 'LIMP M USO COOP HORTELA 12X500ML', max_qty: 50 }, '11520000': { description: 'M LIMPADOR QUALITA ORIGINAL AZUL 12X500ML', max_qty: 50 }, '11520002': { description: 'M LIMPADOR QUALITA ERVAS VERDE 12X500ML', max_qty: 50 }, '11520003': { description: 'M LIMPADOR QUALITA FLORES ROSA 12X500ML', max_qty: 50 }, '11520004': { description: 'M LIMPADOR QUALITA ERVAS VERDE 12X750ML', max_qty: 50 }, '11520005': { description: 'M LIMPADOR QUALITA ORIGINAL AZUL 12X750ML', max_qty: 50 }, '11529001': { description: 'MULTIUSO URCA MAXX LAVANDA AZUL 12X500ML', max_qty: 50 }, '11529019': { description: 'MULTIUSO URCA MAXX HORTELA VERDE 12X500ML', max_qty: 50 }, '11529032': { description: 'M LIMPADOR URCA MULTIACAO C/ALCOOL 12X500ML', max_qty: 50 }, '11529033': { description: 'DESENGORDURANTE URCA 12X500ML', max_qty: 50 }, '11529040': { description: 'M LIMPADOR URCA MAXX MISTO 4X3X500ML PACK', max_qty: 50 }, '11542000': { description: 'M LIMPADOR PUBLIC LAVANDA 12X500ML', max_qty: 50 }, '11542001': { description: 'M LIMPADOR PUBLIC TRADICIONAL 12X500ML', max_qty: 50 }, '11594001': { description: 'PULVERIZADOR 500ML ECOVILLE 12X500ML EMBALAGEM', max_qty: 50 }, '11596000': { description: 'MULTIUSO ZOOM OX ATI SQUEZE 12X500ML', max_qty: 50 }, '11596003': { description: 'MULTIUSO ZOOM SQUEZE TRADICIONAL 4X5L', max_qty: 50 }, '11754001': { description: 'KIT GTEX PROMOCIONAL', max_qty: 50 }, '11920001': { description: 'SAB PASTA TRAD QUALITA 12X500G', max_qty: 50 }, '11922001': { description: 'SAB PASTOSO RIO 18X500G', max_qty: 50 }, '11922002': { description: 'SAB PASTOSO RIO 18X350G', max_qty: 50 }, '11923001': { description: 'SAB PASTA COCO RUTH 18X500G', max_qty: 50 }, '11928001': { description: 'SAB PASTA COCO UFE 18X500G', max_qty: 50 }, '11929001': { description: 'SAB PASTA TRAD URCA 12X500G', max_qty: 50 }, '11929002': { description: 'SAB PASTA TRAD URCA 24X200G', max_qty: 50 }, '11929006': { description: 'SAB PASTA COCO URCA 3EM1 12X500G', max_qty: 50 }, '11940000': { description: 'SAB PAS DIPOL NEUT 12X500G', max_qty: 50 }, '12001001': { description: 'SAB PED AMAZ 20X5X100G', max_qty: 50 }, '12009000': { description: 'SAB PED COCO COOP 10X5X200G', max_qty: 50 }, '12020001': { description: 'SAB PED QUALITA ORI 20X5X200G', max_qty: 50 }, '12020004': { description: 'SAB PED COCO QUALITA FLOW PACK 50X200G', max_qty: 50 }, '12020005': { description: 'SAB PED COCO QUALITA 10X5X200G', max_qty: 50 }, '12023005': { description: 'SAB PED COCO RUTH 10X5X200G', max_qty: 50 }, '12023006': { description: 'SAB PED RUTH 50X200G F PACK', max_qty: 50 }, '12023007': { description: 'SAB PED RUTH 120X100 GRAN', max_qty: 50 }, '12023008': { description: 'SAB PED COCO RUTH GRANEL 10KG', max_qty: 50 }, '12023010': { description: 'SAB PED COCO RUTH FLOW PACK 100X100G', max_qty: 50 }, '12023011': { description: 'SAB PED RUTH 20X5X100G', max_qty: 50 }, '12023013': { description: 'SAB PED COCO RUTH L5P4 20X5X100G', max_qty: 50 }, '12023014': { description: 'SAB PED COCO RUTH L5P4 10X5X200G', max_qty: 50 }, '12028002': { description: 'SAB PED COCO UFE FLOW PACK 50X200G', max_qty: 50 }, '12028003': { description: 'SAB PED COCO UFE 10X5X200G', max_qty: 50 }, '12028004': { description: 'SAB PED COCO UFE L5P4 10X5X200G', max_qty: 50 }, '12028006': { description: 'SAB PED COCO UFE 20X5X100G', max_qty: 50 }, '12028007': { description: 'SAB PED COCO UFE FLOW PACK 200G', max_qty: 50 }, '12028008': { description: 'SAB PED COCO UFE 200G', max_qty: 50 }, '12028009': { description: 'SAB PED COCO UFE 100G', max_qty: 50 }, '12028010': { description: 'SAB PED COCO UFE 20X5X90G', max_qty: 50 }, '12028011': { description: 'SAB PED COCO UFE FL PACK 50X180G', max_qty: 50 }, '12028012': { description: 'SAB PED COCO UFE 10X5X180G', max_qty: 50 }, '12029001': { description: 'SAB PED COCO URCA 10X5X200G', max_qty: 50 }, '12029002': { description: 'SAB PED COCO URCA 20X5X100G', max_qty: 50 }, '12029003': { description: 'SAB PED COCO URCA PREMIUM FPACK 50X200G', max_qty: 50 }, '12029009': { description: 'SAB PED COCO URCA L5P4 10X5X200G', max_qty: 50 }, '12029011': { description: 'SAB PED COCO URCA PREM FLPACK 100X100G', max_qty: 50 }, '12029018': { description: 'SAB PED COCO URCA 20X5X90G', max_qty: 50 }, '12029020': { description: 'SAB PED COCO URCA 10X5X180G', max_qty: 50 }, '12322000': { description: 'SAB PASTA CRISTAL ROSA 24X500G', max_qty: 50 }, '12601007': { description: 'T MANCHAS AMAZON BASTAO 8X10X100G', max_qty: 50 }, '12601008': { description: 'T MANCHAS AMAZON BASTAO MINI 4X30X18G', max_qty: 50 }, '12601009': { description: 'T MANCHAS AMAZON BASTAO MINI BL 40X100G+18G PACK', max_qty: 50 }, '12601010': { description: 'T MANCHAS AMAZON BASTAO BL 50X100G', max_qty: 50 }, '12601011': { description: 'T MANCHAS AMAZON BASTAO MINI BL 120X18G', max_qty: 50 }, '12601012': { description: 'T MANCHAS AMAZON BASTAO 4X10X100G', max_qty: 50 }, '12601013': { description: 'T MANCHAS AMAZON BASTAO MINI 2X30X18G', max_qty: 50 }, '12601014': { description: 'T MANCHAS AMAZON BASTAO 100G', max_qty: 50 }, '12601015': { description: 'T MANCHAS AMAZON BASTAO MINI 18G', max_qty: 50 }, '12601016': { description: 'T MANCHAS AMAZON BASTAO MINI BL 100G+18G PACK', max_qty: 50 }, '12601017': { description: 'T MANCHAS AMAZON BASTAO BL 100G', max_qty: 50 }, '12601018': { description: 'T MANCHAS AMAZON BASTAO MINI BL18G', max_qty: 50 }, '12601019': { description: 'T MANCHAS AMAZON BASTAO 2X10X100G', max_qty: 50 }, '12629000': { description: 'T MANCHAS URCA MAXX POUCH 12X500G', max_qty: 50 }, '12629002': { description: 'T MANCHAS URCA MAXX PO BRANCO 12X450G', max_qty: 50 }, '12629003': { description: 'T MANCHAS URCA MAXX PO VERMELHO 12X450G', max_qty: 50 }, '12630001': { description: 'T MANCHAS URCA MAXX 6X1,5L', max_qty: 50 }, '12637000': { description: 'TIRA MANCHAS B SOFT ROUP BRANC 12X450G', max_qty: 50 }, '12637001': { description: 'TIRA MANCHAS BS ROUPAS COLORIDAS 12X450G', max_qty: 50 }, '12637002': { description: 'T MANCHAS BS POUCH 12X500ML', max_qty: 50 }, '12637003': { description: 'T MANCHAS BS 6X1,5L', max_qty: 50 }, '12642000': { description: 'TIRA MANCHAS PUBLIC 6X1,5L', max_qty: 50 }, '12697002': { description: 'VAMIX ALVEJANTE S/ CLORO 6X2L', max_qty: 50 }, '12697003': { description: 'VAMIX ALVEJANTE SEM CLORO 4X5L', max_qty: 50 }, '13001001': { description: 'KIT LIMPEZA', max_qty: 50 }, '13001002': { description: 'KIT REFIL LIMPEZA', max_qty: 50 }, '13001003': { description: 'KIT CUIDADOS COM A COZINHA', max_qty: 50 }, '13001004': { description: 'KIT CUIDADOS COM A ROUPA', max_qty: 50 }, '13001005': { description: 'KIT FAMILIA CUIDADOS COMA ROUPA', max_qty: 50 }, '13001006': { description: 'KIT CRIANCA EM CASA', max_qty: 50 }, '13001007': { description: 'KIT STOP MOFO', max_qty: 50 }, '13001008': { description: 'KIT TIRA MANCHAS', max_qty: 50 }, '13029001': { description: 'KIT URCA', max_qty: 50 }, '13042000': { description: 'KIT M LIMPADOR PUBLIC LEV MAIS PAG MENOS', max_qty: 50 }, '13043001': { description: 'KIT HIPERCLEAN APLICADOR', max_qty: 50 }, '13043002': { description: 'KIT HIPERCLEAN PADRAO', max_qty: 50 }, '13043003': { description: 'KIT LENCO UMEDECIDO LAVANDA / 3UN DE LENCO UMED LAVANDA', max_qty: 50 }, '13043004': { description: 'KIT LENCO UMED AROMAS DE PARIS /3UN LENCO UMED PARIS', max_qty: 50 }, '13043005': { description: 'KIT LENCO UMEDECIDO FLORAL VERDE / 3UN LENCO FLOR VERDE', max_qty: 50 }, '13043006': { description: 'KIT LENCO UMED CEREJA E AVELA / 3UN LENC CER E AVELA', max_qty: 50 }, '13043007': { description: 'KIT LENCO UMED MULTIUSO 3 EM 1 / 12UN LENCO MULTIUSO', max_qty: 50 }, '13043008': { description: 'KIT LENCO UMED MULTIUSO 3 EM 1 / 4UN LENCO MULTIUSO', max_qty: 50 }, '13043009': { description: 'KIT LENCO UMEDECIDO LIMPA VIDROS / 4UN DE LENCO LIMPA VD', max_qty: 50 }, '13043010': { description: 'KIT REPOSICAO / MISTO', max_qty: 50 }, '13043011': { description: 'KIT FACILITE / 3UN LIMPA VIDROS, 1UN DUSTER', max_qty: 50 }, '13043012': { description: 'KIT LAVE SEM MEDO / 10UN LAVE SEM MEDO', max_qty: 50 }, '13043013': { description: 'KIT LAVE SEM MEDO / 4UN LAVE SEM MEDO', max_qty: 50 }, '13043014': { description: 'KIT DUSTER/ 3UN DUSTER', max_qty: 50 }, '13043015': { description: 'KIT HIPERCLEAN VS POEIRA', max_qty: 50 }, '13043016': { description: 'SUPER KIT PET', max_qty: 50 }, '13043017': { description: 'SUPER KIT OFFICE', max_qty: 50 }, '13043018': { description: 'KIT LENCO SECO ELETROSTATICO / 6UN LENCO ELETROSTATICO', max_qty: 50 }, '13043019': { description: 'SUPER KIT HIPERCLEAN', max_qty: 50 }, '13043020': { description: 'KIT HIPERCLEAN COMO EU QUERO', max_qty: 50 }, '13043021': { description: 'KIT FACILITE 3 UNIDADES DE LIMPA VIDROS SACHE / 1 DUSTER', max_qty: 50 }, '13043022': { description: 'KIT LAVE SEM MEDO 4 UNIDADES DE LAVE SEM MEDO', max_qty: 50 }, '13043023': { description: 'KIT LENCO UMEDECIDO LIMPA VIDROS 4 UNIDADES', max_qty: 50 }, '13043024': { description: 'SUPER KIT PET 1 VASSOURA RODO/ 1 LENCO ELETROSTATICO, 1 PCT', max_qty: 50 }, '13054000': { description: 'KIT SCARLAT 01', max_qty: 50 }, '13137000': { description: 'SEM PASSAR BABY SOFT 18X300ML', max_qty: 50 }, '13399000': { description: 'LIMPADOR C/BRILHO MAXIMUS LAVANDA 4X5L', max_qty: 50 }, '13401000': { description: 'LIMPA PISOS AMAZON REFIL 12X500ML', max_qty: 50 }, '13594001': { description: 'DETERGENTE ECOVILLE C/ AMONIACO NITRO 4X5L', max_qty: 50 }, '13594004': { description: 'DETERGENTE ECOVILLE DESINCRUTANTE ECOGRILL 4X5L', max_qty: 50 }, '14201001': { description: 'LIMPA TENIS AMAZON BASTAO BL 40X100G', max_qty: 50 }, '14201002': { description: 'LIMPA TENIS AMAZON BASTAO BL 100G', max_qty: 50 }, '14301004': { description: 'LIMPA PISOS AMAZON GATILHO 12X500ML', max_qty: 50 }, '14301005': { description: 'LIMPA PISOS AMAZON REFIL 12X500ML', max_qty: 50 }, '14301006': { description: 'LIMPA PISOS AMAZON REFIL 500ML', max_qty: 50 }, '14301007': { description: 'LIMPA PISOS AMAZON GATILHO 500ML', max_qty: 50 }, '14394000': { description: 'CERA LIQUIDA ECOFLOOR 6X2L', max_qty: 50 }, '14401001': { description: 'LIMPA TELA AMAZON 40X120ML', max_qty: 50 }, '14401002': { description: 'LIMPA TELA AMAZON 120ML', max_qty: 50 }, '14601000': { description: 'TIRA LIMO AMAZON REFIL 12X500ML', max_qty: 50 }, '14601001': { description: 'TIRA LIMO AMAZON GATILHO 12X500ML', max_qty: 50 }, '14601002': { description: 'TIRA LIMO AMAZON REFIL 500ML', max_qty: 50 }, '14601003': { description: 'TIRA LIMO AMAZON GATILHO 500ML', max_qty: 50 }, '14729000': { description: 'ULTRA URCA FABRIC SOFTNER MORNING BREEZE (AZUL) 12X1L', max_qty: 50 }, '14729001': { description: 'ULTRA URCA FABRIC SOFTNER COCONUTS (BRANCO) 12X1L', max_qty: 50 }, '14729002': { description: 'ULTRA URCA FABRIC SOFTNER MAGICAL PASSION (VERMELHO) 12X1L', max_qty: 50 }, '14729003': { description: 'ULTRA URCA FABRIC SOFTNER WILDFLOWERS (ROSA) 12X1L', max_qty: 50 }, '16129007': { description: 'LR PO URCA MAXX SACHE 20X1KG (ROSANOR)', max_qty: 50 }, '16129008': { description: 'LR PO URCA MAXX SACHE 20X500G', max_qty: 50 }, '16137001': { description: 'LAVA ROUPAS PO BABY SOFT 5 KG', max_qty: 50 }, '16229000': { description: 'URCA LAUNDRY DETERGENT (AZUL) 12X1L', max_qty: 50 }, '16229001': { description: 'URCA LAUNDRY DETERGENT (VERDE) 12X1L', max_qty: 50 }, '16294000': { description: 'LR ECO AMOR 5X4L', max_qty: 50 }, '16429000': { description: 'URCA PREMIUM LAUNDRY BAR SOAP 100X100G', max_qty: 50 }, '16594000': { description: 'AROM ECO QUALY AÇAI C/ CAMOMILA 12X140ML', max_qty: 50 }, '16601003': { description: 'DESENGORDURANTE AMAZON REFIL 12X500ML', max_qty: 50 }, '16601004': { description: 'DESENGORDURANTE AMAZON GATILHO 12X500ML', max_qty: 50 }, '16601005': { description: 'GEL LIMPA FORNO AMAZON 45X250G', max_qty: 50 }, '16601006': { description: 'DESENGORDURANTE AMAZON REFIL 500ML', max_qty: 50 }, '16601007': { description: 'DESENGORDURANTE AMAZON GATILHO 500ML', max_qty: 50 }, '16601008': { description: 'GEL LIMPA FORNO AMAZON 250G', max_qty: 50 }, '16729000': { description: 'ALCOOL LIQUIDO URCA 12X500ML', max_qty: 50 }, '16729001': { description: 'ALCOOL LIQUIDO URCA 12X1L', max_qty: 50 }, '17543007': { description: 'HIPERCLEAN KIT APLICADOR CX 6 UNIDADES (KA04)', max_qty: 50 }, '17543008': { description: 'HIPERCLEAN KIT APLICADOR CX 24 UNIDADES (KA02)', max_qty: 50 }, '17543009': { description: 'HIPERCLEAN KIT APLICADOR CX 12 UNIDADES (KA12)', max_qty: 50 }, '17543010': { description: 'HIPERCLEAN MULTIUSO 3EM1 CX 12 UNIDADES POTE (RU35)', max_qty: 50 }, '17543011': { description: 'HIPERCLEAN LIMPA VIDROS CX 12 UNIDADES POTE (LV12)', max_qty: 50 }, '17543012': { description: 'HIPERCLEAN LENCOS SECO ELETROSTATICO C/ 16 UNIDADES (RS01)', max_qty: 50 }, '17543013': { description: 'HIPERCLEAN LENCOS UMIDOS LAVANDA C/ 12 UNIDADES (RU01)', max_qty: 50 }, '17543014': { description: 'HIPERCLEAN LENCOS UMIDOS FLORAL VERDE C/ 12 UNIDADES (RU09)', max_qty: 50 }, '17543015': { description: 'HIPERCLEAN LENCOS UMIDOS AROMAS DE PARIS C/ 12 UNIDADES (RU0', max_qty: 50 }, '17543016': { description: 'HIPERCLEAN LENCOS UMIDOS MULTIUSO 3EM1 C/ 20 UNIDADES (RU31)', max_qty: 50 }, '17543017': { description: 'HIPERCLEAN LENCOS UMIDOS MULTIUSO 3 EM 1 POTE C/ 75 UNIDADES', max_qty: 50 }, '17543018': { description: 'HIPERCLEAN LENCOS UMIDOS LIMPA VIDROS C/ 20 UNIDADES (LV01)', max_qty: 50 }, '17543019': { description: 'HIPERCLEAN LENCOS UMIDOS LIMPA VIDROS EM POTE C/75 UNIDADES', max_qty: 50 }, '17543020': { description: 'HIPERCLEAN LENCOS UMIDOS CER E AVELA C/ 12 UNIDADES (RU20)', max_qty: 50 }, '17543021': { description: 'HIPERCLEAN KIT APLICADOR C/ 1 MOP E 2 LENCOS SECOS (KA01)', max_qty: 50 }, '17643000': { description: 'HIPERCLEAN LENCO SECO CX 24 UNIDADES (RS02)', max_qty: 50 }, '17643006': { description: 'HIPERCLEAN LENCO SECO CX 6 UNIDADES (RS04)', max_qty: 50 }, '17743000': { description: 'HIPERCLEAN LIMPA VIDROS CX 24 UNIDADES (LV02)', max_qty: 50 }, '17743001': { description: 'HIPERCLEAN LENCO UMIDO LAVANDA 24 UNIDADE (RU02)', max_qty: 50 }, '17743002': { description: 'HIPERCLEAN LENCO UMIDO FLORAL VERDE 24 UNIDADE (RU10)', max_qty: 50 }, '17743003': { description: 'HIPERCLEAN LENCO UMIDO AROMAS DE PARIS CX 24 UNIDADES (RU15)', max_qty: 50 }, '17743004': { description: 'HIPERCLEAN LENCO UMIDO MULTIUSO 3EM1 CX 24 UNIDADES', max_qty: 50 }, '17743005': { description: 'HIPERCLEAN LENCO UMIDO CER E AVELA CX 24 UNIDADES (RU21)', max_qty: 50 }, '17743006': { description: 'HIPERCLEAN LENCO UMIDO LAVANDA 6 UNIDADES (RU06)', max_qty: 50 }, '17743007': { description: 'HIPERCLEAN LENCO UMIDO FLORAL VERDE 6 UNIDADES (RU11)', max_qty: 50 }, '17743008': { description: 'HIPERCLEAN LENCO UMIDO AROMAS DE PARIS CX 6 UNIDADES (RU16)', max_qty: 50 }, '17743009': { description: 'HIPERCLEAN LENCO UMIDO CER E AVELA CX 6 UNIDADES (RU22)', max_qty: 50 }, '17843009': { description: 'HIPERCLEAN DUSTER CX 6 UNIDADES (DU04)', max_qty: 50 }, '17843010': { description: 'HIPERCLEAN DUSTER CX 24 UNIDADES (DU02)', max_qty: 50 }, '17843011': { description: 'HIPERCLEAN DUSTER C/ 1 HASTE FLEXIVEL E 5 DUSTERS (DU01)', max_qty: 50 }, '17943000': { description: 'HIPERCLEAN LAVE SEM MEDO CX 40 UNIDADES (LM40)', max_qty: 50 }, '17943001': { description: 'HIPERCLEAN LAVE SEM MEDO C/ 20 UNIDADES (LM04)', max_qty: 50 }
        };
        let withdrawalOperations = [];
        let pickListForDisplay = [];
        let withdrawalRequestList = [];
        let productsChart = null;
        let currentPalletsCache = []; 
        let isAuthReady = false; 

        // --- Elementos do DOM ---
        const views = {
            dashboard: document.getElementById('dashboard-view'),
            inventory: document.getElementById('inventory-view'),
            stock: document.getElementById('stock-view'),
            withdraw: document.getElementById('withdraw-view'),
            import: document.getElementById('import-view')
        };
        const navs = {
            dashboard: document.getElementById('nav-dashboard'),
            inventory: document.getElementById('nav-inventory'),
            stock: document.getElementById('nav-stock'),
            withdraw: document.getElementById('nav-withdraw'),
            import: document.getElementById('nav-import')
        };
        const loader = document.getElementById('loader');

        // Dashboard
        const dashboardContent = document.getElementById('dashboard-content');
        const dashboardEmptyState = document.getElementById('dashboard-empty-state');
        const dashboardDataState = document.getElementById('dashboard-data-state');
        const goToStockButton = document.getElementById('go-to-stock-button');
        const totalSkusEl = document.getElementById('total-skus');
        const totalBoxesEl = document.getElementById('total-boxes');
        const productsChartCanvas = document.getElementById('products-chart');

        // Estoque
        const palletList = document.getElementById('pallet-list');
        
        // Estocar
        const locationInput = document.getElementById('location-input');
        const codeInput = document.getElementById('code-input');
        const descriptionInput = document.getElementById('description-input');
        const quantityInput = document.getElementById('quantity-input');
        const batchInput = document.getElementById('batch-input');
        const expirationInput = document.getElementById('expiration-input');
        const addButton = document.getElementById('add-pallet-button');

        // Retirar
        const withdrawCodeInput = document.getElementById('withdraw-code-input');
        const withdrawQuantityInput = document.getElementById('withdraw-quantity-input');
        const addToWithdrawListButton = document.getElementById('add-to-withdraw-list-button');
        const withdrawRequestListElement = document.getElementById('withdraw-request-list');
        const emptyWithdrawListItem = document.getElementById('empty-withdraw-list-item');
        const generateListButton = document.getElementById('generate-list-button');
        const pickListSection = document.getElementById('pick-list-section');
        const pickListContainer = document.getElementById('pick-list-container');
        const pickListActions = document.getElementById('pick-list-actions');
        const printPickListButton = document.getElementById('print-pick-list-button');
        const confirmWithdrawalButton = document.getElementById('confirm-withdrawal-button');
        const pickListSpinner = document.getElementById('pick-list-spinner');

        // Importar
        const importDataTextarea = document.getElementById('import-data');
        const importButton = document.getElementById('import-button');
        const goToImportButton = document.getElementById('go-to-import-button');

        // Modal
        const messageModal = document.getElementById('message-modal');
        const modalMessage = document.getElementById('modal-message');
        const modalCloseButton = document.getElementById('modal-close-button');

        // --- LÓGICA PRINCIPAL ---
        
        async function main() {
            try {
                if (auth.currentUser) {
                    isAuthReady = true;
                } else if (typeof __initial_auth_token !== 'undefined' && __initial_auth_token) {
                    await signInWithCustomToken(auth, __initial_auth_token);
                    isAuthReady = true;
                } else {
                    await signInAnonymously(auth);
                    isAuthReady = true;
                }

                if (isAuthReady) {
                    setupPalletListener();
                } else {
                    throw new Error("A autenticação do Firebase falhou.");
                }

            } catch (error) {
                console.error("Authentication or Initialization Error:", error);
                showMessage("Erro de conexão. Não foi possível carregar os dados. Recarregue a página.");
                loader.classList.add('hidden');
            }
        }

        function setupPalletListener() {
            const palletsCollectionRef = collection(db, 'artifacts', appId, 'public', 'data', PALLET_COLLECTION_NAME);
            onSnapshot(query(palletsCollectionRef), snapshot => {
                currentPalletsCache = snapshot.docs.map(doc => ({id: doc.id, ...doc.data()}));
                
                loader.classList.add('hidden');
                dashboardContent.classList.remove('hidden');
                
                updateAvailableLocationsDropdown();
                renderPalletTable(currentPalletsCache);
                renderDashboard(currentPalletsCache);
            }, error => {
                console.error("Error fetching pallets: ", error);
                showMessage("Não foi possível carregar o estoque.");
                loader.classList.add('hidden');
            });
        }
        
        async function addPallet() {
            const selectedLocation = locationInput.value;
            const code = codeInput.value.trim();
            const totalQuantity = parseInt(quantityInput.value, 10);
            const batch = batchInput.value.trim();
            const expiration = expirationInput.value;
            const productInfo = PRODUCT_CATALOG[code];

            if (!productInfo || isNaN(totalQuantity) || !batch || !expiration) {
                return showMessage('Por favor, preencha todos os campos: Código, Quantidade, Lote e Validade.');
            }
            if (totalQuantity <= 0) return showMessage('A quantidade deve ser maior que zero.');
            if (!SHARED_STOCK_ID) return showMessage("Erro: ID de estoque compartilhado não definido.");

            addButton.disabled = true;
            const originalButtonText = addButton.innerHTML;
            addButton.innerHTML = `<div class="loader h-5 w-5 mx-auto border-2 border-t-2"></div>`;

            try {
                const palletsCollectionRef = collection(db, 'artifacts', appId, 'public', 'data', PALLET_COLLECTION_NAME);
                
                if (selectedLocation) {
                    const maxQtyPerPallet = productInfo.max_qty;
                    if (totalQuantity > maxQtyPerPallet) {
                        throw new Error(`Quantidade (${totalQuantity}) excede o limite de ${maxQtyPerPallet} para esta vaga. Para dividir a carga, não selecione uma vaga.`);
                    }
                    await addDoc(palletsCollectionRef, {
                        location: selectedLocation, code, description: productInfo.description,
                        quantity: totalQuantity, batch, expiration, entryDate: Timestamp.now()
                    });
                    showMessage(`Palete com ${totalQuantity} caixas estocado na vaga ${selectedLocation}.`, true);
                } else {
                    const occupiedLocations = currentPalletsCache.map(p => p.location);
                    const availableLocations = MASTER_LOCATIONS.filter(loc => !occupiedLocations.includes(loc));
                    
                    const maxQtyPerPallet = productInfo.max_qty;
                    const numFullPallets = Math.floor(totalQuantity / maxQtyPerPallet);
                    const remainingQty = totalQuantity % maxQtyPerPallet;
                    const locationsNeeded = numFullPallets + (remainingQty > 0 ? 1 : 0);

                    if (availableLocations.length < locationsNeeded) {
                        throw new Error(`Espaço insuficiente. Necessário: ${locationsNeeded} vagas, Disponível: ${availableLocations.length}.`);
                    }

                    const batchWrite = writeBatch(db);
                    const assignedLocations = availableLocations.slice(0, locationsNeeded);
                    
                    for (let i = 0; i < numFullPallets; i++) {
                        const newPalletRef = doc(palletsCollectionRef);
                        batchWrite.set(newPalletRef, {
                            location: assignedLocations[i], code, description: productInfo.description,
                            quantity: maxQtyPerPallet, batch, expiration, entryDate: Timestamp.now()
                        });
                    }

                    if (remainingQty > 0) {
                         const newPalletRef = doc(palletsCollectionRef);
                         batchWrite.set(newPalletRef, {
                            location: assignedLocations[numFullPallets], code, description: productInfo.description,
                            quantity: remainingQty, batch, expiration, entryDate: Timestamp.now()
                        });
                    }
                    
                    await batchWrite.commit();
                    showMessage(`${totalQuantity} caixas do código ${code} estocadas com sucesso em ${locationsNeeded} vaga(s).`, true);
                }

                [codeInput, descriptionInput, quantityInput, batchInput, expirationInput].forEach(input => input.value = '');
                locationInput.value = '';
                descriptionInput.readOnly = false;
                
            } catch (e) {
                console.error("Error adding pallet(s): ", e);
                showMessage(e.message || "Ocorreu um erro ao estocar.");
            } finally {
                addButton.disabled = false;
                addButton.innerHTML = originalButtonText;
            }
        }
        
        async function generateConsolidatedPickList() {
            if (withdrawalRequestList.length === 0) return showMessage("A lista de retirada está vazia.");
            if (!SHARED_STOCK_ID) return showMessage("Erro: ID de estoque compartilhado não definido.");

            generateListButton.disabled = true;
            pickListSection.classList.remove('hidden');
            pickListSpinner.classList.remove('hidden');
            pickListContainer.innerHTML = '';
            pickListActions.classList.add('hidden');
            
            withdrawalOperations = [];
            pickListForDisplay = [];

            try {
                for (const request of withdrawalRequestList) {
                    const palletsCollectionRef = collection(db, 'artifacts', appId, 'public', 'data', PALLET_COLLECTION_NAME);
                    const q = query(palletsCollectionRef, where("code", "==", request.code));
                    const querySnapshot = await getDocs(q);

                    let availablePallets = querySnapshot.docs.map(doc => ({ id: doc.id, ...doc.data() }));
                    
                    let totalQuantityAvailable = availablePallets.reduce((sum, pallet) => sum + pallet.quantity, 0);

                    if (totalQuantityAvailable < request.quantity) {
                        throw new Error(`Estoque insuficiente para o código ${request.code}. Disponível: ${totalQuantityAvailable}, Solicitado: ${request.quantity}.`);
                    }

                    let quantityToPick = request.quantity;
                    for (const pallet of availablePallets) {
                        if (quantityToPick <= 0) break;

                        const pickFromThisPallet = Math.min(pallet.quantity, quantityToPick);

                        if (pallet.quantity > pickFromThisPallet) {
                            const newQuantity = pallet.quantity - pickFromThisPallet;
                            withdrawalOperations.push({ action: 'update', docId: pallet.id, newQuantity: newQuantity });
                        } else {
                            withdrawalOperations.push({ action: 'delete', docId: pallet.id });
                        }

                        pickListForDisplay.push({ location: pallet.location, code: pallet.code, quantity: pickFromThisPallet });
                        quantityToPick -= pickFromThisPallet;
                    }
                }
                
                renderPickList(pickListForDisplay);
                pickListActions.classList.remove('hidden');

            } catch (error) {
                console.error("Error generating pick list:", error);
                showMessage(error.message);
                pickListSection.classList.add('hidden');
                withdrawalOperations = []; 
                pickListForDisplay = [];
            } finally {
                generateListButton.disabled = false;
                pickListSpinner.classList.add('hidden');
            }
        }

        async function confirmWithdrawal() {
            if (withdrawalOperations.length === 0 || !SHARED_STOCK_ID) return;
            
            confirmWithdrawalButton.disabled = true;
            printPickListButton.disabled = true;
            pickListSpinner.classList.remove('hidden');
            
            try {
                const batchWrite = writeBatch(db);
                withdrawalOperations.forEach(op => {
                    const palletDocRef = doc(db, 'artifacts', appId, 'public', 'data', PALLET_COLLECTION_NAME, op.docId);
                    if (op.action === 'delete') {
                        batchWrite.delete(palletDocRef);
                    } else if (op.action === 'update') {
                        batchWrite.update(palletDocRef, { quantity: op.newQuantity });
                    }
                });

                await batchWrite.commit();
                
                showMessage(`Retirada confirmada com sucesso!`, true);
                
                withdrawalOperations = [];
                withdrawalRequestList = [];
                pickListForDisplay = [];
                renderWithdrawalRequestList();
                pickListSection.classList.add('hidden');

            } catch (error) {
                console.error("Error confirming withdrawal:", error);
                showMessage("Ocorreu um erro ao confirmar a retirada.");
            } finally {
                confirmWithdrawalButton.disabled = false;
                printPickListButton.disabled = false;
                pickListSpinner.classList.add('hidden');
            }
        }
        
        async function importInitialStock() {
            const data = importDataTextarea.value.trim();
            if (!data) return showMessage("O campo de dados está vazio.");

            const lines = data.split('\n').filter(line => line.trim() !== '');
            if (lines.length === 0) return showMessage("Nenhuma linha de dados para importar.");

            importButton.disabled = true;
            const originalButtonText = importButton.innerHTML;
            importButton.innerHTML = `<div class="loader h-5 w-5 mx-auto border-2 border-t-2"></div>`;

            const batchWrite = writeBatch(db);
            const palletsCollectionRef = collection(db, 'artifacts', appId, 'public', 'data', PALLET_COLLECTION_NAME);
            let successCount = 0;
            let errorCount = 0;

            for (const line of lines) {
                const parts = line.split(/\s+/).map(p => p.trim());
                if (parts.length !== 5) {
                    errorCount++;
                    continue;
                }
                
                let [location, code, quantity, expirationStr, batch] = parts;
                const productInfo = PRODUCT_CATALOG[code];
                
                const dateParts = expirationStr.split('/');
                let expiration = expirationStr;
                if (dateParts.length === 3) {
                     // Converte M/D/YYYY para YYYY-MM-DD
                     expiration = `${dateParts[2]}-${dateParts[0].padStart(2, '0')}-${dateParts[1].padStart(2, '0')}`;
                }


                if (location && code && quantity && batch && expiration && productInfo) {
                    const newPalletRef = doc(palletsCollectionRef);
                    batchWrite.set(newPalletRef, {
                        location, code,
                        description: productInfo.description,
                        quantity: parseInt(quantity, 10),
                        batch, expiration, 
                        entryDate: Timestamp.now()
                    });
                    successCount++;
                } else {
                    errorCount++;
                }
            }

            try {
                await batchWrite.commit();
                let resultMessage = `${successCount} itens importados com sucesso!`;
                if(errorCount > 0) {
                    resultMessage += ` ${errorCount} linhas continham erros e foram ignoradas.`
                }
                showMessage(resultMessage, true);
                importDataTextarea.value = '';
                switchView('inventory');
            } catch(e) {
                console.error("Error importing data: ", e);
                showMessage("Ocorreu um erro ao importar os dados.");
            } finally {
                importButton.disabled = false;
                importButton.innerHTML = originalButtonText;
            }
        }

        // --- RENDERIZAÇÃO E UI ---
        
        function renderDashboard(pallets) {
            if (pallets.length === 0) {
                dashboardDataState.classList.add('hidden');
                dashboardEmptyState.classList.remove('hidden');
                return;
            }

            dashboardEmptyState.classList.add('hidden');
            dashboardDataState.classList.remove('hidden');

            const uniqueCodes = new Set();
            let totalBoxes = 0;
            const totalsByProduct = new Map();

            pallets.forEach(pallet => {
                const code = pallet.code.trim();
                uniqueCodes.add(code);
                const currentQty = totalsByProduct.get(code) || 0;
                totalsByProduct.set(code, currentQty + pallet.quantity);
                totalBoxes += pallet.quantity;
            });
            
            totalSkusEl.textContent = uniqueCodes.size;
            totalBoxesEl.textContent = totalBoxes.toLocaleString('pt-BR');
            document.getElementById('vagas-em-uso').textContent = `${pallets.length} / ${MASTER_LOCATIONS.length}`;
            
            if (productsChart) productsChart.destroy();

            const sortedProducts = [...totalsByProduct.entries()].sort((a, b) => b[1] - a[1]);

            productsChart = new Chart(productsChartCanvas, {
                type: 'bar',
                data: {
                    labels: sortedProducts.map(p => p[0]),
                    datasets: [{
                        label: 'Total de Caixas',
                        data: sortedProducts.map(p => p[1]),
                        backgroundColor: 'rgba(59, 130, 246, 0.7)',
                        borderColor: 'rgba(59, 130, 246, 1)',
                        borderWidth: 1,
                        borderRadius: 4
                    }]
                },
                options: {
                    responsive: true,
                    plugins: { legend: { display: false }, tooltip: { backgroundColor: '#1e293b', titleFont: { size: 14 }, bodyFont: { size: 12 }, padding: 10 } },
                    scales: {
                        y: { beginAtZero: true, grid: { color: 'rgba(51, 65, 85, 0.5)' }, ticks: { color: '#94a3b8' } },
                        x: { grid: { display: false }, ticks: { color: '#94a3b8' } }
                    }
                }
            });
        }
        
        function renderPalletTable(pallets) {
            palletList.innerHTML = '';
            pallets.sort((a, b) => a.location.localeCompare(b.location, undefined, {numeric: true}));
            if (pallets.length === 0) {
                palletList.innerHTML = `<tr><td colspan="6" class="text-center p-8 text-slate-400">Nenhum palete no estoque.</td></tr>`;
            } else {
                pallets.forEach(pallet => {
                    const tr = document.createElement('tr');
                    tr.className = 'hover:bg-slate-700/50';
                    const expirationDate = pallet.expiration ? new Date(pallet.expiration + 'T00:00:00').toLocaleDateString('pt-BR') : 'N/A';
                    tr.innerHTML = `
                        <td class="p-4 font-semibold text-slate-100">${pallet.location}</td>
                        <td class="p-4 text-slate-300">${pallet.code}</td>
                        <td class="p-4 text-slate-300">${pallet.description}</td>
                        <td class="p-4 text-slate-300">${pallet.quantity}</td>
                        <td class="p-4 text-slate-300">${pallet.batch}</td>
                        <td class="p-4 text-slate-300">${expirationDate}</td>
                    `;
                    palletList.appendChild(tr);
                });
            }
        }

        function updateAvailableLocationsDropdown() {
            const occupiedLocations = currentPalletsCache.map(p => p.location);
            const availableLocations = MASTER_LOCATIONS.filter(loc => !occupiedLocations.includes(loc));
            
            locationInput.innerHTML = '';
            
            const placeholder = document.createElement('option');
            placeholder.value = ""; // Valor vazio para alocação automática
            placeholder.textContent = "Alocação Automática (Opcional)";
            placeholder.selected = true;
            locationInput.appendChild(placeholder);

            availableLocations.forEach(loc => {
                const option = document.createElement('option');
                option.value = loc;
                option.textContent = loc;
                locationInput.appendChild(option);
            });
            
            locationInput.disabled = false;
            addButton.disabled = false;
        }

        function renderPickList(itemsToPick) {
            pickListContainer.innerHTML = '';
            itemsToPick.forEach(item => {
                const div = document.createElement('div');
                div.className = "bg-slate-700/50 p-4 rounded-lg border-2 border-blue-500 shadow-lg";
                div.innerHTML = `<span class="block text-sm text-slate-400">Coletar Vaga</span><span class="block text-3xl font-bold text-white">${item.location}</span><span class="text-xs text-slate-400">${item.code} (Qtd: ${item.quantity})</span>`;
                pickListContainer.appendChild(div);
            });
        }
        
        function addItemToWithdrawList() {
            const code = withdrawCodeInput.value.trim();
            const quantity = parseInt(withdrawQuantityInput.value, 10);
            if (!code || !quantity || quantity <= 0) return showMessage("Informe o código e uma quantidade válida.");
            withdrawalRequestList.push({ code, quantity });
            renderWithdrawalRequestList();
            withdrawCodeInput.value = '';
            withdrawQuantityInput.value = '';
            withdrawCodeInput.focus();
        }
        
        function renderWithdrawalRequestList() {
            if (withdrawalRequestList.length === 0) {
                withdrawRequestListElement.innerHTML = '';
                withdrawRequestListElement.appendChild(emptyWithdrawListItem);
                generateListButton.classList.add('hidden');
            } else {
                emptyWithdrawListItem.remove();
                withdrawRequestListElement.innerHTML = '';
                withdrawalRequestList.forEach((item, index) => {
                    const li = document.createElement('li');
                    li.className = 'flex justify-between items-center py-3';
                    li.innerHTML = `
                        <div>
                            <span class="font-semibold text-white">${item.code}</span>
                            <span class="text-sm text-slate-400 ml-2">Qtd: ${item.quantity}</span>
                        </div>
                        <button data-index="${index}" class="remove-withdraw-item-button text-red-500 hover:text-red-400 text-2xl font-bold leading-none">&times;</button>
                    `;
                    withdrawRequestListElement.appendChild(li);
                });
                generateListButton.classList.remove('hidden');
            }
        }
        
        function printPickList() {
            if (pickListForDisplay.length === 0) {
                return showMessage("Não há lista de coleta para imprimir.");
            }
            const now = new Date();
            const printDate = now.toLocaleDateString('pt-BR');
            const printTime = now.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });

            const printWindow = window.open('', '_blank');
            printWindow.document.write('<html><head><title>Lista de Coleta - Estoque Geral Paraguai</title>');
            printWindow.document.write(`<style>
                @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
                body { font-family: 'Inter', sans-serif; margin: 20px; color: #111; }
                .header { text-align: center; margin-bottom: 30px; border-bottom: 2px solid #ccc; padding-bottom: 15px; }
                .header h1 { margin: 0; font-size: 24px; }
                .header p { margin: 5px 0 0; font-size: 14px; color: #555; }
                table { width: 100%; border-collapse: collapse; margin-top: 20px; }
                th, td { border: 1px solid #ddd; padding: 12px; text-align: left; }
                th { background-color: #f2f2f2; font-weight: 700; font-size: 16px; }
                tr:nth-child(even) { background-color: #f9f9f9; }
                td { font-size: 15px; }
                .footer { margin-top: 40px; text-align: center; font-size: 12px; color: #777; }
                @media print {
                    body { margin: 0; }
                    .footer { position: fixed; bottom: 0; width: 100%; }
                }
            </style>`);
            
            printWindow.document.write('</head><body>');
            printWindow.document.write(`<div class="header">
                <h1>Estoque Geral Paraguai</h1>
                <p><strong>Data de Retirada:</strong> ${printDate} às ${printTime}</p>
                <p><strong>Retirada por:</strong> Guilherme</p>
            </div>`);

            printWindow.document.write('<table><thead><tr><th>Vaga</th><th>Código</th><th>Quantidade</th></tr></thead><tbody>');

            pickListForDisplay.forEach(item => {
                printWindow.document.write(`<tr><td>${item.location}</td><td>${item.code}</td><td>${item.quantity}</td></tr>`);
            });

            printWindow.document.write('</tbody></table>');
            printWindow.document.write(`<div class="footer"><p>Documento gerado pelo sistema de Controle de Estoque.</p></div>`);
            printWindow.document.write('</body></html>');
            printWindow.document.close();
            printWindow.focus();
            setTimeout(() => {
                printWindow.print();
                printWindow.close();
            }, 250);
        }


        function handleCodeInputChange() {
            const code = codeInput.value.trim();
            const productInfo = PRODUCT_CATALOG[code];

            if (productInfo) {
                descriptionInput.value = productInfo.description;
                descriptionInput.readOnly = true;
            } else {
                descriptionInput.value = '';
                descriptionInput.readOnly = false;
            }
        }

        function switchView(view) {
            Object.keys(views).forEach(key => {
                views[key].classList.add('hidden');
                navs[key].classList.remove('active');
            });
            views[view].classList.remove('hidden');
            navs[view].classList.add('active');
        }

        function showMessage(message, isSuccess = false) {
            modalMessage.textContent = message;
            modalMessage.className = `text-lg mb-6 ${isSuccess ? 'text-green-400' : 'text-yellow-400'}`;
            messageModal.classList.remove('hidden');
        }

        // --- EVENT LISTENERS ---
        
        addButton.addEventListener('click', addPallet);
        addToWithdrawListButton.addEventListener('click', addItemToWithdrawList);
        generateListButton.addEventListener('click', generateConsolidatedPickList);
        printPickListButton.addEventListener('click', printPickList);
        confirmWithdrawalButton.addEventListener('click', confirmWithdrawal);
        goToStockButton.addEventListener('click', () => switchView('stock'));
        goToImportButton.addEventListener('click', () => switchView('import'));
        codeInput.addEventListener('input', handleCodeInputChange);
        importButton.addEventListener('click', importInitialStock);

        withdrawRequestListElement.addEventListener('click', (e) => {
            if (e.target.classList.contains('remove-withdraw-item-button')) {
                const index = parseInt(e.target.dataset.index, 10);
                withdrawalRequestList.splice(index, 1);
                renderWithdrawalRequestList();
            }
        });
        
        Object.keys(navs).forEach(key => navs[key].addEventListener('click', () => switchView(key)));
        modalCloseButton.addEventListener('click', () => messageModal.classList.add('hidden'));

        // Inicia a aplicação
        main();
    </script>
</body>
</html>
