---
title: "Auktorisera developer konton med hjälp av Azure Active Directory - Azure API Management | Microsoft Docs"
description: "Lär dig mer om att auktorisera användare med Azure Active Directory i API-hantering."
services: api-management
documentationcenter: API Management
author: juliako
manager: cfowler
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/30/2017
ms.author: apimpm
ms.openlocfilehash: 3faa6c1867808436a66a2b33ea1a9d79ede2c8fb
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
> [!WARNING]
> Azure Active Directory-integrering finns i den [Developer- och Premium](https://azure.microsoft.com/en-us/pricing/details/api-management/) endast nivåer.

# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Så här auktoriserar developer konton med hjälp av Azure Active Directory i Azure API Management
## <a name="overview"></a>Översikt
Den här guiden visar hur du aktiverar åtkomst till developer-portalen för användare från Azure Active Directory. Den här guiden visar även hur du hanterar grupper med Azure Active Directory-användare genom att lägga till externa grupper som innehåller användare av en Azure Active Directory.

> Du måste ha en Azure Active Directory att skapa ett program för att slutföra stegen i den här guiden.
> 

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Så här auktoriserar developer konton med hjälp av Azure Active Directory
Kom igång genom att klicka på **Publisher portal** i Azure-portalen för API Management-tjänsten. När du gör det kommer du till utgivarportalen för API Management.

![Utgivarportalen][api-management-management-console]

> Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][Create an API Management service instance] i självstudiekursen [Komma igång med Azure API Management][Get started with Azure API Management].
> 
> 

Klicka på **säkerhet** från den **API Management** menyn till vänster och klicka på **externa identiteter**.

![Externa identiteter][api-management-security-external-identities]

Klicka på **Azure Active Directory**. Anteckna den **omdirigerings-URL** och växla till Azure Active Directory i den klassiska Azure-portalen.

![Externa identiteter][api-management-security-aad-new]

Klicka på den **Lägg till** knappen om du vill skapa ett nytt program för Azure Active Directory och välj **Lägg till ett program som min organisation utvecklar**.

![Lägg till nytt Azure Active Directory-program][api-management-new-aad-application-menu]

Ange ett namn för programmet, Välj **Web application och/eller webb-API**, och klicka på Nästa.

![Nya Azure Active Directory-program][api-management-new-aad-application-1]

För **inloggnings-URL**, ange Webbadressen för inloggning till developer-portalen. I det här exemplet i **inloggnings-URL** är `https://aad03.portal.current.int-azure-api.net/signin`. 

För den **App-ID-URL**, ange standarddomänen eller en anpassad domän för Azure Active Directory och lägga till en unik sträng till den. I det här exemplet standarddomän för **https://contoso5api.onmicrosoft.com** används med suffixet för **/api** angivna.

![Nya egenskaper för Azure Active Directory-program][api-management-new-aad-application-2]

Klicka på knappen Kontrollera för att spara och skapa programmet och växla till den **konfigurera** att konfigurera det nya programmet.

![Nya Azure Active Directory-program som skapats][api-management-new-aad-app-created]

Om flera aktiva Azure-kataloger ska användas för det här programmet, klickar du på **Ja** för **programmet är flera innehavare**. Standardvärdet är **nr**.

![Programmet är flera innehavare][api-management-aad-app-multi-tenant]

Kopiera den **omdirigerings-URL** från den **Azure Active Directory** avsnitt i den **externa identiteter** i portalen för utgivaren och klistrar in det i den **svar URL: en** textruta. 

![Svars-URL][api-management-aad-reply-url]

Bläddra till längst ned på fliken Konfigurera väljer den **programbehörigheter** listrutan, och kontrollera **läsa katalogdata**.

![Programbehörigheter][api-management-aad-app-permissions]

Välj den **delegera behörigheter** listrutan, och kontrollera **aktivera inloggning och läsa användarprofiler**.

![Delegerade behörigheter][api-management-aad-delegated-permissions]

> Läs mer om programmet och delegerade behörigheter [åtkomst till Graph API][Accessing the Graph API].
> 
> 

Kopiera den **klient-Id** till Urklipp.

![Klient-ID][api-management-aad-app-client-id]

Växla tillbaka till publisher-portalen och klistra in i den **klient-Id** kopieras från konfigurationen av Azure Active Directory-program.

![Klient-ID][api-management-client-id]

Växla tillbaka till Azure Active Directory-konfigurationen och klicka på den **Markera varaktighet** listrutan i den **nycklar** avsnittet och ange ett intervall. I det här exemplet **1 års** används.

![Nyckel][api-management-aad-key-before-save]

Klicka på **spara** att spara konfigurationen och visa nyckeln. Kopiera nyckeln till Urklipp.

> Anteckna den här nyckeln. När du stänger fönstret Azure Active Directory-konfiguration kan nyckeln inte visas igen.
> 
> 

![Nyckel][api-management-aad-key-after-save]

Växla tillbaka till publisher-portalen och klistra in nyckeln till den **Klienthemlighet** textruta.

![Klienthemlighet][api-management-client-secret]

**Tillåtna hyresgäster** anger vilka kataloger har åtkomst till API: er för API Management service-instans. Ange domäner i Azure Active Directory-instanser som du vill bevilja åtkomst. Du kan avgränsa flera domäner med radmatningar bäddas, mellanslag eller semikolon.

