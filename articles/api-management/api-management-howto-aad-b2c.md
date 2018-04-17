---
title: Auktorisera developer konton med hjälp av Azure Active Directory B2C - Azure API Management | Microsoft Docs
description: Lär dig mer om att auktorisera användare med hjälp av Azure Active Directory B2C i API-hantering.
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
ms.openlocfilehash: 1268905d8a992d0cac65174142f6a3870f38f0d8
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2018
---
> [!WARNING]
> Azure Active Directory B2C-integrering finns i den [Developer- och Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/) endast nivåer.

# <a name="how-to-authorize-developer-accounts-by-using-azure-active-directory-b2c-in-azure-api-management"></a>Så här auktoriserar developer konton med hjälp av Azure Active Directory B2C i Azure API Management
## <a name="overview"></a>Översikt
Azure Active Directory B2C är en cloud lösning för Identitetshantering för konsumentinriktade webb- och mobilprogram. Du kan använda den för att hantera åtkomst till developer-portalen. Den här guiden visar den konfiguration som krävs i din API Management-tjänsten för att integrera med Azure Active Directory B2C. Information om hur du aktiverar åtkomst till developer-portalen med hjälp av den klassiska Azure Active Directory finns i [så att auktorisera developer konton med hjälp av Azure Active Directory].

> [!NOTE]
> Du måste ha en Azure Active Directory B2C-klient för att skapa ett program i för att slutföra stegen i den här guiden. Du måste också ha registrering och inloggning principer som är redo. Mer information finns i [översikt över Azure Active Directory B2C].

## <a name="authorize-developer-accounts-by-using-azure-active-directory-b2c"></a>Auktorisera developer konton med hjälp av Azure Active Directory B2C

1. Kom igång genom att klicka på **Publisher portal** i Azure-portalen för API Management-tjänsten. När du gör det kommer du till utgivarportalen för API Management.

   ![Utgivarportalen][api-management-management-console]

   > [!NOTE]
   > Om du inte har skapat en instans för API Management-tjänsten, se [skapa en instans för API Management-tjänsten] [ Create an API Management service instance] i den [Kom igång med Azure API Management kursen][Get started with Azure API Management].

2. På den **API Management** -menyn klickar du på **säkerhet**. På den **identiteter** , Välj **Azure Active Directory B2C**.

  ![Externa identiteter 1][api-management-howto-aad-b2c-security-tab]

3. Anteckna den **omdirigerings-URL** och växla till Azure Active Directory B2C i Azure-portalen.

  ![Externa identiteter 2][api-management-howto-aad-b2c-security-tab-reply-url]

4. Klicka på den **program** knappen.

  ![Registrera ett nytt program 1][api-management-howto-aad-b2c-portal-menu]

5. Klicka på den **Lägg till** för att skapa ett nytt program för Azure Active Directory B2C.

  ![Registrera ett nytt program 2][api-management-howto-aad-b2c-add-button]

6. I den **nytt program** bladet anger du ett namn för programmet. Välj **Ja** under **Web App/Web API**, och välj **Ja** under **Tillåt implicita flödet**. Kopiera den **omdirigerings-URL** från den **Azure Active Directory B2C** avsnitt i den **identiteter** i portalen för utgivaren och klistrar in det i den **Reply URL** textruta.

  ![Registrera ett nytt program 3][api-management-howto-aad-b2c-app-details]

7. Klicka på knappen **Skapa**. När programmet har skapats visas den i den **program** bladet. Klicka på namnet på programmet vill visa information.

  ![Registrera ett nytt program 4][api-management-howto-aad-b2c-app-created]

8. Från den **egenskaper** bladet, kopiera den **program-ID** till Urklipp.

  ![Program-ID 1][api-management-howto-aad-b2c-app-id]

9. Växla tillbaka till publisher-portalen och klistra in ID i den **klient-Id** textruta.

  ![Program-ID 2][api-management-howto-aad-b2c-client-id]

