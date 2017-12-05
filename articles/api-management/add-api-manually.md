---
title: "Lägg till en API som manuellt med hjälp av Azure portal | Microsoft Docs"
description: "Den här kursen visar hur du använder API Management (APIM) för att lägga till en API manuellt."
services: api-management
documentationcenter: 
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/22/2017
ms.author: apimpm
ms.openlocfilehash: 9426839f88daece1bb688a2079b7854ccaebdc57
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="add-an-api-manually"></a>Lägga till en API manuellt 

Stegen i den här artikeln visar hur du använder Azure-portalen för att lägga till en API manuellt till API Management (APIM)-instans. Ett vanligt scenario när du vill skapa ett tomt API och definiera den manuellt är när du vill mock API: et. Mer information om mocking API finns [Mock API-svar](mock-api-responses.md).

Om du vill importera en befintlig API Se [Närliggande](#related-topics) avsnitt.

I den här artikeln vi skapa ett tomt API och ange [httpbin.org](http://httpbin.org) (en offentlig tester service) som en backend-API.

## <a name="prerequisites"></a>Krav

Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-an-api"></a>Skapa ett API

1. Välj **API: er** från under **API MANAGEMENT**.
2. I den vänstra menyn, Välj **+ Lägg till API**.
3. Välj **tomt API** från listan.

    ![Tomt API](media/add-api-manually/blank-api.png)
4. Ange inställningar för API: et.

    ![Inställningar](media/add-api-manually/settings.png)

    |**Namn**|**Värde**|**Beskrivning**|
    |---|---|---|
    |**Visningsnamn**|”*Tomma API*” |Det här namnet visas i Developer-portalen.|
    |**Webbtjänstens URL** (valfritt)| ”*http://httpbin.org*”| Om du vill mock en API kan du inte ange något. <br/>I detta fall kan vi ange [http://httpbin.org](http://httpbin.org). Detta är en offentlig tester. <br/>Om du vill importera en API som är mappad till en serverdel automatiskt finns i något av avsnitten i den [Närliggande](#related-topics) avsnitt.|
    |**URL-schema**|”*HTTPS*”|I det här fallet, även om serverdelen har icke-säker HTTP-åtkomst, ange vi en säker HTTPS APIM åtkomst till serverdelen. <br/>Den här typen av scenario (HTTPS till HTTP) kallas HTTPS-avslutning. Du kan göra det om din API finns inom ett virtuellt nätverk (där du vet att åtkomsten är säkra, även om inte används för HTTPS). <br/>Du kanske vill använda ”HTTPS-avslutning” att spara på vissa CPU-cykler.|
    |**URL-suffix**|”*hbin*”| Suffixet är ett namn som identifierar den här specifika API: et i den här APIM-instansen. Det måste vara unikt i den här APIM-instansen.|
    |**Produkter**|”*Obegränsade*” |Publicera API: et genom att associera API: et med en produkt. Om du vill för API som publiceras och vara tillgänglig att utvecklare kan lägga till en produkt. Du kan göra det under skapande av API eller Ställ in den senare.<br/><br/>Produkter är kopplingar till en eller flera API: er. Du kan innehålla ett antal API: er och erbjuda utvecklare via developer-portalen. <br/>Utvecklare måste först prenumerera på en produkt kan få åtkomst till API: et. När man prenumererar få de en prenumeration nyckel som är bra för API: er i produkten. Om du har skapat APIM instans är du administratör redan, så du prenumererar på varje produkt som standard.<br/><br/> Som standard varje API Management-instans som levereras med två exempel produkter: **Starter** och **obegränsad**.| 
5. Välj **Skapa**.

Nu har du inga åtgärder i APIM som mappar till åtgärder i backend-API. Om du anropar en åtgärd som exponeras via serverdelen men inte via APIM, får du en **404**. 

>[!NOTE] 
> Som standard när du lägger till en API, även om den är ansluten till en backend-tjänst APIM inte visar några åtgärder förrän du godkända dem. Skapa en APIM åtgärd som mappar till backend-åtgärden till godkända en åtgärd i backend-tjänst.
>

## <a name="add-and-test-an-operation"></a>Lägga till och testa en åtgärd

Det här avsnittet visar hur du lägger till en ”/ hämta”-åtgärd för att mappa till serverdelen ”http://httpbin.org/get” igen.

### <a name="add-the-operation"></a>Lägga till åtgärden

1. Välj API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärden**.
3. I den **URL**väljer **hämta** och ange ”*/get*” i resursen.
4. Ange ”*FetchData*” för **visningsnamn**.
5. Välj **Spara**.

### <a name="test-the-operation"></a>Testa åtgärden

Testa åtgärden i Azure-portalen. Du kan också testa den i den **utvecklarportalen**.

1. Välj den **Test** fliken.
2. Välj **FetchData**.
3. Tryck på **skicka**.

Svaret som genererar åtgärden ”http://httpbin.org/get” visas. Om du vill omvandla din verksamhet finns [transformera och skydda din API](transform-api.md).

## <a name="add-and-test-a-parameterized-operation"></a>Lägga till och testa en åtgärd med parametrar

Det här avsnittet visar hur du lägger till en åtgärd som tar en parameter. I det här fallet mappa vi åtgärden ”http://httpbin.org/status/200”.

### <a name="add-the-operation"></a>Lägga till åtgärden

1. Välj API som du skapade i föregående steg.
2. Klicka på **+ Lägg till åtgärden**.
3. I den **URL**väljer **hämta** och ange ”*/status/ {code}*” i resursen. Du kan också kan du ange information som är associerade med den här parametern. Ange till exempel ”*nummer*” för **typen**”,*200*” (standard) för **värden**.
4. Ange ”GetStatus” för **visningsnamn**.
5. Välj **Spara**.

### <a name="test-the-operation"></a>Testa åtgärden 

Testa åtgärden i Azure-portalen.  Du kan också testa den i den **utvecklarportalen**.

1. Välj den **Test** fliken.
2. Välj **GetStatus**. Värdet för koden är som standard ”*200*”. Du kan ändra det om du vill testa andra värden. Skriv till exempel ”*418*”.
3. Tryck på **skicka**.

    Svaret som genererar åtgärden ”http://httpbin.org/status/200” visas. Om du vill omvandla din verksamhet finns [transformera och skydda din API](transform-api.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-append-apis.md)]

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Transformera och skydda publicerade API](transform-api.md)
