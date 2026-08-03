

# Acción de GitHub para Copia de Seguridad de Base de Datos de Supabase

Este repositorio contiene un flujo de trabajo de GitHub Actions para realizar automáticamente copias de seguridad de una base de datos PostgreSQL de Supabase.

## Resumen

El flujo de trabajo realiza los siguientes pasos:

1.  **Disparador programado**: Se ejecuta automáticamente en un horario específico.
2.  **Disparador manual**: Se puede ejecutar manualmente desde la pestaña de GitHub Actions.
3.  **Volcado de base de datos**: Volca la base de datos en tres archivos separados:
    *   `roles.sql`: Para los roles de la base de datos.
    *   `schema.sql`: Para el esquema de la base de datos.
    *   `data.sql`: Para los datos, utilizando `COPY` para mayor eficiencia.
4.  **Compresión**: Comprime los archivos SQL del volcado en un archivo `.tar.gz`.
5.  **Carga**: Carga el archivo comprimido a un bucket de Supabase Storage.

## Archivo del flujo de trabajo

El flujo de trabajo se define en `.github/workflows/db-backup.yml`.

## Programación

La copia de seguridad está programada para ejecutarse diariamente a las 17:00 UTC (00:00 WIB - Hora de Indonesia Occidental).

```yaml
on:
  schedule:
    - cron: "0 17 * * *" # 24:00 WIB (UTC+7)
```

## Configuración

Para utilizar este flujo de trabajo, debes configurar los siguientes secretos en la configuración de tu repositorio de GitHub (`Settings > Secrets and variables > Actions`):

*   `SUPABASE_DB_URL`: La cadena de conexión de tu base de datos de Supabase. Debe tener el formato `postgresql://postgres:[YOUR-PASSWORD]@[AWS-REGION].sql.supabase.co:5432/postgres`.
*   `SUPABASE_URL`: La URL de tu proyecto de Supabase.
*   `SUPABASE_SERVICE_ROLE_KEY`: La clave `service_role` de tu proyecto de Supabase.

El flujo de trabajo carga las copias de seguridad en un bucket de Supabase Storage llamado `db-backups`. Puedes cambiarlo editando la variable de entorno `BACKUP_BUCKET` en el archivo del flujo de trabajo.

## Disparador manual

Puedes ejecutar la copia de seguridad manualmente:

1.  Ve a la pestaña "Actions" de tu repositorio.
2.  Selecciona el flujo de trabajo "supabase-db-backup".
3.  Haz clic en el menú desplegable "Run workflow" y luego en "Run workflow".

## Estructura de la copia de seguridad

Las copias de seguridad se almacenan en el bucket `db-backups` con la siguiente estructura:

```
daily/YYYY-MM-DD/supabase-backup-YYYY-MM-DDTHH-MM-SSZ.tar.gz
```

*   `YYYY-MM-DD` corresponde a la fecha en horario WIB (UTC+7).
