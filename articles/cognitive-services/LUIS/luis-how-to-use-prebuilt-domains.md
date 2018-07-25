---
title: Använda fördefinierade domäner i LUIS-appar i Azure | Microsoft Docs
description: Lär dig mer om att använda fördefinierade domäner i Language Understanding Intelligent Service (LUIS) program.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 12/13/2017
ms.author: diberry
ms.openlocfilehash: df57f9adf5bf7f5f652a77ddeafe950463c6a9fc
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39224185"
---
# <a name="use-prebuilt-domains-in-luis-apps"></a>Använda fördefinierade domäner i LUIS-appar  

Språkförståelse (LUIS) erbjuder *fördefinierade domäner*, som är färdiga uppsättningar [avsikter](luis-how-to-add-intents.md) och [entiteter](luis-concept-entity-types.md) som fungerar tillsammans för domäner eller vanliga kategorier av klientprogram. Fördefinierade domäner har redan tränats och är redo att lägga till i din LUIS-app. Avsikter och entiteter i en fördefinierade domän är helt anpassningsbar när du har lagt till dem i din app – du kan träna dem med yttranden från systemet så att de fungerar för dina användare. Du kan använda en hel fördefinierade domän som en startpunkt för anpassning eller bara låna några avsikter eller entiteter från en fördefinierade domän. 

Bläddra i **fördefinierade domäner** fliken för att se andra fördefinierade domäner som du kan använda i din app. Klicka på panelen för en domän ska lägga till den i din app eller klicka på **mer** i dess panel och se hur dess avsikter och entiteter.

> [!TIP]
> Du hittar en fullständig lista över fördefinierade domäner i den [fördefinierade domäner referens](./luis-reference-prebuilt-domains.md).

![Lägg till fördefinierade domän](./media/luis-how-to-prebuilt-domain-entities/add-prebuilt-domain.png)


## <a name="add-a-prebuilt-domain"></a>Lägga till en fördefinierad domän
I den **fördefinierade domäner** fliken, hitta en RestaurantReservation domän och klickar på **Lägg till domän**. När den färdiga domänen har lagts till LUIS-appen, öppna **avsikter** och klicka på RestaurantReservation.Reserve avsikten. Du kan se att många exempel yttranden redan har angetts och som är märkt med entiteter.

![RestaurantReservation.Reserve avsikt](./media/luis-how-to-prebuilt-domain-entities/prebuilt-domain-restaurant-reservation.png)


## <a name="designing-luis-apps-from-prebuilt-domains"></a>Designa LUIS-appar från fördefinierade domäner
När du använder fördefinierade domäner i din LUIS-app kan du anpassa en hel fördefinierade domän eller inleda med några av dess avsikter och entiteter.

## <a name="customizing-an-entire-prebuilt-domain"></a>Anpassa en hel fördefinierade domän
Fördefinierade domäner är avsedda att vara Allmänt. De innehåller många intentioner och entiteter som du kan välja mellan att anpassa en app efter dina behov. Om du startar från att anpassa en hel fördefinierade domän, ta bort avsikter och entiteter som din app inte behöver använda. Du kan också lägga till vissa avsikter eller entiteter som redan innehåller fördefinierade domänen. Exempel: Om du använder den **händelser** fördefinierade domänen för en händelse sport-app, kan du för att lägga till entiteter för sportklubbar. När du startar [att tillhandahålla yttranden](luis-how-to-add-example-utterances.md) till LUIS, ingår villkor som är specifika för din app. LUIS lär sig att identifiera dem och anpassar fördefinierade domänens avsikter och entiteter till appens behov. 

> [!TIP]
> Avsikter och entiteter i en fördefinierade domän fungerar bäst tillsammans. Är det bättre att kombinera avsikter och entiteter från samma domän när det är möjligt.
> * Ett bra tips är att använda relaterade avsikter från samma domän. Exempel: Om du anpassar den `MakeReservation` avsikt i den **platser** domän, välj sedan den `Cancel` avsikt från den **platser** domänen i stället för Avbryt avsikten i **Händelser** eller **verktyg** domäner.

## <a name="changing-the-behavior-of-a-prebuilt-domain-intent"></a>Ändra beteendet för en fördefinierade domän avsikt
Du kanske upptäcker att en fördefinierade domän innehåller ett intent som liknar ett intent som du vill ha i din LUIS-app, men du vill att den fungerar på olika sätt. Till exempel den **platser** fördefinierade domänen innehåller en `MakeReservation` avsikt för att göra en restaurang reservation, men du vill att din app att använda den avsikten för att göra hotell reservationer. I så fall kan du kan ändra beteendet för detta syfte genom att tillhandahålla yttranden till LUIS om att skapa hotell reservationer och märkning dem med hjälp av den `MakeReservation` avsiktlig, så sedan LUIS kan vara modellkomponenten för att identifiera den `MakeReservation` avsikt i en begäran om att boka ett hotell .

> [!TIP]
> Kolla in verktyg domänen för fördefinierade avsikter som du kan anpassa för användning i en domän. Du kan till exempel lägga till `Utilities.Repeat` till din app och träna identifiera vilka åtgärder som användaren kanske vill upprepa i ditt program.


## <a name="next-step"></a>Nästa steg

Anpassa en fördefinierade domän genom att lägga till fler exempel yttranden.

> [!div class="nextstepaction"]
> [Lägg till exempel yttranden](./luis-how-to-add-example-utterances.md)

## <a name="additional-resources"></a>Ytterligare resurser

Se den [fördefinierade domäner referens](./luis-reference-prebuilt-domains.md) för mer information om fördefinierade domäner.
