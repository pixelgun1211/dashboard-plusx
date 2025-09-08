<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Painel de Gestão - Consolidação</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.0.0-beta3/css/all.min.css" rel="stylesheet">
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@400;600;700&display=swap');
        body {
            font-family: 'Inter', sans-serif;
            background-color: #1a202c;
            color: #e2e8f0;
        }
        .container {
            max-width: 1400px;
        }
        .card {
            background-color: #2d3748;
            border-radius: 12px;
            padding: 1.5rem;
            box-shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
        }
        .chart-container {
            width: 100%;
            max-width: 300px;
            margin: 0 auto;
        }
    </style>
</head>
<body class="p-6">
    <div class="container mx-auto">
        <h1 class="text-3xl font-bold mb-8 text-center text-white">Painel de Gestão - Consolidação</h1>

        <!-- Seção de Métricas Principais e Controles Manuais -->
        <div class="grid md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8 text-center">
            <div class="card">
                <div class="text-sm font-semibold text-gray-400">Total de Pacotes (Meta)</div>
                <div class="flex items-center justify-center mt-2">
                    <input id="totalPacotesInput" type="number" class="w-24 text-center bg-gray-700 text-white rounded-md p-1" placeholder="5000">
                    <button id="setPacotesBtn" class="ml-2 bg-blue-600 hover:bg-blue-700 text-white font-bold py-1 px-2 rounded-md transition-transform transform hover:scale-105">Definir</button>
                </div>
            </div>
            <div class="card">
                <div class="text-sm font-semibold text-gray-400">Pacotes Descarregados</div>
                <div id="pacotesDescarregados" class="text-4xl font-bold mt-2 text-green-400">0</div>
            </div>
            <div class="card">
                <div class="text-sm font-semibold text-gray-400">Caminhões à Espera</div>
                <div id="caminhoesEspera" class="text-4xl font-bold mt-2 text-yellow-400">0</div>
            </div>
            <div class="card">
                <div class="text-sm font-semibold text-gray-400">Caminhões Descarregados</div>
                <div id="caminhoesDescarregados" class="text-4xl font-bold mt-2 text-red-400">0</div>
            </div>
        </div>
        
        <!-- Botão de Download em uma seção separada -->
        <div class="flex justify-center mb-8">
            <button id="downloadBtn" class="bg-green-600 hover:bg-green-700 text-white font-bold py-2 px-6 rounded-md transition-transform transform hover:scale-105">
                <i class="fas fa-download mr-2"></i> Baixar Dados
            </button>
        </div>

        <!-- Seção de Adição Manual de Caminhões -->
        <div class="card mb-8">
            <h2 class="text-xl font-semibold mb-4">Adicionar Novo Motorista</h2>
            <div class="flex flex-col sm:flex-row items-center justify-center space-y-4 sm:space-y-0 sm:space-x-4 flex-wrap">
                <select id="empresaSelect" class="bg-gray-700 text-white rounded-md p-2">
                    <option value="Prálog">Prálog</option>
                    <option value="Imediato">Imediato</option>
                    <option value="Hawk">Hawk</option>
                    <option value="Ontime">Ontime</option>
                </select>
                <input id="motoristaNomeInput" type="text" class="bg-gray-700 text-white rounded-md p-2" placeholder="Nome do Motorista" required>
                <input id="pacotesInput" type="number" class="bg-gray-700 text-white rounded-md p-2" placeholder="Nº de Pacotes" required>
                <button id="addMotoristaBtn" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-md transition-transform transform hover:scale-105">Adicionar Motorista</button>
            </div>
        </div>

        <!-- Seção Principal com Filas, Docas e Gráficos -->
        <div class="grid lg:grid-cols-3 gap-6">
            <!-- Coluna 1: Filas e Docas -->
            <div class="lg:col-span-2 space-y-6">
                <!-- Seção de Filas de Espera -->
                <div class="card">
                    <h2 class="text-xl font-semibold mb-4">Filas de Espera</h2>
                    <div class="grid md:grid-cols-2 gap-4" id="filasEsperaContainer">
                        <!-- As filas serão geradas dinamicamente aqui -->
                    </div>
                </div>

                <!-- Seção de Docas de Desembarque -->
                <div class="card">
                    <h2 class="text-xl font-semibold mb-4">Docas de Desembarque</h2>
                    <div class="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4" id="docasContainer">
                        <!-- As docas serão geradas dinamicamente aqui -->
                    </div>
                </div>
            </div>

            <!-- Coluna 2: Gráficos -->
            <div class="lg:col-span-1 space-y-6">
                <!-- Gráficos de rosca lado a lado -->
                <div class="card flex flex-col sm:flex-row items-center justify-around space-y-4 sm:space-y-0 sm:space-x-4">
                    <div class="chart-container">
                        <h2 class="text-lg font-semibold mb-2 text-center">Volume Descarregado</h2>
                        <canvas id="volumeChart"></canvas>
                    </div>
                    <div class="chart-container">
                        <h2 class="text-lg font-semibold mb-2 text-center">Pacotes por Empresa</h2>
                        <canvas id="pacotesEmpresaChart"></canvas>
                    </div>
                </div>
                <!-- Gráfico de barras -->
                <div class="card">
                    <h2 class="text-xl font-semibold mb-4 text-center">Entradas por Hora</h2>
                    <canvas id="horariosChart"></canvas>
                </div>
            </div>
        </div>
    </div>

    <!-- Modal de Alerta Customizado -->
    <div id="customAlert" class="hidden fixed inset-0 bg-gray-900 bg-opacity-75 flex items-center justify-center p-4">
        <div class="bg-gray-800 p-6 rounded-lg text-white text-center w-full max-w-sm">
            <p id="alertMessage" class="mb-4"></p>
            <button onclick="document.getElementById('customAlert').classList.add('hidden')" class="bg-blue-600 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded">OK</button>
        </div>
    </div>

    <script>
        // Use a função para mostrar alertas customizados em vez de alert()
        const showAlert = (message) => {
            const alertBox = document.getElementById('customAlert');
            document.getElementById('alertMessage').textContent = message;
            alertBox.classList.remove('hidden');
        };

        // Dados do aplicativo
        const empresas = ['Prálog', 'Imediato', 'Hawk', 'Ontime'];
        let docas = Array(9).fill(null);
        let filas = { Prálog: [], Imediato: [], Hawk: [], Ontime: [] };
        let pacotesDescarregados = localStorage.getItem('pacotesDescarregados') ? parseInt(localStorage.getItem('pacotesDescarregados')) : 0;
        let totalPacotesMeta = localStorage.getItem('totalPacotesMeta') ? parseInt(localStorage.getItem('totalPacotesMeta')) : 5000;
        let historicoEntradas = JSON.parse(localStorage.getItem('historicoEntradas')) || {};
        let historicoPacotesEmpresa = JSON.parse(localStorage.getItem('historicoPacotesEmpresa')) || {};
        let logDescarregamento = JSON.parse(localStorage.getItem('logDescarregamento')) || [];

        // Inicia o estado da aplicação
        const inicializarApp = () => {
            empresas.forEach(empresa => {
                if (!historicoPacotesEmpresa[empresa]) {
                    historicoPacotesEmpresa[empresa] = 0;
                }
            });
            document.getElementById('totalPacotesInput').value = totalPacotesMeta;
            atualizarUI();
            inicializarGraficos();
        };

        // Função para formatar tempo em HH:MM:SS
        const formatarTempo = (segundos) => {
            const h = Math.floor(segundos / 3600).toString().padStart(2, '0');
            const m = Math.floor((segundos % 3600) / 60).toString().padStart(2, '0');
            const s = Math.floor(segundos % 60).toString().padStart(2, '0');
            return `${h}:${m}:${s}`;
        };

        // Função para atualizar os cronômetros das docas ativas
        setInterval(() => {
            docas.forEach((caminhao, index) => {
                if (caminhao && caminhao.status !== 'finalizado') {
                    const timerElement = document.getElementById(`timer-${index}`);
                    if (timerElement) {
                        const tempoDecorrido = (new Date() - caminhao.horaInicio) / 1000;
                        timerElement.textContent = formatarTempo(tempoDecorrido);
                    }
                }
            });
        }, 1000);

        // Função para levar um caminhão da fila para uma doca
        const docarCaminhao = (filaEmpresa, indexCaminhao) => {
            // Encontrar uma doca livre
            const docaLivreIndex = docas.findIndex(doca => doca === null);
            if (docaLivreIndex === -1) {
                showAlert('Não há docas disponíveis!');
                return;
            }

            const caminhao = filas[filaEmpresa].splice(indexCaminhao, 1)[0];
            caminhao.doca = docaLivreIndex + 1;
            caminhao.horaInicio = new Date();
            docas[docaLivreIndex] = caminhao;
            
            atualizarUI();
        };

        // Função para finalizar o descarregamento de um caminhão
        const finalizarCaminhao = (docaIndex) => {
            const caminhao = docas[docaIndex];
            if (!caminhao) return;

            const tempoDecorrido = (new Date() - caminhao.horaInicio) / 1000;
            const tempoDecorridoFormatado = formatarTempo(tempoDecorrido);
            pacotesDescarregados += caminhao.pacotes;
            
            const horaEntrada = new Date(caminhao.horaInicio).getHours();
            historicoEntradas[horaEntrada] = (historicoEntradas[horaEntrada] || 0) + 1;

            historicoPacotesEmpresa[caminhao.empresa] += caminhao.pacotes;

            // Adiciona o evento ao log, incluindo a doca
            logDescarregamento.push({
                data: new Date().toLocaleDateString(),
                hora: new Date().toLocaleTimeString(),
                motorista: caminhao.nome,
                empresa: caminhao.empresa,
                pacotes: caminhao.pacotes,
                tempoNaDoca: tempoDecorridoFormatado,
                doca: caminhao.doca
            });

            docas[docaIndex] = { nome: caminhao.nome, empresa: caminhao.empresa, pacotes: caminhao.pacotes, status: 'finalizado' };

            localStorage.setItem('historicoEntradas', JSON.stringify(historicoEntradas));
            localStorage.setItem('historicoPacotesEmpresa', JSON.stringify(historicoPacotesEmpresa));
            localStorage.setItem('pacotesDescarregados', pacotesDescarregados);
            localStorage.setItem('logDescarregamento', JSON.stringify(logDescarregamento));

            atualizarUI();
            atualizarGraficos();
        };

        // Função para liberar uma doca finalizada
        const liberarDoca = (docaIndex) => {
            docas[docaIndex] = null;
            atualizarUI();
        }

        // Função para renderizar e atualizar a interface
        const atualizarUI = () => {
            const totalCaminhoesEspera = Object.values(filas).flat().length;
            const caminhoesFinalizados = docas.filter(c => c && c.status === 'finalizado').length;
            document.getElementById('pacotesDescarregados').textContent = pacotesDescarregados;
            document.getElementById('caminhoesEspera').textContent = totalCaminhoesEspera;
            document.getElementById('caminhoesDescarregados').textContent = caminhoesFinalizados;

            // Renderizar Filas
            const filasContainer = document.getElementById('filasEsperaContainer');
            filasContainer.innerHTML = '';
            empresas.forEach(empresa => {
                const filaHtml = `
                    <div class="card p-4">
                        <h3 class="font-semibold text-lg text-center mb-2">${empresa}</h3>
                        <ul id="fila-${empresa.toLowerCase()}" class="list-none space-y-2 max-h-48 overflow-y-auto">
                            ${filas[empresa].map((caminhao, index) => `
                                <li class="bg-gray-700 p-2 rounded flex justify-between items-center cursor-pointer hover:bg-gray-600" onclick="docarCaminhao('${empresa}', ${index})">
                                    <span class="text-sm">${caminhao.nome} - ${caminhao.pacotes} pacotes</span>
                                    <i class="fa-solid fa-truck text-yellow-500"></i>
                                </li>
                            `).join('')}
                        </ul>
                    </div>
                `;
                filasContainer.innerHTML += filaHtml;
            });

            // Renderizar Docas
            const docasContainer = document.getElementById('docasContainer');
            docasContainer.innerHTML = '';
            docas.forEach((caminhao, index) => {
                let docaContent;
                if (caminhao === null) {
                    docaContent = `
                        <div class="text-center text-gray-500">
                            <i class="fa-solid fa-box-open text-3xl mb-2"></i>
                            <p>Vaga</p>
                            <p>Doca ${index + 1}</p>
                        </div>
                    `;
                } else if (caminhao.status === 'finalizado') {
                    docaContent = `
                        <div class="text-center text-red-500">
                            <i class="fa-solid fa-check-circle text-3xl mb-2"></i>
                            <p class="font-bold">Finalizado</p>
                            <p>${caminhao.nome} (${caminhao.empresa})</p>
                            <p class="text-sm">${caminhao.pacotes} pacotes</p>
                            <button onclick="liberarDoca(${index})" class="mt-2 bg-gray-600 hover:bg-gray-700 text-white font-bold py-1 px-3 rounded text-sm transition-transform transform hover:scale-105">
                                Liberar Doca
                            </button>
                        </div>
                    `;
                } else {
                    docaContent = `
                        <div class="text-center text-blue-400">
                            <i class="fa-solid fa-truck-ramp-box text-3xl mb-2"></i>
                            <p>Descarregando</p>
                            <p class="font-bold">${caminhao.empresa}</p>
                            <p class="text-sm">${caminhao.nome}</p>
                            <p class="text-xs text-gray-400" id="timer-${index}">00:00:00</p>
                            <button onclick="finalizarCaminhao(${index})" class="mt-2 bg-green-600 hover:bg-green-700 text-white font-bold py-1 px-3 rounded text-sm transition-transform transform hover:scale-105">
                                Finalizar
                            </button>
                        </div>
                    `;
                }
                const docaHtml = `<div class="card p-4">${docaContent}</div>`;
                docasContainer.innerHTML += docaHtml;
            });
        };

        // Variáveis globais para os gráficos
        let volumeChart, pacotesEmpresaChart, horariosChart;

        // Função para inicializar os gráficos
        const inicializarGraficos = () => {
            const ctxVolume = document.getElementById('volumeChart').getContext('2d');
            volumeChart = new Chart(ctxVolume, {
                type: 'doughnut',
                data: {
                    labels: ['Descarregado', 'A Descarregar'],
                    datasets: [{
                        data: [pacotesDescarregados, Math.max(0, totalPacotesMeta - pacotesDescarregados)],
                        backgroundColor: ['#38a169', '#cbd5e0'],
                        hoverOffset: 4
                    }]
                },
                options: {
                    responsive: true,
                    plugins: { legend: { display: false } }
                }
            });

            const ctxPacotes = document.getElementById('pacotesEmpresaChart').getContext('2d');
            pacotesEmpresaChart = new Chart(ctxPacotes, {
                type: 'doughnut',
                data: {
                    labels: empresas,
                    datasets: [{
                        data: empresas.map(empresa => historicoPacotesEmpresa[empresa]),
                        backgroundColor: ['#63b3ed', '#f6ad55', '#fc8181', '#4fd1c5'],
                        hoverOffset: 4
                    }]
                },
                options: {
                    responsive: true,
                    plugins: { legend: { display: false } }
                }
            });

            const ctxHorarios = document.getElementById('horariosChart').getContext('2d');
            horariosChart = new Chart(ctxHorarios, {
                type: 'bar',
                data: {
                    labels: Array.from({ length: 24 }, (_, i) => `${i}h`),
                    datasets: [{
                        label: 'Entradas de Caminhões',
                        data: Array.from({ length: 24 }, (_, i) => historicoEntradas[i] || 0),
                        backgroundColor: '#805ad5',
                    }]
                },
                options: {
                    responsive: true,
                    scales: {
                        x: { ticks: { color: 'white' }, grid: { color: '#4a5568' } },
                        y: { ticks: { color: 'white' }, grid: { color: '#4a5568' } }
                    },
                    plugins: { legend: { labels: { color: 'white' } } }
                }
            });
        };

        // Função para atualizar os dados dos gráficos
        const atualizarGraficos = () => {
            volumeChart.data.datasets[0].data = [pacotesDescarregados, Math.max(0, totalPacotesMeta - pacotesDescarregados)];
            volumeChart.update();

            pacotesEmpresaChart.data.datasets[0].data = empresas.map(empresa => historicoPacotesEmpresa[empresa]);
            pacotesEmpresaChart.update();

            horariosChart.data.datasets[0].data = Array.from({ length: 24 }, (_, i) => historicoEntradas[i] || 0);
            horariosChart.update();
        };

        // Função para baixar os dados em um arquivo CSV
        const downloadCSV = () => {
            const header = ["Data", "Hora", "Motorista", "Empresa", "Pacotes", "Tempo na Doca", "Doca"];
            const csv = [
                header.join(","),
                ...logDescarregamento.map(d => `${d.data},${d.hora},${d.motorista},${d.empresa},${d.pacotes},${d.tempoNaDoca},${d.doca}`)
            ].join("\n");
            
            const blob = new Blob([csv], { type: 'text/csv;charset=utf-8;' });
            const url = URL.createObjectURL(blob);
            const link = document.createElement("a");
            link.setAttribute("href", url);
            link.setAttribute("download", "dados_gestao_frota.csv");
            link.style.visibility = 'hidden';
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            showAlert('Dados baixados com sucesso!');
        };


        // Event Listeners para a adição manual
        document.getElementById('setPacotesBtn').addEventListener('click', () => {
            const input = document.getElementById('totalPacotesInput');
            const novoTotal = parseInt(input.value);
            if (novoTotal > 0) {
                totalPacotesMeta = novoTotal;
                localStorage.setItem('totalPacotesMeta', totalPacotesMeta);
                atualizarGraficos();
                showAlert(`Meta de pacotes definida para ${novoTotal}.`);
            } else {
                showAlert('Por favor, insira um número válido para o total de pacotes.');
            }
        });

        document.getElementById('addMotoristaBtn').addEventListener('click', () => {
            const empresa = document.getElementById('empresaSelect').value;
            const motoristaNomeInput = document.getElementById('motoristaNomeInput');
            const pacotesInput = document.getElementById('pacotesInput');
            const nome = motoristaNomeInput.value.trim();
            const pacotes = parseInt(pacotesInput.value);

            if (nome === '' || pacotes <= 0) {
                showAlert('Por favor, preencha o nome do motorista e o número de pacotes corretamente.');
                return;
            }

            const caminhao = { nome, empresa, pacotes };
            filas[empresa].push(caminhao);
            motoristaNomeInput.value = ''; // Limpa o campo
            pacotesInput.value = ''; // Limpa o campo
            atualizarUI();
            showAlert(`Motorista ${nome} (${empresa}) adicionado à fila com ${pacotes} pacotes.`);
        });

        document.getElementById('downloadBtn').addEventListener('click', downloadCSV);
        
        // Inicia a aplicação
        window.onload = inicializarApp;

    </script>
</body>
</html>
