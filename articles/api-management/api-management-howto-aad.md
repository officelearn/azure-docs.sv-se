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
ms.openlocfilehash: adbe33bedf23e37c2d3fd485965dea86edbd269a
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/30/2018
ms.locfileid: "52681474"
---
# <a name="authorize-developer-accounts-by-using-azure-active-directory-in-azure-api-management"></a>Auktorisera konton med hjälp av Azure Active Directory i Azure API Management

Den här artikeln visar hur du aktiverar åtkomst till utvecklarportalen för användare från Azure Active Directory (AD Azure). Den här guiden visar också hur du hanterar grupper med Azure AD-användare genom att lägga till externa grupper som innehåller användarna.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande snabbstart: [Skapa en Azure API Management-instans](get-started-create-service-instance.md).
- Importera och publicera en Azure API Management-instans. Mer information finns i [importera och publicera](import-and-publish.md).

[!INCLUDE [premium-dev-standard.md](../../includes/api-management-availability-premium-dev-standard.md)]

## <a name="authorize-developer-accounts-by-using-azure-ad"></a>Auktorisera konton med hjälp av Azure AD

1. Logga in på [Azure Portal](https://portal.azure.com). 
1. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
1. Typ **api** i sökrutan.
1. Välj **API Management-tjänster**.
1. Välj API Management-tjänstinstansen.
1. Under **SECURITY**väljer **identiteter**.

1. Välj **+ Lägg till** högst upp.

    Den **Lägg till identitetsprovider** visas i fönstret till höger.
1. Under **providertyp**väljer **Azure Active Directory**.

    Kontroller som gör det möjligt att ange annan information som behövs visas i fönstret. Kontrollerna omfattar **klient-ID** och **klienthemlighet**. (Du får information om de här kontrollerna senare i artikeln.)
1. Anteckna innehållet i **omdirigerings-URL**.
    
   ![Steg för att lägga till en identitetsprovider i Azure portal](./media/api-management-howto-aad/api-management-with-aad001.png)  
1. Öppna en annan flik i webbläsaren. 
1. Gå till [Azure-portalen](https://portal.azure.com).
1. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
1. Typ **active**. Den **Azure Active Directory** visas fönstret.
1. Välj **Azure Active Directory**.
1. Under **hantera**väljer **appregistreringar**.
1. Välj **Ny programregistrering**.

    ![Val för att skapa en ny appregistrering](./media/api-management-howto-aad/api-management-with-aad002.png)

    Den **skapa** visas i fönstret till höger. Det är där du anger Azure AD app-relevant information.
1. Ange ett namn för programmet.
1. Vilken typ av program, Välj **Web app/API**.
1. Ange Webbadressen logga in till din developer-portalen för URL-inloggningen. I det här exemplet, logga in URL: en är `https://apimwithaad.portal.azure-api.net/signin`.
1. Välj **skapa** att skapa programmet.
1. Om du vill hitta din app, Välj **appregistreringar** och Sök efter namn.

    ![Rutan där du söka efter en app](./media/api-management-howto-aad/find-your-app.png)
1. När programmet har registrerats kan du gå till **svars-URL** och se till att **omdirigerings-URL** är inställd på värdet som du fick från steg 9. 
1. Om du vill konfigurera ditt program (till exempel ändra **App-ID-URL**), Välj **egenskaper**.

    ![Öppna fönstret ”Egenskaper”](./media/api-management-howto-aad/api-management-with-aad004.png)

    Om flera Azure AD-instanser kommer att användas för det här programmet, väljer **Ja** för **med flera innehavare**. Standardvärdet är **nr**.
1. Ange behörigheter för programmet genom att välja **behörigheter som krävs för**.
1. Välj ditt program och välj sedan den **läsa katalogdata** och **logga in och läsa användarprofil** kryssrutorna.

    ![Kryssrutorna för behörighet](./media/api-management-howto-aad/api-management-with-aad005.png)

1. Välj **bevilja** godkänna behörigheter för programmet.

    Läs mer om behörigheter för programmet och delegerade behörigheter [åtkomst till Graph API][Accessing the Graph API].
    
1. I den vänstra rutan, kopierar den **program-ID** värde.

    ![”Program-ID-värdet](./media/api-management-howto-aad/application-id.png)
1. Gå tillbaka till ditt API Management-program. 

    I den **Lägg till identitetsprovider** klistrar den **program-ID** värde i den **klient-ID** box.
1. Gå tillbaka till Azure AD-konfigurationen och välj **nycklar**.
1. Skapa en ny nyckel genom att ange ett namn och en varaktighet. 
1. Välj **Spara**. Nyckeln genereras.

    Kopiera nyckeln till Urklipp.

    ![Val för att skapa en nyckel](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Anteckna den här nyckeln. När du stänger fönstret för Azure AD-konfiguration kan nyckeln inte visas igen.
    > 
    > 

1. Gå tillbaka till ditt API Management-program. 

    I den **Lägg till identitetsprovider** och klistra in nyckeln i den **klienthemlighet** textrutan.

    > [!IMPORTANT]
    > Glöm inte att uppdatera den **klienthemlighet** innan nyckeln upphör att gälla. 
    >  
    >

1. Den **Lägg till identitetsprovider** fönstret innehåller också de **tillåtna klienter** textrutan. Där kan ange domänerna för Azure AD-instanser som du vill bevilja åtkomst till API: er för API Management-tjänstinstans. Du kan avgränsa flera domäner med nya rader, blanksteg och kommatecken.

    Du kan ange flera domäner i den **tillåtna klienter** avsnittet. Innan alla användare kan logga in från en annan domän än den ursprungliga domänen där programmet har registrerats, måste en global administratör i en annan domän bevilja behörighet för programmet att komma åt katalogdata. Om du vill bevilja behörighet, bör den globala administratören:
    
    a. Gå till `https://<URL of your developer portal>/aadadminconsent` (till exempel https://contoso.portal.azure-api.net/aadadminconsent).
    
    b. Ange domännamnet för Azure AD-klient som de vill ge åtkomst till.
    
    c. Välj **skicka**. 
    
    I följande exempel visas försöker en global administratör från miaoaad.onmicrosoft.com ge behörighet till den här specifika developer-portalen. 

1. När du anger du önskad konfiguration väljer **Lägg till**.

    ![”Tillägg” knappen ”Lägg till identitetsprovider” fönstret](./media/api-management-howto-aad/api-management-with-aad007.png)

När ändringarna har sparats användare i den angivna Azure AD instans kan logga in på utvecklarportalen genom att följa stegen i [logga in på utvecklarportalen med hjälp av en Azure AD-konto](#log_in_to_dev_portal).

![Ange namnet på en Azure AD-klient](./media/api-management-howto-aad/api-management-aad-consent.png)

På nästa skärm uppmanas den globala administratören att bekräfta ger behörighet. 

![Bekräftelse av tilldela behörigheter](./media/api-management-howto-aad/api-management-permissions-form.png)

Om en icke-globala administratör försöker logga in innan en global administratör beviljar behörigheter, logga in försöket misslyckas och ett felmeddelande visas.

## <a name="add-an-external-azure-ad-group"></a>Lägg till en extern Azure AD-grupp

När du har aktiverat åtkomst för användare i en Azure AD-instans kan du lägga till Azure AD-grupper i API Management. Du kan sedan enkelt hantera associationen mellan utvecklare i gruppen med de önskade produkterna.

Konfigurera en extern Azure AD-grupp, måste du först konfigurera Azure AD-instansen på den **identiteter** fliken genom att följa anvisningarna i föregående avsnitt. 

Du lägger till externa Azure AD-grupper från den **grupper** fliken API Management-instans.

1. Välj fliken **Grupper**.
1. Välj den **Lägg till AAD-grupp** knappen.
   ![Knappen ”Lägg till AAD-grupp”](./media/api-management-howto-aad/api-management-with-aad008.png)
1. Välj den grupp som du vill lägga till.
1. Tryck på den **Välj** knappen.

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

[http://oauth.net/2/]: http://oauth.net/2/
[WebApp-GraphAPI-DotNet]: https://github.com/AzureADSamples/WebApp-GraphAPI-DotNet
[Accessing the Graph API]: http://msdn.microsoft.com/library/azure/dn132599.aspx#BKMK_Graph

[Prerequisites]: #prerequisites
[Configure an OAuth 2.0 authorization server in API Management]: #step1
[Configure an API to use OAuth 2.0 user authorization]: #step2
[Test the OAuth 2.0 user authorization in the Developer Portal]: #step3
[Next steps]: #next-steps

[Sign in to the developer portal by using an Azure AD account]: #Sign-in-to-the-developer-portal-by-using-an-Azure-AD-account
