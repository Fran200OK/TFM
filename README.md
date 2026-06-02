# Caracterización de perfiles de tráfico en redes corporativas mediante Wavelets e Inteligencia Artificial

Este repositorio contiene el código fuente y la metodología experimental desarrollada para el Trabajo Fin de Máster en Ingeniería de Telecomunicación por la Universidad Autónoma de Madrid (UAM). 

El proyecto propone una arquitectura de monitorización agnóstica al *payload* para redes corporativas. Ante la ceguera analítica provocada por la adopción masiva del cifrado de extremo a extremo (TLS 1.3, QUIC), este sistema emplea el Análisis Multirresolución (*Wavelets*) y el Aprendizaje Automático (*Machine Learning*) para extraer la huella espectral de los nodos y clasificar su comportamiento (Usuarios vs. Servidores) analizando únicamente la morfología volumétrica de sus conexiones.

---

## Estructura del Repositorio

El código está diseñado como un *pipeline* secuencial de datos, dividido en cuatro fases principales. Los *scripts* están preparados para procesar registros de flujos agrupados (NetFlow/IPFIX).

* **`01_Preprocesamiento_TFM.ipynb`**: 
    Filtra los datos crudos mediante rangos CIDR configurables, agrupa los *bytes* en ventanas temporales equidistantes (ej. 5 minutos), imputa los periodos de inactividad técnica (*zero-padding*) y aplica una estandarización independiente por IP (*Z-Score*) para lograr la invarianza de escala.

* **`02_Extraccion_Wavelet.ipynb`**: 
    Aplica el motor matemático del proyecto. Realiza la descomposición espectral de las series de tiempo estandarizadas utilizando tanto la Transformada Wavelet Discreta (DWT: *Haar*, *Db2*) como la Continua (CWT: *Mexican Hat*), extrayendo vectores de Energía y Entropía de Shannon.

* **`03_Visualización_Wavelets.ipynb`**: 
    Herramienta de análisis exploratorio que genera representaciones gráficas en PDF de los coeficientes de aproximación, detalle y escalas continuas, permitiendo auditar la morfología de la señal antes de alimentar a la Inteligencia Artificial.

* **`04_Modelos_IA.ipynb`**: 
    Implementa y evalúa los algoritmos *Random Forest* y Perceptrón Multicapa (MLP) bajo un riguroso marco estadístico:
    * Balanceo de clases mediante *Undersampling*.
    * Validación cruzada adaptativa (*StratifiedKFold* para histórico mensual y *GroupKFold* para ventanas operativas de 1 hora).
    * Escalado dinámico dentro del bucle de validación para prevenir el *Data Leakage*.
    * Generación de matrices de confusión, curvas ROC y análisis de importancia de características (Impureza de Gini).

---

## Requisitos y Dependencias

El proyecto está desarrollado en **Python 3.8+**. Las librerías principales requeridas para ejecutar la arquitectura son:

```bash
pip install pandas numpy scipy pywavelets scikit-learn matplotlib seaborn tqdm

*   **A largo plazo (Histórico Mensual):** El modelo MLP, apoyado en métricas de Energía con la transformada *Haar*, alcanza precisiones F1-Score superiores al **99.6%**.
*   **A corto plazo (Ventanas de 1 hora):** El ensamblado *Random Forest*, apoyado en la transformada *Mexican Hat*, demuestra ser altamente resiliente al ruido y escasez de datos, alcanzando un F1-Score operativo del **87.3%** garantizando tiempos de respuesta viables para integración en un SOC.

---

##  Autor

*   **Francisco Orcha Kovacs** 
*   Universidad Autónoma de Madrid (UAM)
