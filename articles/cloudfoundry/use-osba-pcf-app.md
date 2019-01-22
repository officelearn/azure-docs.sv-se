---
title: Använd en Open Service Broker for Azure-databasen med ett program i Pivotal Cloud Foundry
author: zr-msft
manager: jeconnoc
services: azure-dev-spaces
ms.service: azure-dev-spaces
ms.author: zarhoads
ms.date: 01/11/2019
ms.topic: tutorial
description: Beskriver hur du konfigurerar ett Pivotal Cloud Foundry-program för att använda en Open Service Broker for Azure-databas
keywords: Pivotal Cloud Foundry, Cloud Foundry, Open Service Broker, Open Service Broker for Azure
tags: Cloud-Foundry
ms.openlocfilehash: d553cd09ef42e47e3a10fb96039063b8aae665cb
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2019
ms.locfileid: "54258894"
---
# <a name="tutorial-use-an-open-service-broker-for-azure-database-with-an-application-on-pivotal-cloud-foundry"></a>Självstudier: Använd en Open Service Broker for Azure-databasen med ett program i Pivotal Cloud Foundry

Med hjälp av Open Service Broker for Azure med en Pivotal Cloud Foundry-instans kan du etablera tjänster, till exempel databaser, i Azure direkt från Cloud Foundry CLI och en Pivotal Cloud Foundry-instans. Du kan även binda dessa tjänster till ett program som körs i Pivotal Cloud Foundry-instansen. När du binder ett program till en tjänst på det här sättet behöver du inte uppdatera någon kod eller konfiguration i ditt program. Den här artikeln förklarar hur du använder en Open Service Broker for Azure-tjänst för en databas med ett program i Pivotal Cloud Foundry.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förbereda ditt programområde i Pivotal Cloud Foundry.
> * Klona en exempelprogramkälla från GitHub.
> * Förbereda programmet för distribution.
> * Distribuera programmet.
> * Skapa en databas med hjälp av Open Service Broker for Azure.
> * Binda databasen till ditt program.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du kan fortsätta måste du:

