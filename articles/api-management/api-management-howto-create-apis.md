---
title: 'Hur du skapar API: er i Azure API Management'
description: "Lär dig hur du skapar och konfigurerar API: er i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 14c20da4-f29f-4b28-bec7-3d4c50b734da
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 79630fb8998d47aab8eed38daf7d5fcef3d870d7
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2017
---
# <a name="how-to-create-apis-in-azure-api-management"></a>Hur du skapar API: er i Azure API Management
En API i API Management representerar en uppsättning åtgärder som kan anropas av klientprogram. Nya API: er skapas i portalen publisher och sedan önskad åtgärderna har lagts till. När åtgärderna har lagts till API: et har lagts till i en produkt och kan publiceras. När en API som har publicerats kan den prenumererar på och användas av utvecklare.

Den här guiden visar det första steget i processen: hur du skapar och konfigurerar ett nytt API i API-hantering. Mer information om att lägga till åtgärder och publicering av en produkt finns [lägga till åtgärder i en API] [ How to add operations to an API] och [hur du skapar och publicerar en produkt][How to create and publish a product].

## <a name="create-new-api"></a>Skapa ett nytt API
API: er skapas och konfigureras i publisher-portalen. För att komma åt publisher-portalen klickar du på **Publisher portal** i Azure Portal för API Management-tjänsten.

![Utgivarportalen][api-management-management-console]

> Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][Create an API Management service instance] i självstudiekursen [Komma igång med Azure API Management][Get started with Azure API Management].
> 
> 

Klicka på **API: er** från den **API Management** menyn till vänster och klicka sedan på **lägga till API**.

![Skapa API][api-management-create-api]

Använd den **lägga till nya API** fönster för att konfigurera nya API: et.

![Lägga till ett nytt API][api-management-add-new-api]

Följande fält används för att konfigurera nya API: et.

* **Webb-API-namnet** ger ett unikt och beskrivande namn för API: et. Den visas i portaler utvecklare och utgivare.
* **Webbtjänstens URL** refererar till HTTP-tjänsten implementerar API: et. API-hantering vidarebefordrar begäranden till den här adressen.
* **Web API-URL-suffix** läggs till en bas-URL för API management-tjänsten. Bas som URL: en är gemensamma för alla API: er som en instans för API Management-tjänsten är värd för. API Management särskiljer API: er av deras suffix och därmed suffixet måste vara unikt för alla API: et för en viss utgivare.
* **Web API-URL-schema** avgör vilka protokoll som kan användas för åtkomst till API. HTTPs har angetts som standard.
* Du kan också lägga till detta nya Programmeringsgränssnitt för en produkt, klickar du på den **produkter (valfritt)** listrutan och väljer en produkt. Det här steget kan upprepas flera gånger för att lägga till API: et på flera produkter.

När önskade värden har konfigurerats, klickar du på **spara**. När du har skapat den nya API visas sidan Sammanfattning för API i publisher-portalen.

![API-sammanfattning][api-management-api-summary]

## <a name="configure-api-settings"></a>Konfigurera API-inställningar
Du kan använda den **inställningar** att kontrollera och redigera konfigurationen för en API. **Webb-API-namnet**, **-webbtjänstens URL**, och **URL för Web API-suffix** från början anges när API: N skapas och kan ändras här. **Beskrivning** ger en valfri beskrivning och **Web API-URL-schema** avgör vilka protokoll som kan användas för åtkomst till API.

![API-inställningar][api-management-api-settings]

För att konfigurera gateway-autentisering för backend-tjänsten implementerar API: et, Välj den **säkerhet** fliken. Den **med autentiseringsuppgifter** listrutan kan användas för att konfigurera **HTTP basic** eller **klientcertifikat** autentisering. Om du vill använda grundläggande HTTP-autentisering anger du bara de önskade autentiseringsuppgifterna. Information om hur du använder certifikat för klientautentisering finns [säkra backend-tjänster som använder klienten certifikatautentisering i Azure API Management][How to secure back-end services using client certificate authentication in Azure API Management].

Den **säkerhet** fliken kan också användas för att konfigurera **användarautentiseringen** med hjälp av OAuth 2.0. Mer information finns i [så att auktorisera developer konton med hjälp av OAuth 2.0 i Azure API Management][How to authorize developer accounts using OAuth 2.0 in Azure API Management].

![Inställningar för grundläggande autentisering][api-management-api-settings-credentials]

Klicka på **spara** att spara ändringar som görs till API-inställningar.

## <a name="next-steps"> </a>Nästa steg
När en API som har skapats och de konfigurerade inställningarna nästa steg är att lägga till åtgärder i API: et, Lägg till API: et för en produkt och publicera den så att den är tillgänglig för utvecklare. Mer information finns i följande artiklar.

* [Lägga till åtgärder i en API][How to add operations to an API]
* [Hur du skapar och publicerar en produkt][How to create and publish a product]

[api-management-create-api]: ./media/api-management-howto-create-apis/api-management-create-api.png
[api-management-management-console]: ./media/api-management-howto-create-apis/api-management-management-console.png
[api-management-add-new-api]: ./media/api-management-howto-create-apis/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-create-apis/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-create-apis/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-create-apis/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-create-apis/api-management-echo-operations.png

[What is an API?]: #what-is-api
[Create a new API]: #create-new-api
[Configure API settings]: #configure-api-settings
[Configure API operations]: #configure-api-operations
[Next steps]: #next-steps

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[How to secure back-end services using client certificate authentication in Azure API Management]: api-management-howto-mutual-certificates.md
[How to authorize developer accounts using OAuth 2.0 in Azure API Management]: api-management-howto-oauth2.md
