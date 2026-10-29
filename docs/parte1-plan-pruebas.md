# Parte 1: preparación del proyecto

## Qué se hizo

Se creó un proyecto base en C++ con CMake y Google Test dentro de Ubuntu (WSL2). Se creó la estructura de carpetas y el archivo `CMakeLists.txt`.

### Comandos ejecutados

```bash
mkdir -p laboratorio-testing/include
mkdir -p laboratorio-testing/src
mkdir -p laboratorio-testing/tests
mkdir -p laboratorio-testing/docs
mkdir -p laboratorio-testing/.github/workflows
cd laboratorio-testing
```

### Archivo creado

`CMakeLists.txt`, en la raíz de `laboratorio-testing`. Define el estándar C++17, una opción `ENABLE_COVERAGE` para medir cobertura, la descarga de Google Test 1.12.1 con `FetchContent`, la librería `project_lib` y el ejecutable `run_tests`.

## Qué es CMake

CMake es una herramienta que genera los archivos de compilación a partir de una descripción del proyecto en `CMakeLists.txt`. En vez de escribir a mano los comandos de `g++`, se declara qué archivos forman cada librería o ejecutable y CMake se encarga del resto.

## Para qué sirve Google Test

Google Test es un framework de pruebas unitarias para C++. Aporta macros como `TEST`, `EXPECT_EQ` y `ASSERT_EQ` para escribir pruebas, un ejecutable que corre todas las pruebas y un reporte que indica cuáles pasaron y cuáles fallaron, con el valor esperado y el obtenido.

## Qué significa automatizar pruebas

Significa que las pruebas se ejecutan con un comando sin que una persona tenga que probar cada caso a mano ni revisar los resultados a ojo. El programa compara los resultados reales contra los esperados y dice si todo está bien.

## Qué significa que las pruebas sean repetibles

Que se pueden ejecutar muchas veces, en distintos momentos y en distintos ambientes, y dan el mismo resultado si el código no cambió. No dependen de datos externos, de la hora ni de valores aleatorios sin control.

## Archivos y carpetas creados

| Ruta | Para qué sirve |
|---|---|
| `include/` | Archivos de encabezado (`.h`) con las declaraciones de las funciones |
| `src/` | Implementaciones (`.cpp`) de las funciones |
| `tests/` | Pruebas unitarias con Google Test |
| `docs/` | Documentación del laboratorio en Markdown |
| `.github/workflows/` | Workflow de GitHub Actions (`testing.yml`) |
| `CMakeLists.txt` | Configuración de la compilación y de las pruebas |

## Preguntas de reflexión

**1. ¿Por qué conviene separar el código fuente de las pruebas?**
Porque el código de las pruebas no forma parte del programa final. Separarlos mantiene el código fuente limpio, permite compilar y entregar el programa sin las pruebas, y hace más fácil encontrar cada cosa.

**2. ¿Qué ventaja tiene usar CMake en un proyecto de C++?**
Se compila con los mismos comandos en cualquier máquina, maneja dependencias como Google Test de forma automática y evita escribir comandos largos de `g++` a mano. Además, es lo que usa GitHub Actions para compilar el proyecto.

**3. ¿Por qué es útil que las pruebas se puedan ejecutar con un solo comando?**
Porque se ejecutan más seguido y con menos esfuerzo. Si cuesta correrlas, se dejan de correr, y los errores se detectan tarde. Con un solo comando también es fácil automatizarlas.

**4. ¿Qué diferencia hay entre probar manualmente y probar automáticamente?**
Probar manualmente depende de que una persona ejecute el programa, ingrese datos y revise la salida, lo que es lento y propenso a olvidos. Probar automáticamente deja los casos escritos en código, se ejecutan en segundos y siempre verifican lo mismo, así que sirve para detectar si un cambio nuevo rompió algo que ya funcionaba.

---

# Parte 9: pruebas funcionales sencillas

## Requisito funcional

> El sistema debe convertir una nota numérica entre 0 y 100 a una letra de calificación. Las notas entre 90 y 100 son A, entre 80 y 89 son B, entre 70 y 79 son C, entre 60 y 69 son D, y menores a 60 son F. Si la nota está fuera del rango permitido, el sistema debe rechazarla.

