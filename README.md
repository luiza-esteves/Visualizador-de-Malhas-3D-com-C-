# 🚀 Projeto: Visualizador e Processador Básico de Malhas 3D (MeshViewer C++)

## Visão Geral do Projeto

Este projeto é uma aplicação de Computação Gráfica construída do zero em C++ e OpenGL. Seu objetivo principal é demonstrar o domínio dos fundamentos de matemática vetorial, programação C++ orientada a objetos (POO) e a implementação da *pipeline* de renderização gráfica.

O sistema é capaz de carregar modelos 3D, gerenciá-los na memória da GPU e permitir a navegação interativa no ambiente 3D.

## 🛠️ Tecnologias Utilizadas

| Componente | Tecnologia | Foco Técnico |
| :--- | :--- | :--- |
| **Linguagem** | C++ (C++17/20) | Performance, Gerenciamento de Memória, POO. |
| **API Gráfica** | OpenGL (Core Profile 3.3+) | Programação da GPU, Shaders (GLSL). |
| **Matemática** | GLM (OpenGL Mathematics) | Álgebra Linear (Matrizes de Transformação), Geometria 3D. |
| **Janela & Input**| GLFW | Criação do Contexto Gráfico e Tratamento de Eventos. |
| **Carregamento GL** | GLAD / GLEW | Gerenciamento das Extensões do OpenGL. |
| **Modelos 3D** | `.OBJ` | File I/O e *Parsing* de formatos de malhas. |

## 📐 Estrutura do Código e Módulos Principais

O projeto é dividido nos seguintes módulos, cada um com responsabilidades bem definidas:

### 1. Módulo Core: `Mesh` e Gerenciamento de Memória

A classe `Mesh` (Malha) é responsável por estruturar os dados geométricos e interagir diretamente com a GPU.

* **`struct Vertex`**: Define a estrutura fundamental (posição, normal, coordenadas de textura).
* **`setupMesh()`**: Função essencial que encapsula a inicialização do OpenGL:
    * **VBO (Vertex Buffer Object):** Armazena os dados dos vértices na GPU.
    * **EBO (Element Buffer Object) / IBO (Index Buffer Object):** Armazena os índices para reuso de vértices, otimizando a memória.
    * **VAO (Vertex Array Object):** Armazena a configuração dos VBOs, permitindo a troca rápida entre diferentes malhas.
* **`OBJLoader`**: (Implementação manual) Classe para leitura e *parsing* do formato `.obj`, convertendo-o em uma estrutura de malha eficiente.

**Conceitos Chave Demonstrados:** Topologia de Malhas, Gerenciamento de Memória de Alto Desempenho (CPU ↔ GPU), Abstração de Objetos OpenGL.

### 2. Módulo de Renderização: `Shader` e Pipeline Gráfica

A classe `Shader` lida com a compilação e linkagem dos programas que rodam na GPU.

* **GLSL Shaders**: Desenvolvimento de código GLSL (OpenGL Shading Language) para:
    * **Vertex Shader**: Aplicação das transformações geométricas (matrizes **Model, View, Projection - MVP**) para converter coordenadas de 3D para 2D (espaço de tela).
    * **Fragment Shader**: Cálculo de cor final para cada pixel (rasterização).
* **Uniforms**: Passagem de dados dinâmicos da CPU (C++) para a GPU (GLSL), como as matrizes de câmera e parâmetros de iluminação.

**Conceitos Chave Demonstrados:** Programação do Pipeline Gráfico (Programável), Álgebra Linear Aplicada a Transformações 3D.

### 3. Módulo Interativo: `Camera` e Input

Este módulo implementa a lógica de navegação e visualização no ambiente 3D.

* **Classe `Camera`**: Gerencia o estado da câmera (posição, rotação - *pitch/yaw*). Calcula a **View Matrix** (inversa da matriz de transformação da câmera) e a **Projection Matrix** (projeção perspectiva).
* **Tratamento de Input (GLFW)**: Implementação de *callbacks* para:
    * Movimentação livre no espaço 3D (FPS-style: W/A/S/D).
    * Controle de rotação da câmera (mouse look).
    * Alternância do modo de renderização entre Sólido (`GL_FILL`) e Wireframe (`GL_LINE`).

**Conceitos Chave Demonstrados:** Free-Look Camera Implementation, Matrizes View/Projection, Projeção Perspectiva.

## 📈 Próximos Passos (Evolução do Projeto)

O projeto está estruturado para ser modular, permitindo a fácil adição de funcionalidades avançadas, como:

1.  **Iluminação Avançada:** Implementação do modelo de iluminação **Phong** (ambiente, difusa, especular) e adição de diferentes fontes de luz (direcional, pontual).
2.  **Mapeamento de Texturas:** Suporte para carregar e aplicar texturas (.png, .jpg) para adicionar detalhes visuais aos modelos.
3.  **Processamento de Geometria:** Adicionar um módulo `MeshProcessor` para implementar algoritmos como:
    * Recálculo de Normais da Superfície.
    * Simplificação de Malhas (Mesh Decimation).
    * Cálculo de Bounding Boxes (AABB) para otimização de renderização.