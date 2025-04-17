# Project Setup for Test-Comp 2025 Environment

Este README documenta os passos para configurar o ambiente de benchmarking da Test-Comp 2025 para sua ferramenta.

## 1. Clonar o repositório principal

```bash
git clone <URL-do-seu-repo> nome-do-repo
cd nome-do-repo
```

## 2. Adicionar submódulos Git em `tools/`

```bash
git submodule add https://github.com/sosy-lab/benchexec.git tools/benchexec
git submodule add https://gitlab.com/sosy-lab/benchmarking/fm-tools.git tools/fm-tools
git submodule add https://gitlab.com/sosy-lab/test-comp/bench-defs.git tools/bench-defs
```

## 3. Inicializar e buscar submódulos

```bash
git submodule update --init --recursive
```

## 4. Selecionar as tags apropriadas

### BenchExec

A versão 3.27 do BenchExec suporta Test-Comp 2025 (inclui *tool-info* modules atualizados) ([zenodo.org](https://zenodo.org/records/14208652?utm_source=chatgpt.com)).

```bash
cd tools/benchexec
git fetch --tags
git checkout tags/3.27 -b local-3.27
cd ../..
```

### FM-Tools

Use a tag `testcomp25`, contendo metadados atualizados para SV-COMP 2025 e Test-Comp 2025 ([gitlab.com](https://gitlab.com/sosy-lab/benchmarking/fm-tools/-/tags)).

```bash
cd tools/fm-tools
git fetch --tags
git checkout tags/testcomp25 -b local-testcomp25
cd ../..
```

### Bench-Defs

Use a tag `testcomp25`, com as definições de benchmark da competição ([gitlab.com](https://gitlab.com/sosy-lab/test-comp/bench-defs/-/tags)).

```bash
cd tools/bench-defs
git fetch --tags
git checkout tags/testcomp25 -b local-testcomp25
cd ../..
```

## 5. Executar o BenchExec

Com tudo configurado, execute um dos benchmarks para verificar o ambiente:

```bash
benchexec tools/bench-defs/benchmark-defs/symbiotic.xml
```

A saída estará em `results/`, com relatórios HTML e CSV.

---

Agora você tem um ambiente idêntico ao usado na Test‑Comp 2025 para validar o funcionamento da sua ferramenta.

