# Apache Ant

## Basic build.xml structure
```xml
<project name="myproject" default="build" xmlns:if="ant:if" xmlns:unless="ant:unless" xmlns:ivy="antlib:org.apache.ivy.ant" xmlns:rsel="antlib:org.apache.tools.ant.types.resources.selectors">
  <target name="build">
  </target>
</project>
```

## Snippets
### Include a collection of custom tasks & types (see antlib.xml at the bottom for an example)
```xml
<!-- tasks can be called with the namespace prefix for the uri (see root project element), e.g. <me:mytask>...</me:mytask> -->
<!-- if uri is omitted, tasks will be placed in the default namespace -->
<taskdef classpathref="mylib.classpath" resource="org/me/antlib.xml" uri="antlib:org.me"/>
```

### Run javascript and interact with the project (only possible for as long nashorn is in Java)
```xml
<script language="javascript">
  <![CDATA[
  var foo = project.getProperty("foo").toLowerCase();
  var bar = project.getProperty("bar").toLowerCase();
  project.setProperty("foo_bar", foo + "_" + bar);
  ]]>
</script>
```

### Select a single file from a fileset
```xml
<restrict>
  <fileset refid="files"/>
  <rsel:name name="file.zip"/>
</restrict>
```

### Recursively remove empty directories
```xml
<delete includeemptydirs="true">
  <fileset dir="${my.dir}" includes="*/**">
    <and>
      <size value="0"/>
      <type type="dir"/>
    </and>
  </fileset>
</delete>
```

## antlib.xml for custom Ant tasks
```xml
<?xml version="1.0"?>
<!-- example antlib.xml file for use with custom ant tasks -->
<antlib>
  <!-- public class MyTask extends Task -->
  <taskdef name="mytask" classname="org.me.MyTask"/>
  <!-- public class MyCondition extends ProjectComponent implements Condition -->
  <typedef name="mycondition" onerror="ignore" classname="org.me.MyCondition"/>
</antlib>
```
