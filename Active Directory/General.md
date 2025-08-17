
### Estructura
#### 🧱 Objeto

Cualquier recurso en un entorno AD (usuarios, computadoras, impresoras, OUs, controladores de dominio, etc.).

#### 🏷️ Atributos

Propiedades de un objeto, como nombre de host o nombre completo. Se identifican con nombres LDAP (ej. `displayName`).

#### 📐 Esquema

Define los tipos de objetos que pueden existir y sus atributos. Cada objeto es una **instancia** de una **clase** (ej. un usuario es una instancia de la clase "user").

#### 🌐 Dominio

Grupo lógico de objetos AD. Se puede comparar con una ciudad. Los dominios pueden tener relaciones de confianza entre sí.

#### 🌲 Bosque (Forest)

Colección de uno o más dominios AD. Es el contenedor más alto. Se puede ver como un estado o país.

#### 🌳 Árbol

Grupo de dominios relacionados jerárquicamente que comparten un nombre de espacio. Todos los dominios en un árbol comparten un catálogo global.

#### 📂 Contenedor

Objeto que puede contener otros objetos (ej. una OU).

#### 🍃 Hoja

Objeto que **no** contiene otros objetos, como un usuario.

#### 🆔 GUID (Identificador Global Único)

Valor único de 128 bits que identifica internamente a un objeto AD. No cambia y es confiable para búsquedas.

#### 👤 Principios de Seguridad

Cualquier entidad que pueda ser autenticada (usuarios, cuentas de computadora, servicios).

#### 🔐 SID (Identificador de Seguridad)

Identificador único para usuarios o grupos. Nunca se reutiliza y es clave en la autenticación.

#### 📍 DN (Nombre Distinguido)

Ruta completa a un objeto en AD. Ej: `cn=bjones, ou=IT, dc=empresa, dc=local`.

#### 🧩 RDN (Nombre Distinguido Relativo)

Parte del DN que identifica un objeto en su contenedor. Ej: `bjones`.

#### 👥 sAMAccountName

Nombre de inicio de sesión del usuario (máx. 20 caracteres). Ej: `bjones`.

#### ✉️ userPrincipalName (UPN)

Otro formato de nombre de usuario: `usuario@dominio.local`.

#### 🛠️ Roles FSMO

Roles maestros que ayudan a mantener la integridad y consistencia de AD. Son cinco:

- **Schema Master:** Controla el esquema del AD (atributos de objetos).
    
- **Domain Naming Master:** Evita nombres duplicados de dominios en un bosque.
    
- **RID Master:** Asigna bloques de identificadores únicos (RIDs) para evitar duplicados.
    
- **PDC Emulator:** Responde autenticaciones, cambios de contraseña, administra políticas y sincroniza tiempo.
    
- **Infrastructure Master:** Traduce identificadores entre dominios en un bosque para mantener coherencia.

#### 📖 Catálogo Global (GC)

Controlador que guarda una copia completa de su dominio y parcial de los demás, permitiendo búsquedas en todo el bosque.

#### 🚫 RODC (Controlador de Dominio de Solo Lectura)

Solo lectura. No replica cambios, ideal para sitios remotos. No almacena contraseñas por defecto.

#### 🔁 Replicación

Proceso de sincronización de cambios entre controladores de dominio. Usa el servicio **KCC** para gestionar las conexiones.

#### 🎫 SPN (Nombre Principal del Servicio)

Identifica servicios para autenticación Kerberos.

#### 📋 GPO (Objeto de Directiva de Grupo)

Conjunto de configuraciones aplicables a usuarios o computadoras. Se identifican por GUID.

#### 🗂️ ACL (Lista de Control de Acceso)

Conjunto de reglas (ACEs) que indican quién puede acceder a qué.

#### 🎟️ ACE (Entrada de Control de Acceso)

Elemento dentro de una ACL que indica derechos para un usuario/grupo.

#### ⚖️ DACL

Define quién puede (o no) acceder a un objeto. Si no hay DACL, se permite acceso total.

#### 📓 SACL

Usada para registrar intentos de acceso a objetos, útil para auditoría.

#### 🌍 FQDN (Nombre de Dominio Totalmente Calificado)

Nombre completo de un host. Ej: `dc01.inlanefreight.local`.

#### 🪦 Tombstone

Objeto eliminado pero aún recuperable por un tiempo limitado (60–180 días).

#### 🗑️ Papelera de Reciclaje de AD

Permite restaurar objetos eliminados **con sus atributos**. Más eficiente que restaurar desde copia de seguridad.

#### 📁 SYSVOL

Carpeta que almacena scripts y configuraciones de políticas de grupo. Se replica a todos los DCs.

#### 🛡️ AdminSDHolder

