## 🚀 PRONTO v1.5 — Instalação Direta de Sistemas & Nova Interface

### ✨ Novidades
- **Instalação Direta de Windows (por Partição):** Instale Windows 7, 8, 10 ou 11 diretamente em HDs/SSDs externos via USB sem precisar de pendrive bootável.
  - Formata **apenas a partição selecionada**, preservando as demais partições e dados do disco (com trava de segurança que oculta a unidade `C:\`).
  - Detecção automática das edições disponíveis na ISO (`install.wim` e `install.esd`).
  - Criação opcional de usuário/senha local via `unattend.xml` e **bypass automático de TPM 2.0 / Secure Boot** para Windows 11.
  - Gravação automática de boot **UEFI (GPT)** (localizando a partição EFI existente) ou **Legacy (MBR)**.
- **Gravação de ISO Linux (Modo RAW/DD):** Suporte a ISOs Linux gravadas diretamente em discos físicos externos, criando mídias bootáveis/Live prontas para uso.
- **Card com Animação Líquida (60 FPS):** Novo indicador vetorial fluido em tempo real com porcentagem de progresso e mudança dinâmica de cor por estado (Windows, Linux, Concluído e Erro).
- **Modal Escuro Personalizado:** Substituição dos alertas padrão do Windows (`MessageBox`) por modais escuros com efeito blur no fundo para confirmações de segurança e avisos.

### 🎨 Interface & Reorganização
- Aba **Instalação & Manutenção** totalmente centralizada e redesenhada.
- Remoção dos módulos antigos de Benchmark de Disco e Teste de Memória RAM para manter o fluxo focado em implantação e manutenção rápida.
- Seletor circular estilo **Eclipse** para alternar entre modo Windows e Linux.

### 🛠️ Correções
- **Detecção de HDs/Docks USB:** Corrigida a listagem de unidades externas para reconhecer cases e docks que reportam barramento `SATA` ou `Unknown`, mantendo o disco do sistema protegido.
- **Leitura de Edições DISM (PT-BR):** Corrigida a leitura de índices de imagens em sistemas em português (acentuação OEM).
- **Erro 123 do DISM:** Corrigida a sintaxe do parâmetro `/ApplyDir` durante a aplicação da imagem na partição alvo.
