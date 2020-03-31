---
title: Konfigurera autentisering
titleSuffix: Azure Machine Learning
description: Lär dig hur du konfigurerar och konfigurerar autentisering för olika resurser och arbetsflöden i Azure Machine Learning. Det finns flera sätt att konfigurera och använda autentisering inom tjänsten, allt från enkel UI-baserad auth för utvecklings- eller testningsändamål, till fullständig Azure Active Directory-tjänsthuvudbefogning.
services: machine-learning
author: trevorbye
ms.author: trbye
ms.reviewer: trbye
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 12/17/2019
ms.openlocfilehash: fcaa7a0c44851d6b48b40b01af4c8ec992c330b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79283542"
---
# <a name="set-up-authentication-for-azure-machine-learning-resources-and-workflows"></a>Konfigurera autentisering för Azure Machine Learning-resurser och arbetsflöden
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

I den här artikeln får du lära dig hur du konfigurerar och konfigurerar autentisering för olika resurser och arbetsflöden i Azure Machine Learning. Det finns flera sätt att autentisera till tjänsten, allt från enkel UI-baserad auth för utvecklings- eller testningsändamål till fullständig Azure Active Directory-tjänsthuvudbefogning. I den här artikeln beskrivs också skillnaderna i hur webbtjänstautentisering fungerar, samt hur du autentiserar till AZURE Machine Learning REST API.

Den här uppgiften visar hur du utför följande uppgifter:

* Använda interaktiv gränssnittsautentisering för testning/utveckling
* Konfigurera autentisering av tjänstens huvudnamn
* Autentisera till din arbetsyta
* Hämta OAuth2.0-innehavartypstoken för AZURE Machine Learning REST API
* Förstå webbtjänstautentisering

Se [konceptartikeln](concept-enterprise-security.md) för en allmän översikt över säkerhet och autentisering i Azure Machine Learning.

## <a name="prerequisites"></a>Krav

