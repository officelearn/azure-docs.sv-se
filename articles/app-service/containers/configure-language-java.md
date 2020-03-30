---
title: Konfigurera Linux Java-appar
description: Läs om hur du konfigurerar en förbyggd Java-behållare för din app. Den här artikeln visar de vanligaste konfigurationsuppgifterna.
keywords: azure app tjänst, webbapp, linux, oss, java, java ee, jee, javaee
author: bmitchell287
manager: barbkess
ms.devlang: java
ms.topic: article
ms.date: 11/22/2019
ms.author: brendm
ms.reviewer: cephalin
ms.custom: seodec18
ms.openlocfilehash: 970701606811cbd61a9bfebe39ff82cdc91d5693
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245845"
---
# <a name="configure-a-linux-java-app-for-azure-app-service"></a>Konfigurera en Linux Java-app för Azure App Service

Med Azure App Service på Linux kan Java-utvecklare snabbt skapa, distribuera och skala sina Tomcat- eller Java Standard Edition (SE) paketerade webbprogram på en fullständigt hanterad Linux-baserad tjänst. Distribuera program med Maven-plugins från kommandoraden eller i redigerare som IntelliJ, Eclipse eller Visual Studio Code.

Den här guiden innehåller viktiga begrepp och instruktioner för Java-utvecklare som använder en inbyggd Linux-behållare i App Service. Om du aldrig har använt Azure App Service följer du [snabbstarten för Java](quickstart-java.md).

## <a name="deploying-your-app"></a>Distribuera din app

Du kan använda [Maven Plugin för Azure App Service för](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme) att distribuera både .jar- och .war-filer. Distribution med populära ID:er stöds också med [Azure Toolkit för IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij) eller [Azure Toolkit för Eclipse](/java/azure/eclipse/azure-toolkit-for-eclipse).

Annars beror distributionsmetoden på arkivtypen:

