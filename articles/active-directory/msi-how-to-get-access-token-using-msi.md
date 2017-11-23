---
title: "Hur du använder hanterade tjänstidentiteten en Azure VM för inloggning och token"
description: "Steg för steg-anvisningar för användning av en Azure VM MSI tjänsten huvudnamn för inloggning och för att erhålla en åtkomst-token."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/17/2017
ms.author: bryanla
ms.openlocfilehash: 168b2ab3676d3f3e2830966f850e14adbe579f85
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="how-to-use-an-azure-vm-managed-service-identity-msi-for-sign-in-and-token-acquisition"></a>Hur du använder en Azure VM hanterade tjänsten identitet (MSI) för inloggning och token 
[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]När du har aktiverat MSI på en virtuell dator i Azure kan använda du MSI för inloggning och begära en åtkomst-token. Den här artikeln beskrivs olika sätt att använda en MSI [tjänstens huvudnamn](develop/active-directory-dev-glossary.md#service-principal-object) för inloggning, och få en [endast app-åtkomst-token](develop/active-directory-dev-glossary.md#access-token) att komma åt andra resurser, inklusive:

- Tyst obevakad inloggning från PowerShell eller Azure CLI
- Token anskaffning för olika klienter, inklusive .NET, PowerShell, Bash/CURL REST
- Logga in med ett Azure SDK, inklusive .NET, Java, Node.js, Python, Ruby

## <a name="prerequisites"></a>Krav

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

Om du planerar att använda PowerShell-exemplen i den här artikeln bör du installera [Azure PowerShell version 4.3.1](https://www.powershellgallery.com/packages/AzureRM) eller större. Om du planerar att använda Azure CLI-exemplen i den här artikeln har tre alternativ:
- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen.
- Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av Azure CLI kodblock.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.13 eller senare) om du föredrar att använda CLI i en lokal kommandotolk. 


> [!IMPORTANT]
> - Alla exempel kod eller skript i den här artikeln förutsätter klienten körs på en MSI-aktiverad virtuell dator. Mer information om hur du aktiverar MSI på en virtuell dator finns [konfigurera en virtuell dator hanteras Service identitet (MSI) med hjälp av Azure portal](msi-qs-configure-portal-windows-vm.md), eller en variant artiklar (med PowerShell, CLI, en mall eller en Azure SDK). 
> - För att förhindra auktorisering fel (403/AuthorizationFailed) i koden/skript-exempel identiteten för den virtuella datorn måste få ”Reader” tillgång definitionsområdet VM att tillåta Azure Resource Manager-åtgärder på den virtuella datorn. Se [tilldelar en hanterad tjänst identitet (MSI) åtkomst till en resurs med hjälp av Azure portal](msi-howto-assign-access-portal.md) mer information.
> - Innan du fortsätter till någon av följande avsnitt, funktionen VM ”ansluta” i Azure-portalen för att fjärransluta till din MSI-aktiverade VM.

## <a name="how-to-sign-in-from-powershell-or-cli-using-msi"></a>Hur du loggar in från PowerShell eller CLI med hjälp av MSI

Tidigare, köra ett skript under sin egen identitet avsedda:
- registrera det som ett konfidentiellt/klienten webbprogram med Azure AD
- logga in med hjälp av programmets klient-ID-hemlighet autentiseringsuppgifter

Med MSI, skript-klienten inte längre behöver registrera med Azure AD eller ange autentiseringsuppgifter. I exemplen nedan visar vi hur du använder en virtuell dators MSI tjänstens huvudnamn för inloggning.

### <a name="azure-powershell"></a>Azure PowerShell

Följande skript visar hur du:

- Skaffa en MSI-åtkomsttoken för en Azure VM
- Använd åtkomst-token för att logga in på Azure AD under motsvarande MSI tjänstens huvudnamn
- Använd MSI tjänstens huvudnamn för att göra en Azure Resource Manager-anrop, att hämta ID för tjänstens huvudnamn

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token

# Use the access token to sign in under the MSI service principal
Login-AzureRmAccount -AccessToken $access_token -AccountId “CLIENT”

# The MSI service principal is now signed in for this session.
# Next, a call to Azure Resource Manager is made to get the service principal ID for the VM's MSI. 
$spID = (Get-AzureRMVM -ResourceGroupName <RESOURCE-GROUP> -Name <VM-NAME>).identity.principalid
echo "The MSI service principal ID is $spID"
```
   
### <a name="azure-cli"></a>Azure CLI

Följande skript visar hur du:

- logga in på Azure AD under den virtuella datorn MSI-tjänstens huvudnamn
- Använd MSI tjänstens huvudnamn för att göra en Azure Resource Manager-anrop, att hämta ID för tjänstens huvudnamn

```azurecli-interactive
az login --msi
spID=$(az resource list -n <VM-NAME> --query [*].identity.principalId --out tsv)
echo The MSI service principal ID is $spID
```

## <a name="how-to-acquire-an-access-token-from-msi"></a>Hur du skaffar en åtkomst-token från MSI

Med hjälp av MSI, din klient program eller skript inte längre behöver registrera med Azure AD, och inte heller finns dess klient-ID och Hemlig för att erhålla en åtkomst-token. Klienten kan bara begära den lokala MSI-slutpunkten en åtkomst-token utan att behöva ange autentiseringsuppgifter. Endast app-åtkomst-token har utfärdats för MSI tjänstens huvudnamn, lämpliga för användning som en ägartoken i [tjänst-till-tjänst anropar kräver klientens autentiseringsuppgifter](active-directory-protocols-oauth-service-to-service.md).

I exemplen nedan visar vi hur du använder en virtuell dators MSI för token.

### <a name="net"></a>.NET

> [!IMPORTANT]
> Azure AD Authentication Library (ADAL) är inte integrerad med MSI. Gör en begäran till den lokala MSI-slutpunkten på en virtuell dator för att få en åtkomsttoken med hjälp av MSI. Mer information finns i [MSI vanliga frågor och kända problem](msi-known-issues.md#frequently-asked-questions-faqs).

```csharp
// Build request to acquire MSI token
HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://localhost:50342/oauth2/token?resource=https://management.azure.com/");
request.Headers["Metadata"] = "true";
request.Method = "GET";

try
{
    // Call /token endpoint
    HttpWebResponse response = (HttpWebResponse)request.GetResponse();

    // Pipe response Stream to a StreamReader, and extract access token
    StreamReader streamResponse = new StreamReader(response.GetResponseStream()); 
    string stringResponse = streamResponse.ReadToEnd();
    JavaScriptSerializer j = new JavaScriptSerializer();
    Dictionary<string, string> list = (Dictionary<string, string>) j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
    string accessToken = list["access_token"];
}
catch (Exception e)
{
    string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
}

```

### <a name="azure-powershell-token"></a>Azure PowerShell

```powershell
# Get an access token from MSI
$response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token `
                              -Method GET -Body @{resource="https://management.azure.com/"} -Headers @{Metadata="true"}
$content =$response.Content | ConvertFrom-Json
$access_token = $content.access_token
echo "The MSI access token is $access_token"
```

### <a name="bashcurl"></a>Bash/CURL

```bash
response=$(curl http://localhost:50342/oauth2/token --data "resource=https://management.azure.com/" -H Metadata:true -s)
access_token=$(echo $response | python -c 'import sys, json; print (json.load(sys.stdin)["access_token"])')
echo The MSI access token is $access_token
```

### <a name="rest"></a>REST

Exempel på begäran:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Element | Beskrivning |
| ------- | ----------- |
| `GET` | HTTP-verbet, vilket innebär att du vill hämta data från slutpunkten. I det här fallet en OAuth åtkomst-token. | 
| `http://localhost:50342/oauth2/token` | MSI slutpunkten, där 50342 är standardporten och kan konfigureras. |
| `resource` | En frågesträngsparameter som anger App-ID-URI för målresursen. Den visas också i den `aud` (målgrupp) anspråk för Utfärdad token. I det här exemplet begär vi en token för åtkomst till Azure Resource Manager, som har en https://management.azure.com/ App-ID-URI. |
| `Metadata` | En HTTP-begäran huvudfältet, krävs av MSI som en lösning mot angrepp Server Side begäran förfalskning (SSRF). Det här värdet måste anges till ”true”, alla med gemener.

Exempelsvar:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Element | Beskrivning |
| ------- | ----------- |
| `access_token` | Den begärda åtkomst-token. När du anropar en REST-API, token är inbäddad i den `Authorization` begäran huvudfältet som ett ”ägartoken”, vilket gör att API för att autentisera anroparen. | 
| `refresh_token` | Inte används av MSI. |
| `expires_in` | Antal sekunder som åtkomsttoken fortsätter att vara giltig innan det förfaller från utfärdandet. Tid för utfärdande kan hittas i token `iat` anspråk. |
| `expires_on` | Timespan när den åtkomst-token upphör att gälla. Representeras som antalet sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `exp` anspråk). |
| `not_before` | Timespan när åtkomsttoken träder i kraft och kan accepteras. Representeras som antalet sekunder från ”1970-01-01T0:0:0Z UTC” (motsvarar token `nbf` anspråk). |
| `resource` | Resursen åtkomsttoken har begärts för som matchar den `resource` frågesträngparametern för begäran. |
| `token_type` | Typ av token som är en åtkomsttoken ”ägar”, vilket innebär att resursen kan ge åtkomst till innehavare av denna token. |

