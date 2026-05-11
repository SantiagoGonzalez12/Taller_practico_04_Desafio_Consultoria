# Taller_practico_04_Desafio_Consultoria
Santiago González y Álvaro López

## Bloque A: Análisis de Mercado y Selección (CE a, c)
**Debéis elegir entre Odoo (SaaS o Community), SAP S/4HANA o Zoho One.
Justifica la elección basándote en el perfil de la empresa (25 empleados, presupuesto ajustado, necesidad de personalización en el etiquetado).**

Hemos elegido Odoo Community ya que, al ser una empresa de 25 empleados con un presupuesto ajustado, el software libre es ideal ya que elimina el tener que pagar por licencias de usuarios.

También permite la personalización del etiquetado y, al ser modular, permite tener integrado inventario, CRM y facturación de manera fluida. Esto elimina la necesidad de información redundante.

**Cálculo de TCO: Realiza una estimación a 3 años. No olvidéis incluir:
Coste de licencias/suscripción.
Coste de implantación (vuestras horas de desarrollo: estima 100h a 40€/h).
Coste operativo (Hosting en Google Cloud, AWS, Huawei Cloud o similar).**

| Concepto | Detalles | Coste Año 1 | Coste Año 2 | Coste Año 3 |
|----------|----------|-------------|-------------|-------------|
Licencias / Suscripción|Odoo Community|0€|0€|0€|
|Implantación|100h x 40€/h|4000€|0€|0€|
|Operativo|Google Cloud|408€|408€|408€|
|TOTAL||4408€|408€|408€|

<img width="400" height="126" alt="image" src="https://github.com/user-attachments/assets/4482302f-4c37-43f1-b55f-154bea15ee36" />

## Bloque B: Diseño de Seguridad RBAC (CE f)
**Diseña la matriz de permisos para los siguientes roles, asegurando el Principio de Mínimo Privilegio:
Administrador: Acceso total.
Comercial: Solo ve sus clientes y presupuestos (Record Rules).
Operario de Almacén: Solo ve stock y albaranes de entrada/salida.
Contable: Puede mirar facturas pero no puede modificar el stock.**

El diseño que vamos a basarnos es en dos capas de seguridad integradas dentro del ERP:

### Por un lado Grupos de Usuario:
Determinamos si un usuario puede ver el menú de una aplicación ósea para que nosotros entendamos dentro de un ERP sería Contabilidad en comparación con Ventas

### En Segundo Plano tendremos unas Reglas de Registro:
Esto es como si fuera un filtro bastante dinámico. No basta con entrar en el apartado de ventas, lo que nos vamos a encargar en primer lugar es que esté regla de registro asegure que si el Comercial A no vea las ventas de su compañero el Comercial B.  

### Seguridad Transversal:
Esta parte de lo que se va encargar principalmente es para evitar que se implemente un comercial, se asome por ejemplo se asome a costes de producción o a contabilidad, protegiendo así el margen de beneficio de la empresa 

<img width="2595" height="1657" alt="image" src="https://github.com/user-attachments/assets/442d37a5-ae18-4271-ae4c-794c092a52be" />

---
| Rol | ¿Qué puede hacer? (CRUD) | Regla de Registro (Filtro) | Justificación |
| :--- | :--- | :--- | :--- |
| **Administrador** | Tendría el acceso total de todas las áreas, en este caso Ventas, Compras, Almacén, Contabilidad, Ajustes. | `['(1', '=', '1')]` | Necesitaremos un control total para la gestión del sistema y una configuración de usuarios de todo el ERP |
| **Comercial** | Limitado: Ventas y CRM. En este caso solo tendría la lectura de productos | `['user_id', '=', user.id]` |Evitamos que pueda ver clientes ajenos y se bloquea automáticamente el acceso a fabricación y facturas de contables. |
| **Op. Almacén** | Limitado: Inventario y recepciones, en este caso estaría casi igual de limitado que el comercial lectura en productos | `['location_id', 'child_of', [Hogar]]` |Solo se gestiona stock y el tema de albaranes de la entrada y la salida. No tiene acceso a precios de venta ni datos fiscales. |
| **Contable** | Limitado: Facturación y Pagos exclusivamente, tendría solo el acceso a la lectura en Contactos | `['state', '!=', 'draft']` |En esta sección se podra solo gestionar la tesorería de al empresa lo cual tiene n prohibido modificar el stock, evitando asi que descuandren el inventario fisico del almancen |


<img width="1110" height="364" alt="image" src="https://github.com/user-attachments/assets/8dc86a56-1fcb-4995-a331-2e3ecabbbf1b" />


## Bloque C: Documentación de Explotación (CE i)
**Siguiendo la norma ISO/IEC 26514, redacta un breve Manual de Despliegue para que el responsable de IT de la empresa pueda levantar el sistema en caso de caída. Debe incluir:**

**El fragmento de docker-compose.yml necesario.**

```
services:
  odoo:
    image: odoo:latest
    container_name: odoo
    restart: unless-stopped
    depends_on:
      - db
    ports:
      - "8200:8069"
    volumes:
      - odoo-data:/var/lib/odoo
      - ./config:/etc/odoo
      - ./addons:/mnt/extra-addons
    environment:
      - HOST=db
      - USER=odoo
      - PASSWORD=odoo
    command: odoo -d odoo --db_user=odoo --db_password=odoo -i base
 
  db:
    image: postgres:16.0
    container_name: db
    restart: unless-stopped
    environment:
      - POSTGRES_DB=odoo
      - POSTGRES_PASSWORD=odoo
      - POSTGRES_USER=odoo
      - PGDATA=/var/lib/postgresql/data/pgdata
    volumes:
      - db-data:/var/lib/postgresql/data


volumes:
  odoo-data:
  db-data:
```

**El comando para realizar un backup de la base de datos PostgreSQL.**

Para crear una copia de seguridad de bases de datos pequeñas utilizamos la herramienta pg_dump:

`pg_dump -U username -d database_name > backup_file.sql`

Para bases de datos más grandes usamos pg_basebackup:

`pg_basebackup -D /backup/directory -Ft -z -P`


## Webgrafía
[1]	«Principales características y beneficios de Odoo», Navegasoft, 18-jul-2024. [En línea]. Disponible en: https://www.navegasoft.com/blog/odoo-caracteristicas-y-comparacion-10/principales-caracteristicas-y-beneficios-de-odoo-24. [Accedido: 11-may-2026].

[2]	«¿Cuál sería el costo de un servidor de Google Cloud para hospedar Google Tag Manager del lado del servidor para un sitio web con un tráfico de alrededor de 5000 visitantes por mes?», Reddit.com. [En línea]. Disponible en: https://www.reddit.com/r/googlecloud/comments/yro9vn/what_would_be_the_cost_of_google_cloud_server_to/?tl=es-419. [Accedido: 11-may-2026].

[3]	«Reglas y permisos de acceso en Odoo», Sygel, 21-nov-2022. [En línea]. Disponible en: https://www.sygel.es/blog/blog-de-sygel-2/reglas-y-permisos-de-acceso-en-odoo-65. [Accedido: 11-may-2026].

[4]	Cloudflare.com. [En línea]. Disponible en: https://www.cloudflare.com/es-es/learning/access-management/role-based-access-control-rbac/. [Accedido: 11-may-2026].

[5]	Trilio.io. [En línea]. Disponible en: https://trilio.io/es/digitales/Copia-de-seguridad-de-la-base-de-datos-de-Postgres/. [Accedido: 11-may-2026].
