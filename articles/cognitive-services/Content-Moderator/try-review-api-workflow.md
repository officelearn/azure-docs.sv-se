---
title: Definiera moderator arbets flöden med REST API-konsolen – Content Moderator
titleSuffix: Azure Cognitive Services
description: 'Du kan använda Azure Content Moderator gransknings-API: er för att definiera anpassade arbets flöden och tröskelvärden baserat på dina innehålls principer.'
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/14/2019
ms.author: pafarley
ms.openlocfilehash: 3e58be4b94457d95d28cf6528b9151e4be1802cf
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2019
ms.locfileid: "72754189"
---
# <a name="define-and-use-moderation-workflows-rest"></a>Definiera och använda arbets flöden för redaktörer (REST)

Arbets flöden är molnbaserade anpassade filter som du kan använda för att hantera innehåll mer effektivt. Arbets flöden kan ansluta till en mängd olika tjänster för att filtrera innehåll på olika sätt och vidta lämplig åtgärd. Den här guiden visar hur du använder REST-API: er för arbets flöden via API-konsolen för att skapa och använda arbets flöden. När du förstår API: ernas struktur kan du enkelt Porta dessa anrop till alla REST-kompatibla plattformar.

## <a name="prerequisites"></a>Krav

- Logga in eller skapa ett konto på webbplatsen för Content Moderator [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) .

## <a name="create-a-workflow"></a>Skapa ett arbets flöde

Om du vill skapa eller uppdatera ett arbets flöde går du till sidan **[arbets flöde – skapa eller uppdatera API-](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59)** referens och väljer knappen för nyckel regionen (du hittar det i slut punkts-URL: en på sidan **autentiseringsuppgifter** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/)). Detta startar API-konsolen där du enkelt kan skapa och köra REST API-anrop.

![Arbets flöde – skapa eller uppdatera val av sid region](images/test-drive-region.png)

### <a name="enter-rest-call-parameters"></a>Ange REST-anrops parametrar

Ange värden för **team**, **workflowname**och **OCP-APIM-Subscription-Key**:

- **team**: det team-ID som du skapade när du konfigurerade kontot för [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/) (finns i fältet **ID** på skärmens inloggnings uppgifter).
- **workflowname**: namnet på ett nytt arbets flöde som ska läggas till (eller ett befintligt namn om du vill uppdatera ett befintligt arbets flöde).
- **OCP-APIM-Subscription-Key**: din Content moderator-nyckel. Du hittar detta på fliken **Inställningar** i [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com).

![Arbets flöde – skapa eller uppdatera parametrar och rubriker för konsolen](images/workflow-console-parameters.PNG)

### <a name="enter-a-workflow-definition"></a>Ange en arbets flödes definition

1. Redigera rutan **begär ande text** för att ange JSON-begäran med information om **Beskrivning** och **typ** (antingen `Image` eller `Text`).
2. För **uttryck**kopierar du standard uttrycket för arbets flödets JSON. Den slutgiltiga JSON-strängen bör se ut så här:

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
> Du kan definiera enkla, komplexa och till och med kapslade uttryck för dina arbets flöden med hjälp av det här API: et. [Arbets flödet – skapa eller uppdatera](https://westus2.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b46b3f9b0711b43c4c59) dokumentationen innehåller exempel på mer komplex logik.

### <a name="submit-your-request"></a>Skicka in din begäran
  
Välj **Skicka**. Om åtgärden lyckas är **svars status** `200 OK` och rutan **svars innehåll** visar `true`.

### <a name="examine-the-new-workflow"></a>Granska det nya arbets flödet

I [gransknings verktyget](https://contentmoderator.cognitive.microsoft.com/)väljer du **Inställningar**  > **arbets flöden**. Det nya arbets flödet bör visas i listan.

![Gransknings verktygs lista över arbets flöden](images/workflow-console-new-workflow.PNG)

Välj **redigerings** alternativet för arbets flödet och gå till fliken **Designer** . Här kan du se en intuitiv representation av JSON-logiken.

![Fliken Designer för ett valt arbets flöde](images/workflow-console-new-workflow-designer.PNG)

## <a name="get-workflow-details"></a>Hämta arbets flödes information

Om du vill hämta information om ett befintligt arbets flöde går du till sidan **[arbets flöde – Hämta API-](https://westus.dev.cognitive.microsoft.com/docs/services/580519463f9b070e5c591178/operations/5813b44b3f9b0711b43c4c58)** referens och väljer knappen för din region (den region där nyckeln administreras).

![Arbets flöde – Hämta regions val](images/test-drive-region.png)

Ange resten anrops parametrar som i avsnittet ovan. Se till att den här tiden, **workflowname** är namnet på ett befintligt arbets flöde.

![Hämta frågeparametrar och rubriker](images/workflow-get-default.PNG)

Välj **Skicka**. Om åtgärden lyckas är **svars status** `200 OK` och i rutan **svars innehåll** visas arbets flödet i JSON-format, t. ex. följande:

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

- Lär dig hur du använder arbets flöden med [innehålls redigerings jobb](try-review-api-job.md).