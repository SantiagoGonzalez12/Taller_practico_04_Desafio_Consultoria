# Taller_practico_04_Desafio_Consultoria
Santiago González y Álvaro López

## Bloque A: Análisis de Mercado y Selección (CE a, c)
Debéis elegir entre Odoo (SaaS o Community), SAP S/4HANA o Zoho One.
Justifica la elección basándote en el perfil de la empresa (25 empleados, presupuesto ajustado, necesidad de personalización en el etiquetado).
Hemos elegido Odoo Community ya que, al ser una empresa de 25 empleados con un presupuesto ajustado, el software libre es ideal ya que elimina el tener que pagar por licencias de usuarios.
También permite la personalización del etiquetado y, al ser modular, permite tener integrado inventario, CRM y facturación de manera fluida. Esto elimina la necesidad de información redundante.
Cálculo de TCO: Realiza una estimación a 3 años. No olvidéis incluir:
Coste de licencias/suscripción.
Coste de implantación (vuestras horas de desarrollo: estima 100h a 40€/h).
Coste operativo (Hosting en Google Cloud, AWS, Huawei Cloud o similar).

Concepto
Detalles
Coste Año 1
Coste Año 2
Coste Año 3
Licencias / Suscripción
Odoo Community
0€
0€
0€
Implantación
100h x 40€/h
4000€
0€
0€
Operativo
Google Cloud
408€
408€
408€
TOTAL
4408€
408€
408€


Bloque B: Diseño de Seguridad RBAC (CE f)
Diseña la matriz de permisos para los siguientes roles, asegurando el Principio de Mínimo Privilegio:
Administrador: Acceso total.
Comercial: Solo ve sus clientes y presupuestos (Record Rules).
Operario de Almacén: Solo ve stock y albaranes de entrada/salida.
Contable: Puede mirar facturas pero no puede modificar el stock.
El diseño que vamos a basarnos es en dos capas de seguridad integradas dentro del ERP:
Por un lado Grupos de Usuario:
Determinamos si un usuario puede ver el menú de una aplicación ósea para que nosotros entendamos dentro de un ERP sería Contabilidad en comparación con Ventas
En Segundo Plano tendremos unas Reglas de Registro:
 Esto es como si fuera un filtro bastante dinámico. No basta con entrar en el apartado de ventas, lo que nos vamos a encargar en primer lugar es que esté regla de registro asegure que si el Comercial A no vea las ventas de su compañero el Comercial B.  
Seguridad Transversal:
Esta parte de lo que se va encargar principalmente es para evitar que se implemente un comercial, se asome por ejemplo se asome a costes de producción o a contabilidad, protegiendo así el margen de beneficio de la empresa 







ROL
CRUD
REGLA DE REGISTROS
JUSTIFICACIÓN
ADMINISTRADOR
Tendría el acceso total de todas las áreas, en este caso Ventas, Compras, Almacén, Contabilidad, Ajustes.
[‘(1, ‘=’,1)] | Acceso Global
Necesitaremos un control total para la gestión del sistema y una configuración de usuarios de todo el ERP
COMERCIAL
Limitado: Ventas y CRM. En este caso solo tendría la lectura de productos
[‘user_id’, ‘=’, user id | Solo sus propios registros
Evitamos que pueda ver clientes ajenos y se bloquea automáticamente el acceso a fabricación y facturas de contables.
OP.ALMACÉN
Limitado: Inventario y recepciones, en este caso estaría casi igual de limitado que el comercial lectura en productos
[‘loaction_id’, ‘child_of’, [Hogar]] | Solo su almacén 
Solo se gestiona stock y el tema de albaranes de la entrada y la salida. No tiene acceso a precios de venta ni datos fiscales.
CONTABLE
Limitado: Facturación y Pagos exclusivamente, tendría solo el acceso a la lectura en Contactos
[‘state’,’!=’, ‘draft’] | Solo facturas validadas
En esta sección se podra solo gestionar la tesorería de al empresa lo cual tiene n prohibido modificar el stock, evitando asi que descuandren el inventario fisico del almacen 




Bloque C: Documentación de Explotación (CE i)
Siguiendo la norma ISO/IEC 26514, redacta un breve Manual de Despliegue para que el responsable de IT de la empresa pueda levantar el sistema en caso de caída. Debe incluir:
El fragmento de docker-compose.yml necesario.
El comando para realizar un backup de la base de datos PostgreSQL.
