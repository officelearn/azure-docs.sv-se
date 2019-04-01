---
title: Definiera moderering arbetsflöden med REST API-konsolen – Content Moderator
titlesuffix: Azure Cognitive Services
description: 'Du kan använda API: er för Azure innehåll Moderator granska för att definiera anpassade arbetsflöden och tröskelvärden baserat på dina principer för innehåll.'
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: article
ms.date: 03/14/2019
ms.author: sajagtap
ms.openlocfilehash: e150b1321f2fbd348e737222c752203281503643
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2019
ms.locfileid: "58756583"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definiera och Använd moderering arbetsflöden (REST)

Arbetsflöden är molnbaserade anpassade filter som du kan använda för att hantera innehåll på ett mer effektivt. Arbetsflöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Den här guiden visar hur du använder arbetsflödet REST API: er via API-konsolen kan du skapar och använder arbetsflöden. När du förstår hur API: er kan portera du enkelt dessa anrop till valfri REST-kompatibel plattform.

## <a name="prerequisites"></a>Förutsättningar

- Logga in eller skapa ett konto på Content Moderator [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) plats.

## <a name="create-a-workflow"></a>Skapa ett arbetsflöde

Om du vill skapa eller uppdatera ett arbetsflöde, går du till den **[arbetsflöde – skapa eller uppdatera](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API refererar till sidan och klicka på knappen för din nyckel region (du hittar den i slutpunkts-URL på den **autentiseringsuppgifter**  för den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsol, där du kan enkelt skapa och köra REST API-anrop.

![Arbetsflöde – skapa eller uppdatera sidan val](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Ange parametrar för REST-anrop

Ange värden för **team**, **workflowname**, och **Ocp-Apim-Subscription-Key**:

- **team**: Lag-ID som du skapade när du ställer in din [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/) konto (finns i den **Id** på din granskningsverktyget autentiseringsuppgifter skärmen).
- **workflowname**: Namnet på ett nytt arbetsflöde för att lägga till (eller ett befintligt namn om du vill uppdatera ett befintligt arbetsflöde).
- **Ocp-Apim-Subscription-Key**: Din nyckel för Content Moderator. Du hittar den på den **inställningar** fliken den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com).

![Arbetsflöde – skapa eller uppdatera konsolen frågeparametrar och rubriker](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Ange en arbetsflödesdefinition

1. Redigera den **Begärandetext** om du vill ange JSON-begäran med information om **beskrivning** och **typ** (antingen `Image` eller `Text`).
2. För **uttryck**, kopiera standardarbetsflödet JSON-uttryck. Din sista JSON-strängen ska se ut så här:

```json
{
  "Description":"<A description for the Workflow>",
  "Type":"Text",
  "Expression":{
    "Type":"Logic",
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isAdult",
      "Operator":"eq",
      "Value":"true",
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    }
  }
}
```

> [!NOTE]
> Du kan definiera enkel, komplexa och även kapslade uttryck för dina arbetsflöden som använder detta API. Den [arbetsflöde – skapa eller uppdatera](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) -dokumentationen finns exempel på mer komplex logik.

### <a name="submit-your-request"></a>Skicka din begäran
  
Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** rutan visar `true`.

### <a name="examine-the-new-workflow"></a>Granska det nya arbetsflödet

I den [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/)väljer **inställningar** > **arbetsflöden**. Ett nytt arbetsflöde ska visas i listan.

![Granska verktyget listan över arbetsflöden](images/workflow-console-new-workflow.PNG)

Välj den **redigera** för arbetsflödet och gå till den **Designer** fliken. Här kan du se en intuitiv representation av JSON-logiken.

![Designer fliken för ett valt arbetsflöde](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Hämta information om arbetsflöde

Om du vill hämta information om ett befintligt arbetsflöde, går du till den **[arbetsflöde – Get](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API refererar till sidan och klicka på knappen för din region (den region där din nyckel administreras).

![Arbetsflöde – Get valet](images/test-drive-region.png)

Ange parametrar för REST-anrop som i avsnittet ovan. Se till att den här gången **workflowname** är namnet på ett befintligt arbetsflöde.

![Hämta frågeparametrar och rubriker](images/workflow-get-default.PNG)

Välj **Skicka**. Om åtgärden lyckas den **svarsstatus** är `200 OK`, och **svarsinnehåll** visar arbetsflödet i JSON-format som liknar följande:

```json
{
  "Name":"default",
  "Description":"Default",
  "Type":"Image",
  "Expression":{
    "If":{
      "ConnectorName":"moderator",
      "OutputName":"isadult",
      "Operator":"eq",
      "Value":"true",
      "AlternateInput":null,
      "Type":"Condition"
    },
    "Then":{
      "Perform":[
        {
          "Name":"createreview",
          "Subteam":null,
          "CallbackEndpoint":null,
          "Tags":[

          ]
        }
      ],
      "Type":"Actions"
    },
    "Else":null,
    "Type":"Logic"
  }
}
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du använder arbetsflöden med [innehåll moderering jobb](try-review-api-job.md).