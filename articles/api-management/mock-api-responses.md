---
title: Självstudie – de blå API-svaren i API Management-Azure Portal | Microsoft Docs
description: I den här självstudien använder du API Management för att ange en princip för ett API så att den returnerar ett skissat svar om Server delen inte är tillgänglig för att skicka riktiga svar.
author: vladvino
ms.service: api-management
ms.custom: mvc
ms.topic: tutorial
ms.date: 09/30/2020
ms.author: apimpm
ms.openlocfilehash: 78743c5f045f2544cafe88414ed996d08bacd2a0
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631122"
---
# <a name="tutorial-mock-api-responses"></a>Självstudie: blå API-svar

Server dels-API: er kan importeras till ett API Management (APIM) API eller skapas och hanteras manuellt. Stegen i den här självstudien visar hur du använder APIM för att skapa ett tomt API och hanterar det manuellt, och sedan anger du en princip för ett API så att den returnerar ett skissat svar. Den här metoden gör att utvecklare kan fortsätta med implementering och testning av APIM-instanser även om serverdelen inte är tillgänglig för att skicka verkliga svar. 

Möjligheten att modellera upp svar kan vara användbar i ett antal scenarier:

+ Om API-fasaden utformas först och implementeringen av serverdelen kommer senare. Eller om serverdelen utvecklas parallellt.
+ Om serverdelen tillfälligt inte fungerar eller inte går att skala.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa ett test-API 
> * Lägg till en åtgärd till test-API:et
> * Aktivera simulerade svar
> * Testa det simulerade API:et


:::image type="content" source="media/mock-api-responses/mock-api-responses01.png" alt-text="Interagerat API-svar":::

## <a name="prerequisites"></a>Förutsättningar

+ Lär dig [Azure API Management-terminologin](api-management-terminology.md).
+ Förstå [begreppet principer i Azure API Management](api-management-howto-policies.md).
+ Slutför följande snabb start: [skapa en Azure API Management-instans](get-started-create-service-instance.md).

## <a name="create-a-test-api"></a>Skapa ett test-API 

Stegen i det här avsnittet visar hur du skapar ett tomt API utan serverdel. 


1. Logga in på Azure Portal och navigera till API Management-instansen.
1. Välj **API: er**  >  **+ Lägg till API**  >  **tomt API**.
1. I fönstret **skapa ett tomt API** väljer du **fullständig**.
1. Ange *test-API* för **visnings namn**.
1. Välj **obegränsat** för **produkter**.
1. Se till att **hanterad** är valt i **gatewayer**.
1. Välj **Skapa**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-01-create-test-api.png" alt-text="Interagerat API-svar":::

## <a name="add-an-operation-to-the-test-api"></a>Lägg till en åtgärd till test-API:et

Ett API visar en eller flera åtgärder. I det här avsnittet lägger du till en åtgärd i det tomma API som du skapade. Ett fel genereras när du anropar åtgärden efter att du slutfört stegen i det här avsnittet. Du får inga fel när du har slutfört stegen senare i avsnittet [Aktivera svars modeller](#enable-response-mocking) .

1. Välj det API som du skapade i föregående steg.
1. Välj **+ Lägg till åtgärd**.
1. Ange följande värden i fönstret **klient** del.

     | Inställning             | Värde                             | Beskrivning                                                                                                                                                                                   |
    |---------------------|-----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | **Visningsnamn**    | *Testanrop*                       | Namnet som visas i [Developer-portalen](api-management-howto-developer-portal.md).                                                                                                                                       |
    | **URL** (HTTP-verb) | GET                               | Välj en av de fördefinierade HTTP-verben.                                                                                                                                         |
    | **URL**             | */test*                           | En URL-sökväg för API:et.                                                                                                                                                                       |
    | **Beskrivning**     |                                   |  Valfri beskrivning av åtgärden som används för att tillhandahålla dokumentation i Developer-portalen till utvecklare som använder detta API.                                                    |
    
1. Välj fliken **svar** , som finns under fälten URL, visnings namn och beskrivning. Ange inställningar på den här fliken för att definiera svars status koder, innehålls typer, exempel och scheman.
1. Välj **+ Lägg till svar**och välj **200 OK** i listan.
1. Under rubriken **Återangivelser** till höger väljer du **+ Lägg till återgivning**.
1. Ange *Application/JSON* i sökrutan och välj innehålls typen **Application/JSON** .
1. I textrutan **Exempel** anger du `{ "sampleField" : "test" }`.
1. Välj **Spara**.

:::image type="content" source="media/mock-api-responses/03-mock-api-responses-02-add-operation.png" alt-text="Interagerat API-svar" border="false":::

Även om detta inte krävs för det här exemplet kan ytterligare inställningar för en API-åtgärd konfigureras på andra flikar, inklusive:


|Flik      |Beskrivning  |
|---------|---------|
|**Query**     |  Lägg till frågeparametrar. Förutom att ange ett namn och en beskrivning kan du ange värden som har tilldelats till en frågeparameter. Ett av värdena kan vara markerat som standard (valfritt).        |
|**Förfrågan**     |  Definiera innehålls typer, exempel och scheman för begäran.       |

## <a name="enable-response-mocking"></a>Aktivera simulerade svar

1. Välj det API som du skapade i [skapa ett test-API](#create-a-test-api).
1. Välj den teståtgärd som du lade till.
1. I fönstret till höger ser du till att fliken **design** är markerad.
1. I fönstret **inkommande bearbetning** väljer du **+ Lägg till princip**.

    :::image type="content" source="media/mock-api-responses/03-mock-api-responses-03-enable-mocking.png" alt-text="Interagerat API-svar" border="false":::

1. Välj **blå svar**  från galleriet.

    :::image type="content" source="media/mock-api-responses/mock-responses-policy-tile.png" alt-text="Interagerat API-svar" border="false":::

1. I textrutan **API Management-svar** anger du **200 OK, application/json**. Det här alternativet anger att ditt API ska returnera svars-exemplet som du definierade i föregående avsnitt.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-set-mocking.png" alt-text="Interagerat API-svar":::

1. Välj **Spara**.

    > [!TIP]
    > Ett gult fält med texts **skisser är aktiverat** för ditt API anger att svar som returneras från API Management skicka en skissad princip och inte ett faktiskt Server dels svar.

## <a name="test-the-mocked-api"></a>Testa det simulerade API:et

1. Välj det API som du skapade i [skapa ett test-API](#create-a-test-api).
1. Välj fliken **Test**.
1. Kontrollera att API:et **Testanrop** är markerat. Välj **Skicka** för att göra ett testanrop.

   :::image type="content" source="media/mock-api-responses/03-mock-api-responses-04-test-mocking.png" alt-text="Interagerat API-svar":::

1. **HTTP-svaret** visar den JSON som användes som ett exempel i den första delen av självstudien.

    :::image type="content" source="media/mock-api-responses/mock-api-responses-test-response.png" alt-text="Interagerat API-svar":::

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
