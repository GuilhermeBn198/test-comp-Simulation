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

## 5. Verificar dependências

Você precisa garantir que o sistema tenha os requisitos básicos para rodar BenchExec e os scripts auxiliares.

### 📦 Requisitos do sistema

- Python 3.8+
- pip, virtualenv (opcional, mas recomendado)
- pacotes do sistema: libxml2, libxslt, zlib, time, cgroups, etc.

### **Setup no Ubuntu/Debian**

```bash
sudo apt update
sudo apt install python3 python3-pip python3-venv \
                 pkg-config libsystemd-dev \
                 libxml2 libxml2-utils libxslt1-dev \
                 zlib1g-dev cgroup-tools
```

## 6. Instalar o BenchExec (modo dev)

### 🛑 Atenção

Não crie o ambiente virtual dentro da pasta tools/ ou de qualquer submodule (como tools/benchexec). Isso pode sobrescrever os arquivos do submodule e causar perda de dados. Prefira criar o venv na raiz do projeto ou em um diretório separado.

### Você pode usar um ambiente virtual

```bash
python3 -m venv venv
source venv/bin/activate

cd tools/benchexec
pip install .
```

### Opcionalmente, você pode instalar dependências extras

```bash
pip install .[gui]
```

Isso instala o benchexec, o runexec e o compare na linha de comando.

## 7. Validar o ambiente de execução

Antes de tentar rodar qualquer benchmark, é bom testar se o BenchExec funciona corretamente:

```bash
which benchexec   # deve apontar para seu venv/bin/benchexec
benchexec --help
```

Você também pode validar o sistema:

```bash
runexec --version
runexec --no-container /bin/true
```

## #⚠️ Observação sobre Cgroups

Se aparecerem avisos sobre cgroup subsystem ou erro relacionado a freezer, o BenchExec está sem permissão para controlar os recursos do processo. Você pode contornar isso usando o systemd-run:

```bash
systemd-run --user --scope --slice=benchexec -p Delegate=yes runexec --no-container /bin/true
```

Ou instalar a biblioteca pystemd:

```bash
pip install pystemd
```

## 8. Explorar os XMLs de benchmark disponíveis

Dentro de tools/bench-defs/benchmark-defs/, você verá vários arquivos .xml. Cada um representa uma categoria de benchmarks com:

- paths para os binários das ferramentas
- paths para os arquivos de teste (input programs)
- limites de tempo, memória, CPUs
- paths para o tool-info (definições de como chamar sua ferramenta)

### Por exemplo

```bash
ls tools/bench-defs/benchmark-defs/*.xml
```

## 9. Conectar sua ferramenta ao BenchExec

Você precisará de:

- Um arquivo .xml de benchmark específico para sua ferramenta (você pode começar copiando e editando um já existente, como symbiotic.xml)

- Um tool-info module em Python, dentro de tools/benchexec/benchexec/tools/, que define como sua ferramenta será chamada.

### ⚙️ Exemplo: Se sua ferramenta se chama minha-tool, você pode criar:

- tools/bench-defs/benchmark-defs/minha-tool.xml
- tools/benchexec/benchexec/tools/minhatool.py

## 10. Rodar um benchmark de teste

Depois de conectar tudo, execute:

```bash
benchexec tools/bench-defs/benchmark-defs/minha-tool.xml
```

E então verifique os resultados em:

```bash
ls results/
edge results/benchmark-XYZ/index.html
``` 