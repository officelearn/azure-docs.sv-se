---
title: Auktorisera developer konton med hjälp av Azure Active Directory - Azure API Management | Microsoft Docs
description: Lär dig mer om att auktorisera användare med hjälp av Azure Active Directory i API-hantering.
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
ms.openlocfilehash: b0476865b19cd078b05e5def4a51c2df17315daa
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Auktorisera developer konton med hjälp av Azure Active Directory i Azure API Management

Den här artikeln visar hur du aktiverar åtkomst till developer-portalen för användare från Azure Active Directory (AD Azure). Den här guiden visar även hur du hanterar grupper av Azure AD-användare genom att lägga till externa grupper som innehåller användare.

> [!NOTE]
> Azure AD-integrering finns i den [utvecklare, Standard och Premium](https://azure.microsoft.com/pricing/details/api-management/) endast nivåer.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Importera och publicera en Azure API Management-instans. Mer information finns i [Import och publicera](import-and-publish.md).

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Auktorisera developer konton med hjälp av Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
3. Typen **api** i sökrutan.
4. Välj **API Management services**.
5. Välj din API Management service-instans.
6. Under **säkerhet**väljer **identiteter**.

7. Välj **+ Lägg till** från början.

    Den **Lägg till identitetsleverantör** fönstret visas till höger.
8. Under **providertyp**väljer **Azure Active Directory**.

    Kontroller som gör det möjligt att andra nödvändig information visas i fönstret. Kontrollerna inkluderar **klient-ID** och **klienthemlighet**. (Du får information om de här kontrollerna senare i artikeln.)
9. Anteckna innehållet i **omdirigerings-URL**.
    
   ![Steg för att lägga till en identitetsleverantör i Azure-portalen](./media/api-management-howto-aad/api-management-with-aad001.png)  
10. Öppna en annan flik i webbläsaren. 
11. Gå till [Azure-portalen](https://portal.azure.com).
12. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
13. Typen **active**. Den **Azure Active Directory** visas.
14. Välj **Azure Active Directory**.
15. Under **hantera**väljer **App registreringar**.
16. Välj **Ny programregistrering**.

    ![Alternativ för att skapa en ny appregistrering](./media/api-management-howto-aad/api-management-with-aad002.png)

    Den **skapa** fönstret visas till höger. Det är där du anger Azure AD app-relevant information.
17. Ange ett namn för programmet.
18. Programtyp, Välj **webb-app/API**.
19. Ange URL-inloggning för developer-portalen för URL-inloggning. I det här exemplet är URL-inloggning https://apimwithaad.portal.azure-api.net/signin.
20. Välj **skapa** att skapa programmet.
21. För att hitta appen, Välj **App registreringar** och Sök efter namn.

    ![Rutan där du söker efter en app](./media/api-management-howto-aad/find-your-app.png)
22. När programmet har registrerats kan du gå till **Reply URL** och se till att **omdirigerings-URL** är inställd på värdet som du har fått från steg 9. 
23. Om du vill konfigurera ditt program (till exempel ändra **App-ID-URL**), Välj **egenskaper**.

    ![Öppna fönstret ”Egenskaper”](./media/api-management-howto-aad/api-management-with-aad004.png)

    Om flera instanser av Azure AD kommer att användas för det här programmet, väljer **Ja** för **flera innehavare**. Standardvärdet är **nr**.
24. Ange behörigheter för program genom att välja **nödvändiga behörigheter**.
25. Välj programmet och välj sedan den **läsa katalogdata** och **logga in och Läs användarprofil** kryssrutorna.

    ![Kryssrutor för behörigheter](./media/api-management-howto-aad/api-management-with-aad005.png)

    Mer information om behörigheter för program och delegerade behörigheter finns [åtkomst till Graph API][Accessing the Graph API].
26. I den vänstra rutan, kopierar den **program-ID** värde.

    ![”Program-ID”-värdet](./media/api-management-howto-aad/application-id.png)
27. Växla tillbaka till ditt API Management-program. 

    I den **Lägg till identitetsleverantör** och klistra in den **program-ID** värde i den **klient-ID** rutan.
28. Gå tillbaka till Azure AD-konfiguration och välj **nycklar**.
29. Skapa en ny nyckel genom att ange ett namn och en varaktighet. 
30. Välj **Spara**. Nyckeln genereras.

    Kopiera nyckeln till Urklipp.

    ![Alternativ för att skapa en nyckel](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Anteckna den här nyckeln. När du stänger fönstret Azure AD-konfiguration kan nyckeln inte visas igen.
    > 
    > 
31. Växla tillbaka till ditt API Management-program. 

    I den **Lägg till identitetsleverantör** fönstret klistra in nyckeln i den **klienthemlighet** textruta.

    > [!IMPORTANT]
    > Se till att uppdatera den **klienthemlighet** innan nyckeln upphör att gälla. 
    >  
    >
32. Den **Lägg till identitetsleverantör** fönstret innehåller också den **tillåtna klienter** textruta. Ange det, domäner i Azure AD-instanser som du vill bevilja åtkomst till API: er för API Management service-instans. Du kan avgränsa flera domäner med radmatningar bäddas, mellanslag eller semikolon.

    Du kan ange flera domäner i den **tillåtna hyresgäster** avsnitt. Innan en användare kan logga in från en annan domän än den ursprungliga där programmet har registrerats, bevilja en global administratör i domänen annat behörigheten för programmet att komma åt directory data. Om du vill ge behörighet, bör den globala administratören
    
    a. Gå till `https://<URL of your developer portal>/aadadminconsent` (till exempel https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Skriv i domännamnet för Azure AD-klient som de vill ge åtkomst till.
    
    c. Välj **skicka**. 
    
    I följande exempel en global administratör från miaoaad.onmicrosoft.com försöker ge behörighet till den här viss developer-portalen. 

33. När du har angett önskad konfiguration, markera **Lägg till**.

    ![”Knappen Lägg till” i rutan ”Lägg till identitetsleverantören”.](./media/api-management-howto-aad/api-management-with-aad007.png)

När ändringarna har sparats användare i den angivna Azure AD instans kan logga in på developer-portalen genom att följa stegen i [logga in på developer-portalen med hjälp av Azure AD-kontot](#log_in_to_dev_portal).

![Ange namnet på en Azure AD-klient](./media/api-management-howto-aad/api-management-aad-consent.png)

På nästa skärm uppmanas den globala administratören att bekräfta att ge behörigheten. 

![Bekräftelse för att tilldela behörigheter](./media/api-management-howto-aad/api-management-permissions-form.png)

Om en icke-globala administratör försöker logga in innan en global administratör ger behörighet inloggning försöket misslyckas och ett felmeddelande visas.

## <a name="add-an-external-azure-ad-group"></a>Lägg till en extern Azure AD-grupp

När du aktiverar åtkomst för användare i en Azure AD-instans, kan du lägga till Azure AD-grupper i API-hantering. Du kan sedan enkelt hantera associering av utvecklare i gruppen med de önskade produkterna.

Så här konfigurerar du en extern Azure AD-grupp, måste du först konfigurera Azure AD-instansen på den **identiteter** fliken genom att följa anvisningarna i föregående avsnitt. 

Du lägger till externa Azure AD-grupper från den **grupper** för API Management-instans.

1. Välj fliken **Grupper**.
2. Välj den **lägga till AAD grupp** knappen.
   ![Knappen ”Lägg till AAD-grupp”](./media/api-management-howto-aad/api-management-with-aad008.png)
3. Välj den grupp som du vill lägga till.
4. Tryck på den **Välj** knappen.

När du lägger till en extern Azure AD grupp, kan du granska och konfigurera dess egenskaper. Välj namnet på gruppen från den **grupper** fliken. Härifrån kan du redigera **namn** och **beskrivning** information om gruppen.
 
Användare från den konfigurerade Azure AD-instans kan nu logga in på developer-portalen. De kan visa och prenumerera på alla grupper som de har synlighet.

## <a name="a-idlogintodevportalsign-in-to-the-developer-portal-by-using-an-azure-ad-account"></a><a id="log_in_to_dev_portal"/>Logga in på developer-portalen med hjälp av Azure AD-kontot

Logga in på developer-portalen med hjälp av en Azure AD-kontot som du konfigurerade i föregående avsnitt:

1. Öppna ett nytt webbläsarfönster med hjälp av URL-inloggning från konfigurationen av Active Directory-programmet och välj **Azure Active Directory**.

   ![Inloggningssidan][api-management-dev-portal-signin]

2. Ange autentiseringsuppgifter för en användare i Azure AD och välj **logga in**.

   ![Logga in med användarnamn och lösenord][api-management-aad-signin]

3. Du kan uppmanas med ett registreringsformulär om ytterligare information krävs. Fylla i registreringsformuläret och välj **registrera**.

   ![Knappen ”Logga” i registreringsformuläret][api-management-complete-registration]

Användaren är nu inloggad developer-portalen för din API Management service-instans.

![När registreringen är slutförd Developer-portalen][api-management-registration-complete]

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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
