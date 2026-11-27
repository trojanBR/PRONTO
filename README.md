```html
<!DOCTYPE html>
<html lang="pt-BR" class="dark scroll-smooth">
<head>
  <meta charset="UTF-8" />
  <meta name="viewport" content="width=device-width, initial-scale=1.0" />
  <title>PRONTO — Diagnóstico, Manutenção e Pós-Formatação Portátil</title>
  <meta name="description" content="Utilitário portátil em C# / WPF (.NET 8.0) para técnicos de informática. Raio-X de hardware, deploy via Winget e Office C2R, reparos DISM/SFC e NetDoctor em um único executável." />

  <!-- Tipografia Inter (pesos 200 a 600 para estética minimalista) & JetBrains Mono para telemetria -->
  <link rel="preconnect" href="https://fonts.googleapis.com" />
  <link rel="preconnect" href="https://fonts.gstatic.com" crossorigin />
  <link href="https://fonts.googleapis.com/css2?family=Inter:wght@200;300;400;500;600&family=JetBrains+Mono:wght@400;500&display=swap" rel="stylesheet" />

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>
  <script>
    tailwind.config = {
      darkMode: 'class',
      theme: {
        extend: {
          fontFamily: {
            sans: ['Inter', 'system-ui', '-apple-system', 'sans-serif'],
            mono: ['JetBrains Mono', 'monospace'],
          },
          colors: {
            graphite: {
              950: '#181B23',
              900: '#1E212B',
              800: '#242833',
              700: '#2D3240',
              600: '#383E4F',
            },
            pronto: {
              red: '#E53935',
              redHover: '#F44336',
              purple: '#A855F7',
              emerald: '#10B981',
            }
          }
        }
      }
    };
  </script>

  <style>
    /* Base e fundo grafite com profundidade sutil */
    body {
      background-color: #242833;
      color: #F3F4F6;
      font-family: 'Inter', sans-serif;
      overflow-x: hidden;
      background-image:
        radial-gradient(circle at 15% 12%, rgba(229, 57, 53, 0.08) 0%, transparent 42%),
        radial-gradient(circle at 85% 22%, rgba(168, 85, 247, 0.08) 0%, transparent 45%),
        radial-gradient(circle at 50% 85%, rgba(16, 185, 129, 0.05) 0%, transparent 50%);
      background-attachment: fixed;
    }

    /* Painéis translúcidos com desfoque (Glass / WindowChrome) */
    .glass-panel {
      background: rgba(30, 33, 43, 0.72);
      backdrop-filter: blur(12px);
      -webkit-backdrop-filter: blur(12px);
      border: 1px solid rgba(255, 255, 255, 0.08);
    }

    .glass-card {
      background: rgba(45, 50, 64, 0.42);
      backdrop-filter: blur(12px);
      -webkit-backdrop-filter: blur(12px);
      border: 1px solid rgba(255, 255, 255, 0.07);
      transition: transform 0.25s cubic-bezier(0.22, 1, 0.36, 1), border-color 0.25s ease, background-color 0.25s ease;
    }

    .glass-card:hover {
      border-color: rgba(255, 255, 255, 0.16);
      background: rgba(45, 50, 64, 0.62);
    }

    /* Logotipo minimalista PRONTO com o primeiro "O" como círculo vermelho vibrante */
    .pronto-logo {
      display: inline-flex;
      align-items: center;
      font-weight: 200;
      letter-spacing: 0.28em;
      text-transform: uppercase;
      color: #F9FAFB;
      user-select: none;
      line-height: 1;
    }

    .pronto-logo-dot {
      display: inline-block;
      width: 0.72em;
      height: 0.72em;
      border-radius: 9999px;
      background-color: #E53935;
      margin-right: 0.28em;
      box-shadow: 0 0 14px rgba(229, 57, 53, 0.65);
      transform: translateY(-0.03em);
      flex-shrink: 0;
    }

    /* Animação circular estilo Eclipse ao redor dos ícones de deploy */
    @keyframes eclipseSpin {
      0% {
        transform: rotate(0deg);
      }
      100% {
        transform: rotate(360deg);
      }
    }

    .eclipse-orbit {
      animation: eclipseSpin 1.15s linear infinite;
      transform-origin: 50% 50%;
    }

    /* Pulso verde esmeralda na conclusão de instalação/tarefa */
    @keyframes emeraldPulseRing {
      0% {
        box-shadow: 0 0 0 0 rgba(16, 185, 129, 0.55);
      }
      70% {
        box-shadow: 0 0 0 9px rgba(16, 185, 129, 0);
      }
      100% {
        box-shadow: 0 0 0 0 rgba(16, 185, 129, 0);
      }
    }

    .emerald-pulse {
      animation: emeraldPulseRing 2s infinite;
    }

    /* Animação do Radar LAN */
    @keyframes radarSweep {
      0% {
        transform: rotate(0deg);
      }
      100% {
        transform: rotate(360deg);
      }
    }

    .radar-beam {
      transform-origin: 50% 50%;
      animation: radarSweep 3.2s linear infinite;
    }

    /* Transição suave entre abas do simulador */
    .tab-pane {
      animation: fadeSlideIn 0.25s cubic-bezier(0.22, 1, 0.36, 1);
    }

    @keyframes fadeSlideIn {
      from {
        opacity: 0;
        transform: translateY(6px);
      }
      to {
        opacity: 1;
        transform: translateY(0);
      }
    }

    /* Barra de rolagem discreta */
    ::-webkit-scrollbar {
      width: 6px;
      height: 6px;
    }
    ::-webkit-scrollbar-track {
      background: #1E212B;
    }
    ::-webkit-scrollbar-thumb {
      background: #383E4F;
      border-radius: 9999px;
    }
    ::-webkit-scrollbar-thumb:hover {
      background: #4B5369;
    }
  </style>
</head>

<body class="antialiased selection:bg-[#E53935] selection:text-white">

  <!-- ==================== 1. HEADER MINIMALISTA FIXO ==================== -->
  <header class="fixed top-0 inset-x-0 z-50 glass-panel border-b border-white/[0.06]">
    <div class="max-w-6xl mx-auto px-4 sm:px-6 h-16 flex items-center justify-between">
      <!-- Logo PRONTO -->
      <a href="#" class="pronto-logo text-lg sm:text-xl focus:outline-none" aria-label="PRONTO Início">
        PR<span class="pronto-logo-dot" aria-hidden="true"></span>NTO
      </a>

      <!-- Navegação Desktop -->
      <nav class="hidden md:flex items-center gap-8 text-sm font-light text-gray-300">
        <a href="#layout-interativo" class="hover:text-white transition-colors">Layout Interativo</a>
        <a href="#diferenciais" class="hover:text-white transition-colors">Diferenciais</a>
        <a href="#modulos" class="hover:text-white transition-colors">Módulos</a>
        <a href="#como-usar" class="hover:text-white transition-colors">Como Usar</a>
      </nav>

      <!-- CTA Direto + Botão Menu Mobile -->
      <div class="flex items-center gap-3">
        <a
          href="https://github.com/trojanBR/PRONTO/releases/latest"
          target="_blank"
          rel="noopener noreferrer"
          class="inline-flex items-center gap-2 px-4 py-2 rounded-lg bg-[#E53935] hover:bg-[#F44336] text-white text-xs sm:text-sm font-medium tracking-wide transition-all shadow-lg shadow-[#E53935]/20 active:scale-[0.98]"
        >
          <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" d="M4 16v2a2 2 0 002 2h12a2 2 0 002-2v-2M7 10l5 5m0 0l5-5m-5 5V3" />
          </svg>
          <span>Download</span>
        </a>

        <button
          id="mobile-menu-btn"
          type="button"
          class="md:hidden p-2 rounded-lg text-gray-300 hover:text-white hover:bg-white/5 transition-colors"
          aria-label="Abrir menu de navegação"
        >
          <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
            <path stroke-linecap="round" stroke-linejoin="round" d="M4 6h16M4 12h16M4 18h16" />
          </svg>
        </button>
      </div>
    </div>

    <!-- Menu Mobile Expansível -->
    <div id="mobile-menu" class="hidden md:hidden border-t border-white/[0.06] bg-[#1E212B]/95 backdrop-blur-xl px-4 py-4 space-y-3">
      <a href="#layout-interativo" class="block py-2 text-sm font-light text-gray-200 hover:text-white mobile-nav-link">Layout Interativo</a>
      <a href="#diferenciais" class="block py-2 text-sm font-light text-gray-200 hover:text-white mobile-nav-link">Diferenciais</a>
      <a href="#modulos" class="block py-2 text-sm font-light text-gray-200 hover:text-white mobile-nav-link">Módulos</a>
      <a href="#como-usar" class="block py-2 text-sm font-light text-gray-200 hover:text-white mobile-nav-link">Como Usar</a>
    </div>
  </header>

  <!-- ==================== 2. HERO SECTION + SIMULADOR INTERATIVO ==================== -->
  <main class="pt-28 sm:pt-32 pb-20">
    <section class="max-w-6xl mx-auto px-4 sm:px-6">
      <!-- Badge Superior -->
      <div class="flex justify-center mb-6">
        <div class="inline-flex items-center gap-2.5 px-3.5 py-1.5 rounded-full glass-card text-xs font-light text-gray-300">
          <span class="w-2 h-2 rounded-full bg-[#10B981] animate-pulse"></span>
          <span>Windows 10 / 11 • C# WPF (.NET 8.0) • 100% Portátil</span>
        </div>
      </div>

      <!-- Título & Subtítulo Hero -->
      <div class="text-center max-w-3xl mx-auto">
        <h1 class="text-3xl sm:text-5xl lg:text-[3.25rem] font-extralight tracking-tight text-white leading-[1.16]">
          Diagnóstico, manutenção e pós-formatação em um <span class="font-normal text-white">único executável.</span>
        </h1>
        <p class="mt-5 text-base sm:text-lg font-light text-gray-300/90 leading-relaxed">
          Desenvolvido em <span class="text-white font-normal">C# / WPF (.NET 8.0)</span> para bancada técnica e atendimento em campo. Zero instalação — roda direto do seu pendrive sem deixar rastros e mantém-se atualizado automaticamente via <span class="text-white font-normal">GitHub Releases</span>.
        </p>

        <!-- Botões de Ação Principais -->
        <div class="mt-8 flex flex-col sm:flex-row items-center justify-center gap-3.5">
          <a
            href="https://github.com/trojanBR/PRONTO/releases/latest"
            target="_blank"
            rel="noopener noreferrer"
            class="w-full sm:w-auto inline-flex items-center justify-center gap-2.5 px-6 py-3.5 rounded-xl bg-[#E53935] hover:bg-[#F44336] text-white text-sm font-medium transition-all shadow-xl shadow-[#E53935]/25 hover:-translate-y-0.5"
          >
            <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M4 16v2a2 2 0 002 2h12a2 2 0 002-2v-2M7 10l5 5m0 0l5-5m-5 5V3" />
            </svg>
            <span>Baixar Última Versão (.exe)</span>
          </a>

          <a
            href="https://github.com/trojanBR/PRONTO"
            target="_blank"
            rel="noopener noreferrer"
            class="w-full sm:w-auto inline-flex items-center justify-center gap-2.5 px-6 py-3.5 rounded-xl glass-card text-gray-200 hover:text-white text-sm font-normal transition-all hover:-translate-y-0.5"
          >
            <svg class="w-4 h-4" fill="currentColor" viewBox="0 0 24 24">
              <path fill-rule="evenodd" clip-rule="evenodd" d="M12 2C6.477 2 2 6.484 2 12.017c0 4.425 2.865 8.18 6.839 9.504.5.092.682-.217.682-.483 0-.237-.008-.868-.013-1.703-2.782.605-3.369-1.343-3.369-1.343-.454-1.158-1.11-1.466-1.11-1.466-.908-.62.069-.608.069-.608 1.003.07 1.53 1.032 1.53 1.032.892 1.53 2.341 1.088 2.91.832.092-.647.35-1.088.636-1.338-2.22-.253-4.555-1.113-4.555-4.951 0-1.093.39-1.988 1.029-2.688-.103-.253-.446-1.272.098-2.65 0 0 .84-.27 2.75 1.026A9.564 9.564 0 0112 6.844c.85.004 1.705.115 2.504.337 1.909-1.296 2.747-1.027 2.747-1.027.546 1.379.202 2.398.1 2.651.64.7 1.028 1.595 1.028 2.688 0 3.848-2.339 4.695-4.566 4.943.359.309.678.92.678 1.855 0 1.338-.012 2.419-.012 2.747 0 .268.18.58.688.482A10.019 10.019 0 0022 12.017C22 6.484 17.522 2 12 2z" />
            </svg>
            <span>Código no GitHub</span>
          </a>
        </div>
      </div>

      <!-- ==================== MOCKUP INTERATIVO DO APP (WINDOWCHROME) ==================== -->
      <div id="layout-interativo" class="mt-14 sm:mt-16 scroll-mt-24">
        <div class="flex items-center justify-between mb-3 px-1">
          <span class="text-xs font-mono uppercase tracking-widest text-gray-400 flex items-center gap-2">
            <span class="w-1.5 h-1.5 rounded-full bg-[#E53935]"></span>
            Simulador Interativo da Interface — Clique nas abas e teste
          </span>
          <span class="hidden sm:inline text-xs font-light text-gray-400">
            Modo Administrador • Sem Bordas (WindowChrome)
          </span>
        </div>

        <!-- Janela Principal do PRONTO (#242833) -->
        <div class="rounded-2xl bg-[#242833] border border-white/10 shadow-[0_25px_70px_-15px_rgba(0,0,0,0.65)] overflow-hidden">
          
          <!-- Barra de Título Personalizada (WindowChrome) -->
          <div class="h-12 px-4 sm:px-5 bg-[#1B1E26]/90 border-b border-white/[0.06] flex items-center justify-between select-none">
            <!-- Esquerda: Logo + Badge de Versão -->
            <div class="flex items-center gap-3.5">
              <div class="pronto-logo text-sm sm:text-base">
                PR<span class="pronto-logo-dot"></span>NTO
              </div>
              <span class="hidden xs:inline-flex sm:inline-flex items-center gap-1.5 px-2.5 py-0.5 rounded-md bg-white/[0.04] border border-white/[0.07] text-[11px] font-mono text-gray-300">
                <span class="w-1.5 h-1.5 rounded-full bg-[#10B981]"></span>
                v2.4.0 • .NET 8.0
              </span>
            </div>

            <!-- Centro/Direita: Status Portátil + Botões Decorativos de Janela -->
            <div class="flex items-center gap-4">
              <span class="hidden md:inline-flex items-center gap-1.5 text-[11px] font-light text-gray-400">
                <svg class="w-3.5 h-3.5 text-[#10B981]" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                  <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
                </svg>
                Atualizado (GitHub Releases)
              </span>

              <!-- Controles decorativos WindowChrome -->
              <div class="flex items-center gap-1.5">
                <span title="Minimizar" class="w-7 h-7 rounded-md flex items-center justify-center text-gray-400 hover:bg-white/5 hover:text-white transition-colors cursor-default">
                  <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                    <path stroke-linecap="round" d="M6 12h12" />
                  </svg>
                </span>
                <span title="Maximizar" class="w-7 h-7 rounded-md flex items-center justify-center text-gray-400 hover:bg-white/5 hover:text-white transition-colors cursor-default">
                  <svg class="w-3 h-3" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                    <rect x="5" y="5" width="14" height="14" rx="2" />
                  </svg>
                </span>
                <span title="Fechar" class="w-7 h-7 rounded-md flex items-center justify-center text-gray-400 hover:bg-[#E53935] hover:text-white transition-colors cursor-default">
                  <svg class="w-3.5 h-3.5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                    <path stroke-linecap="round" d="M6 6l12 12M18 6L6 18" />
                  </svg>
                </span>
              </div>
            </div>
          </div>

          <!-- Navegação de Abas Reais do PRONTO -->
          <div class="px-3 sm:px-5 pt-3 bg-[#1E212B]/60 border-b border-white/[0.06] flex items-center gap-1.5 overflow-x-auto" role="tablist">
            <button
              type="button"
              data-tab="raiox"
              class="app-tab active-tab flex items-center gap-2 px-4 py-2.5 rounded-t-xl text-xs sm:text-sm font-normal whitespace-nowrap transition-all border-b-2 border-[#E53935] bg-[#242833] text-white"
            >
              <svg class="w-4 h-4 text-[#A855F7]" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M9 19v-6a2 2 0 00-2-2H5a2 2 0 00-2 2v6a2 2 0 002 2h2a2 2 0 002-2zm0 0V9a2 2 0 012-2h2a2 2 0 012 2v10m-6 0a2 2 0 002 2h2a2 2 0 002-2m0 0V5a2 2 0 012-2h2a2 2 0 012 2v14a2 2 0 01-2 2h-2a2 2 0 01-2-2z" />
              </svg>
              <span>Raio-X & Telemetria</span>
            </button>

            <button
              type="button"
              data-tab="deploy"
              class="app-tab flex items-center gap-2 px-4 py-2.5 rounded-t-xl text-xs sm:text-sm font-light whitespace-nowrap transition-all border-b-2 border-transparent text-gray-400 hover:text-gray-200 hover:bg-white/[0.03]"
            >
              <svg class="w-4 h-4 text-[#E53935]" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M20 7l-8-4-8 4m16 0l-8 4m8-4v10l-8 4m0-10L4 7m8 4v10M4 7v10l8 4" />
              </svg>
              <span>Deploy & Office</span>
            </button>

            <button
              type="button"
              data-tab="manutencao"
              class="app-tab flex items-center gap-2 px-4 py-2.5 rounded-t-xl text-xs sm:text-sm font-light whitespace-nowrap transition-all border-b-2 border-transparent text-gray-400 hover:text-gray-200 hover:bg-white/[0.03]"
            >
              <svg class="w-4 h-4 text-[#10B981]" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M10.325 4.317c.426-1.756 2.924-1.756 3.35 0a1.724 1.724 0 002.573 1.066c1.543-.94 3.31.826 2.37 2.37a1.724 1.724 0 001.065 2.572c1.756.426 1.756 2.924 0 3.35a1.724 1.724 0 00-1.066 2.573c.94 1.543-.826 3.31-2.37 2.37a1.724 1.724 0 00-2.572 1.065c-.426 1.756-2.924 1.756-3.35 0a1.724 1.724 0 00-2.573-1.066c-1.543.94-3.31-.826-2.37-2.37a1.724 1.724 0 00-1.065-2.572c-1.756-.426-1.756-2.924 0-3.35a1.724 1.724 0 001.066-2.573c-.94-1.543.826-3.31 2.37-2.37.996.608 2.296.07 2.572-1.065z" />
                <path stroke-linecap="round" stroke-linejoin="round" d="M15 12a3 3 0 11-6 0 3 3 0 016 0z" />
              </svg>
              <span>Manutenção</span>
            </button>

            <button
              type="button"
              data-tab="rede"
              class="app-tab flex items-center gap-2 px-4 py-2.5 rounded-t-xl text-xs sm:text-sm font-light whitespace-nowrap transition-all border-b-2 border-transparent text-gray-400 hover:text-gray-200 hover:bg-white/[0.03]"
            >
              <svg class="w-4 h-4 text-sky-400" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M8.111 16.404a5.5 5.5 0 017.778 0M12 20h.01m-7.08-7.071c3.904-3.905 10.236-3.905 14.14 0M1.394 9.393c5.857-5.857 15.355-5.857 21.213 0" />
              </svg>
              <span>Rede & Radar</span>
            </button>
          </div>

          <!-- Corpo Dinâmico das Abas -->
          <div class="p-5 sm:p-7 min-h-[430px]">

            <!-- ==================== ABA 1: RAIO-X & TELEMETRIA ==================== -->
            <div id="pane-raiox" class="tab-pane grid grid-cols-1 lg:grid-cols-12 gap-6 items-stretch">
              <!-- Coluna Esquerda: Especificações de Hardware + CPU/RAM em Tempo Real -->
              <div class="lg:col-span-7 flex flex-col justify-between gap-5">
                <!-- Grid de Especificações Técnicas -->
                <div class="grid grid-cols-1 sm:grid-cols-2 gap-3.5">
                  <div class="p-4 rounded-xl bg-[#1E212B]/80 border border-white/[0.06]">
                    <div class="flex items-center justify-between text-xs text-gray-400 mb-1">
                      <span>Sistema Operacional</span>
                      <span class="font-mono text-[11px] text-[#10B981]">Ativo</span>
                    </div>
                    <p class="text-sm font-normal text-white">Windows 11 Pro (24H2)</p>
                    <p class="text-xs font-mono text-gray-400 mt-0.5">Build 26100 • UEFI / TPM 2.0</p>
                  </div>

                  <div class="p-4 rounded-xl bg-[#1E212B]/80 border border-white/[0.06]">
                    <div class="flex items-center justify-between text-xs text-gray-400 mb-1">
                      <span>Processador</span>
                      <span class="font-mono text-[11px] text-[#A855F7]">8C / 16T</span>
                    </div>
                    <p class="text-sm font-normal text-white">AMD Ryzen 7 5800X</p>
                    <p class="text-xs font-mono text-gray-400 mt-0.5">Clock Base 3.80 GHz</p>
                  </div>

                  <div class="p-4 rounded-xl bg-[#1E212B]/80 border border-white/[0.06]">
                    <div class="flex items-center justify-between text-xs text-gray-400 mb-1">
                      <span>Memória Física</span>
                      <span class="font-mono text-[11px] text-[#A855F7]">Dual-Channel</span>
                    </div>
                    <p class="text-sm font-normal text-white">32.0 GB DDR4</p>
                    <p class="text-xs font-mono text-gray-400 mt-0.5">3200 MHz • Slots 2/4</p>
                  </div>

                  <div class="p-4 rounded-xl bg-[#1E212B]/80 border border-white/[0.06]">
                    <div class="flex items-center justify-between text-xs text-gray-400 mb-1">
                      <span>Armazenamento Primário</span>
                      <span class="font-mono text-[11px] text-[#10B981]">SMART 99%</span>
                    </div>
                    <p class="text-sm font-normal text-white">NVMe PCIe 4.0 1TB</p>
                    <p class="text-xs font-mono text-gray-400 mt-0.5">Temp: 36°C • Saudável</p>
                  </div>
                </div>

                <!-- Barras de Telemetria VSYNC em Tempo Real -->
                <div class="p-5 rounded-xl bg-[#1E212B]/80 border border-white/[0.06] space-y-4">
                  <div class="flex items-center justify-between">
                    <span class="text-xs font-mono uppercase tracking-wider text-gray-300 flex items-center gap-2">
                      <span class="w-2 h-2 rounded-full bg-[#A855F7] animate-ping"></span>
                      Telemetria Sincronizada (VSYNC)
                    </span>
                    <span id="telemetry-uptime" class="text-xs font-mono text-gray-400">Uptime: 02:14:38</span>
                  </div>

                  <!-- Barra CPU -->
                  <div>
                    <div class="flex justify-between text-xs mb-1.5">
                      <span class="text-gray-300 font-light">Carga de CPU</span>
                      <span id="cpu-percent" class="font-mono text-[#A855F7] font-medium">24%</span>
                    </div>
                    <div class="h-2 w-full rounded-full bg-[#242833] overflow-hidden">
                      <div
                        id="cpu-bar"
                        class="h-full rounded-full bg-gradient-to-r from-[#A855F7] to-[#E53935] transition-all duration-700 ease-out"
                        style="width: 24%"
                      ></div>
                    </div>
                  </div>

                  <!-- Barra RAM -->
                  <div>
                    <div class="flex justify-between text-xs mb-1.5">
                      <span class="text-gray-300 font-light">Uso de Memória RAM (11.8 / 32.0 GB)</span>
                      <span id="ram-percent" class="font-mono text-[#10B981] font-medium">37%</span>
                    </div>
                    <div class="h-2 w-full rounded-full bg-[#242833] overflow-hidden">
                      <div
                        id="ram-bar"
                        class="h-full rounded-full bg-gradient-to-r from-emerald-500 to-[#A855F7] transition-all duration-700 ease-out"
                        style="width: 37%"
                      ></div>
                    </div>
                  </div>
                </div>
              </div>

              <!-- Coluna Direita: Velocímetro de Rede Calibrado Interativo -->
              <div class="lg:col-span-5 p-5 rounded-xl bg-[#1E212B]/90 border border-white/[0.06] flex flex-col items-center justify-between text-center">
                <div class="w-full flex items-center justify-between">
                  <span class="text-xs font-mono uppercase tracking-wider text-gray-400">Velocímetro de Rede</span>
                  <span id="speed-status-badge" class="text-[11px] font-mono px-2 py-0.5 rounded bg-white/5 text-gray-300">
                    Pronto
                  </span>
                </div>

                <!-- Gauge SVG Calibrado -->
                <div class="relative w-60 h-40 my-2 flex items-center justify-center">
                  <svg viewBox="0 0 240 155" class="w-full h-full overflow-visible">
                    <defs>
                      <linearGradient id="speedArcGrad" x1="0%" y1="0%" x2="100%" y2="0%">
                        <stop offset="0%" stop-color="#A855F7" />
                        <stop offset="60%" stop-color="#E53935" />
                        <stop offset="100%" stop-color="#10B981" />
                      </linearGradient>
                    </defs>

                    <!-- Arco de Fundo (220 graus: de -200° a +20°) -->
                    <path
                      d="M 35 125 A 90 90 0 1 1 205 125"
                      fill="none"
                      stroke="#2D3240"
                      stroke-width="12"
                      stroke-linecap="round"
                    />

                    <!-- Arco de Progresso Dinâmico -->
                    <path
                      id="speed-arc"
                      d="M 35 125 A 90 90 0 1 1 205 125"
                      fill="none"
                      stroke="url(#speedArcGrad)"
                      stroke-width="12"
                      stroke-linecap="round"
                      stroke-dasharray="345"
                      stroke-dashoffset="345"
                    />

                    <!-- Marcações da Escala -->
                    <text x="24" y="142" fill="#6B7280" font-size="9" font-family="JetBrains Mono">0</text>
                    <text x="26" y="72" fill="#6B7280" font-size="9" font-family="JetBrains Mono">150</text>
                    <text x="112" y="24" fill="#6B7280" font-size="9" font-family="JetBrains Mono">500</text>
                    <text x="196" y="72" fill="#6B7280" font-size="9" font-family="JetBrains Mono">800</text>
                    <text x="196" y="142" fill="#6B7280" font-size="9" font-family="JetBrains Mono">1G</text>

                    <!-- Ponteiro Calibrado -->
                    <g id="speed-needle" transform="rotate(-110 120 115)">
                      <polygon points="118,115 120,38 122,115" fill="#E53935" />
                      <circle cx="120" cy="115" r="7" fill="#242833" stroke="#E53935" stroke-width="2.5" />
                    </g>
                  </svg>

                  <!-- Leitura Digital Central -->
                  <div class="absolute bottom-0 inset-x-0 text-center">
                    <div id="speed-main-value" class="text-3xl font-light font-mono text-white tracking-tight">0.0</div>
                    <div class="text-[11px] font-mono text-gray-400 uppercase tracking-widest">Mbps</div>
                  </div>
                </div>

                <!-- Métricas Ping / Download / Upload -->
                <div class="w-full grid grid-cols-3 gap-2 py-2.5 px-2 rounded-lg bg-[#242833]/80 border border-white/[0.05] text-xs">
                  <div>
                    <span class="block text-[10px] text-gray-400 uppercase">Ping</span>
                    <span id="metric-ping" class="font-mono text-gray-200">-- ms</span>
                  </div>
                  <div class="border-x border-white/[0.06]">
                    <span class="block text-[10px] text-gray-400 uppercase">Download</span>
                    <span id="metric-down" class="font-mono text-[#A855F7]">-- Mbps</span>
                  </div>
                  <div>
                    <span class="block text-[10px] text-gray-400 uppercase">Upload</span>
                    <span id="metric-up" class="font-mono text-[#10B981]">-- Mbps</span>
                  </div>
                </div>

                <!-- Botão Iniciar Teste -->
                <button
                  id="btn-start-speedtest"
                  type="button"
                  class="mt-3 w-full py-2.5 px-4 rounded-xl bg-[#E53935] hover:bg-[#F44336] text-white text-xs sm:text-sm font-medium transition-all flex items-center justify-center gap-2 shadow-lg shadow-[#E53935]/20 active:scale-[0.98]"
                >
                  <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                    <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
                  </svg>
                  <span id="btn-speedtest-label">Iniciar Teste</span>
                </button>
              </div>
            </div>

            <!-- ==================== ABA 2: DEPLOY & OFFICE ==================== -->
            <div id="pane-deploy" class="tab-pane hidden space-y-5">
              <div class="flex flex-col sm:flex-row sm:items-center justify-between gap-3">
                <div>
                  <h3 class="text-base font-normal text-white">Instalação Silenciosa via Winget & Office C2R</h3>
                  <p class="text-xs font-light text-gray-400">Clique em qualquer pacote para visualizar o progresso circular estilo eclipse e confirmação automática.</p>
                </div>
                <button
                  id="btn-install-all"
                  type="button"
                  class="self-start sm:self-auto px-3.5 py-2 rounded-lg bg-white/[0.06] hover:bg-white/[0.11] border border-white/10 text-xs font-normal text-white transition-all flex items-center gap-2"
                >
                  <span class="w-2 h-2 rounded-full bg-[#E53935]"></span>
                  Instalar Pacote Bancada
                </button>
              </div>

              <!-- Grid de Aplicativos Winget -->
              <div class="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-3.5" id="winget-grid">
                <!-- Item 1: Chrome -->
                <div class="deploy-card p-4 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex flex-col items-center text-center justify-between gap-3" data-app="chrome">
                  <div class="relative w-14 h-14 flex items-center justify-center">
                    <svg class="w-14 h-14 -rotate-90" viewBox="0 0 56 56">
                      <circle cx="28" cy="28" r="24" fill="none" stroke="#2D3240" stroke-width="3" />
                      <circle class="progress-ring transition-all duration-150" cx="28" cy="28" r="24" fill="none" stroke="#E53935" stroke-width="3" stroke-linecap="round" stroke-dasharray="150.8" stroke-dashoffset="150.8" />
                    </svg>
                    <div class="icon-badge absolute inset-2 rounded-full bg-[#242833] flex items-center justify-center text-gray-200 transition-all">
                      <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                        <circle cx="12" cy="12" r="9" />
                        <circle cx="12" cy="12" r="3.5" />
                        <path d="M12 8.5h8.2M8.9 13.8L4.8 6.7M15.1 13.8l-4.1 7.1" />
                      </svg>
                    </div>
                  </div>
                  <div>
                    <p class="text-sm font-normal text-white">Google Chrome</p>
                    <p class="text-[11px] font-mono text-gray-400">Google.Chrome</p>
                  </div>
                  <button type="button" class="deploy-btn w-full py-1.5 px-3 rounded-lg bg-[#2D3240] hover:bg-[#E53935] text-xs text-gray-200 hover:text-white transition-colors">
                    Instalar
                  </button>
                </div>

                <!-- Item 2: 7-Zip -->
                <div class="deploy-card p-4 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex flex-col items-center text-center justify-between gap-3" data-app="7zip">
                  <div class="relative w-14 h-14 flex items-center justify-center">
                    <svg class="w-14 h-14 -rotate-90" viewBox="0 0 56 56">
                      <circle cx="28" cy="28" r="24" fill="none" stroke="#2D3240" stroke-width="3" />
                      <circle class="progress-ring transition-all duration-150" cx="28" cy="28" r="24" fill="none" stroke="#E53935" stroke-width="3" stroke-linecap="round" stroke-dasharray="150.8" stroke-dashoffset="150.8" />
                    </svg>
                    <div class="icon-badge absolute inset-2 rounded-full bg-[#242833] flex items-center justify-center text-gray-200 transition-all">
                      <span class="font-mono text-xs font-semibold tracking-tighter">7z</span>
                    </div>
                  </div>
                  <div>
                    <p class="text-sm font-normal text-white">7-Zip Archiver</p>
                    <p class="text-[11px] font-mono text-gray-400">7zip.7zip</p>
                  </div>
                  <button type="button" class="deploy-btn w-full py-1.5 px-3 rounded-lg bg-[#2D3240] hover:bg-[#E53935] text-xs text-gray-200 hover:text-white transition-colors">
                    Instalar
                  </button>
                </div>

                <!-- Item 3: VLC -->
                <div class="deploy-card p-4 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex flex-col items-center text-center justify-between gap-3" data-app="vlc">
                  <div class="relative w-14 h-14 flex items-center justify-center">
                    <svg class="w-14 h-14 -rotate-90" viewBox="0 0 56 56">
                      <circle cx="28" cy="28" r="24" fill="none" stroke="#2D3240" stroke-width="3" />
                      <circle class="progress-ring transition-all duration-150" cx="28" cy="28" r="24" fill="none" stroke="#E53935" stroke-width="3" stroke-linecap="round" stroke-dasharray="150.8" stroke-dashoffset="150.8" />
                    </svg>
                    <div class="icon-badge absolute inset-2 rounded-full bg-[#242833] flex items-center justify-center text-gray-200 transition-all">
                      <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M14.752 11.168l-3.197-2.132A1 1 0 0010 9.87v4.263a1 1 0 001.555.832l3.197-2.132a1 1 0 000-1.664z" />
                        <path stroke-linecap="round" stroke-linejoin="round" d="M21 12a9 9 0 11-18 0 9 9 0 0118 0z" />
                      </svg>
                    </div>
                  </div>
                  <div>
                    <p class="text-sm font-normal text-white">VLC Media Player</p>
                    <p class="text-[11px] font-mono text-gray-400">VideoLAN.VLC</p>
                  </div>
                  <button type="button" class="deploy-btn w-full py-1.5 px-3 rounded-lg bg-[#2D3240] hover:bg-[#E53935] text-xs text-gray-200 hover:text-white transition-colors">
                    Instalar
                  </button>
                </div>

                <!-- Item 4: AnyDesk -->
                <div class="deploy-card p-4 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex flex-col items-center text-center justify-between gap-3" data-app="anydesk">
                  <div class="relative w-14 h-14 flex items-center justify-center">
                    <svg class="w-14 h-14 -rotate-90" viewBox="0 0 56 56">
                      <circle cx="28" cy="28" r="24" fill="none" stroke="#2D3240" stroke-width="3" />
                      <circle class="progress-ring transition-all duration-150" cx="28" cy="28" r="24" fill="none" stroke="#E53935" stroke-width="3" stroke-linecap="round" stroke-dasharray="150.8" stroke-dashoffset="150.8" />
                    </svg>
                    <div class="icon-badge absolute inset-2 rounded-full bg-[#242833] flex items-center justify-center text-gray-200 transition-all">
                      <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M9.75 17L9 20l-1 1h8l-1-1-.75-3M3 13h18M5 17h14a2 2 0 002-2V5a2 2 0 00-2-2H5a2 2 0 00-2 2v10a2 2 0 002 2z" />
                      </svg>
                    </div>
                  </div>
                  <div>
                    <p class="text-sm font-normal text-white">AnyDesk Suporte</p>
                    <p class="text-[11px] font-mono text-gray-400">AnyDesk.AnyDesk</p>
                  </div>
                  <button type="button" class="deploy-btn w-full py-1.5 px-3 rounded-lg bg-[#2D3240] hover:bg-[#E53935] text-xs text-gray-200 hover:text-white transition-colors">
                    Instalar
                  </button>
                </div>
              </div>

              <!-- Destaque Microsoft Office C2R -->
              <div class="deploy-card p-4 sm:p-5 rounded-xl bg-gradient-to-r from-[#1E212B] via-[#242833] to-[#1E212B] border border-white/[0.08] flex flex-col sm:flex-row items-center justify-between gap-4" data-app="office">
                <div class="flex items-center gap-4">
                  <div class="relative w-14 h-14 flex-shrink-0 flex items-center justify-center">
                    <svg class="w-14 h-14 -rotate-90" viewBox="0 0 56 56">
                      <circle cx="28" cy="28" r="24" fill="none" stroke="#2D3240" stroke-width="3" />
                      <circle class="progress-ring transition-all duration-150" cx="28" cy="28" r="24" fill="none" stroke="#E53935" stroke-width="3" stroke-linecap="round" stroke-dasharray="150.8" stroke-dashoffset="150.8" />
                    </svg>
                    <div class="icon-badge absolute inset-2 rounded-full bg-[#242833] flex items-center justify-center text-[#E53935] transition-all">
                      <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
                        <path stroke-linecap="round" stroke-linejoin="round" d="M4 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2V6zM14 6a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2V6zM4 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2H6a2 2 0 01-2-2v-2zM14 16a2 2 0 012-2h2a2 2 0 012 2v2a2 2 0 01-2 2h-2a2 2 0 01-2-2v-2z" />
                      </svg>
                    </div>
                  </div>
                  <div>
                    <div class="flex items-center gap-2">
                      <h4 class="text-sm sm:text-base font-normal text-white">Microsoft Office (Click-to-Run C2R)</h4>
                      <span class="text-[10px] font-mono px-2 py-0.5 rounded bg-[#E53935]/15 text-[#E53935] border border-[#E53935]/30">pt-BR • x64</span>
                    </div>
                    <p class="text-xs font-light text-gray-400 mt-0.5">Implantação XML personalizada (Word, Excel, PowerPoint) direto dos servidores oficiais Microsoft.</p>
                  </div>
                </div>

                <button type="button" class="deploy-btn w-full sm:w-auto px-5 py-2.5 rounded-xl bg-[#E53935] hover:bg-[#F44336] text-white text-xs font-medium whitespace-nowrap transition-all shadow-lg shadow-[#E53935]/20">
                  Implantar Office
                </button>
              </div>
            </div>

            <!-- ==================== ABA 3: MANUTENÇÃO ==================== -->
            <div id="pane-manutencao" class="tab-pane hidden space-y-5">
              <div class="flex items-center justify-between">
                <div>
                  <h3 class="text-base font-normal text-white">Rotinas Assistidas de Integridade & Otimização</h3>
                  <p class="text-xs font-light text-gray-400">Execução nativa sem janelas de prompt poluídas. Clique em uma rotina para simular a execução.</p>
                </div>
                <span class="hidden sm:inline-block text-xs font-mono text-gray-400">Elevação: SYSTEM / Admin</span>
              </div>

              <div class="grid grid-cols-1 md:grid-cols-2 gap-4">
                <!-- Rotina 1: DISM -->
                <div class="maint-card p-4 sm:p-5 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex items-center justify-between gap-4">
                  <div class="flex items-center gap-3.5">
                    <div class="relative w-12 h-12 flex-shrink-0 flex items-center justify-center">
                      <svg class="w-12 h-12 -rotate-90" viewBox="0 0 48 48">
                        <circle cx="24" cy="24" r="20" fill="none" stroke="#2D3240" stroke-width="3" />
                        <circle class="maint-ring transition-all duration-150" cx="24" cy="24" r="20" fill="none" stroke="#A855F7" stroke-width="3" stroke-linecap="round" stroke-dasharray="125.6" stroke-dashoffset="125.6" />
                      </svg>
                      <span class="maint-pct absolute text-[11px] font-mono text-gray-300">DISM</span>
                    </div>
                    <div>
                      <h4 class="text-sm font-normal text-white">Reparo DISM</h4>
                      <p class="maint-desc text-xs font-light text-gray-400">DISM /Online /Cleanup-Image /RestoreHealth</p>
                    </div>
                  </div>
                  <button type="button" class="maint-btn px-3.5 py-2 rounded-lg bg-[#2D3240] hover:bg-[#A855F7] text-xs text-white transition-colors whitespace-nowrap">
                    Executar
                  </button>
                </div>

                <!-- Rotina 2: SFC -->
                <div class="maint-card p-4 sm:p-5 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex items-center justify-between gap-4">
                  <div class="flex items-center gap-3.5">
                    <div class="relative w-12 h-12 flex-shrink-0 flex items-center justify-center">
                      <svg class="w-12 h-12 -rotate-90" viewBox="0 0 48 48">
                        <circle cx="24" cy="24" r="20" fill="none" stroke="#2D3240" stroke-width="3" />
                        <circle class="maint-ring transition-all duration-150" cx="24" cy="24" r="20" fill="none" stroke="#A855F7" stroke-width="3" stroke-linecap="round" stroke-dasharray="125.6" stroke-dashoffset="125.6" />
                      </svg>
                      <span class="maint-pct absolute text-[11px] font-mono text-gray-300">SFC</span>
                    </div>
                    <div>
                      <h4 class="text-sm font-normal text-white">Verificar SFC</h4>
                      <p class="maint-desc text-xs font-light text-gray-400">sfc /scannow — Arquivos protegidos do Windows</p>
                    </div>
                  </div>
                  <button type="button" class="maint-btn px-3.5 py-2 rounded-lg bg-[#2D3240] hover:bg-[#A855F7] text-xs text-white transition-colors whitespace-nowrap">
                    Executar
                  </button>
                </div>

                <!-- Rotina 3: Limpeza Profunda -->
                <div class="maint-card p-4 sm:p-5 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex items-center justify-between gap-4">
                  <div class="flex items-center gap-3.5">
                    <div class="relative w-12 h-12 flex-shrink-0 flex items-center justify-center">
                      <svg class="w-12 h-12 -rotate-90" viewBox="0 0 48 48">
                        <circle cx="24" cy="24" r="20" fill="none" stroke="#2D3240" stroke-width="3" />
                        <circle class="maint-ring transition-all duration-150" cx="24" cy="24" r="20" fill="none" stroke="#E53935" stroke-width="3" stroke-linecap="round" stroke-dasharray="125.6" stroke-dashoffset="125.6" />
                      </svg>
                      <span class="maint-pct absolute text-[11px] font-mono text-gray-300">TMP</span>
                    </div>
                    <div>
                      <h4 class="text-sm font-normal text-white">Limpeza Profunda</h4>
                      <p class="maint-desc text-xs font-light text-gray-400">Cache Windows Update, Temp, Prefetch e Logs</p>
                    </div>
                  </div>
                  <button type="button" class="maint-btn px-3.5 py-2 rounded-lg bg-[#2D3240] hover:bg-[#E53935] text-xs text-white transition-colors whitespace-nowrap">
                    Limpar
                  </button>
                </div>

                <!-- Rotina 4: Otimizar Sistema -->
                <div class="maint-card p-4 sm:p-5 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex items-center justify-between gap-4">
                  <div class="flex items-center gap-3.5">
                    <div class="relative w-12 h-12 flex-shrink-0 flex items-center justify-center">
                      <svg class="w-12 h-12 -rotate-90" viewBox="0 0 48 48">
                        <circle cx="24" cy="24" r="20" fill="none" stroke="#2D3240" stroke-width="3" />
                        <circle class="maint-ring transition-all duration-150" cx="24" cy="24" r="20" fill="none" stroke="#10B981" stroke-width="3" stroke-linecap="round" stroke-dasharray="125.6" stroke-dashoffset="125.6" />
                      </svg>
                      <span class="maint-pct absolute text-[11px] font-mono text-gray-300">OPT</span>
                    </div>
                    <div>
                      <h4 class="text-sm font-normal text-white">Otimizar Sistema</h4>
                      <p class="maint-desc text-xs font-light text-gray-400">Plano Alto Desempenho, TRIM SSD e Telemetria</p>
                    </div>
                  </div>
                  <button type="button" class="maint-btn px-3.5 py-2 rounded-lg bg-[#2D3240] hover:bg-[#10B981] text-xs text-white transition-colors whitespace-nowrap">
                    Otimizar
                  </button>
                </div>
              </div>
            </div>

            <!-- ==================== ABA 4: REDE & RADAR ==================== -->
            <div id="pane-rede" class="tab-pane hidden grid grid-cols-1 lg:grid-cols-12 gap-5 items-stretch">
              <!-- Coluna Esquerda: NetDoctor (Correção Pastas Compartilhadas) -->
              <div class="lg:col-span-5 p-5 rounded-xl bg-[#1E212B]/85 border border-white/[0.06] flex flex-col justify-between gap-4">
                <div>
                  <div class="inline-flex items-center gap-2 px-2.5 py-1 rounded-md bg-sky-500/10 border border-sky-500/20 text-sky-400 text-[11px] font-mono mb-3">
                    NetDoctor Engine
                  </div>
                  <h3 class="text-base font-normal text-white">Compartilhamento & Rede Windows</h3>
                  <p class="text-xs font-light text-gray-400 mt-1.5 leading-relaxed">
                    Resolve bloqueios clássicos de acesso a pastas em rede no Windows 10/11 (Guest Fallback SMB2, Descoberta de Rede e Serviços Function Discovery) com um clique.
                  </p>

                  <!-- Checklist de Diagnóstico -->
                  <ul id="netdoctor-checklist" class="mt-4 space-y-2 text-xs font-mono text-gray-400">
                    <li class="flex items-center gap-2">
                      <span class="nd-dot w-2 h-2 rounded-full bg-amber-400"></span>
                      <span>AllowInsecureGuestAuth (SMB2)</span>
                    </li>
                    <li class="flex items-center gap-2">
                      <span class="nd-dot w-2 h-2 rounded-full bg-amber-400"></span>
                      <span>Serviço FDResPub / SSDPSRV</span>
                    </li>
                    <li class="flex items-center gap-2">
                      <span class="nd-dot w-2 h-2 rounded-full bg-amber-400"></span>
                      <span>Perfil de Rede Privado & Cache DNS</span>
                    </li>
                  </ul>
                </div>

                <div>
                  <button
                    id="btn-netdoctor"
                    type="button"
                    class="w-full py-2.5 px-4 rounded-xl bg-[#E53935] hover:bg-[#F44336] text-white text-xs sm:text-sm font-medium transition-all flex items-center justify-center gap-2 shadow-lg shadow-[#E53935]/20"
                  >
                    <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
                      <path stroke-linecap="round" stroke-linejoin="round" d="M9 12l2 2 4-4m5.618-4.016A11.955 11.955 0 0112 2.944a11.955 11.955 0 01-8.618 3.04A12.02 12.02 0 003 9c0 5.591 3.824 10.29 9 11.622 5.176-1.332 9-6.03 9-11.622 0-1.042-.133-2.052-.382-3.016z" />
                    </svg>
                    <span id="btn-netdoctor-text">Corrigir Pastas Comp.</span>
                  </button>
                  <p id="netdoctor-feedback" class="text-[11px] font-mono text-center text-gray-400 mt-2">
                    Sub-rede detectada: 192.168.1.0/24
                  </p>
                </div>
              </div>

              <!-- Coluna Direita: Radar Inteligente LAN (Fundo Suavemente Desfocado) -->
              <div class="lg:col-span-7 p-5 rounded-xl bg-[#1E212B]/70 backdrop-blur-md border border-white/[0.08] relative overflow-hidden flex flex-col justify-between">
                <!-- Elemento decorativo Radar SVG ao fundo -->
                <div class="pointer-events-none absolute -right-12 -bottom-12 w-56 h-56 opacity-20">
                  <svg viewBox="0 0 200 200" class="w-full h-full">
                    <circle cx="100" cy="100" r="90" fill="none" stroke="#10B981" stroke-width="1" />
                    <circle cx="100" cy="100" r="60" fill="none" stroke="#10B981" stroke-width="1" />
                    <circle cx="100" cy="100" r="30" fill="none" stroke="#10B981" stroke-width="1" />
                    <line class="radar-beam" x1="100" y1="100" x2="100" y2="10" stroke="#10B981" stroke-width="2" />
                  </svg>
                </div>

                <div class="flex items-center justify-between gap-2 mb-3 relative z-10">
                  <div class="flex items-center gap-2">
                    <span class="w-2 h-2 rounded-full bg-[#10B981] animate-pulse"></span>
                    <h4 class="text-sm font-normal text-white">Radar Inteligente — Dispositivos na LAN</h4>
                  </div>
                  <button
                    id="btn-scan-lan"
                    type="button"
                    class="px-3 py-1.5 rounded-lg bg-white/[0.07] hover:bg-white/[0.13] border border-white/10 text-xs font-mono text-gray-200 transition-colors"
                  >
                    Atualizar Varredura
                  </button>
                </div>

                <!-- Tabela de Hosts Encontrados -->
                <div class="overflow-x-auto relative z-10">
                  <table class="w-full text-left border-collapse">
                    <thead>
                      <tr class="border-b border-white/[0.07] text-[11px] font-mono text-gray-400 uppercase">
                        <th class="py-2 pr-3">Endereço IP</th>
                        <th class="py-2 px-3">Hostname / Fabricante</th>
                        <th class="py-2 pl-3 text-right">Latência</th>
                      </tr>
                    </thead>
                    <tbody id="radar-device-list" class="divide-y divide-white/[0.04] text-xs font-mono">
                      <tr>
                        <td class="py-2.5 pr-3 text-[#10B981]">192.168.1.1</td>
                        <td class="py-2.5 px-3 text-gray-200">Gateway-MikroTik <span class="text-gray-500">(Router)</span></td>
                        <td class="py-2.5 pl-3 text-right text-gray-300">1 ms</td>
                      </tr>
                      <tr>
                        <td class="py-2.5 pr-3 text-white">192.168.1.14</td>
                        <td class="py-2.5 px-3 text-gray-200">BANCADA-TECNICA <span class="text-[#A855F7]">(Este PC)</span></td>
                        <td class="py-2.5 pl-3 text-right text-gray-300">&lt;1 ms</td>
                      </tr>
                      <tr>
                        <td class="py-2.5 pr-3 text-white">192.168.1.22</td>
                        <td class="py-2.5 px-3 text-gray-200">SERVIDOR-ARQUIVOS <span class="text-gray-500">(SMB)</span></td>
                        <td class="py-2.5 pl-3 text-right text-gray-300">2 ms</td>
                      </tr>
                      <tr>
                        <td class="py-2.5 pr-3 text-white">192.168.1.45</td>
                        <td class="py-2.5 px-3 text-gray-200">EPSON-L3250-NET <span class="text-gray-500">(Printer)</span></td>
                        <td class="py-2.5 pl-3 text-right text-gray-300">4 ms</td>
                      </tr>
                      <tr>
                        <td class="py-2.5 pr-3 text-white">192.168.1.89</td>
                        <td class="py-2.5 px-3 text-gray-200">NOTEBOOK-CLIENTE <span class="text-gray-500">(Wi-Fi)</span></td>
                        <td class="py-2.5 pl-3 text-right text-gray-300">6 ms</td>
                      </tr>
                    </tbody>
                  </table>
                </div>

                <div class="mt-3 pt-2.5 border-t border-white/[0.06] flex items-center justify-between text-[11px] font-mono text-gray-400 relative z-10">
                  <span id="radar-summary">5 hosts ativos encontrados em 0.42s</span>
                  <span>ARP / ICMP Paralelo</span>
                </div>
              </div>
            </div>

          </div>
        </div>
      </div>
    </section>

    <!-- ==================== 3. SEÇÃO DIFERENCIAIS (3 PILARES) ==================== -->
    <section id="diferenciais" class="max-w-6xl mx-auto px-4 sm:px-6 mt-24 sm:mt-32 scroll-mt-24">
      <div class="text-center max-w-2xl mx-auto mb-12">
        <span class="text-xs font-mono uppercase tracking-widest text-[#E53935]">Projetado para Bancada</span>
        <h2 class="mt-2 text-2xl sm:text-4xl font-extralight text-white tracking-tight">
          Por que usar o PRONTO na bancada?
        </h2>
        <p class="mt-3 text-sm sm:text-base font-light text-gray-400">
          Criado para eliminar o tempo perdido configurando ferramentas espalhadas após cada formatação ou atendimento técnico.
        </p>
      </div>

      <div class="grid grid-cols-1 md:grid-cols-3 gap-6">
        <!-- Pilar 1 -->
        <div class="glass-card rounded-2xl p-6 sm:p-7">
          <div class="w-11 h-11 rounded-xl bg-[#E53935]/15 border border-[#E53935]/30 flex items-center justify-center text-[#E53935] mb-5">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M12 18h.01M8 21h8a2 2 0 002-2V5a2 2 0 00-2-2H8a2 2 0 00-2 2v14a2 2 0 002 2z" />
            </svg>
          </div>
          <h3 class="text-lg font-normal text-white">100% Portátil (Zero Rastro)</h3>
          <p class="mt-2.5 text-sm font-light text-gray-300/90 leading-relaxed">
            Executável único (<span class="font-mono text-xs text-white">PRONTO.exe</span>) que roda direto do pendrive sem instalar serviços em segundo plano ou sujar o registro do Windows do cliente.
          </p>
        </div>

        <!-- Pilar 2 -->
        <div class="glass-card rounded-2xl p-6 sm:p-7">
          <div class="w-11 h-11 rounded-xl bg-[#A855F7]/15 border border-[#A855F7]/30 flex items-center justify-center text-[#A855F7] mb-5">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M13 10V3L4 14h7v7l9-11h-7z" />
            </svg>
          </div>
          <h3 class="text-lg font-normal text-white">Rápido e Direto ao Ponto</h3>
          <p class="mt-2.5 text-sm font-light text-gray-300/90 leading-relaxed">
            Interface nativa compilada em <span class="font-mono text-xs text-white">.NET 8.0 WPF</span> com abertura instantânea, sem telas carregadas, anúncios ou menus escondidos.
          </p>
        </div>

        <!-- Pilar 3 -->
        <div class="glass-card rounded-2xl p-6 sm:p-7">
          <div class="w-11 h-11 rounded-xl bg-[#10B981]/15 border border-[#10B981]/30 flex items-center justify-center text-[#10B981] mb-5">
            <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="1.8" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M4 4v5h.582m15.356 2A8.001 8.001 0 004.582 9m0 0H9m11 11v-5h-.581m0 0a8.003 8.003 0 01-15.357-2m15.357 2H15" />
            </svg>
          </div>
          <h3 class="text-lg font-normal text-white">Sempre Atualizado</h3>
          <p class="mt-2.5 text-sm font-light text-gray-300/90 leading-relaxed">
            Verificação nativa de novas versões integrada diretamente ao <span class="font-mono text-xs text-white">GitHub Releases</span> com substituição automática do binário em um clique.
          </p>
        </div>
      </div>
    </section>

    <!-- ==================== MÓDULOS INTEGRADOS ==================== -->
    <section id="modulos" class="max-w-6xl mx-auto px-4 sm:px-6 mt-24 sm:mt-32 scroll-mt-24">
      <div class="flex flex-col md:flex-row md:items-end justify-between mb-10 gap-4">
        <div>
          <span class="text-xs font-mono uppercase tracking-widest text-[#A855F7]">Suíte Completa</span>
          <h2 class="mt-2 text-2xl sm:text-4xl font-extralight text-white tracking-tight">
            Quatro módulos essenciais em uma janela
          </h2>
        </div>
        <a href="#layout-interativo" class="text-xs font-mono text-gray-300 hover:text-white flex items-center gap-1.5 transition-colors">
          <span>Testar no simulador acima</span>
          <span>↑</span>
        </a>
      </div>

      <div class="grid grid-cols-1 sm:grid-cols-2 gap-5">
        <div class="glass-card rounded-2xl p-6 flex items-start justify-between gap-4">
          <div>
            <span class="text-xs font-mono text-[#A855F7]">01 / DIAGNÓSTICO</span>
            <h3 class="text-lg font-normal text-white mt-1">Raio-X & Telemetria</h3>
            <p class="text-sm font-light text-gray-400 mt-2 leading-relaxed">
              Leitura instantânea de hardware (CPU, RAM, Placa-mãe, Saúde SMART de SSD/NVMe), monitoramento em tempo real e velocímetro de rede calibrado.
            </p>
          </div>
          <button type="button" data-jump-tab="raiox" class="jump-tab-btn px-3 py-1.5 rounded-lg bg-white/5 hover:bg-white/10 text-xs font-mono text-gray-300 whitespace-nowrap">
            Ver aba
          </button>
        </div>

        <div class="glass-card rounded-2xl p-6 flex items-start justify-between gap-4">
          <div>
            <span class="text-xs font-mono text-[#E53935]">02 / PÓS-FORMATAÇÃO</span>
            <h3 class="text-lg font-normal text-white mt-1">Deploy Winget & Office C2R</h3>
            <p class="text-sm font-light text-gray-400 mt-2 leading-relaxed">
              Instalação silenciosa de navegadores, compactadores, utilitários remotos e implantação oficial do Microsoft Office Click-to-Run sem intervenção manual.
            </p>
          </div>
          <button type="button" data-jump-tab="deploy" class="jump-tab-btn px-3 py-1.5 rounded-lg bg-white/5 hover:bg-white/10 text-xs font-mono text-gray-300 whitespace-nowrap">
            Ver aba
          </button>
        </div>

        <div class="glass-card rounded-2xl p-6 flex items-start justify-between gap-4">
          <div>
            <span class="text-xs font-mono text-[#10B981]">03 / SISTEMA</span>
            <h3 class="text-lg font-normal text-white mt-1">Manutenção DISM & SFC</h3>
            <p class="text-sm font-light text-gray-400 mt-2 leading-relaxed">
              Reparo de imagem do Windows, verificação de arquivos corrompidos, limpeza profunda de temporários e ajustes de desempenho com acompanhamento visual.
            </p>
          </div>
          <button type="button" data-jump-tab="manutencao" class="jump-tab-btn px-3 py-1.5 rounded-lg bg-white/5 hover:bg-white/10 text-xs font-mono text-gray-300 whitespace-nowrap">
            Ver aba
          </button>
        </div>

        <div class="glass-card rounded-2xl p-6 flex items-start justify-between gap-4">
          <div>
            <span class="text-xs font-mono text-sky-400">04 / CONECTIVIDADE</span>
            <h3 class="text-lg font-normal text-white mt-1">NetDoctor & Radar LAN</h3>
            <p class="text-sm font-light text-gray-400 mt-2 leading-relaxed">
              Correção automática de compartilhamento de pastas no Windows 10/11 e varredura rápida de IPs, hostnames e latência na rede local.
            </p>
          </div>
          <button type="button" data-jump-tab="rede" class="jump-tab-btn px-3 py-1.5 rounded-lg bg-white/5 hover:bg-white/10 text-xs font-mono text-gray-300 whitespace-nowrap">
            Ver aba
          </button>
        </div>
      </div>
    </section>

    <!-- ==================== 4. SEÇÃO COMO USAR (3 PASSOS SIMPLES) ==================== -->
    <section id="como-usar" class="max-w-6xl mx-auto px-4 sm:px-6 mt-24 sm:mt-32 scroll-mt-24">
      <div class="glass-panel rounded-3xl p-7 sm:p-12 border border-white/[0.08]">
        <div class="text-center max-w-xl mx-auto mb-10">
          <span class="text-xs font-mono uppercase tracking-widest text-[#10B981]">Fluxo em Segundos</span>
          <h2 class="mt-2 text-2xl sm:text-3xl font-extralight text-white tracking-tight">
            Como usar o PRONTO
          </h2>
        </div>

        <div class="grid grid-cols-1 md:grid-cols-3 gap-8 relative">
          <!-- Passo 1 -->
          <div class="relative">
            <div class="flex items-center gap-3 mb-3">
              <span class="w-8 h-8 rounded-full bg-[#E53935] text-white font-mono text-xs flex items-center justify-center shadow-lg shadow-[#E53935]/30">
                1
              </span>
              <span class="text-xs font-mono uppercase tracking-wider text-gray-400">Download Direto</span>
            </div>
            <h3 class="text-base font-normal text-white">Baixe o PRONTO.exe</h3>
            <p class="mt-2 text-sm font-light text-gray-400 leading-relaxed">
              Acesse a aba <a href="https://github.com/trojanBR/PRONTO/releases/latest" target="_blank" rel="noopener noreferrer" class="text-white underline decoration-[#E53935] underline-offset-4 hover:text-[#E53935] transition-colors">Releases do GitHub</a> e baixe o executável compilado mais recente.
            </p>
          </div>

          <!-- Passo 2 -->
          <div class="relative">
            <div class="flex items-center gap-3 mb-3">
              <span class="w-8 h-8 rounded-full bg-[#2D3240] border border-white/15 text-white font-mono text-xs flex items-center justify-center">
                2
              </span>
              <span class="text-xs font-mono uppercase tracking-wider text-gray-400">Portabilidade</span>
            </div>
            <h3 class="text-base font-normal text-white">Copie para o seu pendrive</h3>
            <p class="mt-2 text-sm font-light text-gray-400 leading-relaxed">
              Mova o <span class="font-mono text-xs text-gray-200">PRONTO.exe</span> para o seu pendrive de manutenção ou pasta de ferramentas sem precisar descompactar nada.
            </p>
          </div>

          <!-- Passo 3 -->
          <div class="relative">
            <div class="flex items-center gap-3 mb-3">
              <span class="w-8 h-8 rounded-full bg-[#10B981]/20 border border-[#10B981]/40 text-[#10B981] font-mono text-xs flex items-center justify-center">
                3
              </span>
              <span class="text-xs font-mono uppercase tracking-wider text-gray-400">Pronto para Ação</span>
            </div>
            <h3 class="text-base font-normal text-white">Execute como Administrador</h3>
            <p class="mt-2 text-sm font-light text-gray-400 leading-relaxed">
              Dê um duplo clique, confirme a elevação UAC e tenha diagnóstico, deploy e reparos prontos em segundos.
            </p>
          </div>
        </div>

        <!-- CTA Final dentro do Card -->
        <div class="mt-10 pt-8 border-t border-white/[0.06] flex flex-col sm:flex-row items-center justify-between gap-4">
          <p class="text-xs sm:text-sm font-light text-gray-300 text-center sm:text-left">
            Código aberto e hospedado oficialmente em <span class="font-mono text-white">github.com/trojanBR/PRONTO</span>
          </p>
          <a
            href="https://github.com/trojanBR/PRONTO/releases/latest"
            target="_blank"
            rel="noopener noreferrer"
            class="inline-flex items-center gap-2 px-5 py-2.5 rounded-xl bg-[#E53935] hover:bg-[#F44336] text-white text-xs sm:text-sm font-medium transition-all shadow-lg shadow-[#E53935]/20"
          >
            <span>Baixar Agora (.exe)</span>
            <svg class="w-4 h-4" fill="none" stroke="currentColor" stroke-width="2" viewBox="0 0 24 24">
              <path stroke-linecap="round" stroke-linejoin="round" d="M17 8l4 4m0 0l-4 4m4-4H3" />
            </svg>
          </a>
        </div>
      </div>
    </section>
  </main>

  <!-- ==================== 5. FOOTER MINIMALISTA ==================== -->
  <footer class="border-t border-white/[0.06] bg-[#1B1E26]/70 py-10">
    <div class="max-w-6xl mx-auto px-4 sm:px-6 flex flex-col sm:flex-row items-center justify-between gap-6">
      <div class="flex flex-col sm:flex-row items-center gap-3 sm:gap-5">
        <a href="#" class="pronto-logo text-base">
          PR<span class="pronto-logo-dot"></span>NTO
        </a>
        <span class="text-xs font-light text-gray-400">
          Utilitário portátil para Windows • C# / WPF (.NET 8.0)
        </span>
      </div>

      <div class="flex items-center gap-6 text-xs font-light text-gray-400">
        <a
          href="https://github.com/trojanBR/PRONTO"
          target="_blank"
          rel="noopener noreferrer"
          class="hover:text-white transition-colors"
        >
          Repositório GitHub
        </a>
        <a
          href="https://github.com/trojanBR/PRONTO/releases/latest"
          target="_blank"
          rel="noopener noreferrer"
          class="hover:text-white transition-colors"
        >
          Última Release
        </a>
      </div>
    </div>
  </footer>

  <!-- ==================== LÓGICA INTERATIVA DO SIMULADOR PRONTO ==================== -->
  <script>
    document.addEventListener('DOMContentLoaded', () => {
      /* ---------------- 1. MENU MOBILE ---------------- */
      const mobileMenuBtn = document.getElementById('mobile-menu-btn');
      const mobileMenu = document.getElementById('mobile-menu');

      mobileMenuBtn.addEventListener('click', () => {
        mobileMenu.classList.toggle('hidden');
      });

      document.querySelectorAll('.mobile-nav-link').forEach((link) => {
        link.addEventListener('click', () => mobileMenu.classList.add('hidden'));
      });

      /* ---------------- 2. NAVEGAÇÃO ENTRE ABAS DO SIMULADOR ---------------- */
      const tabButtons = document.querySelectorAll('.app-tab');
      const tabPanes = {
        raiox: document.getElementById('pane-raiox'),
        deploy: document.getElementById('pane-deploy'),
        manutencao: document.getElementById('pane-manutencao'),
        rede: document.getElementById('pane-rede'),
      };

      function switchTab(tabKey) {
        tabButtons.forEach((btn) => {
          const isTarget = btn.dataset.tab === tabKey;
          if (isTarget) {
            btn.classList.add('active-tab', 'border-[#E53935]', 'bg-[#242833]', 'text-white', 'font-normal');
            btn.classList.remove('border-transparent', 'text-gray-400', 'font-light');
          } else {
            btn.classList.remove('active-tab', 'border-[#E53935]', 'bg-[#242833]', 'text-white', 'font-normal');
            btn.classList.add('border-transparent', 'text-gray-400', 'font-light');
          }
        });

        Object.entries(tabPanes).forEach(([key, pane]) => {
          if (key === tabKey) {
            pane.classList.remove('hidden');
          } else {
            pane.classList.add('hidden');
          }
        });
      }

      tabButtons.forEach((btn) => {
        btn.addEventListener('click', () => switchTab(btn.dataset.tab));
      });

      // Botões "Ver aba" na seção Módulos que levam ao simulador e abrem a aba correspondente
      document.querySelectorAll('.jump-tab-btn').forEach((btn) => {
        btn.addEventListener('click', () => {
          const targetTab = btn.dataset.jumpTab;
          switchTab(targetTab);
          document.getElementById('layout-interativo').scrollIntoView({ behavior: 'smooth' });
        });
      });

      /* ---------------- 3. TELEMETRIA CPU / RAM EM TEMPO REAL ---------------- */
      const cpuBar = document.getElementById('cpu-bar');
      const cpuPercent = document.getElementById('cpu-percent');
      const ramBar = document.getElementById('ram-bar');
      const ramPercent = document.getElementById('ram-percent');
      const uptimeEl = document.getElementById('telemetry-uptime');

      let uptimeSeconds = 2 * 3600 + 14 * 60 + 38;

      setInterval(() => {
        // Oscilação realista suave de CPU e RAM
        const cpuVal = Math.floor(16 + Math.random() * 22);
        const ramVal = Math.floor(35 + Math.random() * 6);

        cpuBar.style.width = `${cpuVal}%`;
        cpuPercent.textContent = `${cpuVal}%`;

        ramBar.style.width = `${ramVal}%`;
        ramPercent.textContent = `${ramVal}%`;

        // Atualiza contador Uptime
        uptimeSeconds++;
        const hrs = String(Math.floor(uptimeSeconds / 3600)).padStart(2, '0');
        const mins = String(Math.floor((uptimeSeconds % 3600) / 60)).padStart(2, '0');
        const secs = String(uptimeSeconds % 60).padStart(2, '0');
        uptimeEl.textContent = `Uptime: ${hrs}:${mins}:${secs}`;
      }, 1200);

      /* ---------------- 4. VELOCÍMETRO DE REDE CALIBRADO ---------------- */
      const btnSpeedTest = document.getElementById('btn-start-speedtest');
      const btnSpeedLabel = document.getElementById('btn-speedtest-label');
      const speedBadge = document.getElementById('speed-status-badge');
      const speedArc = document.getElementById('speed-arc');
      const speedNeedle = document.getElementById('speed-needle');
      const speedMainValue = document.getElementById('speed-main-value');
      const metricPing = document.getElementById('metric-ping');
      const metricDown = document.getElementById('metric-down');
      const metricUp = document.getElementById('metric-up');

      let speedTestRunning = false;
      const ARC_TOTAL_LENGTH = 345; // Comprimento total do path SVG

      function setGaugeMbps(mbps) {
        // Escala não-linear calibrada até 1000 Mbps
        const clamped = Math.max(0, Math.min(1000, mbps));
        let ratio;
        if (clamped <= 150) {
          ratio = (clamped / 150) * 0.25;
        } else if (clamped <= 500) {
          ratio = 0.25 + ((clamped - 150) / 350) * 0.25;
        } else {
          ratio = 0.5 + ((clamped - 500) / 500) * 0.5;
        }

        const dashOffset = ARC_TOTAL_LENGTH * (1 - ratio);
        speedArc.style.strokeDashoffset = dashOffset.toFixed(1);

        // Ângulo do ponteiro: -110deg (0 Mbps) até +110deg (1000 Mbps)
        const angle = -110 + ratio * 220;
        speedNeedle.setAttribute('transform', `rotate(${angle.toFixed(1)} 120 115)`);
        speedMainValue.textContent = clamped.toFixed(1);
      }

      btnSpeedTest.addEventListener('click', () => {
        if (speedTestRunning) return;
        speedTestRunning = true;

        btnSpeedTest.disabled = true;
        btnSpeedTest.classList.add('opacity-70', 'cursor-not-allowed');
        btnSpeedLabel.textContent = 'Testando Conexão...';
        speedBadge.textContent = 'Ping...';
        speedBadge.className = 'text-[11px] font-mono px-2 py-0.5 rounded bg-[#A855F7]/20 text-[#A855F7]';

        metricPing.textContent = '...';
        metricDown.textContent = '...';
        metricUp.textContent = '...';

        const targetPing = Math.floor(4 + Math.random() * 7);
        const targetDown = Number((480 + Math.random() * 240).toFixed(1));
        const targetUp = Number((260 + Math.random() * 140).toFixed(1));

        const startTime = performance.now();
        const totalDuration = 4400; // 4.4 segundos total

        function animateSpeedTest(now) {
          const elapsed = now - startTime;

          if (elapsed < 600) {
            // Fase 1: Ping
            setGaugeMbps(0);
          } else if (elapsed < 2600) {
            // Fase 2: Download (2000ms)
            metricPing.textContent = `${targetPing} ms`;
            speedBadge.textContent = 'Download ↓';
            speedBadge.className = 'text-[11px] font-mono px-2 py-0.5 rounded bg-[#A855F7]/20 text-[#A855F7]';

            const p = (elapsed - 600) / 2000;
            const easeOut = 1 - Math.pow(1 - p, 3);
            const jitter = Math.sin(elapsed * 0.03) * 14 * (1 - p * 0.5);
            const currentDown = Math.max(0, targetDown * easeOut + jitter);

            setGaugeMbps(currentDown);
            metricDown.textContent = `${currentDown.toFixed(0)} Mbps`;
          } else if (elapsed < totalDuration) {
            // Fase 3: Upload (1800ms)
            metricDown.textContent = `${targetDown.toFixed(0)} Mbps`;
            speedBadge.textContent = 'Upload ↑';
            speedBadge.className = 'text-[11px] font-mono px-2 py-0.5 rounded bg-[#10B981]/20 text-[#10B981]';

            const p = (elapsed - 2600) / (totalDuration - 2600);
            const easeOut = 1 - Math.pow(1 - p, 3);
            const jitter = Math.cos(elapsed * 0.03) * 10 * (1 - p * 0.5);
            const currentUp = Math.max(0, targetUp * easeOut + jitter);

            setGaugeMbps(currentUp);
            metricUp.textContent = `${currentUp.toFixed(0)} Mbps`;
          } else {
            // Concluído
            setGaugeMbps(targetDown);
            metricDown.textContent = `${targetDown.toFixed(0)} Mbps`;
            metricUp.textContent = `${targetUp.toFixed(0)} Mbps`;
            speedBadge.textContent = 'Concluído';
            speedBadge.className = 'text-[11px] font-mono px-2 py-0.5 rounded bg-[#10B981]/20 text-[#10B981]';

            btnSpeedTest.disabled = false;
            btnSpeedTest.classList.remove('opacity-70', 'cursor-not-allowed');
            btnSpeedLabel.textContent = 'Testar Novamente';
            speedTestRunning = false;
            return;
          }

          requestAnimationFrame(animateSpeedTest);
        }

        requestAnimationFrame(animateSpeedTest);
      });

      /* ---------------- 5. DEPLOY WINGET & OFFICE (ANIMAÇÃO ECLIPSE) ---------------- */
      const RING_CIRCUMFERENCE = 150.8; // 2 * PI * 24

      function runDeployCard(card) {
        if (card.dataset.installing === 'true') return;
        card.dataset.installing = 'true';

        const ring = card.querySelector('.progress-ring');
        const badge = card.querySelector('.icon-badge');
        const btn = card.querySelector('.deploy-btn');
        const svgEl = ring.closest('svg');

        // Guarda ícone original se ainda não salvo
        if (!badge.dataset.originalHtml) {
          badge.dataset.originalHtml = badge.innerHTML;
        }

        // Reseta visual para início da instalação estilo eclipse
        badge.innerHTML = badge.dataset.originalHtml;
        badge.classList.remove('bg-[#10B981]', 'text-white', 'emerald-pulse');
        badge.classList.add('bg-[#242833]');
        ring.setAttribute('stroke', '#E53935');
        ring.style.strokeDashoffset = RING_CIRCUMFERENCE;
        svgEl.classList.add('eclipse-orbit');

        btn.textContent = 'Instalando...';
        btn.disabled = true;
        btn.classList.add('opacity-75');

        let progress = 0;
        const stepInterval = setInterval(() => {
          progress += Math.floor(8 + Math.random() * 14);
          if (progress >= 100) {
            progress = 100;
            clearInterval(stepInterval);

            // Concluído: anel verde esmeralda pulsante
            svgEl.classList.remove('eclipse-orbit');
            ring.style.strokeDashoffset = 0;
            ring.setAttribute('stroke', '#10B981');

            badge.classList.remove('bg-[#242833]', 'text-gray-200', 'text-[#E53935]');
            badge.classList.add('bg-[#10B981]', 'text-white', 'emerald-pulse');
            badge.innerHTML = `
              <svg class="w-5 h-5" fill="none" stroke="currentColor" stroke-width="2.5" viewBox="0 0 24 24">
                <path stroke-linecap="round" stroke-linejoin="round" d="M5 13l4 4L19 7" />
              </svg>
            `;

            btn.textContent = 'Instalado ✓';
            btn.disabled = false;
            btn.classList.remove('opacity-75', 'bg-[#2D3240]', 'bg-[#E53935]');
            btn.classList.add('bg-[#10B981]/20', 'text-[#10B981]', 'border', 'border-[#10B981]/40');
            card.dataset.installing = 'false';
          } else {
            const offset = RING_CIRCUMFERENCE * (1 - progress / 100);
            ring.style.strokeDashoffset = offset.toFixed(1);
          }
        }, 160);
      }

      document.querySelectorAll('.deploy-card').forEach((card) => {
        const btn = card.querySelector('.deploy-btn');
        btn.addEventListener('click', () => runDeployCard(card));
      });

      // Botão "Instalar Pacote Bancada" (instala todos em cascata)
      const btnInstallAll = document.getElementById('btn-install-all');
      btnInstallAll.addEventListener('click', () => {
        const cards = document.querySelectorAll('#winget-grid .deploy-card');
        cards.forEach((card, idx) => {
          setTimeout(() => runDeployCard(card), idx * 280);
        });
      });

      /* ---------------- 6. MANUTENÇÃO (PROGRESSO CIRCULAR) ---------------- */
      const MAINT_CIRCUMFERENCE = 125.6; // 2 * PI * 20

      document.querySelectorAll('.maint-card').forEach((card) => {
        const btn = card.querySelector('.maint-btn');
        const ring = card.querySelector('.maint-ring');
        const pctLabel = card.querySelector('.maint-pct');
        const desc = card.querySelector('.maint-desc');
        const originalCode = pctLabel.textContent;
        const originalDesc = desc.textContent;

        btn.addEventListener('click', () => {
          if (card.dataset.running === 'true') return;
          card.dataset.running = 'true';

          btn.disabled = true;
          btn.textContent = 'Rodando...';
          btn.classList.add('opacity-70');
          ring.setAttribute('stroke', '#A855F7');
          desc.textContent = 'Executando rotina em segundo plano...';
          desc.classList.remove('text-[#10B981]');

          let pct = 0;
          const timer = setInterval(() => {
            pct += Math.floor(9 + Math.random() * 15);
            if (pct >= 100) {
              pct = 100;
              clearInterval(timer);

              ring.style.strokeDashoffset = 0;
              ring.setAttribute('stroke', '#10B981');
              pctLabel.textContent = '100%';
              pctLabel.classList.add('text-[#10B981]');
              desc.textContent = 'Concluído com sucesso — Nenhum erro pendente.';
              desc.classList.add('text-[#10B981]');

              btn.textContent = 'Refazer';
              btn.disabled = false;
              btn.classList.remove('opacity-70');
              card.dataset.running = 'false';

              setTimeout(() => {
                if (card.dataset.running !== 'true') {
                  pctLabel.textContent = originalCode;
                  pctLabel.classList.remove('text-[#10B981]');
                  desc.textContent = originalDesc;
                }
              }, 4500);
            } else {
              ring.style.strokeDashoffset = (MAINT_CIRCUMFERENCE * (1 - pct / 100)).toFixed(1);
              pctLabel.textContent = `${pct}%`;
            }
          }, 150);
        });
      });

      /* ---------------- 7. REDE & RADAR INTELIGENTE ---------------- */
      const btnNetDoctor = document.getElementById('btn-netdoctor');
      const btnNetDoctorText = document.getElementById('btn-netdoctor-text');
      const netDoctorFeedback = document.getElementById('netdoctor-feedback');
      const ndDots = document.querySelectorAll('.nd-dot');

      btnNetDoctor.addEventListener('click', () => {
        btnNetDoctor.disabled = true;
        btnNetDoctorText.textContent = 'Aplicando Políticas...';
        netDoctorFeedback.textContent = 'Configurando SMB2 e descoberta de rede...';

        setTimeout(() => {
          ndDots.forEach((dot) => {
            dot.classList.remove('bg-amber-400');
            dot.classList.add('bg-[#10B981]');
          });

          btnNetDoctor.classList.remove('bg-[#E53935]', 'hover:bg-[#F44336]');
          btnNetDoctor.classList.add('bg-[#10B981]', 'hover:bg-emerald-600');
          btnNetDoctorText.textContent = 'Pastas Liberadas ✓';
          netDoctorFeedback.textContent = 'Compartilhamento e descoberta ativados com sucesso!';
          netDoctorFeedback.classList.add('text-[#10B981]');
          btnNetDoctor.disabled = false;
        }, 850);
      });

      // Radar Inteligente LAN — Varredura simulada
      const btnScanLan = document.getElementById('btn-scan-lan');
      const radarDeviceList = document.getElementById('radar-device-list');
      const radarSummary = document.getElementById('radar-summary');

      const sampleScans = [
        [
          { ip: '192.168.1.1', host: 'Gateway-MikroTik', tag: '(Router)', tagClass: 'text-gray-500', ms: '1 ms', highlight: true },
          { ip: '192.168.1.14', host: 'BANCADA-TECNICA', tag: '(Este PC)', tagClass: 'text-[#A855F7]', ms: '<1 ms' },
          { ip: '192.168.1.22', host: 'SERVIDOR-ARQUIVOS', tag: '(SMB)', tagClass: 'text-gray-500', ms: '2 ms' },
          { ip: '192.168.1.45', host: 'EPSON-L3250-NET', tag: '(Printer)', tagClass: 'text-gray-500', ms: '4 ms' },
          { ip: '192.168.1.89', host: 'NOTEBOOK-CLIENTE', tag: '(Wi-Fi)', tagClass: 'text-gray-500', ms: '6 ms' },
        ],
        [
          { ip: '192.168.1.1', host: 'Gateway-MikroTik', tag: '(Router)', tagClass: 'text-gray-500', ms: '1 ms', highlight: true },
          { ip: '192.168.1.14', host: 'BANCADA-TECNICA', tag: '(Este PC)', tagClass: 'text-[#A855F7]', ms: '<1 ms' },
          { ip: '192.168.1.31', host: 'NAS-BACKUP-01', tag: '(Storage)', tagClass: 'text-gray-500', ms: '1 ms' },
          { ip: '192.168.1.52', host: 'DESKTOP-FINANCEIRO', tag: '(LAN)', tagClass: 'text-gray-500', ms: '2 ms' },
          { ip: '192.168.1.104', host: 'DVR-CAMERAS-HD', tag: '(RTSP)', tagClass: 'text-gray-500', ms: '3 ms' },
          { ip: '192.168.1.118', host: 'SMARTPHONE-TEC', tag: '(Wi-Fi)', tagClass: 'text-gray-500', ms: '9 ms' },
        ]
      ];

      let scanToggle = 0;

      btnScanLan.addEventListener('click', () => {
        btnScanLan.disabled = true;
        btnScanLan.textContent = 'Escaneando...';
        radarSummary.textContent = 'Disparando pacotes ARP paralelos na sub-rede...';

        setTimeout(() => {
          scanToggle = (scanToggle + 1) % sampleScans.length;
          const devices = sampleScans[scanToggle];

          radarDeviceList.innerHTML = devices
            .map(
              (d) => `
              <tr>
                <td class="py-2.5 pr-3 ${d.highlight ? 'text-[#10B981]' : 'text-white'}">${d.ip}</td>
                <td class="py-2.5 px-3 text-gray-200">${d.host} <span class="${d.tagClass}">${d.tag}</span></td>
                <td class="py-2.5 pl-3 text-right text-gray-300">${d.ms}</td>
              </tr>
            `
            )
            .join('');

          const elapsedSec = (0.34 + Math.random() * 0.18).toFixed(2);
          radarSummary.textContent = `${devices.length} hosts ativos encontrados em ${elapsedSec}s`;
          btnScanLan.textContent = 'Atualizar Varredura';
          btnScanLan.disabled = false;
        }, 650);
      });
    });
  </script>
</body>
</html>
```PRONTO v1.5
