---
title: Självtestklient för att för validera en virtuell dator | Azure Marketplace
description: Så här skapar du en självtestklient för att förvalta en avbildning av virtuella datorer för Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: fb568400cb60f108303909353bfa703e98ab6157
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80286429"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Skapa en självtestklient för att för validera en virtuell Azure-avbildning

Använd den här artikeln som en guide för att skapa en klienttjänst som använder självtest-API:et. Du kan använda självtest-API:et för att för validera en virtuell dator (VM) för att säkerställa att den uppfyller de senaste publiceringskraven för Azure Marketplace. Med den här klienttjänsten kan du testa en virtuell dator innan du skickar in erbjudandet för Microsoft-certifiering.

## <a name="development-and-testing-overview"></a>Översikt över utveckling och testning

Som en del av självtestprocessen skapar du en lokal klient som ansluter till Azure Marketplace för att validera en virtuell dator som körs i din Azure-prenumeration. Den virtuella datorn kan köra Operativsystemet Windows eller Linux.

Den lokala klienten kör ett skript som autentiserar med självtest-API:et, skickar anslutningsinformation och tar emot testresultat.

Stegen på hög nivå för att skapa en självtestklient är:

1. Välj Azure Active Directory (AD) klient för ditt program.
2. Registrera klientappen.
3. Skapa en token för klientens Azure AD-app.
4. Skicka token till självtest-API:et.

När du har skapat klienten kan du testa den mot den virtuella datorn.

### <a name="self-test-client-authorization"></a>Självtestklientauktorisering

Följande diagram visar hur auktorisering fungerar för service till tjänstanrop med hjälp av klientautentiseringsuppgifter (delad hemlighet eller certifikat.)

