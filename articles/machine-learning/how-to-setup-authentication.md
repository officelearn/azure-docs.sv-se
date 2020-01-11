---
title: Konfigurera autentisering
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar och konfigurerar autentisering för olika resurser och arbets flöden i Azure Machine Learning. Det finns flera sätt att konfigurera och använda autentisering i tjänsten, från enkel UI-baserad autentisering i utvecklings-eller testnings syfte, för att få fullständig Azure Active Directory autentisering av tjänstens huvud namn.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: ce85c45d80a776af84a0987cfbc3f496c2bbb72b
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75893964"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurera autentisering för Azure Machine Learning resurser och arbets flöden
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du konfigurerar och konfigurerar autentisering för olika resurser och arbets flöden i Azure Machine Learning. Det finns flera olika sätt att autentisera till tjänsten, allt från enkel UI-baserad autentisering för utvecklings-eller testnings syfte till fullständig Azure Active Directory tjänstens huvud namn. I den här artikeln förklaras också skillnaderna i hur autentisering av webb tjänst fungerar, samt hur du autentiserar till den Azure Machine Learning REST API.

Den här instruktionen visar hur du gör följande uppgifter:

* Använd Interactive UI-autentisering för testning/utveckling
* Konfigurera tjänstens huvud namns autentisering
* Autentisera till din arbets yta
* Hämta OAuth 2.0 Bearer-typ-token för Azure Machine Learning REST API
* Förstå webb tjänst autentisering