## <a name="how-to-use-msi-with-azure-sdk-libraries"></a>Hur du använder MSI med Azure SDK-bibliotek

Azure har stöd för flera programmeringsspråk plattformar genom en serie [Azure SDK](https://azure.microsoft.com/downloads). Flera av dem har uppdaterats för att stödja logga in med en MSI och ger motsvarande exempel för att visa användning. Den här listan uppdateras när ytterligare stöd har lagts till:

| SDK | Exempel |
| --- | ------ | 
| .NET | [Distribuera en ARM-mall från en virtuell Windows-dator med hjälp av hanterade tjänstidentiteten](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet) |
| .NET Core | [Anropa Azure-tjänster från en Linux VM som använder hanterade tjänstidentiteten](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/) |
| Node.js| [Hantera resurser med hjälp av hanterade tjänstidentiteten](https://azure.microsoft.com/resources/samples/resources-node-manage-resources-with-msi/) |
| Python | [Använda MSI för att autentisera bara från inuti en virtuell dator](https://azure.microsoft.com/resources/samples/resource-manager-python-manage-resources-with-msi/) |
| Ruby   | [Hantera resurser från en MSI-aktiverad virtuell dator](https://azure.microsoft.com/resources/samples/resources-ruby-manage-resources-with-msi/) | 

## <a name="additional-information"></a>Ytterligare information

### <a name="token-expiration"></a>Token upphör att gälla

Lokala MSI-undersystemet cachelagrar token. Därför kan anropa du den så ofta du vill och ett under överföring anrop till Azure AD resultatet bara om:
- en cache-miss inträffar på grund av att inga token i cacheminnet
- token har upphört att gälla

Om du Cachelagra token i koden, bör du vara beredd på att hantera scenarier där resursen anger att token har upphört att gälla.

### <a name="resource-ids-for-azure-services"></a>Resurs-ID för Azure-tjänster

Se [Azure-tjänster som stöder Azure AD-autentisering](msi-overview.md#azure-services-that-support-azure-ad-authentication) en lista över tjänster som stöder MSI och deras respektive resurs-ID.

## <a name="troubleshooting"></a>Felsökning

### <a name="sign-in-or-token-acquisition-fails"></a>Logga in eller token förvärv misslyckas

Kontrollera att MSI har aktiverats. Gå tillbaka till den virtuella Azure-datorn i den [Azure-portalen](https://portal.azure.com) och:

- Titta på sidan ”Configuration” och kontrollera MSI aktiverat = ”Yes”.
- Titta på sidan ”tillägg” och kontrollera MSI-tillägget har distribuerats.

Om något är fel, kan du behöva distribuera om MSI-filerna på din resurs eller felsöka distributionen misslyckades.

### <a name="http-request-error-responses"></a>HTTP-felsvar

- *bad_request_102: metadata som krävs huvud har inte angetts*

  Antingen den `Metadata` begäran huvudfältet saknas från begäran, eller är felaktigt formaterad. Värdet måste anges som `true`, alla med gemener. Se ”exempelbegäran” i den [föregående REST-avsnittet](#rest) ett exempel.

- *Okänd: Det gick inte att hämta token från Active directory. Mer information finns i loggarna i \<sökväg\>*

  Kontrollera att din HTTP GET URI-begäran har formaterats korrekt, särskilt resursen URI som angetts i frågesträngen. Finns i ”exempelbegäran” i den [föregående REST avsnittet](#rest) exempelvis eller [Azure-tjänster som stöder Azure AD-autentisering](msi-overview.md#azure-services-that-support-azure-ad-authentication) en lista över tjänster och deras respektive resurs-ID.

- *unknown_source: okänd källa \<URI\>*

  Kontrollera att din HTTP GET-begäran URI är korrekt formaterad. Den `scheme:host/resource-path` del måste anges som `http://localhost:50342/oauth2/token`. Se ”exempelbegäran” i den [föregående REST-avsnittet](#rest) ett exempel.

## <a name="related-content"></a>Relaterat innehåll

- En översikt över MSI finns [hanterade tjänstidentiteten översikt](msi-overview.md).
- För att aktivera MSI på en Azure VM, se [konfigurera en Azure VM hanterade tjänsten identitet (MSI) med hjälp av PowerShell](msi-qs-configure-powershell-windows-vm.md).

Använd följande avsnitt för kommentarer för att ge feedback och hjälp oss att förfina och utforma innehållet.

