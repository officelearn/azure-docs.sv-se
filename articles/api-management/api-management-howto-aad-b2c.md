---
title: Auktorisera utvecklarkonton med hjälp av Azure Active Directory B2C
titleSuffix: Azure API Management
description: Lär dig hur du auktoriserar användare med hjälp av Azure Active Directory B2C i API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: b8215cd852d54283bfc6bd47e77d7d63ee4e2582
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79475501"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Så här auktoriserar du utvecklarkonton med Azure Active Directory B2C i Azure API Management

## <a name="overview"></a>Översikt

Azure Active Directory B2C är en molnidentitetshanteringslösning för konsumentinriktade webb- och mobilappar. Du kan använda den för att hantera åtkomst till utvecklarportalen. Den här guiden visar den konfiguration som krävs i din API Management-tjänst för att integrera med Azure Active Directory B2C. Information om hur du aktiverar åtkomst till utvecklarportalen med hjälp av klassiska Azure Active Directory finns i [Så här godkänner du utvecklarkonton med Azure Active Directory].

> [!NOTE]
> För att kunna slutföra stegen i den här guiden måste du först ha en Azure Active Directory B2C-klient för att kunna skapa ett program i. Du måste också ha regler för registrering och inloggning redo. Mer information finns i [Azure Active Directory B2C översikt].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Auktorisera utvecklarkonton med hjälp av Azure Active Directory B2C

