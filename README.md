<!DOCTYPE html>
<html lang="pt-PT">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Sistema Integrado de Logística e Conferência - Stin Pharma</title>

    <!-- Scripts e Estilos Externos -->
    <!-- ATENÇÃO (PRODUÇÃO): O script CDN do Tailwind serve apenas para desenvolvimento. Para um ambiente produtivo seguro e rápido, compile as classes estaticamente num ficheiro CSS. -->
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/PapaParse/5.4.1/papaparse.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jsbarcode/3.11.5/JsBarcode.all.min.js"></script>
    <script src="https://cdnjs.cloudflare.com/ajax/libs/qrious/4.0.2/qrious.min.js"></script>
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">

    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800;900&display=swap');

        :root {
            --bg-color: #f0f2f5;
            --card-bg: #ffffff;
            --text-color: #333333;
            --title-color: #1a252f;
            --primary-color: #2563eb;
            --border-color: #cbd5e1;
            --table-header-bg: #f8f9fa;
            --table-header-color: #495057;
            --row-even: #f9f9f9;
            --input-bg: #ffffff;
            --input-border: #ced4da;
            --nav-bg: #1e293b;
            --nav-text: #94a3b8;
            --nav-active-bg: #2563eb;
            --nav-active-text: #ffffff;
            --sidebar-width: 280px;
        }

        body.dark .top-bar img,
        body.dark-mode .top-bar img {
            filter: drop-shadow(0 0 2px rgba(0, 240, 255, 0.8)) 
                    drop-shadow(0 0 4px rgba(0, 210, 255, 0.5)) 
                    brightness(1.6) 
                    contrast(1.05);
            transition: filter 0.3s ease;
        }

        body.dark, body.dark-mode {
            --bg-color: #0f172a;
            --card-bg: #1e293b;
            --text-color: #f8fafc;
            --title-color: #f1f5f9;
            --border-color: #334155;
            --table-header-bg: #334155;
            --table-header-color: #f8fafc;
            --row-even: #1e293b;
            --input-bg: #0f172a;
            --input-border: #475569;
            --nav-bg: #0f172a;
            --nav-text: #94a3b8;
            --nav-active-bg: #2563eb;
            --nav-active-text: #ffffff;
        }

        * { box-sizing: border-box; transition: background-color 0.2s, color 0.2s, width 0.3s ease, margin 0.3s ease; }

        body {
            font-family: 'Inter', 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
            background-color: var(--bg-color);
            color: var(--text-color);
            margin: 0;
            padding: 0;
            min-height: 100vh;
            display: flex;
            overflow-x: hidden;
        }

        /* Layout Estrutural Principal com Sidebar à Esquerda */
        .app-layout {
            display: flex;
            width: 100%;
            min-height: 100vh;
            position: relative;
        }

        /* Sidebar Styling */
        .sidebar {
            width: var(--sidebar-width);
            background-color: var(--nav-bg);
            color: var(--nav-text);
            display: flex;
            flex-direction: column;
            justify-content: space-between;
            position: fixed;
            top: 0;
            bottom: 0;
            left: 0;
            z-index: 100;
            box-shadow: 4px 0 15px rgba(0, 0, 0, 0.15);
            transition: transform 0.3s ease, width 0.3s ease;
        }

        .sidebar.collapsed {
            transform: translateX(-100%);
        }

        .sidebar-header {
            padding: 20px;
            display: flex;
            align-items: center;
            justify-content: space-between;
            border-bottom: 1px solid rgba(255, 255, 255, 0.1);
        }

        .sidebar-brand {
            display: flex;
            align-items: center;
            gap: 12px;
        }

        .sidebar-brand img {
            max-height: 38px;
            width: auto;
        }

        .sidebar-title {
            font-size: 15px;
            font-weight: 800;
            color: #ffffff;
            line-height: 1.2;
            letter-spacing: -0.2px;
        }

        /* Botões de Módulos Verticalizados */
        .nav-tabs-vertical {
            display: flex;
            flex-direction: column;
            gap: 8px;
            padding: 20px 12px;
            flex: 1;
            overflow-y: auto;
        }

        .nav-tab-btn {
            width: 100%;
            padding: 14px 16px;
            font-size: 13.5px;
            font-weight: 600;
            border: none;
            background-color: transparent;
            color: var(--nav-text);
            border-radius: 10px;
            cursor: pointer;
            transition: all 0.2s;
            display: flex;
            align-items: center;
            justify-content: flex-start;
            gap: 12px;
            text-align: left;
        }

        .nav-tab-btn:hover {
            background-color: rgba(255, 255, 255, 0.08);
            color: #ffffff;
        }

        .nav-tab-btn.active {
            background-color: var(--nav-active-bg);
            color: var(--nav-active-text);
            box-shadow: 0 4px 12px rgba(37, 99, 235, 0.3);
            font-weight: 700;
        }

        .sidebar-footer {
            padding: 16px 20px;
            border-top: 1px solid rgba(255, 255, 255, 0.1);
            font-size: 11px;
            text-align: center;
            color: #64748b;
        }

        /* Conteúdo Principal */
        .main-wrapper {
            flex: 1;
            margin-left: var(--sidebar-width);
            padding: 20px 25px;
            width: calc(100% - var(--sidebar-width));
            transition: margin-left 0.3s ease, width 0.3s ease;
        }

        .main-wrapper.expanded {
            margin-left: 0;
            width: 100%;
        }

        .top-bar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 20px;
            background: var(--card-bg);
            padding: 14px 22px;
            border-radius: 12px;
            box-shadow: 0 2px 10px rgba(0,0,0,0.05);
            border: 1px solid var(--border-color);
        }

        .logo-header {
            max-height: 40px;
            width: auto;
            object-fit: contain;
        }

        .btn-toggle-sidebar {
            background-color: var(--bg-color);
            color: var(--text-color);
            border: 1px solid var(--border-color);
            padding: 8px 14px;
            border-radius: 8px;
            font-size: 13px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 8px;
        }

        .btn-toggle-sidebar:hover {
            background-color: var(--border-color);
        }

        .btn-theme-toggle {
            background-color: var(--bg-color);
            color: var(--text-color);
            border: 1px solid var(--border-color);
            padding: 8px 16px;
            border-radius: 20px;
            font-size: 13px;
            font-weight: bold;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 6px;
        }

        .tab-content {
            display: none;
            background: var(--card-bg);
            padding: 25px;
            border-radius: 12px;
            box-shadow: 0 4px 20px rgba(0, 0, 0, 0.08);
            position: relative;
            border: 1px solid var(--border-color);
        }

        .tab-content.active {
            display: block;
        }

        .cabecalho-topo {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 5px;
        }
        h2 { margin: 0; color: var(--title-color); font-size: 20px; font-weight: bold; }
        .subtitle {
            text-align: left;
            color: #6c757d;
            font-size: 14px;
            margin-bottom: 20px;
        }

        .btn-som {
            background-color: var(--bg-color);
            border: 1px solid var(--border-color);
            color: var(--text-color);
            padding: 8px 14px;
            border-radius: 20px;
            font-size: 14px;
            font-weight: 600;
            cursor: pointer;
            display: flex;
            align-items: center;
            gap: 6px;
            transition: 0.2s;
        }
        .btn-som.ativo { background-color: #d1e7dd; color: #0f5132; border-color: #badbcc; }
        .btn-som.mudo { background-color: #f8d7da; color: #842029; border-color: #f5c2c7; }

        .painel-transportadora {
            background-color: var(--bg-color);
            border: 1px solid var(--border-color);
            padding: 15px;
            border-radius: 8px;
            margin-bottom: 15px;
            display: flex;
            align-items: center;
            gap: 12px;
            flex-wrap: wrap;
        }
        .painel-transportadora label { font-weight: 700; color: var(--text-color); font-size: 14px; }
        .painel-transportadora input[type="text"] {
            flex: 1; min-width: 200px; padding: 10px; font-size: 15px;
            background-color: var(--input-bg); color: var(--text-color);
            border: 1px solid var(--input-border); border-radius: 6px; text-align: left; margin-bottom: 0;
        }
        .btn-trava {
            padding: 10px 16px; border: none; border-radius: 6px; font-weight: bold; cursor: pointer; transition: 0.2s;
        }
        .btn-trava.destravado { background-color: #ffc107; color: #000; }
        .btn-trava.travado { background-color: #dc3545; color: #fff; }

        .campos-leitura { display: flex; gap: 15px; margin-bottom: 10px; flex-wrap: wrap; }
        .campo-box { flex: 1; min-width: 250px; }
        .campo-box label { display: block; font-size: 12px; font-weight: 700; color: var(--text-color); text-transform: uppercase; margin-bottom: 5px; }

        input[type="text"].input-chave {
            width: 100%; padding: 16px; font-size: 18px; border: 2px solid #28a745;
            background-color: var(--input-bg); color: var(--text-color);
            border-radius: 8px; text-align: center; margin-bottom: 10px;
        }
        input[type="text"].input-campo {
            width: 100%; padding: 16px; font-size: 16px; border: 2px solid #28a745;
            background-color: var(--input-bg); color: var(--text-color);
            border-radius: 8px; text-align: center;
        }
        input[type="text"]:focus { outline: none; box-shadow: 0 0 8px rgba(40, 167, 69, 0.4); }
        input[type="text"]:disabled { background-color: var(--bg-color); border-color: var(--border-color); cursor: not-allowed; opacity: 0.6; }

        .painel-ultimo {
            background-color: var(--bg-color); border: 1px solid var(--border-color); padding: 18px;
            border-radius: 8px; text-align: center; margin-bottom: 20px; margin-top: 10px;
        }
        .rotulo { font-size: 12px; color: #6c757d; text-transform: uppercase; font-weight: 700; }
        .numero-destaque { font-size: 38px; font-weight: 800; color: #198754; margin: 5px 0; letter-spacing: 1px; }
        .transp-destaque { font-size: 15px; color: #0d6efd; font-weight: bold; margin-bottom: 10px; }
        .bloco-chave-completa { background-color: var(--card-bg); border: 1px dashed #28a745; padding: 10px; border-radius: 6px; margin-top: 10px; }
        .chave-destaque { font-size: 14px; color: var(--text-color); font-family: monospace; font-weight: bold; word-break: break-all; }

        .acoes-excel { display: flex; gap: 10px; margin-bottom: 15px; flex-wrap: wrap; }
        .btn { flex: 1; min-width: 140px; padding: 12px; border: none; border-radius: 6px; font-size: 14px; font-weight: 600; cursor: pointer; }
        .btn-copiar { background-color: #198754; color: white; }
        .btn-excel { background-color: #0d6efd; color: white; }
        .btn-finalizar { background-color: #6f42c1; color: white; font-size: 16px; font-weight: bold; padding: 14px; width: 100%; }
        .btn-limpar { background-color: #dc3545; color: white; padding: 6px 12px; border: none; border-radius: 4px; cursor: pointer; font-size: 12px; }
        
        .btn-deletar-linha {
            background-color: #fee2e2;
            color: #dc2626;
            border: 1px solid #fca5a5;
            padding: 6px 10px;
            border-radius: 4px;
            cursor: pointer;
            font-size: 12px;
            font-weight: bold;
            transition: 0.2s;
        }
        .btn-deletar-linha:hover {
            background-color: #dc2626;
            color: white;
        }

        .form-card {
            background: var(--bg-color);
            padding: 20px;
            border-radius: 10px;
            border: 1px solid var(--border-color);
            margin-bottom: 25px;
        }
        .form-card h3 {
            margin-top: 0;
            color: var(--title-color);
            font-size: 15px;
            font-weight: 700;
            border-bottom: 2px solid var(--border-color);
            padding-bottom: 10px;
            margin-bottom: 15px;
        }
        .form-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(220px, 1fr));
            gap: 15px;
        }
        .form-group {
            display: flex;
            flex-direction: column;
        }
        .form-group label {
            font-size: 12px;
            font-weight: 700;
            color: var(--text-color);
            margin-bottom: 6px;
            text-transform: uppercase;
        }
        .form-group input, .form-group textarea, .form-group select {
            padding: 10px;
            background-color: var(--input-bg);
            color: var(--text-color);
            border: 1px solid var(--input-border);
            border-radius: 6px;
            font-size: 14px;
            outline: none;
        }
        .aviso-lote {
            font-size: 11px;
            color: #d97706;
            font-weight: 600;
            margin-top: 5px;
            display: flex;
            align-items: center;
            gap: 4px;
        }

        .btn-submit {
            grid-column: 1 / -1;
            background-color: var(--primary-color, #2563eb);
            color: white;
            border: none;
            padding: 12px;
            border-radius: 6px;
            font-size: 15px;
            font-weight: bold;
            cursor: pointer;
            margin-top: 5px;
            transition: background-color 0.2s;
        }
        .btn-submit:hover { opacity: 0.9; }

        .toolbar {
            display: flex;
            justify-content: space-between;
            align-items: center;
            margin-bottom: 15px;
            flex-wrap: wrap;
            gap: 12px;
        }
        .search-box {
            display: flex;
            align-items: center;
            gap: 8px;
            background-color: var(--input-bg);
            border: 1px solid var(--input-border);
            border-radius: 8px;
            padding: 6px 12px;
            min-width: 280px;
        }
        .search-box input {
            border: none;
            outline: none;
            background: transparent;
            color: var(--text-color);
            font-size: 13px;
            width: 100%;
        }
        .toolbar-btns {
            display: flex;
            gap: 10px;
            flex-wrap: wrap;
        }
        .btn-action {
            padding: 8px 14px;
            border-radius: 6px;
            font-size: 13px;
            font-weight: bold;
            cursor: pointer;
            border: none;
            transition: 0.2s;
        }
        .btn-print { background-color: #0284c7; color: white; }
        .btn-print:hover { background-color: #0369a1; }
        .btn-archive { background-color: #0d9488; color: white; }
        .btn-archive:hover { background-color: #0f766e; }
        .btn-history { background-color: #4f46e5; color: white; }
        .btn-history:hover { background-color: #4338ca; }
        .btn-customize { background-color: #8b5cf6; color: white; }
        .btn-customize:hover { background-color: #7c3aed; }
        .btn-reset { background-color: #ef4444; color: white; }
        .btn-reset:hover { background-color: #dc2626; }

        .cabecalho-secao { display: flex; justify-content: space-between; align-items: center; margin-bottom: 10px; }
        .tabela-container { max-height: 500px; overflow-y: auto; border: 1px solid var(--border-color); border-radius: 8px; }
        table { width: 100%; border-collapse: collapse; font-size: 14px; text-align: left; background: var(--card-bg); }
        th, td { padding: 12px; border: 1px solid var(--border-color); }
        th { background-color: var(--table-header-bg); color: var(--table-header-color); position: sticky; top: 0; z-index: 1; font-weight: 700; text-align: center; }
        tr:nth-child(even) { background-color: var(--row-even); }
        .col-nota { font-size: 15px; font-weight: bold; color: #198754; text-align: center; }
        .col-chave { font-family: monospace; font-size: 12px; color: var(--text-color); word-break: break-all; }
        .col-rastreio { font-family: monospace; font-size: 14px; color: #0d6efd; font-weight: bold; text-align: center; }

        .produto { font-weight: 600; color: var(--text-color); }
        .center { text-align: center; }
        .qr-container { display: flex; flex-direction: column; align-items: center; justify-content: center; gap: 6px; }
        .qr-container img { border: 1px solid var(--border-color); padding: 4px; border-radius: 6px; background: #fff; }
        .lote-number { font-family: monospace; font-size: 15px; font-weight: bold; color: var(--text-color); }

        .erro { color: #dc3545; font-size: 15px; text-align: center; margin-bottom: 10px; font-weight: bold; min-height: 22px; }
        .notificacao {
            position: fixed; bottom: 20px; right: 20px; background: #198754; color: white;
            padding: 12px 20px; border-radius: 6px; box-shadow: 0 4px 10px rgba(0,0,0,0.2);
            display: none; font-weight: 600; z-index: 999;
        }

        .historico-secao { margin-top: 30px; border-top: 2px dashed var(--border-color); padding-top: 20px; }
        .historico-card {
            background: var(--bg-color); border: 1px solid var(--border-color); border-left: 5px solid #6f42c1;
            padding: 12px; margin-bottom: 10px; border-radius: 6px; display: flex;
            justify-content: space-between; align-items: center; flex-wrap: wrap; gap: 10px;
        }

        .modal {
            display: none;
            position: fixed;
            top: 0; left: 0; width: 100%; height: 100%;
            background-color: rgba(0,0,0,0.6);
            z-index: 1000;
            justify-content: center;
            align-items: center;
        }
        .modal-content {
            background: var(--card-bg);
            color: var(--text-color);
            padding: 25px;
            border-radius: 12px;
            width: 90%;
            max-width: 850px;
            max-height: 85vh;
            overflow-y: auto;
            box-shadow: 0 10px 25px rgba(0,0,0,0.3);
        }
        .modal-header {
            display: flex;
            justify-content: space-between;
            align-items: center;
            border-bottom: 2px solid var(--border-color);
            padding-bottom: 12px;
            margin-bottom: 20px;
        }
        .modal-header h3 { margin: 0; color: var(--title-color); font-size: 18px; font-weight: bold; }
        .btn-close { background: #e2e8f0; border: none; padding: 6px 12px; border-radius: 6px; cursor: pointer; font-weight: bold; color: #333; }
        .btn-close:hover { background: #cbd5e1; }

        /* Estilos para Color Pickers */
        .color-pickers-grid {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(200px, 1fr));
            gap: 15px;
            margin-bottom: 20px;
            background: var(--bg-color);
            padding: 15px;
            border-radius: 8px;
            border: 1px solid var(--border-color);
        }
        .color-picker-item {
            display: flex;
            align-items: center;
            justify-content: space-between;
            gap: 10px;
        }
        .color-picker-item label {
            font-size: 12px;
            font-weight: 600;
        }
        .color-picker-item input[type="color"] {
            -webkit-appearance: none;
            border: none;
            width: 36px;
            height: 36px;
            border-radius: 50%;
            cursor: pointer;
            padding: 0;
            background: none;
        }
        .color-picker-item input[type="color"]::-webkit-color-swatch-wrapper {
            padding: 0;
        }
        .color-picker-item input[type="color"]::-webkit-color-swatch {
            border: 2px solid var(--border-color);
            border-radius: 50%;
        }

        .footer {
            margin-top: 30px;
            padding: 15px 0;
            text-align: center;
            font-size: 13px;
            color: #64748b;
            font-weight: 500;
            border-top: 1px solid var(--border-color);
        }

        /* Responsive Mobile Layout adjustments */
        @media (max-width: 768px) {
            .sidebar {
                transform: translateX(-100%);
            }
            .sidebar.mobile-open {
                transform: translateX(0);
            }
            .main-wrapper {
                margin-left: 0;
                width: 100%;
                padding: 15px 10px;
            }
        }

        /* --- ESTILOS DE IMPRESSÃO A4 ISOLADOS E CORRIGIDOS --- */
        @media print {
            body { background-color: white !important; color: black !important; padding: 0 !important; margin: 0 !important; }
            .no-print, .form-card, .toolbar, .top-bar, .sidebar, .btn-deletar-linha, .footer, .modal { display: none !important; }
            .main-wrapper { margin-left: 0 !important; width: 100% !important; padding: 0 !important; }
            .tab-content { padding: 0 !important; box-shadow: none !important; background: transparent !important; border: none !important; }
            
            #printableArea { display: none !important; }
            #areaImpressaoRelatorio { display: block !important; }
            
            .print-page { page-break-after: always; padding: 10mm; }
            .print-page:last-child { page-break-after: avoid; }
            .print-header { text-align: center; border-bottom: 2px solid #000; padding-bottom: 10px; margin-bottom: 20px; }
            .print-header h2 { margin: 0 0 5px 0; font-size: 18pt; color: #000; }
            .print-header p { margin: 0; font-size: 11pt; color: #333; }
            
            .print-logo { max-height: 60px; width: auto; margin-bottom: 8px; display: block; margin-left: auto; margin-right: auto; object-fit: contain; }
            .print-seal-box { border: 2px dashed #000; padding: 8px 12px; border-radius: 6px; margin-top: 15px; font-size: 10pt; text-align: center; background: #fafafa; }
            .seal-code { font-family: monospace; font-size: 12pt; font-weight: bold; letter-spacing: 1px; color: #1e3a8a; }

            table { width: 100% !important; border-collapse: collapse !important; margin: 0 auto !important; }
            th, td { border: 1px solid #000 !important; padding: 8px 10px !important; color: #000 !important; text-align: center !important; vertical-align: middle !important; }
            th { background-color: #f1f5f9 !important; font-size: 11pt !important; }
            .qr-container img { border: 1px solid #000 !important; width: 100px !important; height: 100px !important; }

            @page { size: A4 portrait; margin: 10mm; }
        }

        body.printing-manifesto #areaImpressaoRelatorio { display: none !important; }
        body.printing-manifesto #printableArea {
            display: block !important;
            width: 100% !important;
            max-width: 100% !important;
            margin: 0 auto !important;
            padding: 0 !important;
        }

        @media screen {
            .print-only { display: none; }
        }
    </style>
</head>
<body>

    <!-- ECRÃ DE AUTENTICAÇÃO / LOGIN (OVERLAY) -->
    <div id="loginOverlay" class="fixed inset-0 bg-slate-900/90 backdrop-blur-md z-[2000] flex items-center justify-center p-4">
        <div class="bg-white dark:bg-slate-800 text-slate-800 dark:text-slate-100 p-8 rounded-2xl shadow-2xl max-w-md w-full border border-slate-200 dark:border-slate-700 text-center">
            <div class="mb-6 flex flex-col items-center">
                <div class="w-16 h-16 bg-blue-100 dark:bg-slate-700 text-blue-600 dark:text-blue-400 rounded-full flex items-center justify-center mb-3">
                    <i class="fa-solid fa-lock text-2xl"></i>
                </div>
                <h2 class="text-xl font-bold text-slate-900 dark:text-white">Acesso ao Sistema</h2>
                <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Insira as credenciais para iniciar sessão</p>
            </div>

            <form id="loginForm" onsubmit="validarLogin(event)" class="space-y-4 text-left">
                <div>
                    <label for="inputUsuario" class="block text-xs font-bold text-slate-600 dark:text-slate-300 uppercase mb-1">Utilizador</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-user"></i></span>
                        <input type="text" id="inputUsuario" placeholder="Digite o utilizador..." required class="w-full pl-10 pr-3 py-2.5 border border-slate-300 dark:border-slate-600 rounded-lg bg-slate-50 dark:bg-slate-700 text-slate-900 dark:text-white focus:outline-none focus:border-blue-500 text-sm">
                    </div>
                </div>

                <div>
                    <label for="inputSenha" class="block text-xs font-bold text-slate-600 dark:text-slate-300 uppercase mb-1">Palavra-passe</label>
                    <div class="relative">
                        <span class="absolute inset-y-0 left-0 flex items-center pl-3 text-slate-400"><i class="fa-solid fa-key"></i></span>
                        <input type="password" id="inputSenha" placeholder="Digite a palavra-passe..." required class="w-full pl-10 pr-3 py-2.5 border border-slate-300 dark:border-slate-600 rounded-lg bg-slate-50 dark:bg-slate-700 text-slate-900 dark:text-white focus:outline-none focus:border-blue-500 text-sm">
                    </div>
                </div>

                <div id="loginError" class="text-xs font-bold text-red-500 text-center min-h-[18px]"></div>

                <button type="submit" class="w-full py-3 bg-blue-600 hover:bg-blue-700 text-white font-bold rounded-lg shadow-md transition text-sm flex items-center justify-center gap-2">
                    <i class="fa-solid fa-right-to-bracket"></i> Iniciar Sessão
                </button>

                <p class="text-[11px] text-slate-500 dark:text-slate-400 text-center mt-3 bg-slate-100 dark:bg-slate-700/50 py-1.5 px-3 rounded-lg border border-slate-200 dark:border-slate-600">
                    💡 <strong>Credenciais de acesso:</strong> Utilizador: <code>admin</code> | Palavra-passe: <code>3206</code>
                </p>
            </form>
        </div>
    </div>

    <div class="app-layout">
        <!-- BARRA LATERAL (SIDEBAR): Módulos e Navegação à Esquerda -->
        <aside id="sidebarNav" class="sidebar no-print">
            <div>
                <div class="sidebar-header">
                    <div class="sidebar-brand">
                        <img id="sidebarLogoImg" src="stin_pharma_excelncia_em_sade_logo-removebg-preview_2.png" alt="Stin Pharma Logo" onerror="this.style.display='none'">
                        <span id="sidebarTitleText" class="sidebar-title">Stin Pharma Logística</span>
                    </div>
                </div>

                <!-- Lista Vertical de Botões dos Módulos -->
                <nav class="nav-tabs-vertical">
                    <button class="nav-tab-btn active" onclick="trocarAba('geral', event)">
                        <i class="fa-solid fa-truck"></i> Conferência Geral NF-e
                    </button>
                    <button class="nav-tab-btn" onclick="trocarAba('sedex', event)">
                        <i class="fa-solid fa-box-archive"></i> Conferência SEDEX
                    </button>
                    <button class="nav-tab-btn" onclick="trocarAba('gelado', event)">
                        <i class="fa-solid fa-snowflake"></i> Pedidos Gelado (QR Code)
                    </button>
                    <button class="nav-tab-btn" onclick="trocarAba('manifesto', event)">
                        <i class="fa-solid fa-file-lines"></i> Manifesto / Checklist
                    </button>
                    <button class="nav-tab-btn" onclick="trocarAba('manutencao', event)">
                        <i class="fa-solid fa-wrench"></i> Manutenção do Programa
                    </button>
                </nav>
            </div>

            <div class="sidebar-footer">
                Stin Pharma &copy; 2026<br>
                <span>Sistema Integrado v8.5</span>
            </div>
        </aside>

        <!-- ÁREA DE CONTEÚDO PRINCIPAL -->
        <main id="mainWrapper" class="main-wrapper">
            <!-- Barra Superior de Ações -->
            <div class="top-bar no-print">
                <div style="display: flex; align-items: center; gap: 15px;">
                    <button class="btn-toggle-sidebar" onclick="toggleSidebar()" title="Recolher/Expandir Menu Lateral">
                        <i id="toggleIcon" class="fa-solid fa-bars"></i>
                        <span id="toggleText">Ocultar Menu</span>
                    </button>
                    <div style="display: flex; align-items: center; gap: 12px;">
                        <img id="imgLogoHeader" class="logo-header" alt="Logo Empresa" src="data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD/2wBDAP//////////////////////////////////////////////////////////////////////////////////////wgALCAEAAQABREA/8QAFBABAAAAAAAAAAAAAAAAAAAAAP/aAAgBAQABPxA=" onerror="this.style.display='none'; document.getElementById('fallbackIcon').style.display='inline-block';">
                        <i id="fallbackIcon" class="fa-solid fa-snowflake text-blue-500 text-2xl" style="display:none;"></i>
                        <span id="headerTitleDisplay" class="font-bold text-sm md:text-base text-slate-800 dark:text-slate-100 hidden sm:block">
                            Stin Pharma | Sistema Integrado de Logística e Conferência
                        </span>
                    </div>
                </div>
                <div style="display: flex; align-items: center; gap: 10px;">
                    <button id="themeToggleBtn" class="btn-theme-toggle" onclick="alternarTema()">
                        <span id="themeIcon">🌙</span> <span id="themeText">Tema Escuro</span>
                    </button>
                    <button class="btn-theme-toggle border-red-300 text-red-600 hover:bg-red-50 dark:border-red-800 dark:text-red-400" onclick="terminarSessao()">
                        <i class="fa-solid fa-right-from-bracket"></i> Sair
                    </button>
                </div>
            </div>

            <!-- ABA 1: CONFERÊNCIA GERAL DE NOTAS -->
            <div id="aba-geral" class="tab-content active">
                <div class="cabecalho-topo">
                    <h2>Leitor de NF-e Avançado</h2>
                    <button id="btnSomGeral" class="btn-som ativo" onclick="alternarSom()">
                        <span class="iconeSom">🔊</span> <span class="textoSom">Som Ativo</span>
                    </button>
                </div>
                <div class="subtitle">Validação Estrita de Chave de Acesso + Controle por Transportadora</div>

                <div class="painel-transportadora">
                    <label for="inputTransportadora">🚚 Transportadora:</label>
                    <input type="text" id="inputTransportadora" placeholder="Digite a transportadora..." list="listaTransportadoras">
                    <datalist id="listaTransportadoras">
                        <option value="JADLOG"></option>
                        <option value="CORREIOS"></option>
                        <option value="BRASPRESS"></option>
                        <option value="TOTAL EXPRESS"></option>
                        <option value="LOGGI"></option>
                    </datalist>
                    <button id="btnTravaGeral" class="btn-trava destravado" onclick="alternarTravaGeral()">🔓 Destravado</button>
                </div>

                <input type="text" id="chaveGeralInput" class="input-chave" placeholder="Bipe a chave de acesso aqui..." autofocus autocomplete="off">
                <div id="erroGeral" class="erro"></div>

                <div class="painel-ultimo">
                    <div class="rotulo">Última Nota Bipada (Número NF-e)</div>
                    <div id="ultimoNumeroGeral" class="numero-destaque">---</div>
                    <div id="ultimaTranspGeral" class="transp-destaque"></div>
                    <div class="bloco-chave-completa">
                        <div class="rotulo" style="font-size: 10px; margin-bottom: 3px;">Chave de Acesso Completa (44 Dígitos)</div>
                        <div id="ultimaChaveGeral" class="chave-destaque">Aguardando bipagem...</div>
                    </div>
                </div>

                <div class="acoes-excel">
                    <button class="btn btn-copiar" onclick="copiarGeral()">📋 Copiar Tabela</button>
                    <button class="btn btn-excel" onclick="baixarCSVGeral()">📥 Baixar CSV</button>
                    <button class="btn btn-print" style="background-color: #0284c7; color: white;" onclick="imprimirPDFGeral()">📄 Imprimir / Salvar PDF</button>
                </div>

                <div style="margin-bottom: 20px;">
                    <button class="btn btn-finalizar" onclick="finalizarGeral()">🏁 Finalizar Bipagem e Arquivar Relatório</button>
                </div>

                <div class="cabecalho-secao">
                    <span class="rotulo">Lista Atual de Bipagem (Total: <span id="totalGeral">0</span>)</span>
                    <button class="btn-limpar" onclick="limparGeralAtual()">Limpar Sessão Atual</button>
                </div>

                <div class="tabela-container">
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 10%;">Hora</th>
                                <th style="width: 20%;">Transportadora</th>
                                <th style="width: 8%;">Série</th>
                                <th style="width: 18%;">Número da Nota</th>
                                <th style="width: 34%;">Chave de Acesso Completa</th>
                                <th style="width: 10%; text-align: center;">Ação</th>
                            </tr>
                        </thead>
                        <tbody id="tabelaGeral"></tbody>
                    </table>
                </div>

                <div class="historico-secao">
                    <h3>📁 Relatórios Finalizados e Arquivados (Geral)</h3>
                    <div id="arquivadosGeral"></div>
                </div>
            </div>

            <!-- ABA 2: CONFERÊNCIA SEDEX + RASTREIO -->
            <div id="aba-sedex" class="tab-content">
                <div class="cabecalho-topo">
                    <h2>Leitor NF-e + Rastreio SEDEX</h2>
                    <button id="btnSomSedex" class="btn-som ativo" onclick="alternarSom()">
                        <span class="iconeSom">🔊</span> <span class="textoSom">Som Ativo</span>
                    </button>
                </div>
                <div class="subtitle">Bipe a chave de acesso (44 dígitos) e em seguida o rastreio (Formato: AB...BR, AP...BR, AE...BR, AD...BR)</div>

                <div class="campos-leitura">
                    <div class="campo-box">
                        <label for="chaveSedexInput">1º Bipe: Nota Fiscal / Chave</label>
                        <input type="text" id="chaveSedexInput" class="input-campo" placeholder="Bipe a NF-e (44 dígitos)..." autocomplete="off">
                    </div>
                    <div class="campo-box">
                        <label for="rastreioSedexInput">2º Bipe: Rastreio SEDEX (AB, AP, AE, AD...BR)</label>
                        <input type="text" id="rastreioSedexInput" class="input-campo" placeholder="Aguardando 1º bipe..." disabled autocomplete="off">
                    </div>
                </div>

                <div id="erroSedex" class="erro"></div>

                <div class="painel-ultimo">
                    <div class="rotulo">Último Vínculo Salvo</div>
                    <div id="ultimoNumeroSedex" class="numero-destaque">---</div>
                    <div class="bloco-chave-completa">
                        <div class="rotulo" style="font-size: 10px; margin-bottom: 3px;">Chave de Acesso Completa</div>
                        <div id="ultimaChaveSedex" class="chave-destaque">Aguardando bipagem...</div>
                    </div>
                </div>

                <div class="acoes-excel">
                    <button class="btn btn-copiar" onclick="copiarSedex()">📋 Copiar Tabela</button>
                    <button class="btn btn-excel" onclick="baixarCSVSedex()">📥 Baixar CSV</button>
                    <button class="btn btn-print" style="background-color: #0284c7; color: white;" onclick="imprimirPDFSedex()">📄 Imprimir / Salvar PDF</button>
                </div>

                <div style="margin-bottom: 20px;">
                    <button class="btn btn-finalizar" onclick="finalizarSedex()">🏁 Finalizar Lote e Arquivar Relatório</button>
                </div>

                <div class="cabecalho-secao">
                    <span class="rotulo">Lista da Sessão Atual (Total: <span id="totalSedex">0</span>)</span>
                    <button class="btn-limpar" onclick="limparSedexAtual()">Limpar Sessão Atual</button>
                </div>

                <div class="tabela-container">
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 10%;">Hora</th>
                                <th style="width: 18%;">Número da Nota</th>
                                <th style="width: 42%;">Chave de Acesso Completa</th>
                                <th style="width: 20%;">Rastreio SEDEX</th>
                                <th style="width: 10%; text-align: center;">Ação</th>
                            </tr>
                        </thead>
                        <tbody id="tabelaSedex"></tbody>
                    </table>
                </div>

                <div class="historico-secao">
                    <h3>📁 Relatórios Finalizados e Arquivados (SEDEX)</h3>
                    <div id="arquivadosSedex"></div>
                </div>
            </div>

            <!-- ABA 3: CONFERÊNCIA PEDIDOS GELADO (SISTEMA DE LOTE QR CODE ATUALIZADO) -->
            <div id="aba-gelado" class="tab-content">
                <div class="flex justify-between items-center mb-1">
                    <h2 id="mainPageTitle">Conferência de Pedidos Gelados</h2>
                </div>
                <div id="mainPageSubtitle" class="subtitle">Gerenciamento de Lotes Refrigerados com Geração Automática de QR Code</div>

                <!-- Form de Cadastro -->
                <div class="form-card no-print">
                    <h3>Adicionar Novo Lote Refrigerado</h3>
                    <form id="loteForm">
                        <div class="form-grid">
                            <div class="form-group">
                                <label for="data">Previsão de Faturamento</label>
                                <input type="text" id="data" placeholder="Ex: 20/09/2026 ou 01/set" required>
                            </div>
                            <div class="form-group">
                                <label for="produto">Produto</label>
                                <input type="text" id="produto" placeholder="Ex: TIRZE. 60MG" required>
                            </div>
                            <div class="form-group">
                                <label for="lote">Número do Lote</label>
                                <input type="text" id="lote" maxlength="10" placeholder="Ex: 2608240003" required>
                                <span class="aviso-lote">
                                    <i class="fa-solid fa-circle-exclamation"></i> O lote deve conter exatamente 10 algarismos numéricos.
                                </span>
                            </div>
                            <button type="submit" class="btn-submit"><i class="fa-solid fa-plus"></i> Adicionar Lote</button>
                        </div>
                    </form>
                </div>

                <!-- Toolbar de Ações e Pesquisa por Dia -->
                <div class="toolbar no-print">
                    <div style="display: flex; gap: 12px; align-items: center; flex-wrap: wrap;">
                        <div class="search-box">
                            <i class="fa-solid fa-magnifying-glass text-slate-400"></i>
                            <input type="text" id="inputPesquisaDia" placeholder="Filtrar por dia de Faturamento..." onkeyup="filtrarPorDiaFaturamento()">
                        </div>
                        <span id="saveStatus" style="font-size: 13px; color: #10b981; font-weight: 600;">✓ Salvamento automático ativo</span>
                    </div>

                    <div class="toolbar-btns">
                        <button class="btn-action btn-print" onclick="solicitarSenhaEImprimir()">🖨️ Imprimir Relatório por Dia</button>
                        <button class="btn-action btn-archive" onclick="arquivarLotesAtuais()">📁 Arquivar Lotes do Dia</button>
                        <button class="btn-action btn-history" onclick="abrirHistorico()">🔍 Consultar Arquivo</button>
                        <button class="btn-action btn-customize" onclick="abrirModalPersonalizacao()">🎨 Personalizar Página</button>
                        <button class="btn-action" style="background-color: #64748b; color: white;" onclick="abrirModalConfiguracao()">🔑 Alterar Senha</button>
                        <button class="btn-action btn-reset" onclick="resetarDadosPadrao()">Restaurar Dados</button>
                    </div>
                </div>

                <!-- Tabela Principal Gelado -->
                <div class="tabela-container no-print">
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 20%;">PREVISÃO DE FATURAMENTO</th>
                                <th style="width: 38%;">PRODUTO</th>
                                <th style="width: 30%;">LOTE (QR CODE)</th>
                                <th style="width: 12%;">AÇÃO</th>
                            </tr>
                        </thead>
                        <tbody id="tabelaCorpo"></tbody>
                    </table>
                </div>

                <!-- Área de Impressão A4 Gelado -->
                <div id="areaImpressaoRelatorio"></div>
            </div>

            <!-- ABA 4: MANIFESTO / CHECKLIST DE TRANSPORTADORA SEDEX -->
            <div id="aba-manifesto" class="tab-content p-0 bg-slate-100 dark:bg-slate-900 overflow-hidden rounded-12">
                <!-- Topo Escuro Sedex -->
                <div class="bg-[#032b53] text-white px-6 py-4 flex flex-wrap justify-between items-center shadow-md no-print">
                    <div class="flex items-center gap-3">
                        <div class="bg-[#ffcc00] text-black font-extrabold px-3 py-1 rounded flex items-center gap-1 text-sm tracking-wide">
                            <i class="fa-solid fa-truck-fast"></i> SEDEX
                        </div>
                        <div>
                            <h2 class="text-lg font-bold text-white m-0 leading-tight">Manifesto de Coleta Expedição Alphaville</h2>
                            <p class="text-xs text-slate-300 m-0">Stin Pharma - Comprovante de Postagem e Conferência</p>
                        </div>
                    </div>
                    <div class="flex items-center gap-3">
                        <button onclick="loadSampleData()" class="bg-white/10 hover:bg-white/20 text-white border border-white/20 px-3 py-1.5 rounded text-xs font-semibold flex items-center gap-2 transition">
                            <i class="fa-solid fa-wand-magic-sparkles"></i> Carregar Dados Exemplo
                        </button>
                    </div>
                </div>

                <!-- Conteúdo Principal do Layout Manifesto -->
                <div class="p-6 space-y-6 no-print">
                    <div class="grid grid-cols-1 lg:grid-cols-12 gap-6">
                        <!-- Esquerda: Upload CSV + Instruções -->
                        <div class="lg:col-span-5 space-y-4">
                            <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm">
                                <h3 class="text-sm font-bold mb-3 text-slate-800 dark:text-slate-100 flex items-center gap-2">
                                    <i class="fa-solid fa-file-csv text-blue-600"></i> 1. Importar Arquivo CSV
                                </h3>
                                <div id="dropZone" class="border-2 border-dashed border-slate-300 dark:border-slate-600 rounded-xl p-8 text-center cursor-pointer hover:border-blue-500 hover:bg-blue-50/50 transition flex flex-col items-center justify-center">
                                    <input type="file" id="csvFileInput" accept=".csv, .txt" class="hidden" onchange="handleFileSelect(event)">
                                    <div class="w-12 h-12 bg-blue-100 text-blue-600 rounded-full flex items-center justify-center mb-3">
                                        <i class="fa-solid fa-cloud-arrow-up text-xl"></i>
                                    </div>
                                    <p class="text-sm font-bold text-slate-700 dark:text-slate-200 mb-1">Arraste e solte seu arquivo CSV aqui</p>
                                    <p class="text-xs text-slate-400 mb-3">ou clique para selecionar do seu computador (ponto e vírgula ou vírgula)</p>
                                    <span class="px-3 py-1 bg-blue-50 text-blue-700 text-[11px] font-semibold rounded-full border border-blue-200">Suporta 'NF E RASTREIOS SEDEX.csv'</span>
                                </div>
                                <div id="fileInfoBox" class="hidden mt-3 p-3 bg-emerald-50 dark:bg-emerald-950/30 border border-emerald-200 dark:border-emerald-800 rounded-xl flex justify-between items-center text-xs">
                                    <span id="fileNameText" class="font-bold text-emerald-800 dark:text-emerald-300 truncate"></span>
                                    <button onclick="resetData()" class="text-red-500 font-bold px-2 hover:bg-red-100 rounded">✕ Limpar</button>
                                </div>
                            </div>

                            <!-- Painel Informativo Lateral -->
                            <div class="bg-blue-50/70 dark:bg-slate-800/60 p-4 rounded-xl border border-blue-100 dark:border-slate-700 text-xs space-y-2 text-slate-700 dark:text-slate-300">
                                <p class="font-bold text-blue-900 dark:text-blue-300 flex items-center gap-1.5">
                                    <i class="fa-solid fa-circle-info"></i> Formatos de colunas detectados automaticamente:
                                </p>
                                <ul class="space-y-1 pl-4 list-disc marker:text-blue-500">
                                    <li><strong>Hora</strong> (Ex: 09:30, 14:15)</li>
                                    <li><strong>Número da Nota</strong> (Ex: NF-1001 ou 1001)</li>
                                    <li><strong>Chave de Acesso Completa</strong> (44 dígitos NFe)</li>
                                    <li><strong>Rastreio SEDEX</strong> (Ex: QC123456789BR)</li>
                                </ul>
                            </div>
                        </div>

                        <!-- Direita: Formulários e Configurações -->
                        <div class="lg:col-span-7">
                            <div class="bg-white dark:bg-slate-800 p-5 rounded-2xl border border-slate-200 dark:border-slate-700 shadow-sm space-y-4">
                                <h3 class="text-sm font-bold text-slate-800 dark:text-slate-100 flex items-center gap-2 border-b pb-3 border-slate-100 dark:border-slate-700">
                                    <i class="fa-solid fa-sliders text-blue-600"></i> 2. Configurações do Manifesto de Coleta
                                </h3>
                                <div class="grid grid-cols-1 md:grid-cols-2 gap-4 text-xs">
                                    <div>
                                        <label class="font-bold block mb-1 text-slate-600 dark:text-slate-300">Empresa / Remetente</label>
                                        <input type="text" id="cfgEmpresa" value="Stin Pharma - Excelência em Saúde" class="w-full p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white outline-none focus:border-blue-500">
                                    </div>
                                    <div>
                                        <label class="font-bold block mb-1 text-slate-600 dark:text-slate-300">Data / Hora da Coleta</label>
                                        <input type="datetime-local" id="cfgDataHora" class="w-full p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white outline-none focus:border-blue-500">
                                    </div>
                                    <div>
                                        <label class="font-bold block mb-1 text-slate-600 dark:text-slate-300">Nome do Coletor / Motorista SEDEX</label>
                                        <input type="text" id="cfgMotorista" placeholder="Ex: Carlos Andrade (Correios/SEDEX)" class="w-full p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white outline-none focus:border-blue-500">
                                    </div>
                                    <div>
                                        <label class="font-bold block mb-1 text-slate-600 dark:text-slate-300">Placa do Veículo / Código da Rota</label>
                                        <input type="text" id="cfgPlaca" placeholder="Ex: ABC-1234 / Rota Alphaville" class="w-full p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white outline-none focus:border-blue-500">
                                    </div>

                                    <!-- Logo Personalizado -->
                                    <div class="md:col-span-2">
                                        <label class="font-bold block mb-1 text-[#2563eb]">Logo da Empresa para Impressão</label>
                                        <div class="flex items-center gap-3">
                                            <div class="w-24 h-11 border border-slate-200 dark:border-slate-700 rounded-lg bg-slate-50 dark:bg-slate-900 flex items-center justify-center p-1" id="logoPreviewContainer">
                                            </div>
                                            <input type="file" id="logoFileInput" accept="image/*" class="hidden" onchange="handleLogoUpload(event)">
                                            <button onclick="document.getElementById('logoFileInput').click()" class="px-3 py-2 bg-slate-100 hover:bg-slate-200 dark:bg-slate-700 dark:hover:bg-slate-600 text-slate-700 dark:text-slate-200 rounded-lg text-xs font-bold border border-slate-300 dark:border-slate-600 flex items-center gap-1.5 transition">
                                                <i class="fa-solid fa-upload"></i> Alterar Logo
                                            </button>
                                            <button onclick="resetDefaultLogo()" class="text-xs text-blue-600 hover:underline font-bold">
                                                Restaurar Stin Pharma
                                            </button>
                                        </div>
                                    </div>

                                    <div class="md:col-span-2">
                                        <label class="font-bold block mb-1 text-slate-600 dark:text-slate-300">Observações Adicionais para a Transportadora</label>
                                        <input type="text" id="cfgObs" value="Coleta realizada na Expedição Alphaville. Encomendas com medicamentos e suprimentos identificados." class="w-full p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white outline-none focus:border-blue-500">
                                    </div>
                                </div>

                                <div class="pt-2 flex items-center gap-6 text-xs font-semibold text-slate-700 dark:text-slate-300">
                                    <label class="flex items-center gap-2 cursor-pointer">
                                        <input type="checkbox" id="cfgShowBarcode" checked class="w-4 h-4 text-blue-600 rounded"> Exibir Códigos de Barras no Impresso
                                    </label>
                                    <label class="flex items-center gap-2 cursor-pointer">
                                        <input type="checkbox" id="cfgTruncateKey" checked class="w-4 h-4 text-blue-600 rounded"> Resumir Chave NFe no impresso (Ex: ...1234)
                                    </label>
                                </div>

                                <div class="mt-4 pt-4 border-t border-slate-100 dark:border-slate-700 flex justify-between items-center">
                                    <span class="text-xs font-bold text-slate-500">Total na lista: <strong id="totalVolumesCount" class="text-slate-800 dark:text-white">0 volumes</strong></span>
                                    <div class="flex gap-3">
                                        <button onclick="previewPDFModal()" class="px-4 py-2.5 bg-slate-200 hover:bg-slate-300 dark:bg-slate-700 dark:hover:bg-slate-600 text-slate-800 dark:text-white rounded-lg text-xs font-bold transition flex items-center gap-1.5">
                                            <i class="fa-solid fa-eye"></i> Pré-visualizar Impressão
                                        </button>
                                        <button onclick="printChecklist()" class="px-5 py-2.5 bg-[#e69d00] hover:bg-[#c88800] text-slate-950 font-black rounded-lg text-xs shadow-md transition flex items-center gap-2">
                                            <i class="fa-solid fa-print"></i> IMPRIMIR / SALVAR PDF
                                        </button>
                                    </div>
                                </div>
                            </div>
                        </div>
                    </div>

                    <!-- Tabela de Pacotes Manifesto -->
                    <div class="bg-white dark:bg-slate-800 border border-slate-200 dark:border-slate-700 rounded-2xl overflow-hidden shadow-sm">
                        <div class="p-4 bg-slate-50 dark:bg-slate-900 border-b border-slate-200 dark:border-slate-700 flex justify-between items-center text-xs font-bold">
                            <span class="text-slate-700 dark:text-slate-200">Lista de Encomendas Carregadas (<span id="recordCounter">0 itens</span>)</span>
                            <input type="text" id="searchInput" onkeyup="filterData()" placeholder="Buscar nota, rastreio..." class="p-2 border rounded-lg w-56 text-xs dark:bg-slate-800 dark:border-slate-700 dark:text-white outline-none">
                        </div>
                        <div class="tabela-container" style="max-height: 380px;">
                            <table class="w-full text-xs">
                                <thead>
                                    <tr>
                                        <th style="width: 5%;">#</th>
                                        <th style="width: 10%;">Hora</th>
                                        <th style="width: 20%;">Nº Nota Fiscal</th>
                                        <th style="width: 25%;">Rastreio SEDEX</th>
                                        <th style="width: 30%;">Chave de Acesso</th>
                                        <th style="width: 10%; text-align: center;">Ação</th>
                                    </tr>
                                </thead>
                                <tbody id="tableBody">
                                    <tr id="emptyRow">
                                        <td colspan="6" class="center py-10 text-slate-400">Nenhum dado importado. Carregue um CSV ou selecione os dados de exemplo.</td>
                                    </tr>
                                </tbody>
                            </table>
                        </div>
                    </div>
                </div>
            </div>

            <!-- ABA 5: MANUTENÇÃO DO PROGRAMA E DIAGNÓSTICO -->
            <div id="aba-manutencao" class="tab-content">
                <div class="cabecalho-topo">
                    <h2>Manutenção do Programa e Diagnóstico</h2>
                    <span class="px-3 py-1 bg-blue-100 text-blue-800 dark:bg-blue-900 dark:text-blue-200 text-xs font-bold rounded-full">
                        <i class="fa-solid fa-microchip"></i> Versão 8.6 (Segurança Otimizada)
                    </span>
                </div>
                <div class="subtitle">Gestão de Ocorrências de Manutenção, Cópia de Segurança, Restauração e Saúde do Armazenamento</div>

                <!-- Indicadores de Diagnóstico e Armazenamento -->
                <div class="grid grid-cols-1 md:grid-cols-3 gap-4 mb-6">
                    <div class="bg-slate-50 dark:bg-slate-800 p-4 rounded-xl border border-slate-200 dark:border-slate-700">
                        <div class="flex items-center justify-between mb-2">
                            <span class="text-xs font-bold uppercase text-slate-500 dark:text-slate-400">Armazenamento Local</span>
                            <i class="fa-solid fa-database text-blue-500"></i>
                        </div>
                        <div class="text-2xl font-extrabold text-slate-800 dark:text-white" id="maintStorageUsage">0 KB / 5 MB</div>
                        <div class="w-full bg-slate-200 dark:bg-slate-700 h-2 rounded-full mt-2 overflow-hidden">
                            <div id="maintStorageBar" class="bg-blue-600 h-full w-0 transition-all duration-300"></div>
                        </div>
                    </div>

                    <div class="bg-slate-50 dark:bg-slate-800 p-4 rounded-xl border border-slate-200 dark:border-slate-700">
                        <div class="flex items-center justify-between mb-2">
                            <span class="text-xs font-bold uppercase text-slate-500 dark:text-slate-400">Estado do Sistema</span>
                            <i class="fa-solid fa-heart-pulse text-emerald-500"></i>
                        </div>
                        <div class="text-2xl font-extrabold text-emerald-600 dark:text-emerald-400 flex items-center gap-2">
                            <span class="w-3 h-3 bg-emerald-500 rounded-full animate-pulse"></span> Operacional
                        </div>
                        <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Todos os 5 módulos ativos e sem falhas</p>
                    </div>

                    <div class="bg-slate-50 dark:bg-slate-800 p-4 rounded-xl border border-slate-200 dark:border-slate-700">
                        <div class="flex items-center justify-between mb-2">
                            <span class="text-xs font-bold uppercase text-slate-500 dark:text-slate-400">Tarefas de Manutenção</span>
                            <i class="fa-solid fa-list-check text-amber-500"></i>
                        </div>
                        <div class="text-2xl font-extrabold text-slate-800 dark:text-white" id="maintPendingCount">0 Pendentes</div>
                        <p class="text-xs text-slate-500 dark:text-slate-400 mt-1">Pedidos de reparação e otimização</p>
                    </div>
                </div>

                <!-- Ferramentas de Manutenção de Base de Dados e Backups -->
                <div class="form-card mb-6">
                    <h3><i class="fa-solid fa-screwdriver-wrench text-blue-600"></i> Ferramentas de Manutenção da Base de Dados e Cópia de Segurança</h3>
                    <div class="flex flex-wrap gap-3">
                        <button class="btn btn-excel" style="flex:1; min-width: 200px;" onclick="exportarBackupGlobal()">
                            <i class="fa-solid fa-download"></i> Exportar Cópia de Segurança (JSON)
                        </button>
                        <label class="btn btn-copiar" style="flex:1; min-width: 200px; text-align: center; cursor: pointer; margin:0; display: flex; align-items: center; justify-content: center; gap: 8px;">
                            <i class="fa-solid fa-upload"></i> Importar Cópia de Segurança
                            <input type="file" id="fileImportBackup" accept=".json" class="hidden" onchange="importarBackupGlobal(event)">
                        </label>
                        <button class="btn btn-reset" style="flex:1; min-width: 200px;" onclick="limparCacheSessao()">
                            <i class="fa-solid fa-broom"></i> Limpar Sorteio / Cache Temporário
                        </button>
                        <button class="btn" style="flex:1; min-width: 200px; background-color: #991b1b; color: white;" onclick="resetTotalAplicacao()">
                            <i class="fa-solid fa-triangle-exclamation"></i> Reposição Total de Fábrica
                        </button>
                    </div>
                </div>

                <!-- Formulário de Registo de Incidente / Manutenção de Código -->
                <div class="form-card mb-6">
                    <h3><i class="fa-solid fa-bug text-red-500"></i> Registar Nova Ocorrência / Pedido de Manutenção</h3>
                    <form id="formManutencao" onsubmit="registarOcorrenciaManutencao(event)">
                        <div class="form-grid">
                            <div class="form-group">
                                <label for="maintModulo">Módulo Afetado</label>
                                <select id="maintModulo" required class="p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white text-sm outline-none">
                                    <option value="Conferência Geral NF-e">Conferência Geral NF-e</option>
                                    <option value="Conferência SEDEX">Conferência SEDEX</option>
                                    <option value="Pedidos Gelado (QR Code)">Pedidos Gelado (QR Code)</option>
                                    <option value="Manifesto / Checklist">Manifesto / Checklist</option>
                                    <option value="Núcleo / Sistema Geral">Núcleo / Sistema Geral</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label for="maintTipo">Tipo de Intervenção</label>
                                <select id="maintTipo" required class="p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white text-sm outline-none">
                                    <option value="Correção de Bug">Correção de Bug (Erro)</option>
                                    <option value="Atualização de Código">Atualização de Código / Feature</option>
                                    <option value="Otimização de Performance">Otimização de Performance</option>
                                    <option value="Ajuste de Layout / UI">Ajuste de Layout / UI</option>
                                </select>
                            </div>
                            <div class="form-group">
                                <label for="maintPrioridade">Prioridade</label>
                                <select id="maintPrioridade" required class="p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white text-sm outline-none">
                                    <option value="Baixa">Baixa</option>
                                    <option value="Média">Média</option>
                                    <option value="Alta">Alta</option>
                                    <option value="Crítica">Crítica</option>
                                </select>
                            </div>
                            <div class="form-group md:col-span-3">
                                <label for="maintDescricao">Descrição Detalhada da Manutenção</label>
                                <textarea id="maintDescricao" rows="2" required placeholder="Descreva o problema encontrado ou a alteração de código necessária..." class="p-2.5 border rounded-lg dark:bg-slate-700 dark:border-slate-600 dark:text-white text-sm outline-none"></textarea>
                            </div>
                            <button type="submit" class="btn-submit"><i class="fa-solid fa-plus-circle"></i> Adicionar Registo de Manutenção</button>
                        </div>
                    </form>
                </div>

                <!-- Tabela de Ocorrências e Tarefas de Manutenção -->
                <div class="cabecalho-secao">
                    <span class="rotulo">Lista de Manutenções e Ocorrências do Sistema (<span id="totalManutencoes">0</span>)</span>
                    <button class="btn-limpar" onclick="limparHistoricoManutencoes()">Limpar Concluídas</button>
                </div>

                <div class="tabela-container mb-6">
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 12%;">Data / Hora</th>
                                <th style="width: 18%;">Módulo</th>
                                <th style="width: 15%;">Tipo</th>
                                <th style="width: 10%;">Prioridade</th>
                                <th style="width: 25%;">Descrição</th>
                                <th style="width: 10%;">Estado</th>
                                <th style="width: 10%; text-align: center;">Ações</th>
                            </tr>
                        </thead>
                        <tbody id="tabelaManutencaoCorpo"></tbody>
                    </table>
                </div>

                <!-- Registo de Alterações (Changelog do Sistema) -->
                <div class="historico-secao">
                    <h3><i class="fa-solid fa-clock-rotate-left"></i> Histórico de Atualizações de Software (Changelog)</h3>
                    <div class="space-y-3 mt-3">
                        <div class="historico-card">
                            <div>
                                <strong>v8.6 (Atual) - Correções de Segurança e Performance</strong><br>
                                <small class="text-slate-500">Implementação de Hash local para senhas, motor interno de QR Code (offline-first) e correção do exportador CSV de relatórios mistos.</small>
                            </div>
                            <span class="px-2.5 py-1 bg-emerald-100 text-emerald-800 font-bold text-xs rounded-full">Ativa</span>
                        </div>
                        <div class="historico-card">
                            <div>
                                <strong>v8.5 - Módulo de Manutenção Integrado</strong><br>
                                <small class="text-slate-500">Inclusão do Painel de Diagnóstico de Armazenamento, Exportação/Importação de Backup em JSON, Registo de Incidentes e Controlo de Versões.</small>
                            </div>
                            <span class="px-2.5 py-1 bg-slate-200 text-slate-700 font-bold text-xs rounded-full">Concluído</span>
                        </div>
                        <div class="historico-card">
                            <div>
                                <strong>v8.0 - Manifesto e Checklist SEDEX</strong><br>
                                <small class="text-slate-500">Importação de ficheiros CSV, geração dinâmica de códigos de barras CODE128 e modelo de impressão A4 formal.</small>
                            </div>
                            <span class="px-2.5 py-1 bg-slate-200 text-slate-700 font-bold text-xs rounded-full">Concluído</span>
                        </div>
                    </div>
                </div>
            </div>

            <footer class="footer no-print">
                Created by Vitor Tenório | Stin Pharma
            </footer>
        </main>
    </div>

    <!-- ÁREA DE IMPRESSÃO EXCLUSIVA DO MANIFESTO SEDEX -->
    <div id="printableArea" class="print-only max-w-4xl mx-auto bg-white p-4 text-black">
        <div class="border-2 border-black p-4 mb-4">
            <div class="flex justify-between items-center border-b-2 border-black pb-3 mb-3 gap-3">
                <div class="flex items-center gap-4">
                    <div id="printLogoContainer" class="w-36 h-16 flex items-center justify-center"></div>
                    <div class="border-l-2 border-black pl-4">
                        <h1 class="text-base font-black uppercase tracking-tight leading-tight">MANIFESTO DE COLETA EXPEDIÇÃO ALPHAVILLE</h1>
                        <p class="text-[11px] font-bold uppercase mt-0.5 text-gray-800">Checklist Oficial de Conferência de Encomendas SEDEX</p>
                    </div>
                </div>
                <div class="text-right text-xs font-mono">
                    <div class="font-black text-sm tracking-tighter border-2 border-black px-3 py-1 bg-yellow-300 inline-block mb-1">CORREIOS / SEDEX</div>
                    <p class="font-bold text-[10px]">VIA TRANSPORTADORA / REMETENTE</p>
                    <p class="text-[10px]">Doc ID: <span id="printDocId"></span></p>
                    <p class="text-[10px]">Emissão: <span id="printEmitDate"></span></p>
                </div>
            </div>

            <div class="grid grid-cols-2 gap-4 text-xs">
                <div>
                    <p class="mb-1"><strong>Remetente/Empresa:</strong> <span id="printEmpresa"></span></p>
                    <p class="mb-1"><strong>Data/Hora Coleta:</strong> <span id="printDataHora"></span></p>
                    <p><strong>Observações:</strong> <span id="printObs"></span></p>
                </div>
                <div>
                    <p class="mb-1"><strong>Motorista / Coletor:</strong> <span id="printMotorista"></span></p>
                    <p class="mb-1"><strong>Placa / Rota:</strong> <span id="printPlaca"></span></p>
                    <p><strong>Total de Encomendas / Volumes:</strong> <span id="printTotalCount" class="font-bold underline"></span></p>
                </div>
            </div>
        </div>

        <table class="w-full text-xs text-center mb-4 border border-black mx-auto">
            <thead>
                <tr class="bg-gray-200 text-black uppercase font-bold text-[10px] border-b border-black">
                    <th class="p-1.5 border-r border-black w-8 text-center">Nº</th>
                    <th class="p-1.5 border-r border-black w-16 text-center">Hora</th>
                    <th class="p-1.5 border-r border-black w-28 text-center">Nº da Nota</th>
                    <th class="p-1.5 border-r border-black text-center">Código de Rastreio SEDEX</th>
                    <th class="p-1.5 border-r border-black text-center">Chave NFe</th>
                    <th class="p-1.5 w-12 text-center">Conf.</th>
                </tr>
            </thead>
            <tbody id="printTableBody"></tbody>
        </table>

        <div class="border border-black p-2.5 mb-4 text-xs bg-gray-50 flex justify-between items-center font-bold">
            <span>RESUMO DE CONFERÊNCIA DA CARGA:</span>
            <span>Total Declarado: <span id="printTotalSummary">0</span> vol.</span>
            <span>Total Coletado [ ____ ] vol.</span>
            <span>Divergências [ ____ ] vol.</span>
        </div>

        <div class="border-2 border-black p-4 text-xs space-y-6">
            <p class="text-[10px] leading-relaxed text-justify">
                Atestamos que os volumes relacionados neste manifesto foram entregues/coletados em embalagens íntegras e identificadas com os respectivos códigos de rastreamento SEDEX. A responsabilidade pelo conteúdo e autenticidade das Notas Fiscais é do remetente.
            </p>
            <div class="grid grid-cols-2 gap-12 pt-6">
                <div class="text-center">
                    <div class="border-b border-black mb-1.5"></div>
                    <p class="font-bold">Assinatura do Responsável pelo Envio</p>
                    <p class="text-[10px] text-gray-600">(Empresa / Expedição)</p>
                </div>
                <div class="text-center">
                    <div class="border-b border-black mb-1.5"></div>
                    <p class="font-bold">Assinatura e Matrícula do Coletor SEDEX</p>
                    <p class="text-[10px] text-gray-600">(Correios / Transportadora)</p>
                </div>
            </div>
        </div>
    </div>

    <!-- Modais Gelado -->
    <div id="modalImpressao" class="modal no-print">
        <div class="modal-content">
            <div class="modal-header">
                <h3>🖨️ Opções de Impressão de Relatório com QR Code</h3>
                <button class="btn-close" onclick="fecharModalImpressao()">✕ Fechar</button>
            </div>
            <p>Selecione a data para gerar o relatório impresso contendo todos os QR Codes dos lotes organizados:</p>
            <div style="display: flex; gap: 10px; align-items: center; margin: 20px 0;">
                <label for="selectDiaPrint" style="font-weight: bold; min-width: 120px;">Data do Dia:</label>
                <select id="selectDiaPrint" style="padding: 10px; border-radius: 6px; flex: 1; border: 1px solid var(--border-color); background: var(--input-bg); color: var(--text-color); font-size: 14px;"></select>
            </div>
            <div style="display: flex; justify-content: flex-end; gap: 10px; margin-top: 25px;">
                <button class="btn-action btn-history" onclick="gerarEImprimir('todos')">🖨️ Imprimir Todos os Dias</button>
                <button class="btn-action btn-print" onclick="gerarEImprimir('selecionado')">🖨️ Imprimir Dia Selecionado</button>
            </div>
        </div>
    </div>

    <div id="modalHistorico" class="modal no-print">
        <div class="modal-content">
            <div class="modal-header">
                <h3>📁 Lotes Arquivados (Consultas Futuras)</h3>
                <button class="btn-close" onclick="fecharHistorico()">✕ Fechar</button>
            </div>
            <table>
                <thead>
                    <tr>
                        <th>PREVISÃO FATURAMENTO</th>
                        <th>DATA ARQUIVAMENTO</th>
                        <th>SELO AUTENTICIDADE</th>
                        <th>PRODUTO</th>
                        <th>LOTE</th>
                        <th>AÇÕES</th>
                    </tr>
                </thead>
                <tbody id="tabelaHistorico"></tbody>
            </table>
        </div>
    </div>

    <!-- Modal Personalização de Página com Color Pickers -->
    <div id="modalPersonalizacao" class="modal no-print">
        <div class="modal-content" style="max-width: 600px;">
            <div class="modal-header">
                <h3>🎨 Personalizar Página e Cores</h3>
                <button class="btn-close" onclick="fecharModalPersonalizacao()">✕ Fechar</button>
            </div>
            <form id="formPersonalizacao" onsubmit="salvarPersonalizacao(event)">
                <div class="form-group" style="margin-bottom: 15px;">
                    <label for="inputEmpresaNome">Nome da Empresa / Título Principal:</label>
                    <input type="text" id="inputEmpresaNome" required placeholder="Ex: Stin Pharma" style="width: 100%;">
                </div>
                <div class="form-group" style="margin-bottom: 15px;">
                    <label for="inputSubtituloPgn">Subtítulo da Página:</label>
                    <input type="text" id="inputSubtituloPgn" placeholder="Ex: Gerenciamento de Lotes Refrigerados..." style="width: 100%;">
                </div>

                <!-- Seção de Personalização de Cores -->
                <div style="margin-bottom: 15px;">
                    <label style="font-size: 12px; font-weight: 700; color: var(--text-color); text-transform: uppercase; display: block; margin-bottom: 8px;">
                        🎨 Cores Personalizadas da Interface:
                    </label>
                    <div class="color-pickers-grid">
                        <div class="color-picker-item">
                            <label for="colorBg">Cor de Fundo:</label>
                            <input type="color" id="colorBg" oninput="previewCor('bg', this.value)">
                        </div>
                        <div class="color-picker-item">
                            <label for="colorCard">Cartões / Tabelas:</label>
                            <input type="color" id="colorCard" oninput="previewCor('card', this.value)">
                        </div>
                        <div class="color-picker-item">
                            <label for="colorText">Cor do Texto:</label>
                            <input type="color" id="colorText" oninput="previewCor('text', this.value)">
                        </div>
                        <div class="color-picker-item">
                            <label for="colorPrimary">Cor Primária / Botão:</label>
                            <input type="color" id="colorPrimary" oninput="previewCor('primary', this.value)">
                        </div>
                    </div>
                </div>

                <div class="form-group" style="margin-bottom: 15px;">
                    <label for="inputLogoFile">Carregar Novo Logo (Imagem):</label>
                    <input type="file" id="inputLogoFile" accept="image/*" onchange="processarUploadLogo(this)" style="width: 100%;">
                    <small style="color: #64748b; font-size: 11px; margin-top: 4px;">A imagem será salva no seu navegador para uso contínuo.</small>
                </div>
                <div class="form-group" style="margin-bottom: 20px;">
                    <label for="inputTextoRodape">Mensagem de Rodapé da Impressão:</label>
                    <textarea id="inputTextoRodape" rows="2" style="width: 100%; border-radius: 6px;" placeholder="Ex: Documento gerado eletronicamente..."></textarea>
                </div>

                <div style="display: flex; gap: 10px; justify-content: flex-end;">
                    <button type="button" class="btn-action btn-reset" style="padding: 8px 12px;" onclick="restaurarPersonalizacaoPadrao()">Restaurar Padrão</button>
                    <button type="submit" class="btn-submit" style="width: auto; padding: 10px 20px; margin-top:0;">💾 Salvar Personalização</button>
                </div>
            </form>
        </div>
    </div>

    <!-- Modal Segurança / Alterar Senha -->
    <div id="modalConfiguracao" class="modal no-print">
        <div class="modal-content" style="max-width: 450px;">
            <div class="modal-header">
                <h3>🔑 Alterar Senha de Segurança</h3>
                <button class="btn-close" onclick="fecharModalConfiguracao()">✕ Fechar</button>
            </div>
            <p style="font-size: 14px; margin-bottom: 20px;">Digite a nova senha de proteção para impressões, exclusões e re-impressões:</p>
            <form id="formNovaSenha" onsubmit="salvarNovaSenha(event)">
                <div class="form-group" style="margin-bottom: 15px;">
                    <label for="inputNovaSenha">Nova Senha:</label>
                    <input type="password" id="inputNovaSenha" required placeholder="Digite a nova senha..." style="width: 100%;">
                </div>
                <div class="form-group" style="margin-bottom: 20px;">
                    <label for="inputConfirmaSenha">Confirme a Nova Senha:</label>
                    <input type="password" id="inputConfirmaSenha" required placeholder="Repita a nova senha..." style="width: 100%;">
                </div>
                <button type="submit" class="btn-submit" style="width: 100%;">💾 Salvar Nova Senha</button>
            </form>
        </div>
    </div>

    <!-- Modal Pré-visualização do Manifesto -->
    <div id="previewModal" class="modal no-print">
        <div class="modal-content" style="max-width: 950px;">
            <div class="modal-header">
                <h3>👁️ Pré-visualização do Manifesto A4 Centralizado</h3>
                <button class="btn-close" onclick="closePreviewModal()">✕ Fechar</button>
            </div>
            <div id="modalPreviewContainer" class="bg-white text-black p-4 rounded overflow-x-auto"></div>
            <div style="display: flex; justify-content: flex-end; gap: 10px; margin-top: 15px;">
                <button class="btn-action btn-print" onclick="printChecklist()">🖨️ Imprimir Agora</button>
            </div>
        </div>
    </div>

    <div id="toast" class="notificacao">Ação realizada com sucesso!</div>

    <script>
        /* --- SISTEMA DE AUTENTICAÇÃO E CONTROLOS DE ACESSO --- */
        const CREDENCIAIS_SISTEMA = {
            utilizador: "admin",
            palavraPasse: "3206"
        };

        // Função de hash básica síncrona para evitar armazenamento em plain text
        function hashSenha(str) {
            let hash = 0;
            if (str.length === 0) return hash.toString();
            for (let i = 0; i < str.length; i++) {
                hash = Math.imul(31, hash) + str.charCodeAt(i) | 0;
            }
            return hash.toString();
        }

        function verificarAutenticacao() {
            const estaAutenticado = sessionStorage.getItem('logistica_autenticado');
            const overlay = document.getElementById('loginOverlay');
            
            if (estaAutenticado === 'true') {
                if (overlay) overlay.style.display = 'none';
            } else {
                if (overlay) overlay.style.display = 'flex';
            }
        }

        function validarLogin(e) {
            e.preventDefault();
            const user = document.getElementById('inputUsuario').value.trim();
            const pass = document.getElementById('inputSenha').value.trim();
            const erroEl = document.getElementById('loginError');

            if (user === CREDENCIAIS_SISTEMA.utilizador && pass === CREDENCIAIS_SISTEMA.palavraPasse) {
                sessionStorage.setItem('logistica_autenticado', 'true');
                document.getElementById('loginOverlay').style.display = 'none';
                document.getElementById('inputUsuario').value = '';
                document.getElementById('inputSenha').value = '';
                erroEl.innerText = '';
                
                if (typeof tocarSomSucesso === 'function') tocarSomSucesso();
                if (typeof exibirToast === 'function') exibirToast('Sessão iniciada com sucesso!');
            } else {
                erroEl.innerText = '⚠️ Utilizador ou palavra-passe incorretos!';
                if (typeof tocarSomErro === 'function') tocarSomErro();
            }
        }

        function terminarSessao() {
            if (confirm('Deseja realmente terminar a sessão?')) {
                sessionStorage.removeItem('logistica_autenticado');
                verificarAutenticacao();
            }
        }

        const CHAVE_PERMITIDA_REPETICAO = "11111111111111111111111111111111111111111111";
        const LOGO_PADRAO_BASE64 = `data:image/jpeg;base64,/9j/4AAQSkZJRgABAQEASABIAAD/2wBDAP//////////////////////////////////////////////////////////////////////////////////////wgALCAEAAQABREA/8QAFBABAAAAAAAAAAAAAAAAAAAAAP/aAAgBAQABPxA=`;

        const CORES_PADRAO_LIGHT = {
            bg: '#f0f2f5',
            card: '#ffffff',
            text: '#333333',
            primary: '#2563eb'
        };

        let abaAtiva = 'geral';

        /* --- CONTROLE DA BARRA LATERAL (TOGGLE SIDEBAR) --- */
        function toggleSidebar() {
            const sidebar = document.getElementById('sidebarNav');
            const mainWrapper = document.getElementById('mainWrapper');
            const toggleIcon = document.getElementById('toggleIcon');
            const toggleText = document.getElementById('toggleText');

            const isMobile = window.innerWidth <= 768;

            if (isMobile) {
                sidebar.classList.toggle('mobile-open');
            } else {
                sidebar.classList.toggle('collapsed');
                mainWrapper.classList.toggle('expanded');
            }

            const isCollapsed = sidebar.classList.contains('collapsed') || (!sidebar.classList.contains('mobile-open') && isMobile);
            
            if (isCollapsed) {
                toggleIcon.className = 'fa-solid fa-bars';
                toggleText.innerText = 'Exibir Menu';
            } else {
                toggleIcon.className = 'fa-solid fa-indent';
                toggleText.innerText = 'Ocultar Menu';
            }
        }

        function alternarTema() {
            document.body.classList.toggle('dark-mode');
            document.body.classList.toggle('dark');
            const isDark = document.body.classList.contains('dark-mode');
            document.getElementById('themeIcon').innerText = isDark ? '☀️' : '🌙';
            document.getElementById('themeText').innerText = isDark ? 'Tema Claro' : 'Tema Escuro';
            localStorage.setItem('tema_preferido', isDark ? 'dark' : 'light');

            aplicarCoresSalvas();
        }

        function carregarTema() {
            const temaSalvo = localStorage.getItem('tema_preferido');
            if (temaSalvo === 'dark') {
                document.body.classList.add('dark-mode', 'dark');
                document.getElementById('themeIcon').innerText = '☀️';
                document.getElementById('themeText').innerText = 'Tema Claro';
            }
        }

        /* --- GERENCIAMENTO DINÂMICO DE CORES --- */
        function aplicarCoresSalvas() {
            const isDark = document.body.classList.contains('dark-mode');
            
            const customBg = localStorage.getItem('cfg_color_bg');
            const customCard = localStorage.getItem('cfg_color_card');
            const customText = localStorage.getItem('cfg_color_text');
            const customPrimary = localStorage.getItem('cfg_color_primary');

            if (!isDark) {
                if (customBg) document.documentElement.style.setProperty('--bg-color', customBg);
                else document.documentElement.style.removeProperty('--bg-color');

                if (customCard) document.documentElement.style.setProperty('--card-bg', customCard);
                else document.documentElement.style.removeProperty('--card-bg');

                if (customText) {
                    document.documentElement.style.setProperty('--text-color', customText);
                    document.documentElement.style.setProperty('--title-color', customText);
                } else {
                    document.documentElement.style.removeProperty('--text-color');
                    document.documentElement.style.removeProperty('--title-color');
                }
            } else {
                document.documentElement.style.removeProperty('--bg-color');
                document.documentElement.style.removeProperty('--card-bg');
                document.documentElement.style.removeProperty('--text-color');
                document.documentElement.style.removeProperty('--title-color');
            }

            if (customPrimary) {
                document.documentElement.style.setProperty('--primary-color', customPrimary);
            } else {
                document.documentElement.style.removeProperty('--primary-color');
            }
        }

        function previewCor(tipo, valor) {
            if (document.body.classList.contains('dark-mode')) return;

            if (tipo === 'bg') document.documentElement.style.setProperty('--bg-color', valor);
            if (tipo === 'card') document.documentElement.style.setProperty('--card-bg', valor);
            if (tipo === 'text') {
                document.documentElement.style.setProperty('--text-color', valor);
                document.documentElement.style.setProperty('--title-color', valor);
            }
            if (tipo === 'primary') document.documentElement.style.setProperty('--primary-color', valor);
        }

        /* --- PERSONALIZAÇÃO DE PÁGINA --- */
        function carregarPersonalizacao() {
            const empresa = localStorage.getItem('cfg_empresa_nome') || "Stin Pharma";
            const subtitulo = localStorage.getItem('cfg_subtitulo_pgn') || "Gerenciamento de Lotes Refrigerados com Geração Automática de QR Code";
            const logo = localStorage.getItem('cfg_logo_base64') || LOGO_PADRAO_BASE64;

            document.getElementById('headerTitleDisplay').innerText = `${empresa} | Sistema Integrado de Logística e Conferência`;
            document.getElementById('mainPageSubtitle').innerText = subtitulo;
            
            const imgHeader = document.getElementById('imgLogoHeader');
            imgHeader.src = logo;
            imgHeader.style.display = 'block';
            document.getElementById('fallbackIcon').style.display = 'none';

            aplicarCoresSalvas();
        }

        function abrirModalPersonalizacao() {
            document.getElementById('inputEmpresaNome').value = localStorage.getItem('cfg_empresa_nome') || "Stin Pharma";
            document.getElementById('inputSubtituloPgn').value = localStorage.getItem('cfg_subtitulo_pgn') || "Gerenciamento de Lotes Refrigerados com Geração Automática de QR Code";
            document.getElementById('inputTextoRodape').value = localStorage.getItem('cfg_texto_rodape') || "Documento gerado e validado eletronicamente pelo sistema de logística.";
            
            document.getElementById('colorBg').value = localStorage.getItem('cfg_color_bg') || CORES_PADRAO_LIGHT.bg;
            document.getElementById('colorCard').value = localStorage.getItem('cfg_color_card') || CORES_PADRAO_LIGHT.card;
            document.getElementById('colorText').value = localStorage.getItem('cfg_color_text') || CORES_PADRAO_LIGHT.text;
            document.getElementById('colorPrimary').value = localStorage.getItem('cfg_color_primary') || CORES_PADRAO_LIGHT.primary;

            tempLogoBase64 = null;
            document.getElementById('modalPersonalizacao').style.display = 'flex';
        }

        function fecharModalPersonalizacao() {
            document.getElementById('modalPersonalizacao').style.display = 'none';
            aplicarCoresSalvas();
        }

        let tempLogoBase64 = null;
        function processarUploadLogo(input) {
            if (input.files && input.files[0]) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    tempLogoBase64 = e.target.result;
                };
                reader.readAsDataURL(input.files[0]);
            }
        }

        function salvarPersonalizacao(e) {
            e.preventDefault();
            const empresa = document.getElementById('inputEmpresaNome').value.trim();
            const subtitulo = document.getElementById('inputSubtituloPgn').value.trim();
            const rodape = document.getElementById('inputTextoRodape').value.trim();

            if (empresa) localStorage.setItem('cfg_empresa_nome', empresa);
            if (subtitulo) localStorage.setItem('cfg_subtitulo_pgn', subtitulo);
            if (rodape) localStorage.setItem('cfg_texto_rodape', rodape);
            if (tempLogoBase64) localStorage.setItem('cfg_logo_base64', tempLogoBase64);

            localStorage.setItem('cfg_color_bg', document.getElementById('colorBg').value);
            localStorage.setItem('cfg_color_card', document.getElementById('colorCard').value);
            localStorage.setItem('cfg_color_text', document.getElementById('colorText').value);
            localStorage.setItem('cfg_color_primary', document.getElementById('colorPrimary').value);

            carregarPersonalizacao();
            document.getElementById('modalPersonalizacao').style.display = 'none';
            exibirToast("✅ Personalização e cores salvas com sucesso!");
        }

        function restaurarPersonalizacaoPadrao() {
            if (confirm("Deseja restaurar as configurações visuais e cores para o padrão da Stin Pharma?")) {
                localStorage.removeItem('cfg_empresa_nome');
                localStorage.removeItem('cfg_subtitulo_pgn');
                localStorage.removeItem('cfg_texto_rodape');
                localStorage.removeItem('cfg_logo_base64');
                localStorage.removeItem('cfg_color_bg');
                localStorage.removeItem('cfg_color_card');
                localStorage.removeItem('cfg_color_text');
                localStorage.removeItem('cfg_color_primary');
                
                carregarPersonalizacao();
                document.getElementById('modalPersonalizacao').style.display = 'none';
                exibirToast("Padrão visual restaurado!");
            }
        }

        function trocarAba(nomeAba, evt) {
            abaAtiva = nomeAba;
            document.querySelectorAll('.tab-content').forEach(el => el.classList.remove('active'));
            document.querySelectorAll('.nav-tab-btn').forEach(el => el.classList.remove('active'));

            if (nomeAba === 'geral') {
                document.getElementById('aba-geral').classList.add('active');
                if (evt) evt.currentTarget.classList.add('active');
                inputChaveGeral.focus();
            } else if (nomeAba === 'sedex') {
                document.getElementById('aba-sedex').classList.add('active');
                if (evt) evt.currentTarget.classList.add('active');
                focarAbaSedex();
            } else if (nomeAba === 'gelado') {
                document.getElementById('aba-gelado').classList.add('active');
                if (evt) evt.currentTarget.classList.add('active');
            } else if (nomeAba === 'manifesto') {
                document.getElementById('aba-manifesto').classList.add('active');
                if (evt) evt.currentTarget.classList.add('active');
            } else if (nomeAba === 'manutencao') {
                document.getElementById('aba-manutencao').classList.add('active');
                if (evt) evt.currentTarget.classList.add('active');
                atualizarDiagnosticoStorage();
                renderizarTabelaManutencao();
            }

            if (window.innerWidth <= 768) {
                document.getElementById('sidebarNav').classList.remove('mobile-open');
            }
        }

        document.addEventListener('click', (e) => {
            if (!['BUTTON', 'INPUT', 'OPTION', 'SELECT', 'A', 'I', 'SPAN', 'TEXTAREA'].includes(e.target.tagName)) {
                if (abaAtiva === 'geral') inputChaveGeral.focus();
                else if (abaAtiva === 'sedex') focarAbaSedex();
            }
        });

        /* --- CONTROLE DE ÁUDIO --- */
        let somAtivo = localStorage.getItem('som_leitor_nfe') !== 'desativado';
        let audioCtx = null;

        function alternarSom() {
            somAtivo = !somAtivo;
            localStorage.setItem('som_leitor_nfe', somAtivo ? 'ativado' : 'desativado');
            atualizarSomUI();
            if (somAtivo) tocarSomSucesso();
            if (abaAtiva === 'geral') inputChaveGeral.focus(); else if (abaAtiva === 'sedex') focarAbaSedex();
        }

        function atualizarSomUI() {
            document.querySelectorAll('.btn-som').forEach(btn => {
                btn.className = somAtivo ? 'btn-som ativo' : 'btn-som mudo';
                btn.querySelector('.iconeSom').innerText = somAtivo ? '🔊' : '🔇';
                btn.querySelector('.textoSom').innerText = somAtivo ? 'Som Ativo' : 'Muted (Silenciado)';
            });
        }

        function iniciarAudio() {
            if (!audioCtx) audioCtx = new (window.AudioContext || window.webkitAudioContext)();
        }

        function tocarSomSucesso() {
            if (!somAtivo) return;
            try {
                iniciarAudio();
                const now = audioCtx.currentTime;
                const osc1 = audioCtx.createOscillator(), g1 = audioCtx.createGain();
                osc1.type = 'sine'; osc1.frequency.setValueAtTime(880, now);
                g1.gain.setValueAtTime(0.1, now); g1.gain.exponentialRampToValueAtTime(0.01, now + 0.1);
                osc1.connect(g1); g1.connect(audioCtx.destination); osc1.start(now); osc1.stop(now + 0.1);

                const osc2 = audioCtx.createOscillator(), g2 = audioCtx.createGain();
                osc2.type = 'sine'; osc2.frequency.setValueAtTime(1760, now + 0.08);
                g2.gain.setValueAtTime(0.15, now + 0.08); g2.gain.exponentialRampToValueAtTime(0.01, now + 0.25);
                osc2.connect(g2); g2.connect(audioCtx.destination); osc2.start(now + 0.08); osc2.stop(now + 0.25);
            } catch (e) {}
        }

        function tocarSomErro() {
            if (!somAtivo) return;
            try {
                iniciarAudio();
                const now = audioCtx.currentTime;
                const osc = audioCtx.createOscillator(), g = audioCtx.createGain();
                osc.type = 'sawtooth'; osc.frequency.setValueAtTime(150, now);
                osc.frequency.setValueAtTime(100, now + 0.12);
                g.gain.setValueAtTime(0.2, now); g.gain.exponentialRampToValueAtTime(0.01, now + 0.35);
                osc.connect(g); g.connect(audioCtx.destination); osc.start(now); osc.stop(now + 0.35);
            } catch (e) {}
        }

        /* --- ABA 1: CONFERÊNCIA GERAL --- */
        let registrosGeral = [];
        let historicoGeral = JSON.parse(localStorage.getItem('relatorios_nfe_arquivados') || '[]');
        let travaTranspGeral = false;

        const toast = document.getElementById('toast');
        const inputChaveGeral = document.getElementById('chaveGeralInput');
        const inputTranspGeral = document.getElementById('inputTransportadora');
        const btnTravaGeral = document.getElementById('btnTravaGeral');
        const displayNumeroGeral = document.getElementById('ultimoNumeroGeral');
        const displayTranspGeral = document.getElementById('ultimaTranspGeral');
        const displayChaveGeral = document.getElementById('ultimaChaveGeral');
        const displayErroGeral = document.getElementById('erroGeral');
        const tabelaGeral = document.getElementById('tabelaGeral');
        const totalGeral = document.getElementById('totalGeral');
        const arquivadosGeralContainer = document.getElementById('arquivadosGeral');

        function alternarTravaGeral() {
            if (!inputTranspGeral.value.trim() && !travaTranspGeral) {
                tocarSomErro();
                alert('Digite o nome de uma transportadora antes de travar!');
                inputTranspGeral.focus();
                return;
            }
            travaTranspGeral = !travaTranspGeral;
            inputTranspGeral.disabled = travaTranspGeral;
            btnTravaGeral.className = travaTranspGeral ? 'btn-trava travado' : 'btn-trava destravado';
            btnTravaGeral.innerText = travaTranspGeral ? '🔒 Travado' : '🔓 Destravado';
            inputChaveGeral.focus();
        }

        inputChaveGeral.addEventListener('keydown', (e) => {
            if (e.key === 'Enter') processarGeral(inputChaveGeral.value.trim());
        });

        inputChaveGeral.addEventListener('input', function() {
            const v = this.value.trim();
            if (v.length === 44 && /^\d+$/.test(v)) processarGeral(v);
            else if (v.length > 44) {
                tocarSomErro();
                displayErroGeral.innerText = '❌ CÓDIGO INVÁLIDO: Contém mais de 44 dígitos!';
                this.value = '';
            }
        });

        function processarGeral(valor) {
            if (!valor) return;
            if (/[a-zA-Z]/.test(valor) || valor.length !== 44 || !/^\d+$/.test(valor)) {
                tocarSomErro();
                displayErroGeral.innerText = '⛔ REJEITADO: Isto não é uma chave de NF-e válida!';
                inputChaveGeral.value = '';
                return;
            }

            if (registrosGeral.some(i => i.chave === valor) && valor !== CHAVE_PERMITIDA_REPETICAO) {
                tocarSomErro();
                displayErroGeral.innerText = '⚠️ ATENÇÃO: Esta chave de acesso já foi bipada!';
                inputChaveGeral.value = '';
                return;
            }

            const transp = inputTranspGeral.value.trim().toUpperCase() || 'NÃO INFORMADA';
            const serie = valor.substring(22, 25);
            const numBruto = valor.substring(25, 34);
            const numLimpo = parseInt(numBruto, 10).toString();
            const hora = new Date().toLocaleTimeString('pt-BR');

            registrosGeral.unshift({ hora, transportadora: transp, serie, numeroBruto: numBruto, numeroLimpo: numLimpo, chave: valor });
            tocarSomSucesso();

            displayNumeroGeral.innerText = `${numBruto} (${numLimpo})`;
            displayTranspGeral.innerText = `Transportadora: ${transp}`;
            displayChaveGeral.innerText = valor;
            displayErroGeral.innerText = '';
            inputChaveGeral.value = '';

            renderizarInterfaceGeral();
        }

        function renderizarInterfaceGeral() {
            tabelaGeral.innerHTML = '';
            registrosGeral.forEach((i, idx) => {
                tabelaGeral.innerHTML += `
                    <tr>
                        <td class="center">${i.hora}</td>
                        <td><b>${i.transportadora}</b></td>
                        <td class="center">${i.serie}</td>
                        <td class="col-nota">${i.numeroBruto}</td>
                        <td class="col-chave">${i.chave}</td>
                        <td style="text-align: center;">
                            <button class="btn-deletar-linha" onclick="excluirItemGeral(${idx})" title="Remover este registro">❌ Excluir</button>
                        </td>
                    </tr>`;
            });
            totalGeral.innerText = registrosGeral.length;
        }

        function excluirItemGeral(index) {
            if (confirm("Deseja remover este registro da sessão atual?")) {
                registrosGeral.splice(index, 1);
                renderizarInterfaceGeral();
                exibirToast('Item removido da sessão atual!');
                inputChaveGeral.focus();
            }
        }

        function finalizarGeral() {
            if (registrosGeral.length === 0) return alert('Não há notas para finalizar.');
            if (!confirm('Deseja realmente finalizar esta bipagem e arquivar?')) return;

            const agora = new Date();
            const resumo = {};
            registrosGeral.forEach(i => resumo[i.transportadora] = (resumo[i.transportadora] || 0) + 1);

            historicoGeral.unshift({
                id: Date.now(),
                dataHora: agora.toLocaleDateString('pt-BR') + ' às ' + agora.toLocaleTimeString('pt-BR'),
                totalNotas: registrosGeral.length,
                resumo: resumo,
                itens: [...registrosGeral]
            });

            localStorage.setItem('relatorios_nfe_arquivados', JSON.stringify(historicoGeral));
            exibirToast('Relatório arquivado com sucesso!');
            tocarSomSucesso();

            registrosGeral = [];
            displayNumeroGeral.innerText = '---';
            displayTranspGeral.innerText = '';
            displayChaveGeral.innerText = 'Aguardando bipagem...';
            displayErroGeral.innerText = '';
            renderizarInterfaceGeral();
            renderizarArquivadosGeral();
            inputChaveGeral.focus();
        }

        function renderizarArquivadosGeral() {
            if (historicoGeral.length === 0) {
                arquivadosGeralContainer.innerHTML = '<em style="color: #6c757d;">Nenhum relatório finalizado até o momento.</em>';
                return;
            }
            arquivadosGeralContainer.innerHTML = '';
            historicoGeral.forEach((rel, idx) => {
                let resumoText = Object.entries(rel.resumo).map(([t, q]) => `${t}: ${q}`).join(' | ');
                arquivadosGeralContainer.innerHTML += `
                    <div class="historico-card">
                        <div>
                            <strong>📅 ${rel.dataHora}</strong> - Total: <b>${rel.totalNotas} notas</b><br>
                            <small style="color: #64748b;">🚚 ${resumoText}</small>
                        </div>
                        <button class="btn-excel" style="padding: 6px 10px; font-size: 12px; min-width: auto;" onclick="baixarCSVRelatorioGeral(${idx})">📥 Baixar CSV</button>
                    </div>`;
            });
        }

        function copiarGeral() {
            if (!registrosGeral.length) return alert('Nenhum registro para copiar!');
            let t = "Hora\tTransportadora\tSerie\tNumero da Nota\tChave de Acesso Completa\n";
            registrosGeral.forEach(i => t += `${i.hora}\t${i.transportadora}\t${i.serie}\t${i.numeroBruto}\t${i.chave}\n`);
            navigator.clipboard.writeText(t).then(() => { tocarSomSucesso(); exibirToast('Tabela copiada!'); });
        }

        function baixarCSVGeral() {
            if (!registrosGeral.length) return alert('Nenhum registro para baixar!');
            gerarCSV(registrosGeral, `relatorio_geral_parcial_${Date.now()}.csv`, ['Hora', 'Transportadora', 'Serie', 'Numero da Nota', 'Chave de Acesso Completa']);
        }

        function baixarCSVRelatorioGeral(idx) {
            const rel = historicoGeral[idx];
            gerarCSV(rel.itens, `relatorio_geral_final_${rel.dataHora.replace(/[/ :]/g, '_')}.csv`, ['Hora', 'Transportadora', 'Serie', 'Numero da Nota', 'Chave de Acesso Completa']);
        }

        function limparGeralAtual() {
            if (registrosGeral.length > 0 && !confirm("Limpar sessão sem arquivar?")) return;
            registrosGeral = [];
            displayNumeroGeral.innerText = '---';
            displayTranspGeral.innerText = '';
            displayChaveGeral.innerText = 'Aguardando bipagem...';
            displayErroGeral.innerText = '';
            renderizarInterfaceGeral();
            inputChaveGeral.focus();
        }

        /* --- ABA 2: CONFERÊNCIA SEDEX + RASTREIO --- */
        let registrosSedex = [];
        let historicoSedex = JSON.parse(localStorage.getItem('relatorios_nfe_sedex_arquivados') || '[]');
        let tempNotaSedex = null;

        const inputChaveSedex = document.getElementById('chaveSedexInput');
        const inputRastreioSedex = document.getElementById('rastreioSedexInput');
        const displayNumeroSedex = document.getElementById('ultimoNumeroSedex');
        const displayChaveSedex = document.getElementById('ultimaChaveSedex');
        const displayErroSedex = document.getElementById('erroSedex');
        const tabelaSedex = document.getElementById('tabelaSedex');
        const totalSedex = document.getElementById('totalSedex');
        const arquivadosSedexContainer = document.getElementById('arquivadosSedex');

        function focarAbaSedex() {
            if (inputChaveSedex.disabled) inputRastreioSedex.focus();
            else inputChaveSedex.focus();
        }

        inputChaveSedex.addEventListener('keydown', function(e) {
            if (e.key === 'Enter') { e.preventDefault(); processarChaveSedex(this.value.trim()); }
        });

        inputChaveSedex.addEventListener('input', function() {
            const limpo = this.value.replace(/\D/g, '');
            if (limpo.length === 44) processarChaveSedex(limpo);
        });

        function processarChaveSedex(valor) {
            displayErroSedex.innerText = '';
            if (/[a-zA-Z]/.test(valor) || valor.length !== 44 || !/^\d+$/.test(valor)) {
                tocarSomErro();
                displayErroSedex.innerText = '⛔ REJEITADO: A chave da NF-e deve ter 44 dígitos!';
                inputChaveSedex.value = '';
                return;
            }

            if (registrosSedex.some(i => i.chave === valor) && valor !== CHAVE_PERMITIDA_REPETICAO) {
                tocarSomErro();
                displayErroSedex.innerText = '⚠️ ATENÇÃO: Esta NF-e já foi vinculada nesta sessão!';
                inputChaveSedex.value = '';
                return;
            }

            const numBruto = valor.substring(25, 34);
            tempNotaSedex = {
                hora: new Date().toLocaleTimeString('pt-BR'),
                numeroBruto: numBruto,
                chave: valor
            };

            tocarSomSucesso();
            inputChaveSedex.disabled = true;
            inputRastreioSedex.disabled = false;
            inputRastreioSedex.placeholder = 'Bipe o Rastreio (AB, AP, AE, AD...BR)...';
            inputRastreioSedex.focus();
        }

        inputRastreioSedex.addEventListener('keydown', function(e) {
            if (e.key === 'Enter') { e.preventDefault(); processarRastreioSedex(this.value.trim()); }
        });

        function processarRastreioSedex(valor) {
            displayErroSedex.innerText = '';
            const rastreio = valor.toUpperCase();
            if (!rastreio) return;

            if (!tempNotaSedex) {
                tocarSomErro();
                displayErroSedex.innerText = '⚠️ Bipe a Nota Fiscal antes do Rastreio!';
                resetarCamposSedex();
                return;
            }

            const regraRastreioValido = /^(AB|AP|AE|AD)[A-Z0-9]+BR$/i.test(rastreio);
            if (!regraRastreioValido) {
                tocarSomErro();
                displayErroSedex.innerText = '⛔ REJEITADO: O rastreio SEDEX deve começar com "AB", "AP", "AE" ou "AD" e terminar com "BR"!';
                inputRastreioSedex.value = '';
                return;
            }

            if (registrosSedex.some(i => i.rastreio.toLowerCase() === rastreio.toLowerCase())) {
                tocarSomErro();
                displayErroSedex.innerText = `⚠️ ATENÇÃO: O Rastreio "${rastreio}" já foi utilizado nesta sessão!`;
                inputRastreioSedex.value = '';
                return;
            }

            registrosSedex.unshift({
                hora: tempNotaSedex.hora,
                numeroBruto: tempNotaSedex.numeroBruto,
                chave: tempNotaSedex.chave,
                rastreio: rastreio
            });

            tocarSomSucesso();
            displayNumeroSedex.innerText = `${tempNotaSedex.numeroBruto} | ${rastreio}`;
            displayChaveSedex.innerText = tempNotaSedex.chave;

            resetarCamposSedex();
            renderizarInterfaceSedex();
        }

        function resetarCamposSedex() {
            tempNotaSedex = null;
            inputChaveSedex.value = '';
            inputRastreioSedex.value = '';
            inputChaveSedex.disabled = false;
            inputRastreioSedex.disabled = true;
            inputRastreioSedex.placeholder = 'Aguardando 1º bipe...';
            inputChaveSedex.focus();
        }

        function renderizarInterfaceSedex() {
            tabelaSedex.innerHTML = '';
            registrosSedex.forEach((i, idx) => {
                tabelaSedex.innerHTML += `
                    <tr>
                        <td class="center">${i.hora}</td>
                        <td class="col-nota">${i.numeroBruto}</td>
                        <td class="col-chave">${i.chave}</td>
                        <td class="col-rastreio">${i.rastreio}</td>
                        <td style="text-align: center;">
                            <button class="btn-deletar-linha" onclick="excluirItemSedex(${idx})" title="Remover este registro">❌ Excluir</button>
                        </td>
                    </tr>`;
            });
            totalSedex.innerText = registrosSedex.length;
        }

        function excluirItemSedex(index) {
            if (confirm("Deseja remover este registro da sessão atual?")) {
                registrosSedex.splice(index, 1);
                renderizarInterfaceSedex();
                exibirToast('Item removido da sessão atual!');
                focarAbaSedex();
            }
        }

        function finalizarSedex() {
            if (!registrosSedex.length) return alert('Não há registros na sessão atual.');
            if (!confirm('Deseja realmente finalizar esta bipagem e arquivar?')) return;

            const agora = new Date();
            historicoSedex.unshift({
                id: Date.now(),
                dataHora: agora.toLocaleDateString('pt-BR') + ' às ' + agora.toLocaleTimeString('pt-BR'),
                totalNotas: registrosSedex.length,
                itens: [...registrosSedex]
            });

            localStorage.setItem('relatorios_nfe_sedex_arquivados', JSON.stringify(historicoSedex));
            exibirToast('Relatório SEDEX arquivado!');
            tocarSomSucesso();

            registrosSedex = [];
            displayNumeroSedex.innerText = '---';
            displayChaveSedex.innerText = 'Aguardando bipagem...';
            displayErroSedex.innerText = '';
            resetarCamposSedex();
            renderizarInterfaceSedex();
            renderizarArquivadosSedex();
        }

        function renderizarArquivadosSedex() {
            if (historicoSedex.length === 0) {
                arquivadosSedexContainer.innerHTML = '<em style="color: #6c757d;">Nenhum relatório finalizado até o momento.</em>';
                return;
            }
            arquivadosSedexContainer.innerHTML = '';
            historicoSedex.forEach((rel, idx) => {
                arquivadosSedexContainer.innerHTML += `
                    <div class="historico-card">
                        <div>
                            <strong>📅 ${rel.dataHora}</strong> - Total: <b>${rel.totalNotas} bipagens vinculadas</b>
                        </div>
                        <button class="btn-excel" style="padding: 6px 10px; font-size: 12px; min-width: auto;" onclick="baixarCSVRelatorioSedex(${idx})">📥 Baixar CSV</button>
                    </div>`;
            });
        }

        function copiarSedex() {
            if (!registrosSedex.length) return alert('Nenhum registro para copiar!');
            let t = "Hora\tNumero da Nota\tChave de Acesso Completa\tRastreio SEDEX\n";
            registrosSedex.forEach(i => t += `${i.hora}\t${i.numeroBruto}\t${i.chave}\t${i.rastreio}\n`);
            navigator.clipboard.writeText(t).then(() => { tocarSomSucesso(); exibirToast('Tabela copiada!'); });
        }

        function baixarCSVSedex() {
            if (!registrosSedex.length) return alert('Nenhum registro para baixar!');
            gerarCSV(registrosSedex, `relatorio_sedex_parcial_${Date.now()}.csv`, ['Hora', 'Numero da Nota', 'Chave de Acesso Completa', 'Rastreio SEDEX']);
        }

        function baixarCSVRelatorioSedex(idx) {
            const rel = historicoSedex[idx];
            gerarCSV(rel.itens, `relatorio_sedex_final_${rel.dataHora.replace(/[/ :]/g, '_')}.csv`, ['Hora', 'Numero da Nota', 'Chave de Acesso Completa', 'Rastreio SEDEX']);
        }

        function limparSedexAtual() {
            if (registrosSedex.length > 0 && !confirm("Limpar sessão sem arquivar?")) return;
            registrosSedex = [];
            displayNumeroSedex.innerText = '---';
            displayChaveSedex.innerText = 'Aguardando bipagem...';
            displayErroSedex.innerText = '';
            resetarCamposSedex();
            renderizarInterfaceSedex();
        }

        /* --- ABA 3: CONFERÊNCIA DE GELADO (NOVO SISTEMA DE LOTE QR CODE) --- */
        const dadosPadraoGelado = [
            { id: 1, data: "01/set", hora: "08:30", produto: "TIRZE. 93,6", lote: "2608170002" },
            { id: 2, data: "01/set", hora: "09:15", produto: "TIRZE. 60", lote: "2608204001" },
            { id: 3, data: "03/set", hora: "10:00", produto: "TIRZE. 93,6", lote: "2608240002" },
            { id: 4, data: "03/set", hora: "10:45", produto: "TIRZE. 60MG", lote: "2608204001" },
            { id: 5, data: "04/set", hora: "11:20", produto: "TIRZE. 60MG", lote: "2608240001" },
            { id: 6, data: "04/set", hora: "14:10", produto: "TIRZE 93,6MG", lote: "2608170002" },
            { id: 7, data: "08/set", hora: "15:05", produto: "TIRZE 60MG/2,4ML", lote: "2608240001" },
            { id: 8, data: "08/set", hora: "16:40", produto: "TIRZE 93,6MG/3,6ML", lote: "2608400002" }
        ];

        let listaLotes = [];
        let listaArquivados = [];

        const tbodyGelado = document.getElementById('tabelaCorpo');
        const formGelado = document.getElementById('loteForm');

        function carregarDadosGelado() {
            const salvos = localStorage.getItem('lotes_relatorio_db');
            const arquivados = localStorage.getItem('lotes_arquivados_db');

            if (salvos) {
                listaLotes = JSON.parse(salvos);
            } else {
                listaLotes = [...dadosPadraoGelado];
                salvarDadosGelado();
            }

            if (arquivados) listaArquivados = JSON.parse(arquivados);
            renderizarTabelaGelado();
        }

        function salvarDadosGelado() {
            localStorage.setItem('lotes_relatorio_db', JSON.stringify(listaLotes));
            localStorage.setItem('lotes_arquivados_db', JSON.stringify(listaArquivados));
            
            const statusEl = document.getElementById('saveStatus');
            if (statusEl) {
                statusEl.innerText = "✓ Alterações salvas!";
                setTimeout(() => { statusEl.innerText = "✓ Salvamento automático ativo"; }, 2000);
            }
        }

        function filtrarPorDiaFaturamento() {
            const termo = document.getElementById('inputPesquisaDia').value.toLowerCase().trim();
            if (!termo) {
                renderizarTabelaGelado(listaLotes);
            } else {
                const listaFiltrada = listaLotes.filter(item => item.data.toLowerCase().includes(termo));
                renderizarTabelaGelado(listaFiltrada);
            }
        }

        function renderizarTabelaGelado(dadosParaExibir = listaLotes) {
            tbodyGelado.innerHTML = '';
            
            if (dadosParaExibir.length === 0) {
                tbodyGelado.innerHTML = `<tr><td colspan="4" class="center" style="color: #64748b; padding: 20px;">Nenhum lote encontrado para a consulta informada.</td></tr>`;
                return;
            }

            dadosParaExibir.forEach((item) => {
                const indexReal = listaLotes.findIndex(i => i.id === item.id);
                const tr = document.createElement('tr');
                
                // Geração local de QR code (QRious)
                const qr = new QRious({ value: item.lote, size: 110 });
                const qrUrl = qr.toDataURL();

                tr.innerHTML = `
                    <td class="center">
                        <strong style="color: #0284c7; font-size: 14px;">📅 ${item.data}</strong>
                    </td>
                    <td class="produto">${item.produto}</td>
                    <td class="center">
                        <div class="qr-container">
                            <img src="${qrUrl}" alt="QR Code Lote ${item.lote}" width="110" height="110">
                            <div class="lote-number">${item.lote}</div>
                        </div>
                    </td>
                    <td class="center">
                        <button class="btn-deletar-linha" onclick="removerItemGelado(${indexReal})" title="Excluir item">✕ Remover</button>
                    </td>
                `;
                tbodyGelado.appendChild(tr);
            });
        }

        function removerItemGelado(index) {
            if (confirm("Deseja realmente remover este item do relatório?")) {
                listaLotes.splice(index, 1);
                salvarDadosGelado();
                filtrarPorDiaFaturamento();
                exibirToast("Item removido!");
            }
        }

        function resetarDadosPadrao() {
            if (confirm("Tem certeza que deseja restaurar a lista para os dados padrões de demonstração?")) {
                listaLotes = JSON.parse(JSON.stringify(dadosPadraoGelado));
                salvarDadosGelado();
                document.getElementById('inputPesquisaDia').value = '';
                renderizarTabelaGelado();
                exibirToast("Dados restaurados!");
            }
        }

        function arquivarLotesAtuais() {
            if (listaLotes.length === 0) return alert("A tabela está vazia! Não há dados para arquivar.");

            if (confirm("Deseja arquivar todos os lotes visíveis no momento? A tela será limpa para novas inclusões.")) {
                const dataHoje = new Date();
                const dataArquivamento = dataHoje.toLocaleDateString('pt-BR') + ' ' + dataHoje.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });

                const novosArquivados = listaLotes.map(item => ({
                    ...item,
                    dataArquivamento: dataArquivamento
                }));

                listaArquivados.push(...novosArquivados);
                listaLotes = [];

                salvarDadosGelado();
                document.getElementById('inputPesquisaDia').value = '';
                renderizarTabelaGelado();
                exibirToast("Lotes arquivados com sucesso!");
            }
        }

        function getSenhaAtual() {
            return localStorage.getItem('senha_exclusao_gelado') || hashSenha('');
        }

        function abrirModalConfiguracao() {
            document.getElementById('inputNovaSenha').value = '';
            document.getElementById('inputConfirmaSenha').value = '';
            document.getElementById('modalConfiguracao').style.display = 'flex';
        }

        function fecharModalConfiguracao() {
            document.getElementById('modalConfiguracao').style.display = 'none';
        }

        function salvarNovaSenha(e) {
            e.preventDefault();
            const nova = document.getElementById('inputNovaSenha').value.trim();
            const confirma = document.getElementById('inputConfirmaSenha').value.trim();

            if (!nova) return alert("Digite uma senha válida.");
            if (nova !== confirma) return alert("❌ As senhas digitadas não coincidem.");

            localStorage.setItem('senha_exclusao_gelado', hashSenha(nova));
            exibirToast("✅ Senha alterada com sucesso!");
            fecharModalConfiguracao();
        }

        function excluirItemHistorico(index) {
            const senha = prompt("🔑 Digite a senha para excluir do arquivo:");
            if (senha === null) return;

            if (hashSenha(senha.trim()) === getSenhaAtual()) {
                listaArquivados.splice(index, 1);
                salvarDadosGelado();
                abrirHistorico();
                exibirToast("Item excluído do arquivo!");
            } else {
                alert("❌ Senha incorreta!");
            }
        }

        function abrirHistorico() {
            const tbodyHist = document.getElementById('tabelaHistorico');
            tbodyHist.innerHTML = '';

            if (listaArquivados.length === 0) {
                tbodyHist.innerHTML = `<tr><td colspan="6" class="center" style="padding: 20px; color: #64748b;">Nenhum registro arquivado até o momento.</td></tr>`;
            } else {
                listaArquivados.forEach((item, index) => {
                    if (!item.seloAutenticidade) {
                        item.seloAutenticidade = 'STIN-DOC-' + (index + 1000);
                    }
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="center" style="font-weight: bold; color: #0284c7;">📅 ${item.data}</td>
                        <td class="center" style="font-size: 12px; color: #64748b;">${item.dataArquivamento}</td>
                        <td class="center" style="font-family: monospace; font-size: 11px; font-weight: bold; color: #0284c7;">${item.seloAutenticidade}</td>
                        <td class="produto">${item.produto}</td>
                        <td class="center"><strong>${item.lote}</strong></td>
                        <td class="center" style="display: flex; gap: 5px; justify-content: center;">
                            <button class="btn-action btn-print" style="padding: 4px 8px; font-size: 11px;" onclick="reimprimirItemHistorico(${index})">🖨️ Reimprimir</button>
                            <button class="btn-deletar-linha" onclick="excluirItemHistorico(${index})">🗑️ Excluir</button>
                        </td>
                    `;
                    tbodyHist.appendChild(tr);
                });
            }
            document.getElementById('modalHistorico').style.display = 'flex';
        }

        function fecharHistorico() {
            document.getElementById('modalHistorico').style.display = 'none';
        }

        function solicitarSenhaEImprimir() {
            if (listaLotes.length === 0) return alert("Não há lotes cadastrados para imprimir!");

            const senha = prompt("🔑 Digite a senha para autorizar a impressão:");
            if (senha === null) return;

            if (hashSenha(senha.trim()) === getSenhaAtual()) {
                abrirModalImpressao();
            } else {
                alert("❌ Senha incorreta! Impressão não autorizada.");
            }
        }

        function abrirModalImpressao() {
            const select = document.getElementById('selectDiaPrint');
            select.innerHTML = '';

            const diasUnicos = [...new Set(listaLotes.map(item => item.data))];
            diasUnicos.forEach(dia => {
                const total = listaLotes.filter(i => i.data === dia).length;
                const opt = document.createElement('option');
                opt.value = dia;
                opt.textContent = `Dia: ${dia} (${total} lote${total > 1 ? 's' : ''})`;
                select.appendChild(opt);
            });

            document.getElementById('modalImpressao').style.display = 'flex';
        }

        function fecharModalImpressao() {
            document.getElementById('modalImpressao').style.display = 'none';
        }

        function gerarSeloAutenticidade() {
            const timeHex = Date.now().toString(16).toUpperCase();
            const randHex = Math.floor(Math.random() * 65535).toString(16).toUpperCase().padStart(4, '0');
            return `STIN-DOC-${timeHex.slice(-6)}-${randHex}`;
        }

        function gerarEImprimir(modo) {
            document.body.classList.remove('printing-manifesto');
            const area = document.getElementById('areaImpressaoRelatorio');
            area.innerHTML = '';

            const empresa = localStorage.getItem('cfg_empresa_nome') || "STIN PHARMA";
            const logo = localStorage.getItem('cfg_logo_base64') || LOGO_PADRAO_BASE64;
            const rodapeTxt = localStorage.getItem('cfg_texto_rodape') || "Documento gerado e validado eletronicamente pelo sistema de logística.";

            const diaSel = document.getElementById('selectDiaPrint').value;
            let dias = (modo === 'selecionado') ? [diaSel] : [...new Set(listaLotes.map(item => item.data))];

            const agora = new Date();
            const dataHoraImpressao = agora.toLocaleDateString('pt-BR') + ' às ' + agora.toLocaleTimeString('pt-BR');

            dias.forEach(dia => {
                const itens = listaLotes.filter(item => item.data === dia);
                itens.forEach(item => {
                    if (!item.seloAutenticidade) item.seloAutenticidade = gerarSeloAutenticidade();
                    item.dataImpressao = dataHoraImpressao;
                });
                salvarDadosGelado();

                const page = document.createElement('div');
                page.className = 'print-page';
                const seloUnicoPagina = itens[0]?.seloAutenticidade || gerarSeloAutenticidade();

                let html = `
                    <div class="print-header">
                        <img src="${logo}" class="print-logo" alt="${empresa}">
                        <h2>${empresa.toUpperCase()} | CONFERÊNCIA DE PEDIDOS GELADO</h2>
                        <p style="margin-top: 5px; font-size: 10pt;">
                            <strong>Data do Relatório:</strong> ${dia} &nbsp;|&nbsp; 
                            <strong>Impresso em:</strong> ${dataHoraImpressao} &nbsp;|&nbsp; 
                            <strong>Total de Lotes:</strong> ${itens.length}
                        </p>
                    </div>
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 25%;">PREVISÃO FATURAMENTO</th>
                                <th style="width: 35%;">PRODUTO</th>
                                <th style="width: 25%;">LOTE (QR CODE)</th>
                                <th style="width: 15%;">SELO DOC.</th>
                            </tr>
                        </thead>
                        <tbody>
                `;

                itens.forEach(item => {
                    const qr = new QRious({ value: item.lote, size: 110 });
                    const qrUrl = qr.toDataURL();
                    html += `
                        <tr>
                            <td class="center"><strong>📅 ${item.data}</strong></td>
                            <td class="produto">${item.produto}</td>
                            <td class="center">
                                <div class="qr-container">
                                    <img src="${qrUrl}" alt="QR Code Lote ${item.lote}">
                                    <div class="lote-number">${item.lote}</div>
                                </div>
                            </td>
                            <td class="center" style="font-size: 8pt; font-family: monospace; font-weight: bold;">${item.seloAutenticidade}</td>
                        </tr>
                    `;
                });

                html += `
                        </tbody>
                    </table>
                    <div class="print-seal-box">
                        🛡️ <strong>SELO OFICIAL DE AUTENTICIDADE E INTEGRIDADE DO DOCUMENTO</strong><br>
                        Código de Validação: <span class="seal-code">${seloUnicoPagina}</span><br>
                        <small style="font-size: 8pt; color: #555;">${rodapeTxt} (Emissão: ${dataHoraImpressao})</small>
                    </div>
                `;
                page.innerHTML = html;
                area.appendChild(page);
            });

            fecharModalImpressao();
            setTimeout(() => { window.print(); }, 300);
        }

        function reimprimirItemHistorico(index) {
            const senha = prompt("🔑 Digite a senha para autorizar a REIMPRESSÃO:");
            if (senha === null) return;

            if (hashSenha(senha.trim()) === getSenhaAtual()) {
                document.body.classList.remove('printing-manifesto');
                const item = listaArquivados[index];
                const area = document.getElementById('areaImpressaoRelatorio');
                area.innerHTML = '';

                const empresa = localStorage.getItem('cfg_empresa_nome') || "STIN PHARMA";
                const logo = localStorage.getItem('cfg_logo_base64') || LOGO_PADRAO_BASE64;
                const rodapeTxt = localStorage.getItem('cfg_texto_rodape') || "Documento gerado e validado eletronicamente pelo sistema de logística.";

                const agora = new Date();
                const dataHoraReimpressao = agora.toLocaleDateString('pt-BR') + ' às ' + agora.toLocaleTimeString('pt-BR');

                if (!item.seloAutenticidade) {
                    item.seloAutenticidade = gerarSeloAutenticidade();
                    salvarDadosGelado();
                }

                const page = document.createElement('div');
                page.className = 'print-page';
                const qr = new QRious({ value: item.lote, size: 110 });
                const qrUrl = qr.toDataURL();

                page.innerHTML = `
                    <div class="print-header">
                        <img src="${logo}" class="print-logo" alt="${empresa}">
                        <h2>${empresa.toUpperCase()} | REIMPRESSÃO DE DOCUMENTO ARQUIVADO</h2>
                        <p style="margin-top: 5px; font-size: 10pt;">
                            <strong>Previsão de Faturamento:</strong> ${item.data} &nbsp;|&nbsp; 
                            <strong>Data de Arquivamento:</strong> ${item.dataArquivamento} &nbsp;|&nbsp; 
                            <strong>Reimpresso em:</strong> ${dataHoraReimpressao}
                        </p>
                    </div>
                    <table>
                        <thead>
                            <tr>
                                <th style="width: 25%;">PREVISÃO FATURAMENTO</th>
                                <th style="width: 45%;">PRODUTO</th>
                                <th style="width: 30%;">LOTE (QR CODE)</th>
                            </tr>
                        </thead>
                        <tbody>
                            <tr>
                                <td class="center"><strong>📅 ${item.data}</strong></td>
                                <td class="produto">${item.produto}</td>
                                <td class="center">
                                    <div class="qr-container">
                                        <img src="${qrUrl}" alt="QR Code Lote ${item.lote}">
                                        <div class="lote-number">${item.lote}</div>
                                    </div>
                                </td>
                            </tr>
                        </tbody>
                    </table>
                    <div class="print-seal-box">
                        🛡️ <strong>SELO DE AUTENTICIDADE (DOCUMENTO ARQUIVADO REIMPRESSO)</strong><br>
                        Código Registrado: <span class="seal-code">${item.seloAutenticidade}</span><br>
                        <small style="font-size: 8pt; color: #555;">${rodapeTxt} (Reimpressão autorizada em ${dataHoraReimpressao})</small>
                    </div>
                `;

                area.appendChild(page);
                fecharHistorico();
                setTimeout(() => { window.print(); }, 300);
            } else {
                alert("❌ Senha incorreta! Reimpressão não autorizada.");
            }
        }

        formGelado.addEventListener('submit', function(e) {
            e.preventDefault();

            const loteInput = document.getElementById('lote').value.trim();

            if (!/^\d{10}$/.test(loteInput)) {
                alert("⚠️ ATENÇÃO: O número do lote deve conter exatamente 10 algarismos numéricos!");
                return;
            }

            const agora = new Date();
            const horaFormatada = agora.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit' });

            const novoItem = {
                id: Date.now(),
                data: document.getElementById('data').value.trim(),
                hora: horaFormatada,
                produto: document.getElementById('produto').value.trim(),
                lote: loteInput
            };

            listaLotes.push(novoItem);
            salvarDadosGelado();
            filtrarPorDiaFaturamento();
            formGelado.reset();
            exibirToast("Lote adicionado com sucesso!");
        });

        /* --- MANIFESTO DE COLETA / CHECKLIST SEDEX --- */
        const DEFAULT_STIN_PHARMA_SVG = `
        <svg xmlns="http://www.w3.org/2000/svg" viewBox="0 0 220 85" class="w-full h-full">
            <g fill="none" stroke="#8c4e51" stroke-width="3" stroke-linecap="round" stroke-linejoin="round">
                <path d="M 28,10 Q 28,8 30,8 L 36,8 Q 38,8 38,10 L 38,16 L 33,26 L 33,38 L 46,48 Q 50,52 50,58 L 50,86 Q 50,92 44,92 L 22,92 Q 16,92 16,86 L 16,58 Q 16,52 20,48 L 27,38 L 27,26 L 22,16 Z" transform="scale(0.85)" />
                <path d="M 20,40 C 25,35 30,48 24,48" stroke="#8c4e51" stroke-width="2" transform="scale(0.85)" />
                <line x1="28" y1="58" x2="38" y2="58" stroke="#8c4e51" stroke-width="2.5" transform="scale(0.85)" />
                <line x1="28" y1="66" x2="38" y2="66" stroke="#8c4e51" stroke-width="2.5" transform="scale(0.85)" />
                <line x1="28" y1="74" x2="38" y2="74" stroke="#8c4e51" stroke-width="2.5" transform="scale(0.85)" />
            </g>
            <text x="56" y="38" font-family="sans-serif" font-size="30" font-weight="500" fill="#4a5568">Stin</text>
            <text x="56" y="68" font-family="sans-serif" font-size="30" font-weight="500" fill="#4a5568">Pharma</text>
        </svg>`;

        let currentLogoData = localStorage.getItem('stin_pharma_custom_logo') || DEFAULT_STIN_PHARMA_SVG;
        let rawData = [];
        let filteredData = [];

        function renderLogoManifesto() {
            const previewContainer = document.getElementById('logoPreviewContainer');
            const printContainer = document.getElementById('printLogoContainer');

            let contentHtml = '';
            if (currentLogoData.startsWith('data:image')) {
                contentHtml = `<img src="${currentLogoData}" class="max-h-full max-w-full object-contain mx-auto" alt="Logo">`;
            } else {
                contentHtml = currentLogoData;
            }

            if (previewContainer) previewContainer.innerHTML = contentHtml;
            if (printContainer) printContainer.innerHTML = contentHtml;
        }

        function handleLogoUpload(event) {
            const file = event.target.files[0];
            if (file) {
                const reader = new FileReader();
                reader.onload = function(e) {
                    currentLogoData = e.target.result;
                    localStorage.setItem('stin_pharma_custom_logo', currentLogoData);
                    renderLogoManifesto();
                };
                reader.readAsDataURL(file);
            }
        }

        function resetDefaultLogo() {
            currentLogoData = DEFAULT_STIN_PHARMA_SVG;
            localStorage.removeItem('stin_pharma_custom_logo');
            renderLogoManifesto();
        }

        function setupDragAndDrop() {
            const dropZone = document.getElementById('dropZone');
            if (!dropZone) return;

            ['dragenter', 'dragover', 'dragleave', 'drop'].forEach(evtName => {
                dropZone.addEventListener(evtName, e => { e.preventDefault(); e.stopPropagation(); }, false);
            });

            dropZone.addEventListener('drop', (e) => {
                const files = e.dataTransfer.files;
                if (files.length > 0) processFileManifesto(files[0]);
            });

            dropZone.addEventListener('click', () => {
                document.getElementById('csvFileInput').click();
            });
        }

        function handleFileSelect(e) {
            const file = e.target.files[0];
            if (file) processFileManifesto(file);
        }

        function processFileManifesto(file) {
            Papa.parse(file, {
                header: true,
                skipEmptyLines: true,
                complete: function(results) {
                    if (results.data && results.data.length > 0) {
                        normalizeAndSetDataManifesto(results.data, file.name);
                    } else alert('O ficheiro parece vazio ou inválido.');
                }
            });
        }

        function normalizeAndSetDataManifesto(data, fileName) {
            rawData = data.map((item, index) => {
                const keys = Object.keys(item);
                const findVal = (list) => {
                    for (let n of list) {
                        const k = keys.find(x => x.trim().toLowerCase() === n.toLowerCase());
                        if (k && item[k]) return String(item[k]).trim();
                    }
                    return '';
                };

                return {
                    id: index + 1,
                    hora: findVal(['Hora', 'Horário', 'Time']) || '--:--',
                    nota: findVal(['Numero da Nota', 'Número da Nota', 'Nota Fiscal', 'NF']) || `NF-${1000 + index}`,
                    chave: findVal(['Chave de Acesso Completa', 'Chave de Acesso', 'Chave NFe', 'Chave']) || 'Não informada',
                    rastreio: findVal(['Rastreio SEDEX', 'Rastreio', 'Codigo Rastreio', 'SEDEX']) || 'Sem Rastreio'
                };
            });

            filteredData = [...rawData];
            renderTableManifesto();
            
            document.getElementById('fileInfoBox').classList.remove('hidden');
            document.getElementById('fileNameText').innerText = `${fileName} (${rawData.length} itens)`;
        }

        function loadSampleData() {
            const sample = [
                { 'Hora': '08:30', 'Numero da Nota': '000.124.501', 'Chave de Acesso Completa': '35230912345678000195550010001245011234567890', 'Rastreio SEDEX': 'QC987654321BR' },
                { 'Hora': '08:42', 'Numero da Nota': '000.124.502', 'Chave de Acesso Completa': '35230912345678000195550010001245021234567891', 'Rastreio SEDEX': 'QC987654322BR' },
                { 'Hora': '09:15', 'Numero da Nota': '000.124.503', 'Chave de Acesso Completa': '35230912345678000195550010001245031234567892', 'Rastreio SEDEX': 'QC987654323BR' }
            ];
            normalizeAndSetDataManifesto(sample, 'Dados Exemplo SEDEX');
        }

        function renderTableManifesto() {
            const tbody = document.getElementById('tableBody');
            tbody.innerHTML = '';

            if (filteredData.length === 0) {
                tbody.innerHTML = `<tr id="emptyRow"><td colspan="6" class="center py-10 text-slate-400">Nenhum dado importado. Carregue um CSV ou selecione os dados de exemplo.</td></tr>`;
            } else {
                filteredData.forEach((row, idx) => {
                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="center font-mono text-slate-500">${idx + 1}</td>
                        <td class="center">${escapeHtml(row.hora)}</td>
                        <td class="col-nota">${escapeHtml(row.nota)}</td>
                        <td class="col-rastreio">${escapeHtml(row.rastreio)}</td>
                        <td class="col-chave text-[11px] text-center">${escapeHtml(row.chave)}</td>
                        <td class="center">
                            <button onclick="removeRowManifesto(${row.id})" class="btn-deletar-linha">✕</button>
                        </td>
                    `;
                    tbody.appendChild(tr);
                });
            }
            document.getElementById('recordCounter').innerText = `${filteredData.length} itens`;
            document.getElementById('totalVolumesCount').innerText = `${filteredData.length} volume(s)`;
        }

        function removeRowManifesto(id) {
            rawData = rawData.filter(r => r.id !== id);
            filterData();
        }

        function filterData() {
            const query = document.getElementById('searchInput').value.toLowerCase().trim();
            if (!query) filteredData = [...rawData];
            else {
                filteredData = rawData.filter(r => 
                    r.nota.toLowerCase().includes(query) ||
                    r.rastreio.toLowerCase().includes(query) ||
                    r.chave.toLowerCase().includes(query)
                );
            }
            renderTableManifesto();
        }

        function resetData() {
            if (confirm('Deseja realmente limpar todos os dados carregados?')) {
                rawData = []; filteredData = []; renderTableManifesto();
                document.getElementById('fileInfoBox').classList.add('hidden');
            }
        }

        function preparePrintableContent() {
            renderLogoManifesto();
            const empresa = document.getElementById('cfgEmpresa').value || 'Stin Pharma';
            const dataHoraRaw = document.getElementById('cfgDataHora').value;
            const dataHoraFormatted = dataHoraRaw ? new Date(dataHoraRaw).toLocaleString('pt-BR') : new Date().toLocaleString('pt-BR');
            const motorista = document.getElementById('cfgMotorista').value || 'N/A';
            const placa = document.getElementById('cfgPlaca').value || 'N/A';
            const obs = document.getElementById('cfgObs').value || 'Sem observações.';
            const showBarcode = document.getElementById('cfgShowBarcode').checked;
            const truncateKey = document.getElementById('cfgTruncateKey').checked;

            document.getElementById('printEmpresa').innerText = empresa;
            document.getElementById('printDataHora').innerText = dataHoraFormatted;
            document.getElementById('printMotorista').innerText = motorista;
            document.getElementById('printPlaca').innerText = placa;
            document.getElementById('printObs').innerText = obs;
            document.getElementById('printTotalCount').innerText = `${rawData.length} Pacote(s)`;
            document.getElementById('printTotalSummary').innerText = rawData.length;
            document.getElementById('printDocId').innerText = 'MAN-' + Math.floor(100000 + Math.random() * 900000);
            document.getElementById('printEmitDate').innerText = new Date().toLocaleDateString('pt-BR');

            const printTbody = document.getElementById('printTableBody');
            printTbody.innerHTML = '';

            if (rawData.length === 0) {
                printTbody.innerHTML = `<tr><td colspan="6" class="p-4 text-center">Nenhum pacote na lista de coleta.</td></tr>`;
                return;
            }

            rawData.forEach((item, index) => {
                const tr = document.createElement('tr');
                tr.className = 'border-b border-gray-300';

                let displayChave = item.chave;
                if (truncateKey && displayChave.length > 15) displayChave = '...' + displayChave.slice(-14);

                const barcodeId = `barcode-man-${index}`;

                tr.innerHTML = `
                    <td class="p-1.5 border-r border-black text-center font-mono">${index + 1}</td>
                    <td class="p-1.5 border-r border-black text-center">${escapeHtml(item.hora)}</td>
                    <td class="p-1.5 border-r border-black font-semibold text-center">${escapeHtml(item.nota)}</td>
                    <td class="p-1.5 border-r border-black font-mono text-center">
                        <div class="font-bold text-center">${escapeHtml(item.rastreio)}</div>
                        ${showBarcode ? `<svg id="${barcodeId}" class="max-h-8 mx-auto mt-0.5"></svg>` : ''}
                    </td>
                    <td class="p-1.5 border-r border-black font-mono text-[9px] text-center">${escapeHtml(displayChave)}</td>
                    <td class="p-1.5 text-center font-bold text-gray-400">[ &nbsp; ]</td>
                `;
                printTbody.appendChild(tr);

                if (showBarcode && item.rastreio && item.rastreio !== 'Sem Rastreio') {
                    setTimeout(() => {
                        try {
                            JsBarcode(`#${barcodeId}`, item.rastreio, { format: "CODE128", height: 25, displayValue: false, margin: 0 });
                        } catch (e) {}
                    }, 10);
                }
            });
        }

        function previewPDFModal() {
            preparePrintableContent();
            const printableContent = document.getElementById('printableArea').cloneNode(true);
            printableContent.classList.remove('print-only');
            
            const container = document.getElementById('modalPreviewContainer');
            container.innerHTML = '';
            container.appendChild(printableContent);

            document.getElementById('previewModal').style.display = 'flex';
        }

        function closePreviewModal() {
            document.getElementById('previewModal').style.display = 'none';
        }

        function printChecklist() {
            document.body.classList.add('printing-manifesto');
            preparePrintableContent();
            closePreviewModal();
            setTimeout(() => { 
                window.print();
                document.body.classList.remove('printing-manifesto');
            }, 200);
        }

        /* --- ABA 5: FUNÇÕES DO MÓDULO DE MANUTENÇÃO DO PROGRAMA --- */
        let listaManutencoes = JSON.parse(localStorage.getItem('sistema_manutencoes_db') || '[]');

        function calcularUsoStorage() {
            let totalBytes = 0;
            for (let key in localStorage) {
                if (localStorage.hasOwnProperty(key)) {
                    totalBytes += ((localStorage[key].length + key.length) * 2);
                }
            }
            return totalBytes;
        }

        function atualizarDiagnosticoStorage() {
            const bytes = calcularUsoStorage();
            const kb = (bytes / 1024).toFixed(2);
            const maxKb = 5120; // Capacidade estimada de 5MB
            const perc = Math.min(100, ((kb / maxKb) * 100)).toFixed(1);

            const usageEl = document.getElementById('maintStorageUsage');
            const barEl = document.getElementById('maintStorageBar');

            if (usageEl) usageEl.innerText = `${kb} KB / 5 MB (${perc}%)`;
            if (barEl) {
                barEl.style.width = `${perc}%`;
                if (perc > 85) barEl.className = 'bg-red-600 h-full transition-all duration-300';
                else if (perc > 60) barEl.className = 'bg-amber-500 h-full transition-all duration-300';
                else barEl.className = 'bg-blue-600 h-full transition-all duration-300';
            }

            const pendentes = listaManutencoes.filter(m => m.estado !== 'Concluído').length;
            const pendCountEl = document.getElementById('maintPendingCount');
            if (pendCountEl) pendCountEl.innerText = `${pendentes} Pendente(s)`;
        }

        function registarOcorrenciaManutencao(e) {
            e.preventDefault();
            const modulo = document.getElementById('maintModulo').value;
            const tipo = document.getElementById('maintTipo').value;
            const prioridade = document.getElementById('maintPrioridade').value;
            const descricao = document.getElementById('maintDescricao').value.trim();

            if (!descricao) return alert("Por favor, introduza a descrição da ocorrência.");

            const novaOcorrencia = {
                id: Date.now(),
                dataHora: new Date().toLocaleDateString('pt-PT') + ' ' + new Date().toLocaleTimeString('pt-PT', { hour: '2-digit', minute: '2-digit' }),
                modulo: modulo,
                tipo: tipo,
                prioridade: prioridade,
                descricao: descricao,
                estado: 'Pendente'
            };

            listaManutencoes.unshift(novaOcorrencia);
            salvarManutencoes();
            document.getElementById('formManutencao').reset();
            renderizarTabelaManutencao();
            tocarSomSucesso();
            exibirToast("Registo de manutenção adicionado!");
        }

        function salvarManutencoes() {
            localStorage.setItem('sistema_manutencoes_db', JSON.stringify(listaManutencoes));
            atualizarDiagnosticoStorage();
        }

        function renderizarTabelaManutencao() {
            const tbody = document.getElementById('tabelaManutencaoCorpo');
            const totalEl = document.getElementById('totalManutencoes');
            if (!tbody) return;

            tbody.innerHTML = '';
            if (listaManutencoes.length === 0) {
                tbody.innerHTML = `<tr><td colspan="7" class="center text-slate-400 py-6">Nenhum registo de manutenção ou avaria cadastrado.</td></tr>`;
            } else {
                listaManutencoes.forEach((item) => {
                    let badgePrio = 'bg-slate-100 text-slate-700';
                    if (item.prioridade === 'Crítica') badgePrio = 'bg-red-100 text-red-800 font-bold';
                    else if (item.prioridade === 'Alta') badgePrio = 'bg-amber-100 text-amber-800 font-bold';
                    else if (item.prioridade === 'Média') badgePrio = 'bg-blue-100 text-blue-800';

                    let badgeEstado = 'bg-amber-100 text-amber-800';
                    if (item.estado === 'Em Resolução') badgeEstado = 'bg-blue-100 text-blue-800';
                    else if (item.estado === 'Concluído') badgeEstado = 'bg-emerald-100 text-emerald-800 font-bold';

                    const tr = document.createElement('tr');
                    tr.innerHTML = `
                        <td class="center font-mono text-xs">${escapeHtml(item.dataHora)}</td>
                        <td class="font-semibold text-xs">${escapeHtml(item.modulo)}</td>
                        <td class="text-xs">${escapeHtml(item.tipo)}</td>
                        <td class="center"><span class="px-2 py-0.5 rounded text-[11px] ${badgePrio}">${escapeHtml(item.prioridade)}</span></td>
                        <td class="text-xs">${escapeHtml(item.descricao)}</td>
                        <td class="center">
                            <select onchange="alterarEstadoManutencao(${item.id}, this.value)" class="text-xs p-1 border rounded ${badgeEstado}">
                                <option value="Pendente" ${item.estado === 'Pendente' ? 'selected' : ''}>Pendente</option>
                                <option value="Em Resolução" ${item.estado === 'Em Resolução' ? 'selected' : ''}>Em Resolução</option>
                                <option value="Concluído" ${item.estado === 'Concluído' ? 'selected' : ''}>Concluído</option>
                            </select>
                        </td>
                        <td class="center">
                            <button onclick="eliminarManutencao(${item.id})" class="btn-deletar-linha" title="Eliminar">✕</button>
                        </td>
                    `;
                    tbody.appendChild(tr);
                });
            }

            if (totalEl) totalEl.innerText = listaManutencoes.length;
            atualizarDiagnosticoStorage();
        }

        function alterarEstadoManutencao(id, novoEstado) {
            const item = listaManutencoes.find(m => m.id === id);
            if (item) {
                item.estado = novoEstado;
                salvarManutencoes();
                renderizarTabelaManutencao();
                exibirToast("Estado da manutenção atualizado!");
            }
        }

        function eliminarManutencao(id) {
            if (confirm("Deseja eliminar este registo de manutenção?")) {
                listaManutencoes = listaManutencoes.filter(m => m.id !== id);
                salvarManutencoes();
                renderizarTabelaManutencao();
                exibirToast("Registo eliminado!");
            }
        }

        function limparHistoricoManutencoes() {
            if (confirm("Deseja remover todos os registos marcados como 'Concluído'?")) {
                listaManutencoes = listaManutencoes.filter(m => m.estado !== 'Concluído');
                salvarManutencoes();
                renderizarTabelaManutencao();
                exibirToast("Ocorrências concluídas removidas.");
            }
        }

        function exportarBackupGlobal() {
            const backupData = {
                dataExportacao: new Date().toISOString(),
                versaoSistema: "8.6",
                relatorios_nfe_arquivados: JSON.parse(localStorage.getItem('relatorios_nfe_arquivados') || '[]'),
                relatorios_nfe_sedex_arquivados: JSON.parse(localStorage.getItem('relatorios_nfe_sedex_arquivados') || '[]'),
                lotes_relatorio_db: JSON.parse(localStorage.getItem('lotes_relatorio_db') || '[]'),
                lotes_arquivados_db: JSON.parse(localStorage.getItem('lotes_arquivados_db') || '[]'),
                sistema_manutencoes_db: JSON.parse(localStorage.getItem('sistema_manutencoes_db') || '[]'),
                configuracoes: {
                    empresa: localStorage.getItem('cfg_empresa_nome'),
                    subtitulo: localStorage.getItem('cfg_subtitulo_pgn'),
                    rodape: localStorage.getItem('cfg_texto_rodape'),
                    logo: localStorage.getItem('cfg_logo_base64'),
                    colorBg: localStorage.getItem('cfg_color_bg'),
                    colorCard: localStorage.getItem('cfg_color_card'),
                    colorText: localStorage.getItem('cfg_color_text'),
                    colorPrimary: localStorage.getItem('cfg_color_primary')
                }
            };

            const jsonStr = JSON.stringify(backupData, null, 2);
            const blob = new Blob([jsonStr], { type: 'application/json' });
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = `backup_stin_pharma_${new Date().toISOString().slice(0,10)}.json`;
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
            exibirToast("Cópia de segurança descarregada com sucesso!");
        }

        function importarBackupGlobal(event) {
            const file = event.target.files[0];
            if (!file) return;

            const reader = new FileReader();
            reader.onload = function(e) {
                try {
                    const data = JSON.parse(e.target.result);
                    if (confirm("ATENÇÃO: A importação irá substituir os dados atuais pelos dados da cópia de segurança. Deseja continuar?")) {
                        if (data.relatorios_nfe_arquivados) localStorage.setItem('relatorios_nfe_arquivados', JSON.stringify(data.relatorios_nfe_arquivados));
                        if (data.relatorios_nfe_sedex_arquivados) localStorage.setItem('relatorios_nfe_sedex_arquivados', JSON.stringify(data.relatorios_nfe_sedex_arquivados));
                        if (data.lotes_relatorio_db) localStorage.setItem('lotes_relatorio_db', JSON.stringify(data.lotes_relatorio_db));
                        if (data.lotes_arquivados_db) localStorage.setItem('lotes_arquivados_db', JSON.stringify(data.lotes_arquivados_db));
                        if (data.sistema_manutencoes_db) localStorage.setItem('sistema_manutencoes_db', JSON.stringify(data.sistema_manutencoes_db));

                        if (data.configuracoes) {
                            if (data.configuracoes.empresa) localStorage.setItem('cfg_empresa_nome', data.configuracoes.empresa);
                            if (data.configuracoes.subtitulo) localStorage.setItem('cfg_subtitulo_pgn', data.configuracoes.subtitulo);
                            if (data.configuracoes.rodape) localStorage.setItem('cfg_texto_rodape', data.configuracoes.rodape);
                            if (data.configuracoes.logo) localStorage.setItem('cfg_logo_base64', data.configuracoes.logo);
                            if (data.configuracoes.colorBg) localStorage.setItem('cfg_color_bg', data.configuracoes.colorBg);
                            if (data.configuracoes.colorCard) localStorage.setItem('cfg_color_card', data.configuracoes.colorCard);
                            if (data.configuracoes.colorText) localStorage.setItem('cfg_color_text', data.configuracoes.colorText);
                            if (data.configuracoes.colorPrimary) localStorage.setItem('cfg_color_primary', data.configuracoes.colorPrimary);
                        }

                        alert("Cópia de segurança importada com sucesso! A página será recarregada.");
                        window.location.reload();
                    }
                } catch (err) {
                    alert("❌ Ficheiro de cópia de segurança inválido ou corrompido.");
                }
            };
            reader.readAsText(file);
        }

        function limparCacheSessao() {
            if (confirm("Deseja limpar os dados temporários de navegação da sessão? (Os registos arquivados não serão apagados)")) {
                sessionStorage.clear();
                exibirToast("Cache temporário limpo com sucesso!");
                atualizarDiagnosticoStorage();
            }
        }

        function resetTotalAplicacao() {
            const pass = prompt("🔑 ATENÇÃO EXTREMA: Esta ação apagará TODOS os dados, relatórios, configurações e registos de manutenção do sistema!\n\nDigite a palavra-passe de segurança para confirmar:");
            if (pass === null) return;

            if (hashSenha(pass.trim()) === getSenhaAtual() || (pass.trim() === '' && getSenhaAtual() === hashSenha(''))) {
                localStorage.clear();
                sessionStorage.clear();
                alert("O sistema foi restaurado para o estado de fábrica. A página será recarregada.");
                window.location.reload();
            } else {
                alert("❌ Palavra-passe incorreta! Ação cancelada.");
            }
        }

        /* --- FUNÇÕES AUXILIARES COMUNS --- */
        function gerarCodigoAutenticidade(prefixo) {
            const agora = new Date();
            const timeHex = agora.getTime().toString(16).toUpperCase();
            const randHex = Math.floor(Math.random() * 0xFFFFFF).toString(16).toUpperCase().padStart(6, '0');
            return `${prefixo}-${timeHex.slice(-6)}-${randHex}`;
        }

        function obterDataHoraAtualExtenso() {
            const agora = new Date();
            return agora.toLocaleDateString('pt-BR', { day: '2-digit', month: '2-digit', year: 'numeric' }) + ' às ' + agora.toLocaleTimeString('pt-BR', { hour: '2-digit', minute: '2-digit', second: '2-digit' });
        }

        function imprimirPDFGeral() {
            if (!registrosGeral.length) return alert('Nenhum registro na sessão atual para gerar o PDF!');
            document.body.classList.remove('printing-manifesto');
            
            const area = document.getElementById('areaImpressaoRelatorio');
            area.innerHTML = '';
            
            const dataHoraImp = obterDataHoraAtualExtenso();
            const codigoAutenticidade = gerarCodigoAutenticidade('STIN-NF');
            
            const page = document.createElement('div');
            page.className = 'print-page';
            
            const resumo = {};
            registrosGeral.forEach(i => resumo[i.transportadora] = (resumo[i.transportadora] || 0) + 1);
            let resumoText = Object.entries(resumo).map(([t, q]) => `<b>${t}:</b> ${q}`).join(' | ');

            let html = `
                <div class="print-header">
                    <img src="stin_pharma_excelncia_em_sade_logo-removebg-preview_2.png" alt="Stin Pharma" class="print-logo" onerror="this.style.display='none'">
                    <h2>STIN PHARMA | RELATÓRIO DE CONFERÊNCIA DE NOTAS FISCAIS (NF-e)</h2>
                    <p style="margin-top: 5px; font-size: 10pt;">
                        <strong>Data/Hora de Impressão:</strong> ${dataHoraImp} &nbsp;|&nbsp; 
                        <strong>Total de NF-e Bipadas:</strong> ${registrosGeral.length}
                    </p>
                    <p style="margin-top: 4px; font-size: 9.5pt; color: #333;">
                        <strong>Resumo por Transportadora:</strong> ${resumoText}
                    </p>
                </div>
                <table>
                    <thead>
                        <tr>
                            <th style="width: 12%;">HORA</th>
                            <th style="width: 25%;">TRANSPORTADORA</th>
                            <th style="width: 10%;">SÉRIE</th>
                            <th style="width: 18%;">NÚMERO NF-e</th>
                            <th style="width: 35%;">CHAVE DE ACESSO COMPLETA (44 DÍGITOS)</th>
                        </tr>
                    </thead>
                    <tbody>
            `;

            registrosGeral.forEach(i => {
                html += `
                    <tr>
                        <td class="center">${i.hora}</td>
                        <td><b>${i.transportadora}</b></td>
                        <td class="center">${i.serie}</td>
                        <td class="center" style="font-weight: bold; color: #198754;">${i.numeroBruto}</td>
                        <td style="font-family: monospace; font-size: 9pt;">${i.chave}</td>
                    </tr>
                `;
            });

            html += `
                    </tbody>
                </table>
                <div class="print-seal-box">
                    🛡️ <strong>DOCUMENTO OFICIAL DE CONFERÊNCIA LOGÍSTICA - STIN PHARMA</strong><br>
                    <strong>Selo de Autenticidade:</strong> <span class="seal-code">${codigoAutenticidade}</span> &nbsp;|&nbsp; 
                    <strong>Emissão:</strong> ${dataHoraImp}<br>
                    <small style="font-size: 8pt; color: #555;">Documento gerado eletronicamente com validação de integridade de dados.</small>
                </div>
            `;

            page.innerHTML = html;
            area.appendChild(page);
            
            setTimeout(() => { window.print(); }, 200);
        }

        function imprimirPDFSedex() {
            if (!registrosSedex.length) return alert('Nenhum registro na sessão atual para gerar o PDF!');
            document.body.classList.remove('printing-manifesto');
            
            const area = document.getElementById('areaImpressaoRelatorio');
            area.innerHTML = '';
            
            const dataHoraImp = obterDataHoraAtualExtenso();
            const codigoAutenticidade = gerarCodigoAutenticidade('STIN-SEDEX');
            
            const page = document.createElement('div');
            page.className = 'print-page';

            let html = `
                <div class="print-header">
                    <img src="stin_pharma_excelncia_em_sade_logo-removebg-preview_2.png" alt="Stin Pharma" class="print-logo" onerror="this.style.display='none'">
                    <h2>STIN PHARMA | RELATÓRIO DE VÍNCULO NF-e + SEDEX</h2>
                    <p style="margin-top: 5px; font-size: 10pt;">
                        <strong>Data/Hora de Impressão:</strong> ${dataHoraImp} &nbsp;|&nbsp; 
                        <strong>Total de Objetos Viculados:</strong> ${registrosSedex.length}
                    </p>
                </div>
                <table>
                    <thead>
                        <tr>
                            <th style="width: 12%;">HORA</th>
                            <th style="width: 20%;">NÚMERO NF-e</th>
                            <th style="width: 43%;">CHAVE DE ACESSO COMPLETA (44 DÍGITOS)</th>
                            <th style="width: 25%;">CÓDIGO RASTREIO SEDEX</th>
                        </tr>
                    </thead>
                    <tbody>
            `;

            registrosSedex.forEach(i => {
                html += `
                    <tr>
                        <td class="center">${i.hora}</td>
                        <td class="center" style="font-weight: bold; color: #198754;">${i.numeroBruto}</td>
                        <td style="font-family: monospace; font-size: 9pt;">${i.chave}</td>
                        <td class="center" style="font-family: monospace; font-weight: bold; color: #0d6efd;">${i.rastreio}</td>
                    </tr>
                `;
            });

            html += `
                    </tbody>
                </table>
                <div class="print-seal-box">
                    🛡 <strong>DOCUMENTO OFICIAL DE RASTREABILIDADE SEDEX - STIN PHARMA</strong><br>
                    <strong>Selo de Autenticidade:</strong> <span class="seal-code">${codigoAutenticidade}</span> &nbsp;|&nbsp; 
                    <strong>Emissão:</strong> ${dataHoraImp}<br>
                    <small style="font-size: 8pt; color: #555;">Documento gerado eletronicamente com validação de integridade de dados.</small>
                </div>
            `;

            page.innerHTML = html;
            area.appendChild(page);
            
            setTimeout(() => { window.print(); }, 200);
        }

        function gerarCSV(lista, nomeArquivo, cabecalhos) {
            let conteudo = cabecalhos.join(';') + '\\n';
            lista.forEach(i => {
                if (i.transportadora !== undefined) {
                    conteudo += `${i.hora};${i.transportadora};${i.serie};${i.numeroBruto};${i.chave}\\n`;
                } else {
                    conteudo += `${i.hora};${i.numeroBruto};${i.chave};${i.rastreio}\\n`;
                }
            });

            let blob = new Blob(["\\ufeff" + conteudo], { type: 'text/csv;charset=utf-8;' });
            let link = document.createElement("a");
            link.href = URL.createObjectURL(blob);
            link.setAttribute("download", nomeArquivo);
            document.body.appendChild(link);
            link.click();
            document.body.removeChild(link);
        }

        function escapeHtml(str) {
            if (!str) return '';
            return String(str)
                .replace(/&/g, '&amp;')
                .replace(/</g, '&lt;')
                .replace(/>/g, '&gt;')
                .replace(/"/g, '&quot;')
                .replace(/'/g, '&#039;');
        }

        function exibirToast(msg) {
            toast.innerText = msg;
            toast.style.display = 'block';
            setTimeout(() => { toast.style.display = 'none'; }, 2500);
        }

        // Inicialização global
        document.addEventListener('DOMContentLoaded', () => {
            verificarAutenticacao();
            carregarTema();
            carregarPersonalizacao();
            atualizarSomUI();
            renderizarArquivadosGeral();
            renderizarArquivadosSedex();
            carregarDadosGelado();
            renderLogoManifesto();
            setupDragAndDrop();
            atualizarDiagnosticoStorage();
            renderizarTabelaManutencao();
        });
    </script>
</body>
</html>
