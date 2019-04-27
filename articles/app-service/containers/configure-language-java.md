---
title: Konfigurera Linux Java - appar i Azure App Service | Microsoft Docs
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
ms.date: 03/28/2019
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: b659c076974b0659c645c9b6460e458dfac8974a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60850468"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurera ett Linux Java-program för Azure App Service

Azure App Service i Linux kan Java-utvecklare för att snabbt bygga, distribuera och skala sina Tomcat eller Java Standard Edition (SE) paketeras webbprogram på en helt hanterad tjänst för Linux-baserade. Distribuera program med Maven-plugin-program från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller huvudbegrepp och instruktioner för Java-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service, följer du de [Java-quickstart](quickstart-java.md) och [Java med PostgreSQL självstudien](tutorial-java-enterprise-postgresql-app.md) första.

## <a name="logging-and-debugging-apps"></a>Loggning och felsökning av appar

Prestandarapporter trafik visualiseringar och hälsa kontroller är tillgängliga för varje app via Azure-portalen. Mer information finns i [översikt över Azure App Service-diagnostik](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>SSH-konsolåtkomst

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns i [Direktuppspelningsloggar med Azure CLI](../troubleshoot-diagnostic-logs.md#streaming-with-azure-cli).

### <a name="app-logging"></a>App-loggning

Aktivera [programloggning](../troubleshoot-diagnostic-logs.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#enablediag) via Azure portal eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) att konfigurera App Service för att skriva ditt programs standard konsolens utdata och felströmmar för standard-konsolen till lokalt filsystem eller Azure Blob Storage. Loggning till lokala App Service-filsystemet inaktiveras instans 12 timmar efter att den är konfigurerad. Om du behöver längre kvarhållning kan du konfigurera programmet att skriva utdata till ett Blob storage-behållare.

Om programmet använder [Logback](https://logback.qos.ch/) eller [Log4j](https://logging.apache.org/log4j) för att analysera och du kan vidarebefordra spårning för granskning i Azure Application Insights med hjälp av loggning framework konfigurationsanvisningar i [Utforska Java spårningsloggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service för Linux stöder från box justering och anpassning genom Azure portal och CLI. Granska följande artiklar för icke-Java-specifika web app-konfiguration:

- [Konfigurera inställningar för App Service](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Konfigurera en anpassad domän](../app-service-web-tutorial-custom-domain.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Aktivera SSL](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)
- [Lägga till ett CDN](../../cdn/cdn-add-to-web-app.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)

### <a name="set-java-runtime-options"></a>Ange alternativ för Java runtime

Ange allokerat minne eller andra alternativ för körning av JVM i Tomcat- och Java SE-miljöer och ange JAVA_OPTS enligt nedan som en [programinställning](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings). App Service Linux skickar den här inställningen som en miljövariabel till Java runtime när den startas.

I Azure-portalen under **programinställningar** för webbappen, skapa en ny appinställning med namnet `JAVA_OPTS` som omfattar ytterligare inställningar som `$JAVA_OPTS -Xms512m -Xmx1204m`.

Om du vill konfigurera appinställningen från Azure App Service Linux Maven-pluginprogrammet lägger du till inställningen/värde-taggar i avsnittet Azure-plugin-programmet. I följande exempel anger en viss lägsta och högsta Java heap storlek:

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
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om ditt program:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
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

Appautentisering i Azure-portalen med den **autentisering och auktorisering** alternativet. Därifrån kan aktivera du autentisering med hjälp av Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Konfiguration av Azure portal fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [konfigurera App Service-appen för att använda Azure Active Directory-inloggning](../configure-authentication-provider-aad.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) och relaterade artiklar för andra identitetsleverantörer.

Om du vill aktivera flera inloggning providers, följ instruktionerna i den [anpassa App Service-autentisering](../app-service-authentication-how-to.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) artikeln.

 Spring Boot-utvecklare kan använda den [Azure Active Directory Spring Boot starter](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) att skydda program med hjälp av välbekanta Spring Security anteckningar och API: er.

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i den [binda ett befintligt anpassat SSL-certifikat](../app-service-web-tutorial-custom-ssl.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json) att ladda upp ett befintligt SSL-certifikat och bind det till ditt programs domännamn. Som standard kommer ditt program fortfarande att HTTP-anslutningar – Följ specifikt steg i guiden för att framtvinga SSL och TLS.

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

## <a name="configure-tomcat"></a>Konfigurera Tomcat

### <a name="connect-to-data-sources"></a>Anslut till datakällor

>[!NOTE]
> Om programmet använder Spring Framework eller Spring Boot, kan du ange anslutningsinformation för databasen för Spring Data JPA som miljövariabler för [i filen programmet egenskaper]. Använd sedan [appinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) att definiera dessa värden för ditt program i Azure-portalen eller CLI.

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

## <a name="configure-wildfly-server"></a>Konfigurera WildFly server

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

Ange [appinställningar](../web-sites-configure.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#app-settings) i programkonfigurationen att skicka miljövariabler för användning i skriptet. Programinställningar Behåll anslutningssträngar och andra hemligheter som behövs för att konfigurera ditt program utanför versionskontroll.

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
2. På bladet programinställningar i Azure-portalen ange fältet ”startskript” till platsen för din startskript shell, till exempel `/home/site/deployments/tools/your-startup-script.sh` .
3. Starta om din App Service-instans genom att trycka på den **starta om** knappen i den **översikt** avsnittet i portalen eller med hjälp av Azure CLI.

### <a name="data-sources"></a>Datakällor

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

Allmänna frågor om hur du använder App Service för Linux som inte är specifika för Java-utveckling besvaras inom den [App Service Linux vanliga frågor och svar](app-service-linux-faq.md).