## Tabla de casos funcionales

| ID | Requisito | Entrada | Resultado esperado | Tipo de caso | Prueba en `tests/test_grade_utils.cpp` |
|---|---|---:|---|---|---|
| TC-001 | Convertir nota excelente | 95 | A | Normal | `LetterGradeA` |
| TC-002 | Límite inferior de A | 90 | A | Borde | `GradeBoundaryBetweenBAndA` |
| TC-003 | Límite superior de B | 89 | B | Borde | `GradeBoundaryBetweenBAndA` |
| TC-004 | Nota inválida baja | -1 | Excepción | Inválido | `NegativeGradeThrowsException` |
| TC-005 | Nota inválida alta | 101 | Excepción | Inválido | `GradeJustAboveMaximumThrowsException` (agregada) |
| TC-006 | Límite superior del rango | 100 | A | Borde | `MaximumValidGrade` |
| TC-007 | Límite inferior del rango | 0 | F | Borde | `MinimumValidGrade` |
| TC-008 | Límite inferior de D | 60 | D | Borde | `GradeBoundaryBetweenFAndD` |

Los casos TC-006 a TC-008 no los pedía la tabla de ejemplo, pero se agregaron para cubrir los extremos del rango y un límite intermedio del requisito.

## Cuáles ya estaban cubiertos por pruebas

TC-001, TC-002, TC-003 y TC-004 ya tenían prueba (de las Partes 3 y 6), igual que TC-006, TC-007 y TC-008. Las pruebas `GradeBoundaryBetweenBAndA` y `GradeBoundaryBetweenFAndD` verifican dos valores cada una: `89` y `90`, y `59` y `60`.

## Cuáles se tuvieron que agregar

Solo TC-005. Existía una prueba para una nota inválida alta (`letter_grade(120)`), pero el caso de la tabla es `101`, el valor justo encima del límite, que es donde un error como `grade >= 100` en lugar de `grade > 100` se haría visible. Se agregó:

```cpp
TEST(GradeUtilsTest, GradeJustAboveMaximumThrowsException) {
    EXPECT_THROW(letter_grade(101), std::invalid_argument);
}
```

Después de agregarla se compiló y se ejecutó, y pasaron las 42 pruebas:

```text

[----------] Global test environment tear-down
[==========] 42 tests from 3 test suites ran. (1 ms total)
[  PASSED  ] 42 tests.
```

## Por qué estas pruebas se consideran funcionales

Porque se diseñaron a partir del requisito y no a partir del código interno de `letter_grade`. Cada caso parte de una entrada y un resultado esperado definidos por el requisito, y verifica el comportamiento visible sin importar cómo esté escrita la función por dentro. Si la función se reescribiera por completo, estos casos seguirían siendo válidos.

## Preguntas de reflexión

**1. ¿Qué diferencia hay entre una prueba unitaria y una prueba funcional?**
Una prueba unitaria verifica una pieza pequeña de código, como una función, de forma aislada. Una prueba funcional verifica que el sistema cumpla un requisito, desde el punto de vista de lo que debe hacer. En este laboratorio las pruebas de `letter_grade` pueden verse de ambas formas: son unitarias por lo que prueban, y funcionales por cómo se escogieron los casos.

**2. ¿Por qué una prueba funcional se relaciona con requisitos?**
Porque su resultado esperado sale del requisito, no del código. Así, si el código no cumple lo que se pidió, la prueba lo detecta, y cada caso se puede rastrear hasta una frase del requisito.

**3. ¿Qué significa pensar desde la perspectiva del usuario o del sistema?**
Preguntarse qué espera quien usa la función o el sistema, qué entradas puede mandar, qué respuesta debería recibir y qué debería pasar con una entrada incorrecta. Se pone la atención en el comportamiento y no en cómo está implementado.

**4. ¿Por qué la documentación de casos de prueba ayuda al equipo?**
Porque deja por escrito qué se probó, con qué entradas y qué se espera. Eso permite revisar si faltan casos, repartir el trabajo, volver a ejecutar las pruebas sin depender de la memoria de una persona y entender el comportamiento esperado del sistema.
