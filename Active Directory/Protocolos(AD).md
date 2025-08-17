
### 🛡️ **Kerberos**

**Protocolo de autenticación por defecto** en dominios desde Windows 2000.    
Utiliza **tickets en lugar de contraseñas** para autenticación segura.
Componentes clave: 
    - **KDC (Key Distribution Center)**: Emite tickets.
    - **TGT (Ticket Granting Ticket)**: Solicitado al iniciar sesión.
    - **TGS (Ticket Granting Service)**: Permite acceder a servicios.
**Puerto 88 TCP/UDP**.   
No guarda estado: se basa en la validez del TGT.    
Evita la transmisión de contraseñas por la red.    

---

### 🌐 **DNS (Domain Name System)**

Esencial para que los clientes localicen Controladores de Dominio (DCs).
Usa **registros SRV** para encontrar servicios como archivos, impresoras y DCs.
Soporta **actualización dinámica de registros** si la IP cambia.
**Puertos 53 TCP/UDP**.
Fallas en DNS impiden la comunicación entre sistemas AD.

---

### 📂 **LDAP (Lightweight Directory Access Protocol)**

Protocolo utilizado por AD para búsquedas y autenticación.
Usa **puerto 389 (LDAP)** y **636 (LDAPS)**.
Permite a aplicaciones consultar usuarios, contraseñas y otros objetos.
Métodos de autenticación:
    - **Simple** (usuario/contraseña, incluso anónima).    
    - **SASL** (usa Kerberos u otros métodos externos).    
Recomendable usar **TLS/SSL** ya que los mensajes se transmiten en texto plano.

---

### 🔌 **MSRPC (Microsoft Remote Procedure Call)**

Comunicación entre procesos basada en el modelo cliente-servidor.
Interfaces clave:
    - **lsarpc**: Acceso a políticas de seguridad local.    
    - **netlogon**: Autenticación de usuarios y servicios.    
    - **samr**: Administración remota de cuentas (usuarios y grupos).   
    - **drsuapi**: Replicación entre DCs, usado para extraer el archivo NTDS.dit.
Utilizado tanto por administradores como por atacantes para **reconocimiento o ataque** al dominio.

### NTLM Authentication
Active Directory utiliza múltiples métodos de autenticación además de Kerberos y LDAP, como LM, NTLM, NTLMv1 y NTLMv2. LM y NTLM se refieren a tipos de hash, mientras que NTLMv1 y NTLMv2 son protocolos que los utilizan. Kerberos es más seguro y generalmente preferido por ofrecer autenticación mutua y usar criptografía simétrica y asimétrica.

### LM (LAN Manager)
Es el método más antiguo de almacenamiento de contraseñas, introducido en 1987. Tiene graves debilidades de seguridad: las contraseñas son convertidas a mayúsculas, limitadas a 14 caracteres, divididas en bloques de 7 y cifradas con DES. Es fácil de crackear con herramientas como Hashcat. Aunque está desactivado por defecto desde Windows Vista, aún se encuentra en entornos antiguos.

### NTLM (NT Hash)
Usa un protocolo de desafío-respuesta con tres mensajes: Negotiate, Challenge y Authenticate. El hash NT se calcula con MD4 sobre la versión UTF-16-LE de la contraseña. A pesar de ser más fuerte que LM, puede crackearse offline rápidamente con GPU y permite ataques de tipo pass-the-hash, donde no se necesita la contraseña en texto claro.

### NTLMv1 (Net-NTLMv1)
Usa tanto el hash NT como LM y realiza un intercambio con un desafío de 8 bytes. La respuesta se genera con DES utilizando tres claves derivadas del hash. Es vulnerable y no permite ataques pass-the-hash, pero sí puede ser crackeado fácilmente si se captura.

### NTLMv2 (Net-NTLMv2)
Introducido en Windows NT 4.0 SP4, mejora NTLMv1 al usar HMAC-MD5, desafíos aleatorios del cliente, tiempo y nombre de dominio. Proporciona mayor seguridad y resistencia a ataques de spoofing. Es el protocolo por defecto desde Windows 2000 y más difícil de crackear, aunque sigue siendo posible si las contraseñas son débiles.

### Domain Cached Credentials (MSCache2)
Permite el inicio de sesión en máquinas cuando no hay conexión con el Domain Controller. Guarda los últimos 10 hashes de usuarios que iniciaron sesión exitosamente en el registro de Windows. No se pueden usar para ataques pass-the-hash y son extremadamente lentos de crackear. Su formato es $DCC2$10240#usuario#hash, y se almacenan localmente en la clave de registro HKEY_LOCAL_MACHINE\SECURITY\Cache.