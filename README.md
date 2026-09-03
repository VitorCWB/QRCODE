<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Gerenciador de Lotes - Stin Pharma</title>
    <style>
        * {
            box-sizing: border-box;
            font-family: 'Segoe UI', Arial, sans-serif;
        }
        body {
            background-color: #f4f6f9;
            color: #333;
            padding: 20px 15px;
            margin: 0;
            display: flex;
            flex-direction: column;
            min-height: 100vh;
        }
        .container {
            max-width: 950px;
            margin: 0 auto;
            flex: 1;
            width: 100%;
        }

        /* Cabeçalho / Header */
        .header {
            display: flex;
            justify-content: center;
            align-items: center;
            margin-bottom: 20px;
            padding: 10px 0;
        }
        .header img {
            max-height: 100px;
            width: auto;
            object-fit: contain;
        }
        
        /* Form Card */
        .form-card {
            background: #ffffff;
            padding: 20px 25px;
            border-radius: 12px;
            box-shadow: 0 4px 15px rgba(0,0,0,0.06);
            margin-bottom: 25px;
        }
        .form-card h2 {
            margin-top: 0;
            color: #1e293b;
            font-size: 18px;
            border-bottom: 2px solid #f1f5f9;
            padding-bottom: 10px;
            margin-bottom: 20px;
        }
        .form-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(180px, 1fr));
            gap: 15px;
        }
        .form-group {
            display: flex;
            flex-direction: column;
        }
        .form-group label {
            font-size: 13px;
            font-weight: 600;
            color: #475569;
            margin-bottom: 6px;
        }
        .form-group input, .form-group select {
            padding: 10px;
            border: 1px solid #cbd5e1;
            border-radius: 6px;
            font-size: 14px;
            outline: none;
            transition: border-color 0.2s;
        }
        .form-group input:focus, .form-group select:focus {
            border-color: #2563eb;
        }
        .btn-submit {
            grid-column: 1 / -1;
            background-color: #2563eb;
            color: white;
            border: none;
            padding: 12px;
            border-radius: 6px;
            font-size: 15px;
            font-weight: bold;
            cursor: pointer;
            transition: background 0.2s;
            margin-top: 10px;
        }
        .btn-submit:hover {
            background-color: #1d4ed8;
        }

        /* Toolbar Actions */
        .toolbar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            flex-wrap: wrap;
            gap: 10px;
        }
        .toolbar-btns {
            display: flex;
            gap: 10px;
        }
        .btn-action {
            padding: 8px 14px;
            border-radius: 6px;
            font-size: 13px;
            font-weight: bold;
            cursor: pointer;
            border: none;
            transition: all 0.2s;
        }
        .btn-reset {
            background-color: #ef4444;
            color: white;
        }
        .btn-reset:hover {
            background-color: #dc2626;
        }

        /* Table Card */
        .table-card {
            background: #ffffff;
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0,0,0,0.08);
        }
        h1 {
            text-align: center;
            color: #1e293b;
            margin-top: 0;
            margin-bottom: 25px;
            font-size: 20px;
            text-transform: uppercase;
            letter-spacing: 1px;
        }
        table {
            width: 100%;
            border-collapse: collapse;
            background: #ffffff;
        }
        th, td {
            padding: 12px 16px;
            border: 1px solid #cbd5e1;
            vertical-align: middle;
        }
        th {
            background-color: #f1f5f9;
            color: #0f172a;
            font-weight: 700;
            font-size: 14px;
            text-align: center;
        }
        .produto {
            font-weight: 600;
            color: #1e293b;
        }
        .center {
            text-align: center;
        }
        .qr-container {
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            gap: 6px;
        }
        .qr-container img {
            border: 1px solid #e2e8f0;
            padding: 4px;
            border-radius: 6px;
            background: #fff;
        }
        .lote-number {
            font-family: 'Courier New', Courier, monospace;
            font-size: 15px;
            font-weight: bold;
            color: #0f172a;
            letter-spacing: 0.5px;
        }
        
        /* Select de Status */
        .select-status {
            padding: 8px 12px;
            border-radius: 20px;
            font-size: 12px;
            font-weight: bold;
            letter-spacing: 0.5px;
            cursor: pointer;
            outline: none;
            text-align: center;
            border: 1px solid transparent;
            width: 130px;
        }
        .status-doc {
            background-color: #dcfce7;
            color: #15803d;
            border-color: #bbf7d0;
        }
        .status-aberto {
            background-color: #fef3c7;
            color: #b45309;
            border-color: #fde68a;
        }
        .status-concluido {
            background-color: #dbeafe;
            color: #1e40af;
            border-color: #bfdbfe;
        }
        .status-cancelado {
            background-color: #fee2e2;
            color: #991b1b;
            border-color: #fecaca;
        }

        .btn-delete {
            background: transparent;
            border: none;
            color: #94a3b8;
            font-size: 16px;
            cursor: pointer;
            padding: 4px 8px;
            border-radius: 4px;
            transition: color 0.2s;
        }
        .btn-delete:hover {
            color: #ef4444;
            background-color: #fee2e2;
        }

        tr:nth-child(even) {
            background-color: #f8fafc;
        }

        /* Rodapé / Footer */
        .footer {
            margin-top: 40px;
            padding: 15px 0;
            text-align: center;
            font-size: 13px;
            color: #64748b;
            font-weight: 500;
            border-top: 1px solid #e2e8f0;
        }
    </style>
