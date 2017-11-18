---
title: "Så här konfigurerar du autentisering och auktorisering för ett anpassat program att anropa Azure tid serien Insights API | Microsoft Docs"
description: "Den här artikeln beskriver hur du konfigurerar autentisering och auktorisering för ett anpassat program som anropar Azure tid serien Insights API."
services: time-series-insights
ms.service: time-series-insights
author: dmdenmsft
ms.author: dmden
manager: jhubbard
editor: MicrosoftDocs/tsidocs
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.devlang: csharp
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: a1d364b0dcb70998b390d0d96bcd1e439617e493
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="authentication-and-authorization-for-azure-time-series-insights-api"></a>Autentisering och auktorisering för Azure tid serien Insights API

Den här artikeln förklarar hur du konfigurerar autentisering och auktorisering som används i ett anpassat program som anropar Azure tid serien Insights API.

## <a name="service-principal"></a>Tjänstens huvudnamn

Det här avsnittet beskrivs hur du konfigurerar ett program åtkomst till tid serien Insights API för programmet. Programmet kan sedan fråga data eller publicera referensdata i tid serien insikter-miljö med autentiseringsuppgifter i stället för autentiseringsuppgifter.

När du har ett program som måste åtkomsttid serien insikter måste du ställa in ett Azure Active Directory-program och tilldelar åtkomstprinciper data i tid serien insikter-miljö. Den här metoden är bättre att köra appen enligt dina autentiseringsuppgifter eftersom:

* Du kan tilldela behörigheter till app-identitet som skiljer sig från din egen behörighet. Dessa behörigheter normalt begränsad till endast vilka appen kräver. Du kan exempelvis tillåta appen endast läsa data i en viss tid serien insikter miljö.
* Du behöver ändra appens autentiseringsuppgifterna ändrar dina ansvarsområden.
* Du kan använda ett certifikat eller en tangent för att automatisera autentisering när du kör ett oövervakat skript.

Det här avsnittet visar hur du utför dessa åtgärder via Azure-portalen. Den fokuserar på en enskild klient program där programmet är avsett att köras i en enda organisation. Stöd för en innehavare program används vanligtvis för line-of-business-program som körs i din organisation.

Installationsprogrammet flödet består av tre huvudsakliga steg:

1. Skapa ett program i Azure Active Directory.
2. Det här programmet behörighet att komma åt gången serien insikter-miljön.
3. Använda program-ID och nyckel för att hämta en token för den `"https://api.timeseries.azure.com/"` publik eller resursen. Token kan sedan användas för att anropa tid serien Insights API.

Här följer detaljerade anvisningar:

1. Välj i Azure-portalen **Azure Active Directory** > **App registreringar** > **nya appregistrering**.

   ![Ny programmet registrering i Azure Active Directory](media/authentication-and-authorization/active-directory-new-application-registration.png)  

2. Namnge programmet, Välj den typ som ska vara **webbapp / API**, Välj en giltig URI för **inloggnings-URL**, och klicka på **skapa**.

   ![Skapa programmet i Azure Active Directory](media/authentication-and-authorization/active-directory-create-web-api-application.png)

3. Välj ditt nya program och kopiera dess program-ID till valfri textredigerare.

   ![Kopiera program-ID](media/authentication-and-authorization/active-directory-copy-application-id.png)

4. Välj **nycklar**, ange nyckelnamnet, Välj upphör att gälla och klicka på **spara**.

   ![Välj programnycklar](media/authentication-and-authorization/active-directory-application-keys.png)

   ![Ange namn och upphör att gälla och klicka på Spara](media/authentication-and-authorization/active-directory-application-keys-save.png)

5. Kopiera nyckeln till valfri textredigerare.

   ![Kopiera nyckeln för programmet](media/authentication-and-authorization/active-directory-copy-application-key.png)

6. Tid serien insikter miljön, Välj **principerna dataåtkomst** och på **Lägg till**.

   ![Lägga till nya data åtkomstprincipen i tid serien insikter-miljön](media/authentication-and-authorization/time-series-insights-data-access-policies-add.png)

7. I den **Välj användare** dialogrutan, klistra in namnet på programmet (från steg 2) eller program-ID (från steg 3).

   ![Hitta ett program i dialogrutan Välj användare](media/authentication-and-authorization/time-series-insights-data-access-policies-select-user.png)

8. Välj rollen (**Reader** för datafrågor, **deltagare** för datafrågor och ändra referensdata) och klicka på **Ok**.

   ![Välj läsaren eller deltagare i dialogrutan Välj roll](media/authentication-and-authorization/time-series-insights-data-access-policies-select-role.png)

9. Spara principen genom att klicka på **Ok**.

10. Använd program-ID (från steg 3) och tangent (från steg 5) för att hämta token för programmet. Token kan sedan skickas i den `Authorization` huvud när programmet anropar tid serien Insights API.

    Om du använder C#, kan du använda följande kod för att hämta token för programmet. Ett komplett exempel finns [fråga data med hjälp av C#](time-series-insights-query-data-csharp.md).

    ```csharp
    var authenticationContext = new AuthenticationContext(
        "https://login.microsoftonline.com/common",
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

Använda program-ID och nyckel i ditt program för att autentisera med Azure tid serien insikter. 

## <a name="next-steps"></a>Nästa steg
- Exempelkod som anropar tid serien Insights API finns [fråga data med hjälp av C#](time-series-insights-query-data-csharp.md).
- API-Referensinformation finns i [frågan API-referens för](/rest/api/time-series-insights/time-series-insights-reference-queryapi).

> [!div class="nextstepaction"]
> [Skapa ett huvudnamn för tjänsten](../azure-resource-manager/resource-group-create-service-principal-portal.md)
