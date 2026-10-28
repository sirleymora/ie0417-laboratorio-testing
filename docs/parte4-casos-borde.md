# Parte 6: diseño de casos borde

## Qué se hizo

Se agregaron 13 pruebas que verifican límites y situaciones especiales, además de los casos normales de la Parte 3. Se agregaron a los tres archivos de `tests/` y se ejecutaron con `make` y `./run_tests`.

## Qué es un caso borde

Un caso borde es una entrada que está en el límite de lo que la función debe aceptar, o que es poco común pero válida: el valor mínimo o máximo permitido, el valor justo antes y justo después de un límite, el cero, una cadena vacía, un solo elemento. Es donde más errores aparecen, porque un operador mal escrito (`>` en vez de `>=`) o un error de uno de más o de menos solo se nota ahí.

## Casos borde agregados

| Archivo | Prueba | Qué verifica |
|---|---|---|
| `test_calculator.cpp` | `DivideNegativeNumbers` | `divide(-10, -2)` da `5` (dos negativos dan positivo) |
| `test_calculator.cpp` | `DividePositiveByNegative` | `divide(10, -2)` da `-5` |
| `test_calculator.cpp` | `ZeroIsEven` | `is_even(0)` es `true` |
| `test_string_utils.cpp` | `EmptyStringToUppercase` | cadena vacía da cadena vacía |
| `test_string_utils.cpp` | `EmptyStringIsPalindrome` | la cadena vacía se considera palíndromo |
| `test_string_utils.cpp` | `SingleLetterIsPalindrome` | una sola letra es palíndromo |
| `test_grade_utils.cpp` | `MinimumValidGrade` | `letter_grade(0)` da `F` (límite inferior válido) |
| `test_grade_utils.cpp` | `MaximumValidGrade` | `letter_grade(100)` da `A` (límite superior válido) |
| `test_grade_utils.cpp` | `NegativeGradeThrowsException` | `letter_grade(-1)` lanza excepción (justo debajo del límite) |
| `test_grade_utils.cpp` | `GradeBoundaryBetweenFAndD` | 59 es `F` y 60 es `D` |
| `test_grade_utils.cpp` | `GradeBoundaryBetweenDAndC` | 69 es `D` y 70 es `C` |
| `test_grade_utils.cpp` | `GradeBoundaryBetweenCAndB` | 79 es `C` y 80 es `B` |
| `test_grade_utils.cpp` | `GradeBoundaryBetweenBAndA` | 89 es `B` y 90 es `A` |

`NegativeGradeThrowsException` es a la vez un caso borde (justo debajo del mínimo) y un caso inválido (la nota debe rechazarse).

## Por qué esos casos son importantes

- **Los límites de las letras** (59/60, 69/70, 79/80, 89/90) son donde `letter_grade` cambia de resultado. Si el código usara `>` en lugar de `>=`, las notas 60, 70, 80 y 90 quedarían en la letra equivocada, y una nota como 85 no lo mostraría.
- **0 y 100** son los extremos del rango válido. Si la validación estuviera mal escrita (`grade <= 0` o `grade >= 100`), esas notas válidas se rechazarían.
- **Cero en `is_even`** es un caso especial: no es positivo ni negativo, y conviene confirmar que se trata como par.
- **Cadenas vacías y de un solo carácter** son los casos mínimos de las funciones de texto; son frecuentes y suelen causar errores por recorridos o comparaciones mal planteadas.
- **Divisiones con negativos** confirman que el signo del resultado es el correcto.

## Resultado de las pruebas

```text
[==========] Running 40 tests from 3 test suites.
[==========] 40 tests from 3 test suites ran. (0 ms total)
[  PASSED  ] 40 tests.
```

Detalle de las pruebas nuevas:

```text
[       OK ] CalculatorTest.DivideNegativeNumbers (0 ms)
[       OK ] CalculatorTest.DividePositiveByNegative (0 ms)
[       OK ] CalculatorTest.ZeroIsEven (0 ms)
[       OK ] StringUtilsTest.EmptyStringToUppercase (0 ms)
[       OK ] StringUtilsTest.EmptyStringIsPalindrome (0 ms)
[       OK ] StringUtilsTest.SingleLetterIsPalindrome (0 ms)
[       OK ] GradeUtilsTest.MinimumValidGrade (0 ms)
[       OK ] GradeUtilsTest.MaximumValidGrade (0 ms)
[       OK ] GradeUtilsTest.NegativeGradeThrowsException (0 ms)
[       OK ] GradeUtilsTest.GradeBoundaryBetweenFAndD (0 ms)
[       OK ] GradeUtilsTest.GradeBoundaryBetweenDAndC (0 ms)
[       OK ] GradeUtilsTest.GradeBoundaryBetweenCAndB (0 ms)
[       OK ] GradeUtilsTest.GradeBoundaryBetweenBAndA (0 ms)
```

Pasaron las 40 pruebas (27 anteriores y 13 nuevas). Ninguna prueba falló, por lo que no hubo que corregir código: las funciones ya manejaban correctamente estos casos.

## Preguntas de reflexión

**1. ¿Por qué no basta con probar casos normales?**
Porque los casos normales funcionan aunque el código tenga errores en los límites. Un `>` en lugar de `>=` pasa todas las pruebas con valores intermedios como 85 y falla solo con el valor exacto del límite.

