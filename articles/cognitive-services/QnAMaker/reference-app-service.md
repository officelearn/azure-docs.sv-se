---
title: Tjänstkonfiguration - QnA Maker
description: Förstå hur och var resurser ska konfigureras.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651865"
---
# <a name="service-configuration"></a>Tjänstkonfiguration

QnA Maker använder flera Azure-resurser (tjänster) inklusive Kognitiv sökning, App Service, App Service Plan och Application Insights.

Alla anpassningar till dessa inställningar som stöds av QnA Maker visas nedan.

## <a name="app-service"></a>App Service

QnA Maker använder App-tjänsten för att tillhandahålla frågekörningen som används av [generateAnswer API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


Dessa inställningar är tillgängliga i Azure-portalen för App-tjänsten. Inställningarna är tillgängliga genom att välja **Inställningar**och sedan **Konfiguration**.

Du kan ange en enskild inställning antingen via listan Programinställningar eller ändra flera inställningar genom att välja **Avancerad redigering**.

|Resurs|Inställning|
|--|--|
|AzureSearchAdminKey|Cognitive Search - används för QnA-uppsättning lagring och Ranker #1|
|AzureSearchName (AzureSearchName)|Cognitive Search - används för QnA-uppsättning lagring och Ranker #1|
|DefaultAnswer|Svarstext när ingen matchning hittas|
|UserAppInsightsAppId|Chattlogg och telemetri|
|UserAppInsightsKey|Chattlogg och telemetri|
|UserAppInsightsName|Chattlogg och telemetri|

Läs om hur du [lägger till ändra din cognitive search-tjänst](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) till din tjänst.

Du måste **starta om** tjänsten från **översiktssidan** i Azure-portalen när du är klar med att göra ändringar.

## <a name="qna-maker-service"></a>QnA Maker-tjänst

Tjänsten QnA Maker ger konfiguration för följande användare att samarbeta på en enda QnA Maker-tjänst och alla dess kunskapsbaser.

Läs om hur du [lägger till medarbetare](./how-to/collaborate-knowledge-base.md) i din tjänst.

## <a name="application-insights"></a>Application Insights

Application Insights har inga konfigurationsinställningar som är specifika för QnA Maker.

## <a name="app-service-plan"></a>App Service-plan

App Service Plan har inga konfigurationsinställningar som är specifika för QnA Maker.

## <a name="next-steps"></a>Nästa steg

Läs mer om [format](reference-document-format-guidelines.md) för dokument och webbadresser som du vill importera till en kunskapsbas.