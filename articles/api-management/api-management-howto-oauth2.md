---
title: Auktorisera konton med hjälp av OAuth 2.0 i Azure API Management | Microsoft Docs
description: Lär dig mer om att auktorisera användare som använder OAuth 2.0 i API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2018
ms.author: apimpm
ms.openlocfilehash: cd022677da59588eca910a508b2596aa513eff4b
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44302817"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Hur du auktorisera konton med hjälp av OAuth 2.0 i Azure API Management

Många API: er stöder [OAuth 2.0](http://oauth.net/2/) att skydda API: et och se till att endast giltiga användare har åtkomst och de kan bara komma åt resurser som de är rätt. För att kunna använda Azure API Management interaktiva Developer Console med dessa API: er, kan du konfigurera din tjänstinstans för att fungera med din OAuth 2.0 aktiverade API: et i tjänsten.

## <a name="prerequisites"> </a>Nödvändiga komponenter

Den här guiden visar hur du konfigurerar din API Management-tjänstinstans för att använda OAuth 2.0-auktorisering för konton, men visar inte hur du konfigurerar en OAuth 2.0-provider. Konfigurationen för varje OAuth 2.0-provider är olika, men stegen är liknande och de nödvändiga uppgifter används konfigurerar OAuth 2.0 i din API Management-tjänstinstans är desamma. Det här avsnittet visas exempel med Azure Active Directory som en OAuth 2.0-provider.

> [!NOTE]
> Mer information om hur du konfigurerar OAuth 2.0 genom att använda Azure Active Directory finns i den [WebApp-GraphAPI-DotNet] [ WebApp-GraphAPI-DotNet] exemplet.

## <a name="step1"> </a>Konfigurera en server för OAuth 2.0-auktorisering i API Management

> [!NOTE]
> Om du inte har skapat en API Management-tjänstinstans, se [skapa en API Management-tjänstinstans][Create an API Management service instance].

1. Klicka på fliken OAuth 2.0 i menyn till vänster och klicka på **+ Lägg till**.

    ![OAuth 2.0-menyn](./media/api-management-howto-oauth2/oauth-01.png)

2. Ange ett namn och en valfri beskrivning i den **namn** och **beskrivning** fält.

    > [!NOTE]
    > De här fälten används för att identifiera OAuth 2.0-auktoriseringsservern inom den aktuella API Management-tjänstinstansen och deras värden kommer inte från OAuth 2.0-server.

3. Ange den **klientregistreringssidans URL**. Den här sidan är där användare kan skapa och hantera sina konton, och varierar beroende på OAuth 2.0-providern som används. Den **klientregistreringssidans URL** pekar på den sida som användare kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-leverantörer som har stöd för hantering av konton. Vissa organisationer inte konfigurera eller använda den här funktionen även om OAuth 2.0-provider stöder den. Om OAuth 2.0-providern inte har hantering av konton som konfigurerats av användare, ange en URL-platshållaren här till exempel URL: en för ditt företag eller en URL som `https://placeholder.contoso.com`.

    ![Ny OAuth 2.0-server](./media/api-management-howto-oauth2/oauth-02.png)

4. Nästa avsnitt i formuläret innehåller den **auktoriseringskod beviljandetyper**, **URL för Auktoriseringsslutpunkt**, och **metod för begäran om auktorisering** inställningar.

    Ange den **auktoriseringskod beviljandetyper** genom att markera de önskade typerna. **Auktoriseringskod** har angetts som standard.

    Ange den **URL för Auktoriseringsslutpunkt**. Den här URL: en för Azure Active Directory, ska vara detsamma som följande URL, där `<client_id>` ersätts med klient-id som identifierar ditt program till OAuth 2.0-servern.

    `https://login.microsoftonline.com/<client_id>/oauth2/authorize`

    Den **metod för begäran om auktorisering** anger hur begäran om godkännande skickas till OAuth 2.0-server. Som standard **hämta** har valts.

5. Sedan **Token slutpunkts-URL**, **klientautentiseringsmetoder**, **åtkomsttoken som metod för att skicka** och **standardomfattning** måste vara Ange.

    ![Ny OAuth 2.0-server](./media/api-management-howto-oauth2/oauth-03.png)

    För en Azure Active Directory OAuth 2.0-server i **Token slutpunkts-URL** har följande format, där `<TenantID>` har formatet `yourapp.onmicrosoft.com`.

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Standardinställningen för **klientautentiseringsmetoder** är **grundläggande**, och **åtkomsttoken som metod för att skicka** är **auktoriseringsrubrik**. Dessa värden konfigureras på det här avsnittet av formuläret, tillsammans med den **standardomfattning**.

6. Den **klientautentiseringsuppgifter** avsnittet innehåller den **klient-ID** och **klienthemlighet**, som erhålls under skapande och konfiguration av OAuth 2.0-server . När den **klient-ID** och **klienthemlighet** anges, den **redirect_uri** för den **auktoriseringskod** genereras. URI: N används för att konfigurera svars-URL i OAuth 2.0-serverkonfiguration.

    ![Ny OAuth 2.0-server](./media/api-management-howto-oauth2/oauth-04.png)

    Om **auktoriseringskod beviljandetyper** är inställd på **resursägarens lösenord**, **lösenordsinformation för resurs-ägare** används för att ange autentiseringsuppgifterna; Du kan lämna den annars tom.

    När formuläret är klar klickar du på **skapa** att spara serverkonfiguration för API Management OAuth 2.0-auktorisering. När serverkonfigurationen har sparats kan konfigurera du API: er för att använda den här konfigurationen som du ser i nästa avsnitt.

## <a name="step2"> </a>Konfigurera ett API för att använda OAuth 2.0-användarautentisering

1. Klicka på **API: er** från den **API Management** på vänstra menyn.

    ![OAuth 2.0-API: er](./media/api-management-howto-oauth2/oauth-05.png)

2. Klicka på namnet på den önskade API och klicka på **inställningar**. Bläddra till den **Security** , och sedan markera kryssrutan för **OAuth 2.0**.

    ![OAuth 2.0-inställningar](./media/api-management-howto-oauth2/oauth-06.png)

3. Välj önskat **auktoriseringsservern** i listrutan och klickar på **spara**.

    ![OAuth 2.0-inställningar](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="step3"> </a>Testa OAuth 2.0-användarautentisering i Developer-portalen

När du har konfigurerat din server för OAuth 2.0-auktorisering och konfigurerat ditt API för att använda den här servern, kan du testa den genom att gå in på Utvecklarportalen och anropa ett API.  Klicka på **utvecklarportalen** på den översta menyn från din Azure API Management-instans **översikt** sidan.

![Utvecklarportalen][api-management-developer-portal-menu]

Klicka på **API: er** i den översta menyn och välj **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Om du bara har ett API som är konfigurerat eller som visas för ditt konto och du klickar på API:er så kommer du direkt till åtgärderna för det API:et.

Välj den **GET Resource** åtgärden, klickar du på **öppna konsolen**, och välj sedan **auktoriseringskod** från listrutan.

![Öppna konsolen][api-management-open-console]

När **auktoriseringskod** är markerat visas ett popup-fönster med inloggningsformuläret av OAuth 2.0-providern. I det här exemplet tillhandahålls i inloggningsformuläret av Azure Active Directory.

> [!NOTE]
> Om du har inaktiverat popup-fönster uppmanas du att aktivera dem med webbläsaren. När du har aktiverat dem, Välj **auktoriseringskod** igen och kommer att visas i inloggningsformuläret.

![Logga in][api-management-oauth2-signin]

När du har loggat in, den **begärandehuvuden** fylls med en `Authorization : Bearer` -huvud som godkänner begäran.

![Begära huvud-token][api-management-request-header-token]

Du kan nu konfigurera önskade värden för återstående parametrar och skicka begäran.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder OAuth 2.0 och API Management finns i följande video- och tillhörande [artikeln](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
[api-management-developer-portal-menu]: ./media/api-management-howto-oauth2/api-management-developer-portal-menu.png
[api-management-open-console]: ./media/api-management-howto-oauth2/api-management-open-console.png
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

