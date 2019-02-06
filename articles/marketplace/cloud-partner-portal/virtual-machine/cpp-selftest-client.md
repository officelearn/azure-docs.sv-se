---
title: Själv testa klienten att kontrollera en virtuell dator – Azure Marketplace | Microsoft Docs
description: Så här skapar du en själv testa klient för att verifiera före en avbildning av virtuell dator för Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal, Virtual Machine
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: pbutlerm
ms.openlocfilehash: 7afa64ebedb38b4514bbd155bf8f29268d420d18
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2019
ms.locfileid: "55745766"
---
# <a name="create-a-self-test-client-to-pre-validate-an-azure-virtual-machine-image"></a>Skapa en själv testa klient om du vill kontrollera en Azure VM-avbildning

Använd den här artikeln som en vägledning för att skapa en klienttjänsten som förbrukar själv testa API: et. Du kan använda API: et själv testa för att kontrollera en virtuell dator (VM) för att säkerställa att de uppfyller de senaste publicering kraven för Azure Marketplace. Den här klienttjänsten kan du testa en virtuell dator innan du skickar in ditt erbjudande för Microsoft-certifiering.


## <a name="development-and-testing-overview"></a>Utveckling och testning översikt

Som en del av processen själv testa, ska du skapa en lokal klient som ansluter till Azure Marketplace för att verifiera en virtuell dator som körs i din Azure-prenumeration. Den virtuella datorn kan köra Windows eller Linux-operativsystem.

Den lokala klienten kör ett skript som autentiserar med själv testa API: et, skickar anslutningsinformation och tar emot testresultaten.

Anvisningar för att skapa en själv testa klient är:

1. Välj Azure Active Directory (AD)-klient för ditt program.
2. Registrera klientappen.
3. Skapa en token för klienten Azure AD-app.
4. Skicka token själv testa API: et.

När du har skapat klienten kan testa du den mot den virtuella datorn.


### <a name="self-test-client-authorization"></a>Själv testa klientautentisering

Följande diagram visar hur auktorisering fungerar för tjänst till tjänst-anrop med klientautentiseringsuppgifter (delad hemlighet eller certifikat).

![Processen för klient-auktorisering](./media/stclient-dev-process.png)


## <a name="the-self-test-client-api"></a>Klienten själv testa API

Själv testa API: et innehåller en enda slutpunkt som stöder endast POST-metoden.  Det har följande struktur.

```
Uri:             https://isvapp.azurewebsites.net/selftest-vm
Method:          Post
Request Header:  Content-Type: “application/json”
Authorization:   “Bearer xxxx-xxxx-xxxx-xxxxx”
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

I följande tabell beskrivs de API-fält.


|      Fält         |    Beskrivning    |
|  ---------------   |  ---------------  |
|  Auktorisering     |  Strängen ”ägar xxxx-xxxx-xxxx-xxxxx” innehåller Azure Active Directory (AD) klient-token som kan skapas med hjälp av PowerShell.          |
|  DNS-namn           |  DNS-namnet på den virtuella datorn att testa    |
|  Användare              |  Användarnamn för att logga in på den virtuella datorn         |
|  Lösenord          |  Lösenord för att logga in på den virtuella datorn          |
|  Operativsystem                |  Operativsystemet på den virtuella datorn: antingen `Linux` eller `Windows`          |
|  PortNo            |  Öppna portnummer för att ansluta till den virtuella datorn. Portnumret är vanligtvis `22` för Linux och `5986` för Windows.          |
|  |  |


## <a name="consuming-the-api"></a>Använda API: et

Du kan använda själv testa API: et med PowerShell eller cURL.


### <a name="use-powershell-to-consume-the-api-on-the-linux-os"></a>Använd PowerShell för att använda API: et på Linux-operativsystem

Följ dessa steg för att anropa API: et i PowerShell:

1. Använd den `Invoke-WebRequest` kommando för att anropa API: et.
2. Metoden är Post innehållstypen är JSON, enligt följande kod exempel och skärmen insamlingen.
3. Ange parametrarna brödtext i JSON-format.

I följande kodexempel visas ett PowerShell-anrop till API: et.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers; 
$Content = $res | ConvertFrom-Json
```
Följande skärmdump visar ett exempel för att anropa API: et i PowerShell.

