<?php
session_start();

if (!empty($_GET['_db_fresh'])) { 
    $_SESSION = []; 
    session_regenerate_id(true); 
}

if (isset($_GET['logout'])) {
    session_destroy();
    $self = basename($_SERVER['PHP_SELF']);
    header("Location: $self?_db_fresh=1");
    exit;
}

$erro = '';
if (!empty($_SESSION['logado'])) {
    $perfil = $_SESSION['perfil'];
} else {
    $perfil = 'LOGIN'; 
}

if ($_SERVER['REQUEST_METHOD'] === 'POST' && isset($_POST['acao_login'])) {
    $usuario = trim($_POST['usuario_real'] ?? '');
    $senha   = trim($_POST['senha_real'] ?? '');
    
    if ($senha === '123456' && $usuario === 'Admin') {
        $_SESSION['logado'] = true;
        $_SESSION['usuario'] = $usuario;
        $_SESSION['perfil']  = $usuario;
        $perfil = $usuario;
    } else {
        $erro = 'Usuário ou senha inválidos. Verifique suas credenciais.';
        $perfil = 'LOGIN';
    }
}
?>
<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>NOIR ERP — Gestão de Moda Masculina</title>
<link rel="stylesheet" href="https://jsdelivr.net">
<style>
  *, *::before, *::after { box-sizing: border-box; margin: 0; padding: 0; }
  body { font-family: 'Segoe UI', system-ui, sans-serif; height: 100vh; background-color: #121212; color: #F2F2F2; display: flex; overflow: hidden; }
  .aba-conteudo { display: none; }
  .aba-conteudo.ativa { display: block; }
  
  .sidebar { width: 260px; background-color: #1A1A1A; border-right: 1px solid #2C2C2C; color: white; display: flex; flex-direction: column; justify-content: space-between; padding: 25px 20px; flex-shrink: 0; }
  .main-container { flex: 1; display: flex; flex-direction: column; min-width: 0; height: 100%; overflow: hidden; background-color: #121212; }
  .topbar { height: 64px; background-color: #1A1A1A; border-bottom: 1px solid #2C2C2C; display: flex; align-items: center; justify-content: space-between; padding: 0 30px; flex-shrink: 0; }
  .content-area { flex: 1; padding: 30px; overflow-y: auto; }
  
  .btn-menu { width: 100%; text-align: left; padding: 12px 15px; border: none; background: transparent; color: #8A8A8A; border-radius: 8px; font-size: 14px; font-weight: 500; transition: all 0.2s; display: flex; align-items: center; gap: 10px; margin-bottom: 6px; cursor: pointer; }
  .btn-menu:hover, .btn-menu.ativo { background-color: #2C2C2C; color: #D4AF37; }
  .kpi-card { background: #1E1E1E; padding: 20px; border-radius: 12px; border: 1px solid #2C2C2C; }
  .kpi-title { font-size: 11px; font-weight: 600; color: #8A8A8A; text-transform: uppercase; margin-bottom: 5px; letter-spacing: 0.5px; }
  .kpi-value { font-size: 24px; font-weight: 700; color: #F2F2F2; margin: 0; }
  .text-gold { color: #D4AF37; }
  .text-danger-custom { color: #E5484D; }
  
  .login-wrapper { width: 100vw; height: 100vh; display: flex; overflow: hidden; position: fixed; top: 0; left: 0; z-index: 9999; }
  .login-left { flex: 1; background-color: #1A1A1A; border-right: 1px solid #2C2C2C; display: flex; flex-direction: column; align-items: center; justify-content: center; padding: 40px; color: white; text-align: center; }
  .login-right { flex: 1; background-color: #121212; display: flex; align-items: center; justify-content: center; padding: 40px; }
  .form-group { margin-bottom: 15px; }
  .form-group label { display: block; font-size: 11px; font-weight: 700; color: #8A8A8A; text-transform: uppercase; letter-spacing: 0.5px; }
  .form-control-dark { width: 100%; padding: 12px; border: 1px solid #2C2C2C; border-radius: 8px; background: #1E1E1E; font-size: 14px; color: #F2F2F2; outline: none; transition: border-color 0.2s; }
  .form-control-dark:focus { border-color: #D4AF37; }
  .btn-gold { width: 100%; padding: 12px; background-color: #D4AF37; color: #121212; border: none; border-radius: 8px; font-weight: 700; font-size: 15px; cursor: pointer; transition: opacity 0.2s; }
  .btn-gold:hover { opacity: 0.9; }
</style>
</head>
<body>

<?php if ($perfil === 'LOGIN'): ?>
  <!-- Tela de Login -->
  <div class="login-wrapper">
    <div class="login-left">
      <div style="font-size: 3.5rem; color: #D4AF37; margin-bottom: 15px;">⚜️</div>
      <h1 style="font-size: 2.2rem; font-weight: 800; text-transform: uppercase; letter-spacing: 2px; color: #D4AF37;">NOIR</h1>
      <p style="font-size: 0.9rem; color: #8A8A8A; max-width: 280px; letter-spacing: 0.5px; line-height: 1.6;">MENSWEAR SYSTEM ERP & PDV</p>
    </div>
    <div class="login-right">
      <div style="width: 100%; max-width: 340px;">
        <h2 style="font-size: 1.5rem; font-weight: 700; color: #F2F2F2; margin-bottom: 5px;">Acesso Restrito</h2>
        <p style="font-size: 0.85rem; color: #8A8A8A; margin-bottom: 30px;">Digite suas credenciais administrativas</p>
        
        <?php if ($erro): ?>
          <div style="background-color: rgba(229,72,77,0.1); border: 1px solid #E5484D; color: #E5484D; padding: 12px; border-radius: 8px; font-size: 14px; margin-bottom: 15px;">⚠️ <?= htmlspecialchars($erro) ?></div>
        <?php endif; ?>
        
        <form method="POST" action="" autocomplete="off">
          <input type="hidden" name="acao_login" value="1">
          <div class="form-group">
            <label>Usuário</label>
            <input type="text" name="usuario_real" placeholder="Admin" required class="form-control-dark">
          </div>
          <div class="form-group">
            <label>Senha</label>
            <input type="password" name="senha_real" placeholder="••••••" required class="form-control-dark">
          </div>
          <button type="submit" class="btn-gold">Autenticar Entrada →</button>
        </form>
        <div style="text-align: center; font-size: 10px; color: #444; margin-top: 50px;">NOIR Smart Suite • Desenvolvido por Vinicius</div>
      </div>
    </div>
  </div>

<?php else: ?>
  <!-- Menu Lateral -->
  <aside class="sidebar">
    <div>
      <div style="display: flex; align-items: center; gap: 12px; margin-bottom: 35px; padding-left: 5px;">
        <span style="font-size: 20px; color: #D4AF37;">⚜️</span>
        <div>
          <h1 style="font-size: 15px; font-weight: 700; text-transform: uppercase; letter-spacing: 2px; color: #D4AF37; margin: 0;">NOIR</h1>
          <p style="font-size: 9px; color: #8A8A8A; text-transform: uppercase; letter-spacing: 1px; margin: 0;">MENSWEAR ERP</p>
        </div>
      </div>

      <nav>
        <button onclick="trocarJanela('janela-dashboard')" class="btn-menu ativo">📊 Dashboard Geral</button>
        <button onclick="trocarJanela('janela-estoque')" class="btn-menu">📦 Controle de Estoque</button>
        <button onclick="trocarJanela('janela-financeiro')" class="btn-menu">💼 Fluxo Financeiro</button>
        <button onclick="trocarJanela('janela-pdv')" class="btn-menu">🛒 Frente de Caixa (PDV)</button>
      </nav>
    </div>

    <div style="border-top: 1px solid #2C2C2C; padding-top: 15px;">
      <a href="?logout=1" style="display: flex; align-items: center; gap: 10px; text-decoration: none; color: #E5484D; font-weight: 600; font-size: 13px; padding: 8px 12px; text-transform: uppercase; letter-spacing: 0.5px;">
        🚪 Encerrar Sessão
      </a>
    </div>
  </aside>

  <!-- Corpo Principal -->
  <div class="main-container">
    <header class="topbar">
      <span class="badge rounded-pill border px-3 py-1.5 font-weight-bold text-uppercase" style="font-size: 10px; background-color: #2C2C2C; border-color: #444; color: #D4AF37;">
        Sessão: Gerente Administrativo
      </span>
      <div style="display: flex; align-items: center; gap: 15px;">
        <div style="text-align: right;">
          <p style="font-size: 14px; font-weight: 600; color: #F2F2F2; margin: 0;">Vinicius Santos</p>
          <p style="font-size: 10px; color: #8A8A8A; margin: 0;">Ambiente Comercial PHP</p>
        </div>
        <div style="width: 36px; height: 36px; background-color: #D4AF37; color: #121212; border-radius: 50%; display: flex; align-items: center; justify-content: center; font-weight: 700; font-size: 14px;">V</div>
      </div>
    </header>

    <main class="content-area">
      <!-- 1. DASHBOARD -->
      <div id="janela-dashboard" class="aba-conteudo ativa">
        <h2 style="font-size: 1.3rem; font-weight: 700; color: #F2F2F2; margin-bottom: 20px; text-transform: uppercase; letter-spacing: 1px;">Visão Geral do Negócio</h2>
        <div style="display: grid; grid-template-columns: repeat(auto-fit, minmax(220px, 1fr)); gap: 20px;">
          <div class="kpi-card"><p class="kpi-title">Faturamento Hoje</p><p class="kpi-value text-gold">R$ 3.420,00</p></div>
          <div class="kpi-card"><p class="kpi-title">Produtos Cadastrados</p><p class="kpi-value">1.240 un</p></div>
          <div class="kpi-card"><p class="kpi-title">Alertas Críticos</p><p class="kpi-value text-danger-custom">2 Itens</p></div>
        </div>
      </div>
      
      <!-- 2. ESTOQUE -->
      <div id="janela-estoque" class="aba-conteudo">
        <h2 style="font-size: 1.3rem; font-weight: 700; color: #F2F2F2; margin-bottom: 20px; text-transform: uppercase; letter-spacing: 1px;">Controle de Grade e Estoque</h2>
        <div class="kpi-card" style="padding: 20px;">
          <div style="border-bottom: 1px solid #2C2C2C; padding-bottom: 12px; margin-bottom: 12px;">
            <p style="margin: 0; font-weight: 600; font-size: 14px; color: #F2F2F2;">Camiseta Pima Egípcio — SKU: CS-001</p>
            <span style="color: #8A8A8A; font-size: 12px;">Grade: Tamanho M / Cor: Preto | <span class="text-gold">Estoque: 15 un</span></span>
          </div>
          <div style="padding-bottom: 4px;">
            <p style="margin: 0; font-weight: 600; font-size: 14px; color: #F2F2F2;">Calça Alfaiataria Slim — SKU: CJ-002</p>
Grade: Tamanho 42 / Cor: Cinza | Estoque Crítico: 4 unFluxo de Caixa OperacionalSaldo Atual em CaixaConciliação bancária diária atualizada via back-endR$ 18.450,00Frente de Caixa / CheckoutFinalizar Vendafunction trocarJanela(idJanela) {const abas = document.querySelectorAll('.aba-conteudo');abas.forEach(aba => aba.classList.remove('ativa'));const abaAlvo = document.getElementById(idJanela);if(abaAlvo) abaAlvo.classList.add('ativa');const botoes = document.querySelectorAll('.btn-menu');botoes.forEach(btn => btn.classList.remove('ativo'));if(window.event && window.event.currentTarget) {window.event.currentTarget.classList.add('ativo');}}
