# Mini reto y reflexión final

## Mini reto de testing

### Función implementada

Se agregó la función `is_valid_grade`, que indica si una nota está dentro del rango permitido de 0 a 100, incluyendo ambos extremos. Está declarada en `include/grade_utils.h` y se implementa en `src/grade_utils.cpp`:

```cpp
bool is_valid_grade(int grade) {
    return grade >= 0 && grade <= 100;
}
```

### Casos de prueba diseñados

| Prueba | Entrada | Resultado esperado | Tipo de caso |
|---|---:|---|---|
| `ValidGradeNormalValue` | 85 | `true` | Normal válido |
| `InvalidGradeNormalValue` | 150 | `false` | Normal inválido |
| `ValidGradeLowerBound` | 0 | `true` | Borde inferior válido |
| `ValidGradeUpperBound` | 100 | `true` | Borde superior válido |
| `InvalidGradeJustBelowLowerBound` | -1 | `false` | Justo debajo del borde inferior |
| `InvalidGradeJustAboveUpperBound` | 101 | `false` | Justo encima del borde superior |

```cpp
TEST(GradeUtilsTest, ValidGradeNormalValue) {
    EXPECT_TRUE(is_valid_grade(85));
}

TEST(GradeUtilsTest, InvalidGradeNormalValue) {
    EXPECT_FALSE(is_valid_grade(150));
}

TEST(GradeUtilsTest, ValidGradeLowerBound) {
    EXPECT_TRUE(is_valid_grade(0));
}

TEST(GradeUtilsTest, ValidGradeUpperBound) {
    EXPECT_TRUE(is_valid_grade(100));
}

TEST(GradeUtilsTest, InvalidGradeJustBelowLowerBound) {
    EXPECT_FALSE(is_valid_grade(-1));
}

TEST(GradeUtilsTest, InvalidGradeJustAboveUpperBound) {
    EXPECT_FALSE(is_valid_grade(101));
}
```

### Por qué se escogieron esos casos

- **85 y 150** son los casos normales: un valor típico dentro del rango y uno claramente fuera. Confirman el comportamiento general de la función.
- **0 y 100** son los extremos del rango válido. Verifican que los límites se incluyan, es decir, que la comparación use `>=` y `<=`.
- **-1 y 101** son los valores inválidos más cercanos a cada límite. Verifican que el rango termine exactamente donde debe.

Los cuatro valores de los extremos son los que detectan un error de comparación (por ejemplo, `>` en lugar de `>=`), que los valores normales no revelan.

### Resultado de las pruebas

Se compiló con `make` y se ejecutó `./run_tests`. Pasaron las 48 pruebas (las 42 anteriores y las 6 nuevas):

```text

[----------] Global test environment tear-down
[==========] 48 tests from 3 test suites ran. (3 ms total)
[  PASSED  ] 48 tests.
```

### ¿Se subieron los cambios a GitHub?

Sí. Se subieron con `git add .`, `git commit -m "Agrega is_valid_grade y sus pruebas (mini reto)"` y `git push` (commit `86f2f4e`, rama `main`).

### ¿GitHub Actions ejecutó correctamente?

Sí. Después del `push` (commit `86f2f4e`), el workflow `C++ testing` se ejecutó automáticamente y terminó con check verde, con las 48 pruebas pasando.

### Preguntas de reflexión

**1. ¿Cuál fue el caso más obvio de probar?**
`is_valid_grade(85)`: una nota válida en medio del rango, que cualquier implementación razonable aceptaría.

**2. ¿Cuál fue el caso borde más importante?**
Los límites `0` y `100`, junto con los valores inválidos más cercanos (`-1` y `101`). Son los que comprueban si el rango incluye o excluye los extremos.

**3. ¿Qué error podría aparecer si no se prueban los valores `0` y `100`?**
Si la función estuviera escrita con `>` y `<` en lugar de `>=` y `<=`, rechazaría las notas 0 y 100, que sí son válidas. Con pruebas solo de 85, 150, -1 y 101 ese error pasaría sin detectarse, porque todas darían el resultado esperado.

**4. ¿Qué diferencia hay entre probar un valor como `50` y probar valores como `-1`, `0`, `100` y `101`?**
Un valor como 50 está lejos de los límites y solo confirma que la función acepta lo normal. Los valores `-1`, `0`, `100` y `101` están justo en el punto donde la función cambia de aceptar a rechazar, y es ahí donde aparecen los errores de comparación.

**5. ¿Qué aprendió sobre diseñar pruebas?**
Que conviene pensar primero en los límites y en las entradas inválidas, y no solo en los casos que se espera que funcionen. Cada prueba debe tener una razón concreta: qué error detectaría si el código estuviera mal.

---

## Reflexión final

