---
title: Azure Event Grid – fel sökning av prenumerations validering
description: Den här artikeln visar hur du kan felsöka prenumerations valideringar.
services: event-grid
author: spelluru
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: spelluru
ms.openlocfilehash: a052d4c268fadc60f754630156fe0bc0d33acf3b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82630192"
---
# <a name="troubleshoot-azure-event-grid-errors"></a>Felsöka Azure Event Grid fel
Den här fel söknings guiden innehåller information om hur du felsöker händelse prenumerations valideringar. 


## <a name="troubleshoot-event-subscription-validation"></a>Felsöka verifiering av händelse prenumeration

Om du ser ett fel meddelande, till exempel `The attempt to validate the provided endpoint https://your-endpoint-here failed. For more details, visit https://aka.ms/esvalidation`, innebär det att det är ett fel i validerings hand skakningen när händelse prenumerationen skapas. Kontrol lera följande aspekter för att lösa det här felet:

- Gör ett HTTP-inlägg till webhook-URL: en med ett [exempel på SubscriptionValidationEvent](webhook-event-delivery.md#validation-details) för begäran med Postman eller sväng eller liknande verktyg.
- Om webhooken implementerar mekanismen för synkron verifierings hand skakning kontrollerar du att ValidationCode returneras som en del av svaret.
- Om webhooken implementerar mekanismen för asynkron verifierings hand skakning kontrollerar du att HTTP-inlägget returnerar 200 OK.
- Om webhooken returnerar 403 (förbjuden) i svaret kontrollerar du att webhooken ligger bakom en Azure Application Gateway eller brand vägg för webbaserade program. Om så är fallet måste du inaktivera dessa brand Väggs regler och göra ett HTTP-inlägg igen:

  920300 (begäran saknar ett accept-huvud, vi kan åtgärda detta)

  942430 (begränsad SQL Character-avvikelse identifiering (argument): # av specialtecken har överskridits (12))

  920230 (flera URL-kodning identifierade)

  942130 (SQL-injektering: SQL-Tautology har identifierats.)

  931130 (möjliga RFI-angrepp (Remote File inkludering) = av-domän referens/länk)

> [!IMPORTANT]
> Detaljerad information om slut punkts validering för Webhooks finns i avsnittet om att [leverera en webhook](webhook-event-delivery.md).

## <a name="validate-event-grid-event-subscription-using-postman"></a>Verifiera Event Grid Event-prenumeration med Postman
Här är ett exempel på hur du använder Postman för att verifiera en webhook-prenumeration på en Event Grid händelse: 

![Händelse prenumerations verifiering i Event Grid med Postman](./media/troubleshoot-subscription-validation/event-subscription-validation-postman.png)

Här är ett exempel på en SubscriptionValidationEvent JSON:

```json
[
  {
    "id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66",
    "topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "subject": "",
    "data": {
      "validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6",
    },
    "eventType": "Microsoft.EventGrid.SubscriptionValidationEvent",
    "eventTime": "2018-01-25T22:12:19.4556811Z",
    "metadataVersion": "1",
    "dataVersion": "1"
  }
]
```

Här är exempel på lyckat svar:

```json
{
  "validationResponse": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"
}
```

Mer information om händelse validering i Event Grid för Webhooks finns i [slut punkts validering med event Grid-händelser](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="validate-cloud-event-subscription-using-postman"></a>Verifiera moln händelse prenumeration med Postman
Här är ett exempel på hur du använder Postman för att verifiera en webhook-prenumeration på en moln händelse: 

![Moln händelse prenumerations validering med Postman](./media/troubleshoot-subscription-validation/cloud-event-subscription-validation-postman.png)

Använd **http Options-** metoden för att verifiera med moln händelser. Mer information om validering av moln händelser för Webhooks finns i [slut punkts validering med moln händelser](webhook-event-delivery.md#endpoint-validation-with-event-grid-events).

## <a name="event-grid-event-subscription-validation-using-curl"></a>Händelse prenumerations verifiering i Event Grid med hjälp av sväng 
Här är ett exempel på en spiral-kommando för att verifiera en webhook-prenumeration för en Event Grid händelse: 

```bash
curl -X POST -d '[{"id": "2d1781af-3a4c-4d7c-bd0c-e34b19da4e66","topic": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx","subject": "","data": {"validationCode": "512d38b6-c7b8-40c8-89fe-f46f9e9622b6"},"eventType": "Microsoft.EventGrid.SubscriptionValidationEvent","eventTime": "2018-01-25T22:12:19.4556811Z", "metadataVersion": "1","dataVersion": "1"}]' -H 'Content-Type: application/json' https://{your-webhook-url.com}
```

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan du publicera ditt problem i [Stack Overflow-forumet](https://stackoverflow.com/questions/tagged/azure-eventgrid) eller öppna ett [support ärende](https://azure.microsoft.com/support/options/). 
