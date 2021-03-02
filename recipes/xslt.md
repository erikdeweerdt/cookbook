# XSLT 2.0

## Basic structure
```xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- if you want some xml file to use this stylesheet for processing in the browser, add a processing instruction to the xml file (right below the xml declaration): <?xml-stylesheet type="text/xsl" href="stylesheet.xsl"?> -->
<!-- caveats: you can only use XSLT 1.0 and it doesn't work when using the file:// protocol -->
<xsl:stylesheet version="2.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <xsl:output method="xml" indent="yes"/>
  
  <xsl:template match="/">
  </xsl:template>
</xsl:stylesheet>
```

## Snippets
### Parse a collection of HTML files using tagsoup

* Requires the [tagsoup](https://mvnrepository.com/artifact/org.ccil.cowan.tagsoup/tagsoup) jar to be on the classpath when invoking Saxon
* Requires namespace declaration `xmlns:html="http://www.w3.org/1999/xhtml"`

```xml
<xsl:apply-templates select="collection(concat($root.uri, '?select=*.html;recurse=yes;parser=org.ccil.cowan.tagsoup.Parser'))/html:html"/>
```

### Function to check that a file exists

> Only works with Saxon

XPath built-ins `doc-available` and `unparsed-text-available` should only be used on XML and text files respectively; using them on binary data may yield unexpected results.

Requires namespace declarations:
* `xmlns:fn="urn:fn"` (this is user-defined and can be something different)
* `xmlns:file="java:java.io.File"`
* `xmlns:uri="java:java.net.URI"`

```xml
<xsl:function name="fn:file-exists">
  <xsl:param name="file.uri"/>
  <xsl:variable name="file" select="file:new(uri:new($file.uri))"/>
  <!-- a sequence is used to return a boolean. Using value-of would convert it into a string -->
  <xsl:sequence select="file:exists($file)"/>
</xsl:function>
<!-- example use -->
<xsl:value-of select="if (fn:file-exists($file.uri)) then 'EXISTS' else 'MISSING'"/>
```

### Signal saxon that a document can be discarded after use

> Only works with Saxon

Without this, Saxon caches every document and may run out of memory if large collections are processed.
The function returns the document node, so you can just wrap the call to document with it.

Requires namespace declaration `xmlns:saxon="http://saxon.sf.net/"`.

```xml
<xsl:apply-templates select="saxon:discard-document(document($uri))/root/element"/>
```

If XPath 3.0 is available, you can map every document returned by collection through this (or another) function by writing `collection()!discard-document(.)`.
