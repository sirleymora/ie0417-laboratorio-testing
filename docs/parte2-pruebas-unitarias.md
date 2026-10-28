# Parte 2: implementación inicial del código

## Qué se hizo

Se crearon tres módulos pequeños en C++ que luego se verifican con pruebas unitarias. Cada módulo tiene un `.h` en `include/` y un `.cpp` en `src/`.

## Módulos creados

| Módulo | Archivo `.h` (declaraciones) | Archivo `.cpp` (implementación) |
|---|---|---|
| Calculadora | `include/calculator.h` | `src/calculator.cpp` |
| Utilidades de texto | `include/string_utils.h` | `src/string_utils.cpp` |
| Utilidades de notas | `include/grade_utils.h` | `src/grade_utils.cpp` |

## Función de cada archivo `.h`

- `calculator.h`: declara `add`, `subtract`, `multiply`, `divide` e `is_even`.
- `string_utils.h`: declara `to_uppercase`, `is_palindrome` y `count_vowels`.
- `grade_utils.h`: declara `average`, `is_passing` y `letter_grade`.

Los encabezados le dicen al compilador y a las pruebas qué funciones existen, sin mostrar cómo están implementadas. Usan `#ifndef`/`#define` para evitar incluirse dos veces.

## Función de cada archivo `.cpp`

- `calculator.cpp`: implementa las operaciones aritméticas. `divide` lanza `std::invalid_argument` si el divisor es cero, e `is_even` revisa si el residuo de dividir entre 2 es 0.
- `string_utils.cpp`: `to_uppercase` convierte cada carácter a mayúscula, `is_palindrome` ignora espacios y mayúsculas y compara el texto con su reverso, y `count_vowels` cuenta las letras a, e, i, o, u sin distinguir mayúsculas.
- `grade_utils.cpp`: `average` calcula el promedio (lanza excepción si la lista está vacía), `is_passing` devuelve verdadero con nota de 70 o más, y `letter_grade` convierte la nota a letra A, B, C, D o F (lanza excepción si la nota no está entre 0 y 100).

## Casos normales, casos borde y casos inválidos

| Función | Caso normal | Caso borde | Caso inválido |
|---|---|---|---|
| `add`, `subtract`, `multiply` | `add(2, 3)` | números negativos, cero | no aplica |
| `divide` | `divide(10, 2)` | divisores negativos, dividendo cero | `divide(10, 0)` lanza excepción |
| `is_even` | `is_even(8)`, `is_even(7)` | `is_even(0)`, negativos | no aplica |
| `to_uppercase` | `"hola"` | cadena vacía, texto con números | no aplica |
| `is_palindrome` | `"radar"`, `"anita lava la tina"` | cadena vacía, una sola letra | no aplica |
| `count_vowels` | `"murcielago"` | texto sin vocales, cadena vacía | no aplica |
| `average` | `{80, 90, 100}` | un solo elemento | vector vacío lanza excepción |
| `is_passing` | `85`, `50` | `70` y `69` | no aplica |
| `letter_grade` | `95`, `85`, `75`, `65`, `50` | `0`, `59`, `60`, `69`, `70`, `79`, `80`, `89`, `90`, `100` | `-1` y `101` lanzan excepción |

### Observación

`count_vowels` solo reconoce vocales sin tilde, por lo que una letra como "é" no se cuenta. Es una limitación que se podría probar y documentar más adelante.

---

# Parte 3: primeras pruebas unitarias con Google Test

## Qué se hizo

Se crearon tres archivos de pruebas en `tests/`, uno por módulo: `test_calculator.cpp`, `test_string_utils.cpp` y `test_grade_utils.cpp`. Luego se compiló el proyecto con CMake y se ejecutaron las pruebas con `./run_tests` y con `ctest`.

## Comandos usados para compilar

```bash
mkdir build
cd build
cmake ..
make
```

`cmake ..` configura el proyecto y descarga Google Test 1.12.1. `make` compila la librería `project_lib`, Google Test y el ejecutable `run_tests`. Durante `cmake` apareció una advertencia `CMP0135` sobre las fechas de los archivos descargados; es solo un aviso de desarrollo y no afecta la compilación ni las pruebas.

## Comandos usados para ejecutar las pruebas

```bash
./run_tests
ctest --output-on-failure
```

## Resultado de `./run_tests`

