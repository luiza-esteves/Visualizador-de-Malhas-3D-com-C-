# Define a versão mínima e o padrão C++
cmake_minimum_required(VERSION 3.18)
project(MeshViewer LANGUAGES C CXX)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED True)

# Coleta recursiva dos fontes em src/ (evita quebrar quando arquivos são adicionados)
file(GLOB_RECURSE SOURCE_FILES CONFIGURE_DEPENDS ${CMAKE_SOURCE_DIR}/src/*.cpp ${CMAKE_SOURCE_DIR}/src/*.c)

# Compila o GLAD local: prefer dep/glad/src/glad.c, senão usa src/glad.c quando presente
if(EXISTS ${CMAKE_SOURCE_DIR}/dep/glad/src/glad.c)
    list(APPEND SOURCE_FILES ${CMAKE_SOURCE_DIR}/dep/glad/src/glad.c)
elseif(EXISTS ${CMAKE_SOURCE_DIR}/src/glad.c)
    list(APPEND SOURCE_FILES ${CMAKE_SOURCE_DIR}/src/glad.c)
else()
    message(WARNING "glad.c not found in dep/glad/src/ nor src/ — ensure GLAD source is available or add it to the project.")
endif()

# --- 1. CONFIGURAÇÃO DE INCLUDES ---

# Inclui os headers do projeto e dependências locais
include_directories(${CMAKE_SOURCE_DIR}/src)
include_directories(${CMAKE_SOURCE_DIR}/dep/glad/include)


# --- 2. ENCONTRAR E VINCULAR LIBS DE SISTEMA (GLFW, OpenGL, Assimp) ---

# GLFW via pkg-config
find_package(PkgConfig REQUIRED)
pkg_check_modules(GLFW REQUIRED glfw3)

# OpenGL
find_package(OpenGL REQUIRED)

# Assimp (Importador de modelos) - pode ser REQUIRED ou OPTIONAL dependendo do ambiente
find_package(Assimp QUIET)


# --- 3. CRIAR O EXECUTÁVEL E VINCULAR AS DEPENDÊNCIAS ---

add_executable(MeshViewer ${SOURCE_FILES})

# Certifica que os includes do GLFW (encontrados pelo pkg-config) estão disponíveis
target_include_directories(MeshViewer PRIVATE ${GLFW_INCLUDE_DIRS})

# Vincula as bibliotecas necessárias
target_link_libraries(MeshViewer
    PRIVATE
        ${OPENGL_LIBRARIES}
        ${GLFW_LIBRARIES}
        m
)

# Se o Assimp for encontrado, linka-o; caso contrário, continua sem ele
if(Assimp_FOUND)
    message(STATUS "Assimp found: ${Assimp_VERSION}")
    target_link_libraries(MeshViewer PRIVATE Assimp::Assimp)
else()
    message(WARNING "Assimp not found — model import features will be disabled.")
endif()