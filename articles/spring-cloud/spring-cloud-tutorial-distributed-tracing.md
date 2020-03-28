---
title: Självstudiekurs - Använd distribuerad spårning med Azure Spring Cloud
description: Den här självstudien visar hur du använder Spring Cloud distributed tracing via Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: brendm
ms.openlocfilehash: 0815aa084462d1b829d64cd7c5d6fa7cebf534fc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78273200"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Använda distribuerad spårning med Azure Spring Cloud

Med de distribuerade spårningsverktygen i Azure Spring Cloud kan du enkelt felsöka och övervaka komplexa problem. Azure Spring Cloud integrerar [Spring Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med Azures [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Den här integreringen ger kraftfulla distribuerade spårningsfunktioner från Azure-portalen.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Aktivera distribuerad spårning i Azure-portalen.
> * Lägg till Spring Cloud Sleuth i ditt program.
> * Visa beroendekartor för dina mikrotjänstprogram.
> * Sök spårningsdata med olika filter.

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du en Azure Spring Cloud-tjänst som redan har etablerats och körs. Slutför [snabbstarten när du distribuerar en app via Azure CLI](spring-cloud-quickstart-launch-app-cli.md) för att etablera och köra en Azure Spring Cloud-tjänst.
    
## <a name="add-dependencies"></a>Lägga till beroenden

1. Lägg till följande rad i filen application.properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Efter den här ändringen kan Zipkin-avsändaren skicka till webben.

1. Hoppa över det här steget om du har följt vår [guide till att förbereda ett Azure Spring Cloud-program](spring-cloud-tutorial-prepare-app-deployment.md). Annars går du till din lokala utvecklingsmiljö och redigerar filen pom.xml så att den innehåller följande Vårtmoln-sleuth-beroende:

    ```xml
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-sleuth</artifactId>
                <version>${spring-cloud-sleuth.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-sleuth</artifactId>
        </dependency>
    </dependencies>
    ```

1. Skapa och distribuera igen för din Azure Spring Cloud-tjänst för att återspegla dessa ändringar.

## <a name="modify-the-sample-rate"></a>Ändra samplingsfrekvensen

Du kan ändra hastigheten med vilken telemetri samlas in genom att ändra samplingsfrekvensen. Om du till exempel vill ta exempel på hälften så ofta öppnar du filen application.properties och ändrar följande rad:

```xml
spring.sleuth.sampler.probability=0.5
```

Om du redan har skapat och distribuerat ett program kan du ändra exempelfrekvensen. Gör det genom att lägga till föregående rad som en miljövariabel i Azure CLI eller Azure-portalen.

## <a name="enable-application-insights"></a>Aktivera Application Insights

1. Gå till din Azure Spring Cloud-tjänstsida i Azure-portalen.
1. På sidan **Övervakning** väljer du **Distribuerad spårning**.
1. Välj **Redigera inställning** om du vill redigera eller lägga till en ny inställning.
1. Skapa en ny application insights-fråga eller välj en befintlig.
1. Välj vilken loggningskategori du vill övervaka och ange kvarhållningstiden i dagar.
1. Välj **Använd** om du vill använda den nya spårningen.

## <a name="view-the-application-map"></a>Visa programkartan

Gå tillbaka till sidan **Distribuerad spårning** och välj **Visa programkarta**. Granska den visuella representationen av dina program- och övervakningsinställningar. Mer information om hur du använder programkartan finns i [Programöversikt: Triage distribuerade program](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Använd sökning

Använd sökfunktionen för att fråga efter andra specifika telemetriobjekt. Välj **Sök**på sidan **Distribuerad spårning** . Mer information om hur du använder sökfunktionen finns [i Använda sökfunktionen i Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Använda programinsikter

Application Insights tillhandahåller övervakningsfunktioner utöver programkartan och sökfunktionen. Sök i Azure-portalen efter programmets namn och öppna sedan en sida för programinsikter för att hitta övervakningsinformation. Mer information om hur du använder dessa verktyg finns i [Azure Monitor-loggfrågor](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Inaktivera programinsikter

1. Gå till din Azure Spring Cloud-tjänstsida i Azure-portalen.
1. Välj **Distribuerad spårning**på **Övervakning**.
1. Välj **Inaktivera** om du vill inaktivera programstatistik.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du aktiverar och förstår distribuerad spårning i Azure Spring Cloud. Om du vill veta hur du binder ditt program till en Azure Cosmos DB-databas fortsätter du till nästa självstudiekurs.

> [!div class="nextstepaction"]
> [Lär dig hur du binder till en Azure Cosmos DB-databas](spring-cloud-tutorial-bind-cosmos.md)
