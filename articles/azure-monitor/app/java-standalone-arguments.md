---
title: Övervaka Java-program som körs i valfri miljö – Azure Monitor Application Insights
description: Övervakning av program prestanda för Java-program som körs på alla miljöer med fristående Java-agent utan att instrumentera appen. Distribuerad spårning och program karta.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 527f1eaf04be7b5e8c89c12912a06d2f5d50321f
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82508045"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurera JVM args Java standalone agent för Azure Monitor Application Insights



## <a name="azure-environments"></a>Azure-miljöer

Konfigurera [app Services](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Lägg till JVM- `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` arg någonstans `-jar`före, till exempel:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="spring-boot-via-docker-entry-point"></a>Våren Boot via Docker start punkt

Om du använder formuläret *ledn* lägger du till parametern `"-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"` i parameter listan någonstans före `"-jar"` parametern, till exempel:

```
ENTRYPOINT ["java", "-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar", "-jar", "<myapp.jar>"]
```

Om du använder *Shell* -formuläret lägger du till JVM-arg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` någonstans före `-jar`, till exempel:

```
ENTRYPOINT java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat installerat via `apt-get` eller`yum`

Om du har installerat Tomcat `apt-get` via `yum`eller, bör du ha en- `/etc/tomcat8/tomcat8.conf`fil.  Lägg till den här raden i slutet av filen:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat installerat via hämta och zippa upp

Om du har installerat Tomcat via hämta och zippa [https://tomcat.apache.org](https://tomcat.apache.org)upp, bör du ha en- `<tomcat>/bin/catalina.sh`fil.  Skapa en ny fil i samma katalog med namnet `<tomcat>/bin/setenv.sh` med följande innehåll:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Om filen `<tomcat>/bin/setenv.sh` redan finns ändrar du filen och lägger till `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` i. `CATALINA_OPTS`


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Köra Tomcat från kommando raden

Leta upp filen `<tomcat>/bin/catalina.bat`.  Skapa en ny fil i samma katalog med namnet `<tomcat>/bin/setenv.bat` med följande innehåll:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Citationstecken behövs inte, men om du vill inkludera dem är rätt placering:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Om filen `<tomcat>/bin/setenv.bat` redan finns ändrar du bara filen och lägger till `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` i. `CATALINA_OPTS`

### <a name="running-tomcat-as-a-windows-service"></a>Köra Tomcat som en Windows-tjänst

Leta upp filen `<tomcat>/bin/tomcat8w.exe`.  Kör den körbara filen `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` och Lägg `Java Options` till på `Java` fliken.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Fristående server

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` till i den `JAVA_OPTS` befintliga miljö variabeln i `JBOSS_HOME/bin/standalone.conf` filen (Linux) `JBOSS_HOME/bin/standalone.conf.bat` eller (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Domän Server

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` till i den `jvm-options` befintliga `JBOSS_HOME/domain/configuration/host.xml`i:

```xml
...
<jvms>
    <jvm name="default">
        <heap size="64m" max-size="256m"/>
        <jvm-options>
            <option value="-server"/>
            <!--Add Java agent jar file here-->
            <option value="-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"/>
            <option value="-XX:MetaspaceSize=96m"/>
            <option value="-XX:MaxMetaspaceSize=256m"/>
        </jvm-options>
    </jvm>
</jvms>
...
```

Om du kör flera hanterade servrar på en enda värd måste du lägga till `applicationinsights.agent.id` i `system-properties` för varje: `server`

```xml
...
<servers>
    <server name="server-one" group="main-server-group">
        <!--Edit system properties for server-one-->
        <system-properties> 
            <property name="applicationinsights.agent.id" value="..."/>
        </system-properties>
    </server>
    <server name="server-two" group="main-server-group">
        <socket-bindings port-offset="150"/>
        <!--Edit system properties for server-two-->
        <system-properties>
            <property name="applicationinsights.agent.id" value="..."/> 
        </system-properties>
    </server>
</servers>
...
```

Det angivna `applicationinsights.agent.id` värdet måste vara unikt. Den används för att skapa en under katalog under katalogen applicationinsights, eftersom varje JVM-process behöver sin egen lokala applicationinsights config och den lokala applicationinsights-logg filen. Om du rapporterar till Central insamlaren delas `applicationinsights.properties` filen av de flera hanterade servrarna, och därför krävs det `applicationinsights.agent.id` att du åsidosätter `agent.id` inställningen i den delade filen. `applicationinsights.agent.rollup.id`kan anges på samma sätt i serverns `system-properties` om du behöver åsidosätta `agent.rollup.id` inställningen per hanterad server.


## <a name="jetty-9"></a>Jetty 9

Lägg till dessa rader i`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` till i den `jvm-options` befintliga `glassfish/domains/domain1/config/domain.xml`i:

```xml
...
<java-config ...>
    <!--Edit the JVM options here-->
    <jvm-options>
        -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar>
    </jvm-options>
        ...
</java-config>
...
```

## <a name="websphere-8"></a>WebSphere 8

Öppna hanterings konsolen gå till **servrar > WebSphere program servrar > program servrar**, Välj lämpliga program servrar och klicka på: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
I "allmänna JVM-argument" lägger du till följande:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Efter det sparar och startar du om program servern.


## <a name="openliberty-18"></a>Openfrihet 18

Skapa en ny fil `jvm.options` i Server katalogen (till exempel `<openliberty>/usr/servers/defaultServer`) och Lägg till den här raden:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
