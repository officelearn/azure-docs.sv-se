---
title: Loggar i Azure Monitor | Microsoft Docs
description: Beskriver loggar i Azure Monitor som används för avancerad analys av övervaknings data.
documentationcenter: ''
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 09/09/2020
ms.author: bwren
ms.openlocfilehash: e08c649b9a1d7e8b909a413ee435fce30a8d7e48
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "90032796"
---
# <a name="azure-monitor-logs-overview"></a>Översikt över Azure Monitor loggar
Azure Monitor loggar är en funktion i Azure Monitor som samlar in och ordnar loggdata från olika källor och gör det tillgängligt för analys med hjälp av ett avancerat frågespråk. Data från olika källor kan samlas in i en enda arbets yta och analyseras tillsammans för att utföra sådana uppgifter och trender för analys, avisering och visualisering.

## <a name="relationship-to-azure-monitor-metrics"></a>Relation till Azure Monitor mått
Azure Monitor Mät värden lagrar numeriska data i en databas för tids serier, vilket gör dessa data mer lätta än Azure Monitor loggar och stöder nära real tids scenarier som gör dem särskilt användbara för avisering och snabb identifiering av problem. Mått kan bara lagra numeriska data i en viss struktur, medan loggar kan lagra olika typer av data med sin egen struktur. Du kan också utföra komplex analys på loggar data med hjälp av logg frågor som inte kan användas för analys av mät data.

Numeriska data skickas ofta från data källor till loggar utöver mått. Även om det finns ytterligare kostnader för insamling och bevarande av dessa data i loggar, kan du inkludera mått data i logg frågor och analysera dem med andra övervaknings data.

## <a name="relationship-to-azure-data-explorer"></a>Relation till Azure Datautforskaren
Azure Monitor loggar baseras på Azure Datautforskaren. En Log Analytics-arbetsyta är ungefär detsamma som en databas i Azure Datautforskaren, tabeller struktureras samma och båda använder samma KQL (Kusto Query Language). Erfarenheten av att använda Log Analytics att arbeta med Azure Monitor frågor i Azure Portal liknar upplevelsen med Azure Datautforskaren-webbgränssnittet. Du kan till och med [ta med data från en Log Analytics arbets yta i en Azure datautforskaren-fråga](/azure/data-explorer/query-monitor-data). 


## <a name="structure-of-data"></a>Datastruktur
Data som samlas in av Azure Monitor loggar lagras i en [Log Analytics arbets yta](./design-logs-deployment.md) som innehåller flera tabeller som lagrar data från en viss källa. Arbets ytan definierar den geografiska platsen för data, åtkomst behörighet som definierar vilka användare som har åtkomst till data och konfigurations inställningar som pris nivå och datakvarhållning. Du kan använda en enskild arbets yta för alla dina övervaknings data eller skapa flera arbets ytor beroende på dina behov. Se [utforma dina Azure Monitor loggar distribution](design-logs-deployment.md) på överväganden för att skapa flera arbets ytor.

Varje arbets yta innehåller flera tabeller som är ordnade i separata kolumner med flera rader med data. Varje tabell definieras av en unik uppsättning kolumner som delas av rader med data från data källan. 

[![Struktur för Azure Monitor loggar](media/data-platform-logs/logs-structure.png)](media/data-platform-logs/logs-structure.png#lightbox)


Loggdata från Application Insights lagras också i Azure Monitor loggar, men den lagras olika beroende på hur programmet har kon figurer ATS. För ett arbets grupps program lagras data i en Log Analytics arbets yta i en standard uppsättning tabeller för att lagra data, till exempel program begär Anden, undantag och sid visningar. Flera program kan använda samma arbets yta. För ett klassiskt program lagras inte data i en Log Analytics-arbetsyta. Den använder samma frågespråk och du skapar och kör frågor med hjälp av samma Log Analytics-verktyg i Azure Portal. Data för klassiska program lagras separat från varandra. Den allmänna strukturen är densamma som arbets ytans program, även om tabell-och kolumn namnen skiljer sig åt. Se [arbets ytans baserade resurs ändringar](../app/apm-tables.md) för en detaljerad jämförelse av de två.


> [!NOTE]
> Vi ger fortfarande fullständig bakåtkompatibilitet för dina Application Insights klassiska resurs frågor, arbets böcker och loggbaserade aviseringar inom Application Insightss upplevelsen. Om du vill fråga/Visa mot den [nya arbetsytebaserade tabell strukturen/schemat](../app/apm-tables.md) måste du först gå till din Log Analytics-arbetsyta. Under förhands granskningen får du till gång till den klassiska Application Insights fråge upplevelsen genom att välja **loggar** från Application Insights fönstret. Mer information finns i [fråge omfånget](../log-query/scope.md) .


[![Azure Monitor loggar struktur för Application Insights](media/data-platform-logs/logs-structure-ai.png)](media/data-platform-logs/logs-structure-ai.png#lightbox)

## <a name="log-queries"></a>Loggfrågor
Data hämtas från en Log Analytics arbets yta med en [logg fråga](../log-query/log-query-overview.md) som är en skrivskyddad begäran för att bearbeta data och returnera resultat. Logg frågor skrivs i [KQL (Kusto Query Language)](/azure/data-explorer/kusto/query/), vilket är frågespråket som används av Azure datautforskaren. Använd Log Analytics, som är ett verktyg i Azure Portal för att redigera och köra logg frågor och analysera resultaten interaktivt. Du kan sedan använda de frågor som du skapar för att stödja andra funktioner i Azure Monitor till exempel aviseringar och arbets böcker för logg frågor.


## <a name="sources-of-data-for-azure-monitor-logs"></a>Data källor för Azure Monitor loggar
Azure Monitor samlar in loggdata från en rad olika källor, inklusive resurser i Azure Monitor och agenter som körs på virtuella datorer. Se [vad som övervakas av Azure Monitor?](../monitor-reference.md) för en fullständig lista över data källor som skickar data till en Log Analytics-arbetsyta.



## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [logg frågor](../log-query/log-query-overview.md) för att hämta och analysera data från en Log Analytics-arbetsyta.
- Lär dig mer om [mått i Azure Monitor](data-platform-metrics.md).
- Lär dig mer om [övervaknings data som är tillgängliga](data-sources.md) för olika resurser i Azure.

