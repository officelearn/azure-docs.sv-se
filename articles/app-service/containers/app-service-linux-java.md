---
title: Java developer's guide för App Service på Linux – Azure | Microsoft Docs
description: Lär dig hur du konfigurerar Java-appar som körs i Azure App Service på Linux.
keywords: azure app service, web app, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 12/10/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: d6e8d943d14cfddc260ba502e724543c6dc9cf4f
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110344"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Java developer's guide för App Service på Linux

Azure App Service i Linux kan Java-utvecklare för att snabbt bygga, distribuera och skala sina Tomcat eller Java Standard Edition (SE) paketeras webbprogram på en helt hanterad tjänst för Linux-baserade. Distribuera program med Maven-plugin-program från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare med i App Service för Linux. Om du aldrig har använt Azure App Service för Linux bör du läsa den [Java-quickstart](quickstart-java.md) första. Allmänna frågor om hur du använder App Service för Linux som inte är specifika för Java-utveckling besvaras inom den [App Service Linux vanliga frågor och svar](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda Maven-pluginprogrammet för att distribuera WAR- och .jar-filer. Se [den här dokumentationen](https://docs.microsoft.com/en-us/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable) mer information om Maven-pluginprogrammet. 

Om du inte använder Maven, beror din distributionsmetod på din Arkivtyp:

- Distribuera WAR-filerna till Tomcat genom att använda den `/api/wardeploy/` slutpunkt för att publicera arkivfilen. Mer information om detta API finns i [den här dokumentationen](https://docs.microsoft.com/en-us/azure/app-service/deploy-zip#deploy-war-file).
- Om du vill distribuera .jar-filerna på Java SE-avbildningar, använda den `/api/zipdeploy/` slutpunkten för Kudu-webbplatsen. Mer information om detta API finns i [den här dokumentationen](https://docs.microsoft.com/en-us/azure/app-service/deploy-zip#rest).

Distribuera inte din WAR eller .jar med FTP. FTP-verktyg är utformad för att ladda upp startskript, beroenden eller andra runtime-filer. Det är inte att föredra för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Loggning och felsökning av appar

Prestandarapporter trafik visualiseringar och hälsa kontroller är tillgängliga för varje app via Azure-portalen. Se den [översikt över Azure App Service-diagnostik](/azure/app-service/overview-diagnostics) för mer information om hur du använder dessa diagnostiska verktyg.

## <a name="application-performance-monitoring"></a>Prestandaövervakning av program

Se [verktyg med Java-appar i Azure App Service på Linux för programprestandaövervakning](how-to-java-apm-monitoring.md) till anvisningar att konfigurera New Relic och AppDynamics med Java-appar som körs på App Service i Linux.

### <a name="ssh-console-access"></a>SSH-konsolåtkomst 

SSH-anslutningen till Linux-miljö som kör din app är tillgängliga. Se [SSH-stöd för Azure App Service i Linux](/azure/app-service/containers/app-service-linux-ssh-support) fullständiga instruktioner för att ansluta till Linux-system via webbläsaren eller lokala terminalen.

### <a name="streaming-logs"></a>Direktuppspelningsloggar

För snabb felsökning och felsökning kan strömma du loggar till konsolen med hjälp av Azure CLI. Konfigurera CLI med den `az webapp log config` att aktivera loggning:

```azurecli-interactive
az webapp log config --name ${WEBAPP_NAME} \
 --resource-group ${RESOURCEGROUP_NAME} \
 --web-server-logging filesystem
```

Strömma loggar till konsolen med `az webapp log tail`:

```azurecli-interactive
az webapp log tail --name webappname --resource-group myResourceGroup
```

Mer information finns i [Direktuppspelningsloggar med Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>App-loggning

Aktivera [programloggning](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) via Azure portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) att konfigurera App Service för att skriva ditt programs standard konsolens utdata och felströmmar för standard-konsolen till lokalt filsystem eller Azure Blob Storage. Loggning till lokala App Service-filsystemet inaktiveras instans 12 timmar efter att den är konfigurerad. Om du behöver längre kvarhållning kan du konfigurera programmet att skriva utdata till ett Blob storage-behållare.

Om programmet använder [Logback](https://logback.qos.ch/) eller [Log4j](https://logging.apache.org/log4j) för att analysera och du kan vidarebefordra spårning för granskning i Azure Application Insights med hjälp av loggning framework konfigurationsanvisningar i [Utforska Java spårningsloggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs). 

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder från box justering och anpassning genom Azure Portal och CLI. Granska följande artiklar för icke-Java-specifika web app-konfiguration:

- [Konfigurera inställningar för App Service](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurera en anpassad domän](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aktivera SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Lägga till ett CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Ange alternativ för Java runtime

Ange allokerat minne eller andra alternativ för körning av JVM i Tomcat- och Java SE-miljöer och ange JAVA_OPTS enligt nedan som en [programinställning](/azure/app-service/web-sites-configure#app-settings). App Service Linux skickar den här inställningen som en miljövariabel till Java runtime när den startas.

I Azure-portalen under **programinställningar** för webbappen, skapa en ny appinställning med namnet `JAVA_OPTS` som omfattar ytterligare inställningar som `$JAVA_OPTS -Xms512m -Xmx1204m`.

Om du vill konfigurera appinställningen från Azure App Service Linux Maven-pluginprogrammet lägger du till inställningen/värde-taggar i avsnittet Azure-plugin-programmet. I följande exempel anger en specifik heapsize för lägsta och högsta Java:

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Xms512m -Xmx1204m</value> 
    </property> 
</appSettings> 
```

Utvecklare som kör ett program med en distributionsplats i deras App Service-plan kan använda följande alternativ:

- B1 och S1 instanser:-Xms1024m-Xmx1024m
- B2- och S2-instanser:-Xms3072m-Xmx3072m
- B3 och S3 instanser:-Xms6144m-Xmx6144m


När programmet heap justeringsinställningar, granska information om din App Service-plan och ta hänsyn till flera program och distributionsplats måste hitta den optimala allokeringen av minne.

### <a name="turn-on-web-sockets"></a>Aktivera webbsockets

Aktivera stöd för webbsockets i Azure-portalen i den **programinställningar** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera stöd för socket med Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} --web-sockets-enabled true 
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME} 
az webapp start -n ${WEBAPP_NAME} -g ${WEBAPP_RESOURCEGROUP_NAME}  
```

### <a name="set-default-character-encoding"></a>Ange standardkodning tecken 

I Azure-portalen under **programinställningar** för webbappen, skapa en ny appinställning med namnet `JAVA_OPTS` med värdet `$JAVA_OPTS -Dfile.encoding=UTF-8`.

Du kan också konfigurera appinställningen med App Service-Maven-pluginprogrammet. Lägg till taggar för inställningen namn och värde i konfigurationen av plugin-programmet: 

```xml
<appSettings> 
    <property> 
        <name>JAVA_OPTS</name> 
        <value>$JAVA_OPTS -Dfile.encoding=UTF-8</value> 
    </property> 
</appSettings> 
```

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service för Linux har samma uppsättning [säkerhetsmetoder](/azure/security/security-paas-applications-using-app-services) som andra program. 

### <a name="authenticate-users"></a>Autentisera användare

Appautentisering i Azure-portalen med den **autentisering och auktorisering** alternativet. Därifrån kan aktivera du autentisering med hjälp av Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Konfiguration av Azure portal fungerar bara när du konfigurerar en enda autentiseringsprovider.  Mer information finns i [konfigurera App Service-appen för att använda Azure Active Directory-inloggning](/azure/app-service/configure-authentication-provider-aad) och relaterade artiklar för andra identitetsleverantörer.

Om du vill aktivera flera inloggning providers, följ instruktionerna i den [anpassa App Service-autentisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) artikeln.

Spring Boot-utvecklare kan använda den [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) att skydda program med hjälp av välbekanta Spring Security anteckningar och API: er. Se till att öka den högsta huvudstorlek i din `application.properties` fil. Vi rekommenderar ett värde på `16384`. 

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i den [binda ett befintligt anpassat SSL-certifikat](/azure/app-service/app-service-web-tutorial-custom-ssl) att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domännamn. Som standard kommer ditt program fortfarande att HTTP-anslutningar – Följ specifikt steg i guiden för att framtvinga SSL och TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Ansluta till datakällor

>[!NOTE]
> Om programmet använder Spring Framework eller Spring Boot, kan du ange anslutningsinformation för databasen för Spring Data JPA som miljövariabler för [i filen programmet egenskaper]. Använd sedan [appinställningar](/azure/app-service/web-sites-configure#app-settings) att definiera dessa värden för ditt program i Azure-portalen eller CLI.

Dessa anvisningar gäller för alla anslutningar. Du behöver fylla platshållarna med din valda databasens klassnamnet för drivrutinen och JAR-fil. Tillhandahålls är en tabell med klassnamn och hämtning av drivrutiner för vanliga databaser.

| Databas   | Klassnamnet för drivrutinen                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Drvier`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Ladda ned](https://dev.mysql.com/downloads/connector/j/) (Välj ”plattform oberoende”) |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#available-downloads-of-jdbc-driver-for-sql-server)                                                           |

Om du vill konfigurera Tomcat om du vill använda Java Database Connectivity (JDBC) eller Java Persistence API (JPA) först anpassa den `CATALINA_OPTS` miljövariabeln läsas in av Tomcat början upp. Ange dessa värden via en appinställning i den [App Service-Maven-pluginprogrammet](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md):

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value> 
    </property> 
</appSettings> 
```

Eller ange miljövariabler i bladet ”Application Settings” i Azure-portalen.

Därefter fastställer om datakällan ska vara tillgänglig till ett program eller för alla program som körs på Tomcat servleten.

#### <a name="for-application-level-data-sources"></a>För programnivå datakällor: 

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

#### <a name="for-shared-server-level-resources"></a>För delade resurser på servernivå:

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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Slutligen placera drivrutinen JAR-filer i Tomcat-klassökvägen och starta om App Service: 

1. Se till att JDBC-drivrutinsfilerna är tillgängliga för Tomcat classloader genom att placera dem i den `/home/tomcat/lib` directory. (Skapa den här katalogen om den inte redan finns.) Utför följande steg om du vill överföra filerna till din App Service-instans:  
    1. Installera tillägget Azure App Service webpp:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. Ansluta till den lokala tunneltrafik porten med SFTP-klienten och ladda upp filer till den `/home/tomcat/lib` mapp.

    Du kan också använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ dessa [instruktioner för att hämta dina autentiseringsuppgifter för FTP-](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Om du har skapat en datakälla på servernivå, startar du om App Service på Linux-programmet. Tomcat återställs `CATALINA_HOME` till `/home/tomcat/conf` och använda den uppdaterade konfigurationen.

## <a name="docker-containers"></a>Docker-containrar

Om du vill använda Azure stöder Zulu JDK-Paketet i din behållare, se till att hämta och använda de inbyggda avbildningarna enligt dokumentationen från den [stöds Azul Zulu Enterprise för Azure hämtningssidan](https://www.azul.com/downloads/azure-only/zulu/) eller Använd den `Dockerfile` exempel från [Microsoft Java GitHub-lagringsplatsen](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Runtime-tillgänglighet och support-instruktionen

App Service för Linux stöder två körningar för hanterade användning av Java-webbprogram:

- Den [Tomcat-servletbehållare](https://tomcat.apache.org/) för att köra program som är packade som webbfiler Arkiv (WAR). Versioner som stöds är 8.5 och 9.0.
- Java SE körningsmiljö för att köra program paketeras som Java-Arkiv (JAR)-filer. Den enda största versionen som stöds är Java 8.

## <a name="java-runtime-statement-of-support"></a>Java runtime-instruktionen för support 

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures stöds Java Development Kit (JDK) är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) via [Azul Systems](https://www.azul.com/).

Stora versionsuppdateringar tillhandahålls via nya körningsalternativ i Azure App Service för Linux. Kunder uppdateras till dessa senare versioner av Java genom att konfigurera deras App Service-distribution och ansvarar för att testa och att se till att viktig uppdatering uppfyller deras behov.

Stöds JDKs korrigerade automatiskt kvartalsvis i januari, April, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Uppdateringar och korrigeringar för större säkerhetsproblem släpps när de blir tillgängliga från Azul Systems. En sårbarhet i ett ”större” har definierats som en grundläggande poäng med 9.0 eller senare på den [NIST vanliga säkerhetsproblem bedömning System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasningen och tillbakadragande

Om en stöds Java runtime kommer att dras tillbaka, får Azure-utvecklare som använder den berörda runtime ett utfasningsmeddelande om minst sex månader innan körningen har dragits tillbaka.

### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ned produktion Edition av Azul Zulu Enterprise JDK-Paketet för lokal utveckling från [Azuls hämtningsplats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Stöd för programutveckling

Produktsupport för den [Azure stöder Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) är tillgänglig via när du utvecklar för Azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med en [kvalificerade Azure-supportplan](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Runtime-stöd

Utvecklare kan [öppna ett ärende](/azure/azure-supportability/how-to-create-azure-support-request) med Azul Zulu JDKs via supporten för Azure om de har en [kvalificerade supportavtal](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Nästa steg

Gå till den [Azure for Java Developers](/java/azure/) center för att hitta Azure snabbstarter, självstudier och referensdokumentation för Java.
