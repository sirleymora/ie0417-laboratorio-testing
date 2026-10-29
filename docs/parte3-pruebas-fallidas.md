# Parte 5: pruebas fallidas y corrección de errores

## Qué se hizo

Se introdujo un error intencional en la función `is_even` de `src/calculator.cpp`, se ejecutaron las pruebas para ver cómo Google Test lo detecta, y luego se corrigió la función.

## Cambio incorrecto realizado

Archivo modificado: `src/calculator.cpp`. Se cambió la comparación de `== 0` a `== 1`:

```cpp
// Versión incorrecta
bool is_even(int number) {
    return number % 2 == 1;
}
```

Comandos usados (el cambio se hizo con `sed`, y se verificó con `grep`):

```bash
sed -i 's/return number % 2 == 0;/return number % 2 == 1;/' src/calculator.cpp
grep -n "number % 2" src/calculator.cpp
cd build
make
./run_tests
```

El `grep` confirmó el cambio: `25:    return number % 2 == 1;`.

## Pruebas que fallaron

Fallaron 2 de 27 pruebas, y las otras 25 siguieron pasando:

- `CalculatorTest.DetectEvenNumber`
- `CalculatorTest.DetectOddNumber`

## Mensaje de error mostrado por Google Test

```text
/home/sirley/laboratorio-testing/tests/test_calculator.cpp:29: Failure
Value of: is_even(8)
  Actual: false
Expected: true
[  FAILED  ] CalculatorTest.DetectEvenNumber (0 ms)
/home/sirley/laboratorio-testing/tests/test_calculator.cpp:33: Failure
Value of: is_even(7)
  Actual: true
Expected: false
[  FAILED  ] CalculatorTest.DetectOddNumber (0 ms)
[  PASSED  ] 25 tests.
[  FAILED  ] 2 tests, listed below:
[  FAILED  ] CalculatorTest.DetectEvenNumber
[  FAILED  ] CalculatorTest.DetectOddNumber
 2 FAILED TESTS
```

## Qué esperaba la prueba y qué obtuvo realmente

| Prueba | Expresión | Esperado | Obtenido |
|---|---|---|---|
| `DetectEvenNumber` (línea 29) | `is_even(8)` | `true` | `false` |
| `DetectOddNumber` (línea 33) | `is_even(7)` | `false` | `true` |

Con el error, la función devuelve el resultado contrario al correcto. Un número par como 8 tiene residuo 0 al dividir entre 2, así que `8 % 2 == 1` es falso. Un impar como 7 tiene residuo 1, así que `7 % 2 == 1` es verdadero. La lógica quedó invertida.

## Corrección realizada

Se devolvió la comparación a `== 0`:

```cpp
// Versión correcta
bool is_even(int number) {
    return number % 2 == 0;
}
```

```bash
sed -i 's/return number % 2 == 1;/return number % 2 == 0;/' src/calculator.cpp
grep -n "number % 2" src/calculator.cpp
cd build
make
./run_tests
```

El `grep` confirmó la corrección: `25:    return number % 2 == 0;`.

## Resultado final

```text

[----------] Global test environment tear-down
[==========] 27 tests from 3 test suites ran. (0 ms total)
[  PASSED  ] 27 tests.
```

Después de la corrección pasaron las 27 pruebas.

## Qué se aprendió

Que una sola línea incorrecta basta para romper el comportamiento de una función y que las pruebas lo detectan en segundos. También que las pruebas que fallaron señalaron exactamente la función con el problema, mientras que el resto de las pruebas confirmaron que lo demás no se afectó.

## Preguntas de reflexión

**1. ¿Cómo ayudó Google Test a identificar el error?**
Mostró cuáles pruebas fallaron y en qué línea del archivo de pruebas. Como las dos pruebas que fallaron eran sobre `is_even`, quedó claro que el problema estaba en esa función y no en las demás.

**2. ¿Qué información útil muestra una prueba fallida?**
El nombre de la prueba, el archivo y la línea donde falló, la expresión evaluada (`is_even(8)`), el valor esperado (`true`) y el valor obtenido (`false`), y un resumen de cuántas pruebas pasaron y cuántas fallaron.

**3. ¿Por qué es importante ejecutar pruebas después de modificar código?**
Porque así se detecta un error justo cuando se introduce, cuando el cambio es pequeño y todavía se recuerda qué se tocó. Si se espera es más difícil saber cuál de varios cambios causó el problema.

**4. ¿Qué riesgo existe si se cambia código y no se ejecutan las pruebas?**
Que el error pase sin detectarse y llegue a otras partes del sistema o a los usuarios y que se rompa algo que antes funcionaba. Mientras más tiempo pasa, más caro y difícil es encontrar su origen.
