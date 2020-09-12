---
title: Använd distribuerad spårning med Azure våren Cloud
description: Lär dig att använda våren Cloud Distributed tracing via Azure Application Insights
author: bmitchell287
ms.service: spring-cloud
ms.topic: how-to
ms.date: 10/06/2019
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 1ff76c38031ac367bf81f6d152642a4d9a209bb7
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/02/2020
ms.locfileid: "89294007"
---
# <a name="use-distributed-tracing-with-azure-spring-cloud"></a>Använd distribuerad spårning med Azure våren Cloud

Med de distribuerade spårnings verktygen i Azure våren-molnet kan du enkelt felsöka och övervaka komplexa problem. Azure våren Cloud integrerar [våren Cloud Sleuth](https://spring.io/projects/spring-cloud-sleuth) med azures [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview). Den här integrationen ger kraftfull, distribuerad spårnings funktion från Azure Portal.

I den här artikeln lär du dig hur du:

> [!div class="checklist"]
> * Aktivera distribuerad spårning i Azure Portal.
> * Lägg till vår moln Sleuth i ditt program.
> * Visa beroende kartor för mikrotjänst program.
> * Sök spårnings data med olika filter.

## <a name="prerequisites"></a>Krav

Om du vill följa de här procedurerna behöver du en moln tjänst för Azure våren som redan är etablerad och körs. Slutför [snabb starten när du distribuerar en app via Azure CLI](spring-cloud-quickstart.md) för att etablera och köra en moln tjänst för Azure våren.
    
## <a name="add-dependencies"></a>Lägg till beroenden

1. Lägg till följande rad i filen Application. Properties:

   ```xml
   spring.zipkin.sender.type = web
   ```

   Efter den här ändringen kan Zipkin-avsändaren skicka till webben.

1. Hoppa över det här steget om du följde vår [Guide för att förbereda ett Azure våren Cloud-program](spring-cloud-tutorial-prepare-app-deployment.md). Annars går du till din lokala utvecklings miljö och redigerar pom.xml-filen så att den inkluderar följande Sleuth-beroende för fjäder molnet:

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

1. Bygg och distribuera igen för din Azure våren Cloud-tjänst för att avspegla dessa ändringar.

## <a name="modify-the-sample-rate"></a>Ändra samplings frekvensen

Du kan ändra den hastighet som din telemetri samlas in genom att ändra samplings frekvensen. Om du t. ex. vill använda hälften så ofta öppnar du filen program. Properties och ändrar följande rad:

```xml
spring.sleuth.sampler.probability=0.5
```

Om du redan har skapat och distribuerat ett program kan du ändra samplings frekvensen. Gör detta genom att lägga till föregående rad som en miljö variabel i Azure CLI eller Azure Portal.

## <a name="enable-application-insights"></a>Aktivera Application Insights

1. Gå till sidan Azure våren Cloud service i Azure Portal.
1. På sidan **övervakning** väljer du **distribuerad spårning**.
1. Välj **Redigera inställning** för att redigera eller lägga till en ny inställning.
1. Skapa en ny Application Insights fråga eller Välj en befintlig.
1. Välj vilken loggnings kategori som du vill övervaka och ange Retentions tiden i dagar.
1. Välj **tillämpa** för att tillämpa den nya spårningen.

## <a name="view-the-application-map"></a>Visa program kartan

Gå tillbaka till sidan **distribuerad spårning** och välj **Visa program karta**. Granska den visuella representationen av dina program och övervaknings inställningar. Information om hur du använder program kartan finns i [program karta: prioritering distribuerade program](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="use-search"></a>Använd Sök

Använd Sök funktionen för att fråga efter andra speciella telemetri-objekt. På sidan **distribuerad spårning** väljer du **Sök**. Mer information om hur du använder Sök funktionen finns i [använda Sök i Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="use-application-insights"></a>Använd Application Insights

Application Insights tillhandahåller övervaknings funktioner utöver program kartan och Sök funktionen. Sök i Azure Portal efter programmets namn och öppna sedan en Application Insights sida för att hitta övervaknings information. Mer information om hur du använder dessa verktyg finns i [Azure Monitor logg frågor](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).

## <a name="disable-application-insights"></a>Inaktivera Application Insights

1. Gå till sidan Azure våren Cloud service i Azure Portal.
1. Vid **övervakning**väljer du **distribuerad spårning**.
1. Välj **inaktivera** om du vill inaktivera Application Insights.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du aktiverar och förstår distribuerad spårning i Azure våren Cloud. Information om hur du binder tjänster till ett program finns i [binda en Azure Cosmos DB-databas till ett Azure våren Cloud-program](spring-cloud-tutorial-bind-cosmos.md).
