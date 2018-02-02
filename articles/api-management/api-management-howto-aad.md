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
ms.date: 01/16/2018
ms.author: apimpm
ms.openlocfilehash: c9d99d6f7c2777c8e68f5db50b402280377d88e9
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="how-to-authorize-developer-accounts-using-azure-active-directory-in-azure-api-management"></a>Så här auktoriserar developer konton med hjälp av Azure Active Directory i Azure API Management

Den här guiden visar hur du aktiverar åtkomst till developer-portalen för användare från Azure Active Directory. Den här guiden visar även hur du hanterar grupper med Azure Active Directory-användare genom att lägga till externa grupper som innehåller användare av en Azure Active Directory.

> [!WARNING]
> Azure Active Directory-integrering finns i den [utvecklare, Standard och Premium](https://azure.microsoft.com/pricing/details/api-management/) endast nivåer.

## <a name="prerequisites"></a>Förutsättningar

- Slutför följande Snabbstart: [skapa en instans av Azure API Management](get-started-create-service-instance.md).
- Importera och publicera en API Management-instans. Mer information finns i [Import och publicera](import-and-publish.md).

## <a name="how-to-authorize-developer-accounts-using-azure-active-directory"></a>Så här auktoriserar developer konton med hjälp av Azure Active Directory

1. Logga in på [Azure Portal](https://portal.azure.com). 
2. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
3. Skriv ”api” i sökrutan.
4. Klicka på **API Management services**.
5. Välj din APIM tjänstinstansen.
6. Under **säkerhet**väljer **identiteter**.

    ![Externa identiteter](./media/api-management-howto-aad/api-management-with-aad001.png)
7. Klicka på **+ Lägg till** från början.

    Den **Lägg till identitetsleverantör** visas till höger.
8. Under **providertyp**väljer **Azure Active Directory**.

    Kontroller som gör det möjligt att andra nödvändig information visas i fönstret. Kontrollerna inkluderar: **klient-ID**, **klienthemlighet** (du får information senare under kursen).
9. Anteckna den **omdirigerings-URL**.  
10. Öppna en annan flik i webbläsaren. 
11. Öppna [Azure-portalen](https://portal.azure.com).
12. Välj ![arrow](./media/api-management-howto-aad/arrow.png).
13. Typen ”aktiv” i **Azure Active Directory** visas.
14. Välj **Azure Active Directory**.
15. Under **hantera**väljer **appregistrering**.

    ![Appregistrering](./media/api-management-howto-aad/api-management-with-aad002.png)
16. Klicka på **Ny programregistrering**.

    Den **skapa** visas till höger. Det är där du anger AAD app relavent information.
17. Eneter ett namn för programmet.
18. Programtyp, Välj **webb-app/API**.
19. För inloggnings-URL, anger du URL för inloggning av developer-portalen. I det här exemplet är URL inloggning: https://apimwithaad.portal.azure-api.net/signin.
20. Klicka på **skapa** att skapa programmet.
21. För att hitta appen, Välj **App registreringar** och Sök efter namn.

    ![Appregistrering](./media/api-management-howto-aad/find-your-app.png)
22. När programmet har registrerats kan du gå till **Reply URL** och se till att ”omdirigerings-URL: en” anges till värdet som du har fått från steg 9. 
23. Om du vill konfigurera ditt program (till exempel ändra **App-ID-URL**) Välj **egenskaper**.

    ![Appregistrering](./media/api-management-howto-aad/api-management-with-aad004.png)

    Om flera aktiva Azure-kataloger ska användas för det här programmet, klickar du på **Ja** för **programmet är flera innehavare**. Standardvärdet är **nr**.
24. Ange behörigheter för program genom att välja **nödvändiga behörigheter**.
25. Välj den dina program och kontrollera **läsa katalogdata** och **logga in och Läs användarprofil**.

    ![Appregistrering](./media/api-management-howto-aad/api-management-with-aad005.png)

    Läs mer om programmet och delegerade behörigheter [åtkomst till Graph API][Accessing the Graph API].
26. I det vänstra fönstret, kopierar den **program-ID** värde.

    ![Appregistrering](./media/api-management-howto-aad/application-id.png)
27. Växla tillbaka till ditt API Management-program. Den **Lägg till identitetsleverantör** fönstret ska visas. <br/>Klistra in den **program-ID** värde i den **klient-Id** rutan.
28. Växla tillbaka till Azure Active Directory-konfigurationen och klicka på **nycklar**.
29. Skapa en ny nyckel genom speicifying ett namn och en varaktighet. 
30. Klicka på **Spara**. Nyckeln hämtar genereras.

    Kopiera nyckeln till Urklipp.

    ![Appregistrering](./media/api-management-howto-aad/api-management-with-aad006.png)

    > [!NOTE]
    > Anteckna den här nyckeln. När du stänger fönstret Azure Active Directory-konfiguration kan nyckeln inte visas igen.
    > 
    > 
31. Växla tillbaka till ditt API Management-program. <br/>I den **Lägg till identitetsleverantören** och klistra in nyckeln till den **klienthemlighet** textruta.
32. Den **Lägg till identitetsleverantör** fönstret, innehåller den **tillåtna klienter** textruta i du ange vilka kataloger ska ha åtkomst till API: er för API Management service-instans. <br/>Ange domäner i Azure Active Directory-instanser som du vill bevilja åtkomst. Du kan avgränsa flera domäner med radmatningar bäddas, mellanslag eller semikolon.

    Du kan ange flera domäner i den **tillåtna klienter** avsnitt. Innan en användare kan logga in från en annan domän än den ursprungliga där programmet har registrerats, bevilja en global administratör i domänen annat behörigheten för programmet att komma åt directory data. Om du vill ge behörighet, den globala administratören ska gå att `https://<URL of your developer portal>/aadadminconsent` (till exempel https://contoso.portal.azure-api.net/aadadminconsent), ange domännamnet för Active Directory-klient som de vill ge åtkomst till och klicka på Skicka. I följande exempel visas en global administratör från `miaoaad.onmicrosoft.com` försöker att ge behörighet till den här viss developer-portalen. 

33. När du önskad konfiguration har angetts, klickar du på **Lägg till**.

    ![Appregistrering](./media/api-management-howto-aad/api-management-with-aad007.png)

När ändringarna sparas användare i den angivna Azure Active Directory kan logga in på Developer-portalen genom att följa stegen i [logga in på Developer-portalen med ett konto i Azure Active Directory](#log_in_to_dev_portal).

![Behörigheter](./media/api-management-howto-aad/api-management-aad-consent.png)

På nästa skärm uppmanas den globala administratören att bekräfta att ge behörigheten. 

![Behörigheter](./media/api-management-howto-aad/api-management-permissions-form.png)

Om en icke-globala administratör försöker logga in innan behörigheter beviljas av en global administratör i inloggningsförsök misslyckas och ett felmeddelande visas.

## <a name="how-to-add-an-external-azure-active-directory-group"></a>Hur du lägger till en extern Azure Active Directory-grupp

Du kan lägga till Azure Active Directory-grupper i API-hantering för att lättare hantera associering av utvecklare i gruppen med de önskade produkterna efter att aktivera åtkomst för användare i ett Azure Active Directory.

Om du vill konfigurera en extern Azure Active Directory-grupp, måste Azure Active Directory först konfigureras på fliken identiteter genom att följa anvisningarna i föregående avsnitt. 

Externa Azure Active Directory-grupper har lagts till från den **grupper** för API Management-instans.

![Grupper](./media/api-management-howto-aad/api-management-with-aad008.png)

1. Välj fliken **Grupper**.
2. Klicka på den **lägga till AAD grupp** knappen.
3. Välj den grupp som du vill lägga till.
4. Tryck på **Välj** knappen.

När en Azure Active Directory-grupp har skapats, du kan granska och konfigurera egenskaper för externa grupper när de har lagts till, klicka på namnet på gruppen från den **grupper** fliken.

Härifrån kan du redigera den **namn** och **beskrivning** i gruppen.
 
Användare från den konfigurerade Azure Active Directory kan logga in på Developer-portalen och visa och prenumerera på alla grupper som de har insyn genom att följa anvisningarna i följande avsnitt.

## <a name="a-idlogintodevportalhow-to-log-in-to-the-developer-portal-using-an-azure-active-directory-account"></a><a id="log_in_to_dev_portal"/>Hur du loggar in på den Developer-portalen med ett Azure Active Directory-konto

Om du vill logga in på Developer-portalen med ett Azure Active Directory-konto som har konfigurerats i föregående avsnitt, öppnar du en ny webbläsare med den **inloggnings-URL** från Active Directory-konfigurationen för programmet och klicka på **Azure Active Directory**.

![Developer-portalen][api-management-dev-portal-signin]

Ange autentiseringsuppgifter för en användare i Azure Active Directory och klicka på **logga in**.

![Logga in][api-management-aad-signin]

Du kan uppmanas med ett registreringsformulär om ytterligare information krävs. Fyll i registreringsformuläret och på **registrera**.

![Registrering][api-management-complete-registration]

Dina användare är nu inloggad i developer-portalen för din API Management service-instans.

![Registreringen har slutförts][api-management-registration-complete]

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

[Log in to the Developer portal using an Azure Active Directory account]: #Log-in-to-the-Developer-portal-using-an-Azure-Active-Directory-account
