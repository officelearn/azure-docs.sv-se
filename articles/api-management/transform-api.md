---
title: Transformera och skydda din API med Azure API Management | Microsoft Docs
description: "Lär dig hur du skyddar ditt API med kvoter och begränsningsprinciper (frekvensbegränsning)."
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
ms.date: 11/19/2017
ms.author: apimpm
ms.openlocfilehash: 772f3828d85c54e7b8bb44c857e555175b7444cc
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="transform-and-protect-your-api"></a>Transformera och skydda ditt API 

Kursen visar hur du transformera dina API så att den inte avslöjar ett privat backend-information. Du kanske vill dölja information om teknikstacken som körs på serverdelen. Du kanske också vill dölja ursprungliga URL: er som visas i brödtexten för HTTP-svar för API: er och omdirigerar dem i stället till APIM gateway.

Den här kursen visar också hur lätt det är att lägga till skydd för din serverdel API genom att konfigurera hastighetsbegränsning med Azure API Management. Du kan till exempel vill begränsa ett antal anrop API: et anropas så att den är måtta av utvecklare. Mer information finns i [API Management-principer](api-management-policies.md)

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Omvandla en API för att ta bort svarshuvuden
> * Ersätt ursprungliga URL: er i brödtexten för API-svar med APIM gateway URL: er
> * Skydda en API genom att lägga till hastighet gränsen princip (begränsning)
> * Testa omvandlingarna

![Principer](./media/transform-api/api-management-management-console.png)

## <a name="prerequisites"></a>Krav

+ Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
+ Dessutom slutföra följande kursen: [Import och publicera din första API](import-and-publish.md).
 