* [Ha Pivotal Cloud Foundry installerat och konfigurerat](create-cloud-foundry-on-azure.md)
* [Ha Open Service Broker for Azure installerat och konfigurerat](https://network.pivotal.io/products/azure-open-service-broker-pcf) med Cloud din Foundry-instans

Här är ett exempel på Pivotal Cloud Foundry Ops Manager-skärmen med Open Service Broker for Azure-panelen installerad och konfigurerad:

![Pivotal Cloud Foundry med Open Service Broker for Azure installerat](media/pcf-ops-manager.png)

## <a name="prepare-your-application-space-in-pivotal-cloud-foundry"></a>Förbereda ditt programområde i Pivotal Cloud Foundry

För att kunna distribuera programmet till din Pivotal Cloud Foundry-instans behöver du vara inloggad med `cf`-kommandoradsverktyget. Du måste även ha önskad organisation och önskat utrymme som mål.

Kontrollera att du har loggat in och visa det utrymme som du har som mål med hjälp av `cf target`. I exemplet nedan visas att användaren redan är inloggad som *administratör*med hjälp av organisationen *myorg* och har utrymmet *dev* som mål:

```cmd
$ cf target

api endpoint:   https://api.system.40.85.111.222.cf.pcfazure.com
api version:    2.120.0
user:           admin
org:            myorg
space:          dev
```

Logga in med `cf login`:

```cmd
cf login -a https://api.SYSTEMDOMAINURL --skip-ssl-validation
```

Du kan skapa en ny organisation och ett nytt utrymme med hjälp av `cf create-org` och `cf create-space`:

```cmd 
cf create-org myorg
cf create-space dev -o myorg
```

Välj ett utrymme som mål med hjälp av `cf target`:

```cmd
cf target -o myorg -s dev
```

## <a name="get-application-code"></a>Hämta programkod

För att programmet ska kunna använda Open Service Broker for Azure behöver programmet använda en datakälla, till exempel en databas. I den här artikeln använder vi [Spring-exempelprogrammet för musik från Cloud Foundry](https://github.com/cloudfoundry-samples/spring-music) för att visa ett program som använder en datakälla.

Klona programmet från GitHub och gå till programmets katalog:

```cmd
git clone https://github.com/cloudfoundry-samples/spring-music
cd spring music
```

> [!TIP]
> Du visar datakällorna för det här programmet genom att öppna filen [src/main/resources/application.yml](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/application.yml).


## <a name="prepare-the-application-for-deployment"></a>Förbereda programmet för distribution

Innan du kan distribuera programmet till din Pivotal Cloud Foundry-instans behöver du skapa det. I demonstrativt syfte aktiverar vi även viss *DEBUG*-loggning (felsökning) som loggar information om datakällans anslutning.

Du aktiverar *DEBUG*-loggning för information om datakällans anslutning genom att lägga till nedanstående yaml-egenskap i slutet av *application.yml*:

```yaml
---
logging:
  level:
    com.zaxxer.hikari: DEBUG
```

Exempelprogrammet använder gradle för att skapa programmet i en körbar Spring Boot-jar. Den körbara jar-filen distribueras till din Pivotal Cloud Foundry-instans. Så här skapa du programmet:

```cmd
./gradlew clean assemble

Starting a Gradle Daemon (subsequent builds will be faster)

BUILD SUCCESSFUL in 10s
4 actionable tasks: 4 executed
```


## <a name="deploy-your-application"></a>Distribuera appen

Använd kommandot `cf push` för att distribuera programmet till din Pivotal Cloud Foundry-instans:

```cmd
cf push

Pushing from manifest to org myorg / space dev as admin...
Using manifest file /path/to/spring-music/manifest.yml
Getting app info...
Creating app with these attributes...
+ name:       spring-music
...
Waiting for app to start...

name:              spring-music
requested state:   started
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
     state     since                  cpu    memory         disk           details
#0   running   2018-12-06T21:24:06Z   0.0%   313.3M of 1G   170.7M of 1G
```

Kopiera det värde från *vägar* som visas i utdata från `cf push`. Flödet är den URL som du använder för att få åtkomst till det program som körs. Exempel:

```cmd
...
routes:            spring-music-wacky-oribi.app.40.85.111.222.cf.pcfazure.com
...
```


När programmet har distribuerats kan du visa programmets loggar om du vill se den anslutningen-URL som används av programmet. Kommandot nedan visar programmets loggar och använder `grep` för att söka efter konfigurationen *jdbcUrl*.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
```

Observera att programmet använder *h2:mem:testdb*, vilket är den minnesintern databasen. Ett Spring-program konfigureras automatiskt att använda en minnesintern databas när det finns ett beroende för minnesintern databas i klassökvägen och [automatisk konfiguration](https://docs.spring.io/spring-boot/docs/current/reference/html/using-boot-auto-configuration.html) är aktiverad. Exempelprogrammet har [beroende för den minnesinterna h2-databasen konfigurerat](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L49) och automatisk konfiguration aktiverat i [src/main/java/org/cloudfoundry/samples/music/Application.java](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/java/org/cloudfoundry/samples/music/Application.java#L8).

Använd programmets väg för att navigera till det i en webbläsare. Vägen, eller URL:en, visas i utdata från kommandot `cf push`.

> [!TIP]
> Du kan även visa programmets URL genom att köra `cf apps`.

När du går till programmet via webbläsaren interagerar du med det genom att ta bort några befintliga album och skapa några nya. Exempelprogrammet använder den minnesinterna databasen för att spara dina ändringar. Du kommer även se att programmet har fyllts med några [standarddata](https://github.com/cloudfoundry-samples/spring-music/blob/master/src/main/resources/albums.json). 

![Spring-musikappen med standarddata](media/music-app.png)

Eftersom programmet använder en minnesintern databas bevaras inte dina ändringar om programmet startas om eller distribueras om. För att visa att ändringarna inte bevaras bygger du om programmet med hjälp av `cf restage` efter att du har gjort några ändringar:

```cmd
cf restage spring-music
```

När programmet har byggts om navigerar du till det i en webbläsare med hjälp av samma URL. Observera att de ändringar du gjorde är borta och att standarddata visas.

![Spring-musikappen med standarddata](media/music-app.png)

## <a name="create-a-database"></a>Skapa en databas

Skapa en beständig databas på Azure med hjälp av Open Service Broker med kommandot `cf create-service`. Nedanstående kommando etablerar en PostgreSQL-databas i Azure i resursgruppen *MyResourceGroup* i regionen *eastus*. Mer information om *resourceGroup*, *location* (plats) och andra Azure-specifika JSON-parametrar finns i [referensdokumentationen för PostgreSQL-modul](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md#provision):

```cmd
cf create-service azure-postgresql-9-6 general-purpose mypgsql -c '{"resourceGroup":"MyResourceGroup", "location":"eastus"}'
```

Databasen visas i din Pivotal Cloud Foundry-instans som en tjänst med namnet *mypgsql*. När databasen är klar med etableringen, vilket bör ta några minuter, kan du binda den till ditt program. Kontrollera att databasen har slutfört etableringen med hjälp av kommandot `cf services`:

```cmd
cf services

Getting services in org myorg / space dev as admin...

name      service                plan              bound apps     last operation
mypgsql   azure-postgresql-9-6   general-purpose                  create succeeded
```

Värdet för den *last operation* (senaste åtgärden) för din tjänst ska vara *create succeeded* (skapandes lyckades) när den har slutfört etableringen.

## <a name="bind-the-database-to-your-application"></a>Binda databasen till ditt program

Använd kommando `bind-service` för att binda tjänsten till ditt program.

```cmd
cf bind-service spring-music mypgsql
```

När du har bundit tjänsten till ditt program behöver du bygga om programmet för att ändringarna ska börja gälla.

```cmd
cf restage spring-music
```

Programmet är [konfigurerat för att använda Spring Cloud-anslutningsappar](https://github.com/cloudfoundry-samples/spring-music/blob/master/build.gradle#L45...L46), vilket gör att din Pivotal Cloud Foundry-instans kan använda [automatisk omkonfiguration](https://docs.cloudfoundry.org/buildpacks/java/configuring-service-connections/spring-service-bindings.html#auto) på programmets datakälla. I det här fallet konfigurerar din Pivotal Cloud Foundry-instans automatiskt programmet att använda en tjänst som det är bundet till för en datakälla när programmet byggs om.

När programmet har byggts om använder det *mypgsql* för att lagra data i stället för den minnesinterna databasen.

Eventuella ändringar som du gör bevaras nu mellan omstarter och omdistributioner. Du kan även se den anslutnings-URL som programmet använder genom att visa programmets loggar igen.

```cmd
cf logs spring-music --recent | grep jdbcUrl

2018-12-07T14:44:30.57-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:44:30.574 DEBUG 24 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
2018-12-07T14:48:58.10-0600 [APP/PROC/WEB/0] OUT 2018-12-07 20:48:58.107 DEBUG 16 --- [           main] com.zaxxer.hikari.HikariConfig           : jdbcUrl.........................jdbc:postgresql://12345678-aaaa-bbbb-cccc-1234567890ab.postgres.database.azure.com:5432/123456789?user=aabbcc1122@12345678-aaaa-bbbb-cccc-1234567890ab&password=<masked>&&sslmode=require
```

Observera att det finns två poster:

* Det ursprungliga värdet för *h2:mem:testdb*.
* Det uppdaterade värdet för PostgreSQL-databasen när ditt program byggdes om.

För att verifiera att data sparas till PostgreSQL-databasen går du till programmet i webbläsaren, gör några ändringar och bygger sedan om programmet:

```cmd
cf restage spring-music
```

När programmet har byggts om navigerar du till det i en webbläsare med hjälp av samma URL. Observera att de ändringar du gjorde finns kvar.

## <a name="cleanup"></a>Rensa

Om du vill koppla bort programmet från databasen kan du ta bort bindningen för det med hjälp av kommandot `cf unbind-service`.

```cmd
cf unbind-service spring-music mypgsql
```

På ett liknande sätt som för bindning av programmet till en tjänst måste du bygga om programmet för att de här ändringarna ska börja gälla. Den här åtgärden lämnar både *mypgsql* och programmet orörda, men programmet börjar använda den minnesinterna databasen i stället för *mypgsql*.

Du kan använda kommandot `cf delete-service` för att ta bort databasen. *Du kan inte ångra den här åtgärden, så var säker på att du vill ta bort databasen innan du fortsätter.*

```cmd
cf delete-service mypgsql
```

Så här tar du bort programmet från Pivotal Cloud Foundry-instansen:
 
```cmd
cf delete spring-music
```

## <a name="next-steps"></a>Nästa steg

Den här självstudien gick igenom hur du distribuerar ett program till Pivotal Cloud Foundry samt hur du skapar en databas med hjälp av Open Service Broker for Azure. Den beskrev även hur du binder databasen till programmet i din Pivotal Cloud Foundry-instans. Mer information om hur du distribuerar program till Cloud Foundry på Azure finns i:

* [Cloud Foundry på Azure](../virtual-machines/linux/cloudfoundry-get-started.md)
* [Distribuera din första app till Cloud Foundry på Microsoft Azure](../virtual-machines/linux/cloudfoundry-deploy-your-first-app.md)