1. För att komma igång loggar du in på [Azure-portalen](https://portal.azure.com) och letar reda på din API Management-instans.

   > [!NOTE]
   > Om du ännu inte har skapat en API Management-tjänstinstans läser [du Skapa en API Management-tjänstinstans][Create an API Management service instance] i [självstudien Kom igång med Azure API Management][Get started with Azure API Management].

1. Under **identiteter**. Klicka på **+Lägg till** högst upp.

   Fönstret **Lägg till identitetsprovider** visas till höger. Välj **Azure Active Directory B2C**.
    
   ![Lägg till AAD B2C som identitetsprovider][api-management-howto-add-b2c-identity-provider]

1. Kopiera **url:en för omdirigering**.

   ![AAD B2C-identitetsprovider omdirigerar URL][api-management-howto-copy-b2c-identity-provider-redirect-url]

1. På en ny flik öppnar du din Azure Active Directory B2C-klient i Azure-portalen och öppnar **programbladet.**

   ![Registrera en ny ansökan 1][api-management-howto-aad-b2c-portal-menu]

1. Klicka på knappen **Lägg** till om du vill skapa ett nytt Azure Active Directory B2C-program.

   ![Registrera en ny ansökan 2][api-management-howto-aad-b2c-add-button]

1. Ange ett namn på programmet i bladet **Nytt program.** Välj **Ja** under **Webbapp/webb-API**och välj **Ja** under Tillåt **implicit flöde**. Klistra sedan in **url:en för omdirigering** som kopierats i steg 3 till textrutan **Svara på URL.**

   ![Registrera en ny ansökan 3][api-management-howto-aad-b2c-app-details]

1. Om du använder den nya utvecklarportalen (inte den äldre utvecklarportalen) inkluderar du **förnamnet,** **efternamn**och **användarens objekt-ID** i programanspråken.

    ![Programanspråk](./media/api-management-howto-aad-b2c/api-management-application-claims.png)

1. Klicka på knappen **Skapa**. När programmet skapas visas det i **bladet Program.** Klicka på programnamnet för att se dess information.

   ![Registrera en ny ansökan 4][api-management-howto-aad-b2c-app-created]

1. Kopiera **program-ID:t** till Urklipp från bladet **Egenskaper.**

   ![Program-ID 1][api-management-howto-aad-b2c-app-id]

1. Växla tillbaka till fönstret API Management **Lägg till identitetsprovider** och klistra in ID:t i textrutan **Klient-ID.**
    
1.  Växla tillbaka till registreringen av B2C-appen, klicka på knappen **Nycklar** och klicka sedan på **Generera nyckel**. Klicka på **Spara** om du vill spara konfigurationen och visa **apptangenten**. Kopiera nyckeln till Urklipp.

    ![Appnyckel 1][api-management-howto-aad-b2c-app-key]

1.  Växla tillbaka till fönstret API Management **Lägg till identitetsprovider** och klistra in nyckeln i textrutan **Klienthemlighet.**
    
1.  Ange domännamnet för Azure Active Directory B2C-klienten i **Signin-klienten**.

1.  Med fältet **Auktoritet** kan du styra inloggningsadressen för Azure AD B2C som ska användas. Ange värdet till **<your_b2c_tenant_name>.b2clogin.com**.

1. Ange **registreringsprincipen** och **inloggningsprincipen från** B2C-klientprinciperna. Du kan också ange policy för **profilredigering** och **lösenordsåterställning**.

1. När du har angett önskad konfiguration klickar du på **Spara**.

    När ändringarna har sparats kan utvecklare skapa nya konton och logga in på utvecklarportalen med hjälp av Azure Active Directory B2C.

## <a name="developer-portal---add-azure-ad-b2c-account-authentication"></a>Utvecklarportalen – lägg till autentisering av Azure AD B2C-konton

I utvecklarportalen är inloggning med AAD B2C möjlig med **inloggningsknappen: OAuth-widgeten.** Widgeten finns redan på inloggningssidan för standardportalens innehåll för utvecklarportalen.

Även om ett nytt konto skapas automatiskt när en ny användare loggar in med AAD B2C, kan du överväga att lägga till samma widget på registreringssidan.

**Registreringsformuläret: OAuth-widgeten** representerar ett formulär som används för att registrera dig med OAuth.

> [!IMPORTANT]
> Du måste [publicera om portalen](api-management-howto-developer-portal-customize.md#publish) för att AAD-ändringarna ska börja gälla.

## <a name="legacy-developer-portal---how-to-sign-up-with-azure-ad-b2c"></a>Äldre utvecklarportal – så här registrerar du dig med Azure AD B2C

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

1. Om du vill registrera dig för ett utvecklarkonto med Hjälp av Azure Active Directory B2C öppnar du ett nytt webbläsarfönster och går till utvecklarportalen. Klicka på knappen **Registrera dig.**

   ![Utvecklarportal 1][api-management-howto-aad-b2c-dev-portal]

2. Välj att registrera dig med **Azure Active Directory B2C**.

   ![Utvecklarportal 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Du omdirigeras till registreringsprincipen som du har konfigurerat i föregående avsnitt. Välj att registrera dig med din e-postadress eller ett av dina befintliga sociala konton.

   > [!NOTE]
   > Om Azure Active Directory B2C är det enda alternativet som är aktiverat på fliken **Identiteter** i utgivarportalen omdirigeras du direkt till registreringsprincipen.

   ![Utvecklarportalen][api-management-howto-aad-b2c-dev-portal-b2c-options]

   När registreringen är klar omdirigeras du tillbaka till utvecklarportalen. Du är nu inloggad på utvecklarportalen för din API Management-tjänstinstans.

    ![Registreringen är klar][api-management-registration-complete]

## <a name="next-steps"></a>Nästa steg

*  [Översikt över Azure Active Directory B2C]
*  [Azure Active Directory B2C: Utökningsbart principramverk]
*  [Använda ett Microsoft-konto som identitetsprovider i Azure Active Directory B2C]
*  [Använda ett Google-konto som identitetsleverantör i Azure Active Directory B2C]
*  [Använda ett LinkedIn-konto som identitetsprovider i Azure Active Directory B2C]
*  [Använda ett Facebook-konto som identitetsprovider i Azure Active Directory B2C]



[api-management-howto-add-b2c-identity-provider]: ./media/api-management-howto-aad-b2c/api-management-add-b2c-identity-provider.PNG
[api-management-howto-copy-b2c-identity-provider-redirect-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-identity-provider-redirect-url.PNG
[api-management-howto-aad-b2c-portal-menu]: ./media/api-management-howto-aad-b2c/api-management-b2c-portal-menu.PNG
[api-management-howto-aad-b2c-add-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-add-button.PNG
[api-management-howto-aad-b2c-app-details]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-details.PNG
[api-management-howto-aad-b2c-app-created]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-created.PNG
[api-management-howto-aad-b2c-app-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-id.PNG
[api-management-howto-aad-b2c-client-id]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-id.PNG
[api-management-howto-aad-b2c-app-key]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key.PNG
[api-management-howto-aad-b2c-app-key-saved]: ./media/api-management-howto-aad-b2c/api-management-b2c-app-key-saved.PNG
[api-management-howto-aad-b2c-client-secret]: ./media/api-management-howto-aad-b2c/api-management-b2c-client-secret.PNG
[api-management-howto-aad-b2c-allowed-tenant]: ./media/api-management-howto-aad-b2c/api-management-b2c-allowed-tenant.PNG
[api-management-howto-aad-b2c-policies]: ./media/api-management-howto-aad-b2c/api-management-b2c-policies.PNG
[api-management-howto-aad-b2c-dev-portal]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-button]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-button.PNG
[api-management-howto-aad-b2c-dev-portal-b2c-options]: ./media/api-management-howto-aad-b2c/api-management-b2c-dev-portal-b2c-options.PNG
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.PNG
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-b2c-security-tab.png
[api-management-security-aad-new]: ./media/api-management-howto-aad/api-management-security-aad-new.png
[api-management-new-aad-application-menu]: ./media/api-management-howto-aad/api-management-new-aad-application-menu.png
[api-management-new-aad-application-1]: ./media/api-management-howto-aad/api-management-new-aad-application-1.png
[api-management-new-aad-application-2]: ./media/api-management-howto-aad/api-management-new-aad-application-2.png
[api-management-new-aad-app-created]: ./media/api-management-howto-aad/api-management-new-aad-app-created.png
[api-management-aad-app-permissions]: ./media/api-management-howto-aad/api-management-aad-app-permissions.png
[api-management-aad-app-client-id]: ./media/api-management-howto-aad/api-management-aad-app-client-id.png
[api-management-client-id]: ./media/api-management-howto-aad/api-management-client-id.png
[api-management-aad-key-before-save]: ./media/api-management-howto-aad/api-management-aad-key-before-save.png
[api-management-aad-key-after-save]: ./media/api-management-howto-aad/api-management-aad-key-after-save.png
[api-management-client-secret]: ./media/api-management-howto-aad/api-management-client-secret.png
[api-management-client-allowed-tenants]: ./media/api-management-howto-aad/api-management-client-allowed-tenants.png
[api-management-client-allowed-tenants-save]: ./media/api-management-howto-aad/api-management-client-allowed-tenants-save.png
[api-management-aad-delegated-permissions]: ./media/api-management-howto-aad/api-management-aad-delegated-permissions.png
[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-permissions-form]: ./media/api-management-howto-aad/api-management-permissions-form.png
[api-management-configure-product]: ./media/api-management-howto-aad/api-management-configure-product.png
[api-management-add-groups]: ./media/api-management-howto-aad/api-management-add-groups.png
[api-management-select-group]: ./media/api-management-howto-aad/api-management-select-group.png
[api-management-aad-groups-list]: ./media/api-management-howto-aad/api-management-aad-groups-list.png
[api-management-aad-group-added]: ./media/api-management-howto-aad/api-management-aad-group-added.png
[api-management-groups]: ./media/api-management-howto-aad/api-management-groups.png
[api-management-edit-group]: ./media/api-management-howto-aad/api-management-edit-group.png

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
[Översikt över Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Så här godkänner du utvecklarkonton med Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Utökningsbart principramverk]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Använda ett Microsoft-konto som identitetsprovider i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Använda ett Google-konto som identitetsleverantör i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Använda ett Facebook-konto som identitetsprovider i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Använda ett LinkedIn-konto som identitetsprovider i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
