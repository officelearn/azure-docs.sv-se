---
title: Självtest-klienten för att förverifiera en virtuell dator | Azure Marketplace
description: Så här skapar du en självtest-klient för för validering av en avbildning av en virtuell dator för Azure Marketplace.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: dsindona
ms.openlocfilehash: 9f16d26fa95254282e453cd7bf35d85f8b81ed73
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143199"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Skapa en självtest-klient för att förverifiera en avbildning av en virtuell Azure-dator

> [!IMPORTANT]
> Med början den 13 april 2020 kommer vi att börja flytta hanteringen av dina virtuella Azure-datorer till Partner Center. Efter migreringen kommer du att skapa och hantera dina erbjudanden i Partner Center. Följ anvisningarna i [Azures avbildnings certifiering för virtuella datorer](https://docs.microsoft.com/azure/marketplace/partner-center-portal/azure-vm-image-certification) för att hantera dina migrerade erbjudanden.

Använd den här artikeln som en guide för att skapa en klient tjänst som använder sig av API: t för självtest. Du kan använda API: et för självtest för att förverifiera en virtuell dator (VM) för att säkerställa att den uppfyller de senaste publicerings kraven för Azure Marketplace. Med den här klient tjänsten kan du testa en virtuell dator innan du skickar in ditt erbjudande för Microsoft-certifiering.

## <a name="development-and-testing-overview"></a>Översikt över utveckling och testning

Som en del av självtests processen skapar du en lokal klient som ansluter till Azure Marketplace för att validera en virtuell dator som körs i din Azure-prenumeration. Den virtuella datorn kan köra operativ systemet Windows eller Linux.

Den lokala klienten kör ett skript som autentiserar med API för självtest, skickar anslutnings information och tar emot test resultat.

De övergripande stegen för att skapa en självtests klient är:

1. Välj den Azure Active Directory-klient (AD) för ditt program.
2. Registrera klient programmet.
3. Skapa en token för klientens Azure AD-App.
4. Skicka token till test-API: et.

När du har skapat klienten kan du testa den mot den virtuella datorn.

### <a name="self-test-client-authorization"></a>Klient auktorisering för självtest

Följande diagram visar hur auktorisering fungerar för tjänst-till-tjänst-anrop med klientautentiseringsuppgifterna (delad hemlighet eller certifikat.)

![Process för klient godkännande](./media/stclient-dev-process.png)

## <a name="the-self-test-client-api"></a>API för självtests klienten

API för självtest innehåller en enda slut punkt som endast stöder POST-metoden.  Den har följande struktur.

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
|  Auktorisering     |  Strängen "Bearer xxxx-xxxx-xxxx-xxxxx" innehåller den Azure Active Directory (AD)-klient-token som kan skapas med hjälp av PowerShell.          |
|  DNSName           |  DNS-namnet på den virtuella dator som ska testas    |
|  Användare              |  Användar namn för att logga in på den virtuella datorn         |
|  lösenordsinställning          |  Lösen ord för att logga in på den virtuella datorn          |
|  Operativsystem                |  Operativ systemet på den virtuella datorn: `Linux` antingen eller`Windows`          |
|  PortNo            |  Öppna port nummer för att ansluta till den virtuella datorn. Port numret är vanligt vis `22` för Linux och `5986` för Windows.          |
|  |  |

## <a name="consuming-the-api"></a>Använda API: et

Du kan använda test-API: et med PowerShell eller sväng.

### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Använd PowerShell för att använda API: et på Linux OS

Följ dessa steg om du vill anropa API: t i PowerShell:

1. Använd `Invoke-WebRequest` kommandot för att anropa API: et.
2. Metoden är post och Content-Type är JSON, som du ser i följande kod exempel och skärmdump.
3. Ange Body-parametrarna i JSON-format.

I följande kod exempel visas ett PowerShell-anrop till API: et.

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
Följande skärm bild visar ett exempel på hur du anropar API: et i PowerShell.

![Anropa API med PowerShell för Linux OS](./media/stclient-call-api-ps-linuxvm.png)

I föregående exempel kan du hämta JSON och parsa den för att få följande information:

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

Följande skärmdump, som visas `$res.Content`, ger dig information om dina test resultat i JSON-format.

![JSON-resultat från PowerShell-anrop till Linux](./media/stclient-pslinux-rescontent-json.png)

Följande skärm bild visar ett exempel på JSON-testresultat som visas i en online-JSON-visningsprogrammet (till exempel [kod beautify](https://codebeautify.org/jsonviewer) eller [JSON Viewer](https://jsonformatter.org/json-viewer)).

![JSON-resultat från PowerShell-anrop till virtuell Linux-dator](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Använd PowerShell för att använda API: et på Windows OS

Följ dessa steg om du vill anropa API: t i PowerShell:

1. Använd `Invoke-WebRequest` kommandot för att anropa API: et.
2. Metoden är post och Content-Type är JSON, som du ser i följande kod exempel och skärmdump.
3. Skapa Body-parametrarna i JSON-format.

I följande kod exempel visas ett PowerShell-anrop till API: et.

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

Följande skärm bild visar ett exempel på hur du anropar API: et i PowerShell.

![Anropa API med PowerShell för virtuell Windows-dator](./media/stclient-call-api-ps-windowsvm.png)

I föregående exempel kan du hämta JSON och parsa den för att få följande information:

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

Följande skärmdump, som visas `$res.Content`, ger dig information om dina test resultat i JSON-format.

![JSON-resultat från PowerShell-anrop till Windows](./media/stclient-pswindows-rescontent-json.png)

Följande skärm bild visar de test resultat som visas i en online-JSON-visningsprogrammet.
(till exempel [kod beautify](https://codebeautify.org/jsonviewer), [JSON Viewer](https://jsonformatter.org/json-viewer))

![JSON-resultat från PowerShell-anrop till virtuell Windows-dator](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Använd en sväng för att använda API: et på Linux OS

Följ dessa steg om du vill anropa API: t med hjälp av sväng:

1. Använd kommandot vänd för att anropa API: et.
2. Metoden är post och Content-Type är JSON, vilket visas i följande kodfragment.

```
CURL POST -H "Content-Type:application/json"
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX"
https://isvapp.azurewebsites.net/selftest-vm
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'
```

Följande skärm bild visar ett exempel på hur du använder en sväng för att anropa API: et.

![Anropa API med kommandot spiral](./media/stclient-consume-api-curl.png)

Följande skärm bild visar JSON-resultatet från ett spiral samtal.

![JSON-resultat från sväng samtal](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Välj Azure AD-klient för appen

Använd följande steg för att välja den Azure AD-klient där du vill skapa ditt program.

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. På den översta meny raden väljer du ditt konto och under listan katalog väljer du den Active Directory klient organisation där du vill registrera programmet. Alternativt kan du välja ikonen **katalog + prenumeration** för att se det globala prenumerations filtret. Följande skärm bild visar ett exempel på det här filtret.

   ![Välj prenumerations filtret](./media/stclient-subscription-filter.png)

3. Välj **alla tjänster** i det vänstra navigerings fältet och välj sedan **Azure Active Directory**.

   I följande steg kan du behöva klient namnet (eller katalog namnet) eller klient-ID: t (eller katalog-ID).

   **Så här hämtar du klient information:**

   I **Azure Active Directory översikt**, Sök efter "egenskaper" och välj sedan **Egenskaper**. Använd följande skärm bild som exempel:

   - **Namn** – namnet på klient organisationen eller katalogen
   - **Katalog-ID** – klient-ID eller katalog-ID eller Använd rullnings listen för att hitta egenskaper.

   ![Sidan Azure Active Directory egenskaper](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrera klient programmet

Använd följande steg för att registrera klient programmet.

1. Välj **alla tjänster** i det vänstra navigerings fältet och välj sedan **Appregistreringar**.
2. Under **Appregistreringar**väljer du **+ ny program registrering**.
3. Under **skapa**anger du den information som krävs för följande fält:

   - **Namn** – ange ett eget namn för appen. Till exempel "SelfTestClient".
   - **Program typ** – Välj **webbapp/API**
   - **Inloggnings-URL** – typ "https:\//isvapp.azurewebsites.net/SelfTest-VM"

4. Välj **Skapa**.
5. Kopiera **program-ID: t**under **Appregistreringar** eller **registrerad app**.

   ![Hämta program-ID: t](./media/stclient-app-id.png)

6. I verktygsfältet registrerad app väljer du **Inställningar**.
7. Välj **nödvändiga behörigheter** för att konfigurera behörigheter för ditt program.
8. Under **nödvändiga behörigheter**väljer du **+ Lägg till**.
9. Under **Lägg till API-åtkomst**väljer du **Välj ett API**.
10. Under **Välj ett API**skriver du "Windows Azure klassisk distributions modell" för att söka efter API: et.
11. I Sök resultaten väljer du den **klassiska distributions modellen för Windows Azure** och klickar sedan på **Välj**.

    ![Konfigurera flera innehavare för appen](./media/stclient-select-api.png)

12. Under **Lägg till API-åtkomst**väljer du **Välj behörigheter**.
13. Välj **åtkomst till Windows Azure-Service Management-API**.

    ![Aktivera API-åtkomst för appen](./media/stclient-enable-api-access.png)

14. Klicka på **Välj**.
15. Välj **Klar**.
16. Under **Inställningar** väljer du **Egenskaper**.
17. Rulla ned till **flera klienter**under **Egenskaper**. Välj **Ja**.

    ![Konfigurera flera innehavare för appen](./media/stclient-yes-multitenant.png)

18. Välj **Spara**.
19. Under **Inställningar**väljer du **nycklar**.
20. Skapa en hemlig nyckel genom att välja text rutan nyckel **Beskrivning** . Konfigurera följande fält:

    - Ange ett nyckel namn. Till exempel "selftestclient"
    - Välj "i 1 år" i list rutan **upphör att gälla** .
    - Klicka på **Spara** för att generera nyckeln.
    - Under **värde**, kopierar du nyckeln.

      >[!Important]
      >Du kommer inte att kunna se nyckel värdet när du har avslutat formuläret **nycklar** .

    ![Nyckel värdes formulär](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Skapa token för klient programmet

Du kan använda något av följande program för att skapa och hämta en token med hjälp av OAuth-REST API:

- Postman
- Sväng i Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Skapa och hämta en token med Postman

 Om du vill ställa en Auth0 för token för alla auktoriserade program utför du en POST- [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) åtgärd till slut punkten med en nytto Last i följande format:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```

Överför följande parametrar i begär ande texten:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Överför följande parametrar i begär ande huvudet:

```
Content-Type: application/x-www-form-urlencoded
```

Följande skärm bild visar ett exempel på hur Postman används för att hämta en token.

![Hämta token med Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Skapa och hämta en token med hjälp av sväng i Linux

Om du vill ställa en Auth0 för token för alla auktoriserade program utför du en POST- [https://login.microsoftonline.com/common/oauth2/token](https://login.microsoftonline.com/common/oauth2/token) åtgärd till slut punkten med en nytto Last i följande format:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Följande skärm bild visar ett exempel på hur du använder kommandot vänd för att hämta en token.

![Hämta token med kommandot spiral](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Skapa och hämta en token med C&#35;

Om du vill ställa en Auth0 för token för alla auktoriserade program utför du en POST åtgärd till https\/:/soamtenant.Auth0.com/OAuth/token-slutpunkten med en nytto Last i följande format:

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

### <a name="to-create-and-get-a-token-using-powershell"></a>Skapa och hämta en token med PowerShell

Om du vill ställa en Auth0 för token för alla auktoriserade program utför du en POST åtgärd till https\/:/soamtenant.Auth0.com/OAuth/token-slutpunkten med en nytto Last i följande format:

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

## <a name="pass-the-client-app-token-to-the-api"></a>Skicka klientens app-token till API: et

Skicka token till test-API: et med följande kod i Authorization-huvudet:

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

## <a name="test-your-self-test-client"></a>Testa din självtests klient

Följ dessa steg om du vill testa klienten:

1. Distribuera den virtuella dator som du vill testa.
2. Anropa API: et för självtest med din klient app-token för auktorisering.
3. Hämta test resultatet i JSON-format.

### <a name="test-result-examples"></a>Exempel på test resultat

Följande kodfragment visar test resultat i JSON-format.

**Test resultat för en virtuell Windows-dator:**

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

**Test resultat för en virtuell Linux-dator:**

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
