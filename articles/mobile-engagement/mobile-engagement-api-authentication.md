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
ms.openlocfilehash: b05181d9252c0a804648e01b4058019278ae5abe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="authenticate-with-mobile-engagement-rest-apis"></a>Autentisera med Mobile Engagement REST API: er
## <a name="overview"></a>Översikt
Det här dokumentet beskriver hur du skaffa en giltig AAD-Oauth-token för autentisering med Mobile Engagement REST-API: er. 

Det förutsätts att du har en giltig Azure-prenumeration och du har skapat en Mobile Engagement-app med en av våra [Developer självstudier](mobile-engagement-windows-store-dotnet-get-started.md).

## <a name="authentication"></a>Autentisering
Microsoft Azure Active Directory baserat OAuth-token används för autentisering. 

För att en API autentiseringsbegäran måste ett authorization-huvud läggas till varje begäran som har följande format:

    Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

> [!NOTE]
> Azure Active Directory-token upphör gälla inom en timme.
> 
> 

Det finns flera sätt att hämta en token. Eftersom API: erna kallas vanligtvis från en molnbaserad tjänst, som du vill använda en API-nyckel. En API-nyckel i Azure-terminologi kallas Service principal lösenord. Följande procedur beskriver ett sätt att ställa in manuellt.

### <a name="one-time-setup-using-script"></a>Enstaka installationen (med hjälp av skript)
Du bör följa anvisningarna nedan för att utföra installationen med hjälp av ett PowerShell-skript som tar Minimitiden för installationen, men använder mest tillåtna standardvärdena uppsättning. Alternativt kan du också följa instruktionerna i den [manuell installation](mobile-engagement-api-authentication-manual.md) för att göra detta från Azure-portalen direkt och vill ha bättre konfiguration. 

