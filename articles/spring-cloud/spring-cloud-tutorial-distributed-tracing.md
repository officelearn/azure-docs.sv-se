---
title: Självstudie – Använd distribuerad spårning med Azure våren Cloud
description: I den här självstudien visas hur du använder spridning av våren-moln via Azure Application Insights
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 9c049ecbea3c630e0f7d08e4a42bd441ba3f5cfa
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74708763"
---
# <a name="tutorial-using-distributed-tracing-with-azure-spring-cloud"></a>Självstudie: använda distribuerad spårning med Azure våren Cloud

Våren Clouds verktyg för distribuerad spårning möjliggör enkel fel sökning och övervakning av komplexa problem. Azure våren Cloud integrerar [våren Cloud-Sleuth](https://spring.io/projects/spring-cloud-sleuth) med azures [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/app-insights-overview) för att tillhandahålla kraftfull, distribuerad spårnings funktion från Azure Portal.

I den här artikeln får du lära dig att:

> [!div class="checklist"]
> * Aktivera distribuerad spårning i Azure Portal
> * Lägg till vår moln Sleuth i ditt program
> * Visa beroende kartor för dina mikrotjänst program
> * Sök spårnings data med olika filter

## <a name="prerequisites"></a>Krav

För att slutföra den här självstudien behöver du:

* En Azure våren Cloud-tjänst som redan har tillhandahållits och körs.  Slutför [den här snabb](spring-cloud-quickstart-launch-app-cli.md) starten för att etablera och starta en Azure våren Cloud-tjänst.
    
## <a name="add-dependencies"></a>Lägg till beroenden

Aktivera zipkin-avsändaren att skicka till webben genom att lägga till följande rad i filen Application. Properties:

```xml
spring.zipkin.sender.type = web
```

Du kan hoppa över nästa steg om du följde vår [Guide för att förberedelser ett Azure våren Cloud-program](spring-cloud-tutorial-prepare-app-deployment.md). Annars går du till din lokala utvecklings miljö och redigerar `pom.xml`-filen så att den omfattar våren Cloud Sleuth-beroendet:

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

* Bygg och distribuera igen för din Azure våren Cloud-tjänst för att avspegla dessa ändringar. 

## <a name="modify-the-sample-rate"></a>Ändra samplings frekvensen
Du kan ändra den hastighet som din telemetri samlas in genom att ändra samplings frekvensen. Om du till exempel vill använda hälften som ofta går du till `application.properties`-filen och ändrar följande rad:

```xml
spring.sleuth.sampler.probability=0.5
```

Om du redan har skapat och distribuerat ett program kan du ändra samplings frekvensen genom att lägga till ovanstående rad som en miljö variabel i Azure CLI eller portalen. 

## <a name="enable-application-insights"></a>Aktivera Application Insights

1. Gå till sidan Azure våren Cloud service i Azure Portal.
1. I avsnittet övervakning väljer du **distribuerad spårning**.
1. Välj **Redigera inställning** för att redigera eller lägga till en ny inställning.
1. Skapa en ny insikts fråga för program, eller så kan du välja en befintlig.
1. Välj vilken loggnings kategori som du vill övervaka och ange kvarhållningsperioden (i dagar).
1. Välj **tillämpa** för att tillämpa den nya spårningen.

## <a name="view-application-map"></a>Visa program karta

Gå tillbaka till sidan distribuerad spårning och välj **Visa program karta**. Granska den visuella representationen av dina program och övervaknings inställningar. Information om hur du använder program kartan finns i [den här artikeln](https://docs.microsoft.com/azure/azure-monitor/app/app-map).

## <a name="search"></a>Sök

Använd Sök funktionen för att fråga andra speciella telemetri-objekt. På sidan **distribuerad spårning** väljer du **Sök**. Mer information om hur du använder Sök funktionen finns i [den här artikeln](https://docs.microsoft.com/azure/azure-monitor/app/diagnostic-search).

## <a name="application-insights-page"></a>Application Insights sida

Application Insights tillhandahåller övervaknings funktioner utöver program kartan och Sök funktionen. Sök i Azure Portal efter programmets namn och starta sedan en Application Insights sida för att få mer information. Mer information om hur du använder dessa verktyg finns i [dokumentationen](https://docs.microsoft.com/azure/azure-monitor/log-query/query-language).


## <a name="disable-application-insights"></a>Inaktivera Application Insights

1. Gå till sidan Azure våren Cloud service i Azure Portal.
1. Klicka på **distribuerad spårning**i avsnittet övervakning.
1. Klicka på **inaktivera** om du vill inaktivera Application Insights

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du aktiverar och förstår distribuerad spårning i Azure våren-molnet. Fortsätt till nästa självstudie om du vill lära dig hur du binder ditt program till en Azure-CosmosDB.

> [!div class="nextstepaction"]
> [Lär dig hur du binder ditt program till en Azure-CosmosDB](spring-cloud-tutorial-bind-cosmos.md).