![Tillåtna klienter][api-management-client-allowed-tenants]


När du önskad konfiguration har angetts, klickar du på **spara**.

![Spara][api-management-client-allowed-tenants-save]

När ändringarna sparas användare i den angivna Azure Active Directory kan logga in på Developer-portalen genom att följa stegen i [logga in på Developer-portalen med ett konto i Azure Active Directory] [ Log in to the Developer portal using an Azure Active Directory account].

Du kan ange flera domäner i den **tillåtna klienter** avsnitt. Innan en användare kan logga in från en annan domän än den ursprungliga där programmet har registrerats, bevilja en global administratör i domänen annat behörigheten för programmet att komma åt directory data. Om du vill ge behörighet, den globala administratören ska gå att `https://<URL of your developer portal>/aadadminconsent` (till exempel https://contoso.portal.azure-api.net/aadadminconsent), ange domännamnet för Active Directory-klient som de vill ge åtkomst till och klicka på Skicka. I följande exempel visas en global administratör från `miaoaad.onmicrosoft.com` försöker att ge behörighet till den här viss developer-portalen. 

![Behörigheter][api-management-aad-consent]

På nästa skärm uppmanas den globala administratören att bekräfta att ge behörigheten. 

![Behörigheter][api-management-permissions-form]

> Om en icke-globala administratör försöker logga in innan behörigheter beviljas av en global administratör i inloggningsförsök misslyckas och ett felmeddelande visas.
> 
> 

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Hur du lägger till en extern Azure Active Directory-grupp
Du kan lägga till Azure Active Directory-grupper i API-hantering för att lättare hantera associering av utvecklare i gruppen med de önskade produkterna efter att aktivera åtkomst för användare i ett Azure Active Directory.

> Om du vill konfigurera en extern Azure Active Directory-grupp, måste Azure Active Directory först konfigureras på fliken identiteter genom att följa anvisningarna i föregående avsnitt. 
> 
> 

Externa Azure Active Directory-grupper har lagts till från den **synlighet** fliken av produkten som du vill ge åtkomst till gruppen. Klicka på **produkter**, och klicka sedan på namnet på den önskade produkten.

![Konfigurera produkten][api-management-configure-product]

Växla till den **synlighet** och på **Lägg till grupper från Azure Active Directory**.

![Lägga till grupper][api-management-add-groups]

Välj den **Azure Active Directory-klient** från nedrullningsbara listan och sedan skriver du namnet på den önskade gruppen i den **grupper** som ska läggas till textrutan.

![Välj grupp][api-management-select-group]

Den här gruppnamn kan hittas i den **grupper** lista för din Azure Active Directory som visas i följande exempel.

![Listan för Azure Active Directory-grupper][api-management-aad-groups-list]

Klicka på **Lägg till** Validera gruppnamnet och lägga till gruppen. I det här exemplet i **Contoso 5 utvecklare** externa gruppen har lagts till. 

![Grupp som har lagts till][api-management-aad-group-added]

Klicka på **spara** att spara den nya grupp markeringen.

När en Azure Active Directory-grupp har konfigurerats från en produkt, den är tillgänglig kontrolleras den **synlighet** för andra produkter i API Management service-instans.

Om du vill granska och konfigurera egenskaper för externa grupper när de har lagts till, klicka på namnet på gruppen från den **grupper** fliken.

![Hantera grupper][api-management-groups]

Härifrån kan du redigera den **namn** och **beskrivning** i gruppen.

![Redigera grupp][api-management-edit-group]

Användare från den konfigurerade Azure Active Directory kan logga in på Developer-portalen och visa och prenumerera på alla grupper som de har insyn genom att följa anvisningarna i följande avsnitt.

## <a name="how-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a>Hur du loggar in på den Developer-portalen med ett Azure Active Directory-konto
Om du vill logga in på Developer-portalen med ett Azure Active Directory-konto som har konfigurerats i föregående avsnitt, öppnar du en ny webbläsare med den **inloggnings-URL** från Active Directory-konfigurationen för programmet och klicka på **Azure Active Directory**.

![Developer-portalen][api-management-dev-portal-signin]

Ange autentiseringsuppgifter för en användare i Azure Active Directory och klicka på **logga in**.

![Logga in][api-management-aad-signin]

Du kan uppmanas med ett registreringsformulär om ytterligare information krävs. Fyll i registreringsformuläret och på **registrera**.

![Registrering][api-management-complete-registration]

Dina användare är nu inloggad i developer-portalen för din API Management service-instans.

![Registreringen har slutförts][api-management-registration-complete]

[api-management-management-console]: ./media/api-management-howto-aad/api-management-management-console.png
[api-management-security-external-identities]: ./media/api-management-howto-aad/api-management-security-external-identities.png
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
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png
[api-management-aad-app-multi-tenant]: ./media/api-management-howto-aad/api-management-aad-app-multi-tenant.png
[api-management-aad-reply-url]: ./media/api-management-howto-aad/api-management-aad-reply-url.png
[api-management-aad-consent]: ./media/api-management-howto-aad/api-management-aad-consent.png
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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account

