---
title: Tjänst konfiguration – QnA Maker
description: Förstå hur och var du konfigurerar resurser.
ms.topic: reference
ms.date: 02/21/2020
ms.openlocfilehash: 8ef6fecbfeb119d0c68ec5bc3bbc90ec449dbb7d
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2020
ms.locfileid: "77651865"
---
# <a name="service-configuration"></a>Tjänst konfiguration

QnA Maker använder flera Azure-resurser (tjänster), inklusive Kognitiv sökning, App Service, App Service plan och Application Insights.

Alla anpassningar av de här inställningarna som stöds av QnA Maker visas nedan.

## <a name="app-service"></a>App Service

QnA Maker använder App Service för att tillhandahålla frågekörningen som används av GenerateAnswer- [API: et](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer).


De här inställningarna är tillgängliga i Azure Portal för App Service. Inställningarna är tillgängliga genom att välja **Inställningar**och sedan **konfiguration**.

Du kan ange en enskild inställning antingen via listan program inställningar eller ändra flera inställningar genom att välja **Avancerad redigering**.

|Resource|Inställning|
|--|--|
|AzureSearchAdminKey|Kognitiv sökning – används för QnA set Storage and Rangeer #1|
|AzureSearchName|Kognitiv sökning – används för QnA set Storage and Rangeer #1|
|DefaultAnswer|Svars text när ingen matchning hittas|
|UserAppInsightsAppId|Chat-logg och telemetri|
|UserAppInsightsKey|Chat-logg och telemetri|
|UserAppInsightsName|Chat-logg och telemetri|

Lär dig [hur du lägger till ändra din kognitiv sökning-tjänst](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) till din tjänst.

Du måste **starta om** tjänsten från sidan **Översikt** i Azure Portal när du är klar med ändringarna.

## <a name="qna-maker-service"></a>QnA Maker-tjänsten

Tjänsten QnA Maker tillhandahåller konfiguration för följande användare att samar beta på en enskild QnA Maker tjänst och alla dess kunskaps baser.

Lär dig [hur du lägger till medarbetare](./how-to/collaborate-knowledge-base.md) till din tjänst.

## <a name="application-insights"></a>Application Insights

Application Insights har inga konfigurations inställningar som är speciella för QnA Maker.

## <a name="app-service-plan"></a>App Service-plan

App Services plan har inga konfigurations inställningar som är speciella för QnA Maker.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [format](reference-document-format-guidelines.md) för dokument och webb adresser som du vill importera till en kunskaps bas.