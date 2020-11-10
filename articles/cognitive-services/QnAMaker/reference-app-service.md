---
title: Tjänst konfiguration – QnA Maker
description: Förstå hur och var du konfigurerar resurser.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 11/9/2020
ms.openlocfilehash: d2095919d6f6be482390250fd8d0ee20e9015237
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2020
ms.locfileid: "94444868"
---
# <a name="service-configuration"></a>Tjänstkonfiguration

Varje version av QnA Maker använder en annan uppsättning Azure-resurser (tjänster). I den här artikeln beskrivs anpassningar som stöds för dessa tjänster. 

## <a name="app-service"></a>App Service

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

QnA Maker använder App Service för att tillhandahålla frågekörningen som används av GenerateAnswer- [API: et](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker4.0/runtime/generateanswer).

De här inställningarna är tillgängliga i Azure Portal för App Service. Inställningarna är tillgängliga genom att välja **Inställningar** och sedan **konfiguration**.

Du kan ange en enskild inställning antingen via listan program inställningar eller ändra flera inställningar genom att välja **Avancerad redigering**.

|Resurs|Inställningen|
|--|--|
|AzureSearchAdminKey|Kognitiv sökning – används för QnA par Storage och Ranger #1|
|AzureSearchName|Kognitiv sökning – används för QnA par Storage och Ranger #1|
|DefaultAnswer|Svars text när ingen matchning hittas|
|UserAppInsightsAppId|Chat-logg och telemetri|
|UserAppInsightsKey|Chat-logg och telemetri|
|UserAppInsightsName|Chat-logg och telemetri|

Du måste **starta om** tjänsten från sidan **Översikt** i Azure Portal när du är klar med ändringarna.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

App Service anpassningar gäller inte för QnA Maker Managed (för hands version).

---

## <a name="qna-maker-service"></a>QnA Maker tjänst

Tjänsten QnA Maker tillhandahåller konfiguration för följande användare att samar beta på en enskild QnA Maker tjänst och alla dess kunskaps baser.

Lär dig [hur du lägger till medarbetare](./how-to/collaborate-knowledge-base.md) till din tjänst.

## <a name="change-azure-cognitive-search"></a>Ändra Azure-Kognitiv sökning

Lär dig [hur du ändrar kognitiv söknings tjänsten som är](./how-to/set-up-qnamaker-service-azure.md#configure-qna-maker-to-use-different-cognitive-search-resource) kopplad till din QNA Maker-tjänst.

## <a name="change-default-answer"></a>Ändra standardsvar

Lär dig [hur du ändrar texten för dina standard svar](How-To/change-default-answer.md). 

## <a name="telemetry"></a>Telemetri

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (stabil utgåva)](#tab/v1)

Application Insights används för att övervaka telemetri med QnA Maker GA. Det finns inga konfigurations inställningar som är speciella för QnA Maker.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker hanterad (för hands version)](#tab/v2)

Lär dig [hur du lägger till telemetri till din QNA Maker hanterade tjänst (för hands version)](How-To/get-analytics-knowledge-base.md). 

---

## <a name="app-service-plan"></a>App Service-plan

# <a name="qnamaker-ga-stable-release"></a>[QnAMaker GA (stabil utgåva)](#tab/v1)

App Services plan har inga konfigurations inställningar som är speciella för QnA Maker.

# <a name="qnamaker-managed-preview-release"></a>[QnAMaker-hanterad (för hands version)](#tab/v2)

App Service planen används inte med QnA Maker hanterad (för hands version).

---

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [format](reference-document-format-guidelines.md) för dokument och webb adresser som du vill importera till en kunskaps bas.
