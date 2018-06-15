---
title: Använda fördefinierade domäner i THOMAS appar i Azure | Microsoft Docs
description: Lär dig hur du använder fördefinierade domäner i språk förstå Intelligent Service (THOMAS) program.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: v-geberr
ms.openlocfilehash: 72ca0def8528adae5e46a02fa5bfccd14a3b6ab4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "35355776"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Använda fördefinierade domäner i THOMAS appar  

Språk förstå (THOMAS) ger *färdiga domäner*, vilket är en fördefinierad uppsättning [intents](luis-how-to-add-intents.md) och [entiteter](luis-concept-entity-types.md) som fungerar tillsammans för domäner eller vanliga kategorier av klientprogram. Fördefinierade domäner har redan tränats och är redo att lägga till i appen THOMAS. Intents och entiteter i en fördefinierad domän är helt anpassningsbar när du har lagt till dem i din app – du kan lära dem med utterances från systemet så att de fungerar för dina användare. Du kan använda en hel färdiga domän som en startpunkt för anpassning eller bara lånar några avsikter eller enheter från en fördefinierad domän. 

Bläddra i **färdiga domäner** fliken för att se andra färdiga domäner som du kan använda i din app. Klicka på ikonen för en domän att lägga till din app, eller klicka på **mer** i dess sida vid sida för att lära dig om dess avsikter och enheter.

> [!TIP]
> Du hittar en fullständig lista över fördefinierade domäner i den [färdiga domäner referens](./luis-reference-prebuilt-domains.md).

![Lägg till fördefinierade domän](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän
I den **färdiga domäner** , hitta RestaurantReservation domän och klicka på **Lägg till domän**. När den färdiga domänen har lagts till appen THOMAS, öppna **Intents** och klicka på avsikten RestaurantReservation.Reserve. Du kan se att många exempel utterances redan har angetts och betecknas med entiteter.

![RestaurantReservation.Reserve avsikt](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Designa THOMAS appar från fördefinierade domäner
När du använder fördefinierade domäner i din app THOMAS du anpassa en hel färdiga domän eller bara börja med några av dess avsikter och enheter.

## <a name="customizing-an-entire-prebuilt-domain"></a>Anpassa en fördefinierad hela domänen
Fördefinierade domäner är avsedda att vara Allmänt. De innehåller många avsikter och enheter som du kan välja mellan att anpassa en app på dina behov. Om du startar från att anpassa en fördefinierad hela domänen, ta bort avsikter och enheter som din app inte behöver använda. Du kan också lägga till vissa avsikter eller entiteter som redan innehåller fördefinierade domänen. Om du använder till exempel den **händelser** färdiga domänen för en händelse sport-app kan du lägga till entiteter för sport team. När du startar [tillhandahåller utterances](luis-how-to-add-example-utterances.md) till THOMAS, ta med termer som är specifika för din app. THOMAS lär sig att identifiera dem och anpassar domänen färdiga avsikter och enheter till din app behov. 

> [!TIP]
> Intents och entiteter i en fördefinierad domän fungerar bäst tillsammans. Det är bättre att kombinera avsikter och enheter från samma domän när det är möjligt.
> * Ett bra tips är att använda relaterade avsikter från samma domän. Till exempel om du anpassar den `MakeReservation` avsiktshantering i den **platser** domän, välj sedan den `Cancel` avsiktshantering från den **platser** domän i stället för att avbryta avsikten i **Händelser** eller **verktyg** domäner.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändra funktionssättet för en fördefinierad domän avsikt
Du kanske upptäcker att en fördefinierad domän innehåller syftet som liknar en avsikten som du vill ha i din app THOMAS men den ska fungera annorlunda. Till exempel den **platser** färdiga domän ger en `MakeReservation` avsett för att göra en restaurang reservation, men du vill att din app att använda den avsikten för att göra hotell reservationer. I så fall kan du ändra funktionssättet för att avsikten genom att tillhandahålla utterances THOMAS om att skapa hotell reservationer och ge dem etiketter med hjälp av den `MakeReservation` avsiktshantering, så sedan THOMAS kan vara retrained för att identifiera den `MakeReservation` avsiktshantering i en begäran till bok hotell .

> [!TIP]
> Checka ut domänen verktyg för färdiga avsikter som du kan anpassa för användning i en domän. Du kan till exempel lägga till `Utilities.Repeat` till appen och train identifierar de åtgärder användaren kanske vill upprepa i ditt program.


## <a name="next-step"></a>Nästa steg

Anpassa en fördefinierad domän genom att lägga till fler exempel utterances.

> [!div class="nextstepaction"]
> [Lägg till exempel utterances](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Ytterligare resurser

Finns det [färdiga domäner referens](./luis-reference-prebuilt-domains.md) för mer information om de fördefinierade domänerna.
