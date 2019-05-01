---
title: 'Så här att autentisera och auktorisera av API: et i Azure Time Series Insights | Microsoft Docs'
description: 'Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar API: et för Azure Time Series Insights.'
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: anshan
manager: cshankar
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: seodec18
ms.openlocfilehash: 66a3c40bf1e1e1dc6253520a555e19ebf011297c
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64698574"
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure Time Series Insights API

Den här artikeln förklarar hur du konfigurerar autentisering och auktorisering som används i ett anpassat program som anropar API: et för Azure Time Series Insights.

## <a name="service-principal"></a>Tjänstens huvudnamn

Det här avsnittet beskrivs hur du konfigurerar ett program för att få åtkomst till API: T för Time Series Insights åt programmet. Programmet kan sedan fråga efter data eller publicera referensdata i Time Series Insights-miljö med autentiseringsuppgifter för program i stället för användarens autentiseringsuppgifter.

När du har ett program som måste åtkomst Time Series Insights, måste du konfigurera ett Azure Active Directory-program och tilldelar principerna för dataåtkomst i Time Series Insights-miljö. Den här metoden är bättre att köra appen enligt dina autentiseringsuppgifter eftersom:

* Du kan tilldela behörigheter till appen identiteten som skiljer sig från dina egna behörigheter. Normalt är behörigheterna begränsade till bara vad appen kräver. Du kan till exempel Tillåt att appen endast kan läsa data i en viss Time Series Insights-miljö.
* Du behöver inte ändra dess autentiseringsuppgifter ändrar dina ansvarsområden.
* Du kan använda ett certifikat eller en programnyckel för att automatisera autentisering när du kör ett oövervakat skript.

Den här artikeln visar hur du utför de här stegen via Azure portal. Den fokuserar på en enda klient program där programmet är avsett att köras i endast en organisation. Du använder vanligtvis en enda klient program för line-of-business-program som körs i din organisation.

Installationsprogrammet flödet består av tre steg på hög nivå:

1. Skapa ett program i Azure Active Directory.
2. Tillåter programmet att få åtkomst till Time Series Insights-miljö.
3. Använder det program-ID och nyckel för att hämta en token för den `"https://api.timeseries.azure.com/"` publik eller resursen. Token kan sedan användas för att anropa API: T för Time Series Insights.

Här följer de detaljerade stegen:

1. I Azure-portalen väljer du **Azure Active Directory** > **appregistreringar** > **ny programregistrering**.

   ![Ny programregistrering i Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Ge programmet ett namn, Välj den typ som ska vara **webbapp / API**, Välj en giltig URI för **inloggnings-URL**, och klicka på **skapa**.

   ![Skapa programmet i Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Välj ditt nya program och kopiera dess program-ID till valfri textredigerare.

   ![Kopiera program-ID](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Välj **nycklar**, ange nyckelnamnet, Välj förfallodatum och klicka på **spara**.

   ![Välj nycklar för programmet](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Ange namn och upphör att gälla och klicka på Spara](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Kopiera nyckeln till valfri textredigerare.

   ![Kopiera programnyckeln](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Time Series Insights-miljö, Välj **Dataåtkomstprinciper** och klicka på **Lägg till**.

   ![Lägg till ny policy för åtkomst till Time Series Insights-miljö](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. I den **Välj användare** dialogrutan, klistra in namnet på programmet (från steg 2) eller program-ID (från steg 3).

   ![Hitta ett program i dialogrutan Välj användare](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Välj rollen (**läsare** för att fråga efter data, **deltagare** för datafrågor och ändra referensdata) och klicka på **Ok**.

   ![Välj läsare eller deltagare i dialogrutan Välj roll](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Spara principen genom att klicka på **Ok**.

10. Använd program-ID (från steg 3) och programnyckel (från steg 5) för att hämta token för programmet. Token kan sedan skickas i den `Authorization` rubrik när programmet anropar API: T för Time Series Insights.

    Om du använder C#, kan du använda följande kod för att hämta token för programmet. Ett komplett exempel finns i [fråga data med C#](time-series-insights-query-data-csharp.md).

    ```csharp
    // Enter your Active Directory tenant domain name
    var tenant = "YOUR_AD_TENANT.onmicrosoft.com";
    var authenticationContext = new AuthenticationContext(
        $"https://login.microsoftonline.com/{tenant}",
        TokenCache.DefaultShared);

    AuthenticationResult token = await authenticationContext.AcquireTokenAsync(
        // Set the resource URI to the Azure Time Series Insights API
        resource: "https://api.timeseries.azure.com/", 
        clientCredential: new ClientCredential(
            // Application ID of application registered in Azure Active Directory
            clientId: "1bc3af48-7e2f-4845-880a-c7649a6470b8", 
            // Application key of the application that's registered in Azure Active Directory
            clientSecret: "aBcdEffs4XYxoAXzLB1n3R2meNCYdGpIGBc2YC5D6L2="));

    string accessToken = token.AccessToken;
    ```

Använda program-ID och nyckel i ditt program för att autentisera med Azure Time Series Insight. 

## <a name="next-steps"></a>Nästa steg

- Exempelkod som anropar API: T för Time Series Insights, se [fråga data med C#](time-series-insights-query-data-csharp.md).

- API-Referensinformation finns i [fråge-API-referens](/rest/api/time-series-insights/ga-query-api).

- Lär dig hur du [skapa ett huvudnamn för tjänsten](../active-directory/develop/howto-create-service-principal-portal.md).