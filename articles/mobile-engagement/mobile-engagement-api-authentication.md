---
title: 'Autentisera med Mobile Engagement REST API: er'
description: 'Beskriver hur du autentiserar med Azure Mobile Engagement REST API: er'
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: da82cb36-957a-4e19-a805-b44733cf6597
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 10/05/2016
ms.author: wesmc;ricksal
ms.openlocfilehash: 574e699a1cfca2caef0cf20872570bbb8650117b
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2018
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autentisera med Mobile Engagement REST API: er

## <a name="overview"></a>Översikt

Det här dokumentet beskriver hur du hämta en giltig Azure Active Directory (AD Azure) OAuth-token för autentisering med Mobile Engagement REST-API: er.

Den här proceduren förutsätter att du har en giltig Azure-prenumeration och har skapat en Mobile Engagement-app med någon av de [developer självstudier](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autentisering

En Microsoft Azure Active Directory-baserad OAuth-token används för autentisering. 

Ett authorization-huvud måste läggas till varje begäran för att autentisera en API-begäran. Authorization-huvud är i följande format:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory-token ut om en timme.
> 
> 

Det finns flera sätt att hämta en token. Eftersom API: er anropas från en molnbaserad tjänst, som du vill använda en API-nyckel. En API-nyckel i Azure-terminologi kallas ett huvudnamn för tjänsten lösenord. Följande procedur beskriver ett sätt att konfigurera den manuellt.

### <a name="one-time-setup-using-a-script"></a>Enstaka installationen (med ett skript)

Vidta åtgärder i följande instruktioner för att utföra installationen genom att använda ett PowerShell-skript. Ett PowerShell-skript kräver den minsta mängden tid för installationen, men använder mest tillåtna standardvärdena. 

Alternativt kan du också följa instruktionerna i den [manuell installation](mobile-engagement-api-authentication-manual.md) för att göra detta direkt från Azure-portalen. Du kan göra en mer detaljerad konfiguration när du ställer in från Azure-portalen.

1. Hämta den senaste versionen av Azure PowerShell med [ladda ned den](http://aka.ms/webpi-azps). Mer information om anvisningarna finns [översikten](/powershell/azure/overview).

2. När PowerShell har installerats kan använda följande kommandon för att säkerställa att du har den **Azure-modulen** installerad:

    a. Kontrollera att Azure PowerShell-modulen finns i listan över tillgängliga moduler.

        Get-Module –ListAvailable

    ![Tillgängliga Azure moduler][1]

    b. Om du inte hittar Azure PowerShell-modulen i listan ovan, måste du köra:

        Import-Module Azure
3. Logga in till Azure Resource Manager via PowerShell genom att köra följande kommando. Ange användarnamn och lösenord för kontot: 

        Login-AzureRmAccount
4. Om du har flera prenumerationer, gör du följande:

    a. Hämta en lista över alla prenumerationer. Kopiera den **SubscriptionId** för den prenumeration som du vill använda. Kontrollera att den här prenumerationen har Mobile Engagement-app. Du ska använda appen för att interagera med API: erna. 

        Get-AzureRmSubscription

    b. Kör följande kommando. Ange den **SubscriptionId** så här konfigurerar du den prenumeration som du ska använda:

        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Kopiera texten för den [ny AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) skript för att den lokala datorn. Spara den som en PowerShell-cmdlet (till exempel `APIAuth.ps1`), och kör den.

         `.\APIAuth.ps1`.

6. Skriptet begär du ange indata för **huvudkontot**. Ange ett lämpligt namn som du vill använda för ditt Active Directory-program (till exempel APIAuth). 

7. När skriptet är klar visas följande fyra värdena. Glöm inte att kopiera dem, eftersom du behöver dem för att autentisera programmässigt med Active Directory: 

   - **TenantId**
   - **Prenumerations-ID**
   - **ApplicationId**
   - **Hemlighet**

   Du använder TenantId som `{TENANT_ID}`, ApplicationId som `{CLIENT_ID}` och hemliga som `{CLIENT_SECRET}`.

   > [!NOTE]
   > Säkerhetsprinciperna standard kanske hindrar dig från att köra PowerShell-skript. I så fall använder du följande kommando tillfälligt konfigurera din körningsprincipen för att tillåta körning av skript:
   > 
   > Set-ExecutionPolicy RemoteSigned
8. Här är hur uppsättning PowerShell-cmdlets för ser ut.
    ![PowerShell-cmdletar][3]
9. I Azure-portalen går du till Active Directory, Välj **App registreringar**, och sedan söker du efter din app så att den finns.
    ![Sök efter appen][4]

### <a name="steps-to-get-a-valid-token"></a>Steg för att hämta en giltig token

1. Anropa API: et med följande parametrar. Ersätt **klient\_ID**, **klienten\_ID**, och **klienten\_HEMLIGHET**:
   
   * **URL-begäran** som`https://login.microsoftonline.com/{TENANT_ID}/oauth2/token`

   * **HTTP Content-Type-huvud** som`application/x-www-form-urlencoded`
   
   * **HTTP-begäran brödtext** som`grant_type=client\_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&resource=https%3A%2F%2Fmanagement.core.windows.net%2F`
     
    Följande är en exempelbegäran:
    ```
    POST /{TENANT_ID}/oauth2/token HTTP/1.1
    Host: login.microsoftonline.com
    Content-Type: application/x-www-form-urlencoded
    grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
    urce=https%3A%2F%2Fmanagement.core.windows.net%2F
    ```
    Följande är ett exempelsvar:
    ```
    HTTP/1.1 200 OK
    Content-Type: application/json; charset=utf-8
    Content-Length: 1234

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
    5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_TOKEN}}
    ```
     Det här exemplet innehåller URL-kodning av parametrarna efter där `resource` värdet är `https://management.core.windows.net/`. Var noga med att även URL-koda `{CLIENT_SECRET}`eftersom den kan innehålla specialtecken.

     > [!NOTE]
     > För att testa, kan du använda en HTTP-klientverktyg som [Fiddler](http://www.telerik.com/fiddler) eller [Chrome Postman tillägget](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop). 
     > 
     > 
2. Innehåller nu begärandehuvudet auktorisering i varje API-anrop:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Om din begäran returnerar statuskod 401, kontrollera svarstexten. Det kan informera om token har upphört att gälla. I så fall får en ny token.

## <a name="use-the-apis"></a>API: er i
Nu när du har en giltig token är du redo att göra API-anrop.

1. Du måste skicka en giltiga token i varje API-begäran. Du kan hämta det återstående token i föregående avsnitt.

2. Anslut vissa parametrar till URI som identifierar ditt program. Förfrågan URI som ser ut som följande kod:
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Välj programnamnet för att hämta parametrar. Välj sedan **instrumentpanelen**. Du ser en sida med alla tre parametrar:
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** din resursgruppens namn kommer att vara **MobileEngagement** om du har skapat en ny. 

> [!NOTE]
> Ignorera API rot-adress, eftersom det var för tidigare API: er.
> 
> Om du har skapat appen med hjälp av Azure-portalen måste du använda programmet resursnamnet, vilket skiljer sig från namnet på appen sig själv. Om du skapade appen i Azure-portalen, bör du använda namnet på appen. (Det finns ingen skillnad mellan programmets resursnamn och namnet på appen för appar som skapas i den nya portalen.)
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/search-app.png



