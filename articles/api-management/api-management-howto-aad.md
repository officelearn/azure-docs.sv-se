---
title: Auktorisera konton med hjälp av Azure Active Directory – Azure API Management | Microsoft Docs
description: Lär dig mer om att auktorisera användare med hjälp av Azure Active Directory i API Management.
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
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: 422c2a8a61b1df36b452c153aa6cd78ba7e2dcef
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64723636"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Auktorisera konton med hjälp av Azure Active Directory i Azure API Management

Den här artikeln visar hur du aktiverar åtkomst till utvecklarportalen för användare från Azure Active Directory (AD Azure). Den här guiden visar också hur du hanterar grupper med Azure AD-användare genom att lägga till externa grupper som innehåller användarna.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Importera och publicera en Azure API Management-instans. Mer information finns i [importera och publicera](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Auktorisera konton med hjälp av Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
3. Typ **api** i sökrutan.
4. Välj **API Management-tjänster**.
5. Välj API Management-tjänstinstansen.
6. Under **Security**väljer **identiteter**.
7. Välj **+ Lägg till** högst upp.

    Den **Lägg till identitetsprovider** visas i fönstret till höger.
8. Under **providertyp**väljer **Azure Active Directory**.

    Kontroller som gör det möjligt att ange annan information som behövs visas i fönstret. Kontrollerna omfattar **klient-ID** och **klienthemlighet**. (Du får information om de här kontrollerna senare i artikeln.)
9. Anteckna innehållet i **omdirigerings-URL**.
    
   ![Steg för att lägga till en identitetsprovider i Azure portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Öppna en annan flik i webbläsaren. 
11. Navigera till den [Azure portal – appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) att registrera en app i Active Directory.
12. Under **hantera**väljer **appregistreringar**.
13. Välj **ny registrering**. På den **registrera ett program** ställer du in värdena på följande sätt:
    
* Ange **namn** till ett beskrivande namn. t.ex. *developer-portalen*
* Ange **stöds kontotyper** till **konton i den här organisationens katalogen**. 
* Ange **omdirigerings-URI** till det värde som du fick från steg 9. 
* Välj **registrera**. 

14.  När programmet har registrerats kan du kopiera den **(klient)-ID: T** från den **översikt** sidan. 
15. Gå tillbaka till din API Management-instans. I den **Lägg till identitetsprovider** klistrar den **(klient)-ID: T** värde i den **klient-ID** box.
16. Gå tillbaka till Azure AD-konfiguration, Välj **certifikat och hemligheter** under **hantera**. Välj den **nya klienthemligheten** knappen. Ange ett värde i **beskrivning**, Välj ett alternativ för **förfaller** och välj **Lägg till**. Kopiera värdet för hemligheten klienten innan de lämnar sidan. Du behöver den i nästa steg. 
17. Gå tillbaka till din API Management-instans, klistra in hemligheten i den **klienthemlighet** box.

    > [!IMPORTANT]
    > Glöm inte att uppdatera den **klienthemlighet** innan nyckeln upphör att gälla. 
    >  
    >

18. Den **Lägg till identitetsprovider** fönstret innehåller också de **tillåtna klienter** textrutan. Där kan ange domänerna för Azure AD-instanser som du vill bevilja åtkomst till API: er för API Management-tjänstinstans. Du kan avgränsa flera domäner med nya rader, blanksteg och kommatecken.

> [!NOTE]
> Du kan ange flera domäner i den **tillåtna klienter** avsnittet. Innan alla användare kan logga in från en annan domän än den ursprungliga domänen där programmet har registrerats, måste en global administratör i en annan domän bevilja behörighet för programmet att komma åt katalogdata. Om du vill bevilja behörighet, den globala administratören bör: en. Gå till `https://<URL of your developer portal>/aadadminconsent` (till exempel https://contoso.portal.azure-api.net/aadadminconsent).
> b. Ange domännamnet för Azure AD-klient som de vill ge åtkomst till.
> c. Välj **Skicka**. 

19.  När du anger du önskad konfiguration väljer **Lägg till**.

När ändringarna har sparats användare i den angivna Azure AD instans kan logga in på utvecklarportalen genom att följa stegen i [logga in på utvecklarportalen med hjälp av en Azure AD-konto](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Lägg till en extern Azure AD-grupp

När du har aktiverat åtkomst för användare i en Azure AD-instans kan du lägga till Azure AD-grupper i API Management. Du kan sedan enkelt hantera associationen mellan utvecklare i gruppen med de önskade produkterna.

 > [!IMPORTANT]
 > Att lägga till en extern Azure AD-grupp, måste du först konfigurera Azure AD-instansen på den **identiteter** fliken genom att följa anvisningarna i föregående avsnitt. Dessutom kan programmet måste beviljas åtkomst till Azure AD Graph API med `Directory.Read.All` behörighet. 

Du lägger till externa Azure AD-grupper från den **grupper** fliken API Management-instans.

1. Välj fliken **Grupper**.
2. Välj den **Lägg till AAD-grupp** knappen.
   ![Knappen ”Lägg till AAD-grupp”](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Välj den grupp som du vill lägga till.
4. Tryck på den **Välj** knappen.

När du lägger till en extern Azure AD gruppen, du kan granska och konfigurera dess egenskaper. Välj namnet på gruppen från den **grupper** fliken. Härifrån kan du redigera **namn** och **beskrivning** information för gruppen.
 
Användare från den konfigurerade Azure AD-instans kan nu logga in på utvecklarportalen. De kan visa och prenumerera på alla grupper som de har synlighet.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Logga in på utvecklarportalen med hjälp av en Azure AD-konto

Att logga in på utvecklarportalen med hjälp av en Azure AD-konto som du konfigurerade i föregående avsnitt:

1. Öppna ett nytt webbläsarfönster med hjälp av URL-inloggning från konfigurationen av Active Directory-programmet och välj **Azure Active Directory**.

   ![På inloggningssidan][api-management-dev-portal-signin]

1. Ange autentiseringsuppgifterna för en av användarna i Azure AD och välj **logga in**.

   ![Logga in med användarnamn och lösenord][api-management-aad-signin]

1. Du kan uppmanas med ett registreringsformulär om ytterligare information krävs. Fylla i registreringsformuläret och välj **registrera**.

   ![Knappen ”Logga” i registreringsformuläret][api-management-complete-registration]

Användaren är nu inloggad på developer-portalen för din API Management-tjänstinstans.

![Developer-portalen när registreringen är slutförd][api-management-registration-complete]

[api-management-dev-portal-signin]: ./media/api-management-howto-aad/api-management-dev-portal-signin.png
[api-management-aad-signin]: ./media/api-management-howto-aad/api-management-aad-signin.png
[api-management-complete-registration]: ./media/api-management-howto-aad/api-management-complete-registration.png
[api-management-registration-complete]: ./media/api-management-howto-aad/api-management-registration-complete.png

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
