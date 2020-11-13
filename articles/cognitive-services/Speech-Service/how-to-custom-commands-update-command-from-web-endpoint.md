---
title: Uppdatera ett kommando från en webb slut punkt
titleSuffix: Azure Cognitive Services
description: uppdatera ett kommando från en webb slut punkt
services: cognitive-services
author: encorona-ms
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/20/2020
ms.author: encorona
ms.openlocfilehash: 4432843ac93002bc92068db191706352234d76e6
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2020
ms.locfileid: "94571291"
---
# <a name="update-a-command-from-a-web-endpoint"></a>Uppdatera ett kommando från en webb slut punkt

Om klient programmet måste uppdatera statusen för ett pågående kommando utan röst information kan du använda ett anrop till en webb slut punkt för att uppdatera kommandot.

I den här artikeln får du lära dig hur du uppdaterar ett pågående kommando från en webb slut punkt.

## <a name="prerequisites"></a>Förutsättningar
> [!div class = "checklist"]
> * En tidigare [skapad app för Anpassade kommandon](quickstart-custom-commands-application.md)

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion 

I det här exemplet behöver vi en HTTP-Triggered [Azure-funktion](https://docs.microsoft.com/azure/azure-functions/) som stöder följande indatatyper (eller en delmängd av dessa inaktuella ingångar).

```JSON
{
  "conversationId": "SomeConversationId",
  "currentCommand": {
    "name": "SomeCommandName",
    "parameters": {
      "SomeParameterName": "SomeParameterValue",
      "SomeOtherParameterName": "SomeOtherParameterValue"
    }
  },
  "currentGlobalParameters": {
      "SomeGlobalParameterName": "SomeGlobalParameterValue",
      "SomeOtherGlobalParameterName": "SomeOtherGlobalParameterValue"
  }
}
```

Tillåter granskning av de viktigaste attributen för den här indatamängden.

| Attribut | Förklaring |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **conversationId** | "conversationId" är den unika identifieraren för konversationen, Observera att detta ID kan genereras från klient programmet. |
| **currentCommand** | "currentCommand" är det kommando som är aktivt i konversationen. |
| **Namn** | "namn" är namnet på kommandot och "parameters" är en karta med de aktuella värdena för parametrarna. |
| **currentGlobalParameters** | "currentGlobalParameters" är också en karta som "parametrar", men används för globala parametrar. |

Utdata från Azure-funktionen måste ha stöd för följande format.

```JSON
{
  "updatedCommand": {
    "name": "SomeCommandName",
    "updatedParameters": {
      "SomeParameterName": "SomeParameterValue"
    },
    "cancel": false
  },
  "updatedGlobalParameters": {
    "SomeGlobalParameterName": "SomeGlobalParameterValue"
  }
}
```

Du kan känna igen det här formatet eftersom det är samma som används vid [uppdatering av ett kommando från klienten](./how-to-custom-commands-update-command-from-client.md). 

Skapa nu en Azure-funktion baserat på NodeJS och kopiera och klistra in den här koden

```nodejs
module.exports = async function (context, req) {
    context.log(req.body);
    context.res = {
        body: {
            updatedCommand: {
                name: "IncrementCounter",
                updatedParameters: {
                    Counter: req.body.currentCommand.parameters.Counter + 1
                }
            }
        }
    };
}
```

När vi kallar den här Azure-funktionen från anpassade kommandon skickar vi de aktuella värdena för konversationen. vi kommer att returnera de parametrar som vi vill uppdatera eller om vi vill avbryta det aktuella kommandot.

## <a name="update-the-existing-custom-commands-app"></a>Uppdatera den befintliga anpassade kommando-appen

Nu ska vi koppla upp Azure-funktionen med den befintliga anpassade kommando-appen.

1. Lägg till ett nytt kommando med namnet IncrementCounter.
1. Lägg bara till en exempel mening med värdet "Increment".
1. Lägg till en ny parameter med namnet Counter (samma namn som anges i Azure Function ovan) av typen Number med standardvärdet 0.
1. Lägg till en ny webb slut punkt med namnet IncrementEndpoint med URL: en för din Azure-funktion och med aktiverade fjärruppdateringar.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-web-endpoint-with-remote-updates.png" alt-text="Ange webb slut punkt med fjärruppdateringar":::
1. Skapa en ny interaktions regel med namnet "IncrementRule" och Lägg till en anrops webb slut punkts åtgärd.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-rule-web-endpoint.png" alt-text="Regel för ökning":::
1. I åtgärds konfigurationen väljer du IncrementEndpoint, konfigurerar på lyckad för att skicka tal svar med värdet räknare och vid fel med ett fel meddelande.
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/set-increment-counter-call-endpoint.png" alt-text="Ange slut punkt för anrop av ökande räknare":::
1. Ange att regelns efter körnings tillstånd ska vänta på användarens indata

## <a name="test-it"></a>Testa den

1. Spara och träna din app
1. Klicka på testa
1. Skicka några gånger "Increment" (som är exempel meningen för kommandot IncrementCounter)
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/custom-commands/increment-counter-example.png" alt-text="Exempel på öknings räknare":::

Observera hur värdet för Counter-parametern ökar i varje tur med Azure-funktionen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Aktivera en CI/CD-process för programmet Anpassade kommandon](./how-to-custom-commands-deploy-cicd.md)