- Om du vill distribuera .war-filer till Tomcat använder du `/api/wardeploy/` slutpunkten för att publicera arkivfilen. Mer information om det här API:et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#deploy-war-file).
- Om du vill distribuera JAR-filer på `/api/zipdeploy/` Java SE-avbildningarna använder du slutpunkten för Kudu-webbplatsen. Mer information om det här API:et finns i [den här dokumentationen](https://docs.microsoft.com/azure/app-service/deploy-zip#rest).

Distribuera inte din .war eller .jar med FTP. FTP-verktyget är utformat för att ladda upp startskript, beroenden eller andra körningsfiler. Det är inte det optimala valet för distribution av webbappar.

## <a name="logging-and-debugging-apps"></a>Logga och felsöka appar

Prestandarapporter, trafikvisualiseringar och hälsokontroller är tillgängliga för varje app via Azure-portalen. Mer information finns i [diagnostiköversikten för Azure App Service](../overview-diagnostics.md).

### <a name="ssh-console-access"></a>Åtkomst till SSH-konsolen

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

### <a name="stream-diagnostic-logs"></a>Strömma diagnostikloggar

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

Mer information finns [i Strömma loggar i Cloud Shell](../troubleshoot-diagnostic-logs.md#in-cloud-shell).

### <a name="app-logging"></a>Apploggning

Aktivera [programloggning](../troubleshoot-diagnostic-logs.md?toc=/azure/app-service/containers/toc.json#enable-application-logging-windows) via Azure-portalen eller [Azure CLI](/cli/azure/webapp/log#az-webapp-log-config) för att konfigurera App Service för att skriva programmets standardkonsolutdata och standardkonsolfelströmmar till det lokala filsystemet eller Azure Blob Storage. Loggning till den lokala App Service-filsysteminstansen inaktiveras 12 timmar efter att den har konfigurerats. Om du behöver längre kvarhållning konfigurerar du programmet så att det skriver utdata till en Blob-lagringsbehållare. Dina Java- och Tomcat-apploggar finns i katalogen */home/logfiles/application/.*

Om ditt program använder [Logback](https://logback.qos.ch/) eller [Log4j](https://logging.apache.org/log4j) för spårning kan du vidarebefordra dessa spårningar för granskning av Azure Application Insights med hjälp av konfigurationsinstruktionerna för loggningsramverk i [Utforska Java-spårningsloggar i Application Insights](/azure/application-insights/app-insights-java-trace-logs).

### <a name="troubleshooting-tools"></a>Felsökningsverktyg

De inbyggda Java-avbildningarna är baserade på [det alpina operativsystemet Linux.](https://alpine-linux.readthedocs.io/en/latest/getting_started.html) Använd `apk` pakethanteraren för att installera felsökningsverktyg eller kommandon.

### <a name="flight-recorder"></a>Färdskrivare

Alla Linux Java-avbildningar på App Service har Zulu Flight Recorder installerat så att du enkelt kan ansluta till JVM och starta en profiler inspelning eller generera en hög dump.

#### <a name="timed-recording"></a>Tidsinnad inspelning

För att komma igång, SSH i `jcmd` din App Service och köra kommandot för att se en lista över alla Java-processer som körs. Förutom jcmd själv, bör du se din Java-program som körs med ett process-ID-nummer (pid).

```shell
078990bbcd11:/home# jcmd
Picked up JAVA_TOOL_OPTIONS: -Djava.net.preferIPv4Stack=true
147 sun.tools.jcmd.JCmd
116 /home/site/wwwroot/app.jar
```

Kör kommandot nedan för att starta en 30-sekunders inspelning av JVM. Detta kommer att profilera JVM och skapa en JFR-fil med namnet *jfr_example.jfr* i arbetskatalogen. (Ersätt 116 med pid-appen i java-appen.)

```shell
jcmd 116 JFR.start name=MyRecording settings=profile duration=30s filename="/home/jfr_example.jfr"
```

Under intervallet 30 sekunder kan du validera inspelningen `jcmd 116 JFR.check`sker genom att köra . Detta kommer att visa alla inspelningar för den givna Java-processen.

#### <a name="continuous-recording"></a>Kontinuerlig inspelning

Du kan använda Zulu Flight Recorder för att kontinuerligt profilera ditt Java-program med minimal inverkan på körningsprestanda[(källa).](https://assets.azul.com/files/Zulu-Mission-Control-data-sheet-31-Mar-19.pdf) För att göra det, kör följande Azure CLI-kommando för att skapa en appinställning med namnet JAVA_OPTS med nödvändig konfiguration. Innehållet i JAVA_OPTS appinställning skickas till `java` kommandot när appen startas.

```azurecli
az webapp config appsettings set -g <your_resource_group> -n <your_app_name> --settings JAVA_OPTS=-XX:StartFlightRecording=disk=true,name=continuous_recording,dumponexit=true,maxsize=1024m,maxage=1d
```

När inspelningen har startat kan du dumpa de aktuella `JFR.dump` inspelningsdata när som helst med kommandot.

```shell
jcmd <pid> JFR.dump name=continuous_recording filename="/home/recording1.jfr"
```

Mer information finns i [Jcmd Command Reference](https://docs.oracle.com/javacomponents/jmc-5-5/jfr-runtime-guide/comline.htm#JFRRT190).

### <a name="analyzing-recordings"></a>Analysera inspelningar

Använd [FTPS](../deploy-ftp.md) för att ladda ned JFR-filen till din lokala dator. För att analysera JFR-filen, ladda ner och installera [Zulu Mission Control](https://www.azul.com/products/zulu-mission-control/). Instruktioner om Zulu Mission Control finns i [Azuldokumentationen](https://docs.azul.com/zmc/) och [installationsanvisningarna](https://docs.microsoft.com/java/azure/jdk/java-jdk-flight-recorder-and-mission-control).

## <a name="customization-and-tuning"></a>Anpassning och justering

Azure App Service for Linux stöder out of the box tuning and customization via Azure portal och CLI. Läs följande artiklar för konfiguration av icke-Java-specifika webbappar:

- [Konfigurera appinställningar](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings)
- [Konfigurera en anpassad domän](../app-service-web-tutorial-custom-domain.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurera SSL-bindningar](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json)
- [Lägga till ett CDN](../../cdn/cdn-add-to-web-app.md?toc=/azure/app-service/containers/toc.json)
- [Konfigurera Kudu-webbplatsen](https://github.com/projectkudu/kudu/wiki/Configurable-settings#linux-on-app-service-settings)

### <a name="set-java-runtime-options"></a>Ange alternativ för Java-körning

Om du vill ange allokerat minne eller andra JVM-körningsalternativ i både `JAVA_OPTS` Tomcat- och Java SE-miljöer skapar du en [appinställning](../configure-common.md?toc=/azure/app-service/containers/toc.json#configure-app-settings) med namnet med alternativen. App Service Linux skickar den här inställningen som en miljövariabel till Java-körningen när den startar.

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

Om du distribuerar ett JAR-program bör det heta *app.jar* så att den inbyggda avbildningen kan identifiera din app korrekt. (Maven plugin gör detta byta namn automatiskt.) Om du inte vill byta namn på JAR till *app.jar*kan du ladda upp ett skalskript med kommandot för att köra din JAR. Klistra sedan in den fullständiga sökvägen till skriptet i textrutan [Startfil](app-service-linux-faq.md#built-in-images) i konfigurationsavsnittet i portalen. Startskriptet körs inte från den katalog där den placeras. Använd därför alltid absoluta sökvägar för att referera till filer i startskriptet (till exempel: `java -jar /home/myapp/myapp.jar`).

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

### <a name="adjust-startup-timeout"></a>Justera timeout för start

Om ditt Java-program är särskilt stort bör du öka starttidens gräns. Det gör du genom att `WEBSITES_CONTAINER_START_TIME_LIMIT` skapa en programinställning och ange den till antalet sekunder som App Service ska vänta innan du går ut. Det maximala `1800` värdet är sekunder.

### <a name="pre-compile-jsp-files"></a>Förkompilera JSP-filer

För att förbättra prestanda för Tomcat-program kan du kompilera dina JSP-filer innan du distribuerar till App Service. Du kan använda [Maven plugin](https://sling.apache.org/components/jspc-maven-plugin/plugin-info.html) tillhandahålls av Apache Sling, eller med hjälp av denna [Ant bygga fil](https://tomcat.apache.org/tomcat-9.0-doc/jasper-howto.html#Web_Application_Compilation).

## <a name="secure-applications"></a>Säkra program

Java-program som körs i App Service för Linux har samma uppsättning [säkerhetstips](/azure/security/security-paas-applications-using-app-services) som andra program.

### <a name="authenticate-users-easy-auth"></a>Autentisera användare (Easy Auth)

Konfigurera appautentisering i Azure-portalen med alternativet **Autentisering och auktorisering.** Därifrån kan du aktivera autentisering med Azure Active Directory eller sociala inloggningar som Facebook, Google eller GitHub. Azure portal-konfiguration fungerar bara när du konfigurerar en enda autentiseringsprovider. Mer information finns i [Konfigurera apptjänstappen för att använda Azure Active Directory-inloggning](../configure-authentication-provider-aad.md?toc=/azure/app-service/containers/toc.json) och relaterade artiklar för andra identitetsleverantörer. Om du behöver aktivera flera inloggningsleverantörer följer du instruktionerna i autentiseringsartikeln för anpassning av [App Service.](../app-service-authentication-how-to.md?toc=/azure/app-service/containers/toc.json)

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

#### <a name="spring-boot"></a>Spring Boot

Spring Boot-utvecklare kan använda [startmotorn Azure Active Directory Spring Boot](/java/azure/spring-framework/configure-spring-boot-starter-java-app-with-azure-active-directory?view=azure-java-stable) för att skydda program med välbekanta Spring Security-anteckningar och API:er. Var noga med att öka den maximala rubrikstorleken i filen *application.properties.* Vi föreslår ett `16384`värde av .

### <a name="configure-tlsssl"></a>Konfigurera TLS/SSL

Följ instruktionerna i [Secure ett anpassat DNS-namn med en SSL-bindning i Azure App Service](../configure-ssl-bindings.md?toc=/azure/app-service/containers/toc.json) för att ladda upp ett befintligt SSL-certifikat och binda det till ditt programs domännamn. Som standard tillåter ditt program fortfarande HTTP-anslutningar att följa de specifika stegen i självstudien för att framtvinga SSL och TLS.

### <a name="use-keyvault-references"></a>Använda KeyVault-referenser

[Azure KeyVault](../../key-vault/key-vault-overview.md) tillhandahåller centraliserad hemlig hantering med åtkomstprinciper och granskningshistorik. Du kan lagra hemligheter (till exempel lösenord eller anslutningssträngar) i KeyVault och komma åt dessa hemligheter i ditt program via miljövariabler.

Följ först instruktionerna för [att ge din app åtkomst till Key Vault](../app-service-key-vault-references.md#granting-your-app-access-to-key-vault) och göra en [KeyVault-referens till din hemlighet i en programinställning](../app-service-key-vault-references.md#reference-syntax). Du kan verifiera att referensen matchas till hemligheten genom att skriva ut miljövariabeln när du fjärransluter till App Service-terminalen.

Om du vill injicera dessa hemligheter i konfigurationsfilen För`${MY_ENV_VAR}`våren eller Tomcat använder du miljövariabel injektionssyntax ( ). För spring-konfigurationsfiler, se den här dokumentationen om [externaliserade konfigurationer](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html).

### <a name="using-the-java-key-store"></a>Använda Java Key Store

Som standard kommer alla offentliga eller privata certifikat som [laddas upp till App Service Linux](../configure-ssl-certificate.md) att läsas in i respektive Java-nyckelbutiker när behållaren startar. När du har laddat upp certifikatet måste du starta om apptjänsten för att den ska läsas in i Java Key Store. Offentliga certifikat läses in i `$JAVA_HOME/jre/lib/security/cacerts`Nyckelarkivet i och `$JAVA_HOME/lib/security/client.jks`privata certifikat lagras i .

Ytterligare konfiguration kan vara nödvändig för att kryptera din JDBC-anslutning med certifikat i Java Key Store. Se dokumentationen för din valda JDBC-drivrutin.

- [PostgreSQL](https://jdbc.postgresql.org/documentation/head/ssl-client.html)
- [SQL Server](https://docs.microsoft.com/sql/connect/jdbc/connecting-with-ssl-encryption?view=sql-server-ver15)
- [MySQL](https://dev.mysql.com/doc/connector-j/5.1/en/connector-j-reference-using-ssl.html)
- [Mongodb](https://mongodb.github.io/mongo-java-driver/3.4/driver/tutorials/ssl/)
- [Cassandra](https://docs.datastax.com/en/developer/java-driver/4.3/)

#### <a name="initializing-the-java-key-store"></a>Initiera Java Key Store

Om du `import java.security.KeyStore` vill initiera objektet läser du in keystore-filen med lösenordet. Standardlösenordet för båda nyckelbutikerna är "changeit".

```java
KeyStore keyStore = KeyStore.getInstance("jks");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/cacets"),
    "changeit".toCharArray());

KeyStore keyStore = KeyStore.getInstance("pkcs12");
keyStore.load(
    new FileInputStream(System.getenv("JAVA_HOME")+"/lib/security/client.jks"),
    "changeit".toCharArray());
```

#### <a name="manually-load-the-key-store"></a>Ladda nyckelarkivet manuellt

Du kan läsa in certifikat manuellt till nyckelarkivet. Skapa en `SKIP_JAVA_KEYSTORE_LOAD`appinställning, med `1` värdet för att inaktivera App Service från att läsa in certifikaten i nyckelarkivet automatiskt. Alla offentliga certifikat som överförs till App Service `/var/ssl/certs/`via Azure-portalen lagras under . Privata certifikat lagras `/var/ssl/private/`under .

Du kan interagera eller felsöka Java-nyckelverktyget genom att öppna en `keytool` [SSH-anslutning](app-service-linux-ssh-support.md) till apptjänsten och köra kommandot . Se [dokumentationen](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/keytool.html) för nyckelverktyg för en lista med kommandon. Mer information om KeyStore-API:et finns [i den officiella dokumentationen](https://docs.oracle.com/javase/8/docs/api/java/security/KeyStore.html).

## <a name="configure-apm-platforms"></a>Konfigurera APM-plattformar

Det här avsnittet visar hur du ansluter Java-program som distribueras på Azure App Service på Linux med APM-plattformarna (NewRelic och AppDynamics Application Performance Monitoring).

### <a name="configure-new-relic"></a>Konfigurera ny relik

1. Skapa ett NewRelic-konto på [NewRelic.com](https://newrelic.com/signup)
2. Ladda ner Java-agenten från NewRelic, kommer det att ha ett filnamn som liknar *newrelic-java-x.x.x.zip .*
3. Kopiera din licensnyckel, du behöver den för att konfigurera agenten senare.
4. [SSH i apptjänstinstansen](app-service-linux-ssh-support.md) och skapa en ny katalog */home/site/wwwroot/apm*.
5. Ladda upp de uppackade NewRelic Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent bör vara i */ hem / webbplats / wwwroot / apm / newrelic*.
6. Ändra YAML-filen på */home/site/wwwroot/apm/newrelic/newrelic.yml* och ersätt platshållarlicensvärdet med din egen licensnyckel.
7. I Azure-portalen bläddrar du till ditt program i App Service och skapar en ny programinställning.
    - Om din app använder **Java SE**skapar `JAVA_OPTS` du `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`en miljövariabel med namnet .
    - Om du använder **Tomcat**skapar du `CATALINA_OPTS` en miljövariabel med namnet `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.

### <a name="configure-appdynamics"></a>Konfigurera AppDynamics

1. Skapa ett AppDynamics-konto på [AppDynamics.com](https://www.appdynamics.com/community/register/)
2. Ladda ner Java-agenten från AppDynamics webbplats, filnamnet kommer att likna *AppServerAgent-x.x.x.xxxxx.zip*
3. [SSH i apptjänstinstansen](app-service-linux-ssh-support.md) och skapa en ny katalog */home/site/wwwroot/apm*.
4. Ladda upp Java-agentfilerna till en katalog under */home/site/wwwroot/apm*. Filerna för din agent ska finnas i */home/site/wwwroot/apm/appdynamics*.
5. I Azure-portalen bläddrar du till ditt program i App Service och skapar en ny programinställning.
    - Om du använder **Java SE**skapar du `JAVA_OPTS` en `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` miljövariabel med namnet med värdet där `<app-name>` är ditt apptjänstnamn.
    - Om du använder **Tomcat**skapar du `CATALINA_OPTS` en miljövariabel med namnet med värdet `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<app-name>` där `<app-name>` är ditt apptjänstnamn.

> [!NOTE]
> Om du redan har `JAVA_OPTS` en `CATALINA_OPTS`miljövariabel `-javaagent:/...` för eller lägger du till alternativet i slutet av det aktuella värdet.

## <a name="configure-jar-applications"></a>Konfigurera JAR-program

### <a name="starting-jar-apps"></a>Starta JAR-appar

Som standard förväntar sig App Service att JAR-programmet ska heta *app.jar*. Om det här namnet har körs det automatiskt. För Maven-användare kan du ange `<finalName>app</finalName>` JAR-namnet genom att inkludera i `<build>` avsnittet i din *pom.xml*. [Du kan göra samma sak i Gradle](https://docs.gradle.org/current/dsl/org.gradle.api.tasks.bundling.Jar.html#org.gradle.api.tasks.bundling.Jar:archiveFileName) genom att ange egenskapen. `archiveFileName`

Om du vill använda ett annat namn för JAR måste du också ange [startkommandot](app-service-linux-faq.md#built-in-images) som kör JAR-filen. Till exempel `java -jar my-jar-app.jar`. Du kan ange värdet för startkommandot i portalen, under Konfiguration > Allmänna `STARTUP_COMMAND`inställningar eller med en programinställning med namnet .

### <a name="server-port"></a>Serverport

App Service Linux dirigerar inkommande förfrågningar till port 80, så ditt program bör lyssna på port 80 också. Du kan göra detta i programmets konfiguration (till exempel Spring's *application.properties-filen)* eller i startkommandot (till `java -jar spring-app.jar --server.port=80`exempel). Se följande dokumentation för vanliga Java-ramverk:

- [Spring Boot](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-properties-and-configuration.html#howto-use-short-command-line-arguments)
- [SparkJava (storbritannien)](http://sparkjava.com/documentation#embedded-web-server)
- [Mikronaut](https://docs.micronaut.io/latest/guide/index.html#runningSpecificPort)
- [Spela Ramverk](https://www.playframework.com/documentation/2.6.x/ConfiguringHttps#Configuring-HTTPS)
- [Vertx (vertx)](https://vertx.io/docs/vertx-core/java/#_start_the_server_listening)
- [Kvark](https://quarkus.io/guides/application-configuration-guide)

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

#### <a name="shared-server-level-resources"></a>Delade resurser på servernivå

Om du lägger till en delad datakälla på servernivå måste du redigera Tomcats server.xml. Ladda först upp ett [startskript](app-service-linux-faq.md#built-in-images) och ange sökvägen till skriptet i **Configuration** > **Startup Command**. Du kan ladda upp startskriptet med [FTP](../deploy-ftp.md).

Startskriptet kommer att göra en [xsl-transformering](https://www.w3schools.com/xml/xsl_intro.asp) till filen `/usr/local/tomcat/conf/server.xml`server.xml och utdata den resulterande xml-filen till . Startskriptet ska installera libxslt via apk. Din xsl-fil och startskript kan laddas upp via FTP. Nedan är ett exempel startskript.

```sh
# Install libxslt. Also copy the transform file to /home/tomcat/conf/
apk add --update libxslt

# Usage: xsltproc --output output.xml style.xsl input.xml
xsltproc --output /home/tomcat/conf/server.xml /home/tomcat/conf/transform.xsl /usr/local/tomcat/conf/server.xml
```

Ett exempel xsl fil finns nedan. Exempelet xsl-fil lägger till en ny anslutningsnod i Tomcat server.xml.

```xml
<xsl:stylesheet version="1.0" xmlns:xsl="http://www.w3.org/1999/XSL/Transform">
  <xsl:output method="xml" indent="yes"/>

  <xsl:template match="@* | node()" name="Copy">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()"/>
    </xsl:copy>
  </xsl:template>

  <xsl:template match="@* | node()" mode="insertConnector">
    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template match="comment()[not(../Connector[@scheme = 'https']) and
                                 contains(., '&lt;Connector') and
                                 (contains(., 'scheme=&quot;https&quot;') or
                                  contains(., &quot;scheme='https'&quot;))]">
    <xsl:value-of select="." disable-output-escaping="yes" />
  </xsl:template>

  <xsl:template match="Service[not(Connector[@scheme = 'https'] or
                                   comment()[contains(., '&lt;Connector') and
                                             (contains(., 'scheme=&quot;https&quot;') or
                                              contains(., &quot;scheme='https'&quot;))]
                                  )]
                      ">
    <xsl:copy>
      <xsl:apply-templates select="@* | node()" mode="insertConnector" />
    </xsl:copy>
  </xsl:template>

  <!-- Add the new connector after the last existing Connnector if there is one -->
  <xsl:template match="Connector[last()]" mode="insertConnector">
    <xsl:call-template name="Copy" />

    <xsl:call-template name="AddConnector" />
  </xsl:template>

  <!-- ... or before the first Engine if there is no existing Connector -->
  <xsl:template match="Engine[1][not(preceding-sibling::Connector)]"
                mode="insertConnector">
    <xsl:call-template name="AddConnector" />

    <xsl:call-template name="Copy" />
  </xsl:template>

  <xsl:template name="AddConnector">
    <!-- Add new line -->
    <xsl:text>&#xa;</xsl:text>
    <!-- This is the new connector -->
    <Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true" 
               maxThreads="150" scheme="https" secure="true" 
               keystroreFile="${{user.home}}/.keystore" keystorePass="changeit"
               clientAuth="false" sslProtocol="TLS" />
  </xsl:template>
  
</xsl:stylesheet>
```

#### <a name="finalize-configuration"></a>Slutför konfigurationen

Placera slutligen drivrutinens JAR:er i Tomcat-klasssökvägen och starta om apptjänsten.

1. Kontrollera att JDBC-drivrutinsfilerna är tillgängliga för Tomcat classloader genom att placera dem i katalogen */home/tomcat/lib.* (Skapa den här katalogen om den inte redan finns.) Så här laddar du upp dessa filer till App Service-instansen:

    1. Installera webapp-tillägget i [Cloud Shell:](https://shell.azure.com)

      ```azurecli-interactive
      az extension add -–name webapp
      ```

    2. Kör följande CLI-kommando för att skapa en SSH-tunnel från ditt lokala system till App Service:

      ```azurecli-interactive
      az webapp remote-connection create --resource-group <resource-group-name> --name <app-name> --port <port-on-local-machine>
      ```

    3. Anslut till den lokala tunnelporten med SFTP-klienten och ladda upp filerna till mappen */home/tomcat/lib.*

    Du kan också använda en FTP-klient för att ladda upp JDBC-drivrutinen. Följ de här [instruktionerna för att hämta dina FTP-autentiseringsuppgifter](../deploy-configure-credentials.md?toc=/azure/app-service/containers/toc.json).

2. Om du har skapat en datakälla på servernivå startar du om App Service Linux-programmet. Tomcat `CATALINA_BASE` återställs `/home/tomcat` till och använder den uppdaterade konfigurationen.

### <a name="spring-boot"></a>Spring Boot

Om du vill ansluta till datakällor i Spring Boot-program föreslår vi att du skapar anslutningssträngar och injicerar dem i filen *application.properties.*

1. I avsnittet "Konfiguration" på sidan Apptjänst anger du ett namn för strängen, klistrar in JDBC-anslutningssträngen i värdefältet och anger typen till "Anpassad". Du kan också ställa in den här anslutningssträngen som platsinställning.

    Den här anslutningssträngen är tillgänglig `CUSTOMCONNSTR_<your-string-name>`för vårt program som en miljövariabel med namnet . Anslutningssträngen som vi skapade ovan `CUSTOMCONNSTR_exampledb`kommer till exempel att heta .

2. I filen *application.properties* refererar du till den här anslutningssträngen med miljövariabelnamnet. Till vårt exempel skulle vi använda följande.

    ```yml
    app.datasource.url=${CUSTOMCONNSTR_exampledb}
    ```

Mer information om det här avsnittet finns i dokumentationen för [Spring Boot om dataåtkomst](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-data-access.html) och externa [konfigurationer.](https://docs.spring.io/spring-boot/docs/current/reference/html/boot-features-external-config.html)

## <a name="use-redis-as-a-session-cache-with-tomcat"></a>Använda Redis som sessionscache med Tomcat

Du kan konfigurera Tomcat så att en extern sessionsarkiv som [Azure Cache for Redis används.](/azure/azure-cache-for-redis/) På så sätt kan du bevara användarsessionstillståndet (till exempel kundvagnsdata) när en användare överförs till en annan instans av appen, till exempel när automatisk skalning, omstart eller redundans sker.

Om du vill använda Tomcat med Redis måste du konfigurera appen så att den använder en [PersistentManager-implementering.](https://tomcat.apache.org/tomcat-8.5-doc/config/manager.html) I följande steg förklaras den här processen med Hjälp av [Pivotal Session Manager: redis-store](https://github.com/pivotalsoftware/session-managers/tree/master/redis-store) som exempel.

1. Öppna en Bash-terminal och använd `<variable>=<value>` för att ställa in var och en av följande miljövariabler.

    | Variabel                 | Värde                                                                      |
    |--------------------------|----------------------------------------------------------------------------|
    | RESOURCEGROUP_NAME       | Namnet på resursgruppen som innehåller apptjänstinstansen.       |
    | WEBAPP_NAME              | Namnet på apptjänstinstansen.                                     |
    | WEBAPP_PLAN_NAME         | Namnet på apptjänstplanen.                                         |
    | REGION                   | Namnet på den region där appen finns.                           |
    | REDIS_CACHE_NAME         | Namnet på azure-cache för Redis-instans.                           |
    | REDIS_PORT               | SSL-porten som Redis-cachen lyssnar på.                             |
    | REDIS_PASSWORD           | Den primära åtkomstnyckeln för din instans.                                  |
    | REDIS_SESSION_KEY_PREFIX | Ett värde som du anger för att identifiera sessionsnycklar som kommer från appen. |

    ```bash
    RESOURCEGROUP_NAME=<resource group>
    WEBAPP_NAME=<web app>
    WEBAPP_PLAN_NAME=<App Service plan>
    REGION=<region>
    REDIS_CACHE_NAME=<cache>
    REDIS_PORT=<port>
    REDIS_PASSWORD=<access key>
    REDIS_SESSION_KEY_PREFIX=<prefix>
    ```

    Du kan hitta namn, port och åtkomst till viktig information på Azure-portalen genom att titta i avsnitten **Egenskaper** eller **Åtkomstnycklar** i tjänstinstansen.

2. Skapa eller uppdatera appens *src/main/webapp/META-INF/context.xml-fil* med följande innehåll:

    ```xml
    <?xml version="1.0" encoding="UTF-8"?>
    <Context path="">
        <!-- Specify Redis Store -->
        <Valve className="com.gopivotal.manager.SessionFlushValve" />
        <Manager className="org.apache.catalina.session.PersistentManager">
            <Store className="com.gopivotal.manager.redis.RedisStore"
                   connectionPoolSize="20"
                   host="${REDIS_CACHE_NAME}.redis.cache.windows.net"
                   port="${REDIS_PORT}"
                   password="${REDIS_PASSWORD}"
                   sessionKeyPrefix="${REDIS_SESSION_KEY_PREFIX}"
                   timeout="2000"
            />
        </Manager>
    </Context>
    ```

    Den här filen anger och konfigurerar implementeringen av sessionshanteraren för din app. Den använder de miljövariabler som du anger i föregående steg för att hålla din kontoinformation utanför källfilerna.

3. Använd FTP för att ladda upp sessionshanterarens JAR-fil till apptjänstinstansen och placera den i katalogen */home/tomcat/lib.* Mer information finns i [Distribuera din app till Azure App Service med FTP/S](https://docs.microsoft.com/azure/app-service/deploy-ftp).

4. Inaktivera [sessionstillhörighetscookien](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) för din App Service-instans. Du kan göra detta från Azure-portalen genom att navigera till din app och sedan ställa in **konfiguration > allmänna inställningar > ARR-tillhörighet** till **Av**. Alternativt kan du använda följande kommando:

    ```azurecli
    az webapp update -g <resource group> -n <webapp name> --client-affinity-enabled false
    ```

    Som standard använder App Service sessionstillhörighetscookies för att säkerställa att klientbegäranden med befintliga sessioner dirigeras till samma instans av ditt program. Det här standardbeteendet kräver ingen konfiguration, men det går inte att bevara användarsessionstillståndet när appinstansen startas om eller när trafiken omdirigeras till en annan instans. När du [inaktiverar den befintliga ARR-instanstillhörighetskonfigurationen](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) för att stänga av den cookie-baserade routningen för session tillåter du att det konfigurerade sessionsarkivet fungerar utan störningar.

5. Navigera till avsnittet **Egenskaper** i apptjänstinstansen och ta reda på **ytterligare utgående IP-adresser**. Dessa representerar alla möjliga utgående IP-adresser för din app. Kopiera dessa för användning i nästa steg.

6. Skapa en brandväggsregel i Azure Cache för Redis-instans för varje IP-adress. Du kan göra detta på Azure-portalen från **brandväggen** i din Redis-instans. Ange ett unikt namn för varje regel och ange **värdena start-IP-adress** och **Avsluta IP-adress** till samma IP-adress.

7. Navigera till avsnittet **Avancerade inställningar** i Redis-instansen och ange Tillåt åtkomst endast **via SSL** till **Nej**. Detta gör att din App Service-instans kan kommunicera med din Redis-cache via Azure-infrastrukturen.

8. Uppdatera `azure-webapp-maven-plugin` konfigurationen i appens *pom.xml-fil* för att referera till din Redis-kontoinformation. Den här filen använder de miljövariabler som du angav tidigare för att hålla kontoinformationen borta från källfilerna.

    Vid behov ändrar du `1.7.0` till den aktuella versionen av [Maven-pluginet för Azure App Service](/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme).

    ```xml
    <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.7.0</version>
        <configuration>

            <!-- Web App information -->
            <resourceGroup>${RESOURCEGROUP_NAME}</resourceGroup>
            <appServicePlanName>${WEBAPP_PLAN_NAME}-${REGION}</appServicePlanName>
            <appName>${WEBAPP_NAME}-${REGION}</appName>
            <region>${REGION}</region>
            <linuxRuntime>tomcat 9.0-jre8</linuxRuntime>

            <appSettings>
                <property>
                    <name>REDIS_CACHE_NAME</name>
                    <value>${REDIS_CACHE_NAME}</value>
                </property>
                <property>
                    <name>REDIS_PORT</name>
                    <value>${REDIS_PORT}</value>
                </property>
                <property>
                    <name>REDIS_PASSWORD</name>
                    <value>${REDIS_PASSWORD}</value>
                </property>
                <property>
                    <name>REDIS_SESSION_KEY_PREFIX</name>
                    <value>${REDIS_SESSION_KEY_PREFIX}</value>
                </property>
                <property>
                    <name>JAVA_OPTS</name>
                    <value>-Xms2048m -Xmx2048m -DREDIS_CACHE_NAME=${REDIS_CACHE_NAME} -DREDIS_PORT=${REDIS_PORT} -DREDIS_PASSWORD=${REDIS_PASSWORD} IS_SESSION_KEY_PREFIX=${REDIS_SESSION_KEY_PREFIX}</value>
                </property>

            </appSettings>

        </configuration>
    </plugin>
    ```

9. Återskapa och distribuera om appen.

    ```bash
    mvn package -DskipTests azure-webapp:deploy
    ```

Din app använder nu Redis-cachen för sessionshantering.

Ett exempel som du kan använda för att testa dessa instruktioner finns i [den skalning-stateful-java-web-app-on-azure-repoen](https://github.com/Azure-Samples/scaling-stateful-java-web-app-on-azure) på GitHub.

## <a name="docker-containers"></a>Docker-containrar

Om du vill använda Zulu JDK som stöds av Azure i dina behållare måste du hämta och använda de färdiga avbildningarna som dokumenteras från [nedladdningssidan Azul Zulu Enterprise för Azure](https://www.azul.com/downloads/azure-only/zulu/) eller använda `Dockerfile` exemplen från Microsoft Java [GitHub-repoen](https://github.com/Microsoft/java/tree/master/docker).

## <a name="statement-of-support"></a>Uttalande om stöd

### <a name="runtime-availability"></a>Tillgänglighet för körning

App Service för Linux stöder två runtimes för hanterad hosting av Java webbapplikationer:

- [Tomcat servlet-behållaren](https://tomcat.apache.org/) för att köra program paketerade som KRIGS-filer (Web Archive). Versioner som stöds är 8.5 och 9.0.
- Java SE runtime miljö för att köra program paketerade som Java arkiv (JAR) filer. Versioner som stöds är Java 8 och 11.

### <a name="jdk-versions-and-maintenance"></a>JDK-versioner och underhåll

Azul Zulu Enterprise-versioner av OpenJDK är en kostnadsfri, produktionsklar distribution av OpenJDK för Azure och Azure Stack som stöds av Microsoft och Azul Systems. De innehåller alla komponenter för att skapa och köra Java SE-program. Du kan installera JDK från [Java JDK Installation](https://aka.ms/azure-jdks).

Gemensamma tillsynspunkter som stöds korrigeras automatiskt kvartalsvis i januari, april, juli och oktober varje år.

### <a name="security-updates"></a>Säkerhetsuppdateringar

Patchar och korrigeringar för större säkerhetsproblem kommer att släppas så snart de blir tillgängliga från Azul Systems. Ett "större" säkerhetsproblem definieras av en baspoäng på 9,0 eller högre på [NIST Common Vulnerability Scoring System, version 2](https://nvd.nist.gov/cvss.cfm).

### <a name="deprecation-and-retirement"></a>Utfasning och pensionering

Om en Java-körning som stöds kommer att dras tillbaka, kommer Azure-utvecklare som använder den berörda körningen att få ett meddelande om utfasning minst sex månader innan körningen dras tillbaka.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Nästa steg

Besök [Azure for Java Developers](/java/azure/) center för att hitta Azure-snabbstarter, självstudier och Java-referensdokumentation.

Allmänna frågor om hur du använder App Service för Linux som inte är specifika för Java-utvecklingen besvaras i [vanliga frågor om App Service Linux](app-service-linux-faq.md).