[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="transform-an-api-to-strip-response-headers"></a>Omvandla en API för att ta bort svarshuvuden

Det här avsnittet visar hur du dölja HTTP-huvuden som du inte vill visa för användarna. Följande huvuden tas bort i HTTP-svar i detta exempel:

* **X drivs av**
* **X-AspNet-Version**

### <a name="test-the-original-response"></a>Testa det ursprungliga svaret

Visa ursprungliga svaret:

1. Välj den **API** fliken.
2. Klicka på **Demo konferens API** API-listan.
3. Välj den **GetSpeakers** igen.
4. Klicka på den **Test** fliken överst på skärmen.
5. Tryck på den **skicka** längst ned på skärmen. 

    Som du kan se det ursprungliga svaret ser ut så här:

    ![Principer](./media/transform-api/original-response.png)

### <a name="set-the-transformation-policy"></a>Ange princip för omvandling

1. Bläddra till APIM-instans.
2. Välj den **API** fliken.
3. Klicka på **Demo konferens API** API-listan.
4. Välj **alla åtgärder**.
5. Överst på skärmen väljer **Design** fliken.
6. I den **utgående bearbetning** fönstret klickar du på triangel (bredvid penna).
7. Välj **redigerare**.
    
     ![Redigera princip](./media/set-edit-policies/set-edit-policies01.png)
9. Placera markören i den  **<outbound>**  element.
10. I det högra fönstret, under **principer för Anspråksomvandling**, klickar du på **+ uppsättning HTTP-huvudet** två gånger (för att infoga två princip kodavsnitt).

    ![Principer](./media/transform-api/transform-api.png)
11. Ändra din  **<outbound>**  kod för att se ut så här:

        <set-header name="X-Powered-By" exists-action="delete" />
        <set-header name="X-AspNet-Version" exists-action="delete" />
                
## <a name="replace-original-urls-in-the-body-of-the-api-response-with-apim-gateway-urls"></a>Ersätt ursprungliga URL: er i brödtexten för API-svar med APIM gateway URL: er

Det här avsnittet visar hur du dölja ursprungliga URL: er som visas i brödtexten för HTTP-svar för API: er och omdirigerar dem i stället till APIM gateway.

### <a name="test-the-original-response"></a>Testa det ursprungliga svaret

Visa ursprungliga svaret:

1. Välj den **API** fliken.
2. Klicka på **Demo konferens API** API-listan.
3. Välj den **GetSpeakers** igen.
4. Klicka på den **Test** fliken överst på skärmen.
5. Tryck på den **skicka** längst ned på skärmen. 

    Som du kan se det ursprungliga svaret ser ut så här:

    ![Principer](./media/transform-api/original-response2.png)

### <a name="set-the-transformation-policy"></a>Ange princip för omvandling

1. Bläddra till APIM-instans.
2. Välj den **API** fliken.
3. Klicka på **Demo konferens API** API-listan.
4. Välj **alla åtgärder**.
5. Överst på skärmen väljer **Design** fliken.
6. I den **utgående bearbetning** fönstret klickar du på triangel (bredvid penna).
7. Välj **redigerare**.
8. Placera markören i den  **<outbound>**  element.
9. I det högra fönstret, under **principer för Anspråksomvandling**, klickar du på **+ Sök och Ersätt strängen i brödtexten**.
10. Ändra din **< Sök och Ersätt** koden (i det  **<outbound>**  element) att ersätta URL: en för att matcha APIM-gateway. Exempel:

        <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>

## <a name="protect-an-api-by-adding-rate-limit-policy-throttling"></a>Skydda en API genom att lägga till hastighet gränsen princip (begränsning)

Det här avsnittet visar hur du lägger till skydd för din serverdel API genom att konfigurera hastighetsbegränsningar. Du kan till exempel vill begränsa ett antal anrop API: et anropas så att den är måtta av utvecklare. I det här exemplet anges gränsen till 3 anrop per 15 sekunder för varje prenumerations-Id. Efter 15 sekunder kan en utvecklare försök anropar API: et.

1. Bläddra till APIM-instans.
2. Välj den **API** fliken.
3. Klicka på **Demo konferens API** API-listan.
4. Välj **alla åtgärder**.
5. Överst på skärmen väljer **Design** fliken.
6. I den **inkommande bearbetning** fönstret klickar du på triangel (bredvid penna).
7. Välj **redigerare**.
8. Placera markören i den  **<inbound>**  element.
9. I det högra fönstret, under **åtkomst till principer för begränsning av**, klickar du på **+ gränsen anropet frekvensen per nyckel**.
10. Ändra din **< hastighet gränsen av nyckeln** koden (i det  **<inbound>**  element) till följande kod:

        <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />

## <a name="test-the-transformations"></a>Testa omvandlingarna
        
Nu dina principer koden ser ut så här:

    <policies>
        <inbound>
            <rate-limit-by-key calls="3" renewal-period="15" counter-key="@(context.Subscription.Id)" />
            <base />
        </inbound>
        <backend>
            <base />
        </backend>
        <outbound>
            <set-header name="X-Powered-By" exists-action="delete" />
            <set-header name="X-AspNet-Version" exists-action="delete" />
            <find-and-replace from="://conferenceapi.azurewebsites.net" to="://apiphany.azure-api.net/conference"/>
            <base />
        </outbound>
        <on-error>
            <base />
        </on-error>
    </policies>

Resten av det här avsnittet testar princip transformationer som du anger i den här artikeln.

### <a name="test-the-stripped-response-headers"></a>Testa Borttaget svarshuvuden

1. Bläddra till APIM-instans.
2. Välj den **API** fliken.
3. Klicka på **Demo konferens API** API-listan.
4. Klicka på den **GetSpeakers** igen.
5. Välj den **Test** fliken.
6. Tryck på **skicka**.

    Som du kan se rubrikerna som har tagits bort:

    ![Principer](./media/transform-api/final-response1.png)

### <a name="test-the-replaced-url"></a>Testa den ersatta URL

1. Bläddra till APIM-instans.
2. Välj den **API** fliken.
3. Klicka på **Demo konferens API** API-listan.
4. Klicka på den **GetSpeakers** igen.
5. Välj den **Test** fliken.
6. Tryck på **skicka**.

    Som du ser har URL: en ersatts.

    ![Principer](./media/transform-api/final-response2.png)

### <a name="test-the-rate-limit-throttling"></a>Testa gräns för överföringshastigheten (begränsning)

1. Bläddra till APIM-instans.
2. Välj den **API** fliken.
3. Klicka på **Demo konferens API** API-listan.
4. Klicka på den **GetSpeakers** igen.
5. Välj den **Test** fliken.
6. Tryck på **skicka** tre gånger i rad.

    När du skickar en begäran till 3 gånger, **429 för många begäranden** svar.
7. Vänta 15 sekunder eller liknande och tryck på **skicka** igen. Den här gången som du bör få en **200 OK** svar.

    ![Begränsning](./media/transform-api/test-throttling.png)

## <a name="video"></a>Video

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Omvandla en API för att ta bort svarshuvuden
> * Ersätt ursprungliga URL: er i brödtexten för API-svar med APIM gateway URL: er
> * Skydda en API genom att lägga till hastighet gränsen princip (begränsning)
> * Testa omvandlingarna

Gå vidare till nästa kurs:

> [!div class="nextstepaction"]
> [Övervaka ditt API](api-management-howto-use-azure-monitor.md)
