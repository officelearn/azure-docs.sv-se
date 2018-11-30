---
title: Java language-stöd för Azure App Service i Linux | Microsoft Docs
description: Utvecklarens guide till att distribuera Java-appar med Azure App Service i Linux.
keywords: Azure apptjänst, webbapp, linux, oss, java
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.openlocfilehash: 8d15aeb92911a26a9a42a0449a24e8c0fee4467b
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497336"
---
# <a name="java-developers-guide-for-app-service-on-linux"></a>Java developer's guide för App Service på Linux

Azure App Service i Linux kan Java-utvecklare för att snabbt bygga, distribuera och skala sina Tomcat eller Java Standard Edition (SE) paketeras webbprogram på en helt hanterad tjänst för Linux-baserade. Distribuera program med Maven-plugin-program från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare med i App Service för Linux. Om du aldrig har använt Azure App Service för Linux bör du läsa den [Java-quickstart](quickstart-java.md) första. Allmänna frågor om hur du använder App Service för Linux som inte är specifika för Java-utveckling besvaras inom den [App Service Linux vanliga frågor och svar](app-service-linux-faq.md).

## <a name="logging-and-debugging-apps"></a>Loggning och felsökning av appar

Prestandarapporter trafik visualiseringar och hälsa kontroller är tillgängliga för eeach app via Azure-portalen. Se den [översikt över Azure App Service-diagnostik](/azure/app-service/app-service-diagnostics) för mer information om hur du använder dessa diagnostiska verktyg.

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

Mer information finns i [Direktuppspelningsloggar med Azure CLI](/azure/app-service/web-sites-enable-diagnostic-log#streaming-with-azure-command-line-interface).

### <a name="app-logging"></a>App-loggning

Aktivera [programloggning](/azure/app-service/web-sites-enable-diagnostic-log#enablediag) via Azure portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) att konfigurera App Service för att skriva ditt programs standard konsolens utdata och felströmmar för standard-konsolen till lokalt filsystem eller Azure Blob Storage. Loggning till lokala App Service-filsystemet inaktiveras instans 12 timmar efter att den är konfigurerad. Om du behöver längre kvarhållning kan du konfigurera programmet att skriva utdata till ett Blob storage-behållare.

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

Appautentisering i Azure-portalen med den **autentisering och auktorisering** alternativet. Därifrån kan aktivera du autentisering med hjälp av Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Konfiguration av Azure portal fungerar bara när du konfigurerar en enda autentiseringsprovider.  Mer information finns i [konfigurera App Service-appen för att använda Azure Active Directory-inloggning](/azure/app-service/app-service-mobile-how-to-configure-active-directory-authentication) och relaterade artiklar för andra identitetsleverantörer.

Om du vill aktivera flera inloggning providers, följ instruktionerna i den [anpassa App Service-autentisering](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to) artikeln.

 Spring Boot-utvecklare kan använda den [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) att skydda program med hjälp av välbekanta Spring Security anteckningar och API: er.

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i den [binda ett befintligt anpassat SSL-certifikat](/azure/app-service/app-service-web-tutorial-custom-ssl) att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domännamn. Som standard kommer ditt program fortfarande att HTTP-anslutningar – Följ specifikt steg i guiden för att framtvinga SSL och TLS.

## <a name="tomcat"></a>Tomcat 

### <a name="connecting-to-data-sources"></a>Ansluta till datakällor

>[!NOTE]
> Om programmet använder Spring Framework eller Spring Boot, kan du ange anslutningsinformation för databasen för Spring Data JPA som miljövariabler för [i filen programmet egenskaper]. Använd sedan [appinställningar](/azure/app-service/web-sites-configure#app-settings) att definiera dessa värden för ditt program i Azure-portalen eller CLI.

Exempelavsnitt för konfiguration i det här avsnittet använda MySQL-databas. Mer information finns i dokumentationen för configuration [MySQL](https://dev.mysql.com/doc/connector-j/8.0/en/connector-j-usagenotes-tomcat.html) , [SQL Server JDBC](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server?view=sql-server-2017), och [PostgreSQL](https://jdbc.postgresql.org/documentation/head/index.html).

Om du vill konfigurera Tomcat för att använda hanterade anslutningar till databaser med hjälp av Java Database Connectivity (JDBC) eller Java Persistence API (JPA) först anpassa CATALINA_OPTS miljövariabeln läses i av Tomcat vid start. Ange dessa värden via en appinställning i App Service-Maven-pluginprogrammet:

```xml
<appSettings> 
    <property> 
        <name>CATALINA_OPTS</name> 
        <value>"$CATALINA_OPTS -Dmysqluser=${mysqluser} -Dmysqlpass=${mysqlpass} -DmysqlURL=${mysqlURL}"</value> 
    </property> 
</appSettings> 
```

Eller en motsvarande App Service-inställningen från Azure-portalen.

Sedan avgöra om datakällan måste vara tillgängliga bara till ett program eller till alla program som körs på App Service-planen.

För programnivå datakällor: 

1. Lägg till en `context.xml` filen, om det inte finns i ditt webbprogram och lägga till den i `META-INF` för WAR-filen när projektet har skapats.

2. I den här filen lägger du till en `Context` sökvägspost att länka datakällan till en JNDI-adress.

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Uppdatera ditt programs `web.xml` att använda datakällan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

För delade resurser på servernivå:

1. Kopiera innehållet i `/usr/local/tomcat/conf` till `/home/tomcat/conf` instans på din App Service på Linux med SSH om du inte har en konfiguration för det redan.

2. Lägg till kontext till din `server.xml`

    ```xml
    <Context>
        <Resource
            name="jdbc/mysqldb" type="javax.sql.DataSource"
            url="${mysqlURL}"
            driverClassName="com.mysql.jdbc.Driver"
            username="${mysqluser}" password="${mysqlpass}"
        />
    </Context>
    ```

3. Uppdatera ditt programs `web.xml` att använda datakällan i ditt program.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/mysqldb</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

4. Se till att JDBC-drivrutinsfilerna är tillgängliga för Tomcat classloader genom att placera dem i den `/home/tomcat/lib` directory. Utför följande steg om du vill överföra filerna till din App Service-instans:  
    1. Installera tillägget Azure App Service webpp:

      ```azurecli-interactive
      az extension add –name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

      ```azurecli-interactive
      az webapp remote-connection create –g [resource group] -n [app name] -p [local port to open]
      ```

    3. Ansluta till den lokala tunneltrafik porten med SFTP-klienten och ladda upp filer till den `/home/tomcat/lib` mapp.

5. Starta om App Service på Linux-programmet. Tomcat återställs `CATALINA_HOME` till `/home/tomcat/conf` och använda den uppdaterade konfigurationen och klasser.

## <a name="docker-containers"></a>Docker-containrar

Om du vill använda Azure stöder Zulu JDK-Paketet i din behållare, se till att hämta och använda de inbyggda avbildningarna enligt dokumentationen från den [stöds Azul Zulu Enterprise för Azure hämtningssidan](https://www.azul.com/downloads/azure-only/zulu/) eller Använd den `Dockerfile` exempel från [Microsoft Java GitHub-lagringsplatsen](https://github.com/Microsoft/java/tree/master/docker).

## <a name="runtime-availability-and-statement-of-support"></a>Runtime-tillgänglighet och support-instruktionen

App Service för Linux stöder två körningar för hanterade användning av Java-webbprogram:

- Den [Tomcat-servletbehållare](http://tomcat.apache.org/) för att köra program som är packade som webbfiler Arkiv (WAR). Versioner som stöds är 8.5 och 9.0.
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
