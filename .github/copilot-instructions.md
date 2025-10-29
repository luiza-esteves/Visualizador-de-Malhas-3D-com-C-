## Instruções rápidas para agentes de código

Objetivo: ajudar um agente a ser imediatamente produtivo neste repositório C++/OpenGL.

- Big picture: é um visualizador de malhas 3D em C++ usando OpenGL (Core Profile). O projeto separa responsabilidades em módulos: carregamento/representação de malhas (Mesh / OBJ loader), renderização (Shader, GLSL), e input/câmera (Camera + callbacks). Veja o `README.md` para o overview.

- Build: o projeto usa CMake (arquivo raiz `CMakeLists.txt`). Observações importantes:
  - `CMakeLists.txt` chama `pkg_check_modules(GLFW REQUIRED glfw3)` — o sistema espera encontrar GLFW via pkg-config.
  - O `add_executable` lista `src/main.cpp` e `src/Shader.cpp` (verifique que os fontes realmente existem antes de tentar compilar).
  - GLAD está incluído em `dep/glad/` (headers em `dep/glad/include/glad/` e fonte em `dep/glad/src/glad.c`).
  - Shaders GLSL ficam em `shaders/` (arquivos de vertex/fragment). Esses arquivos são carregados em tempo de execução pelo código `Shader`.

- Comandos de build (úteis para agentes):
  - Configurar + build (diretório `build/`):

    cmake -S . -B build
    cmake --build build -- -j$(nproc)

  - Se houver problemas com GLFW, inspecione `pkg-config --cflags --libs glfw3` no sistema alvo.

- Convenções e padrões de código detectáveis aqui:
  - Estruturas típicas de OpenGL: `VAO`, `VBO`, `EBO` (o README descreve `setupMesh()` que configura esses buffers). Procure por funções/nomes similares ao manipular malhas.
  - Classe `Shader` é responsável por compilar/linkar programas GLSL e por setar `uniforms` (pattern: carregar código GLSL de `shaders/`, compilar, checar log de compilação/linkagem e expor setters para uniforms).
  - `OBJ` loader é implementado manualmente (parsing no código) — modificações aqui exigem cuidado com índices e normalização de dados.

- Integrações externas e dependências locais:
  - `dep/glad/` contém GLAD (incluir/compilar como parte do projeto). Não há um submódulo automático; agente deve preservar esse layout.
  - GLFW é esperado como biblioteca do sistema (pkg-config). GLM é mencionado no README como header-only (caso seja usado, pode estar ausente no repositório e esperado como dependência do sistema ou submodule).

- Dicas práticas para um agente que edita/implementa recursos:
  - Se for adicionar novos sources, atualize `CMakeLists.txt` em `add_executable(...)` ou converta a lista de .cpp em `file(GLOB ...)` com cuidado (prefira listar explicitamente para clareza).
  - Para diagnosticar builds, inspecione `build/CMakeFiles/CMakeOutput.log` e use o `pkg-config` para verificar flags de link/compile do GLFW.
  - Preserve o padrão de abstração: não misture lógica de parsing (OBJ) diretamente com código de render — mantenha separação Mesh/Renderer/Shader.

- Arquivos chave para inspecionar (exemplos reais neste repositório):
  - `CMakeLists.txt` — requisitos de build e referência a `src/main.cpp` e `src/Shader.cpp`.
  - `README.md` — visão geral do design (Mesh, Shader, Camera) e planos futuros.
  - `dep/glad/include/glad/glad.h` e `dep/glad/src/glad.c` — integração local do cargador GL.
  - `shaders/` — local onde shaders GLSL devem viver (vertex/fragment), carregados pelo `Shader` C++.

Se alguma seção estiver ambígua ou faltar arquivos (por exemplo `src/*.cpp` referenciados no CMake), pergunte ao mantenedor se o repositório deve incluir os fontes faltantes ou se o CMake precisa ser atualizado. Peça feedback sobre este rascunho para iterarmos.
