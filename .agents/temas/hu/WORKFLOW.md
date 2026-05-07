---
name: qa-full
description: Dynamic QA workflow for frontend, backend, or fullstack analysis.
---

## Input

{
  "mode": "frontend | backend | fullstack (optional)",
  "hu": "string"
}

---

## Steps

0. Detect mode (if not provided)

---

1. Execute EXE-ENR-ParsearHU_v1_0
   - Input: HU

2. Execute EXE-ENR-ExtraerKeywordsHU_v1_0
   - Input: EXE-ENR-ParsearHU_v1_0 output

---

3. Execute SYS-QRY-AnalizarCodigo_v1_0
   - Input: EXE-ENR-ExtraerKeywordsHU_v1_0 output

4. Execute ERP-QRY-AnalizarBD_v1_0 (ONLY if backend or fullstack)
   - Input: EXE-ENR-ExtraerKeywordsHU_v1_0 output

---

5. Execute EXE-ENR-MapearTrazabilidad_v1_0 (ONLY if fullstack)
   - Input:
     - EXE-ENR-ParsearHU_v1_0 output
     - SYS-QRY-AnalizarCodigo_v1_0 output
     - ERP-QRY-AnalizarBD_v1_0 output

---

6. Execute EXE-VAL-EvaluarReglas_v1_0
   - Input:
     - EXE-ENR-ParsearHU_v1_0 output
     - SYS-QRY-AnalizarCodigo_v1_0 output
     - ERP-QRY-AnalizarBD_v1_0 output (if exists)
     - EXE-ENR-MapearTrazabilidad_v1_0 output (if exists)

---

7. Execute EXE-VAL-DictaminarHU_v1_0
   - Input:
     - EXE-ENR-ParsearHU_v1_0 output
     - SYS-QRY-AnalizarCodigo_v1_0 output
     - ERP-QRY-AnalizarBD_v1_0 output (if exists)
     - EXE-VAL-EvaluarReglas_v1_0 output

---

8. Execute EXE-VAL-MedirCobertura_v1_0
   - Input:
     - EXE-ENR-ParsearHU_v1_0 output
     - SYS-QRY-AnalizarCodigo_v1_0 output
     - ERP-QRY-AnalizarBD_v1_0 output (if exists)
     - EXE-ENR-MapearTrazabilidad_v1_0 output (if exists)
     - EXE-VAL-EvaluarReglas_v1_0 output
     - EXE-VAL-DictaminarHU_v1_0 output

---

9. Execute EXE-ENR-GenerarSugerencias_v1_0
   - Input:
     - EXE-VAL-DictaminarHU_v1_0 output
     - EXE-VAL-EvaluarReglas_v1_0 output
     - EXE-VAL-MedirCobertura_v1_0 output

---

10. Execute EXE-ENR-GenerarPruebas_v1_0
    - Input:
      - EXE-ENR-ParsearHU_v1_0 output
      - EXE-VAL-DictaminarHU_v1_0 output
      - EXE-VAL-EvaluarReglas_v1_0 output

---

11. Execute EXE-SUM-ConsolidarResultadoQA_v1_0
    - Input:
      - EXE-VAL-DictaminarHU_v1_0
      - EXE-VAL-MedirCobertura_v1_0
      - EXE-VAL-EvaluarReglas_v1_0
      - EXE-ENR-GenerarSugerencias_v1_0
      - EXE-ENR-GenerarPruebas_v1_0