I [artikeln begrepp](concept-enterprise-security.md) finns en allmän översikt över säkerhet och autentisering inom Azure Machine Learning.

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* [Konfigurera utvecklings miljön](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [Azure Machine Learning Notebook VM](concept-azure-machine-learning-architecture.md#compute-instance) med SDK redan installerad.

## <a name="interactive-authentication"></a>Interaktiv autentisering

De flesta exempel i dokumentationen för den här tjänsten använder interaktiv autentisering i Jupyter notebook-datorer som en enkel metod för testning och demonstration. Detta är ett lätt sätt att testa det du skapar. Det finns två funktions anrop som automatiskt kommer att fråga dig om ett UI-baserat autentiseringspaket.

Om du anropar funktionen `from_config()` får du frågan.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Funktionen `from_config()` söker efter en JSON-fil som innehåller din anslutnings information för arbets ytan. Du kan också ange anslutnings informationen explicit med hjälp av `Workspace`-konstruktorn, som även kommer att uppmanas att använda interaktiv autentisering. Båda anropen är likvärdiga.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Om du har åtkomst till flera klienter kan du behöva importera-klassen och uttryckligen definiera vilken klient som du är mål för. Om du anropar konstruktorn för `InteractiveLoginAuthentication` kommer du också att uppmanas att logga in liknande anropen ovan.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Även om det är användbart för testning och inlärning hjälper interaktiva autentisering inte att hjälpa dig att skapa automatiserade eller automatiserade arbets flöden. Att konfigurera tjänstens huvud namns autentisering är den bästa metoden för automatiserade processer som använder SDK: n.

## <a name="set-up-service-principal-authentication"></a>Konfigurera tjänstens huvud namns autentisering

Den här processen är nödvändig för att aktivera autentisering som är frikopplad från en speciell användar inloggning, vilket gör att du kan autentisera till Azure Machine Learning python SDK i automatiserade arbets flöden. Med autentisering av tjänstens huvud namn kan du också [autentisera till REST API](#azure-machine-learning-rest-api-auth).

Om du vill konfigurera autentisering av tjänstens huvud namn skapar du först en app-registrering i Azure Active Directory och ger sedan din app-rollbaserad åtkomst till din ML-arbetsyta. Det enklaste sättet att slutföra installationen är genom [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) i Azure Portal. När du har loggat in på portalen klickar du på ikonen `>_` längst upp till höger på sidan för att öppna gränssnittet.

Om du inte har använt Cloud Shell tidigare på ditt Azure-konto måste du skapa en lagrings konto resurs för lagring av filer som skrivs. I allmänhet kommer det här lagrings kontot att innebära en försumbar månatlig kostnad. Installera dessutom Machine Learning-tillägget om du inte har använt det tidigare med följande kommando.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Du måste vara administratör för prenumerationen för att utföra följande steg.

Kör sedan följande kommando för att skapa tjänstens huvud namn. Ge den ett namn, i det här fallet **ml-autentisering**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

Utdata är en JSON som liknar följande. Anteckna fälten `clientId`, `clientSecret`och `tenantId` eftersom du behöver dem för andra steg i den här artikeln.

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

Kör sedan följande kommando för att hämta information om tjänstens huvud namn som du nyss skapade, med hjälp av `clientId` värde från ovan som indata till `--id`-parametern.

```azurecli-interactive
az ad sp show --id your-client-id
```

Följande är ett förenklat exempel på JSON-utdata från kommandot. Anteckna `objectId` fältet, eftersom du kommer att behöva värdet för nästa steg.

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

Använd sedan följande kommando för att tilldela tjänstens huvud namn åtkomst till din Machine Learning-arbetsyta. Du behöver namnet på din arbets yta och dess resurs grupp namn för `-w` respektive `-g` parametrar. För parametern `--user` använder du `objectId`-värdet från föregående steg. Med parametern `--role` kan du ange åtkomst rollen för tjänstens huvud namn, och i allmänhet kommer du att använda antingen **ägare** eller **deltagare**. Båda har skriv åtkomst till befintliga resurser som beräknings kluster och data lager, men endast **ägare** kan etablera dessa resurser. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Det här anropet genererar inga utdata, men du har nu konfigurerat autentisering av tjänstens huvud namn för din arbets yta.

## <a name="authenticate-to-your-workspace"></a>Autentisera till din arbets yta

Nu när du har aktiverat tjänstens huvud namn kan du autentisera till din arbets yta i SDK utan att logga in som en användare fysiskt. Använd konstruktorn `ServicePrincipalAuthentication` Class och Använd värdena som du fick från föregående steg som parametrar. Parametern `tenant_id` mappas till `tenantId` från ovan, `service_principal_id` mappas till `clientId`och `service_principal_password` mappas till `clientSecret`.

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Variabeln `sp` innehåller nu ett autentiseringscertifikat som du använder direkt i SDK. I allmänhet är det en bra idé att lagra de ID/hemligheter som används ovan i miljövariabler som du ser i följande kod.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

För automatiserade arbets flöden som körs i python och använder SDK: n främst kan du använda det här objektet som det är i de flesta fall för autentisering. Följande kod autentiserar till din arbets yta med det auth-objekt som du nyss skapade.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>Azure Machine Learning REST API auth

Tjänstens huvud namn som skapades i stegen ovan kan också användas för att autentisera till Azure Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/). Du använder den Azure Active Directory [tilldelningen av autentiseringsuppgifter för klient](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow), som tillåter tjänst-till-tjänst-anrop för konsol lös autentisering i automatiserade arbets flöden. Exemplen implementeras med [ADAL-biblioteket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) i både python och Node. js, men du kan också använda alla bibliotek med öppen källkod som stöder OpenID Connect 1,0. 

> [!NOTE]
> MSAL. js är ett nyare bibliotek än ADAL, men du kan inte utföra tjänst-till-tjänst-autentisering med hjälp av klientautentiseringsuppgifter med MSAL. js, eftersom det i huvudsak är ett bibliotek på klient sidan som är avsett för interaktivt/UI-autentisering som är kopplat till en speciell användare. Vi rekommenderar att du använder ADAL så som visas nedan för att bygga automatiserade arbets flöden med REST API.

### <a name="nodejs"></a>Node.js

Använd följande steg för att generera en auth-token med Node. js. Kör `npm install adal-node`i din miljö. Använd sedan `tenantId`, `clientId`och `clientSecret` från tjänstens huvud namn som du skapade i stegen ovan som värden för de matchande variablerna i följande skript.

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

Variabeln `tokenResponse` är ett objekt som inkluderar token och associerade metadata som förfallo tid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande är ett exempel svar.

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

Använd egenskapen `accessToken` för att hämta auth-token. I [REST API-dokumentationen](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) hittar du exempel på hur du använder token för att göra API-anrop.

### <a name="python"></a>Python 

Använd följande steg för att generera en auth-token med python. Kör `pip install adal`i din miljö. Använd sedan `tenantId`, `clientId`och `clientSecret` från tjänstens huvud namn som du skapade i stegen ovan som värden för lämpliga variabler i följande skript.

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

Variabeln `token_response` är en ord lista som inkluderar token och associerade metadata som förfallo tid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande är ett exempel svar.

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

Använd `token_response["accessToken"]` för att hämta auth-token. I [REST API-dokumentationen](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API) hittar du exempel på hur du använder token för att göra API-anrop.

## <a name="web-service-authentication"></a>Autentisering för webb tjänst

Webb tjänster i Azure Machine Learning använda ett annat autentiseringsschema än det som beskrivs ovan. Det enklaste sättet att autentisera till distribuerade webb tjänster är att använda **nyckelbaserad autentisering**, vilket genererar statiska nycklar för en statisk Bearer-typ som inte behöver uppdateras. Om du bara behöver autentisera till en distribuerad webb tjänst behöver du inte konfigurera service princip-autentisering som visas ovan.

Webb tjänster som distribueras i Azure Kubernetes-tjänsten har nyckelbaserad autentisering *aktiverat* som standard. Azure Container Instances distribuerade tjänster har nyckelbaserad autentisering *inaktive rad* som standard, men du kan aktivera den genom att ange `auth_enabled=True`när du skapar ACI-webb tjänsten. Följande är ett exempel på hur du skapar en distributions konfiguration för ACI med nyckelbaserad autentisering aktive rad.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enable=True)
```

Sedan kan du använda den anpassade ACI-konfigurationen i distributionen med hjälp av klassen `Model`.

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

Använd `aci_service.get_keys()`för att hämta auth-nycklarna. Om du vill återskapa en nyckel använder du funktionen `regen_key()` och skickar antingen **primär** eller **sekundär**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Webb tjänster har även stöd för tokenbaserad autentisering, men endast för Azure Kubernetes service-distributioner. Se [instruktionen för att använda](how-to-consume-web-service.md) webb tjänster för att få mer information om autentisering.

### <a name="token-based-web-service-authentication"></a>Tokenbaserad autentisering av webb tjänst

När du aktiverar token-autentisering för en webb tjänst måste användarna presentera en Azure Machine Learning JSON Web Token till webb tjänsten för att komma åt den. Token upphör att gälla efter en angiven tids period och måste uppdateras för att du ska kunna fortsätta att ringa.

* Token-autentisering **inaktive ras som standard** när du distribuerar till Azure Kubernetes-tjänsten.
* Token-autentisering **stöds inte** när du distribuerar till Azure Container instances.

Om du vill kontrol lera token-autentisering använder du parametern `token_auth_enabled` när du skapar eller uppdaterar en distribution.

Om token-autentisering har Aktiver ATS kan du använda metoden `get_token` för att hämta ett JSON Web Token (JWT) och den tokens förfallo tid:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter `refresh_by`s tiden för token. Om du behöver uppdatera tokens utanför python SDK: n, är det ett alternativ att använda REST API med tjänstens huvudsakliga autentisering för att regelbundet göra `service.get_token()`-anrop, enligt beskrivningen ovan.
>
> Vi rekommenderar starkt att du skapar din Azure Machine Learning arbets yta i samma region som ditt Azure Kubernetes service-kluster. 
>
> För att autentisera med en token kommer webb tjänsten att ringa till den region där din Azure Machine Learning arbets yta skapas. Om arbets ytans region inte är tillgänglig kan du inte hämta en token för din webb tjänst, även om klustret finns i en annan region än din arbets yta. Resultatet är att Azure AD-autentisering inte är tillgängligt förrän arbets ytans region är tillgänglig igen. 
>
> Dessutom ökar avståndet mellan klustrets region och arbets ytans region, desto längre tid tar det att hämta en token.

## <a name="next-steps"></a>Nästa steg

* [Träna och distribuera en bild klassificerings modell](tutorial-train-models-with-aml.md).
* [Använda en Azure Machine Learning modell som distribueras som en webb tjänst](how-to-consume-web-service.md).