![Klientauktoriseringsprocessen](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>Självtestklient-API:et

Självtest-API:et innehåller en enda slutpunkt som endast stöder POST-metoden.  Den har följande struktur.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: "application/json"
Authorization:   "Bearer xxxx-xxxx-xxxx-xxxxx"
Request body:    The Request body parameters should use the following JSON format:
                 {
                   "DNSName":"XXXX.westus.cloudapp.azure.com",
                   "User":"XXX",
                   "Password":"XXX@1234567",
                   "OS":"XXX",
                   "PortNo":"22",
                   "CompanyName":"ABCD",
                 }
```

I följande tabell beskrivs API-fälten.


|      Field         |    Beskrivning    |
|  ---------------   |  ---------------  |
|  Auktorisering     |  Strängen "Bearer xxxx-xxxx-xxxx-xxxxx" innehåller Azure Active Directory (AD) klienttoken, som kan skapas med PowerShell.          |
|  DNS-namn           |  DNS-namn på den virtuella datorn som ska testas    |
|  Användare              |  Användarnamn för att logga in på den virtuella datorn         |
|  lösenord          |  Lösenord för signering i den virtuella datorn          |
|  Operativsystem                |  Operativsystemet för den `Linux` virtuella datorn: antingen eller`Windows`          |
|  PortNo (På)            |  Öppna portnummer för anslutning till den virtuella datorn. Portnumret är `22` vanligtvis för `5986` Linux och för Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Använda API:et

Du kan använda självtest-API:et med PowerShell eller cURL.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Använda PowerShell för att använda API:et på Linux OS

Så här anropar du API:et i PowerShell:

1. Använd `Invoke-WebRequest` kommandot för att anropa API:et.
2. Metoden är Post och innehållstyp är JSON, som visas i följande kodexempel och skärmdump.
3. Ange brödtextparametrar i JSON-format.

I följande kodexempel visas ett PowerShell-anrop till API:et.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Linux"
    "PortNo" = "22"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```
Följande skärminspelning visar ett exempel för att anropa API:et i PowerShell.

![Anropa API med PowerShell för Linux OS](./media/stclient-call-api-ps-linuxvm.png)

Med hjälp av föregående exempel kan du hämta JSON och tolka den för att få följande information:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Följande skärmdump, som `$res.Content`visar , ger dig information om dina testresultat i JSON-format.

![JSON resultat från PowerShell samtal till Linux](./media/stclient-pslinux-rescontent-json.png)

Följande skärmdump visar ett exempel på JSON testresultat ses i en online JSON viewer (till exempel [Kod försköna](https://codebeautify.org/jsonviewer) eller [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON resultat från PowerShell-anrop till Linux VM](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Använda PowerShell för att använda API:et i Windows OS

Så här anropar du API:et i PowerShell:

1. Använd `Invoke-WebRequest` kommandot för att anropa API:et.
2. Metoden är Post och innehållstyp är JSON, som visas i följande kodexempel och skärmdump.
3. Skapa body-parametrarna i JSON-format.

I följande kodexempel visas ett PowerShell-anrop till API:et.

```powershell
$accesstoken = "Get token for your Client AAD App"
$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body = @{
    "DNSName" = "XXXX.westus.cloudapp.azure.com"
    "User" = "XXX"
    "Password" = "XXX@123456"
    "OS" = "Windows"
    "PortNo" = "5986"
    "CompanyName" = "ABCD"

} | ConvertTo-Json
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" -Headers $headers;
$Content = $res | ConvertFrom-Json
```

Följande skärminspelning visar ett exempel för att anropa API:et i PowerShell.

![Anropa API med PowerShell för Windows VM](./media/stclient-call-api-ps-windowsvm.png)

Med hjälp av föregående exempel kan du hämta JSON och tolka den för att få följande information:

```powershell
$testresult = ConvertFrom-Json -InputObject (ConvertFrom-Json -InputObject $res)

  Write-Host "OSName: $($testresult.OSName)"
  Write-Host "OSVersion: $($testresult.OSVersion)"
  Write-Host "Overall Test Result: $($testresult.TestResult)"

For ($i=0; $i -lt $testresult.Tests.Length; $i++)
{
    Write-Host "TestID: $($testresult.Tests[$i].TestID)"
    Write-Host "TestCaseName: $($testresult.Tests[$i].TestCaseName)"
    Write-Host "Description: $($testresult.Tests[$i].Description)"
    Write-Host "Result: $($testresult.Tests[$i].Result)"
    Write-Host "ActualValue: $($testresult.Tests[$i].ActualValue)"
}
```

Följande skärmdump, som `$res.Content`visar , ger dig information om dina testresultat i JSON-format.

![JSON:s resultat från PowerShell-anrop till Windows](./media/stclient-pswindows-rescontent-json.png)

Följande skärmdump visar testresultat som visas i en online-JSON-visning.
(till exempel [Kod försköna](https://codebeautify.org/jsonviewer), [JSON Viewer](https://jsonformatter.org/json-viewer))

![JSON:s resultat från PowerShell-anrop till Windows VM](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Använd cURL för att använda API:et på Linux OS

Så här anropar du API:et med cURL:

1. Använd kommandot curl för att anropa API:et.
2. Metoden är Post och innehållstyp är JSON, som visas i följande kodavsnitt.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Följande skärm visar ett exempel på hur du använder curl för att anropa API:et.

![Anropa API med kommandot Curl](./media/stclient-consume-api-curl.png)

Följande skärmdump visar JSON resultaten från curl samtalet.

![JSON resultat från curl samtal](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Välj Azure AD-klient för appen

Följ följande steg för att välja Azure AD-klienten där du vill skapa ditt program.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den övre menyraden väljer du ditt konto och väljer den Active Directory-klientorganisation där du vill registrera programmet under kataloglistan. Du kan också välja ikonen **Katalog + Prenumeration** för att se filtret Global prenumeration. Följande skärmdump visar ett exempel på det här filtret.

   ![Välj prenumerationsfilter](./media/stclient-subscription-filter.png)

3. I det vänstra navigeringsfältet väljer du **Alla tjänster** och väljer sedan Azure **Active Directory**.

   I följande steg kan du behöva klientnamnet (eller katalognamnet) eller klient-ID (eller katalog-ID).

   **Så här hämtar du klientinformation:**

   Sök efter "Egenskaper" i **Azure Active Directory Översikt**och välj sedan **Egenskaper**. Använda följande skärminspelning som exempel:

   - **Namn** - Klientnamnet eller katalognamnet
   - **Katalog-ID** - Klient-ID eller katalog-ID eller använd rullningslisten för att hitta egenskaper.

   ![Egenskapssida för Azure Active Directory](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrera klientappen

Använd följande steg för att registrera klientappen.

1. I det vänstra navigeringsfältet väljer du **Alla tjänster** och väljer sedan **Appregistreringar**.
2. Under **Appregistreringar**väljer du **+ Ny programregistrering**.
3. Under **Skapa**anger du den information som krävs för följande fält:

   - **Namn** - Ange ett eget namn för appen. Till exempel "SelfTestClient".
   - **Programtyp** - Välj **webbapp/API**
   - **Inloggnings-URL** - Skriv\/"https: /isvapp.azurewebsites.net/selftest-vm"

4. Välj **Skapa**.
5. Under **Appregistreringar** eller **Registrerad app**kopierar du **program-ID: t**.

   ![Hämta program-ID:et](./media/stclient-app-id.png)

6. Välj **Inställningar**i det registrerade appverktygsfältet .
7. Välj **Behörigheter som krävs** för att konfigurera behörigheter för ditt program.
8. Under **Obligatoriska behörigheter**väljer du **+ Lägg till**.
9. Under **Lägg till API-åtkomst**väljer du **Välj ett API**.
10. Under **Välj ett API**skriver du "Windows Azure classic deployment model" för att söka efter API:et.
11. I sökresultaten väljer du **Windows Azure classic deployment model** och klickar sedan på **Välj**.

    ![Konfigurera flera innehavare för app](./media/stclient-select-api.png)

12. Under **Lägg till API-åtkomst**väljer **du Välj behörigheter**.
13. Välj **Åtkomst till "Windows Azure Service Management API"**.

    ![Aktivera API-åtkomst för app](./media/stclient-enable-api-access.png)

14. Klicka på **Markera**.
15. Välj **Done** (Klar).
16. Under **Inställningar** väljer du **Egenskaper**.
17. Under **Egenskaper**bläddrar du ned till **Flera innehavare**. Välj **Ja**.

    ![Konfigurera flera innehavare för app](./media/stclient-yes-multitenant.png)

18. Välj **Spara**.
19. Under **Inställningar**väljer du **Nycklar**.
20. Skapa en hemlig nyckel genom att välja textrutan **Nyckelbeskrivning.** Konfigurera följande fält:

    - Skriv in ett nyckelnamn. Till exempel "självtestklient"
    - I **listrutan FÖR UTGÅNGAR** väljer du "I 1 år".
    - Välj **Spara** om du vill generera nyckeln.
    - Kopiera nyckeln under **VÄRDE.**

      >[!Important]
      >Du kan inte se nyckelvärdet när du har avslutat formuläret **Nycklar.**

    ![Formulär för nyckelvärde](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Skapa token för klientappen

Du kan använda något av följande program för att skapa och hämta en token med OAuth REST API:

- Postman
- cURL i Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Så här skapar och hämtar du en token med Postman

 Om du vill be Auth0 om token för någon av [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) dina auktoriserade program utför du en POST-åtgärd till slutpunkten med en nyttolast i följande format:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Skicka följande parametrar i brödtexten Begär:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Skicka följande parametrar i begäranden:

```
Content-Type: application/x-www-form-urlencoded
```

Följande skärmdump visar ett exempel på hur du använder Postman för att hämta en token.

![Hämta token med Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Så här skapar och hämtar du en token med cURL i Linux

Om du vill be Auth0 om token för någon av [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) dina auktoriserade program utför du en POST-åtgärd till slutpunkten med en nyttolast i följande format:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Följande skärmdump visar ett exempel på hur du använder kommandot curl för att få en token.

![Hämta token med curl-kommando](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Så här skapar och hämtar du en token med C&#35;

Om du vill be Auth0 om token för någon av dina\/auktoriserade program utför du en POST-åtgärd till https: /soamtenant.auth0.com/oauth/token slutpunkt med en nyttolast i följande format:

```csharp
string clientId = "Your Application Id";
string clientSecret = "Your Application Secret";
string audience = "https://management.core.windows.net";
string authority = String.Format(System.Globalization.CultureInfo.InvariantCulture, "https://login.microsoftonline.com/common/oauth2/token");
string grantType = "client_credentials";
var client = new RestClient(authority);
var request = new RestRequest(Method.POST);
       request.AddHeader("content-type", "application/x-www-form- urlencoded");

string requestBody = "grant_type=" + grantType + "&" + "client_id=" +   clientId + "&" + "client_secret=" + clientSecret + "&" + "resource=" +  audience;

request.AddParameter("application/x-www-form-urlencoded", requestBody,  ParameterType.RequestBody);

IRestResponse response = client.Execute(request);
var content = response.Content;
var token = JObject.Parse(content)["access_token"];
```

### <a name="to-create-and-get-a-token-using-powershell"></a>Så här skapar och hämtar du en token med PowerShell

Om du vill be Auth0 om token för någon av dina\/auktoriserade program utför du en POST-åtgärd till https: /soamtenant.auth0.com/oauth/token slutpunkt med en nyttolast i följande format:

```powershell
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP "
$audience = "https://management.core.windows.net";
$authority = "https://login.microsoftonline.com/common/oauth2/token"
$grantType = "client_credentials";

$requestBody = "grant_type=" + $grantType + "&" + "client_id=" +  $clientId   + "&" + "client_secret=" + $clientSecret + "&" + "resource=" + $audience;

$headers = New-Object  "System.Collections.Generic.Dictionary[[String],[String]]"
             $headers.Add("ContentType", "application/x-www-form-urlencoded")
resp = Invoke-WebRequest -Method Post -Uri $authority -Headers $headers -ContentType 'application/x-www-form-urlencoded' -Body $requestBody

$token = $resp.Content | ConvertFrom-Json
$token.AccessToken
```

## <a name="pass-the-client-app-token-to-the-api"></a>Skicka klientapptoken till API:et

Skicka token till självtest-API:et med hjälp av följande kod i auktoriseringshuvudet:

```powershell
$redirectUri = 'https://isvapp.azurewebsites.net/selftest-vm'
$accesstoken = 'place your token here'

$headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
$headers.Add("Authorization", "Bearer $accesstoken")
$Body =
@{
      'DNSName'="XXXX.cloudapp.azure.com";
      'User'="XXX";
      'Password'="XXXX@12345";
      'OS'="Linux";
      'PortNo'="22"
  } | ConvertTo-Json

$result=Invoke-WebRequest -Method Post -Uri $redirectUri -Headers $headers -ContentType 'application/json' -Body $Body
$result
Write-Output 'Test Results:'
$result.Content
```

## <a name="test-your-self-test-client"></a>Testa din självtestklient

Så här testar du klienten:

1. Distribuera den virtuella datorn som du vill testa.
2. Anropa självtest-API:et med hjälp av klientapptoken för auktorisering.
3. Få testresultaten i JSON-format.

### <a name="test-result-examples"></a>Exempel på testresultat

Följande utdrag visar testresultat i JSON-format.

**Testresultat för en virtuell dator med Windows:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Microsoft Windows Server 2016 Datacenter",
  "OSVersion": "10.0.14393",
  "CreatedDate": "06/01/2018 07:48:04",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "Tes tID": "1.1.4",
      "TestCaseName": "OS Architecture",
      "Description": "Azure supports 64bit Operating System Only.",
      "Result": "Passed",
      "ActualValue": "64 Bit",
      "Re quiredValue": "OS Should be 64 bit"
    },
    {
      "TestID": "1.1.5",
      "TestCaseName": "User account dependency",
      "Description": "Application execution should not have de pendency on administrator account.",
      "Result": "Passed",
      "ActualValue": "isv",
      "RequiredValue": "Logged in user should not be an administrator"
    },
    {
      "TestID": " 1.1.6",
      "TestCaseName": "Failover Cluster",
      "Description": "Windows Server Failover Clustering feature is not yet supported, Application should not have dependency on this feature.",
      "Result": "Passed",
      "ActualValue": "Disabled",
      "RequiredValue": "Failover Clustering feature is disabled"
    },
```

**Testresultat för en virtuell Linux-dator:**

```json
{
  "SchemaVersion": 1,
  "AppCertificationCategory": "Microsoft Single VM Certification",
  "ProviderID": "050DE427-2A99-46D4-817C-5354D3BF2AE8",
  "OSName": "Ubuntu  16.04",
  "OSVersion": "16.04",
  "CreatedDate": "06/01/2018 07:04:24",
  "TestResult": "Pass",
  "APIVersion": "1.1",
  "Tests": [
    {
      "TestID": "48",
      "TestCaseName": "Bash Hi story",
      "Description": "Bash history files should be cleared before creating the VM image.",
      "Result": "Passed",
      "ActualValue": "No file Exist",
      "RequiredVal ue": "1024"
    },
    {
      "TestID": "39",
      "TestCaseName": "Linux Agent Version",
      "Description": "Azure Linux Agent 2.2.10 and above should be installed. ",
      "Result": "Pas sed",
      "ActualValue": "2.2.20",
      "RequiredValue": "2.2.10"
    },
    {
      "TestID": "40",
      "TestCaseName": "Required Kernel Parameters",
      "Description": "Verifies the following  kernel parameters are set console=ttyS0, earlyprintk=ttyS0, rootdelay=300",
      "Result": "Passed",
      "ActualValue": "Matched Parameter: console=ttyS0,earlypri ntk=ttyS0,rootdelay=300",
      "RequiredValue": "console=ttyS0#earlyprintk=ttyS0#rootdelay=300"
    },
```

## <a name="next-steps"></a>Nästa steg

När du har testat din virtuella Azure-dator kan du [publicera erbjudandet](./cpp-publish-offer.md).
