---
title: Auktorisera developer konton med hjälp av OAuth 2.0 i Azure API Management | Microsoft Docs
description: Lär dig mer om att auktorisera användare som använder OAuth 2.0 i API-hantering.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: f3611fa4da571dd74d844c7fad45788ece372be4
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Så här auktoriserar developer konton med hjälp av OAuth 2.0 i Azure API Management
Stöd för många API: er [OAuth 2.0](http://oauth.net/2/) att skydda API: et och se till att endast giltigt användare har åtkomst och de kan bara komma åt resurser som de är rätt. För att kunna använda Azure API Management interaktiva Utvecklarkonsolen med dessa API: er, kan tjänsten du konfigurera service-instans för att fungera med din OAuth 2.0 aktiverade API.

## <a name="prerequisites"> </a>Nödvändiga komponenter
Den här guiden visar hur du konfigurerar API Management service-instans för att använda OAuth 2.0-auktorisering för utvecklare konton, men visar inte hur du konfigurerar en OAuth 2.0-provider. Konfigurationen för varje OAuth 2.0-provider är olika, men stegen är liknande och nödvändiga typer av information som används när du konfigurerar OAuth 2.0 i din API Management service-instans är samma. Det här avsnittet visas exempel som använder Azure Active Directory som en OAuth 2.0-provider.

> [!NOTE]
> Mer information om hur du konfigurerar OAuth 2.0 med Azure Active Directory finns i [WebApp-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] exempel.
> 
> 

## <a name="step1"> </a>Konfigurera en server för OAuth 2.0-auktorisering i API Management
Börja genom att klicka på **Publisher-portal** på Azure Portal för API Management-tjänsten.

![Utgivarportalen][api-management-management-console]

> [!NOTE]
> Om du inte har skapat en instans för API Management-tjänsten finns [skapa en instans för API Management-tjänsten][Create an API Management service instance].
> 
> 

Klicka på **säkerhet** från den **API Management** menyn till vänster, klickar på **OAuth 2.0**, och klicka sedan på **Lägg till auktorisering server**.

![OAuth 2.0][api-management-oauth2]

När du klickar på **Lägg till auktorisering server**, nya auktorisering server formuläret visas.

![Ny server][api-management-oauth2-server-1]

Ange ett namn och en valfri beskrivning i den **namn** och **beskrivning** fält. 

> [!NOTE]
> Fälten som används för att identifiera OAuth 2.0 auktorisering server inom den aktuella tjänstinstansen för API Management och deras värden kommer inte från OAuth 2.0-server.
> 
> 

Ange den **klienten URL för registrering**. Den här sidan är där användare kan skapa och hantera sina konton och varierar beroende på OAuth 2.0-providern som används. Den **klienten URL för registrering** pekar på den sida som användare kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-leverantörer som stöder Användarhantering av konton. Vissa organisationer konfigurera eller använda den här funktionen även om OAuth 2.0-providern stöds inte. Om OAuth 2.0-providern inte har användarhantering för konton som har konfigurerats, ange en platshållare URL här som URL för ditt företag eller en URL som `https://placeholder.contoso.com`.

Nästa avsnitt i formuläret innehåller den **Auktoriseringskoden bevilja typer**, **slutpunkts-URL-auktorisering**, och **begäran auktoriseringsmetod** inställningar.

![Ny server][api-management-oauth2-server-2]

Ange den **Auktoriseringskoden bevilja typer** genom att markera de önskade typerna. **Auktoriseringskoden** har angetts som standard.

Ange den **slutpunkts-URL-auktorisering**. URL: en för Azure Active Directory, kommer att likna följande URL, där `<client_id>` ersätts med klient-id som identifierar ditt program till OAuth 2.0-servern.

`https://login.microsoftonline.com/<client_id>/oauth2/authorize`

Den **begäran auktoriseringsmetod** anger hur auktoriseringsbegäran skickas till OAuth 2.0-server. Som standard **hämta** är markerad.

I nästa avsnitt är var den **Token slutpunkts-URL**, **klienten autentiseringsmetoder**, **åtkomsttoken skickar metoden**, och **standard scope**har angetts.

![Ny server][api-management-oauth2-server-3]

För en Azure Active Directory OAuth 2.0-server i **Token slutpunkts-URL** har följande format, där `<APPID>` har formatet för `yourapp.onmicrosoft.com`.

`https://login.microsoftonline.com/<APPID>/oauth2/token`

Standardinställningen för **klienten autentiseringsmetoder** är **grundläggande**, och **åtkomsttoken skickar metoden** är **Authorization-huvud**. Dessa värden är konfigurerade på det här avsnittet av formuläret, tillsammans med den **standard scope**.

Den **klientautentiseringsuppgifterna** avsnittet innehåller den **klient-ID** och **klienthemlighet**, som erhålls under skapande och konfiguration av OAuth 2.0-server . En gång i **klient-ID** och **klienthemlighet** har angetts i **redirect_uri** för den **Auktoriseringskoden** genereras. URI: N används för att konfigurera reply-URL i serverkonfigurationen OAuth 2.0.

![Ny server][api-management-oauth2-server-4]

Om **Auktoriseringskoden bevilja typer** är inställd på **resurs ägarlösenord**, **lösenordsinformation för resurs-ägare** avsnitt används för att ange autentiseringsuppgifterna; Annars kan du lämna det tomt.

![Ny server][api-management-oauth2-server-5]

När formuläret är klar klickar du på **spara** spara serverkonfiguration för API Management OAuth 2.0-auktorisering. När serverkonfigurationen sparas kan konfigurera du API: er för att använda den här konfigurationen som visas i nästa avsnitt.

## <a name="step2"> </a>Konfigurera en API för att använda OAuth 2.0 användarautentiseringen
Klicka på **API: er** från den **API Management** Klicka på menyn till vänster, klickar på namnet på den önskade API **säkerhet**, och sedan markera kryssrutan för **OAuth 2.0**.

![Auktoriseringen av användaren][api-management-user-authorization]

Välj den önskade **auktorisering server** i listrutan och klickar på **spara**.

![Auktoriseringen av användaren][api-management-user-authorization-save]

## <a name="step3"> </a>Testa användarautentiseringen OAuth 2.0 i Developer-portalen
När du har konfigurerat OAuth 2.0 auktorisering servern och konfigurerat ditt API för att använda den servern kan testa du den genom att gå till Developer-portalen och anropar en API.  Klicka på **Utvecklarportal** på menyn längst upp till höger.

![Utvecklarportalen][api-management-developer-portal-menu]

Klicka på **API: er** i den översta menyn och välj **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Om du bara har ett API som är konfigurerat eller som visas för ditt konto och du klickar på API:er så kommer du direkt till åtgärderna för det API:et.
> 
> 

Välj den **hämta resurs** åtgärden, klickar du på **öppna konsolen**, och välj sedan **Auktoriseringskoden** från listrutan.

![Öppna konsolen][api-management-open-console]

När **Auktoriseringskoden** är markerat visas ett popup-fönster med formuläret inloggning av OAuth 2.0-providern. I det här exemplet tillhandahålls formuläret inloggning av Azure Active Directory.

> [!NOTE]
> Om du har inaktiverat popup-fönster uppmanas du att aktivera dem i webbläsaren. När du har aktiverat dem, Välj **Auktoriseringskoden** igen och logga in formuläret visas.
> 
> 

![Logga in][api-management-oauth2-signin]

När du har loggat in, den **Begäransrubriker** fylls i med en `Authorization : Bearer` huvud som godkänner begäran.

![Token för anslutningsbegäran sidhuvud][api-management-request-header-token]

Du kan nu konfigurera önskade värden för parametrarna återstående och skicka din begäran. 

## <a name="next-steps"></a>Nästa steg
Mer information om att använda OAuth 2.0 och API-hantering finns i följande video och tillhörande [artikel](api-management-howto-protect-backend-with-aad.md).


[api-management-management-console]: ./media/api-management-howto-oauth2/api-management-management-console.png
[api-management-oauth2]: ./media/api-management-howto-oauth2/api-management-oauth2.png
[api-management-user-authorization]: ./media/api-management-howto-oauth2/api-management-user-authorization.png
[api-management-user-authorization-save]: ./media/api-management-howto-oauth2/api-management-user-authorization-save.png
[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
[api-management-oauth2-server-1]: ./media/api-management-howto-oauth2/api-management-oauth2-server-1.png
[api-management-oauth2-server-2]: ./media/api-management-howto-oauth2/api-management-oauth2-server-2.png
[api-management-oauth2-server-3]: ./media/api-management-howto-oauth2/api-management-oauth2-server-3.png
[api-management-oauth2-server-4]: ./media/api-management-howto-oauth2/api-management-oauth2-server-4.png
[api-management-oauth2-server-5]: ./media/api-management-howto-oauth2/api-management-oauth2-server-5.png
[api-management-apis-echo-api]: ./media/api-management-howto-oauth2/api-management-apis-echo-api.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Get started with Azure API Management]: get-started-create-service-instance.md
[API Management policy reference]: api-management-policy-reference.md
[Caching policies]: api-management-policy-reference.md#caching-policies
[Create an API Management service instance]: get-started-create-service-instance.md

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

