---
title: Konfigurera Windows Java-appar
description: Lär dig hur du konfigurerar Java-appar så att de körs på Windows VM-instanserna i Azure App Service. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
keywords: azure app tjänst, webbapp, fönster, oss, java
author: jasonfreeberg
ms.devlang: java
ms.topic: article
ms.date: 04/12/2019
ms.author: jafreebe
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 2b21061e8a939b91c637ef05bbe6375c0b3f82e8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80383981"
---
# <a name="configure-a-windows-java-app-for-azure-app-service"></a>Konfigurera en Windows Java-app för Azure App Service

Med Azure App Service kan Java-utvecklare snabbt skapa, distribuera och skala sina Tomcat-webbprogram på en fullständigt hanterad Windows-baserad tjänst. Distribuera program med Maven-plugins från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder i App Service. Om du aldrig har använt Azure App Service bör du läsa igenom [snabbstarten](app-service-web-get-started-java.md) för Java först. Allmänna frågor om hur du använder App Service som inte är specifika för Java-utvecklingen besvaras i [vanliga frågor och svar om App Service Windows](faq-configuration-and-management.md).

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [Azure Web App Plugin för Maven för](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) att distribuera dina .war-filer. Distribution med populära ID:er stöds också med [Azure Toolkit för IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) eller [Azure Toolkit för Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Annars beror distributionsmetoden på arkivtypen:

- Om du vill distribuera .war-filer till Tomcat använder du `/api/wardeploy/` slutpunkten för att publicera arkivfilen. Mer information om det här API:et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Om du vill distribuera JAR-filer `/api/zipdeploy/` till Java SE använder du slutpunkten för Kudu-webbplatsen. Mer information om det här API:et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Distribuera inte ditt .war med FTP. FTP-verktyget är utformat för att ladda upp startskript, beroenden eller andra körningsfiler. Det är inte det optimala valet för distribution av webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och felsöka appar

Prestandarapporter, trafikvisualiseringar och hälsokontroller är tillgängliga för varje app via Azure-portalen. Mer information finns i [diagnostiköversikten för Azure App Service](overview-diagnostics.md).

### <a name="use-flight-recorder"></a>Använd flight recorder

Alla Java-runtimes på App Service med Azul JVMs levereras med Zulu Flight Recorder. Du kan använda detta för att registrera JVM-, system- och Java-nivåhändelser för att övervaka beteendet och felsöka problem i dina Java-program.

För att ta en tidsinniga inspelning behöver du PID (Process ID) i Java-programmet. Om du vill hitta PID öppnar du en webbläsare till webbappens SCM-webbplats på https://<din webbplatsnamn>.scm.azurewebsites.net/ProcessExplorer/. På den här sidan visas de processer som körs i webbappen. Hitta processen med namnet "java" i tabellen och kopiera motsvarande PID (Process ID).

Öppna sedan **felsökningskonsolen** i det övre verktygsfältet på SCM-webbplatsen och kör följande kommando. Ersätt `<pid>` med det process-ID som du kopierade tidigare. Det här kommandot startar en 30 sekunders profilering `timed_recording_example.jfr` av `D:\home` Java-programmet och genererar en fil som heter i katalogen.

```
jcmd <pid> JFR.start name=TimedRecording settings=profile duration=30s filename="D:\home\timed_recording_example.JFR"
```

Mer information finns i [Jcmd Command Reference](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

#### <a name="analyze-jfr-files"></a>Analysera `.jfr` filer

Använd [FTPS](deploy-ftp.md) för att ladda ned JFR-filen till din lokala dator. För att analysera JFR-filen, ladda ner och installera [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Instruktioner om Zulu Mission Control finns i [Azuldokumentationen](https://docs.azul.com/zmc/) och [installationsanvisningarna](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns [i Strömma loggar i Cloud Shell](troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Apploggning

Aktivera [programloggning](troubleshoot-diagnostic-logs.md#enable-application-logging-windows) via Azure-portalen eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service för att skriva programmets standardkonsolutdata och standardkonsolfelströmmar till det lokala filsystemet eller Azure Blob Storage. Loggning till den lokala App Service-filsysteminstansen inaktiveras 12 timmar efter att den har konfigurerats. Om du behöver längre kvarhållning konfigurerar du programmet så att det skriver utdata till en Blob-lagringsbehållare. Dina Java- och Tomcat-apploggar finns i katalogen */LogFiles/Application/.*

Om ditt program använder [Logback](https://logback.qos.ch/) eller [Log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra dessa spårningar för granskning av Azure Application Insights med hjälp av konfigurationsinstruktionerna för loggningsramverk i [Utforska Java-spårningsloggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs).


## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service stöder out of the box tuning and customization via Azure portal och CLI. Läs följande artiklar för konfiguration av icke-Java-specifika webbappar:

- [Konfigurera appinställningar](configure-common.md#configure-app-settings)
- [Konfigurera en anpassad domän](app-service-web-tutorial-custom-domain.md)
- [Konfigurera SSL-bindningar](configure-ssl-bindings.md)
- [Lägga till ett CDN](../cdn/cdn-add-to-web-app.md)
- [Konfigurera Kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings)

### <a name="set-java-runtime-options"></a>Ange alternativ för Java-körning

Om du vill ange allokerat minne eller andra JVM-körningsalternativ skapar du en [appinställning](configure-common.md#configure-app-settings) med namnet `JAVA_OPTS` med alternativen. App Service skickar den här inställningen som en miljövariabel till Java-körningen när den startar.

Skapa en ny **Application Settings** appinställning med namnet `JAVA_OPTS` som innehåller ytterligare inställningar, till exempel `-Xms512m -Xmx1204m`.

Om du vill konfigurera appinställningen från Maven-insticksprogrammet lägger du till inställnings-/värdetaggar i avsnittet Azure-plugin. I följande exempel anges en specifik minsta och högsta Java-heapstorlek:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Xms512m -Xmx1204m</value>
    </property>
</appSettings>
```

Utvecklare som kör ett enda program med en distributionsplats i apptjänstplanen kan använda följande alternativ:

- B1- och S1-instanser:`-Xms1024m -Xmx1024m`
- B2- och S2-instanser:`-Xms3072m -Xmx3072m`
- B3- och S3-instanser:`-Xms6144m -Xmx6144m`

När du justerar programhöginställningar läser du information om App Service-planen och tar hänsyn till flera program och distributionsplats måste hitta den optimala allokeringen av minne.

### <a name="turn-on-web-sockets"></a>Aktivera webbuttag

Aktivera stöd för webbuttag i Azure-portalen i **programinställningarna** för programmet. Du måste starta om programmet för att inställningen ska börja gälla.

Aktivera webbuttagsstöd med hjälp av Azure CLI med följande kommando:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --web-sockets-enabled true
```

Starta sedan om programmet:

```azurecli-interactive
az webapp stop --name <app-name> --resource-group <resource-group-name>
az webapp start --name <app-name> --resource-group <resource-group-name>
```

### <a name="set-default-character-encoding"></a>Ange standardteckenkodning

Skapa en ny appinställning med namnet `JAVA_OPTS` med värde `-Dfile.encoding=UTF-8`i Azure-portalen under **Programinställningar** för webbappen .

Du kan också konfigurera appinställningen med hjälp av plugin-programmet App Service Maven. Lägg till inställningsnamn och värdetaggar i instickskonfigurationen:

```xml
<appSettings>
    <property>
        <name>JAVA_OPTS</name>
        <value>-Dfile.encoding=UTF-8</value>
    </property>
</appSettings>
```

### <a name="pre-compile-jsp-files"></a>Förkompilera JSP-filer

För att förbättra prestanda för Tomcat-program kan du kompilera dina JSP-filer innan du distribuerar till App Service. Du kan använda [Maven plugin](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) tillhandahålls av Apache Sling, eller med hjälp av denna [Ant bygga fil](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service har samma uppsättning [säkerhetstips](/azure/security/security-paas-applications-using-app-services) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (Easy Auth)

Konfigurera appautentisering i Azure-portalen med alternativet **Autentisering och auktorisering.** Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure portal-konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera apptjänstappen för att använda Azure Active Directory-inloggning](configure-authentication-provider-aad.md) och relaterade artiklar för andra identitetsleverantörer. Om du behöver aktivera flera inloggningsleverantörer följer du instruktionerna i autentiseringsartikeln för anpassning av [App Service.](app-service-authentication-how-to.md)

#### <a name="tomcat"></a>Tomcat

Ditt Tomcat-program kan komma åt användarens anspråk direkt från servlet genom att casta huvudobjektet till ett Map-objekt. Kartobjektet mappar varje anspråkstyp till en samling anspråk för den typen. I koden nedan `request` är en `HttpServletRequest`instans av .

```java
Map<String, Collection<String>> map = (Map<String, Collection<String>>) request.getUserPrincipal();
```

Nu kan du `Map` inspektera objektet för ett specifikt anspråk. Följande kodavsnitt itererar till exempel igenom alla anspråkstyper och skriver ut innehållet i varje samling.

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

Om du vill logga `/.auth/ext/logout` ut användare använder du sökvägen. Information om hur du utför andra åtgärder finns i dokumentationen om [apptjänstautentisering och auktoriseringsanvändning](https://docs.microsoft.com/azure/app-service/app-service-authentication-how-to). Det finns också officiell dokumentation om Tomcat [HttpServletRequest gränssnitt](https://tomcat.apache.org/tomcat-5.5-doc/servletapi/javax/servlet/http/HttpServletRequest.html) och dess metoder. Följande servletmetoder är också hydrerade baserat på apptjänstkonfigurationen:

```java
public boolean isSecure()
public String getRemoteAddr()
public String getRemoteHost()
public String getScheme()
public int getServerPort()
```

Om du vill inaktivera den `WEBSITE_AUTH_SKIP_PRINCIPAL` här funktionen `1`skapar du en programinställning med namnet . Om du vill inaktivera alla servletfilter som `WEBSITE_SKIP_FILTERS` läggs till `1`av App Service skapar du en inställning med namnet .

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i [Secure ett anpassat DNS-namn med en SSL-bindning i Azure App Service](configure-ssl-bindings.md) för att ladda upp ett befintligt SSL-certifikat och binda det till ditt programs domännamn. Som standard tillåter ditt program fortfarande HTTP-anslutningar att följa de specifika stegen i självstudien för att framtvinga SSL och TLS.

### <a name="use-keyvault-references"></a>Använda KeyVault-referenser

[Azure KeyVault](../key-vault/key-vault-overview.md) tillhandahåller centraliserad hemlig hantering med åtkomstprinciper och granskningshistorik. Du kan lagra hemligheter (till exempel lösenord eller anslutningssträngar) i KeyVault och komma åt dessa hemligheter i ditt program via miljövariabler.

Följ först instruktionerna för [att ge din app åtkomst till Key Vault](app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och göra en [KeyVault-referens till din hemlighet i en programinställning](app-service-key-vault-references.md#reference-syntax). Du kan verifiera att referensen matchas till hemligheten genom att skriva ut miljövariabeln när du fjärransluter till App Service-terminalen.

Om du vill injicera dessa hemligheter i konfigurationsfilen För`${MY_ENV_VAR}`våren eller Tomcat använder du miljövariabel injektionssyntax ( ). För spring-konfigurationsfiler, se den här dokumentationen om [externaliserade konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).


## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribueras på Azure App Service på Linux med APM-plattformarna (NewRelic och AppDynamics Application Performance Monitoring).

### <a name="configure-new-relic"></a>Konfigurera ny relik

1. Skapa ett nytt relikkonto på [NewRelic.com](https://newrelic.com/signup)
2. Ladda ner Java-agenten från NewRelic, kommer det att ha ett filnamn som liknar *newrelic-java-x.x.x.zip .*
3. Kopiera din licensnyckel, du behöver den för att konfigurera agenten senare.
4. Använd [Kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att skapa en ny katalog */home/site/wwwroot/apm*.
5. Ladda upp de uppackade Nya Relic Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent bör vara i */ hem / webbplats / wwwroot / apm / newrelic*.
6. Ändra YAML-filen på */home/site/wwwroot/apm/newrelic/newrelic.yml* och ersätt platshållarlicensvärdet med din egen licensnyckel.
7. I Azure-portalen bläddrar du till ditt program i App Service och skapar en ny programinställning.
    - Om din app använder **Java SE**skapar `JAVA_OPTS` du `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`en miljövariabel med namnet .
    - Om du använder **Tomcat**skapar du `CATALINA_OPTS` en miljövariabel med namnet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Ladda ner Java-agenten från AppDynamics webbplats, filnamnet kommer att likna *AppServerAgent-x.x.x.xxxxx.zip*
3. Använd [Kudu-konsolen](https://github.com/projectkudu/kudu/wiki/Kudu-console) för att skapa en ny katalog */home/site/wwwroot/apm*.
4. Ladda upp Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent ska finnas i */home/site/wwwroot/apm/appdynamics*.
5. I Azure-portalen bläddrar du till ditt program i App Service och skapar en ny programinställning.
    - Om du använder **Java SE**skapar du `JAVA_OPTS` en `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` miljövariabel med namnet med värdet där `<app-name>` är ditt apptjänstnamn.
    - Om du använder **Tomcat**skapar du `CATALINA_OPTS` en miljövariabel med namnet med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt apptjänstnamn.

>  Om du redan har `JAVA_OPTS` en `CATALINA_OPTS`miljövariabel `-javaagent:/...` för eller lägger du till alternativet i slutet av det aktuella värdet.

## <a name="data-sources"></a>Datakällor

### <a name="tomcat"></a>Tomcat

Dessa instruktioner gäller för alla databasanslutningar. Du måste fylla platshållare med den valda databasens drivrutinsklassnamn och JAR-fil. Tillhandahålls är en tabell med klassnamn och hämtningar av drivrutiner för vanliga databaser.

| Databas   | Namn på förares klass                             | JDBC-drivrutin                                                                      |
|------------|-----------------------------------------------|------------------------------------------------------------------------------------------|
| PostgreSQL | `org.postgresql.Driver`                        | [Ladda ned](https://jdbc.postgresql.org/download.html)                                    |
| MySQL      | `com.mysql.jdbc.Driver`                        | [Ladda ner](https://dev.mysql.com/downloads/connector/j/) (Välj "Plattform Oberoende") |
| SQL Server | `com.microsoft.sqlserver.jdbc.SQLServerDriver` | [Ladda ned](https://docs.microsoft.com/sql/connect/jdbc/download-microsoft-jdbc-driver-for-sql-server?view=sql-server-2017#download)                                                           |

Om du vill konfigurera Tomcat så att den använder Java Database Connectivity (JDBC) eller Java Persistence API (JPA) anpassar du först `CATALINA_OPTS` miljövariabeln som läss in av Tomcat vid start. Ange dessa värden via en appinställning i [plugin:en App Service Maven:](https://github.com/Microsoft/azure-maven-plugins/blob/develop/azure-webapp-maven-plugin/README.md)

```xml
<appSettings>
    <property>
        <name>CATALINA_OPTS</name>
        <value>"$CATALINA_OPTS -Ddbuser=${DBUSER} -Ddbpassword=${DBPASSWORD} -DconnURL=${CONNURL}"</value>
    </property>
</appSettings>
```

Eller ange miljövariabler på sidan **Inställningar för konfigurationsprogram** > **Application Settings** i Azure-portalen.

Ta sedan reda på om datakällan ska vara tillgänglig för ett program eller för alla program som körs på Tomcat servlet.

#### <a name="application-level-data-sources"></a>Datakällor på programnivå

1. Skapa en *context.xml-fil* i *katalogen META-INF/* i projektet. Skapa *KATALOGEN META-INF/om* den inte finns.

2. I *context.xml*lägger du till ett `Context` element för att länka datakällan till en JNDI-adress. Ersätt `driverClassName` platshållaren med förarens klassnamn från tabellen ovan.

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

3. Uppdatera programmets *web.xml* för att använda datakällan i programmet.

    ```xml
    <resource-env-ref>
        <resource-env-ref-name>jdbc/dbconnection</resource-env-ref-name>
        <resource-env-ref-type>javax.sql.DataSource</resource-env-ref-type>
    </resource-env-ref>
    ```

#### <a name="finalize-configuration"></a>Slutför konfigurationen

Slutligen placerar vi förarens JARs i Tomcat-klassökvägen och startar om apptjänsten. Kontrollera att JDBC-drivrutinsfilerna är tillgängliga för Tomcat classloader genom att placera dem i katalogen */home/tomcat/lib.* (Skapa den här katalogen om den inte redan finns.) Så här laddar du upp dessa filer till App Service-instansen:

1. Installera webapp-tillägget i [Cloud Shell:](https://shell.azure.com)

    ```azurecli-interactive
    az extension add -–name webapp
    ```

2. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

    ```azurecli-interactive
    az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
    ```

3. Anslut till den lokala tunnelporten med SFTP-klienten och ladda upp filerna till mappen */home/tomcat/lib.*

Du kan också använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [instruktionerna för att hämta dina FTP-autentiseringsuppgifter](deploy-configure-credentials.md).

## <a name="configuring-tomcat"></a>Konfigurera Tomcat

Om du vill `server.xml` redigera Tomcats eller andra konfigurationsfiler bör du först ta del av tomcat-huvudversionen i portalen.

1. Leta reda på Tomcats arbetskatalog `env` för din version genom att köra kommandot. Sök efter den miljövariabel `AZURE_TOMCAT`som börjar med och matchar huvudversionen. Pekar till `AZURE_TOMCAT85_HOME` exempel på Tomcat-katalogen för Tomcat 8.5.
1. När du har identifierat Tomcat-arbetskatalogen för din `D:\home`version kopierar du konfigurationskatalogen till . Om du `AZURE_TOMCAT85_HOME` till exempel `D:\Program Files (x86)\apache-tomcat-8.5.37`har ett värde på skulle den `D:\home\apache-tomcat-8.5.37`nya sökvägen till den kopierade katalogen vara .

Slutligen startar du om din App Service. Dina distributioner bör `D:\home\site\wwwroot\webapps` gå till precis som tidigare.

## <a name="configure-java-se"></a>Konfigurera Java SE

När du kör en . JAR-programmet på Java `server.port` SE på Windows, skickas som ett kommandoradsalternativ när ditt program startar. Du kan manuellt lösa HTTP-porten från `HTTP_PLATFORM_PORT`miljövariabeln . Värdet för den här miljövariabeln är den HTTP-port som programmet ska lyssna på. 

## <a name="java-runtime-statement-of-support"></a>Java-körningsuttalande för support

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azures Java Development Kit (JDK) som stöds är [Zulu](https://www.azul.com/downloads/azure-only/zulu/) som tillhandahålls via [Azul Systems](https://www.azul.com/).

Viktiga versionsuppdateringar kommer att tillhandahållas via nya körningsalternativ i Azure App Service för Windows. Kunder uppdaterar till dessa nyare versioner av Java genom att konfigurera sin App Service-distribution och ansvarar för att testa och se till att den stora uppdateringen uppfyller deras behov.

Gemensamma tillsynspunkter som stöds korrigeras automatiskt kvartalsvis i januari, april, juli och oktober varje år. Mer information om Java på Azure finns i [det här supportdokumentet](https://docs.microsoft.com/azure/java/jdk/).

### <a name="security-updates"></a>Säkerhetsuppdateringar

Patchar och korrigeringar för större säkerhetsproblem kommer att släppas så snart de blir tillgängliga från Azul Systems. Ett "större" säkerhetsproblem definieras av en baspoäng på 9,0 eller högre på [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

Tomcat 8.0 har nått [End of Life (EOL) per den 30 september 2018](https://tomcat.apache.org/tomcat-80-eol.html). Körningen är fortfarande avialbar på Azure App Service, men Azure kommer inte att tillämpa säkerhetsuppdateringar på Tomcat 8.0. Om möjligt migrerar du dina program till Tomcat 8.5 eller 9.0. Både Tomcat 8.5 och 9.0 är tillgängliga på Azure App Service. Se den [officiella Tomcat-webbplatsen](https://tomcat.apache.org/whichversion.html) för mer information. 

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds kommer att dras tillbaka, kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om utfasning minst sex månader innan körningen dras tillbaka.

### <a name="local-development"></a>Lokal utveckling

Utvecklare kan ladda ner Production Edition av Azul Zulu Enterprise JDK för lokal utveckling från [Azul nedladdning webbplats](https://www.azul.com/downloads/azure-only/zulu/).

### <a name="development-support"></a>Utvecklingsstöd

Produktsupport för [Azure-stödda Azul Zulu JDK](https://www.azul.com/downloads/azure-only/zulu/) är tillgänglig via Microsoft när du utvecklar för Azure eller [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) med en kvalificerad [Azure-supportplan](https://azure.microsoft.com/support/plans/).

### <a name="runtime-support"></a>Support för körning

Utvecklare kan [öppna ett problem](/azure/azure-portal/supportability/how-to-create-azure-support-request) med Azul Zulu JDK:er via Azure Support om de har en kvalificerad [supportplan](https://azure.microsoft.com/support/plans/).

## <a name="next-steps"></a>Nästa steg

Det här avsnittet innehåller Java Runtime-supportförklaringen för Azure App Service i Windows.

- Mer information om hur du är värd för webbprogram med Azure App Service finns i [översikt över App-tjänsten](overview.md).
- Information om Java om Azure-utveckling finns i [Azure för Java Dev Center](https://docs.microsoft.com/java/azure/?view=azure-java-stable).
