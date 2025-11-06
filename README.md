# 📊 Sesiones Abiertas por Pushes - Analytics GCBA

Script para analizar y reportar sesiones de chatbot iniciadas por WhatsApp Templates (pushes) desde AWS Athena, generando reportes automatizados en formato Excel Dashboard para el Gobierno de la Ciudad de Buenos Aires.

## 📝 Descripción

Este script consulta la base de datos `boti_session_metrics_2` en AWS Athena para obtener métricas de sesiones agrupadas por `starting_cause`, identificando específicamente las sesiones iniciadas mediante **WhatsAppTemplate** (pushes enviados a usuarios).

El resultado se exporta en:
- **CSV**: Datos completos con todas las causas de inicio
- **Excel Dashboard**: Reporte estructurado con el valor en la celda D4

## 🎯 Contexto GCBA

Este script forma parte del ecosistema de analytics para el chatbot BOTI del Gobierno de la Ciudad de Buenos Aires, complementando otros reportes mensuales de métricas web y conversacionales.

### Indicador medido
**Sesiones abiertas por Pushes**: Cantidad de sesiones que se iniciaron como resultado de un mensaje push (WhatsAppTemplate) enviado proactivamente a usuarios.

## 🔧 Requisitos

### Credenciales AWS
- **Rol requerido**: `PIBAConsumeBoti`
- **Workgroup**: `Production-caba-piba-athena-boti-group`
- **Database**: `caba-piba-consume-zone-db`
- **Región**: `us-east-1`

### Dependencias Python
```bash
pip install boto3
pip install awswrangler
pip install pandas
pip install openpyxl
```

### Herramienta de autenticación
```bash
npm install -g aws-azure-login
```

## ⚙️ Configuración

### 1. Configurar AWS Azure Login
```bash
aws-azure-login --configure --profile default
```

### 2. Autenticarse con el rol correcto
```bash
aws-azure-login --profile default --mode=gui
```
⚠️ **Importante**: Seleccionar el rol **PIBAConsumeBoti** durante la autenticación.

### 3. Configurar fechas
El script crea automáticamente un archivo `config_fechas.txt` la primera vez que se ejecuta. También podés crearlo manualmente:

```txt
# Configuracion de fecha para filtro automatico
# Formato: MES=numero del mes (1-12)
# Formato: AÑO=año completo (ej: 2024)

MES=9
AÑO=2024
```

## 🚀 Uso

### Ejecución básica
```bash
python Pushes_Abiertas.py
```

### Ejecución desde IPython/Spyder
```python
%runfile C:/ruta/a/Pushes_Abiertas.py
```

### Cambiar mes/año
1. Editar `config_fechas.txt`
2. Modificar valores de `MES` y `AÑO`
3. Volver a ejecutar el script

## 📂 Estructura de archivos

```
Sesiones_Abiertas_Pushes/
│
├── Pushes_Abiertas.py          # Script principal
├── config_fechas.txt            # Configuración de mes/año (auto-generado)
├── README.md                    # Este archivo
│
└── output/                      # Carpeta de salida (auto-generada)
    ├── pushes_abiertas_septiembre_2024.csv
    └── pushes_abiertas_septiembre_2024.xlsx
```

## 📊 Query ejecutada

```sql
SELECT starting_cause, count(distinct (session_id)) as Cant_sesiones 
FROM "caba-piba-consume-zone-db"."boti_session_metrics_2"   
WHERE CAST(session_creation_time AS DATE) BETWEEN date '2024-09-01' and date '2024-09-30' 
GROUP BY starting_cause
```

La query filtra automáticamente por el rango de fechas del mes especificado en `config_fechas.txt`.

## 📈 Salida

### Console Output
```
============================================================
RESULTADOS - SEPTIEMBRE 2024
============================================================

Desglose por starting_cause:
  WhatsAppTemplate: 1,234
  user: 5,678
  other: 890

============================================================
SESIONES ABIERTAS POR PUSHES (WhatsAppTemplate): 1,234
============================================================
```

### Archivos generados

#### 1. CSV (`pushes_abiertas_septiembre_2024.csv`)
Contiene todas las filas retornadas por la query con columnas:
- `starting_cause`: Tipo de inicio de sesión
- `Cant_sesiones`: Cantidad de sesiones únicas

#### 2. Excel Dashboard (`pushes_abiertas_septiembre_2024.xlsx`)
Hoja "Dashboard" con estructura completa de indicadores GCBA donde:
- **Celda D4**: Contiene el valor de sesiones con `starting_cause = 'WhatsAppTemplate'`
- Resto de celdas: Estructura preparada para otros indicadores

| Indicador | Descripción/Detalle | sep-24 |
|-----------|---------------------|--------|
| Conversaciones | Q Conversaciones | |
| Usuarios | Q Usuarios únicos | |
| **Sesiones abiertas por Pushes** | **Q Sesiones que se abrieron con una Push** | **1,234** |
| Sesiones Alcanzadas por Pushes | Q Sesiones que recibieron al menos 1 Push | |
| ... | ... | |

## 🔍 Troubleshooting

### Error: "Rol actual no es PIBAConsumeBoti"
```bash
aws-azure-login --profile default --mode=gui
# Seleccionar PIBAConsumeBoti durante la autenticación
```

### Error: "ExpiredToken"
```bash
# Tu sesión AWS expiró, volver a autenticar
aws-azure-login --profile default --mode=gui
```

### Error: "No se encontró 'WhatsAppTemplate' en starting_cause"
- Verificar que existan datos para el mes especificado
- Revisar que el campo `starting_cause` contenga el valor esperado
- El script usará `0` si no encuentra WhatsAppTemplate

### Error: "Workgroup not found"
El script intentará ejecutar sin especificar workgroup automáticamente.

## 🔗 Proyectos relacionados

- [Metricas_Web_Mensual](https://github.com/EdVeralli/Metricas_Web_Mensual) - Analytics GA4 para sitios GCBA
- Otros scripts de métricas BOTI del ecosistema GCBA

## 👤 Autor

**Damián Veralli**  
Data Scientist - Gobierno de la Ciudad de Buenos Aires (GCBA)

## 📄 Licencia

Este proyecto es de uso interno del GCBA para reportes de métricas del chatbot BOTI.

## 📞 Soporte

Para consultas sobre este script o métricas del chatbot BOTI:
- Contactar al equipo de Data Analytics GCBA
- Verificar permisos de acceso a AWS Athena con rol PIBAConsumeBoti

---

**Última actualización**: Noviembre 2024  
**Versión**: 1.0
