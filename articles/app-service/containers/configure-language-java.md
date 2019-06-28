---
title: Konfigurera Linux Java - appar i Azure App Service | Microsoft Docs
description: Lär dig hur du konfigurerar Java-appar som körs i Azure App Service på Linux.
keywords: azure app service, web app, linux, oss, java, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: 91368ac3b1d7948257fa9e55debc862567593425
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/24/2019
ms.locfileid: "67341375"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurera ett Linux Java-program för Azure App Service

Azure App Service i Linux kan Java-utvecklare för att snabbt bygga, distribuera och skala sina Tomcat eller Java Standard Edition (SE) paketeras webbprogram på en helt hanterad tjänst för Linux-baserade. Distribuera program med Maven-plugin-program från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller huvudbegrepp och instruktioner för Java-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, följer du de [Java-quickstart](quickstart-java.md) och [Java med PostgreSQL självstudien](tutorial-java-enterprise-postgresql-app.md) första.

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [Maven-pluginprogrammet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) att distribuera WAR- och .jar-filer. Distribution med populära IDE: er stöds även med [Azure Toolkit för IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) eller [Azure Toolkit för Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

I annat fall beror distributionsmetod på din Arkivtyp:

- Distribuera WAR-filerna till Tomcat genom att använda den `/api/wardeploy/` slutpunkt för att publicera arkivfilen. Mer information om detta API finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Om du vill distribuera .jar-filerna på Java SE-avbildningar, använda den `/api/zipdeploy/` slutpunkten för Kudu-webbplatsen. Mer information om detta API finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Distribuera inte din WAR eller .jar med FTP. FTP-verktyg är utformad för att ladda upp startskript, beroenden eller andra runtime-filer. Det är inte att föredra för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Loggning och felsökning av appar

Prestandarapporter trafik visualiseringar och hälsa kontroller är tillgängliga för varje app via Azure-portalen. Mer information finns i [översikt över Azure App Service-diagnostik](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH-konsolåtkomst

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns i [Direktuppspelningsloggar med Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>App-loggning

Aktivera [programloggning](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) via Azure portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) att konfigurera App Service för att skriva ditt programs standard konsolens utdata och felströmmar för standard-konsolen till lokalt filsystem eller Azure Blob Storage. Loggning till lokala App Service-filsystemet inaktiveras instans 12 timmar efter att den är konfigurerad. Om du behöver längre kvarhållning kan du konfigurera programmet att skriva utdata till ett Blob storage-behållare. Din Java och Tomcat apploggarna finns i den `/home/LogFiles/Application/` directory.

Om programmet använder [Logback](https://logback.qos.ch/) eller [Log4j](https://logging.apache.org/log4j) för att analysera och du kan vidarebefordra spårning för granskning i Azure Application Insights med hjälp av loggning framework konfigurationsanvisningar i [Utforska Java spårningsloggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Verktyg för felsökning

De inbyggda Java-avbildningarna är baserade på den [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) operativsystem. Använd den `apk` package manager för att installera alla felsökning verktyg eller kommandon.

### <a name="flight-recorder"></a>Svart låda

Alla Linux Java-avbildningar på App Service har Zulu svart låda installerad så kan du enkelt ansluta till JVM och starta en profiler spela in eller generera en heap dump.

#### <a name="timed-recording"></a>Tidsinställda inspelning

Komma igång, SSH till din App Service och kör den `jcmd` kommando för att se en lista över alla Java-processer som körs. Du bör se ditt Java-program som körs med ett process-ID (pid) utöver jcmd själva.

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Kör kommandot nedan för att starta en 30 sekunder inspelning av JVM. Det här JVM-profilen och skapa en JFR-fil med namnet `jfr_example.jfr` i arbetskatalogen. (Ersätt 116 med process-ID för din Java-app).

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Du kan verifiera inspelningen under 30 andra intervallet, sker genom att köra `jcmd 116 JFR.check`. Det här alternativet visas alla inspelningar för den angivna Java-processen.

#### <a name="continuous-recording"></a>Kontinuerlig registrering

Du kan använda Zulu svart låda du kontinuerligt Profilera ditt Java-program med minimal påverkan på runtime-prestanda ([källa](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf)). Du gör detta genom att köra följande Azure CLI-kommando för att skapa en App-inställning med namnet JAVA_OPTS med den nödvändiga konfigurationen. Innehållet i JAVA_OPTS Appinställningen skickas till den `java` kommandot när appen startas.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

Mer information finns i den [Jcmd kommandoreferens](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analysera inspelningar

Använd [FTPS](../deploy-ftp.md) hämtningen JFR till den lokala datorn. Om du vill analysera filen JFR, ladda ned och installera [Zulu Sambandscentral](https://www.azul.com/products/zulu-mission-control/). Anvisningar för Zulu Sambandscentral finns i den [Azul dokumentation](https://docs.azul.com/zmc/) och [Installationsinstruktioner](https://docs.microsoft.com/en-us/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder från box justering och anpassning genom Azure portal och CLI. Granska följande artiklar för icke-Java-specifika web app-konfiguration:

- [Konfigurera appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)
- [Konfigurera en anpassad domän](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aktivera SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Lägga till ett CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurera Kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ange alternativ för Java runtime

Ange allokerat minne eller andra alternativ för körning av JVM i Tomcat- och Java SE-miljöer och skapa en [appinställningen](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) med namnet `JAVA_OPTS` med alternativ. App Service Linux skickar den här inställningen som en miljövariabel till Java runtime när den startas.

I Azure-portalen under **programinställningar** för webbappen, skapa en ny appinställning med namnet `JAVA_OPTS` som omfattar ytterligare inställningar som `-Xms512m -Xmx1204m`.

Om du vill konfigurera appinställningen från Maven-pluginprogrammet lägger du till inställningen/värde-taggar i avsnittet Azure-plugin-programmet. I följande exempel anger en viss lägsta och högsta Java heap storlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett program med en distributionsplats i deras App Service-plan kan använda följande alternativ:

- B1 och S1 instanser: `-Xms1024m -Xmx1024m`
- B2 och S2 instanser: `-Xms3072m -Xmx3072m`
- B3 och S3 instanser: `-Xms6144m -Xmx6144m`

När programmet heap justeringsinställningar, granska information om din App Service-plan och ta hänsyn till flera program och distributionsplats måste hitta den optimala allokeringen av minne.

Om du distribuerar ett JAR-program kan det ha namnet `app.jar` så att den inbyggda avbildningen kan identifiera din app. (Maven-pluginprogrammet gör den här byta namn på automatiskt.) Om du inte vill att byta namn på din JAR till `app.jar`, du kan ladda upp ett kommandoskript med kommandot för att köra dina JAR-filen. Klistra in den fullständiga sökvägen till skriptet i den [startfil](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#startup-file) textrutan i konfigurationsavsnittet i portalen.

### <a name="turn-on-web-sockets"></a>Aktivera webbsockets

Aktivera stöd för webbsockets i Azure-portalen i den **programinställningar** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera stöd för socket med Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standardkodning tecken

I Azure-portalen under **programinställningar** för webbappen, skapa en ny appinställning med namnet `JAVA_OPTS` med värdet `-Dfile.encoding=UTF-8`.

Du kan också konfigurera appinställningen med App Service-Maven-pluginprogrammet. Lägg till taggar för inställningen namn och värde i konfigurationen av plugin-programmet:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="adjust-startup-timeout"></a>Justera tidsgräns för Start

Om ditt Java-program är särskilt stor, bör du öka tidsgräns för start. Gör detta genom att skapa en programinställning `WEBSITES_CONTAINER_START_TIME_LIMIT` och väljer hur många sekunder som App Service ska vänta innan den avbryts. Det högsta värdet är `1800` sekunder.

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service för Linux har samma uppsättning [säkerhetsmetoder](/azure/security/security-paas-applications-using-app-services) som andra program. 

### <a name="authenticate-users"></a>Autentisera användare

Appautentisering i Azure-portalen med den **autentisering och auktorisering** alternativet. Därifrån kan aktivera du autentisering med hjälp av Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Konfiguration av Azure portal fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [konfigurera App Service-appen för att använda Azure Active Directory-inloggning](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) och relaterade artiklar för andra identitetsleverantörer. Om du vill aktivera flera inloggning providers, följ instruktionerna i den [anpassa App Service-autentisering](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) artikeln.

#### <a name="tomcat"></a>Tomcat

Tomcat-program kan komma åt användarens anspråk direkt från Tomcat servleten av instruktionsvideor huvudkontot objekt till ett Kartobjekt. Kartobjekt mappar varje typ av anspråk till en uppsättning anspråk för den typen. I följande kodexempel anger `request` är en instans av `HttpServletRequest`.

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du granska den `Map` objekt för eventuella specifika krav. Följande kodavsnitt går igenom alla anspråkstyper och skriver ut innehållet i varje samling.

```java
for (Object key : map.keySet()) {
        Object value = map.get(key);
        if (value != null && value instanceof Collection {
            Collection claims = (Collection) value;
            for (Object claim : claims) {
                System.out.println(claims);
            }
        }
    }
```

Om du vill logga ut användarna och utföra andra åtgärder, finns i dokumentationen om [App Service-autentisering och auktorisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Det finns också officiella dokumentationen på Tomcat [Objektparametern gränssnittet](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servleten metoder är också hydrerat baserat på din App Service-konfiguration:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den här funktionen, skapa programinställning med namnet `WEBSITE_AUTH_SKIP_PRINCIPAL` med värdet `1`. För att inaktivera alla servlet filter har lagts till av App Service, skapa en inställning med namnet `WEBSITE_SKIP_FILTERS` med värdet `1`.

#### <a name="spring-boot"></a>Spring Boot

Spring Boot-utvecklare kan använda den [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) att skydda program med hjälp av välbekanta Spring Security anteckningar och API: er. Se till att öka den högsta huvudstorlek i din `application.properties` fil. Vi rekommenderar ett värde på `16384`.

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i den [binda ett befintligt anpassat SSL-certifikat](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domännamn. Som standard kommer ditt program fortfarande att HTTP-anslutningar – Följ specifikt steg i guiden för att framtvinga SSL och TLS.

### <a name="use-keyvault-references"></a>Använda KeyVault-referenser

[Azure KeyVault](../../key-vault/key-vault-overview.md) tillhandahåller centraliserad hantering av hemliga med åtkomst till principer och granska historiken. Du kan lagra hemligheter (till exempel lösenord eller anslutningssträngar) i KeyVault och få åtkomst till dessa hemligheter i ditt program via miljövariabler.

Först, följer du anvisningarna för [ge din appåtkomst till Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [att göra en KeyVault referens till din hemlighet i en programinställning](../app-service-key-vault-references.md#reference-syntax). Du kan verifiera att referensen motsvarar hemligheten genom att skriva ut miljövariabeln vid fjärråtkomst till App Service-terminalen.

För att mata in dessa hemligheter i konfigurationsfilen Spring eller Tomcat, använder du miljön variabeln inmatning syntax (`${MY_ENV_VAR}`). För Spring-konfigurationsfiler, finns i den här dokumentationen [externalized konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="configure-apm-platforms"></a>Konfigurera plattformar som APM

Det här avsnittet visar hur du ansluter Java-program som distribueras på Azure App Service i Linux med den NewRelic och AppDynamics för programprestandaövervakning (APM) plattformar.

[Konfigurera nya Relic](#configure-new-relic)
[konfigurera AppDynamics](#configure-appdynamics)

### <a name="configure-new-relic"></a>Konfigurera New Relic

1. Skapa ett konto för NewRelic på [NewRelic.com](https://newrelic.com/signup)
2. Ladda ned Java-agenten från NewRelic, den kommer att ha ett filnamn som liknar `newrelic-java-x.x.x.zip`.
3. Kopiera licensnyckeln, måste den att konfigurera agenten senare.
4. [SSH till din App Service-instans](app-service-linux-ssh-support.md) och skapa en ny katalog `/home/site/wwwroot/apm`.
5. Ladda upp uppackat NewRelic Java-agentfilerna till en katalog under `/home/site/wwwroot/apm`. Filer för hanteringsagenten måste vara i `/home/site/wwwroot/apm/newrelic`.
6. Ändra YAML-fil på `/home/site/wwwroot/apm/newrelic/newrelic.yml` och Ersätt platshållarvärdet licens med din egen licensnyckel.
7. Bläddra till ditt program i App Service i Azure-portalen och skapa en ny inställning för programmet.
    - Om din app använder **Java SE**, skapa miljövariabeln `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **Tomcat**, skapa miljövariabeln `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Om du använder **WildFly**, finns i dokumentationen för New Relic [här](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) anvisningar om hur du installerar Java-agenten och JBoss konfiguration.
    - Om du redan har en miljövariabel för `JAVA_OPTS` eller `CATALINA_OPTS`, lägga till den `javaagent` alternativet i slutet av det aktuella värdet.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett konto för AppDynamics på [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Hämta Java-agenten från webbplatsen AppDynamics, namnet på filen ska vara detsamma som `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH till din App Service-instans](app-service-linux-ssh-support.md) och skapa en ny katalog `/home/site/wwwroot/apm`.
1. Ladda upp Java-agentfilerna till en katalog under `/home/site/wwwroot/apm`. Filer för hanteringsagenten måste vara i `/home/site/wwwroot/apm/appdynamics`.
1. Bläddra till ditt program i App Service i Azure-portalen och skapa en ny inställning för programmet.
    - Om du använder **Java SE**, skapa miljövariabeln `JAVA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är din App Service-namn.
    - Om du använder **Tomcat**, skapa miljövariabeln `CATALINA_OPTS` med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är din App Service-namn.
    - Om du använder **WildFly**, finns i dokumentationen för AppDynamics [här](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) anvisningar om hur du installerar Java-agenten och JBoss konfiguration.
    
## <a name="configure-jar-applications"></a>Konfigurera JAR-program

### <a name="starting-jar-apps"></a>Startar JAR-appar

Som standard, App-tjänsten förväntas JAR-programmet för att få namnet `app.jar`. Om så är det här namnet, körs den automatiskt. För Maven-användare, kan du ange JAR namn genom att inkludera `<finalName>app</finalName>` i den `<build>` delen av din `pom.xml`. [Du kan göra samma i Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) genom att ange den `archiveFileName` egenskapen.

Om du vill använda ett annat namn för din JAR måste du också ange den [startkommandot](app-service-linux-faq.md#built-in-images) som kör JAR-filen. Till exempel `java -jar my-jar-app.jar`. Du kan ange värdet för din startkommandot i portalen under konfiguration > allmänna inställningar eller med en programinställning med namnet `STARTUP_COMMAND`.

### <a name="server-port"></a>Serverport

App Service Linux dirigerar inkommande begäranden till port 80, så att ditt program ska lyssna på port 80 samt. Du kan göra detta i programmets konfiguration (till exempel Spring's `application.properties` fil), eller i din startkommandot (till exempel `java -jar spring-app.jar --server.port=80`). Se följande dokumentation för vanliga Java-ramverk:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava](http://sparkjava.com/documentation#embedded-web-server)
- [Micronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Spela upp Framework](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Quarkus](https://quarkus.io/guides/application-configuration-guide)

## <a name="data-sources"></a>Datakällor

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla anslutningar. Du behöver fylla platshållarna med din valda databasens klassnamnet för drivrutinen och JAR-fil. Tillhandahålls är en tabell med klassnamn och hämtning av drivrutiner för vanliga databaser.

| Databas   | Klassnamnet för drivrutinen                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Ladda ned](https://dev.mysql.com/downloads/connector/j/) (Välj ”plattform oberoende”) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Om du vill konfigurera Tomcat om du vill använda Java Database Connectivity (JDBC) eller Java Persistence API (JPA) först anpassa den `CATALINA_OPTS` miljövariabeln läsas in av Tomcat vid start. Ange dessa värden via en appinställning i den [App Service-Maven-pluginprogrammet](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler den **Configuration** > **programinställningar** sidan på Azure portal.

Därefter fastställer om datakällan ska vara tillgänglig till ett program eller för alla program som körs på Tomcat servleten.

#### <a name="application-level-data-sources"></a>Programnivå datakällor

1. Skapa en `context.xml` fil i den `META-INF/` katalogen i ditt projekt. Skapa den `META-INF/` katalogen om den inte finns.

2. I `context.xml`, lägga till en `Context` element att länka datakällan till en JNDI-adress. Ersätt den `driverClassName` med drivrutinens klassnamn från tabellen ovan.

    ```xml
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ```

3. Uppdatera ditt programs `web.xml` att använda datakällan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="shared-server-level-resources"></a>Delade resurser på servernivå

1. Kopiera innehållet i `/usr/local/tomcat/conf` till `/home/tomcat/conf` instans på din App Service på Linux med SSH om du inte har en konfiguration för det redan.
    ```
    mkdir -p /home/tomcat
    cp -a /usr/local/tomcat/conf /home/tomcat/conf
    ```

2. Lägg till en kontext-element i din `server.xml` inom den `<Server>` element.

    ```xml
    <Server>
    ...
    <Context>
        <Resource
            name="jdbc/dbconnection" 
            type="javax.sql.DataSource"
            url="${dbuser}"
            driverClassName="<insert your driver class name>"
            username="${dbpassword}" 
            password="${connURL}"
        />
    </Context>
    ...
    </Server>
    ```

3. Uppdatera ditt programs `web.xml` att använda datakällan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Slutför konfiguration

Slutligen placera drivrutinen JAR-filer i Tomcat-klassökvägen och starta om App Service.

1. Se till att JDBC-drivrutinsfilerna är tillgängliga för Tomcat classloader genom att placera dem i den `/home/tomcat/lib` directory. (Skapa den här katalogen om den inte redan finns.) Utför följande steg om du vill överföra filerna till din App Service-instans:
    1. I den [Cloud Shell](https://shell.azure.com), installera tillägget webapp:

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Ansluta till den lokala tunneltrafik porten med SFTP-klienten och ladda upp filer till den `/home/tomcat/lib` mapp.

    Du kan också använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ dessa [instruktioner för att hämta dina autentiseringsuppgifter för FTP-](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

2. Om du har skapat en datakälla på servernivå, startar du om App Service på Linux-programmet. Tomcat återställs `CATALINA_HOME` till `/home/tomcat/conf` och använda den uppdaterade konfigurationen.

### <a name="spring-boot"></a>Spring Boot

För att ansluta till datakällor i Spring Boot-program, föreslår vi att skapa anslutningssträngar och infoga dem till din `application.properties` fil.

1. Ange ett namn för strängen i avsnittet ”Configuration” i App Service-sidan, klistra in JDBC-anslutningssträngen i värdefältet och ange till ”anpassad”. Du kan också ange den här anslutningssträngen som platsinställning.

    Den här anslutningssträngen är tillgänglig för vårt program som en miljövariabel som heter `CUSTOMCONNSTR_<your-string-name>`. Till exempel den anslutningssträng som vi skapade ovan får namnet `CUSTOMCONNSTR_exampledb`.

2. I din `application.properties` fil, referera till den här anslutningssträngen med miljövariabeln. I vårt exempel använder vi följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Finns det [Spring Boot-dokumentationen på dataåtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) och [externalized konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) mer information om det här avsnittet.

## <a name="configure-java-ee-wildfly"></a>Konfigurera Java EE (WildFly)

> [!NOTE]
> Java Enterprise Edition i App Service på Linux är för närvarande i förhandsversion. Den här stacken är **inte** rekommenderas för produktion-riktade arbete. information om hur våra Java SE och Tomcat-stackar.

Azure App Service i Linux kan Java-utvecklare för att bygga, distribuera och skala Java (EE Java) program på en helt hanterad tjänst för Linux-baserade.  Den underliggande Java Enterprise-körningsmiljön är öppen källkod [Wildfly](https://wildfly.org/) programserver.

[Skala med App Service](#scale-with-app-service)
[anpassa programmet serverkonfiguration](#customize-application-server-configuration)
[moduler och beroenden](#modules-and-dependencies)
[Data källor](#data-sources)
[Aktivera meddelanden providers](#enable-messaging-providers)
[konfigurera sessionen management cachelagring](#configure-session-management-caching)

### <a name="scale-with-app-service"></a>Skala med App Service

Programservern WildFly som körs i App Service på Linux körs i fristående läge, inte i en domänkonfiguration. När du skalar ut App Service-planen konfigureras varje WildFly-instans som en fristående server.

 Skala ditt program vågrätt eller lodrätt med [regler](../../monitoring-and-diagnostics/monitoring-autoscale-get-started.md) och av [ökar din instansantalet](../web-sites-scale.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json).

### <a name="customize-application-server-configuration"></a>Anpassa programmet serverkonfiguration

Web App-instanserna är tillståndslösa, så att varje ny instans som är igång måste konfigureras på Start som stöder Wildfly-konfiguration som krävs av programmet.
Du kan skriva en start Bash-skript för att anropa WildFly CLI för att:

- Skapa datakällor
- Konfigurera meddelanden-providrar
- Lägga till andra moduler och beroenden i Wildfly serverkonfigurationen.

 Skriptet körs när Wildfly är igång, men innan programmet startas. Skriptet bör använda den [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) anropas från `/opt/jboss/wildfly/bin/jboss-cli.sh` att konfigurera application server med en konfiguration eller ändringar som behövs när servern startas.

Använd inte interaktivt läge i CLI för att konfigurera Wildfly. I stället, du kan ange ett skript med kommandon till JBoss CLI med hjälp av den `--file` kommandot, till exempel:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Ladda upp startskriptet att `/home/site/deployments/tools` i din App Service-instans. Se [det här dokumentet](../deploy-configure-credentials.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#userscope) anvisningar om hur du hämtar din FTP-autentiseringsuppgifter.

Ange den **startskript** fältet i Azure-portalen till platsen för din start shell-skript, till exempel `/home/site/deployments/tools/your-startup-script.sh`.

Ange [appinställningar](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) i programkonfigurationen att skicka miljövariabler för användning i skriptet. Programinställningar Behåll anslutningssträngar och andra hemligheter som behövs för att konfigurera ditt program utanför versionskontroll.

### <a name="modules-and-dependencies"></a>Moduler och beroenden

För att installera moduler och deras beroenden i klassökvägen Wildfly via JBoss CLI, behöver du skapa följande filer i sina egna katalogen. Vissa moduler och beroenden kanske behöver ytterligare konfiguration, till exempel JNDI naming eller andra API-konfiguration, så att den här listan är en minimiuppsättning för vad du behöver konfigurera ett beroende i de flesta fall.

- En [XML-modulen descriptor](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Den här XML-filen definierar namn, attribut och beroenden modulens. Detta [module.xml-exempelfil](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) definierar en Postgres-modul, beroendet JDBC JAR-filen och andra modulberoenden som krävs.
- Alla nödvändiga JAR-filen beroenden för din modul.
- Ett skript med din JBoss CLI-kommandon för att konfigurera den nya modulen. Den här filen innehåller kommandon som ska köras av JBoss CLI att konfigurera servern för att använda beroendet. Dokumentation om kommandon för att lägga till moduler, datakällor och leverantörer av meddelanden som avser [det här dokumentet](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- Ett Bash-startskript att anropa JBoss CLI och köra skriptet i föregående steg. Den här filen ska utföras när din App Service-instans startas eller när nya instanser som levereras under en skalbar. Den här startskript är där du kan utföra alla andra inställningar för ditt program som JBoss kommandon skickas till JBoss CLI. Den här filen kan vara ett enda kommando för att skicka ditt kommando JBoss CLI-skript till JBoss CLI minimum:

```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli`
```

När du har de filer och innehåll för din modul, följer du stegen nedan för att lägga till modulen på programservern Wildfly.

1. FTP-filer till `/home/site/deployments/tools` i din App Service-instans. Se det här dokumentet för instruktioner om hur du hämtar din FTP-autentiseringsuppgifter.
2. I den **Configuration** > **allmänna inställningar** sidan i Azure portal, Ställ in ”startskriptet” fältet till platsen för din start shell-skript, till exempel `/home/site/deployments/tools/your-startup-script.sh` .
3. Starta om din App Service-instans genom att trycka på den **starta om** knappen i den **översikt** avsnittet i portalen eller med hjälp av Azure CLI.

### <a name="configure-data-source-connections"></a>Konfigurera anslutningar till datakälla

Följ samma process som beskrivs ovan i avsnittet Installera moduler och beroenden för att konfigurera Wildfly för anslutning till en datakälla. Du kan följa samma steg för alla Azure-databastjänsten.

1. Ladda ned JDBC-drivrutinen för din databas smak. För enkelhetens skull följer drivrutinerna för [Postgres](https://jdbc.postgresql.org/download.html) och [MySQL](https://dev.mysql.com/downloads/connector/j/). Packa upp hämtningen för att hämta .jar-filen.
2. Följ stegen disposition i ”moduler och beroenden” kan skapa och överföra din XML-modulen descriptor JBoss CLI-skript, startskript och JDBC .jar-beroendet.

Mer information om hur du konfigurerar Wildfly med [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), och [SQL Database](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) är tillgänglig. Du kan använda dessa anpassade anvisningar tillsammans med den generaliserade metoden ovan för att lägga till definitioner av datakällor i din server.

### <a name="enable-messaging-providers"></a>Aktivera meddelanden providers

För att aktivera meddelandefunktioner Beans använder Service Bus som mekanism för meddelanden:

1. Använd den [Apache QPId JMS meddelandebiblioteket](https://qpid.apache.org/proton/index.html). Med det här beroendet i din pom.xml (eller andra-filen) för programmet.

2. Skapa [Service Bus-resurser](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Skapa en Azure Service Bus-namnområde och kö i namnområdet och en princip för delad åtkomst med skicka och ta emot funktioner.

3. Ange den delade åtkomstnyckeln för principen för koden antingen med URL-kodning den primära nyckeln i din princip eller [Använd Service Bus SDK](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Följ stegen som beskrivs i avsnittet Installera moduler och beroenden med modulen XML descriptor, .jar beroenden, JBoss CLI-kommandon och startskript för JMS-providern. Förutom de fyra filerna måste du också skapa en XML-fil som definierar JNDI namnet för den JMS queue och topic. Se [den här lagringsplatsen](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) för referens konfigurationsfiler.

### <a name="configure-session-management-caching"></a>Konfigurera sessionen management cachelagring

Som standard App Service på Linux ska använda sessionscookies tillhörighet att se till att klientbegäranden med befintliga sessioner dirigeras samma instans av ditt program. Den här standardfunktionen kräver ingen konfiguration, men har vissa begränsningar:

- Om en programinstans startas om och skalas, går användaren sessionstillstånd i programservern förlorad.
- Om program har lång timeout sessionsinställningar eller ett fast antal användare, kan det ta lite tid för autoscaled nya instanser kan ta emot belastning eftersom endast nya sessioner kommer att dirigeras till de nyligen startats instanserna.

Du kan konfigurera Wildfly om du vill använda en extern session butik som [Azure Cache för Redis](/azure/azure-cache-for-redis/). Behöver du [inaktivera den befintliga instansen ARR-tillhörighet](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) konfiguration för att stänga av sessionen cookie-baserad Routning och tillåta arkivet session konfigurerade Wildfly att fungera utan störningar.

## <a name="docker-containers"></a>Docker-containrar

Om du vill använda Azure stöder Zulu JDK-Paketet i din behållare, se till att hämta och använda de inbyggda avbildningarna enligt dokumentationen från den [stöds Azul Zulu Enterprise för Azure hämtningssidan](https://www.azul.com/downloads/azure-only/zulu/) eller Använd den `Dockerfile` exempel från [Microsoft Java GitHub-lagringsplatsen](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Instruktionen för support

### <a name="runtime-availability"></a>Runtime-tillgänglighet

App Service för Linux stöder två körningar för hanterade användning av Java-webbprogram:

- Den [Tomcat-servletbehållare](https://tomcat.apache.org/) för att köra program som är packade som webbfiler Arkiv (WAR). Versioner som stöds är 8.5 och 9.0.
- Java SE körningsmiljö för att köra program paketeras som Java-Arkiv (JAR)-filer. Versioner som stöds är Java 8 och 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azul Zulu Enterprise-versioner av OpenJDK är en kostnadsfri och vara färdigt för produktionsanvändning fördelning av OpenJDK för Azure och Azure Stack med Microsoft och Azul Systems. De innehåller alla komponenter för att skapa och köra program med Java SE. Du kan installera JDK från [Java JDK Installation](https://aka.ms/azure-jdks).

Stöds JDKs korrigerade automatiskt kvartalsvis i januari, April, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Uppdateringar och korrigeringar för större säkerhetsproblem släpps när de blir tillgängliga från Azul Systems. En sårbarhet i ett ”större” har definierats som en grundläggande poäng med 9.0 eller senare på den [NIST vanliga säkerhetsproblem bedömning System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasningen och tillbakadragande

Om en stöds Java runtime kommer att dras tillbaka, får Azure-utvecklare som använder den berörda runtime ett utfasningsmeddelande om minst sex månader innan körningen har dragits tillbaka.

## <a name="next-steps"></a>Nästa steg

Gå till den [Azure for Java Developers](/java/azure/) center för att hitta Azure snabbstarter, självstudier och referensdokumentation för Java.

Allmänna frågor om hur du använder App Service för Linux som inte är specifika för Java-utveckling besvaras inom den [App Service Linux vanliga frågor och svar](app-service-linux-faq.md).

