---
title: Skapa en Java Enterprise-webbapp i Linux – Azure App Service | Microsoft Docs
description: Lär dig hur du får en Java Enterprise-app att fungera i Wildfly på Azure App Service i Linux.
author: JasonFreeberg
manager: routlaw
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: tutorial
ms.date: 11/13/2018
ms.author: jafreebe
ms.custom: seodec18
ms.openlocfilehash: dcd1ef5c54885b758ac9a301616d79a163999bc9
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509634"
---
# <a name="tutorial-build-a-java-ee-and-postgres-web-app-in-azure"></a>Självstudie: Skapa en Java EE- och Postgres-webbapp i Azure

Den här självstudien visar hur du skapar en webbapp i Java Enterprise Edition (EE) på Azure App Service och ansluter den till en Postgres-databas. När du är klar har du en [WildFly](https://www.wildfly.org/about/)-app som lagrar data i [Azure Database for Postgres](https://azure.microsoft.com/services/postgresql/) och körs på Azure [App Service på Linux](app-service-linux-intro.md).

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Distribuera en Java EE-app till Azure med Maven
> * Skapa en Postgres-databas i Azure
> * Konfigurera WildFly-servern om du vill använda Postgres
> * Uppdatera och distribuera om appen
> * Köra enhetstester på WildFly

## <a name="prerequisites"></a>Förutsättningar

1. [Ladda ned och installera Git](https://git-scm.com/)
2. [Ladda ned och installera Maven 3](https://maven.apache.org/install.html)
3. [Ladda ned och installera Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)

## <a name="clone-and-edit-the-sample-app"></a>Klona och redigera exempelappen

I det här steget ska du klona exempelprogrammet och konfigurera Maven-projekt Object Model (POM eller *pom.xml*) för distribution.

### <a name="clone-the-sample"></a>Klona exemplet

Gå till terminalfönstret, navigera till en arbetskatalog och klona [exempellagringsplatsen](https://github.com/Azure-Samples/wildfly-petstore-quickstart).

```bash
git clone https://github.com/Azure-Samples/wildfly-petstore-quickstart.git
```

### <a name="update-the-maven-pom"></a>Uppdatera Maven POM

Uppdatera Maven-plugin-programmet för Azure med önskat namn och resursgrupp i din App Service. Du behöver inte skapa App Service-planen eller -instansen i förväg. Maven-pluginprogrammet skapar resursgrupp och App Service om det inte redan finns.

Du kan rulla ned till `<plugins>`-avsnittet i *pom.xml* rad 200, för att göra ändringarna.

```xml
<!-- Azure App Service Maven plugin for deployment -->
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>${version.maven.azure.plugin}</version>
  <configuration>
    <appName>YOUR_APP_NAME</appName>
    <resourceGroup>YOUR_RESOURCE_GROUP</resourceGroup>
    <linuxRuntime>wildfly 14-jre8</linuxRuntime>
  ...
</plugin>  
```

Ersätt `YOUR_APP_NAME` och `YOUR_RESOURCE_GROUP` med namnen på din App Service och resursgruppen.

## <a name="build-and-deploy-the-application"></a>Skapa och distribuera programmet

Vi kommer nu använda Maven för att skapa vårt program och distribuera det till App Service.

### <a name="build-the-war-file"></a>Skapa .war-filen

POM i det här projektet är konfigurerad för att paketera programmet till en WAR-fil (Web Archive). Skapa programmet med hjälp av Maven:

```bash
mvn clean install -DskipTests
```

Testfallen i det här programmet är avsedda att köras när programmet har distribuerats till WildFly. Vi hoppar över testerna för att skapa lokalt och du kör testerna när programmet distribueras till App Service.

### <a name="deploy-to-app-service"></a>Distribuera till App Service

Nu när WAR-filen är klar kan vi använda Azure-plugin-programmet för att distribuera till App Service:

```bash
mvn azure-webapp:deploy
```

När distributionen är klar kan du gå vidare till nästa steg.

### <a name="create-a-record"></a>Skapa en post

Öppna en webbläsare och navigera till `https://<your_app_name>.azurewebsites.net/`. Grattis, du har distribuerat ditt Java EE-program till Azure App Service!

Programmet använder nu en minnesintern H2-databas. Klicka på ”admin” i navigeringsfältet och skapa en ny kategori. Posten i din minnesinterna databas går förlorad om du startar om din App Service-instans. I följande steg ska du åtgärda det genom att etablera en Postgres-databas på Azure och konfigurera WildFly för att använda den.

![Administratörsknappens placering](media/tutorial-java-enterprise-postgresql-app/admin_button.JPG)

## <a name="provision-a-postgres-database"></a>Etablera en Postgres-databas

Öppna en terminal för att etablera en Postgres-databasserver, och Använd den [az postgres server skapa](https://docs.microsoft.com/cli/azure/postgres/server) kommandot, som visas i följande exempel. Ersätt platshållarna (inklusive hakparenteser) med värden som du väljer, med hjälp av samma resurs gruppen som du angav tidigare för din App Service-instans. De autentiseringsuppgifter du anger att ge framtida åtkomst, så var noga med att hålla ned dem för senare användning.

```bash
az postgres server create \
    --name <server name> \
    --resource-group <resource group> \
    --location <location>
    --sku-name GP_Gen5_2 \
    --admin-user <administrator username> \
    --admin-password <administrator password> \
```

När du har kört det här kommandot Bläddra till Azure-portalen och gå till Postgres-databasen. När bladet är igång kopierar du värdena för ”Servernamn” och ”Inloggningsnamn för serveradministratör”, för du behöver dem senare.

### <a name="allow-access-to-azure-services"></a>Tillåt åtkomst till Azure-tjänster

I panelen **Anslutningssäkerhet** på bladet Azure Database ändrar du knappen ”Tillåt åtkomst till Azure-tjänster” till läget **PÅ**.

![Tillåta åtkomst till Azure-tjänster](media/tutorial-java-enterprise-postgresql-app/postgress_button.JPG)

## <a name="update-your-java-app-for-postgres"></a>Uppdatera din Java-app för Postgres

Vi kommer nu att vissa ändringar i Java-programmet för att kunna använda vår Postgres-databas.

### <a name="add-postgres-credentials-to-the-pom"></a>Lägga till Postgres-autentiseringsuppgifter i POM

I *pom.xml* ersätter du de versala platshållarvärdena med Postgres-servernamnet, administratörens inloggningsnamn och lösenord. Dessa fält är i Maven-plugin-programmet för Azure. (Se till att ersätta `YOUR_SERVER_NAME`, `YOUR_PG_USERNAME`, och `YOUR_PG_PASSWORD` i taggarna `<value>` ... inte inom `<name>`-taggarna!)

```xml
<plugin>
      ...
      <appSettings>
      <property>
        <name>POSTGRES_CONNECTIONURL</name>
        <value>jdbc:postgresql://YOUR_SERVER_NAME:5432/postgres?ssl=true</value>
      </property>
      <property>
        <name>POSTGRES_USERNAME</name>
        <value>YOUR_PG_USERNAME</value>
      </property>
      <property>
        <name>POSTGRES_PASSWORD</name>
        <value>YOUR_PG_PASSWORD</value>
      </property>
    </appSettings>
  </configuration>
</plugin>
```

### <a name="update-the-java-transaction-api"></a>Uppdatera Java Transaction API

Därefter behöver vi redigera vår API för JPA-konfiguration för Java-transaktion (Java Transaction API) så att våra Java-program ska kommunicera med Postgres i stället för InMemory-H2-databasen som vi använde tidigare. Öppna ett redigeringsprogram till *src/main/resources/META-INF/persistence.xml*. Ersätt värdet för `<jta-data-source>` med `java:jboss/datasources/postgresDS`. Din JTA XML bör nu ha den här inställningen:

```xml
<jta-data-source>java:jboss/datasources/postgresDS</jta-data-source>
```

## <a name="configure-the-wildfly-application-server"></a>Konfigurera programservern WildFly

Innan du distribuerar vårt omkonfigurerade program måste vi uppdatera programservern WildFly med Postgres-modulen och dess beroenden. Mer konfigurationsinformation finns på [konfigurera WildFly server](configure-language-java.md#configure-java-ee-wildfly).

För att konfigurera servern, måste vi fyra filerna i den *wildfly_config /* directory:

- **postgresql-42.2.5.jar**: Den här JAR-filen är JDBC-drivrutinen för Postgres. Mer information finns på den [officiella webbplatsen](https://jdbc.postgresql.org/index.html).
- **postgres-module.xml**: Den här XML-filen deklarerar ett namn för Postgres-modulen (org.postgres). Den anger också de resurser och beroenden som krävs för att modulen ska användas.
- **jboss_cli_commands.cl**: Den här filen innehåller konfigurationskommandon som körs av JBoss CLI. Kommandona lägger till Postgres-modulen till WildFly-programservern, anger autentiseringsuppgifterna, deklarerar ett JNDI-namn, anger tröskelvärde för timeout osv. Om du inte känner till JBoss CLI finns i den [officiella dokumentationen](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
- **startup_script.sh**: Slutligen körs detta gränssnittsskript när din App Service-instans startas. Skriptet utför endast en funktion: skicka kommandon i *jboss_cli_commands.cli* till JBoss CLI.

Vi rekommenderar starkt att du läser innehållet i filerna, särskilt *jboss_cli_commands.cli*.

### <a name="ftp-the-configuration-files"></a>FTP-konfigurationsfiler

Vi kommer att behöva FTP-innehållet i *wildfly_config /* till vår App Service-instans. För att få dina FTP-autentiseringsuppgifter klickar du på knappen **Hämta publiceringsprofil** på App Service-bladet i Azure-portalen. FTP-användarnamnet och -lösenordet finns i det nedladdade XML-dokumentet. Mer information om publiceringsprofilen finns i [det här dokumentet](https://docs.microsoft.com/azure/app-service/deploy-configure-credentials).

Med hjälp av en FTP-verktyg som helst, överföra fyra filer i *wildfly_config /* till */home/site/distribution/tools/* . (Observera att du inte överför katalogen, bara själva filerna.)

### <a name="finalize-app-service"></a>Slutföra App Service

Gå till panelen ”Programinställningar” på App Service-bladet. Under ”körning”, ange fältet ”startfil” till */home/site/deployments/tools/startup_script.sh*. Det säkerställer att kommandoskriptet körs efter att App Service-instansen har skapats, men innan WildFly-servern startar.

Slutligen startar du om din App Service. Knappen finns i panelen ”Översikt”.

## <a name="redeploy-the-application"></a>Distribuera om programmet

Återskapa och distribuera om ditt program i ett terminalfönster.

```bash
mvn clean install -DskipTests azure-webapp:deploy
```

Grattis! Programmet använder nu en Postgres-databas och alla poster som skapats i programmet kommer att lagras i Postgres i stället för den föregående H3 InMemory-databasen. För att bekräfta detta kan du göra en post och starta om App Service. Posterna kommer dock att finnas kvar när programmet startas om.

## <a name="clean-up"></a>Rensa

Om du inte behöver de här resurserna för en annan självstudie (se Nästa steg) kan du ta bort dem genom att köra följande kommando:

```bash
az group delete --name <your-resource-group>
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Distribuera en Java EE-app till Azure med Maven
> * Skapa en Postgres-databas i Azure
> * Konfigurera WildFly-servern om du vill använda Postgres
> * Uppdatera och distribuera om appen
> * Köra enhetstester på WildFly

Gå vidare till nästa självstudie för att läsa hur du mappar ett anpassat DNS-namn till din app.

> [!div class="nextstepaction"]
> [Självstudie: Mappa anpassad DNS-namn till din app](../app-service-web-tutorial-custom-domain.md)

Eller titta på andra resurser:

> [!div class="nextstepaction"]
> [Konfigurera Java-app](configure-language-java.md)