**2. ¿Qué es un caso borde?**
Una entrada en el límite de lo permitido (el mínimo, el máximo, el valor justo antes o después de un límite) o una situación especial pero válida, como el cero o una cadena vacía.

**3. ¿Qué es un caso inválido?**
Una entrada que no cumple los requisitos y que el sistema debe rechazar, por ejemplo una nota de `-1` o `120`, o dividir entre cero. La función no debe devolver un resultado normal: debe lanzar una excepción.

**4. ¿Qué diferencia hay entre probar `85` y probar exactamente `80`, `90` o `70`?**
85 está en medio del rango de la `B`, y daría `B` sin importar si el código usa `>= 80` o `> 80`. Solo el valor exacto `80` distingue entre los dos. Probar 80, 90 o 70 verifica que la comparación del límite esté bien escrita.

**5. ¿Cómo puede un caso borde revelar errores ocultos?**
Porque los errores de comparación, los errores de uno de más o de menos y el manejo de entradas vacías solo se activan en los límites. Con valores normales el programa parece funcionar bien, y el caso borde hace visible el defecto antes de que lo encuentre un usuario.

---

## Semillas en pruebas

### Qué se hizo

Se agregó a `tests/test_calculator.cpp` la prueba `RandomAdditionsWithFixedSeed`, que genera 10 pares de números aleatorios entre -100 y 100 con `std::mt19937` y verifica que `add(a, b)` sea igual a `a + b`. También se agregó `#include <random>` al inicio del archivo.

```cpp
TEST(CalculatorTest, RandomAdditionsWithFixedSeed) {
    std::mt19937 generator(12345);
    std::uniform_int_distribution<int> distribution(-100, 100);

    for (int i = 0; i < 10; i++) {
        int a = distribution(generator);
        int b = distribution(generator);

        EXPECT_EQ(add(a, b), a + b);
    }
}
```

### Qué hace la semilla

La semilla es el valor inicial con el que arranca el generador de números pseudoaleatorios. El generador no produce números realmente aleatorios: aplica una fórmula a partir de ese valor, de modo que la misma semilla produce siempre la misma secuencia de números.

### Por qué se usa una semilla fija

Para que la prueba sea determinista y repetible. Con la semilla fija `12345`, la prueba usa los mismos 10 pares de números en cada ejecución, en cualquier momento y en cualquier computadora. Si la prueba fallara, volvería a fallar con los mismos datos y se podría investigar.

### Qué pasó al cambiar la semilla

Se cambió temporalmente `generator(12345)` por `generator(54321)`, se recompiló y se ejecutó. Luego se regresó a `12345`.

Resultado con la semilla `12345`:

```text

[----------] Global test environment tear-down
[==========] 41 tests from 3 test suites ran. (0 ms total)
[  PASSED  ] 41 tests.
```

Resultado con la semilla `54321`:

```text

[----------] Global test environment tear-down
[==========] 41 tests from 3 test suites ran. (0 ms total)
[  PASSED  ] 41 tests.
```

La prueba pasó con las dos semillas porque `add` es correcta, y para cualquier par de números el resultado es el mismo. Lo que sí cambió fueron los datos de entrada. Para comprobarlo se escribió un programa auxiliar que imprime los primeros 6 números que genera cada semilla:

```text
Semilla 12345: 86 78 -37 -74 -64 -93
Semilla 54321: 83 2 25 -64 59 62
```

Con la semilla `12345` los primeros pares de la prueba son (86, 78), (-37, -74) y (-64, -93). Con `54321` son (83, 2), (25, -64) y (59, 62). Los números son completamente distintos. Además, se ejecutó el programa dos veces y se comparó la salida con `diff`: fue idéntica, lo que confirma que la misma semilla reproduce exactamente la misma secuencia.

### Por qué esto ayuda en debugging

Si una prueba con datos aleatorios fallara con ciertos números, con una semilla fija se podría repetir exactamente la misma falla las veces que haga falta, usar el depurador sobre esos datos y comprobar que el arreglo funciona. Sin semilla fija, la falla aparecería solo a veces y no se podría reproducir.

### Preguntas de reflexión

**1. ¿Por qué las pruebas con datos aleatorios pueden ser peligrosas si no se controlan?**
Porque pueden pasar en una ejecución y fallar en otra sin que el código haya cambiado. Un fallo que aparece de forma intermitente es difícil de reproducir, y se pierde la confianza en los resultados de las pruebas.

**2. ¿Qué ventaja tiene usar una semilla fija?**
Que los datos "aleatorios" son los mismos en cada ejecución. La prueba sigue cubriendo una variedad de valores, pero es determinista y repetible.

**3. ¿Cómo ayuda la semilla a reproducir errores?**
Si la prueba falla, la semilla permite regenerar la misma secuencia de datos que causó el fallo y volver a ejecutar el caso exacto hasta encontrar y corregir el error.

**4. ¿Por qué es importante documentar la semilla usada?**
Porque sin ella no se puede reconstruir la ejecución. Otra persona, o uno mismo más adelante, necesita saber qué semilla se usó para reproducir los mismos datos, sobre todo si la prueba falla en un servidor como GitHub Actions.
