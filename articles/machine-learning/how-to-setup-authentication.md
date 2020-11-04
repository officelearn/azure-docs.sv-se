---
title: Konfigurera autentisering
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar och konfigurerar autentisering för olika resurser och arbets flöden i Azure Machine Learning.
services: machine-learning
author: cjgronlund
ms.author: cgronlun
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: core
ms.date: 06/17/2020
ms.topic: conceptual
ms.custom: how-to, has-adal-ref, devx-track-js, devx-track-azurecli
ms.openlocfilehash: fd6f933e1b3c1e7c003f62e03215273e3d28ea5c
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93318537"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden


Lär dig hur du autentiserar till din Azure Machine Learning-arbetsyta och modeller som distribueras som webb tjänster.

I allmänhet finns det två typer av autentisering som du kan använda med Azure Machine Learning:

* __Interaktiv__ : du använder ditt konto i Azure Active Directory för att antingen autentisera dig direkt eller hämta en token som används för autentisering. Interaktiv autentisering används vid experimentering och iterativ utveckling. Eller var du vill kontrol lera åtkomsten till resurser (till exempel en webb tjänst) per användare.
* __Tjänstens huvud namn__ : du skapar ett tjänst huvud namns konto i Azure Active Directory och använder det för att autentisera eller hämta en token. Ett huvud namn för tjänsten används när du behöver en automatiserad process för att autentisera till tjänsten utan att det krävs några åtgärder från användaren. Till exempel ett kontinuerligt integrerings-och distributions skript som tågen och testar en modell varje gång inlärnings koden ändras. Du kan också använda ett huvud namn för tjänsten för att hämta en token för att autentisera till en webb tjänst, om du inte vill kräva att slutanvändaren av tjänsten kan autentisera sig. Eller där autentiseringen av slutanvändare inte utförs direkt med Azure Active Directory.

