---
title: Hanterad tjänstidentitet i App Service och Azure Functions | Microsoft Docs
description: Konceptuell referens och installationsprogrammet guide för hanterad tjänstidentitet stöd i Azure App Service och Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 06/25/2018
ms.author: mahender
ms.openlocfilehash: 2e392a3a50cda3daacb5bc358baaea2627eeafc0
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/06/2018
ms.locfileid: "39578838"
---
# <a name="how-to-use-azure-managed-service-identity-in-app-service-and-azure-functions"></a>Hur du använder Azure-hanterad tjänstidentitet i App Service och Azure Functions

> [!NOTE] 
> App Service i Linux och Web App for Containers stöder för närvarande inte hanterade tjänstidentiteten.

> [!Important] 
> Hanterad tjänstidentitet för App Service och Azure Functions fungerar inte som förväntat om din app migreras över prenumerationer/klienter. Appen måste du skaffa en ny identitet som kan göras genom att inaktivera och återaktivera funktionen. Se [tar bort en identitet](#remove) nedan. Underordnade resurser måste också ha åtkomstprinciper har uppdaterats för att använda den nya identiteten.

Det här avsnittet visar hur du skapar en hanterad app identitet för App Service och Azure Functions och hur du använder den för att komma åt andra resurser. En hanterad tjänstidentitet från Azure Active Directory kan din app för att enkelt komma åt andra AAD-skyddade resurser, till exempel Azure Key Vault. Identiteten hanteras av Azure-plattformen och kräver inte att etablera eller rotera hemligheter. Mer information om hanterad tjänstidentitet finns i den [hanterad tjänstidentitet översikt](../active-directory/managed-service-identity/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Skapa en app med en identitet

Skapa en app med en identitet som kräver ytterligare en egenskap anges för programmet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill konfigurera en hanterad tjänstidentitet i portalen, ska du först skapa ett program som vanligt och sedan aktivera funktionen.

1. Skapa en app i portalen som vanligt. Navigera till den i portalen.

2. Om du använder en funktionsapp, gå till **plattformsfunktioner**. För andra typer av appar, rulla ned till den **inställningar** i det vänstra navigeringsfönstret.

3. Välj **hanterad tjänstidentitet**.

4. Växeln **registreras med Azure Active Directory** till **på**. Klicka på **Spara**.

![Hanterad tjänstidentitet i App Service](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad tjänstidentitet med hjälp av Azure CLI, behöver du använda den `az webapp identity assign` kommandot mot ett befintligt program. Har du tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen.
- Använd inbäddad Azure Cloud Shell via ”Prova” knappen, finns i det övre högra hörnet av varje kodblocket nedan.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

Följande steg beskriver hur du skapar en webbapp och tilldela den en identitet med hjälp av CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerade med Azure-prenumerationen som du vill distribuera programmet:

    ```azurecli-interactive
    az login
    ```
2. Skapa ett webbprogram med hjälp av CLI. Fler exempel på hur du använder CLI med App Service finns [App Service CLI-exempel](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Kör den `identity assign` kommando för att skapa identiteten för det här programmet:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

Följande steg beskriver hur du skapar en webbapp och tilldela den en identitet med hjälp av Azure PowerShell:

1. Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Login-AzureRmAccount` för att skapa en anslutning till Azure.

2. Skapa ett webbprogram med Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med App Service finns [App Service-PowerShell-exempel](../app-service/app-service-powershell-samples.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzureRmResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzureRmAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzureRmWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Kör den `identity assign` kommando för att skapa identiteten för det här programmet:

    ```azurepowershell-interactive
    Set-AzureRmWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Med en Azure Resource Manager-mall

En Azure Resource Manager-mall kan användas för att automatisera distributionen av dina Azure-resurser. Läs mer om att distribuera till App Service och Functions i [automatisera resursdistribution i App Service](../app-service/app-service-deploy-complex-application-predictably.md) och [automatisera resursdistribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Alla resurser av typen `Microsoft.Web/sites` kan skapas med en identitet genom att inkludera följande egenskap i resursdefinitionen:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Detta informerar du Azure att skapa och hantera identiteten för ditt program.

En webbapp kan till exempel se ut så här:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "SystemAssigned"
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]"
    ]
}
```

När webbplatsen har skapats, har följande ytterligare egenskaper:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Där `<TENANTID>` och `<PRINCIPALID>` har ersatts med GUID. Egenskapen tenantId identifierar vilka AAD-klient som tillhör identiteten. PrincipalId är en unik identifierare för programmets ny identitet. I AAD har tjänstens huvudnamn samma namn som du gav till din App Service eller Azure Functions-instans.

## <a name="obtaining-tokens-for-azure-resources"></a>Hämta token för Azure-resurser

En app kan använda sin identitet för att hämta token till andra resurser som skyddas av AAD, till exempel Azure Key Vault. Dessa token representerar programmet åtkomst till resursen och inte någon specifik användare av programmet. 

> [!IMPORTANT]
> Du kan behöva konfigurera målresursen för att tillåta åtkomst från ditt program. Om du begär en token för Key Vault, måste du kontrollera att du har lagt till en åtkomstprincip som innehåller ditt programs identitet. I annat fall avvisas dina anrop till Key Vault, även om de innehåller token. Läs mer om vilka resurser som stöder hanterad tjänstidentitet token i [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-service-identity/overview.md#which-azure-services-support-managed-service-identity).

Det finns ett enkelt REST-protokoll för att hämta en token i App Service och Azure Functions. För .NET-program, Microsoft.Azure.Services.AppAuthentication biblioteket ger en abstraktion över det här protokollet och har stöd för en lokal utvecklingsmiljö.

### <a name="asal"></a>Med hjälp av Microsoft.Azure.Services.AppAuthentication-klientbiblioteket för .NET

För .NET-program och funktioner är det enklaste sättet att arbeta med hanterade tjänstidentiteter via Microsoft.Azure.Services.AppAuthentication-paketet. Det här biblioteket gör också att du kan testa din kod lokalt på utvecklingsdatorn, använder användarkontot från Visual Studio, den [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), eller Active Directory-integrerad autentisering. Mer information om alternativ för lokal utveckling med det här biblioteket finns i den [Microsoft.Azure.Services.AppAuthentication referens]. Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till referenser till den [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet-paket till ditt program.

2.  Lägg till följande kod i ditt program:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://management.azure.com/");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Läs mer om Microsoft.Azure.Services.AppAuthentication och vilka åtgärder som den exponerar i den [Microsoft.Azure.Services.AppAuthentication referens] och [App Service och KeyVault med MSI-.NET exemplet](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Med hjälp av REST-protokoll

En app med en hanterad tjänstidentitet har två miljövariabler som definieras:
- MSI_ENDPOINT
- MSI_SECRET

Den **MSI_ENDPOINT** är en lokal URL som din app kan begära token. För att få en token för en resurs kan du göra en HTTP GET-begäran i den här slutpunkten, inklusive följande parametrar:

> [!div class="mx-tdBreakAll"]
> |Parameternamn|I|Beskrivning|
> |-----|-----|-----|
> |resurs|Fråga|AAD resurs-URI för resursen för som en token ska hämtas.|
> |API-versionen|Fråga|Versionen av token API: et som ska användas. ”2017-09-01” är för närvarande den enda versionen som stöds.|
> |hemlighet|Sidhuvud|Värdet för miljövariabeln MSI_SECRET.|


En lyckad svar med 200 OK innehåller en JSON-texten med följande egenskaper:

> [!div class="mx-tdBreakAll"]
> |Egenskapsnamn|Beskrivning|
> |-------------|----------|
> |access_token|Den begärda åtkomst-token. Anropa webbtjänsten kan använda denna token för att autentisera till mottagande webbtjänsten.|
> |expires_on|Den tid då den åtkomst-token upphör att gälla. Datumet visas som hur många sekunder en från 1970-01-01T0:0:0Z UTC tills de upphör att gälla. Det här värdet används för att fastställa livslängd för cachelagrade token.|
> |resurs|App-ID URI för den mottagande webbtjänsten.|
> |token_type|Anger typ tokenu värdet. Den enda typen som har stöd för Azure AD är ägar. Läs mer om ägar-token, [The OAuth 2.0 auktorisering Framework: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Svaret är samma som den [svar för AAD tjänst-till-tjänst begäran om åtkomsttoken](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE] 
> Miljövariabler som ställs in när processen startas första gången, så när du har aktiverat hanterad tjänstidentitet för ditt program du kan behöva starta om ditt program eller distribuera om koden, innan `MSI_ENDPOINT` och `MSI_SECRET` är tillgängliga för din kod.

### <a name="rest-protocol-examples"></a>REST-protokollet exempel
En exempelbegäran kan se ut så här:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
Och en exempelsvaret kan se ut så här:
```
HTTP/1.1 200 OK
Content-Type: application/json
 
{
    "access_token": "eyJ0eXAi…",
    "expires_on": "09/14/2017 00:00:00 PM +00:00",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer"
}
```

### <a name="code-examples"></a>Kodexempel
<a name="token-csharp"></a>Att göra denna begäran i C#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> För .NET-språk, kan du också använda [Microsoft.Azure.Services.AppAuthentication](#asal) i stället för att utforma det begär du själv.

<a name="token-js"></a>I Node.JS:
```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    var options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-powershell"></a>I PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="remove"></a>Ta bort en identitet

En identitet kan tas bort genom att inaktivera funktionen med hjälp av portalen, PowerShell eller CLI på samma sätt som den skapades. Detta görs i protokollet REST/ARM-mall genom att ange att typen ”None”:

```json
"identity": {
    "type": "None"
}    
```

Ta bort identiteten på så vis tas även bort huvudkontot från AAD. Systemtilldelade identiteter tas automatiskt bort från AAD när appresursen tas bort.

> [!NOTE] 
> Det finns också en programinställningen som kan ställas in WEBSITE_DISABLE_MSI, vilket bara inaktiverar den lokala token-tjänsten. Men den lämnar identiteten på plats och verktyg fortfarande visas MSI som ”on” eller ”aktiverad”. Användning av den här inställningen är därför inte rekommenderas.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Säker åtkomst till SQL-databaser med hanterade tjänstidentiteter](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication referens]: https://go.microsoft.com/fwlink/p/?linkid=862452