![Anropa API: et med PowerShell för Linux OS](./media/stclient-call-api-ps-linuxvm.png)

Med det tidigare exemplet kan du hämta JSON och parsa den för att hämta följande information:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

Följande skärmdump som visar `$res.Content`, får du information om testresultaten i JSON-format.

![JSON-resultat från PowerShell-anrop till Linux](./media/stclient-pslinux-rescontent-json.png)

Följande skärmdump visar ett exempel på JSON-testresultaten som visas i en online JSON-visare (till exempel [kod tjärgropar](https://codebeautify.org/jsonviewer) eller [JSON-visare](https://jsonformatter.org/json-viewer)).

![JSON-resultat från PowerShell-anrop till Linux VM](./media/stclient-consume-api-pslinux-json.png)

### <a name="use-powershell-to-consume-the-api-on-the-windows-os"></a>Använd PowerShell för att använda API: et på Windows-operativsystem

Följ dessa steg för att anropa API: et i PowerShell:

1. Använd den `Invoke-WebRequest` kommando för att anropa API: et.
2. Metoden är Post innehållstypen är JSON, enligt följande kod exempel och skärmen insamlingen.
3. Skapa parametrarna brödtext i JSON-format.

I följande kodexempel visas ett PowerShell-anrop till API: et.

```powershell
$accesstoken = “Get token for your Client AAD App”
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
$res = Invoke-WebRequest -Method "Post" -Uri $uri -Body $Body -ContentType "application/json" –Headers $headers;
$Content = $res | ConvertFrom-Json
```

Följande skärmdump visar ett exempel för att anropa API: et i PowerShell.

![Anropa API: et med PowerShell för Windows VM](./media/stclient-call-api-ps-windowsvm.png)

Med det tidigare exemplet kan du hämta JSON och parsa den för att hämta följande information:

```powershell
$testresult = ConvertFrom-Json –InputObject (ConvertFrom-Json –InputObject $res)

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

Följande skärmdump som visar `$res.Content`, får du information om testresultaten i JSON-format.

![JSON-resultat från PowerShell anrop till Windows](./media/stclient-pswindows-rescontent-json.png)

Följande skärmdump visar resultat visas i en online JSON-visningsprogram.
(till exempel [kod tjärgropar](https://codebeautify.org/jsonviewer), [JSON-visare](https://jsonformatter.org/json-viewer))

![JSON-resultat från PowerShell-anrop till Windows-VM](./media/stclient-consume-api-pswindows-json.png)

### <a name="use-curl-to-consume-the-api-on-the-linux-os"></a>Använd cURL till att använda API: et på Linux-operativsystem

Följ dessa steg för att anropa API: et med cURL:

1. Använd kommandot curl för att anropa API: et.
2. Metoden är Post innehållstypen är JSON, enligt följande kodavsnitt.

```
CURL POST -H "Content-Type:application/json" 
-H "Authorization: Bearer XXXXXX-Token-XXXXXXXX”
https://isvapp.azurewebsites.net/selftest-vm 
-d '{ "DNSName":"XXXX.westus.cloudapp.azure.com", "User":"XXX", "Password":"XXXX@123456", "OS":"Linux", "PortNo":"22", "CompanyName":"ABCD"}'

```
Följande skärmbild visar ett exempel på hur du använder curl för att anropa API: et.

![Anropa API: T med curl-kommando](./media/stclient-consume-api-curl.png)

Följande skärmdump visar JSON-resultat från curl-anrop.

![JSON-resultat från curl-anrop](./media/stclient-consume-api-curl-json.png)


## <a name="choose-the-azure-ad-tenant-for-the-app"></a>Välj Azure AD-klient för appen

Använd följande steg för att välja Azure AD-klient där du vill skapa ditt program.

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. På den övre menyraden väljer du ditt konto och välj den Active Directory-klient där du vill registrera ditt program under listan katalog. Eller Välj den **katalog + prenumeration** så visas det globala prenumerationsfiltret. Följande skärmdump visar ett exempel på det här filtret.

   ![Välj prenumerationsfiltret](./media/stclient-subscription-filter.png)

3. I det vänstra navigeringsfältet, väljer **alla tjänster** och välj sedan **Azure Active Directory**.

   I följande steg ska du innehavarens namn (eller katalognamnet) eller klient-ID (eller katalog-ID).

   **Hämta klientinformation:**
  
   I **översikt av Azure Active Directory**, Sök efter ”egenskaper” och välj sedan **egenskaper**. Med följande skärmdump som exempel:

   - **Namn på** -klientnamn eller katalognamn
   - **Katalog-ID** -klient-ID eller katalog-ID eller Använd rullningslisten att hitta egenskaper.

   ![Azure Active Directory-egenskapssidan](./media/stclient-aad-properties.png)

## <a name="register-the-client-app"></a>Registrera klientappen

Använd följande steg för att registrera klientappen.

1. I det vänstra navigeringsfältet, väljer **alla tjänster** och välj sedan **appregistreringar**.
2. Under **appregistreringar**väljer **+ ny programregistrering**.
3. Under **skapa**, ange den information som krävs för följande fält:

   - **Namn på** – ange ett eget namn för appen. Till exempel ”SelfTestClient”.
   - **Programtyp** – Välj **Web App/API**
   - **Inloggnings-URL** – typen ”https://isvapp.azurewebsites.net/selftest-vm”

4. Välj **Skapa**.
5. Under **appregistreringar** eller **registrerad app**, kopiera den **program-ID**.

   ![Hämta program-ID](./media/stclient-app-id.png)

6. I verktygsfältet registrerad app väljer **inställningar**.
7. Välj **nödvändiga behörigheter** att konfigurera behörigheter för ditt program.
8. Under **nödvändiga behörigheter**väljer **+ Lägg till**.
9. Under **Lägg till API-åtkomst**, Välj **Välj en API**.
10. Under **Välj en API**, typ ”Windows Azures klassiska distributionsmodell” att söka efter API: et.
11. I sökresultaten väljer **Windows Azure klassiska distributionsmodellen** och klicka sedan på **Välj**.

    ![Konfigurera flera innehavare för app](./media/stclient-select-api.png)

12. Under **Lägg till API-åtkomst**, Välj **Välj behörigheter**.
13. Välj **åtkomst till ”Windows Azure Service Management API”**.

    ![Aktivera API-åtkomst för app](./media/stclient-enable-api-access.png)

14. Klicka på **Välj**.
15. Välj **Done** (Klar).
16. Under **inställningar**väljer **egenskaper**.
17. Under **egenskaper**, rulla ned till **med flera innehavare**. Välj **Ja**.  

    ![Konfigurera flera innehavare för app](./media/stclient-yes-multitenant.png)

18. Välj **Spara**.
19. Under **inställningar**väljer **nycklar**.
20. Skapa en hemlig nyckel genom att välja nyckeln **beskrivning** textrutan. Konfigurera följande fält:

    - Ange ett nyckelnamn. Till exempel ”selftestclient”
    - På den **FÖRFALLER** listrutan, Välj ”i 1 år”.
    - Välj **spara** att generera nyckeln.
    - Under **värdet**, kopiera nyckeln.

     >[!Important]
     >Du kommer inte att kunna se nyckelvärdet när du har avslutat den **nycklar** formuläret.

    ![Nyckelvärdet formulär](./media/stclient-create-key.png)

## <a name="create-the-token-for-the-client-app"></a>Skapa en token för klientappen

Du kan använda någon av följande program för att skapa och få en token med hjälp av OAuth REST-API:

- Postman
- cURL i Linux
- C&#35;
- PowerShell

### <a name="to-create-and-get-a-token-using-postman"></a>Skapa och hämta en token med hjälp av Postman

 Om du vill ställa Auth0 för token för någon av dina auktoriserade program, utför du en POST-åtgärd för att den [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) slutpunkten med en nyttolast i följande format:

```
Method Type : POST
Base Url: https://login.microsoftonline.com/common/oauth2/token
```
Skicka följande parametrar i begärandetexten:

```
Body Content-Type: x-www-form-urlencoded
client_id: XXX (Paste your Application ID of Web App/API Type client AD App)
grant_type: client_credentials
client_secret: XXX (Paste your Secret Key of Web App/API Type client AD App)
resource: https://management.core.windows.net
```

Skicka följande parametrar i huvudet i begäran:

```
Content-Type: application/x-www-form-urlencoded
```

Följande skärmdump visar ett exempel på hur du använder Postman för att hämta en token.

![Hämta token med Postman](./media/stclient-postman-get-token.png)

### <a name="to-create-and-get-a-token-using-curl-in-linux"></a>Skapa och hämta en token med cURL i Linux

Om du vill ställa Auth0 för token för någon av dina auktoriserade program, utför du en POST-åtgärd för att den [ https://login.microsoftonline.com/common/oauth2/token ](https://login.microsoftonline.com/common/oauth2/token) slutpunkten med en nyttolast i följande format:

```
Request:

curl --request POST \
                --url 'https://login.microsoftonline.com/common/oauth2/token' \
                --header "Content-Type:application/x-www-form-urlencoded" \
                --data "grant_type=client_credentials&client_id=XXXXX-XXXX-XXXX-XXXXXXX&client_secret=XXXXXXXXX&resource=https://management.core.windows.net"

Response:

{"token":"UClCUUKxUlkdbhE1cHLz3kyjbIZYVh9eB34A5Q21Y3FPqKGSJs","expires":"2014-02-17 18:46:08"}
```

Följande skärmdump visar ett exempel på hur du använder curl-kommando för att hämta en token.

![Hämta token med curl-kommando](./media/stclient-curl-get-token.png)

### <a name="to-create-and-get-a-token-using-c35"></a>Skapa och hämta en token med hjälp av C&#35;

Om du vill ställa Auth0 för token för någon av dina auktoriserade program, utför du en POST-åtgärd för att den [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) slutpunkten med en nyttolast i följande format:

```
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

### <a name="to-create-and-get-a-token-using-powershell"></a>Skapa och hämta en token med hjälp av PowerShell

Om du vill ställa Auth0 för token för någon av dina auktoriserade program, utför du en POST-åtgärd för att den [ https://soamtenant.auth0.com/oauth/token ](https://soamtenant.auth0.com/oauth/token) slutpunkten med en nyttolast i följande format:

```
$clientId = "Application Id of AD Client APP";
$clientSecret = "Secret Key of AD Client APP “
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

## <a name="pass-the-client-app-token-to-the-api"></a>Skicka klienten apptoken till API: et

Skicka token själv testa API: et med följande kod i auktoriseringshuvudet:

```
$redirectUri = ‘https://isvapp.azurewebsites.net/selftest-vm’
$accesstoken = ‘place your token here’

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
echo 'Test Results:'
$result.Content

```

## <a name="test-your-self-test-client"></a>Testa själv testa klienten

Följ dessa steg om du vill testa klienten:

1. Distribuera den virtuella datorn som du vill testa.
2. Anropa den själv testa API: T med din klient apptoken för auktorisering.
3. Hämta testresultat i JSON-format.

### <a name="test-result-examples"></a>Testa resultatet exempel

Följande kodavsnitt visar testresultaten i JSON-format.

**Testresultat för en virtuell Windows-dator:**

```
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

**Testresultat för en Linux-VM:**

```
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

När du har har testat din Azure-dator, kan du [publicera erbjudandet](./cpp-publish-offer.md).
