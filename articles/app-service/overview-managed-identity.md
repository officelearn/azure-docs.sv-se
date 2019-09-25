---
title: Översikt över hanterade identiteter – Azure App Service | Microsoft Docs
description: Konceptuell referens och installations guide för hanterade identiteter i Azure App Service och Azure Functions
services: app-service
author: mattchenderson
manager: cfowler
editor: ''
ms.service: app-service
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2019
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 1774fcf0af287bba03c2c5c79e14883e3594ef0c
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260147"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Använda hanterade identiteter för App Service och Azure Functions

> [!Important] 
> Hanterade identiteter för App Service och Azure Functions fungerar inte som förväntat om din app migreras mellan prenumerationer/klienter. Appen måste skaffa en ny identitet, som du kan göra genom att inaktivera och aktivera funktionen på nytt. Se [ta bort en identitet](#remove) nedan. Underordnade resurser måste också ha åtkomst principer uppdaterade för att använda den nya identiteten.

Det här avsnittet visar hur du skapar en hanterad identitet för App Service och Azure Functions program och hur du använder den för att få åtkomst till andra resurser. Med en hanterad identitet från Azure Active Directory kan din app enkelt komma åt andra AAD-skyddade resurser som Azure Key Vault. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i AAD finns i [hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

Ditt program kan beviljas två typer av identiteter: 
- En **systemtilldelad identitet** är kopplad till ditt program och tas bort om din app tas bort. En app kan bara ha en tilldelad identitet.
- En **användardefinierad identitet** är en fristående Azure-resurs som kan tilldelas till din app. En app kan ha flera användare tilldelade identiteter.

## <a name="adding-a-system-assigned-identity"></a>Lägga till en tilldelad identitet

Att skapa en app med en tilldelad identitet kräver att ytterligare en egenskap anges för programmet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

För att konfigurera en hanterad identitet i portalen skapar du först ett program som vanligt och aktiverar sedan funktionen.

1. Skapa en app i portalen på vanligt sätt. Gå till den i portalen.

2. Om du använder en Function-app navigerar du till **plattforms funktioner**. För andra typer av appar rullar du ned till **inställnings** gruppen i det vänstra navigerings fältet.

3. Välj **hanterad identitet**.

4. Växla **status** till **på på**fliken systemtilldelad. Klicka på **Spara**.

![Hanterad identitet i App Service](media/app-service-managed-service-identity/msi-blade-system.png)

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med Azure CLI måste du använda `az webapp identity assign` kommandot mot ett befintligt program. Det finns tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure Portal.
- Använd den inbäddade Azure Cloud Shell via knappen "testa", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda en lokal CLI-konsol. 

Följande steg vägleder dig genom att skapa en webbapp och tilldela den en identitet med hjälp av CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med den Azure-prenumeration under vilken du vill distribuera programmet:

    ```azurecli-interactive
    az login
    ```
2. Skapa ett webb program med hjälp av CLI. Fler exempel på hur du använder CLI med App Service finns i [App Service CLI-exempel](../app-service/samples-cli.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. `identity assign` Kör kommandot för att skapa identiteten för det här programmet:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Följande steg vägleder dig genom att skapa en webbapp och tilldela den en identitet med hjälp av Azure PowerShell:

1. Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Login-AzAccount` för att skapa en anslutning till Azure.

2. Skapa ett webb program med hjälp av Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med App Service finns i [App Service PowerShell-exempel](../app-service/samples-powershell.md):

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location
    
    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free
    
    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. `Set-AzWebApp -AssignIdentity` Kör kommandot för att skapa identiteten för det här programmet:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall

En Azure Resource Manager mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och funktioner finns i [Automatisera resurs distribution i App Service](../app-service/deploy-complex-application-predictably.md) och [Automatisera resurs distribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

En resurs av typen `Microsoft.Web/sites` kan skapas med en identitet genom att inkludera följande egenskap i resurs definitionen:
```json
"identity": {
    "type": "SystemAssigned"
}    
```

> [!NOTE] 
> Ett program kan ha både tilldelade och användarspecifika identiteter på samma tidpunkt. I det här fallet `type` skulle egenskapen vara`SystemAssigned,UserAssigned`

Genom att lägga till den systemtilldelade typen kan Azure Skapa och hantera identiteten för ditt program.

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

När platsen har skapats har den följande ytterligare egenskaper:
```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Där `<TENANTID>` och`<PRINCIPALID>` ersätts med GUID. Egenskapen tenantId identifierar vilken AAD-klient identiteten tillhör. PrincipalId är en unik identifierare för programmets nya identitet. I AAD har tjänstens huvud namn samma namn som du gav App Service-eller Azure Functions-instansen.


## <a name="adding-a-user-assigned-identity"></a>Lägga till en användardefinierad identitet

Om du skapar en app med en användardefinierad identitet måste du skapa identiteten och sedan lägga till dess resurs-ID i appens konfiguration.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Först måste du skapa en användardefinierad identitets resurs.

1. Skapa en användare som tilldelats en hanterad identitets resurs enligt [dessa anvisningar](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Skapa en app i portalen på vanligt sätt. Gå till den i portalen.

3. Om du använder en Function-app navigerar du till **plattforms funktioner**. För andra typer av appar rullar du ned till **inställnings** gruppen i det vänstra navigerings fältet.

4. Välj **hanterad identitet**.

5. Klicka på **Lägg till**i fliken **tilldelade användare** .

6. Sök efter den identitet som du skapade tidigare och markera den. Klicka på **Lägg till**.

![Hanterad identitet i App Service](media/app-service-managed-service-identity/msi-blade-user.png)

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager mall

En Azure Resource Manager mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och funktioner finns i [Automatisera resurs distribution i App Service](../app-service/deploy-complex-application-predictably.md) och [Automatisera resurs distribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

En resurs av typen `Microsoft.Web/sites` kan skapas med en identitet genom att inkludera följande block i resurs definitionen och ersätta `<RESOURCEID>` med resurs-ID: t för den önskade identiteten:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}    
```

> [!NOTE] 
> Ett program kan ha både tilldelade och användarspecifika identiteter på samma tidpunkt. I det här fallet `type` skulle egenskapen vara`SystemAssigned,UserAssigned`

Genom att lägga till den användardefinierade typen och en meddelad Azure för att skapa och hantera identiteten för ditt program.

En webbapp kan till exempel se ut så här:
```json
{
    "apiVersion": "2016-08-01",
    "type": "Microsoft.Web/sites",
    "name": "[variables('appName')]",
    "location": "[resourceGroup().location]",
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]": {}
        }
    },
    "properties": {
        "name": "[variables('appName')]",
        "serverFarmId": "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "hostingEnvironment": "",
        "clientAffinityEnabled": false,
        "alwaysOn": true
    },
    "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', variables('hostingPlanName'))]",
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', variables('identityName'))]"
    ]
}
```

När platsen har skapats har den följande ytterligare egenskaper:
```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {
            "principalId": "<PRINCIPALID>",
            "clientId": "<CLIENTID>"
        }
    }
}
```

Där `<PRINCIPALID>` och`<CLIENTID>` ersätts med GUID. PrincipalId är en unik identifierare för den identitet som används för AAD-administration. ClientId är en unik identifierare för programmets nya identitet som används för att ange vilken identitet som ska användas vid körnings anrop.


## <a name="obtaining-tokens-for-azure-resources"></a>Hämta token för Azure-resurser

En app kan använda sin identitet för att hämta tokens till andra resurser som skyddas av AAD, till exempel Azure Key Vault. Dessa tokens representerar programmet som använder resursen och inte någon specifik användare av programmet. 

> [!IMPORTANT]
> Du kan behöva konfigurera mål resursen för att tillåta åtkomst från ditt program. Om du till exempel begär en token till Key Vault måste du kontrol lera att du har lagt till en åtkomst princip som innehåller programmets identitet. Annars avvisas anrop till Key Vault, även om de inkluderar token. Mer information om vilka resurser som stöder Azure Active Directory tokens finns i [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

Det finns ett enkelt REST-protokoll för att hämta en token i App Service och Azure Functions. För .NET-program ger Microsoft. Azure. Services. AppAuthentication-biblioteket en abstraktion över det här protokollet och har stöd för en lokal utvecklings miljö.

### <a name="asal"></a>Använda Microsoft. Azure. Services. AppAuthentication-biblioteket för .NET

För .NET-program och-funktioner är det enklaste sättet att arbeta med en hanterad identitet via paketet Microsoft. Azure. Services. AppAuthentication. Med det här biblioteket kan du också testa din kod lokalt på din utvecklings dator med ditt användar konto från Visual Studio, [Azure CLI](/cli/azure)eller Active Directory integrerad autentisering. Mer information om lokala utvecklings alternativ med det här biblioteket finns i [referens för Microsoft. Azure. Services. AppAuthentication]. Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till referenser till [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och andra nödvändiga NuGet-paket till ditt program. I exemplet nedan används även [Microsoft. Azure. nyckel valv](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Lägg till följande kod i programmet och ändra till målet för rätt resurs. I det här exemplet visas två sätt att arbeta med Azure Key Vault:

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.KeyVault;
// ...
var azureServiceTokenProvider = new AzureServiceTokenProvider();
string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
// OR
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
```

