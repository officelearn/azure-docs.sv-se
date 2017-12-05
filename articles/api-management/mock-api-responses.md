---
title: Mock API-svar med Azure-portalen | Microsoft Docs
description: "Den här kursen visar hur du använder API Management (APIM) för att ange en princip för en API så att den returnerar ett mocked svar. Den här metoden endables utvecklare att fortsätta med implementering och testning av API Management-instans om serverdelen inte är tillgänglig för att skicka verkliga svar."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: e485071b026c52eb23532639546ad475fc92cde3
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="mock-api-responses"></a>Fingerad API-svar

Backend APIs kan importeras till en APIM API eller skapas och hanteras manuellt. Stegen i den här kursen visar hur du använder APIM att skapa en tom API och hantera den manuellt. Kursen visar hur du ställer in en princip på en API så att den returnerar ett mocked svar. Den här metoden gör att utvecklare kan fortsätta med implementering och testning av APIM-instans även om serverdelen inte är tillgänglig för att skicka verkliga svar. Möjligheten att mock bort svar kan vara användbart i ett antal scenarier:

+ När API facade är utformad först och backend-implementeringen kommer senare. Eller serverdelen utvecklas parallellt.
+ När serverdelen är tillfälligt inte operativa eller går inte att skala.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Skapa en test-API 
> * Lägg till en åtgärd till test-API
> * Aktivera svar mocking
> * Testa mocked API

![Mocked åtgärden svar](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Krav

Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-test-api"></a>Skapa en test-API 

Stegen i det här avsnittet visar hur du skapar en tom API med ingen serverdelen. Den visar även hur du lägger till en åtgärd i API: et. Anropar igen när du har slutfört stegen i det här avsnittet genererar ett fel. Du får inga fel när du har slutfört stegen i avsnittet ”Aktivera svar mocking”.

1. Välj **API: er** från under **API MANAGEMENT**.
2. I den vänstra menyn, Välj **+ Lägg till API**.
3. Välj **tomt API** från listan.
4. Ange ”*Test-API*” för **visningsnamn**.
5. Ange ”*obegränsade*” för **produkter**.
6. Välj **Skapa**.

## <a name="add-an-operation-to-the-test-api"></a>Lägg till en åtgärd till test-API

1. Välj API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärden**.

    ![Mocked åtgärden svar](./media/mock-api-responses/mock-api-responses02.png)

    |Inställning|Värde|Beskrivning|
    |---|---|---|
    |**URL: en** (HTTP-verb)|HÄMTA|Du kan välja någon av de fördefinierade HTTP-verb.|
    |**URL: EN** |*/test*|URL-sökvägen för API: et. |
    |**Visningsnamn**|*Testa anrop*|Namnet som visas i den **utvecklarportalen**.|
    |**Beskrivning**||Ange en beskrivning av åtgärden som används för att tillhandahålla dokumentation för utvecklare som använder detta API i den **utvecklarportalen**.|
    |**Frågan** fliken||Du kan lägga till Frågeparametrar. Förutom att tillhandahålla ett namn och beskrivning, kan du ange värden som kan tilldelas till den här parametern. Ett av värdena kan vara markerad som standard (valfritt).|
    |**Begära** fliken||Du kan definiera begäran innehållstyper, exempel och scheman. |
    |**Svaret** fliken|Se steg som följer den här tabellen.|Ange svarsstatuskoder, innehållstyper, exempel och scheman.|

3. Välj den **svar** fliken finns under URL, namn och beskrivning.
4. Klicka på **+ Lägg till svaret**.
5. Välj **200 OK** från listan.
6. Under den **garantier** rubrik till höger, Välj **+ Lägg till representation**.
7. Ange ”*application/json*” i sökrutan och väljer den **application/json** innehållstyp.
8. I den **exempel** text Ange ”*{'sampleField”: ”test”}*”.
9. Välj **Spara**.

## <a name="enable-response-mocking"></a>Aktivera svar mocking

1. Välj API som du skapade i steg ”skapa ett test-API”.
2. Välj teståtgärden som du har lagt till.
2. I fönstret till höger klickar du på den **Design** fliken.
3. I den **inkommande bearbetning** fönster, klicka på pennikonen.
4. I den **Mocking** väljer **statiska svar** för **Mocking beteende**.
5. I den **API Management returnerar följande svar:** skriver **200 OK application/json**. Det här alternativet anger att din API ska returnera svar-exempel som du definierade i föregående avsnitt.
6. Välj **Spara**.

## <a name="test-the-mocked-api"></a>Testa mocked API

1. Välj API som du skapade i steg ”skapa ett test-API”.
2. Öppna den **Test** fliken.
3. Se till att den **testa anropet** API är markerad.

    > [!TIP]
    > Ett gult fält med texten **Mocking aktiveras** anger att svar som returnerades från API-hantering skickar en mocking och inte ett faktiska backend-svar.

3. Välj **skicka** att göra ett test anropa.
4. Den **HTTP-svar** visar JSON som tillhandahålls som ett exempel i den första delen av kursen.

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]
> 
> 

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en test-API
> * Lägg till en åtgärd till test-API
> * Aktivera svar mocking
> * Testa mocked API

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Transformera och skydda publicerade API](transform-api.md)
