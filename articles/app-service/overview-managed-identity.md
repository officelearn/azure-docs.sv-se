---
title: Hanterade identiteter
description: Lär dig hur hanterade identiteter fungerar i Azure App Service och Azure Functions, hur du konfigurerar en hanterad identitet och genererar en token för en backend-resurs.
author: mattchenderson
ms.topic: article
ms.date: 04/14/2020
ms.author: mahender
ms.reviewer: yevbronsh
ms.openlocfilehash: 875d2bbebdfa95c6d180979399d876eb2afc01b4
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392524"
---
# <a name="how-to-use-managed-identities-for-app-service-and-azure-functions"></a>Så här använder du hanterade identiteter för App Service och Azure Functions

> [!Important] 
> Hanterade identiteter för App Service och Azure Functions fungerar inte som förväntat om din app migreras mellan prenumerationer/klienter. Appen måste skaffa en ny identitet, vilket kan göras genom att inaktivera och återaktivera funktionen. Se [Ta bort en identitet](#remove) nedan. Underordnade resurser måste också ha åtkomstprinciper uppdaterade för att använda den nya identiteten.

Det här avsnittet visar hur du skapar en hanterad identitet för App Service- och Azure Functions-program och hur du använder den för att komma åt andra resurser. En hanterad identitet från Azure Active Directory (Azure AD) gör att din app enkelt kan komma åt andra Azure AD-skyddade resurser, till exempel Azure Key Vault. Identiteten hanteras av Azure-plattformen och kräver inte att du etablerar eller roterar några hemligheter. Mer information om hanterade identiteter i Azure AD finns i [Hanterade identiteter för Azure-resurser](../active-directory/managed-identities-azure-resources/overview.md).

Din ansökan kan beviljas två typer av identiteter:

- En **systemtilldelad identitet** är knuten till ditt program och tas bort om appen tas bort. En app kan bara ha en systemtilldelad identitet.
- En **användartilldelad identitet** är en fristående Azure-resurs som kan tilldelas din app. En app kan ha flera användartilldelade identiteter.

## <a name="add-a-system-assigned-identity"></a>Lägga till en systemtilldelad identitet

Skapa en app med en systemtilldelad identitet kräver ytterligare en egenskap som ska anges på programmet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

För att konfigurera en hanterad identitet i portalen skapar du först ett program som vanligt och aktiverar sedan funktionen.

1. Skapa en app i portalen som vanligt. Gå till den i portalen.

2. Om du använder en funktionsapp navigerar du till **plattformsfunktioner**. För andra apptyper bläddrar du ned till gruppen **Inställningar** i den vänstra navigeringen.

3. Välj **Identitet**.

4. Växla **status** till **På**fliken System **som tilldelats** . Klicka på **Spara**.

    ![Hanterad identitet i App Service](media/app-service-managed-service-identity/system-assigned-managed-identity-in-azure-portal.png)

### <a name="using-the-azure-cli"></a>Använda Azure CLI

Om du vill konfigurera en hanterad identitet med `az webapp identity assign` Hjälp av Azure CLI måste du använda kommandot mot ett befintligt program. Du har tre alternativ för att köra exemplen i det här avsnittet:

- Använd [Azure Cloud Shell](../cloud-shell/overview.md) från Azure-portalen.
- Använd det inbäddade Azure Cloud Shell via knappen "Prova", som finns i det övre högra hörnet av varje kodblock nedan.
- [Installera den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.31 eller senare) om du föredrar att använda en lokal CLI-konsol. 

I följande steg kan du skapa en webbapp och tilldela den en identitet med CLI:

1. Om du använder Azure CLI i en lokal konsol börjar du med att logga in i Azure med [az login](/cli/azure/reference-index#az-login). Använd ett konto som är associerat med Azure-prenumerationen under vilken du vill distribuera programmet:

    ```azurecli-interactive
    az login
    ```

2. Skapa ett webbprogram med CLI. Fler exempel på hur du använder CLI med App Service finns i [EXEMPEL på App Service CLI:](../app-service/samples-cli.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location westus
    az appservice plan create --name myPlan --resource-group myResourceGroup --sku S1
    az webapp create --name myApp --resource-group myResourceGroup --plan myPlan
    ```

3. Kör `identity assign` kommandot för att skapa identiteten för det här programmet:

    ```azurecli-interactive
    az webapp identity assign --name myApp --resource-group myResourceGroup
    ```

### <a name="using-azure-powershell"></a>Använda Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I följande steg kan du skapa en webbapp och tilldela den en identitet med Azure PowerShell:

1. Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna `Login-AzAccount` som finns i [Azure PowerShell-guiden](/powershell/azure/overview)och kör sedan för att skapa en anslutning med Azure.

2. Skapa ett webbprogram med Azure PowerShell. Fler exempel på hur du använder Azure PowerShell med App Service finns i [Exempel på PowerShell för App Service:](../app-service/samples-powershell.md)

    ```azurepowershell-interactive
    # Create a resource group.
    New-AzResourceGroup -Name myResourceGroup -Location $location

    # Create an App Service plan in Free tier.
    New-AzAppServicePlan -Name $webappname -Location $location -ResourceGroupName myResourceGroup -Tier Free

    # Create a web app.
    New-AzWebApp -Name $webappname -Location $location -AppServicePlan $webappname -ResourceGroupName myResourceGroup
    ```

3. Kör `Set-AzWebApp -AssignIdentity` kommandot för att skapa identiteten för det här programmet:

    ```azurepowershell-interactive
    Set-AzWebApp -AssignIdentity $true -Name $webappname -ResourceGroupName myResourceGroup 
    ```

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager-mall

En Azure Resource Manager-mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och Funktioner finns [i Automatisera resursdistribution i App Service](../app-service/deploy-complex-application-predictably.md) och Automatisera [resursdistribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Alla typer `Microsoft.Web/sites` av resurser kan skapas med en identitet genom att inkludera följande egenskap i resursdefinitionen:

```json
"identity": {
    "type": "SystemAssigned"
}
```

> [!NOTE]
> Ett program kan ha både systemtilldelade och användartilldelade identiteter samtidigt. I detta fall `type` skulle fastigheten vara`SystemAssigned,UserAssigned`

Om du lägger till den systemtilldelade typen får Azure att skapa och hantera identiteten för ditt program.

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

När webbplatsen skapas har den följande ytterligare egenskaper:

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

Egenskapen tenantId identifierar vilken Azure AD-klient som identiteten tillhör. PrincipalId är en unik identifierare för programmets nya identitet. I Azure AD har tjänstens huvudnamn samma namn som du gav till din App-tjänst eller Azure Functions-instans.

## <a name="add-a-user-assigned-identity"></a>Lägga till en användartilldelad identitet

Att skapa en app med en användartilldelad identitet kräver att du skapar identiteten och sedan lägger till dess resursidentifierare i appkonfigurationen.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Först måste du skapa en användartilldelad identitetsresurs.

1. Skapa en användartilldelad hanterad identitetsresurs enligt [dessa instruktioner](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity).

2. Skapa en app i portalen som vanligt. Gå till den i portalen.

3. Om du använder en funktionsapp navigerar du till **plattformsfunktioner**. För andra apptyper bläddrar du ned till gruppen **Inställningar** i den vänstra navigeringen.

4. Välj **Identitet**.

5. Klicka på **Lägg till**på fliken **Användare.**

6. Sök efter den identitet du skapade tidigare och välj den. Klicka på **Lägg till**.

    ![Hanterad identitet i App Service](media/app-service-managed-service-identity/user-assigned-managed-identity-in-azure-portal.png)

### <a name="using-an-azure-resource-manager-template"></a>Använda en Azure Resource Manager-mall

En Azure Resource Manager-mall kan användas för att automatisera distributionen av dina Azure-resurser. Mer information om hur du distribuerar till App Service och Funktioner finns [i Automatisera resursdistribution i App Service](../app-service/deploy-complex-application-predictably.md) och Automatisera [resursdistribution i Azure Functions](../azure-functions/functions-infrastructure-as-code.md).

Alla typer `Microsoft.Web/sites` av resurser kan skapas med en identitet genom `<RESOURCEID>` att inkludera följande block i resursdefinitionen och ersätta med resurs-ID för önskad identitet:

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "<RESOURCEID>": {}
    }
}
```

> [!NOTE]
> Ett program kan ha både systemtilldelade och användartilldelade identiteter samtidigt. I detta fall `type` skulle fastigheten vara`SystemAssigned,UserAssigned`

Om du lägger till den användartilldelade typen får Azure att använda den användartilldelade identitet som angetts för ditt program.

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

När webbplatsen skapas har den följande ytterligare egenskaper:

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

PrincipalId är en unik identifierare för den identitet som används för Azure AD-administration. ClientId är en unik identifierare för programmets nya identitet som används för att ange vilken identitet som ska användas under körningsanrop.

## <a name="obtain-tokens-for-azure-resources"></a>Hämta token för Azure-resurser

En app kan använda sin hanterade identitet för att hämta token för att komma åt andra resurser som skyddas av Azure AD, till exempel Azure Key Vault. Dessa token representerar programmet som använder resursen och inte någon specifik användare av programmet. 

Du kan behöva konfigurera målresursen så att åtkomsten från programmet får åtkomst. Om du till exempel begär en token för att komma åt Key Vault måste du se till att du har lagt till en åtkomstprincip som innehåller programmets identitet. Annars kommer dina samtal till Key Vault att avvisas, även om de innehåller token. Mer information om vilka resurser som stöder Azure Active Directory-token finns i [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

> [!IMPORTANT]
> Backend-tjänsterna för hanterade identiteter underhåller en cache per resurs-URI i cirka 8 timmar. Om du uppdaterar åtkomstprincipen för en viss målresurs och omedelbart hämtar en token för den resursen, kan du fortsätta att hämta en cachelagd token med inaktuella behörigheter tills den token upphör att gälla. Det finns för närvarande inget sätt att tvinga fram en tokenuppdatering.

Det finns ett enkelt REST-protokoll för att hämta en token i App Service och Azure Functions. Detta kan användas för alla program och språk. För .NET och Java tillhandahåller Azure SDK en abstraktion över det här protokollet och underlättar en lokal utvecklingsupplevelse.

### <a name="using-the-rest-protocol"></a>Använda REST-protokollet

En app med en hanterad identitet har två miljövariabler definierade:

- IDENTITY_ENDPOINT - URL:en till den lokala tokentjänsten.
- IDENTITY_HEADER - ett huvud som används för att minska SSRF-attacker (Server-Side Request Forgery). Värdet roteras av plattformen.

Den **IDENTITY_ENDPOINT** är en lokal URL från vilken din app kan begära token. Om du vill hämta en token för en resurs gör du en HTTP GET-begäran till den här slutpunkten, inklusive följande parametrar:

> | Parameternamn    | I     | Beskrivning                                                                                                                                                                                                                                                                                                                                |
> |-------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | resource          | Söka i data  | Azure AD-resurs-URI för den resurs som en token ska erhållas för. Detta kan vara en av De [Azure-tjänster som stöder Azure AD-autentisering](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) eller någon annan resurs URI.    |
> | api-version       | Söka i data  | Den version av token-API:et som ska användas. Använd "2019-08-01" eller senare.                                                                                                                                                                                                                                                                 |
> | X-IDENTITY-HEADER | Huvud | Värdet för IDENTITY_HEADER miljövariabeln. Det här huvudet används för att minska SSRF-attacker (Server-Side Request Forgery).                                                                                                                                                                                                    |
> | client_id         | Söka i data  | (Valfritt) Klient-ID för den användartilldelade identitet som ska användas. Det går inte att `principal_id`använda `mi_res_id`en `object_id`begäran som innehåller , eller . Om alla ID-parametrar `object_id`( `mi_res_id``client_id`, `principal_id`, och ) utelämnas används den systemtilldelade identiteten.                                             |
> | principal_id      | Söka i data  | (Valfritt) Huvud-ID:t för den användartilldelade identitet som ska användas. `object_id`är ett alias som kan användas i stället. Det går inte att använda en begäran som innehåller client_id, mi_res_id eller object_id. Om alla ID-parametrar `object_id`( `mi_res_id``client_id`, `principal_id`, och ) utelämnas används den systemtilldelade identiteten. |
> | mi_res_id         | Söka i data  | (Valfritt) Azure-resurs-ID för den användartilldelade identitet som ska användas. Det går inte att `principal_id`använda `client_id`en `object_id`begäran som innehåller , eller . Om alla ID-parametrar `object_id`( `mi_res_id``client_id`, `principal_id`, och ) utelämnas används den systemtilldelade identiteten.                                      |

> [!IMPORTANT]
> Om du försöker hämta token för användartilldelade identiteter måste du inkludera en av de valfria egenskaperna. Annars försöker tokentjänsten hämta en token för en systemtilldelad identitet, som kanske eller kanske inte finns.

Ett lyckat 200 OK-svar innehåller en JSON-brödtext med följande egenskaper:

> | Egenskapsnamn | Beskrivning                                                                                                                                                                                                                                        |
> |---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
> | access_token  | Den begärda åtkomsttoken. Anropande webbtjänst kan använda den här token för att autentisera till den mottagande webbtjänsten.                                                                                                                               |
> | client_id     | Klient-ID för den identitet som användes.                                                                                                                                                                                                       |
> | expires_on    | Tidsintervallet när åtkomsttoken upphör att gälla. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `exp` anspråk).                                                                                |
> | not_before    | Tidsintervallet när åtkomsttoken börjar gälla och kan accepteras. Datumet representeras som antalet sekunder från "1970-01-01T0:0:0Z UTC" (motsvarar tokens `nbf` anspråk).                                                      |
> | resource      | Resursen som åtkomsttoken begärdes `resource` för, vilket matchar frågesträngparametern för begäran.                                                                                                                               |
> | token_type    | Anger tokentypsvärdet. Den enda typen som Azure AD stöder är FBearer. Mer information om innehavartoken finns i [OAuth 2.0 Authorization Framework: Bearer Token Usage (RFC 6750)](https://www.rfc-editor.org/rfc/rfc6750.txt). |

Det här svaret är detsamma som [svaret för Azure AD-tjänst-till-tjänst-åtkomsttokenbegäran](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md#service-to-service-access-token-response).

> [!NOTE]
> En äldre version av det här protokollet, med api-versionen "2017-09-01" använde `secret` huvudet i stället för `X-IDENTITY-HEADER` och accepterade bara egenskapen `clientid` för användartilldelade. Den returnerade `expires_on` också i ett tidsstämpelformat. MSI_ENDPOINT kan användas som ett alias för IDENTITY_ENDPOINT och MSI_SECRET kan användas som ett alias för IDENTITY_HEADER.

### <a name="rest-protocol-examples"></a>EXEMPEL PÅ REST-protokoll

En exempelbegäran kan se ut så här:

```http
GET /MSI/token?resource=https://vault.azure.net&api-version=2019-08-01 HTTP/1.1
Host: localhost:4141
X-IDENTITY-HEADER: 853b9a84-5bfa-4b22-a3f3-0b9a43d9ad8a
```

Och ett exempel svar kan se ut så här:

```http
HTTP/1.1 200 OK
Content-Type: application/json

{
    "access_token": "eyJ0eXAi…",
    "expires_on": "1586984735",
    "resource": "https://vault.azure.net",
    "token_type": "Bearer",
    "client_id": "5E29463D-71DA-4FE0-8E69-999B57DB23B0"
}
```

### <a name="code-examples"></a>Kodexempel

# <a name="net"></a>[.NET](#tab/dotnet)

> [!TIP]
> För .NET-språk kan du också använda [Microsoft.Azure.Services.AppAuthentication](#asal) i stället för att skapa den här begäran själv.

```csharp
private readonly HttpClient _client;
// ...
public async Task<HttpResponseMessage> GetToken(string resource)  {
    var request = new HttpRequestMessage(HttpMethod.Get, 
        String.Format("{0}/?resource={1}&api-version=2019-08-01", Environment.GetEnvironmentVariable("IDENTITY_ENDPOINT"), resource));
    request.Headers.Add("X-IDENTITY-HEADER", Environment.GetEnvironmentVariable("IDENTITY_HEADER"));
    return await _client.SendAsync(request);
}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const rp = require('request-promise');
const getToken = function(resource, cb) {
    let options = {
        uri: `${process.env["IDENTITY_ENDPOINT"]}/?resource=${resource}&api-version=2019-08-01`,
        headers: {
            'X-IDENTITY-HEADER': process.env["IDENTITY_HEADER"]
        }
    };
    rp(options)
        .then(cb);
}
```

# <a name="python"></a>[Python](#tab/python)

```python
import os
import requests

identity_endpoint = os.environ["IDENTITY_ENDPOINT"]
identity_header = os.environ["IDENTITY_HEADER"]

def get_bearer_token(resource_uri):
    token_auth_uri = f"{identity_endpoint}?resource={resource_uri}&api-version=2019-08-01"
    head_msi = {'X-IDENTITY-HEADER':identity_header}

    resp = requests.get(token_auth_uri, headers=head_msi)
    access_token = resp.json()['access_token']

    return access_token
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
$resourceURI = "https://<AAD-resource-URI-for-resource-to-obtain-token>"
$tokenAuthURI = $env:IDENTITY_ENDPOINT + "?resource=$resourceURI&api-version=2019-08-01"
$tokenResponse = Invoke-RestMethod -Method Get -Headers @{"X-IDENTITY-HEADER"="$env:IDENTITY_HEADER"} -Uri $tokenAuthURI
$accessToken = $tokenResponse.access_token
```

---

### <a name="using-the-microsoftazureservicesappauthentication-library-for-net"></a><a name="asal"></a>Använda biblioteket Microsoft.Azure.Services.AppAuthentication för .NET

För .NET-program och funktioner är det enklaste sättet att arbeta med en hanterad identitet via Paketet Microsoft.Azure.Services.AppAuthentication. Med det här biblioteket kan du också testa koden lokalt på utvecklingsmaskinen med hjälp av ditt användarkonto från Visual Studio, [Azure CLI](/cli/azure)eller Active Directory Integrated Authentication. Mer information om lokala utvecklingsalternativ med det här biblioteket finns i [referensen Microsoft.Azure.Services.AppAuthentication]. I det här avsnittet visas hur du kommer igång med biblioteket i koden.

1. Lägg till referenser till [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) och alla andra nödvändiga NuGet-paket till ditt program. I exemplet nedan används också [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault).

2. Lägg till följande kod i ditt program, ändra för att rikta rätt resurs. I det här exemplet visas två sätt att arbeta med Azure Key Vault:

    ```csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;
    // ...
    var azureServiceTokenProvider = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider.GetAccessTokenAsync("https://vault.azure.net");
    // OR
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider.KeyVaultTokenCallback));
    ```

Mer information om Microsoft.Azure.Services.AppAuthentication och de åtgärder som exponeras finns i [microsoft.Azure.Services.AppAuthentication-referensen] och [apptjänsten och KeyVault med MSI .NET-exempel](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet).

### <a name="using-the-azure-sdk-for-java"></a>Använda Azure SDK för Java

För Java-program och -funktioner är det enklaste sättet att arbeta med en hanterad identitet via [Azure SDK för Java](https://github.com/Azure/azure-sdk-for-java). I det här avsnittet visas hur du kommer igång med biblioteket i koden.

1. Lägg till en referens till [Azure SDK-biblioteket](https://mvnrepository.com/artifact/com.microsoft.azure/azure). För Maven-projekt kan du lägga till `dependencies` det här kodavsnittet i avsnittet i projektets POM-fil:

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure</artifactId>
        <version>1.23.0</version>
    </dependency>
    ```

2. Använd `AppServiceMSICredentials` objektet för autentisering. Det här exemplet visar hur den här mekanismen kan användas för att arbeta med Azure Key Vault:

    ```java
    import com.microsoft.azure.AzureEnvironment;
    import com.microsoft.azure.management.Azure;
    import com.microsoft.azure.management.keyvault.Vault
    //...
    Azure azure = Azure.authenticate(new AppServiceMSICredentials(AzureEnvironment.AZURE))
            .withSubscription(subscriptionId);
    Vault myKeyVault = azure.vaults().getByResourceGroup(resourceGroup, keyvaultName);

    ```


## <a name="remove-an-identity"></a><a name="remove"></a>Ta bort en identitet

En systemtilldelad identitet kan tas bort genom att inaktivera funktionen med hjälp av portalen, PowerShell eller CLI på samma sätt som den skapades. Användartilldelade identiteter kan tas bort individuellt. Om du vill ta bort alla identiteter anger du typen till "Ingen" i [ARM-mallen:](#using-an-azure-resource-manager-template)

```json
"identity": {
    "type": "None"
}
```

Om du tar bort en systemtilldelad identitet på det här sättet tas den också bort från Azure AD. Systemtilldelade identiteter tas också bort automatiskt från Azure AD när appresursen tas bort.

> [!NOTE]
> Det finns också en programinställning som kan ställas in, WEBSITE_DISABLE_MSI, som bara inaktiverar den lokala tokentjänsten. Det lämnar dock identiteten på plats, och verktyg kommer fortfarande att visa den hanterade identiteten som "på" eller "aktiverad". Därför rekommenderas inte användning av den här inställningen.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Komma åt SQL-databasen på ett säkert sätt med hjälp av en hanterad identitet](app-service-web-tutorial-connect-msi.md)

[Referens för Microsoft.Azure.Services.AppAuthentication]: https://go.microsoft.com/fwlink/p/?linkid=862452
