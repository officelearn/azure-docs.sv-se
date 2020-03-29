---
title: Definiera modereringsarbetsflöden med REST API-konsolen - Innehållsmoderator
titleSuffix: Azure Cognitive Services
description: Du kan använda AZURE Content Moderator Review API:er för att definiera anpassade arbetsflöden och tröskelvärden baserat på dina innehållsprinciper.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "72754189"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definiera och använda modereringsarbetsflöden (REST)

Arbetsflöden är molnbaserade anpassade filter som du kan använda för att hantera innehåll mer effektivt. Arbetsflöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och sedan vidta lämpliga åtgärder. Den här guiden visar hur du använder REST-API:erna för arbetsflödet, via API-konsolen, för att skapa och använda arbetsflöden. När du har förstått API:ernas struktur kan du enkelt portera dessa anrop till valfri REST-kompatibel plattform.

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för [granskning](https://contentmoderator.cognitive.microsoft.com/) av innehållsmoderator.

## <a name="create-a-workflow"></a>Skapa ett arbetsflöde

Om du vill skapa eller uppdatera ett arbetsflöde går du till referenssidan **[för arbetsflöde - Skapa eller uppdatera](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** API och väljer knappen för nyckelområdet (du hittar den i slutpunkts-URL:en på sidan **Autentiseringsuppgifter** i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsolen, där du enkelt kan konstruera och köra REST API-anrop.

![Arbetsflöde - Val av sidregion för att skapa eller uppdatera](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Ange parametrar för REST-anrop

Ange värden för **team,** **arbetsflödesnamn**och **Ocp-Apim-Subscription-Key:**

- **team:** Det grupp-ID som du skapade när du konfigurerade ditt [granskningsverktygskonto](https://contentmoderator.cognitive.microsoft.com/) (finns i **fältet ID** på skärmen Autentiseringsuppgifter för granskningsverktyget).
- **arbetsflödesnamn**: Namnet på ett nytt arbetsflöde som ska läggas till (eller ett befintligt namn, om du vill uppdatera ett befintligt arbetsflöde).
- **Ocp-Apim-Prenumeration-Key:** Din Innehåll Moderator nyckel. Du hittar detta på fliken **Inställningar** i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com).

![Arbetsflöde - Skapa eller uppdatera konsolfrågaparametrar och rubriker](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Ange en arbetsflödesdefinition

1. Redigera **rutan Begäran om** du vill ange JSON-begäran med `Text`information om **Beskrivning** och **typ** (antingen `Image` eller ).
2. För **Uttryck**kopierar du standardarbetsflödet JSON-uttryck. Din sista JSON sträng bör se ut så här:

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
> Du kan definiera enkla, komplexa och till och med kapslade uttryck för dina arbetsflöden med hjälp av det här API:et. Dokumentationen [Arbetsflöde - Skapa eller uppdatera](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) har exempel på mer komplex logik.

### <a name="submit-your-request"></a>Skicka din förfrågan
  
Välj **Skicka**. Om åtgärden lyckas är `200 OK` **svarsstatusen** och rutan `true` **Svarsinnehåll** visas .

### <a name="examine-the-new-workflow"></a>Undersök det nya arbetsflödet

Välj **Inställningar** > **arbetsflöden**i [granskningsverktyget](https://contentmoderator.cognitive.microsoft.com/). Det nya arbetsflödet ska visas i listan.

![Granska verktygslista över arbetsflöden](images/workflow-console-new-workflow.PNG)

Välj alternativet **Redigera** för arbetsflödet och gå till fliken **Designer.** Här kan du se en intuitiv representation av JSON-logiken.

![Fliken Designer för ett markerat arbetsflöde](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Hämta arbetsflödesinformation

Om du vill hämta information om ett befintligt arbetsflöde går du till referenssidan **[för arbetsflöde - få](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** API och väljer knappen för din region (den region där nyckeln administreras).

![Arbetsflöde - Hämta regionval](images/test-drive-region.png)

Ange REST-samtalsparametrarna som i avsnittet ovan. Kontrollera att **arbetsflödesnamnet** är namnet på ett befintligt arbetsflöde den här gången.

![Hämta frågeparametrar och rubriker](images/workflow-get-default.PNG)

Välj **Skicka**. Om åtgärden lyckas är `200 OK` **svarsstatusen** och rutan **Svarsinnehåll** visar arbetsflödet i JSON-format, till exempel följande:

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

- Lär dig hur du använder arbetsflöden med [jobb för innehållsmoderering](try-review-api-job.md).