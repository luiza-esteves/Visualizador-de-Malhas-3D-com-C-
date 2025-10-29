## Configuração inicial do projeto — passo a passo

Este documento descreve como a configuração atual do projeto funciona e como compilar e rodar

### Visão rápida
Esta configuração usa CMake para descobrir fontes em `src/`, compilar o GLAD local se tiver e ligar com OpenGL/GLFW.  O Assimp é detectado se estiver instalado, mas não é obrigatório. O projeto suporta arquivos C (.c) e C++ (.cpp).

### Estrutura relevante de arquivos
- `CMakeLists.txt` — configuração de build
- `src/` — código-fonte do projeto 
- `dep/` — dependências adicionadas ao projeto

### Pré-requisitos (sistema)
Em Debian/Ubuntu instale:

```bash
sudo apt update
sudo apt install build-essential cmake pkg-config libglfw3-dev libglm-dev
sudo apt install libassimp-dev #(opicional)
```

Verifique também que `pkg-config` encontra `glfw3`:

```bash
pkg-config --cflags --libs glfw3
```

### Passo a passo: configurar e compilar
1. No diretório raiz do repositório execute:

```bash
cmake -S . -B build
cmake --build build -- -j$(nproc)
```

2. O esperado durante a configuração/compilação:
- CMake coleta automaticamente todos os `.cpp` e `.c` em `src/`.
- Se existir `dep/glad/src/glad.c` ele será incluído; caso contrário, o projeto usará `src/glad.c`.
- O projeto compila ambos arquivos C e C++ (projeto configurado com `LANGUAGES C CXX`).
- Se `Assimp` não estiver presente, CMake exibirá uma WARNING e continuará (recursos de import podem ficar desabilitados).

3. Rodar o executável gerado:

```bash
./build/MeshViewer
```

### O que o `CMakeLists.txt` faz (detalhes técnicos)
- `file(GLOB_RECURSE SOURCE_FILES CONFIGURE_DEPENDS ${CMAKE_SOURCE_DIR}/src/*.cpp ${CMAKE_SOURCE_DIR}/src/*.c)`
	- Coleta recursiva de fontes para evitar esquecer de adicionar novos .cpp manualmente.
- Lógica para `glad.c`:
	- Prioriza `dep/glad/src/glad.c`.
	- Emite warning se nenhum `glad.c` for encontrado.
- Configura includes:
	- `dep/glad/include` é adicionado para resolver `#include <glad/glad.h>`.
	- Também adiciona `src/` como include path.
- Descobre dependências do sistema:
	- `pkg_check_modules(GLFW REQUIRED glfw3)` — usa pkg-config para encontrar flags e bibliotecas do GLFW.
	- `find_package(OpenGL REQUIRED)` — busca OpenGL.
	- `find_package(Assimp QUIET)` — procura Assimp, mas não falha se ausente.
- Linkagem:
	- Linka `${OPENGL_LIBRARIES}`, `${GLFW_LIBRARIES}` e `m` (libm).
	- Se Assimp for encontrado (`Assimp_FOUND`), linka `Assimp::Assimp`.


### Como estender o projeto (boas práticas específicas deste repositório)
- Ao adicionar novos arquivos `.cpp` coloque-os em `src/` — o CMake irá detectá-los automaticamente.
- Se adicionar novos headers em `dep/` (por exemplo GLM local), atualize `include_directories` em `CMakeLists.txt` se necessário.
- Para substituir o stub `src/main.cpp`:
	- Implemente a classe `Shader` em `src/Shader.cpp` e a sua header `include/Shader.h` (ou `src/Shader.h`).
	- Remova placeholders; garanta que não haja múltiplas funções `main`.
- Se preferir controle explícito sobre os arquivos fonte (versus glob), substitua `file(GLOB_RECURSE ...)` por uma lista explícita no `CMakeLists.txt` para maior previsibilidade.

### Checklist rápido (para copiar/colar)
- Instalar dependências de sistema (ex.: Debian/Ubuntu):
```bash
sudo apt update
sudo apt install build-essential cmake pkg-config libglfw3-dev libglm-dev libassimp-dev
```
- Configurar e compilar:
```bash
cmake -S . -B build
cmake --build build -- -j$(nproc)
```
- Rodar:
```bash
./build/MeshViewer
```