* Skapa en [Azure Machine Learning-arbetsyta](how-to-manage-workspace.md).
* [Konfigurera din utvecklingsmiljö](how-to-configure-environment.md) för att installera Azure Machine Learning SDK eller använd en [virtuell virtuell azure machine learning-anteckningsbok](concept-azure-machine-learning-architecture.md#compute-instance) med SDK redan installerat.

## <a name="interactive-authentication"></a>Interaktiv autentisering

De flesta exempel i dokumentationen för den här tjänsten använder interaktiv autentisering i Jupyter-anteckningsböcker som en enkel metod för testning och demonstration. Detta är ett lätt sätt att testa vad du bygger. Det finns två funktionsanrop som automatiskt kommer att uppmana dig med ett gränssnittsbaserat autentiseringsflöde.

När `from_config()` du anropar funktionen uppstår prompten.

```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Funktionen `from_config()` söker efter en JSON-fil som innehåller anslutningsinformationen för arbetsytan. Du kan också ange anslutningsinformation `Workspace` uttryckligen med hjälp av konstruktorn, som också kommer att fråga efter interaktiv autentisering. Båda samtalen är likvärdiga.

```python
ws = Workspace(subscription_id="your-sub-id",
               resource_group="your-resource-group-id",
               workspace_name="your-workspace-name"
              )
```

Om du har åtkomst till flera klienter kan du behöva importera klassen och uttryckligen definiera vilken klient du riktar in dig på. Ringa konstruktören för `InteractiveLoginAuthentication` kommer också att uppmana dig att logga in liknar samtalen ovan.

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Interaktiv autentisering hjälper dig inte att skapa automatiserade eller huvudlösa arbetsflöden, även om du är användbar för testning och inlärning. Att konfigurera tjänsthuvudhuvudautentisering är den bästa metoden för automatiserade processer som använder SDK.

## <a name="set-up-service-principal-authentication"></a>Konfigurera autentisering av tjänstens huvudnamn

Den här processen är nödvändig för att aktivera autentisering som är frikopplad från en viss användarinloggning, vilket gör att du kan autentisera till Azure Machine Learning Python SDK i automatiserade arbetsflöden. Med tjänstens huvudautentisering kan du också [autentisera till REST API.](#azure-machine-learning-rest-api-auth)

Om du vill konfigurera autentisering av tjänstens huvudnamn skapar du först en appregistrering i Azure Active Directory och beviljar sedan appen rollbaserad åtkomst till din ML-arbetsyta. Det enklaste sättet att slutföra den här installationen är via [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) i Azure-portalen. När du har loggat `>_` in på portalen klickar du på ikonen längst upp till höger på sidan nära ditt namn för att öppna skalet.

Om du inte har använt molnskalet tidigare i ditt Azure-konto måste du skapa en lagringskontoresurs för att lagra filer som skrivs. I allmänhet kommer detta lagringskonto att medföra en försumbar månadskostnad. Installera dessutom maskininlärningstillägget om du inte har använt det tidigare med följande kommando.

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!NOTE]
> Du måste vara administratör för prenumerationen för att kunna utföra följande steg.

Kör sedan följande kommando för att skapa tjänstens huvudnamn. Ge det ett namn, i detta fall **ml-auth**.

```azurecli-interactive
az ad sp create-for-rbac --sdk-auth --name ml-auth
```

Utdata kommer att vara en JSON som liknar följande. Anteckna `clientId`fälten `clientSecret`, `tenantId` och eftersom du behöver dem för andra steg i den här artikeln.

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

Kör sedan följande kommando för att få information om tjänstens `clientId` huvudnamn som du `--id` just skapade, med värdet ovanifrån som indata till parametern.

```azurecli-interactive
az ad sp show --id your-client-id
```

Följande är ett förenklat exempel på JSON-utdata från kommandot. Notera fältet, `objectId` eftersom du behöver dess värde för nästa steg.

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

Använd sedan följande kommando för att tilldela tjänstens huvudåtkomst till din maskininlärningsarbetsyta. Du behöver ditt arbetsytenamn och dess `-w` resursgruppsnamn för parametrarna respektive. `-g` Använd `--user` `objectId` värdet från föregående steg för parametern. Med `--role` parametern kan du ange åtkomstrollen för tjänstens huvudnamn, och i allmänhet använder du antingen **ägare** eller **deltagare**. Båda har skrivbehörighet till befintliga resurser som beräkningskluster och datalager, men endast **ägaren** kan etablera dessa resurser. 

```azurecli-interactive
az ml workspace share -w your-workspace-name -g your-resource-group-name --user your-sp-object-id --role owner
```

Det här anropet ger inga utdata, men du har nu konfigurerad autentisering av tjänstens huvudnamn för arbetsytan.

## <a name="authenticate-to-your-workspace"></a>Autentisera till din arbetsyta

Nu när du har aktiverat tjänstens huvudansvariga kan du autentisera till arbetsytan i SDK utan att fysiskt logga in som användare. Använd `ServicePrincipalAuthentication` klasskonstruktorn och använd de värden du fick från föregående steg som parametrar. Parametern `tenant_id` mappar `tenantId` till `service_principal_id` ovanifrån, `service_principal_password` mappar `clientSecret`till `clientId`och mappar till .

```python
from azureml.core.authentication import ServicePrincipalAuthentication

sp = ServicePrincipalAuthentication(tenant_id="your-tenant-id", # tenantID
                                    service_principal_id="your-client-id", # clientId
                                    service_principal_password="your-client-secret") # clientSecret
```

Variabeln `sp` innehåller nu ett autentiseringsobjekt som du använder direkt i SDK. I allmänhet är det en bra idé att lagra ids / hemligheter som används ovan i miljövariabler som visas i följande kod.

```python
import os 

sp = ServicePrincipalAuthentication(tenant_id=os.environ['AML_TENANT_ID'],
                                    service_principal_id=os.environ['AML_PRINCIPAL_ID'],
                                    service_principal_password=os.environ['AML_PRINCIPAL_PASS'])
```

För automatiserade arbetsflöden som körs i Python och i första hand använda SDK kan du använda det här objektet som i de flesta fall för din autentisering. Följande kod autentiserar till arbetsytan med det auth-objekt som du just skapade.

```python
from azureml.core import Workspace

ws = Workspace.get(name="ml-example", 
                   auth=sp,
                   subscription_id="your-sub-id")
ws.get_details()
```

## <a name="azure-machine-learning-rest-api-auth"></a>AZURE Machine Learning REST API-autentisering

Tjänsthuvudhuvudnamnet som skapas i stegen ovan kan också användas för att autentisera till AZURE Machine Learning [REST API](https://docs.microsoft.com/rest/api/azureml/). Du använder Azure Active [Directory-klientautentiseringsuppgifter bevilja flöde](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow), som tillåter service-to-service-samtal för huvudlös autentisering i automatiserade arbetsflöden. Exemplen implementeras med [ADAL-biblioteket](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries) i både Python och Node.js, men du kan också använda alla bibliotek med öppen källkod som stöder OpenID Connect 1.0. 

> [!NOTE]
> MSAL.js är ett nyare bibliotek än ADAL, men du kan inte göra autentisering mellan tjänst och tjänst med klientautentisering med MSAL.js, eftersom det i första hand är ett bibliotek på klientsidan som är avsett för interaktiv/gränssnittsautentisering som är knuten till en viss användare. Vi rekommenderar att du använder ADAL enligt nedan för att skapa automatiserade arbetsflöden med REST API.

### <a name="nodejs"></a>Node.js

Följ följande steg för att generera en auth-token med Node.js. Kör i din `npm install adal-node`miljö . Använd sedan `tenantId`ditt `clientId`, `clientSecret` och från tjänstens huvudnamn som du skapade i stegen ovan som värden för de matchande variablerna i följande skript.

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

Variabeln `tokenResponse` är ett objekt som innehåller token och associerade metadata, till exempel förfallotid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande är ett exempelsvar.

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

Använd `accessToken` egenskapen för att hämta auth-token. Mer om hur du använder token för att ringa API-anrop finns i [REST API-dokumentationen.](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)

### <a name="python"></a>Python 

Följ följande steg för att generera en auth-token med Python. Kör i din `pip install adal`miljö . Använd sedan `tenantId`ditt `clientId`, `clientSecret` och från tjänstens huvudnamn som du skapade i stegen ovan som värden för lämpliga variabler i följande skript.

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

Variabeln `token_response` är en ordlista som innehåller token och associerade metadata, till exempel förfallotid. Token är giltiga i 1 timme och kan uppdateras genom att köra samma anrop igen för att hämta en ny token. Följande är ett exempelsvar.

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

Används `token_response["accessToken"]` för att hämta auth-token. Mer om hur du använder token för att ringa API-anrop finns i [REST API-dokumentationen.](https://github.com/microsoft/MLOps/tree/master/examples/AzureML-REST-API)

## <a name="web-service-authentication"></a>Webbtjänstautentisering

Webbtjänster i Azure Machine Learning använder ett annat autentiseringsmönster än vad som beskrivs ovan. Det enklaste sättet att autentisera till distribuerade webbtjänster är att använda **nyckelbaserad autentisering**, som genererar autentiseringsnycklar av statisk bäraretyp som inte behöver uppdateras. Om du bara behöver autentisera till en distribuerad webbtjänst behöver du inte konfigurera autentisering av tjänstprinciper enligt ovan.

Webbtjänster som distribueras på Azure Kubernetes-tjänsten har nyckelbaserad autentisering *aktiverad* som standard. Azure Container Instances distribuerade tjänster har nyckelbaserad auth *inaktiverad* `auth_enabled=True`som standard, men du kan aktivera den genom att ange när du skapar ACI-webbtjänsten. Följande är ett exempel på att skapa en ACI-distributionskonfiguration med nyckelbaserad auth aktiverad.

```python
from azureml.core.webservice import AciWebservice

aci_config = AciWebservice.deploy_configuration(cpu_cores = 1,
                                                memory_gb = 1,
                                                auth_enabled=True)
```

Sedan kan du använda den anpassade ACI-konfigurationen i distributionen `Model` med klassen.

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

Om du vill hämta `aci_service.get_keys()`auth-tangenterna använder du . Om du vill återskapa `regen_key()` en nyckel använder du funktionen och skickar antingen **Primär** eller **Sekundär**.

```python
aci_service.regen_key("Primary")
# or
aci_service.regen_key("Secondary")
```

Webbtjänster stöder också tokenbaserad autentisering, men endast för Azure Kubernetes-tjänstdistributioner. Mer information om hur du autentisering finns i [hur du](how-to-consume-web-service.md) kan använda webbtjänster.

### <a name="token-based-web-service-authentication"></a>Tokenbaserad webbtjänstautentisering

När du aktiverar tokenautentisering för en webbtjänst måste användare presentera en Azure Machine Learning JSON-webbtoken för webbtjänsten för att komma åt den. Token upphör att gälla efter en angiven tidsram och måste uppdateras för att fortsätta ringa samtal.

* Tokenautentisering **inaktiveras som standard** när du distribuerar till Azure Kubernetes Service.
* Tokenautentisering **stöds inte** när du distribuerar till Azure Container Instances.

Om du vill styra `token_auth_enabled` tokenautentisering använder du parametern när du skapar eller uppdaterar en distribution.

Om tokenautentisering är aktiverat kan `get_token` du använda metoden för att hämta en JWT (JWT) (JSON Web Token) och den tokens förfallotid:

```python
token, refresh_by = service.get_token()
print(token)
```

> [!IMPORTANT]
> Du måste begära en ny token efter `refresh_by` tokens tid. Om du behöver uppdatera token utanför Python SDK är ett alternativ att använda REST API med `service.get_token()` tjänsthuvudvillkorsautentisering för att regelbundet ringa samtalet, som diskuterats tidigare.
>
> Vi rekommenderar starkt att du skapar din Azure Machine Learning-arbetsyta i samma region som ditt Azure Kubernetes Service-kluster. 
>
> Om du vill autentisera med en token ringer webbtjänsten ett anrop till den region där arbetsytan Azure Machine Learning skapas. Om arbetsytans region inte är tillgänglig kan du inte hämta en token för webbtjänsten, även om klustret ligger i en annan region än arbetsytan. Resultatet är att Azure AD-autentisering inte är tillgänglig förrän arbetsytans region är tillgänglig igen. 
>
> Ju större avstånd det tar mellan klustrets region och arbetsytans region, desto längre tid tar det att hämta en token.

## <a name="next-steps"></a>Nästa steg

* [Träna och distribuera en bildklassificeringsmodell](tutorial-train-models-with-aml.md).
* [Använda en Azure Machine Learning-modell som distribueras som en webbtjänst](how-to-consume-web-service.md).
