---
title: Använd Cortana färdiga appen från THOMAS | Microsoft Docs
description: Använd Cortana personlig assistent ett fördefinierade program från språk förstå Intelligent tjänster (THOMAS).
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: b792d090d037ef180258a1634d4bd063c0a71b9a
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35353319"
---
# <a name="cortana-prebuilt-app"></a>Cortana färdiga appen

> [!IMPORTANT]
> Vi rekommenderar att du använder den [färdiga domäner](./luis-how-to-use-prebuilt-domains.md), i stället för Cortana färdiga appen. Till exempel i stället för **builtin.intent.calendar.create_calendar_entry**, använda **Calendar.Add** från den **kalender** färdiga domän.
> De färdiga domänerna ger följande fördelar: 
> * De ger paket med fördefinierade och pretrained avsikter och enheter som är utformade för att fungera med varandra. Du kan integrera en fördefinierad domän direkt i din app. Till exempel om du bygger en lämplighet spåraren, du kan lägga till den **lämplighet** domän och har en hel grupp avsikter och enheter för att spåra lämplighet aktiviteter, inklusive avsikter för spårning av vikt och rätten planering återstående tid eller avstånd och sparar lämplighet aktivitet anteckningar.
> * De färdiga domän avsikter kan anpassas. Om du vill ange granskning av hotell du till exempel träna och anpassa den **Places.GetReviews** avsiktshantering från den **platser** domänen för att identifiera begäranden för hotell granskningar.
> * De färdiga domänerna är extensible. Till exempel om du vill använda den **platser** färdiga domän i en bot som söker efter hotell och behov syftet för att hämta typ av köket med alla dess kan du skapa och träna en **Places.GetCuisine** avsikt.

Förutom att du kan skapa egna program, ger THOMAS också avsikter och enheter från Microsoft Cortana personliga assistent som en fördefinierad app. Den här offentligt tillgängliga THOMAS appens beteende kan inte ändras. Intents och enheter i det här programmet kan inte redigeras eller integrerad i andra THOMAS appar. Om du vill att ditt klientprogram ska ha åtkomst till både färdiga programmet och THOMAS programmet, har ditt klientprogram att referera till båda THOMAS appar.

Den färdiga personliga assistent appen är tillgänglig i dessa kulturer (språk): engelska, franska, italienska, spanska och kinesiska.

## <a name="get-the-endpoint-for-the-cortana-prebuilt-app"></a>Hämta slutpunkten för Cortana färdiga appen

Du kan komma åt Cortana färdiga appen med följande slutpunkter: 

| Språk | Slutpunkt|
|--------| ------------------|
| Svenska| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821|
|    Kinesiska| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c27c4af7-d44a-436f-a081-841bb834fa29|
|    Franska| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/0355ead1-2d08-4955-ab95-e263766e8392|
|    Spanska| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/cb2675e5-fbea-4f8b-8951-f071e9fc7b38|
|    Italienska| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/30a0fddc-36f4-4488-b022-03de084c1633|


> [!NOTE]
> Slutpunkten URL: er är också tillgängliga på den [appar – hämta personliga assistent program](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c32) API.

## <a name="try-out-the-personal-assistant-app"></a>Testa appen personliga assistent
För att anropa slutpunkten, kan du lägga till din prenumeration argument och fråga sträng nyckel till slutpunkten. 

Till exempel om utterance som du vill tolka är ”skapa ett möte för team möte”, och sedan kan du lägga till den utterance slutpunkts-URL. 

```
https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/c413b2ef-382c-45bd-8ff0-f76d60e2a821?subscription-key={YOUR-SUBSCRIPTION-KEY}&q=create an appointment for team meeting
```

Du kan klistra in Webbadressen till en webbläsare och ersätta din prenumeration nyckel för den `{YOUR-SUBSCRIPTION-KEY}` fältet.

I webbläsaren ser du att den färdiga appen Cortana identifierar `builtin.intent.calendar.create_calendar_entry` som avsikt, och `builtin.calendar.title` som entitetstyp och för utterance `create an appointment for team meeting`.

![Använda fördefinierade Cortana-app](./media/luis-how-to-prebuilt-cortana/luis-prebuilt-cortana-browser.png)

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Referera till Cortana färdiga appen](./luis-reference-cortana-prebuilt.md)