1. Hämta den senaste versionen av Azure PowerShell från [här](http://aka.ms/webpi-azps). Mer information om anvisningarna kan se detta [länk](/powershell/azure/overview).  
2. När Azure PowerShell har installerats kan använda följande kommandon för att säkerställa att du har den **Azure-modulen** installerad:
   
    a. Kontrollera att Azure PowerShell-modulen finns i listan över tillgängliga moduler. 
   
        Get-Module –ListAvailable 
   
    ![Tillgängliga Azure moduler][1]
   
    b. Om du inte hittar Azure PowerShell-modulen i listan ovan måste du köra följande:
   
        Import-Module Azure 
3. Logga in till Azure Resource Manager från PowerShell genom att köra följande kommando och ditt användarnamn och lösenord för kontot: 
   
        Login-AzureRmAccount
4. Om du har flera prenumerationer bör du köra följande:
   
    a. Hämta en lista över alla prenumerationer och kopiera prenumerations-ID för den prenumeration som du vill använda. Kontrollera att den här prenumerationen är samma som har Mobile Engagement-App som du kommer att interagera med hjälp av API: erna. 
   
        Get-AzureRmSubscription
   
    b. Kör följande kommando för att tillhandahålla SubscriptionId att konfigurera prenumerationen som ska användas.
   
        Select-AzureRmSubscription –SubscriptionId <subscriptionId>
5. Kopiera texten för den [ny AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) skript till din lokala dator och spara den som en PowerShell-cmdlet (t.ex. `APIAuth.ps1`) och kör den `.\APIAuth.ps1`. 
6. Skriptet blir du ombedd att ange indata för **huvudkontot**. Ange ett lämpligt namn som du vill använda för att skapa Active Directory-program (t.ex. APIAuth). 
7. När skriptet har slutförts visas följande fyra värden som du behöver för att autentisera programmässigt med AD så se till att kopiera dem. 
   
    **TenantId**, **SubscriptionId**, **ApplicationId**, och **hemlighet**.
   
    Du kommer att använda TenantId som `{TENANT_ID}`, ApplicationId som `{CLIENT_ID}` och hemliga som `{CLIENT_SECRET}`.
   
   > [!NOTE]
   > Säkerhetsprinciperna standard kan blockeras från att köra ett PowerShell-skript. I så fall, konfigurerar du tillfälligt din körningsprincipen för att tillåta körning av skript med hjälp av följande kommando:
   > 
   > Set-ExecutionPolicy RemoteSigned
   > 
   > 
8. Här är hur uppsättning PS-cmdlets skulle se ut. 
   
    ![][3]
9. Kontrollera i Azure-hanteringsportalen att ett nytt AD-program har skapats med det namn du angav i skriptet som kallas **Principalnamnet** under **Visa program som företaget äger**.
   
    ![][4]

#### <a name="steps-to-get-a-valid-token"></a>Steg för att hämta en giltig token
1. Anropa API: et med följande parametrar och Ersätt INNEHAVAREN\_, klient-ID\_-ID och klienten\_HEMLIGHET:
   
   * **URL-begäran** som *https://login.microsoftonline.com/ {klient\_ID} / oauth2/token*
   * **HTTP-innhållstyphuvud** som *program/x-www-formuläret-urlencoded*
   * **Brödtext i HTTP-begäran** som *bevilja\_typ = klient\_autentiseringsuppgifter & client_id = {klienten\_ID} & client_secret = {klienten\_HEMLIGHET} & resource=https%3A%2F%2Fmanagement.core.windows.net%2F*
     
     Följande är en exempelbegäran:
     
       POST / {TENANT_ID} / oauth2/token HTTP/1.1 värden: login.microsoftonline.com Content-Type: program/x-www-formuläret-urlencoded grant_type = client_credentials & client_id = {CLIENT_ID} & client_secret = {CLIENT_SECRET} & sammanstä spänningskälla = https % 3A % 2F%2Fmanagement.Core.Windows.NET%2F
     
     Här är ett exempelsvar:
     
       HTTP/1.1 200 OK Content-Type: application/json; charset = utf-8 Content-Length: 1234
     
       {”token_type”: ”ägar”, ”expires_in”: ”3599”, ”expires_on”: ”1445395811”, ”not_before” ”: 144 5391911” ”, resurs”: ”https://management.core.windows.net/”, ”access_token”: {ACCESS_TOKEN}}
     
     Det här exemplet ingår URL-kodning av parametrarna efter `resource` värdet är `https://management.core.windows.net/`. Var noga med att även URL koda `{CLIENT_SECRET}` som den kan innehålla specialtecken.
     
     > [!NOTE]
     > För att testa, kan du använda en HTTP-klientverktyg som [Fiddler](http://www.telerik.com/fiddler) eller [Chrome Postman tillägg](https://chrome.google.com/webstore/detail/postman/fhbjgbiflinjbdggehcddcbncdddomop) 
     > 
     > 
2. Innehåller nu begärandehuvudet auktorisering i varje API-anrop:
   
        Authorization: Bearer {ACCESS_TOKEN}
   
    Om du får ett 401 statuskoden Kontrollera svarstexten, den kan informera om token har upphört att gälla. I så fall får en ny token.

## <a name="using-the-apis"></a>Med hjälp av API: er
Nu när du har en giltig token är du redo att göra API-anrop.

1. I varje API-begäran behöver du ange en giltiga token som du hämtade i föregående avsnitt.
2. Behöver du ansluter vissa parametrar i URI som identifierar ditt program. Förfrågan URI ser ut ungefär så här
   
        https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/
        providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/
   
    Hämta parametrar, klicka på programmets namn och klicka på instrumentpanelen och du ser en sida som följande med parametrarna som 3.
   
   * **1** `{subscription-id}`
   * **2** `{app-collection}`
   * **3** `{app-resource-name}`
   * **4** din resursgruppens namn kommer att vara **MobileEngagement** om du har skapat en ny. 
     
     ![Mobile Engagement API URI-parametrar][2]

> [!NOTE]
> <br/>
> 
> 1. Ignorera API rot-adress som det var för tidigare API: er.<br/>
> 2. Om du har skapat programmet med den klassiska Azure-portalen måste du använda programmet resursnamnet som skiljer sig från namnet på programmet sig själv. Om du skapade appen i Azure Portal bör du använda namnet på appen sig själv (det finns ingen skillnad mellan programmets resursnamn och programnamn för appar som har skapats i den nya portalen).  
> 
> 

<!-- Images -->
[1]: ./media/mobile-engagement-api-authentication/azure-module.png
[2]: ./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png
[3]: ./media/mobile-engagement-api-authentication/ps-cmdlets.png
[4]: ./media/mobile-engagement-api-authentication/ad-app-creation.png