```text
Running main() from /home/sirley/laboratorio-testing/build/_deps/googletest-src/googletest/src/gtest_main.cc
[==========] Running 25 tests from 3 test suites.
[----------] Global test environment set-up.
[----------] 8 tests from CalculatorTest
[ RUN      ] CalculatorTest.AddPositiveNumbers
[       OK ] CalculatorTest.AddPositiveNumbers (0 ms)
[ RUN      ] CalculatorTest.AddNegativeNumbers
[       OK ] CalculatorTest.AddNegativeNumbers (0 ms)
[ RUN      ] CalculatorTest.SubtractNumbers
[       OK ] CalculatorTest.SubtractNumbers (0 ms)
[ RUN      ] CalculatorTest.MultiplyNumbers
[       OK ] CalculatorTest.MultiplyNumbers (0 ms)
[ RUN      ] CalculatorTest.DivideNumbers
[       OK ] CalculatorTest.DivideNumbers (0 ms)
[ RUN      ] CalculatorTest.DivideByZeroThrowsException
[       OK ] CalculatorTest.DivideByZeroThrowsException (30 ms)
[ RUN      ] CalculatorTest.DetectEvenNumber
[       OK ] CalculatorTest.DetectEvenNumber (0 ms)
[ RUN      ] CalculatorTest.DetectOddNumber
[       OK ] CalculatorTest.DetectOddNumber (0 ms)
[----------] 8 tests from CalculatorTest (30 ms total)

[----------] 7 tests from StringUtilsTest
[ RUN      ] StringUtilsTest.ConvertTextToUppercase
[       OK ] StringUtilsTest.ConvertTextToUppercase (0 ms)
[ RUN      ] StringUtilsTest.ConvertMixedTextToUppercase
[       OK ] StringUtilsTest.ConvertMixedTextToUppercase (0 ms)
[ RUN      ] StringUtilsTest.DetectSimplePalindrome
[       OK ] StringUtilsTest.DetectSimplePalindrome (0 ms)
[ RUN      ] StringUtilsTest.DetectPalindromeWithSpaces
[       OK ] StringUtilsTest.DetectPalindromeWithSpaces (0 ms)
[ RUN      ] StringUtilsTest.DetectNonPalindrome
[       OK ] StringUtilsTest.DetectNonPalindrome (0 ms)
[ RUN      ] StringUtilsTest.CountVowels
[       OK ] StringUtilsTest.CountVowels (0 ms)
[ RUN      ] StringUtilsTest.CountVowelsInTextWithoutVowels
[       OK ] StringUtilsTest.CountVowelsInTextWithoutVowels (0 ms)
[----------] 7 tests from StringUtilsTest (0 ms total)

[----------] 10 tests from GradeUtilsTest
[ RUN      ] GradeUtilsTest.CalculateAverage
[       OK ] GradeUtilsTest.CalculateAverage (0 ms)
[ RUN      ] GradeUtilsTest.AverageEmptyVectorThrowsException
[       OK ] GradeUtilsTest.AverageEmptyVectorThrowsException (0 ms)
[ RUN      ] GradeUtilsTest.PassingGrade
[       OK ] GradeUtilsTest.PassingGrade (0 ms)
[ RUN      ] GradeUtilsTest.FailingGrade
[       OK ] GradeUtilsTest.FailingGrade (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeA
[       OK ] GradeUtilsTest.LetterGradeA (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeB
[       OK ] GradeUtilsTest.LetterGradeB (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeC
[       OK ] GradeUtilsTest.LetterGradeC (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeD
[       OK ] GradeUtilsTest.LetterGradeD (0 ms)
[ RUN      ] GradeUtilsTest.LetterGradeF
[       OK ] GradeUtilsTest.LetterGradeF (0 ms)
[ RUN      ] GradeUtilsTest.InvalidGradeThrowsException
[       OK ] GradeUtilsTest.InvalidGradeThrowsException (0 ms)
[----------] 10 tests from GradeUtilsTest (0 ms total)

[----------] Global test environment tear-down
[==========] 25 tests from 3 test suites ran. (31 ms total)
[  PASSED  ] 25 tests.
```

## Resultado de `ctest --output-on-failure`

