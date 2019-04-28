---
title: Auktorisera konton med hjälp av Azure Active Directory B2C – Azure API Management | Microsoft Docs
description: Lär dig mer om att auktorisera användare med hjälp av Azure Active Directory B2C i API Management.
services: api-management
documentationcenter: API Management
author: miaojiang
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 644cc2a4175043b523d53b39f17483c6f3acfe96
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558484"
---
# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Hur du auktorisera konton med hjälp av Azure Active Directory B2C i Azure API Management

## <a name="overview"></a>Översikt

Azure Active Directory B2C är en identitetshanteringslösning för molnet för konsumentinriktade webb- och mobilprogram. Du kan använda den för att hantera åtkomst till din developer-portalen. Den här guiden visar konfigurationen som krävs i API Management-tjänsten att integrera med Azure Active Directory B2C. Information om hur du aktiverar åtkomst till utvecklarportalen genom att använda klassiska Azure Active Directory finns i [Hur du auktorisera konton med Azure Active Directory].

> [!NOTE]
> Du måste ha en Azure Active Directory B2C-klient för att skapa ett program i för att slutföra stegen i den här guiden. Du måste dessutom ha registrering och inloggning från principer som är redo. Mer information finns i [översikt över Azure Active Directory B2C].

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Auktorisera konton med hjälp av Azure Active Directory B2C

1. För att komma igång, logga in på den [Azure-portalen](https://portal.azure.com) och leta upp din API Management-instans.

   > [!NOTE]
   > Om du inte har skapat en API Management-tjänstinstans, se [skapa en API Management-tjänstinstans] [ Create an API Management service instance] i den [Kom igång med Azure API Management självstudien] [Get started with Azure API Management].

2. Under **identiteter**. Klicka på **+ Lägg till** högst upp.

   Den **Lägg till identitetsprovider** visas i fönstret till höger. Välj **Azure Active Directory B2C**.
    
   ![Lägg till AAD B2C som identitetsprovider][api-management-howto-add-b2c-identity-provider]

3. Kopiera den **omdirigerings-URL**.

   ![Omdirigerings-URL för AAD B2C identitetsprovider][api-management-howto-copy-b2c-identity-provider-redirect-url]

4. I en ny flik, att få åtkomst till din Azure Active Directory B2C-klient i Azure-portalen och öppna den **program** bladet.

   ![Registrera ett nytt program 1][api-management-howto-aad-b2c-portal-menu]

5. Klicka på den **Lägg till** för att skapa ett nytt Azure Active Directory B2C-program.

   ![Registrera ett nytt program 2][api-management-howto-aad-b2c-add-button]

6. I den **nytt program** bladet anger du ett namn för programmet. Välj **Ja** under **Web App/Web API**, och välj **Ja** under **Tillåt implicit flöde**. Klistra in den **omdirigerings-URL** kopierade i steg 3 i den **svars-URL** textrutan.

   ![Registrera ett nytt program 3][api-management-howto-aad-b2c-app-details]

7. Klicka på knappen **Skapa**. När programmet har skapats visas den i den **program** bladet. Klicka på namnet på programmet för att visa information om.

   ![Registrera ett nytt program 4][api-management-howto-aad-b2c-app-created]

8. Från den **egenskaper** bladet, kopiera den **program-ID** till Urklipp.

   ![Program-ID 1][api-management-howto-aad-b2c-app-id]

9. Gå tillbaka till API Management **Lägg till identitetsprovider** fönstret och klistra in ID: T i den **klient-Id** textrutan.
    
10. Gå tillbaka till appregistreringen B2C, klickar du på den **nycklar** knappen och klicka sedan på **skapa nycklar**. Klicka på **spara** att spara konfigurationen och visa den **appnyckeln**. Kopiera nyckeln till Urklipp.

    ![App-nyckel 1][api-management-howto-aad-b2c-app-key]

11. Gå tillbaka till API Management **Lägg till identitetsprovider** fönstret och klistra in nyckeln i den **Klienthemlighet** textrutan.
    
12. Ange domännamnet för Azure Active Directory B2C-klient i **registrerande klient**.

13. Den **utfärdare** fält kan du styra inloggnings-URL för Azure AD B2C att använda. Ange värdet till **< your_b2c_tenant_name >. b2clogin.com**.

14. Ange den **registreringen princip** och **inloggning från principen** från principer för B2C-klient. Du kan också du kan också ange den **redigera Profilprincip** och **princip för lösenordsåterställning**.

15. När du har angett den önskade konfigurationen, klickar du på **spara**.

    När ändringarna har sparats kommer utvecklare att kunna skapa nya konton och logga in på utvecklarportalen med hjälp av Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Registrera dig för ett utvecklarkonto med hjälp av Azure Active Directory B2C

1. För att registrera dig för ett utvecklarkonto med hjälp av Azure Active Directory B2C, öppna ett nytt webbläsarfönster och gå till utvecklarportalen. Klicka på den **registrera** knappen.

   ![Utvecklarportalen 1][api-management-howto-aad-b2c-dev-portal]

2. Välja att registrera dig med **Azure Active Directory B2C**.

   ![Utvecklarportalen 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Du är omdirigeras till registreringen principen som du konfigurerade i föregående avsnitt. Välja att registrera dig med din e-postadress eller ett av dina befintliga sociala konton.

   > [!NOTE]
   > Om Azure Active Directory B2C är det enda alternativet är aktiverat på den **identiteter** fliken på utgivarportalen du kommer att omdirigeras till registreringen principen direkt.

   ![Utvecklarportalen][api-management-howto-aad-b2c-dev-portal-b2c-options]

   När registreringen är klar, är du omdirigeras tillbaka till utvecklarportalen. Du är nu inloggad på developer-portalen för din API Management-tjänstinstans.

    ![Registreringen har slutförts][api-management-registration-complete]

## <a name="next-steps"></a>Nästa steg

*  [Översikt över Azure Active Directory B2C]
*  [Azure Active Directory B2C: Utökningsbart principramverk]
*  [Använd ett microsoftkonto som en identitetsprovider i Azure Active Directory B2C]
*  [Använd ett Google-konto som en identitetsprovider i Azure Active Directory B2C]
*  [Använd ett LinkedIn-konto som en identitetsprovider i Azure Active Directory B2C]
*  [Använd ett Facebook-konto som en identitetsprovider i Azure Active Directory B2C]



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

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
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
[Accessing the Graph API]: https://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[Översikt över Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[Hur du auktorisera konton med Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: Utökningsbart principramverk]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Använd ett microsoftkonto som en identitetsprovider i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Använd ett Google-konto som en identitetsprovider i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Använd ett Facebook-konto som en identitetsprovider i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Använd ett LinkedIn-konto som en identitetsprovider i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