**1. ¿Qué es software testing?**
Es el proceso de evaluar y verificar que una aplicación cumple con los requisitos especificados y funciona como se espera. Busca encontrar errores y comportamientos incorrectos antes de que el software llegue a los usuarios.

**2. ¿Por qué el testing mejora la calidad del software?**
Porque detecta los errores cuando se introducen, que es cuando son más baratos de corregir, y deja por escrito el comportamiento esperado de cada función. Además permite modificar el código con confianza: si algo que ya funcionaba se rompe, las pruebas lo señalan.

**3. ¿Cuál es la diferencia entre verificación y validación?**
La verificación comprueba que el software se construyó correctamente, es decir, que cumple lo que se especificó (por ejemplo, que `letter_grade(90)` devuelva `A`, como dice el requisito). La validación comprueba que se construyó lo correcto, es decir, que el producto sirve para lo que el usuario realmente necesita.

**4. ¿Qué es una prueba unitaria?**
Una prueba que verifica una unidad pequeña de código, como una función o un método, de forma aislada. Por ejemplo, `EXPECT_EQ(add(2, 3), 5)` prueba solo la función `add`.

**5. ¿Qué es una prueba funcional?**
Una prueba diseñada a partir del comportamiento esperado del sistema, es decir, de un requisito, y no de cómo está escrito el código por dentro. En este laboratorio, la tabla de casos de `letter_grade` (95, 90, 89, -1 y 101) se construyó a partir del requisito de convertir notas a letras.

**6. ¿Qué diferencia hay entre `EXPECT_` y `ASSERT_`?**
Si la verificación falla, `EXPECT_` marca la prueba como fallida pero continúa, mientras que `ASSERT_` la detiene de inmediato. Se usa `ASSERT_` cuando el resto de la prueba no tiene sentido sin esa condición (como comprobar que el divisor no sea cero antes de dividir) y `EXPECT_` cuando las verificaciones son independientes.

**7. ¿Por qué las pruebas deben ser deterministas?**
Porque si una prueba da resultados distintos con el mismo código, no se puede saber si un fallo es un error real o casualidad. Una prueba determinista da siempre el mismo resultado, y por eso se puede confiar en ella y reproducir cualquier fallo.

**8. ¿Por qué puede ser útil una semilla en pruebas con valores aleatorios?**
Porque con una semilla fija el generador produce siempre la misma secuencia de números. La prueba sigue usando una variedad de datos, pero es repetible, y si falla se puede volver a ejecutar con exactamente los mismos valores. Por eso conviene documentar la semilla usada.

**9. ¿Qué es cobertura de código?**
Una métrica que indica qué partes del código (líneas, funciones o ramas) se ejecutaron mientras corrían las pruebas, y cuáles no. Se midió con `gcov` y `lcov`.

**10. ¿Por qué una cobertura alta no garantiza ausencia de errores?**
Porque la cobertura mide qué código se ejecutó, no si el resultado se verificó correctamente ni si se probaron todas las entradas posibles. En este laboratorio hubo 100% de cobertura de líneas y funciones, pero solo 87.5% de ramas, y aun con 100% de todo seguiría siendo posible que una prueba no comprobara nada útil.

**11. ¿Qué ventaja tiene ejecutar pruebas en GitHub Actions?**
Las pruebas se ejecutan automáticamente en un ambiente limpio cada vez que se sube código, sin depender de que alguien se acuerde de correrlas. Con el fallo intencional se vio que un cambio que rompe una prueba queda marcado con una X roja en el commit que lo causó.

**12. ¿Qué parte del laboratorio le pareció más útil?**
Las pruebas unitarias con Google Test. Al romper `is_even` a propósito se vio cómo una prueba señala la función con el problema, con el valor esperado y el obtenido, y cómo agregar casos borde ayuda a comprobar el comportamiento en los límites.

**13. ¿Qué parte le pareció más difícil?**
La parte de GitHub Actions. La dificultad estuvo sobre todo en conectar el repositorio con GitHub: el `push` fue rechazado con un error 403 porque Git usaba las credenciales de otra cuenta, y hubo que crear un token de acceso personal con los permisos `repo` y `workflow`. Una vez que el código estuvo subido, el workflow sí se ejecutó correctamente.

**14. ¿Cómo aplicaría pruebas automatizadas en un proyecto futuro?**
Aplicaría los conceptos aprendidos en este laboratorio (pruebas unitarias, casos borde, medición de cobertura e integración continua) como una forma rápida y segura de verificar el código, tanto en los proyectos de la carrera como más adelante en el ámbito laboral. Escribir las pruebas junto con el código y ejecutarlas automáticamente en cada cambio permite detectar los errores a tiempo y modificar el código con confianza.