```text
Test project /home/sirley/laboratorio-testing/build
      Start  1: CalculatorTest.AddPositiveNumbers
 1/25 Test  #1: CalculatorTest.AddPositiveNumbers ..................   Passed    0.01 sec
      Start  2: CalculatorTest.AddNegativeNumbers
 2/25 Test  #2: CalculatorTest.AddNegativeNumbers ..................   Passed    0.01 sec
      Start  3: CalculatorTest.SubtractNumbers
 3/25 Test  #3: CalculatorTest.SubtractNumbers .....................   Passed    0.01 sec
      Start  4: CalculatorTest.MultiplyNumbers
 4/25 Test  #4: CalculatorTest.MultiplyNumbers .....................   Passed    0.01 sec
      Start  5: CalculatorTest.DivideNumbers
 5/25 Test  #5: CalculatorTest.DivideNumbers .......................   Passed    0.01 sec
      Start  6: CalculatorTest.DivideByZeroThrowsException
 6/25 Test  #6: CalculatorTest.DivideByZeroThrowsException .........   Passed    0.01 sec
      Start  7: CalculatorTest.DetectEvenNumber
 7/25 Test  #7: CalculatorTest.DetectEvenNumber ....................   Passed    0.01 sec
      Start  8: CalculatorTest.DetectOddNumber
 8/25 Test  #8: CalculatorTest.DetectOddNumber .....................   Passed    0.01 sec
      Start  9: StringUtilsTest.ConvertTextToUppercase
 9/25 Test  #9: StringUtilsTest.ConvertTextToUppercase .............   Passed    0.01 sec
      Start 10: StringUtilsTest.ConvertMixedTextToUppercase
10/25 Test #10: StringUtilsTest.ConvertMixedTextToUppercase ........   Passed    0.01 sec
      Start 11: StringUtilsTest.DetectSimplePalindrome
11/25 Test #11: StringUtilsTest.DetectSimplePalindrome .............   Passed    0.01 sec
      Start 12: StringUtilsTest.DetectPalindromeWithSpaces
12/25 Test #12: StringUtilsTest.DetectPalindromeWithSpaces .........   Passed    0.01 sec
      Start 13: StringUtilsTest.DetectNonPalindrome
13/25 Test #13: StringUtilsTest.DetectNonPalindrome ................   Passed    0.01 sec
      Start 14: StringUtilsTest.CountVowels
14/25 Test #14: StringUtilsTest.CountVowels ........................   Passed    0.01 sec
      Start 15: StringUtilsTest.CountVowelsInTextWithoutVowels
15/25 Test #15: StringUtilsTest.CountVowelsInTextWithoutVowels .....   Passed    0.01 sec
      Start 16: GradeUtilsTest.CalculateAverage
16/25 Test #16: GradeUtilsTest.CalculateAverage ....................   Passed    0.01 sec
      Start 17: GradeUtilsTest.AverageEmptyVectorThrowsException
17/25 Test #17: GradeUtilsTest.AverageEmptyVectorThrowsException ...   Passed    0.01 sec
      Start 18: GradeUtilsTest.PassingGrade
18/25 Test #18: GradeUtilsTest.PassingGrade ........................   Passed    0.01 sec
      Start 19: GradeUtilsTest.FailingGrade
19/25 Test #19: GradeUtilsTest.FailingGrade ........................   Passed    0.01 sec
      Start 20: GradeUtilsTest.LetterGradeA
20/25 Test #20: GradeUtilsTest.LetterGradeA ........................   Passed    0.01 sec
      Start 21: GradeUtilsTest.LetterGradeB
21/25 Test #21: GradeUtilsTest.LetterGradeB ........................   Passed    0.01 sec
      Start 22: GradeUtilsTest.LetterGradeC
22/25 Test #22: GradeUtilsTest.LetterGradeC ........................   Passed    0.01 sec
      Start 23: GradeUtilsTest.LetterGradeD
23/25 Test #23: GradeUtilsTest.LetterGradeD ........................   Passed    0.01 sec
      Start 24: GradeUtilsTest.LetterGradeF
24/25 Test #24: GradeUtilsTest.LetterGradeF ........................   Passed    0.01 sec
      Start 25: GradeUtilsTest.InvalidGradeThrowsException
25/25 Test #25: GradeUtilsTest.InvalidGradeThrowsException .........   Passed    0.01 sec

100% tests passed, 0 tests failed out of 25

Total Test time (real) =   0.24 sec
```

## Resumen de resultados

| Concepto | Cantidad |
|---|---:|
| Pruebas ejecutadas | 25 |
| Pruebas exitosas | 25 |
| Pruebas fallidas | 0 |

Por suite: 8 pruebas de `CalculatorTest`, 7 de `StringUtilsTest` y 10 de `GradeUtilsTest`. Ambos comandos dan el mismo resultado: `run_tests` muestra el detalle de Google Test y `ctest` corre cada prueba como un proceso aparte y resume el total.

