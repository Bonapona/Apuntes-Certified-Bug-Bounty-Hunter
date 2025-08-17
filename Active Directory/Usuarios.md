
### User & machines accounts

#### **Cuentas de Usuario**

- Se crean de forma **local** (en sistemas no unidos a un dominio) o en **Active Directory (AD)** para que una persona o servicio pueda iniciar sesión y acceder a recursos.
    
- Al iniciar sesión, el sistema genera un **token de acceso** con la identidad del usuario y sus pertenencias a grupos.
    
- Los **grupos** permiten una gestión más sencilla de permisos asignándolos una sola vez al grupo en lugar de a cada usuario individual.
    
- Las empresas suelen tener **una o más cuentas por usuario** (por ejemplo, cuentas administrativas o de servicio).
    
- Las cuentas inactivas (ex-empleados, temporales, etc.) suelen **desactivarse, no eliminarse**, y ubicarse en Unidades Organizativas como "FORMER EMPLOYEES".
    
- Las cuentas pueden tener desde permisos **básicos (usuarios de dominio)** hasta **control total (Enterprise Admin)**.
    
- Las **configuraciones incorrectas** representan un riesgo de seguridad importante y suelen ser objetivo en pruebas de penetración.
    
- Se recomienda tener **políticas y controles** robustos para mitigar los riesgos asociados al comportamiento humano y la mala gestión de cuentas.
    

#### **Cuentas Locales**

- Solo existen en el **sistema donde fueron creadas** y no tienen validez en todo el dominio.
    
- Cuentas comunes por defecto:
    
    - **Administrator**: Control total del sistema; no se puede eliminar.
        
    - **Guest**: Acceso limitado; desactivada por defecto.
        
    - **SYSTEM**: Usada por el sistema operativo; tiene los privilegios más altos.
        
    - **Network Service**: Usada por servicios de Windows; privilegios limitados, presenta credenciales en red.
        
    - **Local Service**: Privilegios mínimos; presenta credenciales anónimas en la red.
        

#### **Usuarios de Dominio**

- Pueden iniciar sesión en **cualquier equipo unido al dominio**.
    
- Tienen acceso a recursos como **archivos, impresoras, intranets**, según los permisos asignados a su cuenta o grupo.
    
- Cuentas especiales como **KRBTGT** son críticas para el sistema de autenticación y pueden ser objetivo de ataques como **Golden Ticket**.
    

#### **Atributos de Usuario en AD**

- **UserPrincipalName (UPN)**: Nombre de inicio de sesión (generalmente el correo).
    
- **ObjectGUID**: Identificador único del objeto.
    
- **SAMAccountName**: Compatible con versiones anteriores de Windows.
    
- **objectSID**: Identificador de seguridad del usuario y sus grupos.
    
- **sIDHistory**: Historial de SIDs (útil en migraciones de dominios).
    

#### **Máquinas unidas vs. no unidas al dominio**

- **Unidas al dominio**: Se gestionan centralizadamente a través del Controlador de Dominio (DC) y políticas de grupo.
    
- **No unidas (Workgroup)**: Son autónomas, adecuadas para entornos pequeños o domésticos; cada cuenta es local.
    

#### **Nivel de acceso de cuentas de máquina**

- Una cuenta de **máquina con nivel SYSTEM** en un equipo unido al dominio puede tener acceso de lectura amplio en el dominio.
    
- Esto permite comenzar ataques desde una posición privilegiada sin credenciales de usuario específicas.

### Grupos

#### Estructura

**type** -> define el proposito del grupo
	|-> hay dos: 
	**1.security**: se suele usar para dar permisos en conjunto y no usuario por usuario
	**2.distribution**: usado por email apps para mandar mensajes a los usuarios de un grupo
**scope** -> te dice como puede ser usado el grupo en el domain o forest
     |-> hay tres : 
     **1.Domain local**: manejan los permisos a los recursos del dominio en el que fueron creados, no pueden ser usados en otros dominios pero si pueden contener usuarios de otros dominios 
     **2.Global**: igual que local pero para otro los dominios lo único que solo tiene usuarios del dominio en el que fue creado
     **3.Universal**: para todos los dominios ademas dan permisos a cualquier objeto de su mismo forest