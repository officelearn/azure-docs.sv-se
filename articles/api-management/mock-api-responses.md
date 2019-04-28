---
title: Simulera API-svar med Azure Portal | Microsoft Docs
description: Den här självstudien visar hur du använder API Management (APIM) för att ange principer för API så att den returnerar ett simulerat svar. Den här metoden gör att utvecklare kan fortsätta med implementering och testning av API Management-instanser även om serverdelen inte är tillgänglig för att skicka verkliga svar.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: 9b9a691cb2bce2357d184420912ab340aee534e8
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128091"
---
# <a name="mock-api-responses"></a>Simulera API-svar

API på serverdelen kan importeras till ett APIM API eller skapas och hanteras manuellt. De här stegen i denna självstudie visar hur du använder APIM för att skapa ett tomt API och hanterar det manuellt. Du får även lära dig att hur du anger principer för API så att det returnerar ett simulerat svar. Den här metoden gör att utvecklare kan fortsätta med implementering och testning av APIM-instanser även om serverdelen inte är tillgänglig för att skicka verkliga svar. Möjligheten att skapa simulerade svar kan vara användbart i flera olika scenarier:

+ Om API-fasaden utformas först och implementeringen av serverdelen kommer senare. Eller om serverdelen utvecklas parallellt.
+ Om serverdelen tillfälligt inte fungerar eller inte går att skala.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett test-API 
> * Lägg till en åtgärd till test-API:et
> * Aktivera simulerade svar
> * Testa det simulerade API:et

![Svar från simulerade åtgärder](./media/mock-api-responses/mock-api-responses01.png)

## <a name="prerequisites"></a>Nödvändiga komponenter

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Förstå [begreppet principer i Azure API Management](api-management-howto-policies.md).
+ Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Skapa ett test-API 

Stegen i det här avsnittet visar hur du skapar ett tomt API utan serverdel. Det visar även hur du lägger till en åtgärd i API:et. Ett fel genereras när du anropar åtgärden efter att du slutfört stegen i det här avsnittet. Du kommer inte få fel när du slutför stegen i avsnittet ”Aktivera simulerade svar”.

![Skapa tomt API](./media/mock-api-responses/03-MockAPIResponses-01-CreateTestAPI.png)

1. I **API Management-tjänsten** väljer du **API**.
2. Välj **+ Lägg till API** på den vänstra menyn.
3. Välj **Tomt API** i listan.
4. Ange *Test-API* för **Visningsnamn**.
5. Ange *Obegränsade* för **Produkter**.
6. Välj **Skapa**.

## <a name="add-an-operation-to-the-test-api"></a>Lägg till en åtgärd till test-API:et

![Lägga till åtgärd i API](./media/mock-api-responses/03-MockAPIResponses-02-AddOperation.png)

1. Välj det API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärd**.

    | Inställning             | Värde                             | Beskrivning                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Visningsnamn**    | *Testanrop*                       | Det här namnet visas i **utvecklarportalen**.                                                                                                                                       |
    | **URL** (HTTP-verb) | HÄMTA                               | Du kan välja någon av de fördefinierade HTTP-verben.                                                                                                                                         |
    | **URL**             | */test*                           | En URL-sökväg för API:et.                                                                                                                                                                       |
    | **Beskrivning**     |                                   | Ange en beskrivning av åtgärden för att tillhandahålla dokumentation för utvecklare som använder detta API i **utvecklarportalen**.                                                    |
    | Fliken **Fråga**       |                                   | Du kan lägga till frågeparametrar. Förutom att tillhandahålla namn och beskrivning, kan du även ange värden som kan tilldelas till den här parametern. Ett av värdena kan vara markerat som standard (valfritt). |
    | Fliken **Begäran**     |                                   | Du kan definiera innehållstyper för begäran, exempel och scheman.                                                                                                                                  |
    | Fliken **Svar**    | Se steg som följer den här tabellen. | Ange svarsstatuskoder, innehållstyper, exempel och scheman.                                                                                                                           |

3. Välj fliken **Svar**, som finns under fälten URL, Visningsnamn och Beskrivning.
4. Klicka på **+ Lägg till svar**.
5. Välj **200 OK** från listan.
6. Under rubriken **Återangivelser** till höger väljer du **+ Lägg till återgivning**.
7. Ange ”*application/json*” i sökrutan och välj sedan innehållstypen **application/json**.
8. I textrutan **Exempel** anger du `{ 'sampleField' : 'test' }`.
9. Välj **Skapa**.

## <a name="enable-response-mocking"></a>Aktivera simulerade svar

![Aktivera simulerade svar](./media/mock-api-responses/03-MockAPIResponses-03-EnableMocking.png)

1. Välj det API som du skapade i stege ”Skapa ett test-API”.
2. Välj den teståtgärd som du lade till.
3. I fönstret till höger klickar du på fliken **Design**.
4. I fönstret **Inkommande bearbetning** klickar du på **+ Lägg till princip**.
5. Välj panelen **Simulerade svar** i galleriet.

    ![Panelen Princip för simulerade svar](./media/mock-api-responses/mock-responses-policy-tile.png)

6. I textrutan **API Management-svar** anger du **200 OK, application/json**. Det här alternativet anger att ditt API ska returnera svars-exemplet som du definierade i föregående avsnitt.

    ![Aktivera simulerade svar](./media/mock-api-responses/mock-api-responses-set-mocking.png)

7. Klicka på **Spara**.

## <a name="test-the-mocked-api"></a>Testa det simulerade API:et

![Testa simulerat API](./media/mock-api-responses/03-MockAPIResponses-04-TestMocking.png)

1. Välj det API som du skapade i steget ”Skapa ett test-API”.
2. Öppna fliken **Test**.
3. Kontrollera att API:et **Testanrop** är markerat.

    > [!TIP]
    > Ett gult fält med texten **Simulering har aktiverats** anger att svaren som returneras från API-hanteringen är simulerade, och inte riktiga svar från serverdelen.

4. Välj **Skicka** för att göra ett testanrop.
5. **HTTP-svaret** visar den JSON som användes som ett exempel i den första delen av självstudien.

    ![Aktivera simulerade svar](./media/mock-api-responses/mock-api-responses-test-response.png)

## <a name="video"></a>Video

> [!VIDEO https://www.youtube.com/embed/i9PjUAvw7DQ]

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa ett test-API
> * Lägg till en åtgärd till test-API:et
> * Aktivera simulerade svar
> * Testa det simulerade API:et

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Omvandla och skydda ett publicerat API](transform-api.md)