## Explicación de cinco pruebas

**1. `CalculatorTest.AddNegativeNumbers`**

```cpp
EXPECT_EQ(add(-2, -3), -5);
```

Verifica que `add` suma bien dos números negativos. Es importante porque los números negativos suelen ser una fuente de errores que no se ven con enteros positivos.

**2. `CalculatorTest.DivideByZeroThrowsException`**

```cpp
EXPECT_THROW(divide(10, 0), std::invalid_argument);
```

Verifica que dividir entre cero no devuelve un resultado inválido, sino que lanza `std::invalid_argument`. Es un caso inválido: la prueba pasa porque la excepción ocurre.

**3. `StringUtilsTest.DetectPalindromeWithSpaces`**

```cpp
EXPECT_TRUE(is_palindrome("anita lava la tina"));
```

Verifica que `is_palindrome` ignora los espacios al comparar. Sin esa normalización, la frase no sería palíndromo porque los espacios quedarían en posiciones distintas.

**4. `StringUtilsTest.CountVowels`**

```cpp
EXPECT_EQ(count_vowels("murcielago"), 5);
```

Verifica que se cuentan correctamente las vocales de una palabra (u, i, e, a, o). Es el caso normal de la función.

**5. `GradeUtilsTest.InvalidGradeThrowsException`**

```cpp
EXPECT_THROW(letter_grade(120), std::invalid_argument);
```

Verifica que una nota fuera del rango de 0 a 100 se rechaza con una excepción en lugar de devolver una letra sin sentido.

**6. `GradeUtilsTest.CalculateAverage`**

```cpp
EXPECT_DOUBLE_EQ(average({80, 90, 100}), 90.0);
```

Verifica el promedio de tres notas. Se usa `EXPECT_DOUBLE_EQ` porque el resultado es un `double` y las comparaciones de decimales necesitan tolerancia por redondeo.

## Preguntas de reflexión

**1. ¿Qué significa que una prueba pase?**
Que todas las verificaciones de la prueba se cumplieron: la función devolvió lo que se esperaba (o lanzó la excepción esperada). Significa que, para ese caso concreto, el código se comporta como se definió.

**2. ¿Qué significa que una prueba falle?**
Que al menos una verificación no se cumplió. Puede haber un error en el código, o que la prueba esté mal escrita o desactualizada. Google Test muestra qué valor se esperaba y cuál se obtuvo.

**3. ¿Qué diferencia hay entre probar una función manualmente y probarla con Google Test?**
Manualmente hay que ejecutar el programa, ingresar los datos y revisar la salida a ojo cada vez. Con Google Test los casos quedan escritos en código, se ejecutan todos con un comando, se comparan contra el valor esperado automáticamente y se pueden repetir después de cada cambio.

**4. ¿Por qué las pruebas unitarias deben ser rápidas?**
Porque se ejecutan muy seguido, después de cada cambio. Las 25 pruebas tardaron 31 ms; si fueran lentas, se dejarían de ejecutar y los errores se detectarían tarde.

**5. ¿Por qué las pruebas unitarias deben ser deterministas?**
Porque si una prueba da resultados distintos con el mismo código, no se puede saber si un fallo es un error real o solo mala suerte. Una prueba determinista permite confiar en el resultado y reproducir cualquier fallo.

---

# Parte 4: EXPECT vs ASSERT

## Qué se hizo

Se agregaron dos pruebas a `tests/test_calculator.cpp`: `MultipleExpectChecks`, que usa varias verificaciones `EXPECT_EQ`, y `AssertBeforeDivision`, que usa `ASSERT_NE` antes de dividir. Luego se hizo una actividad experimental cambiando temporalmente el divisor de `2` a `0`.

## Pruebas agregadas

```cpp
TEST(CalculatorTest, MultipleExpectChecks) {
    EXPECT_EQ(add(1, 1), 2);
    EXPECT_EQ(add(2, 2), 4);
    EXPECT_EQ(add(3, 3), 6);
}

TEST(CalculatorTest, AssertBeforeDivision) {
    int divisor = 2;

    ASSERT_NE(divisor, 0);

    EXPECT_EQ(divide(10, divisor), 5);
}
```

## Qué pasó cuando `divisor` era `2`

Las dos pruebas pasaron. Como el divisor es distinto de cero, `ASSERT_NE` se cumple y la prueba continúa hasta `EXPECT_EQ(divide(10, divisor), 5)`, que también se cumple. El total pasó de 25 a 27 pruebas.