Controla permisos para cuentas privilegiadas. Protege cuentas críticas aplicando ACLs mediante el proceso **SDProp**.

#### ⚙️ dsHeuristics

Atributo que permite configurar comportamientos avanzados de AD (ej. excluir grupos de AdminSDHolder).

#### 🔢 adminCount

Si se establece en `1`, el usuario es considerado privilegiado y se le aplican protecciones especiales.

#### 🧑‍💼 ADUC

Consola gráfica para administrar objetos de AD (usuarios, grupos, etc.).

#### 🧠 ADSI Edit

Herramienta avanzada para editar directamente los atributos de objetos AD. Uso delicado.

#### 📜 sIDHistory

Almacena antiguos SIDs de un objeto (útil para migraciones). Puede ser abusado si no se filtra correctamente.

#### 💾 NTDS.DIT

Base de datos que contiene toda la información del dominio, incluidas contraseñas. Es crítico para la seguridad.

#### 📡 MSBROWSE

Protocolo obsoleto que servía para descubrir recursos en la red en versiones antiguas de Windows.
### Objetos

#### 🔐 Objetos principales de seguridad (tienen SID y pueden tener permisos)

👤 Usuarios

    Representan personas con cuentas en AD.

    Leaf objects (no contienen otros objetos).

    Atributos: nombre, último login, email, etc.

    Clave para ataques, incluso con privilegios bajos.

💻 Computadoras

    Equipos unidos al dominio (servidores o estaciones).

    Leaf objects.

    También tienen SID y GUID.

    Pueden ser usados por atacantes para realizar enumeración.

👥 Grupos

    Objetos contenedor: pueden incluir usuarios, computadoras u otros grupos (grupos anidados).

    Usados para delegar permisos de manera eficiente.

    Clave en auditorías y análisis de rutas de ataque (ej. con BloodHound).

🌐 Dominios

    Unidad estructural principal que contiene todos los objetos.

    Tiene su propia base de datos y políticas (como política de contraseñas).

    Organiza objetos en OUs y grupos.

🧠 Controladores de Dominio (DCs)

    Servidores que manejan la autenticación y políticas de seguridad.

    Valida accesos y gestiona objetos del dominio.

#### 📦 Objetos sin privilegios de seguridad (sin SID)

📇 Contactos

    Representan usuarios externos.

    Solo tienen un GUID.

    Ejemplo: proveedor o cliente.

🖨️ Impresoras

    Puntos de acceso a impresoras en la red.

    Atributos: nombre, driver, puerto.

📁 Carpetas Compartidas

    Acceso a carpetas en red.

    Control de acceso personalizado: público, solo autenticados o acceso restringido.

#### 🗂️ Objetos contenedor (almacenan otros objetos)

🏢 Organizational Units (OUs)

    Contenedores para organizar objetos (por ejemplo, por departamentos).

    Permiten delegar tareas administrativas de forma precisa.

    Útiles para aplicar políticas de grupo (GPOs) a subconjuntos.

📌 Sites

    Agrupan equipos en una o más subredes conectadas por enlaces rápidos.

    Optimizan la replicación entre controladores de dominio.

📁 Built-in

    Contenedor que almacena grupos predeterminados del dominio (ej. Administrators, Guests, etc.).

#### 🌍 Objetos de seguridad externa

🌐 Foreign Security Principals (FSPs)

    Representan usuarios, grupos o equipos de otros bosques confiables.

    Tienen un SID, pero son objetos "marcadores" (placeholders).

    Se crean automáticamente al agregar usuarios externos a grupos locales.


### Funcionamiento

#### Niveles Funcionales de Dominio y Bosque

Determinan características disponibles y sistemas operativos compatibles en controladores de dominio.

    Desde Windows 2000 hasta Windows Server 2016, cada nivel añade funcionalidades nuevas (grupos universales, políticas avanzadas, Kerberos mejorado, etc.).

    Los niveles funcionales de bosque introducen capacidades como trusts entre bosques, controladores de solo lectura, y la papelera de reciclaje de AD.

    Windows Server 2019 no añade un nuevo nivel funcional pero requiere mínimo nivel 2008 y uso de DFS-R para replicación.

#### Trusts (Confianzas)
Permiten que usuarios de un dominio o bosque accedan a recursos de otro, mediante enlaces de autenticación.

    Tipos incluyen: Parent-child (padre-hijo), Cross-link (entre hijos), External (entre dominios externos), Tree-root (entre árboles) y Forest (entre bosques).

    Pueden ser transitivos (extienden confianza a objetos de dominios hijos) o no transitivos.

    Pueden ser unidireccionales o bidireccionales (acceso mutuo o solo en una dirección).

    Configuraciones incorrectas pueden crear riesgos de seguridad y caminos de ataque, especialmente tras fusiones o adquisiciones.