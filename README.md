# Laboratorio: pruebas de software con Google Test, cobertura y GitHub Actions

Laboratorio del curso IE0417 - Diseño de software para ingeniería. Proyecto en C++ con pruebas unitarias en Google Test, medición de cobertura con `gcov`/`lcov` y ejecución automática de las pruebas con GitHub Actions.

## Estructura

| Ruta | Contenido |
|---|---|
| `include/` | Encabezados: `calculator.h`, `string_utils.h`, `grade_utils.h` |
| `src/` | Implementaciones de los tres módulos |
| `tests/` | Pruebas unitarias con Google Test |
| `docs/` | Documentación de cada parte del laboratorio |
| `.github/workflows/testing.yml` | Workflow de GitHub Actions |
| `CMakeLists.txt` | Configuración de CMake |

## Compilar y ejecutar las pruebas

```bash
mkdir build
cd build
cmake ..
make
./run_tests
ctest --output-on-failure
```

## Medir cobertura

```bash
rm -rf build
mkdir build
cd build
cmake -DENABLE_COVERAGE=ON ..
make
./run_tests
lcov --capture --directory . --output-file coverage.info --rc branch_coverage=1 --ignore-errors mismatch
lcov --remove coverage.info '/usr/*' '*/_deps/*' '*/tests/*' --output-file coverage_filtered.info --rc branch_coverage=1 --ignore-errors unused
genhtml coverage_filtered.info --rc branch_coverage=1 --output-directory coverage_report
```

Con `lcov` 2.0 (Ubuntu 24.04) hace falta `--ignore-errors mismatch`; el detalle está en `docs/parte5-cobertura.md`.

## Documentación

| Archivo | Contenido |
|---|---|
| `docs/parte1-plan-pruebas.md` | Preparación del proyecto (Parte 1) y casos funcionales (Parte 9) |
| `docs/parte2-pruebas-unitarias.md` | Código base, pruebas unitarias y EXPECT vs ASSERT (Partes 2, 3 y 4) |
| `docs/parte3-pruebas-fallidas.md` | Prueba fallida y corrección (Parte 5) |
| `docs/parte4-casos-borde.md` | Casos borde y semillas (Partes 6 y 7) |
| `docs/parte5-cobertura.md` | Cobertura de código (Parte 8) |
| `docs/parte6-github-actions.md` | GitHub Actions y fallo intencional en CI (Partes 10 y 11) |
| `docs/reflexion-final.md` | Mini reto y reflexión final (Partes 12 y 13) |