```text
[==========] Running 27 tests from 3 test suites.
[ RUN      ] CalculatorTest.MultipleExpectChecks
[       OK ] CalculatorTest.MultipleExpectChecks (0 ms)
[ RUN      ] CalculatorTest.AssertBeforeDivision
[       OK ] CalculatorTest.AssertBeforeDivision (0 ms)
[==========] 27 tests from 3 test suites ran. (2 ms total)
[  PASSED  ] 27 tests.
```

## Qué pasó cuando `divisor` era `0`

Se cambió temporalmente `int divisor = 2;` por `int divisor = 0;` y se ejecutó solo esa prueba con `./run_tests --gtest_filter=CalculatorTest.AssertBeforeDivision`. La prueba falló:

```text
Running main() from /home/sirley/laboratorio-testing/build/_deps/googletest-src/googletest/src/gtest_main.cc
Note: Google Test filter = CalculatorTest.AssertBeforeDivision
[==========] Running 1 test from 1 test suite.
[----------] Global test environment set-up.
[----------] 1 test from CalculatorTest
[ RUN      ] CalculatorTest.AssertBeforeDivision
/home/sirley/laboratorio-testing/tests/test_calculator.cpp:45: Failure
Expected: (divisor) != (0), actual: 0 vs 0
[  FAILED  ] CalculatorTest.AssertBeforeDivision (0 ms)
[----------] 1 test from CalculatorTest (0 ms total)

[----------] Global test environment tear-down
[==========] 1 test from 1 test suite ran. (0 ms total)
[  PASSED  ] 0 tests.
[  FAILED  ] 1 test, listed below:
[  FAILED  ] CalculatorTest.AssertBeforeDivision

 1 FAILED TEST
```

El mensaje indica la línea del fallo (`test_calculator.cpp:45`), qué se esperaba (`divisor != 0`) y qué se obtuvo (`actual: 0 vs 0`). Después se devolvió el valor a `int divisor = 2;`, se recompiló y volvieron a pasar las 27 pruebas.

## Por qué `ASSERT_NE` detuvo la prueba

`ASSERT_` termina la prueba en el momento en que su verificación falla. Al ser `divisor` igual a `0`, `ASSERT_NE(divisor, 0)` falló y la prueba terminó ahí, por lo que la línea siguiente (`EXPECT_EQ(divide(10, divisor), 5)`) nunca se ejecutó. Si hubiera seguido, `divide(10, 0)` habría lanzado `std::invalid_argument` sin que nadie lo capturara, y la prueba habría fallado por una excepción inesperada, un segundo error derivado del primero que confunde la causa real. Con `ASSERT_NE` el reporte muestra solo la causa: el divisor era cero.

## Diferencia entre `EXPECT_` y `ASSERT_`

| | `EXPECT_` | `ASSERT_` |
|---|---|---|
| Si la verificación falla | La prueba se marca como fallida pero **continúa** | La prueba se marca como fallida y **se detiene** |
| Efecto en las verificaciones siguientes | Se siguen ejecutando y reportando | No se ejecutan |
| Uso típico | Verificar varios resultados independientes | Verificar una condición previa sin la cual el resto no tiene sentido |

En `MultipleExpectChecks` se usa `EXPECT_` porque las tres sumas son independientes: si una falla, igual interesa saber si las otras dos están bien.

## Preguntas de reflexión

**1. ¿Cuándo conviene usar `EXPECT_`?**
Cuando las verificaciones son independientes entre sí y conviene ver todos los fallos en una sola ejecución, en lugar de descubrirlos uno por uno.

**2. ¿Cuándo conviene usar `ASSERT_`?**
Cuando el resto de la prueba depende de que una condición previa se cumpla, por ejemplo que un puntero no sea nulo, que un vector tenga elementos antes de acceder a ellos o que un divisor no sea cero.

**3. ¿Qué podría pasar si se usa `EXPECT_` cuando en realidad se necesitaba detener la prueba?**
La prueba seguiría ejecutándose con datos inválidos, y podría causar fallos en cadena, excepciones o incluso un acceso inválido a memoria que cierre el programa de pruebas. El reporte tendría mucho ruido y sería difícil ver cuál fue el error original.

**4. ¿Qué podría pasar si se usa `ASSERT_` en exceso?**
Cada fallo detendría la prueba y ocultaría los demás, de modo que habría que corregir y volver a ejecutar varias veces para descubrir todos los errores de una prueba que tenía varias verificaciones independientes.