</head>
<body>
    <div class="container">
        
        <!-- Cabeçalho com Apontamento Local para a Logo -->
        <header class="header">
            <img src="stin_pharma_excelncia_em_sade_logo-removebg-preview_2.png" alt="Stin Pharma Logo">
        </header>

        <!-- Formulário para novos cadastros -->
        <div class="form-card">
            <h2>Adicionar Novo Lote</h2>
            <form id="loteForm">
                <div class="form-grid">
                    <div class="form-group">
                        <label for="data">DATA</label>
                        <input type="text" id="data" placeholder="Ex: 09/set" required>
                    </div>
                    <div class="form-group">
                        <label for="produto">PRODUTO</label>
                        <input type="text" id="produto" placeholder="Ex: TIRZE. 60MG" required>
                    </div>
                    <div class="form-group">
                        <label for="lote">NÚMERO DO LOTE</label>
                        <input type="text" id="lote" placeholder="Ex: 2608240003" required>
                    </div>
                    <div class="form-group">
                        <label for="status">STATUS INICIAL</label>
                        <select id="status" required>
                            <option value="EM ABERTO">EM ABERTO</option>
                            <option value="DOC">DOC</option>
                            <option value="CONCLUÍDO">CONCLUÍDO</option>
                            <option value="CANCELADO">CANCELADO</option>
                        </select>
                    </div>
                    <button type="submit" class="btn-submit">Adicionar à Tabela</button>
                </div>
            </form>
        </div>

        <!-- Tabela com os registros -->
        <div class="table-card">
            <div class="toolbar">
                <span id="saveStatus" style="font-size: 13px; color: #10b981; font-weight: 600;">✓ Salvamento automático ativo</span>
                <div class="toolbar-btns">
                    <button class="btn-action btn-reset" onclick="resetarDadosPadrao()">Restaurar Dados Iniciais</button>
                </div>
            </div>
            
            <h1>Controle de Lotes de Produtos</h1>
            <table>
                <thead>
                    <tr>
                        <th style="width: 12%;">DATA</th>
                        <th style="width: 32%;">PRODUTO</th>
                        <th style="width: 28%;">LOTE</th>
                        <th style="width: 20%;">STATUS (ALTERAR)</th>
                        <th style="width: 8%;">AÇÃO</th>
                    </tr>
                </thead>
                <tbody id="tabelaCorpo">
                </tbody>
            </table>
        </div>

    </div>

    <!-- Rodapé -->
    <footer class="footer">
        Created by Vitor Tenório
    </footer>

    <script>
        const dadosPadrao = [
            { id: 1, data: "01/set", produto: "TIRZE. 93,6", lote: "2608170002", status: "DOC" },
            { id: 2, data: "01/set", produto: "TIRZE. 60", lote: "2608204001", status: "DOC" },
            { id: 3, data: "03/set", produto: "TIRZE. 93,6", lote: "2608240002", status: "EM ABERTO" },
            { id: 4, data: "03/set", produto: "TIRZE. 60MG", lote: "2608204001", status: "EM ABERTO" },
            { id: 5, data: "04/set", produto: "TIRZE. 60MG", lote: "2608240001", status: "EM ABERTO" },
            { id: 6, data: "04/set", produto: "TIRZE 93,6MG", lote: "2608170002", status: "EM ABERTO" },
            { id: 7, data: "08/set", produto: "TIRZE 60MG/2,4ML", lote: "2608240001", status: "EM ABERTO" },
            { id: 8, data: "08/set", produto: "TIRZE 93,6MG/3,6ML", lote: "260840002", status: "EM ABERTO" }
        ];

        let listaLotes = [];

        const tbody = document.getElementById('tabelaCorpo');
        const form = document.getElementById('loteForm');

        function getStatusClass(status) {
            switch(status) {
                case 'DOC': return 'status-doc';
                case 'EM ABERTO': return 'status-aberto';
                case 'CONCLUÍDO': return 'status-concluido';
                case 'CANCELADO': return 'status-cancelado';
                default: return 'status-aberto';
            }
        }

        function carregarDados() {
            const salvos = localStorage.getItem('lotes_relatorio_db');
            if (salvos) {
                listaLotes = JSON.parse(salvos);
            } else {
                listaLotes = [...dadosPadrao];
                salvarDados();
            }
            renderizarTabela();
        }

        function salvarDados() {
            localStorage.setItem('lotes_relatorio_db', JSON.stringify(listaLotes));
            const statusEl = document.getElementById('saveStatus');
            statusEl.innerText = "✓ Alterações salvas!";
            setTimeout(() => {
                statusEl.innerText = "✓ Salvamento automático ativo";
            }, 2000);
        }

        function renderizarTabela() {
            tbody.innerHTML = '';
            listaLotes.forEach((item, index) => {
                const tr = document.createElement('tr');
                const qrUrl = `https://api.qrserver.com/v1/create-qr-code/?size=110x110&data=${encodeURIComponent(item.lote)}`;

                tr.innerHTML = `
                    <td class="center">${item.data}</td>
                    <td class="produto">${item.produto}</td>
                    <td class="center">
                        <div class="qr-container">
                            <img src="${qrUrl}" alt="QR Code Lote ${item.lote}" width="110" height="110">
                            <div class="lote-number">${item.lote}</div>
                        </div>
                    </td>
                    <td class="center">
                        <select class="select-status ${getStatusClass(item.status)}" onchange="alterarStatus(${index}, this.value)">
                            <option value="EM ABERTO" ${item.status === 'EM ABERTO' ? 'selected' : ''}>EM ABERTO</option>
                            <option value="DOC" ${item.status === 'DOC' ? 'selected' : ''}>DOC</option>
                            <option value="CONCLUÍDO" ${item.status === 'CONCLUÍDO' ? 'selected' : ''}>CONCLUÍDO</option>
                            <option value="CANCELADO" ${item.status === 'CANCELADO' ? 'selected' : ''}>CANCELADO</option>
                        </select>
                    </td>
                    <td class="center">
                        <button class="btn-delete" onclick="removerItem(${index})" title="Excluir item">✕</button>
                    </td>
                `;
                tbody.appendChild(tr);
            });
        }

        function alterarStatus(index, novoStatus) {
            listaLotes[index].status = novoStatus;
            salvarDados();
            renderizarTabela();
        }

        function removerItem(index) {
            if (confirm("Deseja realmente remover este item do relatório?")) {
                listaLotes.splice(index, 1);
                salvarDados();
                renderizarTabela();
            }
        }

        function resetarDadosPadrao() {
            if (confirm("Tem certeza que deseja restaurar a lista para os dados originais?")) {
                listaLotes = JSON.parse(JSON.stringify(dadosPadrao));
                salvarDados();
                renderizarTabela();
            }
        }

        form.addEventListener('submit', function(e) {
            e.preventDefault();

            const novoItem = {
                id: Date.now(),
                data: document.getElementById('data').value,
                produto: document.getElementById('produto').value,
                lote: document.getElementById('lote').value,
                status: document.getElementById('status').value
            };

            listaLotes.push(novoItem);
            salvarDados();
            renderizarTabela();
            form.reset();
        });

        carregarDados();
    </script>
</body>
</html>
