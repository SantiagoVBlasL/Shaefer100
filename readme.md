# QC y detección de outliers en rs‑fMRI (Schaefer‑100)

> *Scripts:* **analisis\_outliers\_schaefer100\_v4.0.py**  |  *pipeline general* **v5.3**
> *Fecha:* 19 jun 2025
> *Atlas:* [Schaefer 2018 – 100 parcelas/7 redes](https://github.com/ThomasYeoLab/CBIG/tree/master/stable_projects/brain_parcellation/Schaefer2018_LocalGlobal)
> *Objetivo:* asegurar la **fiabilidad** de las series BOLD antes de calcular conectividad funcional.

---

## 📌 Descripción breve

Esta es la **Fase 1** del pipeline de mi tesis doctoral.  Ejecuta controles de calidad (QC) rigurosos sobre las series temporales BOLD, identifica y descarta sujetos/TPs con artefactos y genera reportes reproducibles para las siguientes etapas de conectividad estática y dinámica.

---

## 🔗 Metodología

| # | Paso                        | Detalle                                 | Umbrales / notas                            |                 |                                   |
| - | --------------------------- | --------------------------------------- | ------------------------------------------- | --------------- | --------------------------------- |
| 1 | **Carga de datos**          | `.mat` con 100 ROIs + CSV de metadatos  | —                                           |                 |                                   |
| 2 | **QC inicial**              | TPs, NaNs, canales nulos                | `TP ≥ 140`, `NaNs ≤ 5 %`, `Null ≤ 1 %`      |                 |                                   |
| 3 | **Outliers univariantes**   | \`                                      | Z                                           | > 3.5\` por ROI | descartar sujeto si **% > 1.2 %** |
| 4 | **Outliers multivariantes** | **MCD** (α = 0.001) → dist. Mahalanobis | descartar si **% > 28 %**                   |                 |                                   |
| 5 | **Scrubbing**               | PCA (n=10) + leverage MAD×5             | TPs eliminados individualmente              |                 |                                   |
| 6 | **Bandera de descarte**     | combina criterios anteriores            | `ToDiscard_Overall`                         |                 |                                   |
| 7 | **Reportes & figuras**      | CSV + PNG                               | generados en `qc_outputs_schaefer100_v4.0/` |                 |                                   |

---

## ⚙️ Configuración rápida

Las rutas y parámetros se definen al inicio del script:

```python
ROI_DIR   = Path('ROISignals_Schaefer2018_100Parcels_...')
CSV_PATH  = Path('SubjectsData_Schaefer2018.csv')
EXPORT_DIR= Path('./qc_outputs_schaefer100_v4.0')

# Umbrales clave
Z_THRESHOLD_UNIV = 3.5
ALPHA_MAHAL      = 0.001
TP_THRESHOLD_VALUE                 = 140
UNIV_OUTLIER_PCT_ABSOLUTE_THRESHOLD= 1.2  # %
MV_OUTLIER_PCT_ABSOLUTE_THRESHOLD  = 28   # %
```

---

## 💾 Requisitos e instalación

```bash
conda env create -f environment.yml   # crea entorno sugerido
conda activate fmri_qc                # o nombre que prefieras
# ó, versión mínima:
pip install -r requirements.txt
```

Paquetes principales: `numpy`, `pandas`, `scipy`, `scikit-learn`, `matplotlib`, `seaborn`, `nilearn`, `neuroCombat‑sklearn` (opcional).

---

## ▶️ Cómo ejecutar

```bash
python analisis_outliers_schaefer100_v4.0.py
```

Genera automáticamente el directorio de salida y los reportes.

---

## 📊 Resumen de resultados

* **Sujetos iniciales:** 434
* **Descartados:** 5 *(2 por univariantes ↑, 3 por multivariantes ↑)*
* **Muestra final:** **429 sujetos**

| Métrica            | Media ± DE    | Mediana \[RIC] |
| ------------------ | ------------- | -------------- |
| TPs originales     | 177 ± 28      | 197 \[140–197] |
| TPs post‑scrubbing | 171 ± 27      | 188 \[137–193] |
| % outliers univ    | 0.50 ± 0.26 % | 0.46 %         |
| % outliers MV      | 21.5 ± 6.0 %  | 25.4 %         |

> **Correlación** entre % outliers univ y TPs eliminados: ρ ≈ 0.63.

---

## 🗂️ Archivos de salida clave

* **report\_qc\_initial\_schaefer100.csv** – QC inicial por sujeto
* **tp\_length\_distribution.csv** – distribución de longitudes de serie
* **report\_qc\_full\_compiled\_schaefer100.csv** – QC + % outliers
* **report\_qc\_final\_with\_discard\_flags\_schaefer100.csv** – **flag de descarte** por criterio
* **plot\_\*.png** – histogramas, scatter y violin de QC

Directorio generado ↓

```text
qc_outputs_schaefer100_v4.0/
├─ csv & pkl
├─ figuras PNG
└─ …
```

---

## 🚀 Próximos pasos

1. Ajustar umbral MV → evaluar 30 % para salvar 3 sujetos borderline.
2. Armonizar conectividad entre sitios con **ComBat** y comparar distribuciones.
3. Documentar impacto del scrubbing sobre métricas dinámicas.
4. Migrar visualizaciones a Plotly/Dash (v5.4).

---

## 📚 Citar este trabajo

Si usas este pipeline, cita:

> *Apellido, D.* (2025). **QC y detección de outliers en rs‑fMRI con Schaefer‑100**. Repositorio GitHub. [https://github.com/usuario/repositorio](https://github.com/usuario/repositorio)

---

## 📝 Licencia

Este proyecto se distribuye bajo la licencia MIT. Consulta el archivo `LICENSE` para más detalles.
