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
