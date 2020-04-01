---
title: Auktorisera utvecklarkonton med hjälp av Azure Active Directory
titleSuffix: Azure API Management
description: Lär dig hur du auktoriserar användare med hjälp av Azure Active Directory i API Management.
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
ms.openlocfilehash: 6102b1e1d6ddbac01033b9cecfeba96a7eb33777
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473548"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Auktorisera utvecklarkonton med Azure Active Directory i Azure API Management

I den här artikeln visas hur du aktiverar åtkomst till utvecklarportalen för användare från Azure Active Directory (Azure AD). Den här guiden visar också hur du hanterar grupper av Azure AD-användare genom att lägga till externa grupper som innehåller användarna.

## <a name="prerequisites"></a>Krav

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Importera och publicera en Azure API Management-instans. Mer information finns i [Importera och publicera](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Auktorisera utvecklarkonton med hjälp av Azure AD

1. Logga in på [Azure-portalen](https://portal.azure.com). 
2. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
3. Skriv **api** i sökrutan.
4. Välj **API Management-tjänster**.
5. Välj API Management-tjänstinstansen.
6. Under **Säkerhet**väljer du **Identiteter**.
7. Välj **+Lägg till** högst upp.

    Fönstret **Lägg till identitetsprovider** visas till höger.
8. Under **Providertyp**väljer du **Azure Active Directory**.

    Kontroller som gör att du kan ange annan nödvändig information visas i fönstret. Kontrollerna omfattar **klient-ID** och **klienthemlighet**. (Du får information om dessa kontroller senare i artikeln.)
9. Anteckna innehållet i **URL:en omdirigering**.
    
   ![Steg för att lägga till en identitetsprovider i Azure-portalen](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Öppna en annan flik i webbläsaren. 
11. Navigera till [Azure-portalen - Appregistreringar](https://go.microsoft.com/fwlink/?linkid=2083908) för att registrera en app i Active Directory.
12. Under **Hantera**väljer du **Appregistreringar**.
13. Välj **Ny registrering**. På sidan **Registrera ett program** anger du värdena på följande sätt:
    
    * Ange **namn** till ett meningsfullt namn. t.ex. *utvecklare-portal*
    * Ange **kontotyper som stöds** till konton i den här **organisationskatalogen**. 
    * Ange **Redirect URI** till det värde du fick från steg 9. 
    * Välj **Registrera**. 

14.  När programmet har registrerats kopierar du **program-ID:t (klient)från** sidan **Översikt.** 
15. Gå tillbaka till API Management-instansen. Klistra in värdet **Program -ID i** rutan **Klient-ID** i fönstret Lägg **till identitetsprovider.**
16. Växla tillbaka till Azure AD-konfigurationen, Välj **certifikat & hemligheter** under **Hantera**. Klicka på knappen **Ny klienthemlighet**. Ange ett värde i **Beskrivning**, välj ett alternativ för **Förfaller** och välj **Lägg till**. Kopiera klientens hemliga värde innan du lämnar sidan. Du behöver den i nästa steg. 
17. Under **Hantera**väljer du **Autentisering** och väljer sedan **ID-token** under **Implicit grant**
18. Gå tillbaka till API Management-instansen och klistra in hemligheten i **klienthemligheten.**

    > [!IMPORTANT]
    > Se till att uppdatera **klienthemligheten** innan nyckeln går ut. 
    >  
    >

19. Fönstret **Lägg till identitetsprovider** innehåller också textrutan **Tillåtna klienter.** Där anger du domänerna för Azure AD-instanser som du vill bevilja åtkomst till API:erna för API Management-tjänstinstansen. Du kan avgränsa flera domäner med nyalinjer, blanksteg eller kommatecken.

    > [!NOTE]
    > Du kan ange flera domäner i avsnittet **Tillåtna klienter.** Innan en användare kan logga in från en annan domän än den ursprungliga domänen där programmet registrerades, måste en global administratör för den olika domänen bevilja behörighet för programmet att komma åt katalogdata. För att bevilja behörighet bör den globala administratören: a. Gå `https://<URL of your developer portal>/aadadminconsent` till (till https://contoso.portal.azure-api.net/aadadminconsent)exempel .
    > b. Skriv in domännamnet för Azure AD-klienten som de vill ge åtkomst till.
    > c. Välj **Skicka**. 

20.  När du har angett önskad konfiguration väljer du **Lägg till**.

När ändringarna har sparats kan användare i den angivna Azure AD-instansen logga in på utvecklarportalen genom att följa stegen i [Logga in på utvecklarportalen med hjälp av ett Azure AD-konto](#log_in_to_dev_portal).

## <a name="add-an-external-azure-ad-group"></a>Lägga till en extern Azure AD-grupp

När du har aktiverat åtkomst för användare i en Azure AD-klient kan du lägga till Azure AD-grupper i API Management. Därför kan du styra produktsynligheten med Azure AD-grupper.

Om du vill lägga till en extern Azure AD-grupp i APIM måste du först fylla i föregående avsnitt. Dessutom måste programmet du registrerade beviljas åtkomst till Microsoft `Directory.Read.All` Graph API med tillstånd genom att följa dessa steg: 

1. Gå tillbaka till din appregistrering som skapades i föregående avsnitt.
2. Välj **API-behörigheter**och klicka sedan på **+Lägg till en behörighet**. 
3. I fönstret **Begär API-behörigheter** markerar du fliken **Microsoft API:er** och väljer sedan microsoft **graph-panelen.** Välj **Programbehörigheter**, sök efter **katalog**och välj sedan behörigheten **Directory.Read.All.** 
4. Klicka på **Lägg till behörigheter** längst ned i fönstret och klicka sedan på **Bevilja administratörsmedgivande för {klientnamn}** så att du beviljar åtkomst för alla användare i den här katalogen. 

Nu kan du lägga till externa Azure AD-grupper från fliken **Grupper** i din API Management-instans.

1. Välj fliken **Grupper**.
2. Välj **knappen Lägg till AAD-grupp.**
    ![Knappen "Lägg till AAD-grupp"](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Markera den grupp som du vill lägga till.
4. Tryck på **knappen Välj.**

När du har lagt till en extern Azure AD-grupp kan du granska och konfigurera dess egenskaper. Välj namnet på gruppen på fliken **Grupper.** Härifrån kan du redigera **namn-** och **beskrivningsinformation** för gruppen.
 
Användare från den konfigurerade Azure AD-instansen kan nu logga in på utvecklarportalen. De kan visa och prenumerera på alla grupper som de har synlighet för.

## <a name="developer-portal---add-azure-ad-account-authentication"></a><a id="log_in_to_dev_portal"/>Utvecklarportalen – lägg till autentisering av Azure AD-konton

I utvecklarportalen är inloggning med AAD möjlig med **inloggningsknappen: OAuth-widgeten.** Widgeten finns redan på inloggningssidan för standardportalens innehåll för utvecklarportalen.

Även om ett nytt konto skapas automatiskt när en ny användare loggar in med AAD, kan du överväga att lägga till samma widget på registreringssidan.

**Registreringsformuläret: OAuth-widgeten** representerar ett formulär som används för att registrera dig med OAuth.

> [!IMPORTANT]
> Du måste [publicera om portalen](api-management-howto-developer-portal-customize.md#publish) för att AAD-ändringarna ska börja gälla.

## <a name="legacy-developer-portal---how-to-sign-in-with-azure-ad"></a>Äldre utvecklarportal – så här loggar du in med Azure AD

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

Så här loggar du in på utvecklarportalen med hjälp av ett Azure AD-konto som du har konfigurerat i föregående avsnitt:

1. Öppna ett nytt webbläsarfönster med hjälp av inloggnings-URL:en från Active Directory-programkonfigurationen och välj **Azure Active Directory**.

   ![Inloggningssida][api-management-dev-portal-signin]

1. Ange autentiseringsuppgifterna för en av användarna i Azure AD och välj **Logga in**.

   ![Logga in med användarnamn och lösenord][api-management-aad-signin]

1. Du kan bli tillfrågad med ett registreringsformulär om ytterligare information krävs. Fyll i registreringsformuläret och välj **Registrera dig**.

   !["Registrera dig"-knappen på registreringsformuläret][api-management-complete-registration]

Användaren är nu inloggad på utvecklarportalen för din API Management-tjänstinstans.

![Utvecklarportalen efter att registreringen är klar][api-management-registration-complete]

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

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
