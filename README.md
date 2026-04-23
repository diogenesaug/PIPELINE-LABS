# PIPELINE-LABS
# LAB 01
name: "Estrutura de Diretorios e Arquivos"

on:
  push:
    branches: [main]
  workflow_dispatch:

env:
  NODE_VERSION: "18"

jobs:
  demonstrar-estrutura:
    name: "Demonstrar Estrutura Básica"
    runs-on: ubuntu-latest
    
    steps:
      - name: "Checkout do código"
        uses: actions/checkout@v5

      - name: "Mostrar estrutura .github/workflows"
        run: |
          echo "=== ESTRUTURA DO DIRETÓRIO .github/workflows ==="
          ls -la .github/workflows
          echo ""
          echo "Total de arquivos e diretórios: $(ls -1 .github/workflows/*.yaml 2>/dev/null | wc -l)"

      - name: "Elementos obrigatórios do workflow"
        run: |
          echo "=== ELEMENTOS OBRIGATÓRIOS ==="
          echo "1. name: Nome do workflow"
          echo "2. on: Evento(s) que disparam o workflow"
          echo "3. jobs: Conjunto de tarefas a serem executadas"
          echo "4. runs-on: Ambiente de execução"
          echo "5. steps: Passos a serem executados dentro de cada job"

      - name: "Variáveis e contextos"
        run: |
          echo "=== VARIÁVEIS e CONTEXTOS ==="
          echo "NODE_VERSION: ${{ env.NODE_VERSION }}"
          echo "GITHUB_WORKSPACE: ${{ github.workspace }}"
          echo "GITHUB_REPOSITORY: ${{ github.repository }}"
          echo "GITHUB_REF: ${{ github.ref }}"
          echo "GITHUB_SHA: ${{ github.sha }}"
          echo "GITHUB_ACTOR: ${{ github.actor }}"
          echo "GITHUB_EVENT_NAME: ${{ github.event_name }}"
          echo "GITHUB_EVENT_PATH: ${{ github.event_path }}"

          ========== Arquivo 2 =================

          on:
  schedule:
    # Executa as 00:00 (UTC) todos os dias, o que corresponde a 20:00 no horário de Brasília
    - cron: '07 00 * * *' # Runs every day at midnight
  workflow_dispatch: # Permite execução manual do workflow

jobs:
  scheduler-job:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout código
        uses: actions/checkout@v5

      - name: Informação do Schedule
        run: |
          echo "Workflow disparado por schedule"
          echo "Data e hora atual: $(date)"
          echo "Hora UTC: $(date -u)"
          echo "Timezone local: $(date +%Z)"
      
      
      - name: Relatório de dependências    
        run: |
          echo "RELATÓRIO DE DEPENDÊNCIAS"
          echo "-----------------------"
          echo "Total de commits: $(git rev-list --all --count)"
          echo "Contribuidores únicos: $(git shortlog -s -n | wc -l)"
          echo "Último commit: $(git log -1 --pretty=format:'%h - %s (%an, %ar)')"

          
name: Matrix Strategy

on:
  push:
    branches: [main]
  workflow_dispatch:

jobs:
  # Job com matrix para múltiplos ambientes
  test-matrix:
    runs-on: ${{ matrix.os }}
    
    strategy:
      fail-fast: false # Continua rodando os outros jobs mesmo se um falhar
      matrix:
        os: [ubuntu-latest, windows-latest, macos-latest]
        node-version: [20, 22, 24]
        include:
          - os: ubuntu-latest
            node-version: 20
            experimental: true
        exclude:
          - os: windows-latest
            node-version: 24
    steps:
    - name: Checkout código
      uses: actions/checkout@v6
    - name: Informações da matriz
      run: |
        echo "OS: ${{ matrix.os }}"
        echo "Node: ${{ matrix.node-version }}"
        echo "Experimental: ${{ matrix.experimental }}"
       
    - name: Setup Node.js
      uses: actions/setup-node@v4
      with:
        node-version: ${{ matrix.node-version }}
      
    - name: Verificar versões
      run: |
        echo "Node version: $(node --version)"
        echo "NPM version: $(npm --version)"
    - name: Executar testes específicos do OS
      run: |
        echo "Executando testes no ${{ matrix.os }} com Node ${{ matrix.node-version }}..."
        sleep 5
        echo "Testes concluídos no ${{ matrix.os }} com Node ${{ matrix.node-version }}."
    - name: Verificar experimental
      if: ${{ matrix.experimental == 'true' }}
      run: |
        echo "Executando testes experimentais..."
        sleep 5
        echo "Testes experimentais concluídos."
    #  Job coletar resultados da matrix
  collect-results:
    runs-on: ubuntu-latest
    needs: test-matrix
    steps:
    - name: Coletar resultados
      run: |
        echo "Coletando resultados de todos os jobs da matrix..."
        sleep 5
        echo "Todos os testes da matrix foram executados."
        