Oavsett vilken autentiseringstyp som används, används Azure rollbaserad åtkomst kontroll (Azure RBAC) för att begränsa den åtkomst nivå som tillåts för resurserna. Till exempel behöver ett konto som används för att hämta åtkomsttoken för en distribuerad modell bara Läs behörighet till arbets ytan. Mer information om Azure RBAC finns i [Hantera åtkomst till Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

## <a name="prerequisites"></a>Förutsättningar

* Skapa en [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance) med SDK redan installerad.

## <a name="interactive-authentication"></a>Interaktiv autentisering

> [!IMPORTANT]
> Interaktiv autentisering använder webbläsaren och kräver cookies (inklusive cookies från tredje part). Om du har inaktiverat cookies kan du få ett fel meddelande som "vi kunde inte logga in dig". Det här felet kan också inträffa om du har aktiverat [Azure Multi-Factor Authentication](../active-directory/authentication/concept-mfa-howitworks.md).

De flesta exempel i dokumentationen och exemplen använder interaktiv autentisering. Om du exempelvis använder SDK finns det två funktions anrop som automatiskt kommer att fråga dig om ett UI-baserat autentiseringspaket:

* Anrop till `from_config()` funktionen kommer att utfärda prompten.

    ```python
    from azureml.core import Workspace
    ws = Workspace.from_config()
    ```

    Funktionen `from_config()` söker efter en JSON-fil som innehåller din anslutningsinformation för arbetsytan.

* Om du använder `Workspace` konstruktorn för att tillhandahålla information om prenumeration, resurs grupp och arbets yta, kommer även att uppmanas att använda interaktiv autentisering.

    ```python
    ws = Workspace(subscription_id="your-sub-id",
                  resource_group="your-resource-group-id",
                  workspace_name="your-workspace-name"
                  )
    ```

> [!TIP]
> Om du har åtkomst till flera klienter kan du behöva importera-klassen och uttryckligen definiera vilken klient som du är mål för. Genom att anropa konstruktorn för `InteractiveLoginAuthentication` kan du också uppmanas att logga in på liknande sätt som anropen ovan.
>
> ```python
> from azureml.core.authentication import InteractiveLoginAuthentication
> interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
> ```

## <a name="service-principal-authentication"></a>Autentisering av tjänstens huvudnamn

Om du vill använda tjänstens huvud namn (SP)-autentisering måste du först skapa SP och ge den åtkomst till din arbets yta. Som tidigare nämnts används Azure rollbaserad åtkomst kontroll (Azure RBAC) för att kontrol lera åtkomsten, så du måste också bestämma vilken åtkomst som ska ge SP.

> [!IMPORTANT]
> När du använder ett huvud namn för tjänsten ger du det den __lägsta åtkomst som krävs för den aktivitet__ som används för. Du skulle till exempel inte bevilja tjänstens huvud namn ägare eller deltagar åtkomst om all den används för läser in åtkomsttoken för en webb distribution.
>
> Skälet till att bevilja den lägsta åtkomsten är att tjänstens huvud namn använder ett lösen ord för att autentisera, och lösen ordet kan lagras som en del av ett Automation-skript. Om lösen ordet läcker, minimeras den skadliga användningen av SP för en speciell uppgift.

Det enklaste sättet att skapa en SP-och bevilja åtkomst till din arbets yta är genom att använda [Azure CLI](/cli/azure/install-azure-cli?preserve-view=true&view=azure-cli-latest). Använd följande steg för att skapa ett huvud namn för tjänsten och ge det åtkomst till din arbets yta:

> [!NOTE]
> Du måste vara administratör för prenumerationen för att utföra alla de här stegen.

1. Autentisera till din Azure-prenumeration:

    ```azurecli-interactive
    az login
    ```

    Om CLI kan öppna din standardwebbläsare så sker det och en inloggningssida läses in. Annars måste du öppna en webbläsare och följa anvisningarna på kommando raden. Anvisningarna omfattar att bläddra till [https://aka.ms/devicelogin](https://aka.ms/devicelogin) och ange en auktoriseringskod.

    [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)] 

    För andra metoder för autentisering, se [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli?preserve-view=true&view=azure-cli-latest).

1. Installera Azure Machine Learning-tillägget:

    ```azurecli-interactive
    az extension add -n azure-cli-ml
    ```

1. Skapa tjänstens huvud namn. I följande exempel skapas en SP-namngiven **ml-autentisering** :

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth --name ml-auth
    ```

    Utdata är en JSON som liknar följande. Anteckna `clientId` `clientSecret` fälten, och `tenantId` eftersom du behöver dem för andra steg i den här artikeln.

    ```json
    {
        "clientId": "your-client-id",
        "clientSecret": "your-client-secret",
        "subscriptionId": "your-sub-id",
        "tenantId": "your-tenant-id",
        "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
        "resourceManagerEndpointUrl": "https://management.azure.com",
        "activeDirectoryGraphResourceId": "https://graph.windows.net",
        "sqlManagementEndpointUrl": "https://management.core.windows.net:5555",
        "galleryEndpointUrl": "https://gallery.azure.com/",
        "managementEndpointUrl": "https://management.core.windows.net"
    }
    ```

1. Hämta information om tjänstens huvud namn med hjälp av `clientId` värdet som returnerades i föregående steg:

    ```azurecli-interactive
    az ad sp show --id your-client-id
    ```

    Följande JSON är ett förenklat exempel på utdata från kommandot. Anteckna `objectId` fältet, eftersom du kommer att behöva värdet för nästa steg.

    ```json
    {
        "accountEnabled": "True",
        "addIns": [],
        "appDisplayName": "ml-auth",
        ...
        ...
        ...
        "objectId": "your-sp-object-id",
        "objectType": "ServicePrincipal"
    }
    ```

1. Tillåt att SP får åtkomst till din Azure Machine Learning-arbetsyta. Du behöver namnet på din arbets yta och dess resurs grupps namn för `-w` `-g` parametrarna respektive. För `--user` parametern använder du `objectId` värdet från föregående steg. `--role`Parametern låter dig ange åtkomst rollen för tjänstens huvud namn. I följande exempel tilldelas SP till **ägar** rollen. 

    > [!IMPORTANT]
    > Ägar åtkomst innebär att tjänstens huvud namn kan utföra i princip alla åtgärder i din arbets yta. Den används i det här dokumentet för att demonstrera hur du beviljar åtkomst. i en produktions miljö rekommenderar Microsoft att du beviljar tjänstens huvud namn den lägsta åtkomst som krävs för att utföra den roll du avsåg. Mer information finns i [Hantera åtkomst till Azure Machine Learning-arbetsyta](how-to-assign-roles.md).

    ```azurecli-interactive
    az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
    ```

    Det här anropet genererar inga utdata om det lyckas.

### <a name="use-a-service-principal-from-the-sdk"></a>Använda ett huvud namn för tjänsten från SDK

Om du vill autentisera till din arbets yta från SDK: n använder du-tjänstens huvud namn `ServicePrincipalAuthentication` . Använd de värden som du fick när du skapade tjänst leverantören som parametrar. `tenant_id`Parametern mappar till `tenantId` från ovan, `service_principal_id` mappar till `clientId` och `service_principal_password` mappar till `clientSecret` .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

`sp`Variabeln innehåller nu ett autentiseringscertifikat som du använder direkt i SDK. I allmänhet är det en bra idé att lagra de ID/hemligheter som används ovan i miljövariabler som du ser i följande kod. Genom att lagra i miljövariabler förhindrar du att informationen oavsiktligt checkas in i en GitHub-lagrings platsen.

```python
import os

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

För automatiserade arbets flöden som körs i python och använder SDK: n främst kan du använda det här objektet som det är i de flesta fall för autentisering. Följande kod autentiserar till din arbets yta med hjälp av auth-objektet som du skapade.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example",
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

### <a name="use-a-service-principal-from-the-azure-cli"></a>Använda ett huvud namn för tjänsten från Azure CLI

Du kan använda ett tjänst huvud namn för Azure CLI-kommandon. Mer information finns i [Logga in med ett huvud namn för tjänsten](/cli/azure/create-an-azure-service-principal-azure-cli?preserve-view=true&view=azure-cli-latest#sign-in-using-a-service-principal).

### <a name="use-a-service-principal-with-the-rest-api-preview"></a>Använd ett huvud namn för tjänsten med REST API (för hands version)

Tjänstens huvud namn kan också användas för att autentisera till Azure Machine Learning [REST API](/rest/api/azureml/) (för hands version). Du använder den Azure Active Directory [tilldelningen av autentiseringsuppgifter för klient](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md), som tillåter tjänst-till-tjänst-anrop för konsol lös autentisering i automatiserade arbets flöden. Exemplen implementeras med [ADAL-biblioteket](../active-directory/azuread-dev/active-directory-authentication-libraries.md) i både Python och Node.js, men du kan också använda ett bibliotek med öppen källkod som stöder OpenID Connect 1,0.

> [!NOTE]
> MSAL.js är ett nyare bibliotek än ADAL, men du kan inte utföra tjänst-till-tjänst-autentisering med hjälp av klientautentiseringsuppgifter med MSAL.js, eftersom det främst är ett bibliotek på klient sidan som är avsett för interaktivt/UI-autentisering som är kopplat till en speciell användare. Vi rekommenderar att du använder ADAL så som visas nedan för att bygga automatiserade arbets flöden med REST API.

#### <a name="nodejs"></a>Node.js

Använd följande steg för att skapa en auth-token med hjälp av Node.js. Kör i din miljö `npm install adal-node` . Använd sedan, `tenantId` `clientId` och `clientSecret` från tjänstens huvud namn som du skapade i stegen ovan som värden för de matchande variablerna i följande skript.

```javascript
const adal = require('adal-node').AuthenticationContext;

const authorityHostUrl = 'https://login.microsoftonline.com/';
const tenantId = 'your-tenant-id';
const authorityUrl = authorityHostUrl + tenantId;
const clientId = 'your-client-id';
const clientSecret = 'your-client-secret';
const resource = 'https://management.azure.com/';

const context = new adal(authorityUrl);

context.acquireTokenWithClientCredentials(
  resource,
  clientId,
  clientSecret,
  (err, tokenResponse) => {
    if (err) {
      console.log(`Token generation failed due to ${err}`);
    } else {
      console.dir(tokenResponse, { depth: null, colors: true });
    }
  }
);
```

Variabeln `tokenResponse` är ett objekt som inkluderar token och associerade metadata som till exempel förfallo tid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande kodfragment är ett exempel svar.

```javascript
{
    tokenType: 'Bearer',
    expiresIn: 3599,
    expiresOn: 2019-12-17T19:15:56.326Z,
    resource: 'https://management.azure.com/',
    accessToken: "random-oauth-token",
    isMRRT: true,
    _clientId: 'your-client-id',
    _authority: 'https://login.microsoftonline.com/your-tenant-id'
}
```

Använd `accessToken` egenskapen för att hämta auth-token. I [REST API-dokumentationen](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) hittar du exempel på hur du använder token för att göra API-anrop.

#### <a name="python"></a>Python

Använd följande steg för att generera en auth-token med python. Kör i din miljö `pip install adal` . Använd sedan, `tenantId` `clientId` och `clientSecret` från tjänstens huvud namn som du skapade i stegen ovan som värden för lämpliga variabler i följande skript.

```python
from adal import AuthenticationContext

client_id = "your-client-id"
client_secret = "your-client-secret"
resource_url = "https://login.microsoftonline.com"
tenant_id = "your-tenant-id"
authority = "{}/{}".format(resource_url, tenant_id)

auth_context = AuthenticationContext(authority)
token_response = auth_context.acquire_token_with_client_credentials("https://management.azure.com/", client_id, client_secret)
print(token_response)
```

Variabeln `token_response` är en ord lista som inkluderar token och associerade metadata som till exempel förfallo tid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande kodfragment är ett exempel svar.

```python
{
    'tokenType': 'Bearer',
    'expiresIn': 3599,
    'expiresOn': '2019-12-17 19:47:15.150205',
    'resource': 'https://management.azure.com/',
    'accessToken': 'random-oauth-token',
    'isMRRT': True,
    '_clientId': 'your-client-id',
    '_authority': 'https://login.microsoftonline.com/your-tenant-id'
}
```

Används `token_response["accessToken"]` för att hämta auth-token. I [REST API-dokumentationen](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) hittar du exempel på hur du använder token för att göra API-anrop.

#### <a name="java"></a>Java

I Java hämtar du Bearer-token med ett standard REST-anrop:

```java
String tenantId = "your-tenant-id";
String clientId = "your-client-id";
String clientSecret = "your-client-secret";
String resourceManagerUrl = "https://management.azure.com";

HttpRequest tokenAuthenticationRequest = tokenAuthenticationRequest(tenantId, clientId, clientSecret, resourceManagerUrl);

HttpClient client = HttpClient.newBuilder().build();
Gson gson = new Gson();
HttpResponse<String> response = client.send(request, HttpResponse.BodyHandlers.ofString());
if (response.statusCode == 200)
{
     body = gson.fromJson(body, AuthenticationBody.class);

    // ... etc ... 
}
// ... etc ...

static HttpRequest tokenAuthenticationRequest(String tenantId, String clientId, String clientSecret, String resourceManagerUrl){
    String authUrl = String.format("https://login.microsoftonline.com/%s/oauth2/token", tenantId);
    String clientIdParam = String.format("client_id=%s", clientId);
    String resourceParam = String.format("resource=%s", resourceManagerUrl);
    String clientSecretParam = String.format("client_secret=%s", clientSecret);

    String bodyString = String.format("grant_type=client_credentials&%s&%s&%s", clientIdParam, resourceParam, clientSecretParam);

    HttpRequest request = HttpRequest.newBuilder()
            .uri(URI.create(authUrl))
            .POST(HttpRequest.BodyPublishers.ofString(bodyString))
            .build();
    return request;
}

class AuthenticationBody {
    String access_token;
    String token_type;
    int expires_in;
    String scope;
    String refresh_token;
    String id_token;
    
    AuthenticationBody() {}
}
```

Föregående kod skulle behöva hantera undantag och status koder förutom `200 OK` , men visar mönstret: 

- Använd klient-ID och hemlighet för att kontrol lera att programmet har åtkomst
- Använd ditt klient-ID för att ange var du vill `login.microsoftonline.com` söka
- Använd Azure Resource Manager som källa för autentiseringstoken

## <a name="web-service-authentication"></a>Autentisering för webb tjänst

Modell distributioner som skapats av Azure Machine Learning tillhandahålla två autentiseringsmetoder:

* **nyckelbaserad** : en statisk nyckel används för att autentisera till webb tjänsten.
* **tokenbaserad** : en tillfällig token måste hämtas från arbets ytan och användas för att autentisera till webb tjänsten. Denna token upphör att gälla efter en viss tids period och måste uppdateras för att fortsätta arbeta med webb tjänsten.

    > [!NOTE]
    > Tokenbaserad autentisering är endast tillgängligt när du distribuerar till Azure Kubernetes-tjänsten.

### <a name="key-based-web-service-authentication"></a>Nyckelbaserad autentisering av webb tjänst

Webb tjänster som distribueras på Azure Kubernetes service (AKS) har nyckelbaserad autentisering *aktiverat* som standard. Azure Container Instances (ACI) distribuerade tjänster har nyckelbaserad autentisering *inaktive rad* som standard, men du kan aktivera den genom att ange `auth_enabled=True` när du skapar ACI-webb tjänsten. Följande kod är ett exempel på hur du skapar en distributions konfiguration för ACI med nyckelbaserad autentisering aktive rad.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Sedan kan du använda den anpassade ACI-konfigurationen i distributionen med hjälp av- `Model` klassen.

```python
from azureml.core.model import Model, InferenceConfig


inference_config = InferenceConfig(entry_script="score.py",
                                   environment=myenv)
aci_service = Model.deploy(workspace=ws,
                       name="aci_service_sample",
                       models=[model],
                       inference_config=inference_config,
                       deployment_config=aci_config)
aci_service.wait_for_deployment(True)
```

Använd för att hämta auth-nycklarna `aci_service.get_keys()` . Om du vill återskapa en nyckel använder du `regen_key()` funktionen och skickar antingen **primär** eller **sekundär**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Mer information om autentisering till en distribuerad modell finns i [skapa en klient för en modell som distribueras som en webb tjänst](how-to-consume-web-service.md).

### <a name="token-based-web-service-authentication"></a>Tokenbaserad autentisering av webb tjänst

När du aktiverar token-autentisering för en webb tjänst måste användarna presentera en Azure Machine Learning JSON Web Token till webb tjänsten för att komma åt den. Token upphör att gälla efter en angiven tidsperiod och måste uppdateras för att anrop ska fortsätta att skickas.

* Token-autentisering **inaktive ras som standard** när du distribuerar till Azure Kubernetes-tjänsten.
* Token-autentisering **stöds inte** när du distribuerar till Azure Container instances.
* Token **-autentisering kan inte användas på samma gång som nyckelbaserad autentisering**.

Om du vill kontrol lera token-autentisering använder `token_auth_enabled` du parametern när du skapar eller uppdaterar en-distribution:

```python
from azureml.core.webservice import AksWebservice
from azureml.core.model import Model, InferenceConfig

# Create the config
aks_config = AksWebservice.deploy_configuration()

#  Enable token auth and disable (key) auth on the webservice
aks_config = AksWebservice.deploy_configuration(token_auth_enabled=True, auth_enabled=False)

aks_service_name ='aks-service-1'

# deploy the model
aks_service = Model.deploy(workspace=ws,
                           name=aks_service_name,
                           models=[model],
                           inference_config=inference_config,
                           deployment_config=aks_config,
                           deployment_target=aks_target)

aks_service.wait_for_deployment(show_output = True)
```

Om token-autentisering har Aktiver ATS kan du använda `get_token` metoden för att hämta en JSON Web token (JWT) och den tokens förfallo tid:

> [!TIP]
> Om du använder ett huvud namn för tjänsten för att hämta token och vill att den ska ha den lägsta åtkomst som krävs för att hämta en token tilldelar du den till rollen **läsare** för arbets ytan.

```python
token, refresh_by = aks_service.get_token()
print(token)
```

> [!IMPORTANT]
> Du behöver begära en ny token efter tokens `refresh_by`-tid. Om du behöver uppdatera token utanför python SDK är ett alternativ att använda REST API med tjänstens huvudsakliga autentisering för att regelbundet `service.get_token()` anropa anropet, enligt beskrivningen ovan.
>
> Vi rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster.
>
> För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om arbets ytans region inte är tillgänglig kan du inte hämta en token för din webb tjänst, även om klustret finns i en annan region än din arbets yta. Resultatet är att Azure AD-autentisering inte är tillgängligt förrän arbets ytans region är tillgänglig igen.
>
> Dessutom ökar avståndet mellan klustrets region och arbets ytans region, desto längre tid tar det att hämta en token.

## <a name="next-steps"></a>Nästa steg

* [Hur du använder hemligheter i träning](how-to-use-secrets-in-runs.md).
* [Träna och distribuera en bild klassificerings modell](tutorial-train-models-with-aml.md).
* [Använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md).