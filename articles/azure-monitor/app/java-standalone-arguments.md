---
title: Övervaka Java-program som körs i alla miljöer – Azure Monitor Application Insights
description: Programprestandaövervakning för Java-program som körs på alla miljöer med Java-fristående agent utan att instrumentera appen. Distribuerad spårning och programkarta.
ms.topic: conceptual
ms.date: 04/16/2020
ms.openlocfilehash: 08a83fbc05276808b62a0391a5c4217cc09f6d00
ms.sourcegitcommit: d791f8f3261f7019220dd4c2dbd3e9b5a5f0ceaf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2020
ms.locfileid: "81641880"
---
# <a name="configuring-jvm-args-java-standalone-agent-for-azure-monitor-application-insights"></a>Konfigurera JVM args Java fristående agent för Azure Monitor Application Insights



## <a name="azure-environments"></a>Azure-miljöer

Konfigurera [App Services](https://docs.microsoft.com/azure/app-service/configure-language-java#set-java-runtime-options).

## <a name="spring-boot"></a>Spring Boot

Lägg till JVM `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `-jar <myapp.jar>`arg någonstans innan , till exempel:

```
java -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar -jar <myapp.jar>
```

> [!NOTE]
> Args placeras `-jar <myapp.jar>` efter skickas till appen som program args.


## <a name="tomcat-8-linux"></a>Tomcat 8 (Linux)

### <a name="tomcat-installed-via-apt-get-or-yum"></a>Tomcat installeras `apt-get` via eller`yum`

Om du har `apt-get` installerat `yum`Tomcat via eller `/etc/tomcat8/tomcat8.conf`så ska du ha en fil .  Lägg till den här raden i slutet av filen:

```
JAVA_OPTS="$JAVA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

### <a name="tomcat-installed-via-download-and-unzip"></a>Tomcat installeras via nedladdning och packa upp

Om du installerade Tomcat via nedladdning [https://tomcat.apache.org](https://tomcat.apache.org)och packa upp `<tomcat>/bin/catalina.sh`från , så ska du ha en fil .  Skapa en ny fil i `<tomcat>/bin/setenv.sh` samma katalog med följande innehåll:

```
CATALINA_OPTS="$CATALINA_OPTS -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Om filen `<tomcat>/bin/setenv.sh` redan finns ändrar du `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`filen och lägger till i .


## <a name="tomcat-8-windows"></a>Tomcat 8 (Windows)

### <a name="running-tomcat-from-the-command-line"></a>Köra Tomcat från kommandoraden

Leta reda `<tomcat>/bin/catalina.bat`på filen .  Skapa en ny fil i `<tomcat>/bin/setenv.bat` samma katalog med följande innehåll:

```
set CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```

Citat är inte nödvändigt, men om du vill inkludera dem, är rätt placering:

```
set "CATALINA_OPTS=%CATALINA_OPTS% -javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar"
```

Om filen `<tomcat>/bin/setenv.bat` redan finns ändrar du `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` `CATALINA_OPTS`bara filen och lägger till i .

### <a name="running-tomcat-as-a-windows-service"></a>Köra Tomcat som windows-tjänst

Leta reda `<tomcat>/bin/tomcat8w.exe`på filen .  Kör den körbara `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` filen `Java Options` och `Java` lägg till under fliken.


## <a name="jboss-eap-7"></a>JBoss EAP 7

### <a name="standalone-server"></a>Fristående server

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` till `JAVA_OPTS` den befintliga miljövariabeln i filen `JBOSS_HOME/bin/standalone.conf` (Linux) eller `JBOSS_HOME/bin/standalone.conf.bat` (Windows):

```java    ...
    JAVA_OPTS="<b>-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar</b> -Xms1303m -Xmx1303m ..."
    ...
```

### <a name="domain-server"></a>Domänserver

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` till `jvm-options` i `JBOSS_HOME/domain/configuration/host.xml`den befintliga i:

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

Om du kör flera hanterade servrar på en enda `applicationinsights.agent.id` värd `system-properties` måste `server`du lägga till för varje:

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

Det angivna `applicationinsights.agent.id` värdet måste vara unikt. Den används för att skapa en underkatalog under applicationinsights-katalogen, eftersom varje JVM-process behöver sin egen lokala applicationinsights config och lokala applicationinsights loggfil. Om du rapporterar till den `applicationinsights.properties` centrala insamlaren delas filen av flera `applicationinsights.agent.id` hanterade servrar, `agent.id` och därför krävs det angivna för att åsidosätta inställningen i den delade filen. `applicationinsights.agent.rollup.id`kan anges på samma sätt i `system-properties` serverns om `agent.rollup.id` du behöver åsidosätta inställningen per hanterad server.


## <a name="jetty-9"></a>Brygga 9

Lägg till dessa rader i`start.ini`

```
--exec
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```


## <a name="payara-5"></a>Payara 5

Lägg `-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar` till `jvm-options` i `glassfish/domains/domain1/config/domain.xml`den befintliga i:

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

Open Management Console gå till **servrar > WebSphere programservrar > Application-servrar,** välj lämpliga programservrar och klicka på: 

```
Java and Process Management > Process definition >  Java Virtual Machine
```
I "Allmänna JVM-argument" lägger du till följande:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
Efter det sparar och startar du om programservern.


## <a name="openliberty-18"></a>OpenLiberty 18

Skapa en `jvm.options` ny fil i serverkatalogen (till exempel `<openliberty>/usr/servers/defaultServer`) och lägg till den här raden:
```
-javaagent:path/to/applicationinsights-agent-3.0.0-PREVIEW.jar
```
