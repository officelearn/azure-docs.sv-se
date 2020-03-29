---
title: Auktorisera utvecklarkonton med OAuth 2.0 i API Management
titleSuffix: Azure API Management
description: Läs om hur du auktoriserar användare med OAuth 2.0 i API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 90c890925378c30ce5688d2713990b4b2cdd20c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430678"
---
# <a name="how-to-authorize-developer-accounts-using-oauth-20-in-azure-api-management"></a>Så här godkänner du utvecklarkonton med OAuth 2.0 i Azure API Management

Många API:er stöder [OAuth 2.0](https://oauth.net/2/) för att skydda API:et och se till att endast giltiga användare har åtkomst, och de kan bara komma åt resurser som de har rätt till. För att kunna använda Azure API Managements interaktiva utvecklarkonsol med sådana API:er kan du med tjänsten konfigurera din tjänstinstans så att den fungerar med det OAuth 2.0-aktiverade API:et.

> [!IMPORTANT]
> OAuth 2.0-auktorisering är ännu inte tillgänglig i den interaktiva konsolen i den nya utvecklarportalen.

## <a name="prerequisites"></a><a name="prerequisites"> </a>Krav

Den här guiden visar hur du konfigurerar tjänstinstansen för API Management så att OAuth 2.0-auktoriseringen för utvecklarkonton används, men du får inte hur du konfigurerar en OAuth 2.0-provider. Konfigurationen för varje OAuth 2.0-provider är olika, även om stegen är likartade, och den information som krävs för att konfigurera OAuth 2.0 i api management-tjänstinstansen är desamma. Det här avsnittet visar exempel med Azure Active Directory som OAuth 2.0-provider.

> [!NOTE]
> Mer information om hur du konfigurerar OAuth 2.0 med Azure Active Directory finns i exemplet [på WebApp-GraphAPI-DotNet.][WebApp-GraphAPI-DotNet]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="configure-an-oauth-20-authorization-server-in-api-management"></a><a name="step1"> </a>Konfigurera en OAuth 2.0-auktoriseringsserver i API Management

> [!NOTE]
> Om du ännu inte har skapat en API Management-tjänstinstans läser du [Skapa en API Management-tjänstinstans][Create an API Management service instance].

1. Klicka på fliken OAuth 2.0 i menyn till vänster och klicka på **+Lägg till**.

    ![OAuth 2.0-menyn](./media/api-management-howto-oauth2/oauth-01.png)

2. Ange ett namn och en valfri beskrivning i fälten **Namn** och **Beskrivning.**

    > [!NOTE]
    > Dessa fält används för att identifiera OAuth 2.0-auktoriseringsservern i den aktuella API Management-tjänstinstansen och deras värden kommer inte från OAuth 2.0-servern.

3. Ange **URL:en**för sidan För klientregistrering . Den här sidan är där användare kan skapa och hantera sina konton och varierar beroende på vilken OAuth 2.0-provider som används. **Url:en för klientregistreringssidan** pekar på sidan som användare kan använda för att skapa och konfigurera sina egna konton för OAuth 2.0-leverantörer som stöder användarhantering av konton. Vissa organisationer konfigurerar eller använder inte den här funktionen även om OAuth 2.0-providern stöder den. Om OAuth 2.0-leverantören inte har konfigurerat användarhantering av konton anger du en platshållaradress här, till exempel företagets URL eller en URL som `https://placeholder.contoso.com`.

    ![OAuth 2.0 ny server](./media/api-management-howto-oauth2/oauth-02.png)

4. Nästa avsnitt i formuläret innehåller **inställningarna för auktoriseringsbidrag,** **URL för auktoriseringsslutpunkt**och **metodinställningar för auktoriseringsbegäran.**

    Ange **typerna för auktoriseringsbidrag** genom att kontrollera önskade typer. **Auktoriseringskod** anges som standard.

    Ange **URL:en för auktoriseringsslutpunkt**. För Azure Active Directory liknar den här URL:en följande URL, där `<tenant_id>` ersätts med ID:t för din Azure AD-klientorganisation.

    `https://login.microsoftonline.com/<tenant_id>/oauth2/authorize`

    **Metoden auktoriseringsbegäran** anger hur auktoriseringsbegäran skickas till OAuth 2.0-servern. Som standard **ÄR GET** markerat.

5. Sedan måste **tokenslutpunkts-URL**, **klientautentiseringsmetoder**, **Åtkomsttoken skicka metod** och **Standardomfång** anges.

    ![OAuth 2.0 ny server](./media/api-management-howto-oauth2/oauth-03.png)

    För en Azure Active Directory OAuth 2.0-server har **url:en tokenslutpunkt** följande format, där `<TenantID>` formatet . `yourapp.onmicrosoft.com`

    `https://login.microsoftonline.com/<TenantID>/oauth2/token`

    Standardinställningen för **klientautentiseringsmetoder** är **Grundläggande**och **åtkomsttokens sändningsmetod** är **Auktoriseringshuvud**. Dessa värden konfigureras i det här avsnittet i formuläret, tillsammans med **standardomfånget**.

6. Avsnittet **Klientautentiseringsuppgifter** innehåller **klient-ID** och **klienthemlighet**, som erhålls under skapandet och konfigurationsprocessen för din OAuth 2.0-server. När **klient-ID** och **klienthemlighet** har angetts genereras **redirect_uri** för **auktoriseringskoden.** Den här URI:n används för att konfigurera svars-URL:en i OAuth 2.0-serverkonfigurationen.

    ![OAuth 2.0 ny server](./media/api-management-howto-oauth2/oauth-04.png)

    Om **tilldelningsbidragstyper** är inställt på **Resursägarelösenord**används avsnittet **Autentiseringsuppgifter** för resursägare för att ange dessa autentiseringsuppgifter. Annars kan du lämna den tom.

    När formuläret är klart klickar du på **Skapa** för att spara API Management OAuth 2.0-auktoriseringsserverkonfigurationen. När serverkonfigurationen har sparats kan du konfigurera API:er så att de använder den här konfigurationen, som visas i nästa avsnitt.

## <a name="configure-an-api-to-use-oauth-20-user-authorization"></a><a name="step2"> </a>Konfigurera ett API för att använda OAuth 2.0-användarauktorisering

1. Klicka på **API:er** från **API Management-menyn** till vänster.

    ![OAuth 2.0 API:er](./media/api-management-howto-oauth2/oauth-05.png)

2. Klicka på namnet på önskat API och klicka på **Inställningar**. Bläddra till avsnittet **Säkerhet** och markera sedan kryssrutan för **OAuth 2.0**.

    ![OAuth 2.0-inställningar](./media/api-management-howto-oauth2/oauth-06.png)

3. Välj önskad **auktoriseringsserver** i listrutan och klicka på **Spara**.

    ![OAuth 2.0-inställningar](./media/api-management-howto-oauth2/oauth-07.png)

## <a name="legacy-developer-portal---test-the-oauth-20-user-authorization"></a><a name="step3"> </a>Äldre utvecklarportal – testa användarauktoriseringen av OAuth 2.0

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

När du har konfigurerat Auktoriseringsservern OAuth 2.0 och konfigurerat ditt API för att använda den servern kan du testa den genom att gå till utvecklarportalen och anropa ett API. Klicka på **utvecklarportal (äldre)** på den övre menyn från sidan **översiktssida** för Azure API Management-instans.

Klicka **på API:er** i den övre menyn och välj **Echo API**.

![Echo API][api-management-apis-echo-api]

> [!NOTE]
> Om du bara har ett API som är konfigurerat eller som visas för ditt konto och du klickar på API:er så kommer du direkt till åtgärderna för det API:et.

Välj åtgärden **GET-resurs,** klicka på **Öppna konsol**och välj sedan **Auktoriseringskod** i listrutan.

![Öppna konsolen][api-management-open-console]

När **Auktoriseringskod** är markerad visas ett popup-fönster med inloggningsformen för OAuth 2.0-providern. I det här exemplet tillhandahålls inloggningsformuläret av Azure Active Directory.

> [!NOTE]
> Om du har inaktiverat popup-fönster uppmanas du att aktivera dem av webbläsaren. När du har aktiverat dem väljer du **Auktoriseringskod** igen och inloggningsformuläret visas.

![Logga in][api-management-oauth2-signin]

När du har loggat in **fylls beställda rubriker** med ett `Authorization : Bearer` huvud som auktoriserar begäran.

![Begäran sidhuvudtoken][api-management-request-header-token]

Nu kan du konfigurera önskade värden för de återstående parametrarna och skicka begäran.

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder OAuth 2.0 och API Management finns i följande video och medföljande [artikel](api-management-howto-protect-backend-with-aad.md).

[api-management-oauth2-signin]: ./media/api-management-howto-oauth2/api-management-oauth2-signin.png
[api-management-request-header-token]: ./media/api-management-howto-oauth2/api-management-request-header-token.png
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

[https://oauth.net/2/]: https://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

