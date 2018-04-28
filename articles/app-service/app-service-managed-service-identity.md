---
title: Hanterade tjänstidentiteten i Apptjänst och Azure Functions | Microsoft Docs
description: Konceptuell referens- och guiden för hanterade tjänstidentiteten stöd i Azure App Service och Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 04/12/2018
ms.author: mahender
ms.openlocfilehash: 800105d29fa284531e02ce80db69eff3a9915652
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="how-to-use-azure-managed-service-identity-public-preview-in-app-service-and-azure-functions"></a>Hur du använder Azure hanterade tjänstidentiteten (förhandsversion) i App Service och Azure Functions

> [!NOTE] 
> Hanterade tjänstidentiteten för Apptjänst och Azure Functions är för närvarande under förhandsgranskning. Apptjänst på Linux- och webbprogrammet för behållare stöds inte för närvarande.

Det här avsnittet visar hur du skapar en hanterad app identitet för App Service och Azure Functions program och hur du använder den för att komma åt andra resurser. En hanterad tjänstidentitet från Azure Active Directory kan din app att enkelt komma åt andra AAD-skyddade resurser, till exempel Azure Key Vault. Identiteten hanteras av Azure-plattformen och kräver inte att etablera eller rotera alla hemligheter. Mer information om hanterade tjänstidentiteten finns i [hanterade tjänstidentiteten översikt](../active-directory/managed-service-identity/overview.md).

## <a name="creating-an-app-with-an-identity"></a>Skapa en app med en identitet

Skapa en app med en identitet kräver ytterligare en egenskap som ska anges för programmet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Om du vill konfigurera en hanterade tjänstidentiteten i portalen ska du först skapa ett program som vanligt och aktivera funktionen.

1. Skapa en app i portalen som vanligt. Navigera till den i portalen.

2. Om du använder en funktionsapp, gå till **plattformsfunktioner**. För andra apptyper av rulla ned till den **inställningar** i det vänstra navigeringsfönstret.

3. Välj **hanterade tjänstidentiteten**.

4. Växeln **registrera med Azure Active Directory** till **på**. Klicka på **Spara**.

![Hanterade tjänstidentiteten i App Service](media/app-service-managed-service-identity/msi-blade.png)

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill ställa in en hanterad tjänstidentitet med hjälp av Azure CLI, behöver du använda den `az webapp identity assign` kommandot mot ett befintligt program. Det finns tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen.
- Använd inbäddade Azure Cloud Shell via försök ”knappen”, finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda den lokala CLI-konsolen. 

Följande steg beskriver hur du skapar en webbapp och tilldela den en identitet med hjälp av CLI:

1. Om du använder Azure CLI i den lokala konsolen först logga in på Azure med hjälp av [az inloggningen](/cli/azure/reference-index#az_login). Använd ett konto som är associerade med Azure-prenumeration som du vill distribuera programmet:

    ```azurecli-interactive
    az login
    ```
2. Skapa ett webbprogram med hjälp av CLI. Fler exempel på hur du använder CLI med App Service finns [App Service CLI prover](../app-service/app-service-cli-samples.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Kör den `identity assign` kommando för att skapa identiteten för det här programmet:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-an-azure-resource-manager-template"></a>Med en Azure Resource Manager-mall

En Azure Resource Manager-mall kan användas för att automatisera distributionen av Azure-resurser. Mer information om hur du distribuerar till App Service och funktioner finns [automatisera distributionen av resurs i App Service](../app-service/app-service-deploy-complex-application-predictably.md) och [automatisera distributionen av resurs i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Alla resurser av typen `Microsoft.Web/sites` kan skapas med en identitet genom att inkludera följande egenskapen i resursdefinitionen:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

Detta visar Azure för att skapa och hantera identitet för tillämpningsprogrammet.

Ett webbprogram kan se ut ungefär så här:
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

När webbplatsen har skapats har följande ytterligare egenskaper:
```json
"identity": {
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Där `<TENANTID>` och `<PRINCIPALID>` ersätts med GUID. Egenskapen tenantId identifierar vilka AAD-klient som programmet tillhör. PrincipalId är en unik identifierare för den nya Programidentitet. I AAD har programmet samma namn som du gav din App Service eller Azure Functions-instans.

## <a name="obtaining-tokens-for-azure-resources"></a>Hämta token för Azure-resurser

En app kan använda sin identitet för att hämta token till andra resurser som skyddas av AAD, till exempel Azure Key Vault. Dessa token representerar programmet åtkomst till resursen och inte de specifika användaren. 

> [!IMPORTANT]
> Du kan behöva konfigurera målresursen för att tillåta åtkomst från ditt program. Om du vill begära en token för Nyckelvalvet, måste du kontrollera att du har lagt till en åtkomstprincip som innehåller programmets identitet. Annars avvisas anropen till Key Vault, även om de inkludera token. Läs mer om vilka resurser som stöder hanterade tjänstidentiteten token i [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-service-identity/overview.md#which-azure-services-support-managed-service-identity).

Det finns ett enkelt REST-protokoll för att få en token i App Service och Azure Functions. För .NET-program Microsoft.Azure.Services.AppAuthentication biblioteket ger en abstraktion över det här protokollet och stöder en lokal utveckling upplevelse.

### <a name="asal"></a>Med Microsoft.Azure.Services.AppAuthentication-biblioteket för .NET

För .NET-program och funktioner är det enklaste sättet att arbeta med en hanterad tjänstidentitet via Microsoft.Azure.Services.AppAuthentication-paketet. Det här biblioteket kan du testa din kod lokalt på utvecklingsdatorn med ditt användarkonto från Visual Studio, även de [Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest), eller Active Directory-integrerad autentisering. Mer information om alternativ för lokal utveckling med det här biblioteket finns det [Microsoft.Azure.Services.AppAuthentication referens]. Det här avsnittet visar hur du kommer igång med bibliotek i koden.

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

Läs mer om Microsoft.Azure.Services.AppAuthentication och hur det visar i den [Microsoft.Azure.Services.AppAuthentication referens] och [App Service och KeyVault med MSI .NET exempel](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-rest-protocol"></a>Med hjälp av REST-protokollet

En app med en hanterad tjänstidentitet har två miljövariabler som definierats:
- MSI_ENDPOINT
- MSI_SECRET

Den **MSI_ENDPOINT** är en lokal URL som din app kan begära token. Om du vill hämta en token för en resurs, gör du en HTTP GET-begäran för den här slutpunkten, inklusive följande parametrar:

> [!div class="mx-tdBreakAll"]
> |Parameternamn|i|Beskrivning|
> |-----|-----|-----|
> |resurs|Fråga|AAD-resurs-URI för resursen för som en token ska hämtas.|
> |API-version|Fråga|Versionen av token API som ska användas. ”2017-09-01” är för närvarande den enda version som stöds.|
> |hemlighet|Sidhuvud|Värdet för miljövariabeln MSI_SECRET.|


Ett lyckat svar för 200 OK innehåller en JSON-meddelandetext med följande egenskaper:

> [!div class="mx-tdBreakAll"]
> |Egenskapsnamn|Beskrivning|
> |-------------|----------|
> |access_token|Den begärda åtkomst-token. Anropa webbtjänsten kan använda den här variabeln för att autentisera till mottagande webbtjänsten.|
> |expires_on|Tiden då den åtkomst-token upphör att gälla. Representeras som antalet sekunder från 1970-01-01T0:0:0Z UTC tills förfallotid. Det här värdet används för att fastställa livslängden för cachelagrade token.|
> |resurs|App-ID URI för mottagande webbtjänsten.|
> |token_type|Anger värdet för token-typer. Den enda typen som har stöd för Azure AD är ägar. Mer information om ägar-token finns [OAuth 2.0 auktorisering ramen: ägar-Token användning (RFC 6750)](http://www.rfc-editor.org/rfc/rfc6750.txt).|


Svaret är samma som den [svar för AAD-tjänster åtkomst tokenbegäran](../active-directory/develop/active-directory-protocols-oauth-service-to-service.md#service-to-service-access-token-response).

> [!NOTE] 
> Miljövariabler ställs in när processen startas först, så när du har aktiverat hanterade tjänstidentiteten för programmet du kan behöva starta om programmet eller distribuera om dess kod innan `MSI_ENDPOINT` och `MSI_SECRET` är tillgängliga för din kod.

### <a name="rest-protocol-examples"></a>REST-protokollet exempel
En exempelbegäran kan se ut ungefär så här:
```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```
Och en exempelsvar kan se ut ungefär så här:
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
Att göra denna begäran i C#:
```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```
> [!TIP]
> För .NET-språk, kan du också använda [Microsoft.Azure.Services.AppAuthentication](#asal) i stället för att utforma detta begära själv.

I Node.JS:
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

I PowerShell:
```powershell
$apiVersion = "2017-09-01"
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:MSI_ENDPOINT + "?resource=$resourceURI&api-version=$apiVersion"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"Secret"="$env:MSI_SECRET"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Åtkomst till SQL-databas på ett säkert sätt med hjälp av hanterade tjänstidentiteten](app-service-web-tutorial-connect-msi.md)

[Microsoft.Azure.Services.AppAuthentication referens]: https://go.microsoft.com/fwlink/p/?linkid=862452