Mer information om Microsoft. Azure. Services. AppAuthentication och de åtgärder den visar finns i [Referens för Microsoft. Azure. Services. AppAuthentication] och [app service-och nyckel VALVET med MSI .net-exemplet](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).


### <a name="using-the-azure-sdk-for-java"></a>Använda Azure SDK för Java

För Java-program och-funktioner är det enklaste sättet att arbeta med en hanterad identitet via [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java). Det här avsnittet visar hur du kommer igång med biblioteket i din kod.

1. Lägg till en referens till [Azure SDK-biblioteket](https://mvnrepository.com/artifact/com.microsoft.azure/azure). För Maven-projekt kan du lägga till det här kodfragmentet i `dependencies` avsnittet i projektets Pom-fil:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure</artifactId>
    <version>1.23.0</version>
</dependency>
```

2. `AppServiceMSICredentials` Använd objektet för autentisering. Det här exemplet visar hur den här mekanismen kan användas för att arbeta med Azure Key Vault:

```java
import com.microsoft.azure.AzureEnvironment;
import com.microsoft.azure.management.Azure;
import com.microsoft.azure.management.keyvault.Vault
//...
Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
        .withSubscription(subscriptionId);
Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

```

### <a name="using-the-rest-protocol"></a>Använda REST-protokollet

En app med en hanterad identitet har två miljövariabler definierade:

- MSI_ENDPOINT – URL: en till den lokala token service.
- MSI_SECRET – ett-sidhuvud som används för att minska risken för att SSRF-attacker (Server sidans begäran). Värdet roteras av plattformen.

**MSI_ENDPOINT** är en lokal URL som din app kan begära token från. Om du vill hämta en token för en resurs gör du en HTTP GET-begäran till den här slut punkten, inklusive följande parametrar:

> |Parameternamn|I|Beskrivning|
> |-----|-----|-----|
> |resource|Söka i data|AAD-resurs-URI för resursen som en token ska hämtas för. Detta kan vara en av de [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) eller andra resurs-URI: er.|
> |api-version|Söka i data|Den version av token API som ska användas. "2017-09-01" är för närvarande den enda version som stöds.|
> |secret|Huvud|Värdet för MSI_SECRET-miljövariabeln. Den här rubriken används för att minska risken för förfalskning av SSRF-attacker (Server sidans begäran).|
> |clientid|Söka i data|(Valfritt, såvida inte användaren tilldelas) ID för den användar tilldelnings identitet som ska användas. Om det utelämnas används den systemtilldelade identiteten.|

> [!IMPORTANT]
> Om du försöker hämta tokens för användarspecifika identiteter måste du inkludera `clientid` egenskapen. Annars försöker token-tjänsten hämta en token för en tilldelad identitet, som kan vara eller inte finns.

Ett lyckat 200 OK-svar innehåller en JSON-text med följande egenskaper:

> |Egenskapsnamn|Beskrivning|
> |-------------|----------|
> |access_token|Den begärda åtkomsttoken. Den anropande webb tjänsten kan använda denna token för att autentisera till den mottagande webb tjänsten.|
> |expires_on|Tiden då åtkomsttoken upphör att gälla. Datumet visas som antalet sekunder från 1970-01-01T0:0: 0Z UTC fram till förfallo tiden. Det här värdet används för att fastställa livs längden för cachelagrade token.|
> |resource|App-ID-URI för den mottagande webb tjänsten.|
> |token_type|Anger värdet för token-typ. Den enda typ som Azure AD stöder är Bearer. Mer information om Bearer-token finns i [OAuth 2,0 Authorization Framework: Användning av Bearer-token (](https://www.rfc-editor.org/rfc/rfc6750.txt)RFC 6750).|

Svaret är detsamma som [svaret på AAD-begäran om tjänst-till-tjänst-](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response)åtkomsttoken.

> [!NOTE]
> Miljövariabler ställs in när processen startas första gången, så när du har aktiverat en hanterad identitet för ditt program kan du behöva starta om programmet eller distribuera om dess kod innan `MSI_ENDPOINT` och `MSI_SECRET` är tillgängliga för din kod.

### <a name="rest-protocol-examples"></a>Exempel på REST-protokoll

En exempel förfrågan kan se ut så här:

```
GET /MSI/token?resource=https://vault.azure.net&api-version=2017-09-01 HTTP/1.1
Host: localhost:4141
Secret: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Och ett exempel svar kan se ut ungefär så här:

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

### <a name="code-examples"></a>Kod exempel

<a name="token-csharp"></a>Gör så här för att C#göra denna begäran i:

```csharp
public static async Task<HttpResponseMessage> GetToken(string resource, string apiversion)  {
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Add("Secret", Environment.GetEnvironmentVariable("MSI_SECRET"));
    return await client.GetAsync(String.Format("{0}/?resource={1}&api-version={2}", Environment.GetEnvironmentVariable("MSI_ENDPOINT"), resource, apiversion));
}
```

> [!TIP]
> För .NET-språk kan du också använda [Microsoft. Azure. Services. AppAuthentication](#asal) i stället för att göra den här begäran själv.

<a name="token-js"></a>I Node. JS:

```javascript
const rp = require('request-promise');
const getToken = function(resource, apiver, cb) {
    let options = {
        uri: `${process.env["MSI_ENDPOINT"]}/?resource=${resource}&api-version=${apiver}`,
        headers: {
            'Secret': process.env["MSI_SECRET"]
        }
    };
    rp(options)
        .then(cb);
}
```

<a name="token-python"></a>I python:

```python
import os
import requests

msi_endpoint = os.environ["MSI_ENDPOINT"]
msi_secret = os.environ["MSI_SECRET"]

def get_bearer_token(resource_uri, token_api_version):
    token_auth_uri = f"{msi_endpoint}?resource={resource_uri}&api-version={token_api_version}"
    head_msi = {'Secret':msi_secret}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
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

En systemtilldelad identitet kan tas bort genom att inaktivera funktionen med hjälp av portalen, PowerShell eller CLI på samma sätt som den skapades. Användare-tilldelade identiteter kan tas bort individuellt. Om du vill ta bort alla identiteter i REST/ARM-filprotokollet gör du detta genom att ange typen till "ingen":

```json
"identity": {
    "type": "None"
}
```

Om du tar bort en tilldelad identitet på det här sättet tas även den bort från AAD. Systemtilldelade identiteter tas också bort automatiskt från AAD när app-resursen tas bort.

> [!NOTE]
> Det finns också en program inställning som kan ställas in, WEBSITE_DISABLE_MSI, som bara inaktiverar den lokala token-tjänsten. Men den lämnar identiteten på plats, och om du fortsätter med verktyg visas den hanterade identiteten som "på" eller "aktive rad". Därför rekommenderas inte användning av den här inställningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Åtkomst SQL Database på ett säkert sätt med en hanterad identitet](app-service-web-tutorial-connect-msi.md)

[Referens för Microsoft. Azure. Services. AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