10. Gå tillbaka till Azure-portalen, klicka på den **nycklar** knappen och klicka sedan på **skapa nycklar**. Klicka på **spara** att spara konfigurationen och visa den **appkey**. Kopiera nyckeln till Urklipp.

  ![Appkey 1][api-management-howto-aad-b2c-app-key]

11. Växla tillbaka till publisher-portalen och klistra in nyckeln till den **Klienthemlighet** textruta.

  ![Appkey 2][api-management-howto-aad-b2c-client-secret]

12. Ange domännamnet för Azure Active Directory B2C-klient i **tillåtna klient**.

  ![Tillåtna klient][api-management-howto-aad-b2c-allowed-tenant]

13. Ange den **Signup princip** och **inloggning princip**. Alternativt kan du kan också tillhandahålla den **redigera Profilprincip** och **princip för lösenordsåterställning**.

  ![Principer][api-management-howto-aad-b2c-policies]

  > [!NOTE]
  > Mer information om principer finns i [Azure Active Directory B2C: utökningsbart principramverk].

14. När du har angett önskad konfiguration, klickar du på **spara**.

  När ändringarna har sparats kommer utvecklare att kunna skapa nya konton och logga in på developer-portalen med hjälp av Azure Active Directory B2C.

## <a name="sign-up-for-a-developer-account-by-using-azure-active-directory-b2c"></a>Registrera dig för ett utvecklarkonto med hjälp av Azure Active Directory B2C

1. Om du vill registrera dig för ett utvecklarkonto med hjälp av Azure Active Directory B2C, öppna ett nytt webbläsarfönster och gå till developer-portalen. Klicka på den **registrering** knappen.

   ![Developer-portalen 1][api-management-howto-aad-b2c-dev-portal]

2. Välja att logga med **Azure Active Directory B2C**.

   ![Developer-portalen 2][api-management-howto-aad-b2c-dev-portal-b2c-button]

3. Du ska omdirigeras till registreringen principen som du konfigurerade i föregående avsnitt. Välja att registrera dig med din e-postadress eller en av dina befintliga sociala konton.

   > [!NOTE]
   > Om Azure Active Directory B2C är det enda alternativet som är aktiverad på den **identiteter** fliken i publisher-portal du ska omdirigeras till registreringen principen direkt.

   ![Utvecklarportalen][api-management-howto-aad-b2c-dev-portal-b2c-options]

   När registreringen är klar, är du omdirigeras till developer-portalen. Du är nu inloggad på developer-portalen för din API Management service-instans.

    ![Registreringen har slutförts][api-management-registration-complete]

## <a name="next-steps"></a>Nästa steg

*  [översikt över Azure Active Directory B2C]
*  [Azure Active Directory B2C: utökningsbart principramverk]
*  [Använd ett Microsoft-konto som en identitetsleverantör i Azure Active Directory B2C]
*  [Använd ett Google-konto som en identitetsleverantör i Azure Active Directory B2C]
*  [Använd ett LinkedIn-konto som en identitetsleverantör i Azure Active Directory B2C]
*  [Använd ett Facebook-konto som en identitetsleverantör i Azure Active Directory B2C]




[api-management-howto-aad-b2c-security-tab]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab.PNG
[api-management-howto-aad-b2c-security-tab-reply-url]: ./media/api-management-howto-aad-b2c/api-management-b2c-security-tab-reply-url.PNG
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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph
[översikt över Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-overview
[så att auktorisera developer konton med hjälp av Azure Active Directory]: https://docs.microsoft.com/azure/api-management/api-management-howto-aad
[Azure Active Directory B2C: utökningsbart principramverk]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-policies
[Använd ett Microsoft-konto som en identitetsleverantör i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-msa-app
[Använd ett Google-konto som en identitetsleverantör i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-goog-app
[Använd ett Facebook-konto som en identitetsleverantör i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-fb-app
[Använd ett LinkedIn-konto som en identitetsleverantör i Azure Active Directory B2C]: https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-li-app

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
