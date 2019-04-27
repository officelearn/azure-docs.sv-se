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
ms.openlocfilehash: 5c9f70650f518c72a75d9a7826e7cbc30a95a00c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60852719"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Java developer's guide för App Service på Linux

Azure App Service i Linux kan Java-utvecklare för att snabbt bygga, distribuera och skala sina Tomcat eller Java Standard Edition (SE) paketeras webbprogram på en helt hanterad tjänst för Linux-baserade. Distribuera program med Maven-plugin-program från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare med i App Service för Linux. Om du aldrig har använt Azure App Service för Linux bör du läsa den [Java-quickstart](quickstart-java.md) första. Allmänna frågor om hur du använder App Service för Linux som inte är specifika för Java-utveckling besvaras inom den [App Service Linux vanliga frågor och svar](app-service-linux-faq.md).

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [Maven-pluginprogrammet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) att distribuera WAR- och .jar-filer. Distribution med populära IDE: er stöds även med [Azure Toolkit för IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) eller [Azure Toolkit för Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

I annat fall beror distributionsmetod på din Arkivtyp:

- Distribuera WAR-filerna till Tomcat genom att använda den `/api/wardeploy/` slutpunkt för att publicera arkivfilen. Mer information om detta API finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Om du vill distribuera .jar-filerna på Java SE-avbildningar, använda den `/api/zipdeploy/` slutpunkten för Kudu-webbplatsen. Mer information om detta API finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Distribuera inte din WAR eller .jar med FTP. FTP-verktyg är utformad för att ladda upp startskript, beroenden eller andra runtime-filer. Det är inte att föredra för att distribuera webbappar.

## <a name="logging-and-debugging-apps"></a>Loggning och felsökning av appar

Prestandarapporter trafik visualiseringar och hälsa kontroller är tillgängliga för varje app via Azure-portalen. Se den [översikt över Azure App Service-diagnostik](/azure/app-service/overview-diagnostics) för mer information om hur du använder dessa diagnostiska verktyg.

## <a name="application-performance-monitoring"></a>Prestandaövervakning av program

Se [verktyg med Java-appar i Azure App Service på Linux för programprestandaövervakning](how-to-java-apm-monitoring.md) till anvisningar att konfigurera New Relic och AppDynamics med Java-appar som körs på App Service i Linux.

### <a name="ssh-console-access"></a>SSH-konsolåtkomst

SSH-anslutningen till Linux-miljö som kör appen är tillgänglig. Se [SSH-stöd för Azure App Service i Linux](/azure/app-service/containers/app-service-linux-ssh-support) fullständiga instruktioner för att ansluta till Linux-system via webbläsaren eller lokala terminalen.

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

Aktivera [programloggning](/azure/app-service/troubleshoot-diagnostic-logs#enablediag) via Azure portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) att konfigurera App Service för att skriva ditt programs standard konsolens utdata och felströmmar för standard-konsolen till lokalt filsystem eller Azure Blob Storage. Loggning till lokala App Service-filsystemet inaktiveras instans 12 timmar efter att den är konfigurerad. Om du behöver längre kvarhållning kan du konfigurera programmet att skriva utdata till ett Blob storage-behållare. Din Java och Tomcat apploggarna finns i den `/home/LogFiles/Application/` directory.

Om programmet använder [Logback](https://logback.qos.ch/) eller [Log4j](https://logging.apache.org/log4j) för att analysera och du kan vidarebefordra spårning för granskning i Azure Application Insights med hjälp av loggning framework konfigurationsanvisningar i [Utforska Java spårningsloggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Verktyg för felsökning

De inbyggda Java-avbildningarna är baserade på den [Alpine Linux](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) operativsystem. Använd den `apk` package manager för att installera alla felsökning verktyg eller kommandon.

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder från box justering och anpassning genom Azure portal och CLI. Granska följande artiklar för icke-Java-specifika web app-konfiguration:

- [Konfigurera inställningar för App Service](/azure/app-service/web-sites-configure?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurera en anpassad domän](/azure/app-service/app-service-web-tutorial-custom-domain?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aktivera SSL](/azure/app-service/app-service-web-tutorial-custom-ssl?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Lägga till ett CDN](/azure/cdn/cdn-add-to-web-app?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurera Kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ange alternativ för Java runtime

Ange allokerat minne eller andra alternativ för körning av JVM i Tomcat- och Java SE-miljöer och skapa en [programinställningen](/azure/app-service/web-sites-configure#app-settings) med namnet `JAVA_OPTS` med alternativ. App Service Linux skickar den här inställningen som en miljövariabel till Java runtime när den startas.

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

Om du distribuerar ett JAR-program kan det ha namnet `app.jar` så att den inbyggda avbildningen kan identifiera din app. (Maven-pluginprogrammet gör den här byta namn på automatiskt.) Om du inte vill att byta namn på din JAR till `app.jar`, du kan ladda upp ett kommandoskript med kommandot för att köra dina JAR-filen. Klistra in den fullständiga sökvägen till skriptet i den [startfil](https://docs.microsoft.com/en-us/azure/app-service/containers/app-service-linux-faq#startup-file) textrutan i konfigurationsavsnittet i portalen.

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

Appautentisering i Azure-portalen med den **autentisering och auktorisering** alternativet. Därifrån kan aktivera du autentisering med hjälp av Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Konfiguration av Azure portal fungerar bara när du konfigurerar en enda autentiseringsprovider.  Mer information finns i [konfigurera App Service-appen för att använda Azure Active Directory-inloggning](/azure/app-service/configure-authentication-provider-aad) och relaterade artiklar för andra identitetsleverantörer.

Om du vill aktivera flera inloggning providers, följ instruktionerna i den [anpassa App Service-autentisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) artikeln.

Spring Boot-utvecklare kan använda den [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) att skydda program med hjälp av välbekanta Spring Security anteckningar och API: er. Se till att öka den högsta huvudstorlek i din `application.properties` fil. Vi rekommenderar ett värde på `16384`.

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i den [binda ett befintligt anpassat SSL-certifikat](/azure/app-service/app-service-web-tutorial-custom-ssl) att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domännamn. Som standard kommer ditt program fortfarande att HTTP-anslutningar – Följ specifikt steg i guiden för att framtvinga SSL och TLS.

### <a name="use-keyvault-references"></a>Använda KeyVault-referenser

[Azure KeyVault](../../key-vault/key-vault-overview.md) tillhandahåller centraliserad hantering av hemliga med åtkomst till principer och granska historiken. Du kan lagra hemligheter (till exempel lösenord eller anslutningssträngar) i KeyVault och få åtkomst till dessa hemligheter i ditt program via miljövariabler.

Först, följer du anvisningarna för [ge din appåtkomst till Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och [att göra en KeyVault referens till din hemlighet i en programinställning](../app-service-key-vault-references.md#reference-syntax). Du kan verifiera att referensen motsvarar hemligheten genom att skriva ut miljövariabeln vid fjärråtkomst till App Service-terminalen.

För att mata in dessa hemligheter i konfigurationsfilen Spring eller Tomcat, använder du miljön variabeln inmatning syntax (`${MY_ENV_VAR}`). För Spring-konfigurationsfiler, finns i den här dokumentationen [externalized konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

## <a name="data-sources"></a>Datakällor

### <a name="tomcat"></a>Tomcat

Dessa anvisningar gäller för alla anslutningar. Du behöver fylla platshållarna med din valda databasens klassnamnet för drivrutinen och JAR-fil. Tillhandahålls är en tabell med klassnamn och hämtning av drivrutiner för vanliga databaser.

| Databas   | Klassnamnet för drivrutinen                             | JDBC Driver                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
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

    ```bash
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

#### <a name="finally-place-the-driver-jars-in-the-tomcat-classpath-and-restart-your-app-service"></a>Slutligen placera drivrutinen JAR-filer i Tomcat-klassökvägen och starta om App Service

1. Se till att JDBC-drivrutinsfilerna är tillgängliga för Tomcat classloader genom att placera dem i den `/home/tomcat/lib` directory. (Skapa den här katalogen om den inte redan finns.) Utför följande steg om du vill överföra filerna till din App Service-instans:  
   1. Installera tillägget Azure App Service webpp:

      ```azurecli-interactive
      az extension add –name webapp
      ```

   1. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

   1. Ansluta till den lokala tunneltrafik porten med SFTP-klienten och ladda upp filer till den `/home/tomcat/lib` mapp.

      Du kan också använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ dessa [instruktioner för att hämta dina autentiseringsuppgifter för FTP-](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

2. Om du har skapat en datakälla på servernivå, startar du om App Service på Linux-programmet. Tomcat återställs `CATALINA_HOME` till `/home/tomcat/conf` och använda den uppdaterade konfigurationen.

### <a name="spring-boot"></a>Spring Boot

För att ansluta till datakällor i Spring Boot-program, föreslår vi att skapa anslutningssträngar och infoga dem till din `application.properties` fil.

1. Ange ett namn för strängen i avsnittet ”Application Settings” i App Service-bladet, klistra in JDBC-anslutningssträngen i värdefältet och ange till ”anpassad”. Du kan också ange den här anslutningssträngen som platsinställning.

    ![Skapa en anslutningssträng i portalen.][1]

    Den här anslutningssträngen är tillgänglig för vårt program som en miljövariabel som heter `CUSTOMCONNSTR_<your-string-name>`. Till exempel den anslutningssträng som vi skapade ovan får namnet `CUSTOMCONNSTR_exampledb`.

2. I din `application.properties` fil, referera till den här anslutningssträngen med miljövariabeln. I vårt exempel använder vi följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Finns det [Spring Boot-dokumentationen på dataåtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html
) och [externalized konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html) mer information om det här avsnittet.

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

<!--Image references-->
[1]: ./media/app-service-linux-java/connection-string.png
