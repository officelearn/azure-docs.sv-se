---
title: Skapa en Docker-avbildning för modellhantering i Azure Machine Learning | Microsoft Docs
description: Den här artikeln beskriver stegen för att skapa en Docker-avbildning för webbtjänsten.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: c0f51e47038737d6aa743be718ad6b28c161c766
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35649395"
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Azure Machine Learning Model Management konto API-referens

Information om hur du konfigurerar distributionsmiljön finns i [kontoinställning för modellhantering](deployment-setup-configuration.md).

Azure Machine Learning Model Management konto API implementerar följande åtgärder:

- Modellen
- Skapa en manifest
- Skapa en docker-avbildning
- Skapa webbtjänst

Du kan använda den här bilden för att skapa en webbtjänst antingen lokalt eller på ett fjärranslutet Azure Container Service-kluster eller en annan Docker-stöd valfri miljö.

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att du har gått igenom de installationssteg som beskrivs i den [installera snabbstarten och skapa](../service/quickstart-installation.md) dokumentet.

Följande krävs innan du fortsätter:
1. Modellera kontoetablering
2. Skapa en miljö för att distribuera och hantera modeller
3. En Machine Learning-modell

### <a name="azure-ad-token"></a>Azure AD-token
När du använder Azure CLI kan logga in med hjälp av `az login`. CLI använder din Azure Active Directory (Azure AD)-token från .azure-filen. Om du vill använda API: erna har du följande alternativ.

##### <a name="acquire-the-azure-ad-token-manually"></a>Hämta Azure AD-token manuellt
Du kan använda `az login` och hämta senast åtkomsttoken från .azure-fil i din arbetskatalog.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Hämta Azure AD-token via programmering
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
Spara utdata när du har skapat tjänstens huvudnamn. Du kan nu använda som för att hämta en token från Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Token placeras i en auktoriseringsrubrik för API-anrop.


## <a name="register-a-model"></a>Registrera en modell

Registreringssteget modellen registrerar din Machine Learning-modell med Azure modellhanteringskontot som du skapade. Denna registrering kan spåra modeller och deras versioner som är tilldelade vid tidpunkten för registrering. Användaren anger namnet på modellen. Efterföljande registrering av modeller med samma namn som genererar en ny version och -ID.

### <a name="request"></a>Förfrågan

| Metod | Förfrågans URI |
|------------|------------|
| POST |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modeller 
 |
### <a name="description"></a>Beskrivning
Registrerar en modell.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| modell | brödtext | Nyttolasten som används för att registrera en modell. | Ja | [Modellen](#model) |


### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | OKEJ. Modell-registreringen har slutförts. | [Modellen](#model) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Fråga efter listan över modeller i ett konto
### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modeller 
 |
### <a name="description"></a>Beskrivning
Frågar listan över modeller i ett konto. Du kan filtrera resultatlistan efter tagg och namn. Om inget filter skickas anges i fråga alla modeller i kontot. Den returnerade listan sidnumrerade och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| namn | DocumentDB | Objektnamnet. | Nej | sträng |
| tagg | DocumentDB | Modeller tagg. | Nej | sträng |
| count | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedModelList](#paginatedmodellist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Hämta information om modellen
### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /models/ {id}  
 |

### <a name="description"></a>Beskrivning
Hämtar en modell av ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Modellen](#model) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registrera ett manifest med den registrerade modellen och alla beroenden

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / manifest | 

### <a name="description"></a>Beskrivning
Registrerar ett manifest med den registrerade modellen och alla dess beroenden.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| manifestRequest | brödtext | Nyttolasten som används för att registrera ett manifest. | Ja | [Manifest](#manifest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Manifest registreringen har lyckats. | [Manifest](#manifest) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Fråga listan med manifest i ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / manifest | 

### <a name="description"></a>Beskrivning
Söka i manifesten i ett konto. Du kan filtrera resultatlistan efter modell-ID och manifestnamn. Om inget filter skickas anges i fråga alla manifesten i kontot. Den returnerade listan sidnumrerade och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| modelId | DocumentDB | Modell-ID. | Nej | sträng |
| manifestName | DocumentDB | Manifestnamn. | Nej | sträng |
| count | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedManifestList](#paginatedmanifestlist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Få manifest information

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /manifests/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar manifestet efter-ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Manifest](#manifest) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Skapa en avbildning

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / avbildningar | 

### <a name="description"></a>Beskrivning
Skapar en avbildning som en Docker-avbildning i Azure Container Registry.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| imageRequest | brödtext | Nyttolasten som används för att skapa en avbildning. | Ja | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Sidhuvuden | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden plats-URL. Ett GET-anrop visar status för aktiviteten i avbildningen. | Åtgärden-plats |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Fråga efter listan över avbildningar i ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / avbildningar | 

### <a name="description"></a>Beskrivning
Frågar listan över avbildningar i ett konto. Du kan filtrera resultatlistan av manifest-ID och namn. Om inget filter skickas anges i fråga alla avbildningar i kontot. Den returnerade listan sidnumrerade och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| Manifest-ID | DocumentDB | Manifest-ID. | Nej | sträng |
| manifestName | DocumentDB | Manifestnamn. | Nej | sträng |
| count | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedImageList](#paginatedimagelist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Hämta avbildningsinformation

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /images/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar en bild av ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Bild-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Avbildning](#image) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Skapa en tjänst

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / services | 

### <a name="description"></a>Beskrivning
Skapar en tjänst från en avbildning.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| serviceRequest | brödtext | Nyttolasten som används för att skapa en tjänst. | Ja | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Sidhuvuden | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden plats-URL. Ett GET-anrop visar status för aktiviteten i tjänsten. | Åtgärden-plats |
| 409 | Det finns redan en tjänst med det angivna namnet. |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Fråga i listan över tjänster i ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / services | 

### <a name="description"></a>Beskrivning
Frågar i listan över tjänster i ett konto. Du kan filtrera resultatlistan av modellen namn/ID, manifest namn-ID, avbildnings-ID, namn eller resurs-ID. för beräkning av Machine Learning Om inget filter skickas anges i fråga alla tjänster i kontot. Den returnerade listan sidnumrerade och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| Tjänstnamn | DocumentDB | Tjänstnamn. | Nej | sträng |
| modelId | DocumentDB | Modellnamn. | Nej | sträng |
| %{ModelName/ | DocumentDB | Modell-ID. | Nej | sträng |
| Manifest-ID | DocumentDB | Manifest-ID. | Nej | sträng |
| manifestName | DocumentDB | Manifestnamn. | Nej | sträng |
| imageId | DocumentDB | Bild-ID. | Nej | sträng |
| computeResourceId | DocumentDB | Machine Learning beräkning resurs-ID. | Nej | sträng |
| count | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedServiceList](#paginatedservicelist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Hämta tjänstinformation

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar en tjänst efter-ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [ServiceResponse](#serviceresponse) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Uppdatera en tjänst

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| PUT |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Uppdaterar en befintlig tjänst.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| serviceUpdateRequest | brödtext | Nyttolasten som används för att uppdatera en befintlig tjänst. | Ja |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Sidhuvuden | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden plats-URL. Ett GET-anrop visar status för aktivitet för update-tjänsten. | Åtgärden-plats |
| 404 | Tjänsten med angivet ID finns inte. |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Ta bort en tjänst

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| DELETE |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Tar bort en tjänst.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. |  |
| 204 | Tjänsten med angivet ID finns inte. |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Hämta för tjänsten

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / nycklar | 

### <a name="description"></a>Beskrivning
Hämtar för tjänsten.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Service-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [AuthKeys](#authkeys)
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Återskapa för tjänsten

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / regenerateKeys | 

### <a name="description"></a>Beskrivning
Återskapar för tjänsten och returnerar dem.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Service-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| regenerateKeyRequest | brödtext | Nyttolasten som används för att uppdatera en befintlig tjänst. | Ja | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [AuthKeys](#authkeys)
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Fråga i listan över distributioner i ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / distributioner | 

### <a name="description"></a>Beskrivning
Frågar i listan över distributioner i ett konto. Du kan filtrera resultatlistan efter tjänst-ID, som returnerar bara de distributioner som har skapats för en viss tjänst. Om inget filter skickas anges i fråga alla distributioner i kontot.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |
| serviceId | DocumentDB | Service-ID. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [DeploymentList](#deploymentlist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Hämta information om distribution

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /deployments/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar distributionen av ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Distributions-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Distribution](#deployment) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Få åtgärdsinformation

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  / API/subscriptions / {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /operations/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar Åtgärdsstatus async efter åtgärden-ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure-prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellhanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på modellhanteringskontot. | Ja | sträng |
| id | sökväg | Åtgärds-ID. | Ja | sträng |
| API-versionen | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara något som liknar ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [OperationStatus](#asyncoperationstatus) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definitioner

<a name="asset"></a>
### <a name="asset"></a>Tillgång
Objektet tillgången som behövs när Docker-avbildning skapas.


|Namn|Beskrivning|Schema|
|---|---|---|
|**ID**  <br>*Valfritt*|Plats-ID.|sträng|
|**mimeType**  <br>*Valfritt*|MIME-typ av modellinnehåll. Mer information om MIME-typen finns i den [lista över medietyper IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|sträng|
|**Packa upp**  <br>*Valfritt*|Anger när vi behöver packa upp innehållet när Docker-avbildning skapas.|boolesk|
|**URL: en**  <br>*Valfritt*|Tillgången plats-URL.|sträng|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Det asynkrona åtgärden tillståndet.

*Typ*: uppräkning (Ej startad, körs, Cancelled, lyckades, misslyckades)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Åtgärdsstatus.


|Namn|Beskrivning|Schema|
|---|---|---|
|**createdTime**  <br>*Valfritt*  <br>*skrivskyddad*|Asynkrona åtgärden Skapandetid (UTC).|sträng (datum / tid)|
|**endTime**  <br>*Valfritt*  <br>*skrivskyddad*|Sluttid för asynkron åtgärd (UTC).|sträng (datum / tid)|
|**Fel**  <br>*Valfritt*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Valfritt*|Async-åtgärds-ID.|sträng|
|**Åtgärdstyp**  <br>*Valfritt*|Async åtgärdstyp.|Uppräkning (bild, Service)|
|**resourceLocation**  <br>*Valfritt*|Resursen skapas eller uppdateras med den asynkron åtgärden.|sträng|
|**tillstånd**  <br>*Valfritt*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Autentiseringsnycklar för en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**primaryKey**  <br>*Valfritt*|Primär nyckel.|sträng|
|**sekundär nyckel**  <br>*Valfritt*|Sekundär nyckel.|sträng|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>Autoskalningen
Inställningar för autoskalningen.


|Namn|Beskrivning|Schema|
|---|---|---|
|**autoscaleEnabled**  <br>*Valfritt*|Aktivera eller inaktivera autoskalningen.|boolesk|
|**maxReplicas**  <br>*Valfritt*|Högsta antalet poddrepliker att skala upp till.  <br>**Minimivärdet**: `1`|heltal|
|**minReplicas**  <br>*Valfritt*|Minsta antalet poddrepliker att skala ned till.  <br>**Minimivärdet**: `0`|heltal|
|**refreshPeriodInSeconds**  <br>*Valfritt*|Uppdateringstid för automatisk skalning utlösare.  <br>**Minimivärdet**: `1`|heltal|
|**targetUtilization**  <br>*Valfritt*|Utnyttjandeprocent som utlöser automatisk skalning.  <br>**Minimivärdet**: `0`  <br>**Maxvärde**: `100`|heltal|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Machine Learning beräkningsresursen.


|Namn|Beskrivning|Schema|
|---|---|---|
|**ID**  <br>*Valfritt*|Resurs-ID.|sträng|
|**typ**  <br>*Valfritt*|Typ av resurs.|Uppräkning (kluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Konfiguration för att reservera resurser för en behållare i klustret.


|Namn|Beskrivning|Schema|
|---|---|---|
|**CPU**  <br>*Valfritt*|Anger CPU-reservation. Format för Kubernetes: se [betydelsen av CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|sträng|
|**Minne**  <br>*Valfritt*|Anger minnesreservation. Format för Kubernetes: se [innebörden av minne](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|sträng|


<a name="deployment"></a>
### <a name="deployment"></a>Distribution
En instans av en Azure Machine Learning-distribution.


|Namn|Beskrivning|Schema|
|---|---|---|
|**createdAt**  <br>*Valfritt*  <br>*skrivskyddad*|Distribution Skapandetid (UTC).|sträng (datum / tid)|
|**expiredAt**  <br>*Valfritt*  <br>*skrivskyddad*|Distributionen har upphört att gälla tid (UTC).|sträng (datum / tid)|
|**ID**  <br>*Valfritt*|Distributions-ID.|sträng|
|**imageId**  <br>*Valfritt*|Avbildnings-ID som är associerade med den här distributionen.|sträng|
|**Tjänstnamn**  <br>*Valfritt*|Tjänstnamn.|sträng|
|**Status**  <br>*Valfritt*|Aktuell distributionsstatus.|sträng|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
En matris med objekt.

*Typ*: <[distribution](#deployment)> matris


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Modell felinformation för Management-tjänsten.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Kod**  <br>*Krävs*|Felkod.|sträng|
|**meddelande**  <br>*Krävs*|Felmeddelande.|sträng|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Modellhantering service felobjekt.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Kod**  <br>*Krävs*|Felkod.|sträng|
|**Information om**  <br>*Valfritt*|Matris med objekt för fel.|<[ErrorDetail](#errordetail)> matris|
|**meddelande**  <br>*Krävs*|Felmeddelande.|sträng|
|**statusCode**  <br>*Valfritt*|HTTP-statuskod.|heltal|


<a name="image"></a>
### <a name="image"></a>Bild
Azure Machine Learning-bild.


|Namn|Beskrivning|Schema|
|---|---|---|
|**computeResourceId**  <br>*Valfritt*|ID för den miljö som skapats i Machine Learning beräkningsresursen.|sträng|
|**createdTime**  <br>*Valfritt*|Bild Skapandetid (UTC).|sträng (datum / tid)|
|**creationState**  <br>*Valfritt*||[AsyncOperationState](#asyncoperationstate)|
|**Beskrivning**  <br>*Valfritt*|Beskrivningstext för avbildningen.|sträng|
|**Fel**  <br>*Valfritt*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Valfritt*|Bild-ID.|sträng|
|**imageBuildLogUri**  <br>*Valfritt*|URI för överförda loggar från avbildningen versionen.|sträng|
|**imageLocation**  <br>*Valfritt*|Azure Container Registry plats sträng för den skapa avbildningen.|sträng|
|**ImageType**  <br>*Valfritt*||[ImageType](#imagetype)|
|**Manifest**  <br>*Valfritt*||[Manifest](#manifest)|
|**Namn**  <br>*Valfritt*|Avbildningens namn.|sträng|
|**Version**  <br>*Valfritt*|Avbildningsversion som angetts av tjänsten modellhantering.|heltal|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
En begäran att skapa en Azure Machine Learning-avbildning.


|Namn|Beskrivning|Schema|
|---|---|---|
|**computeResourceId**  <br>*Krävs*|ID för den miljö som skapats i Machine Learning beräkningsresursen.|sträng|
|**Beskrivning**  <br>*Valfritt*|Beskrivningstext för avbildningen.|sträng|
|**ImageType**  <br>*Krävs*||[ImageType](#imagetype)|
|**Manifest-ID**  <br>*Krävs*|ID för manifestet som avbildningen ska skapas.|sträng|
|**Namn**  <br>*Krävs*|Avbildningens namn.|sträng|


<a name="imagetype"></a>
### <a name="imagetype"></a>Bildtyp
Anger vilken typ av avbildningen.

*Typ*: uppräkning (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
Azure Machine Learning-manifestet.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Tillgångar**  <br>*Krävs*|Lista över tillgångar.|<[Tillgången](#asset)> matris|
|**createdTime**  <br>*Valfritt*  <br>*skrivskyddad*|Manifest Skapandetid (UTC).|sträng (datum / tid)|
|**Beskrivning**  <br>*Valfritt*|Manifest beskrivningstext.|sträng|
|**driverProgram**  <br>*Krävs*|Driver-program för manifestet.|sträng|
|**ID**  <br>*Valfritt*|Manifest-ID.|sträng|
|**modelIds**  <br>*Valfritt*|Lista över modell-ID: N för de registrerade modellerna. Begäran misslyckas om någon av de inkluderade modellerna inte är registrerad.|<string> Matris|
|**modelType**  <br>*Valfritt*|Anger att modeller är redan registrerad med tjänsten modellhantering.|Uppräkning (registrerad)|
|**Namn**  <br>*Krävs*|Manifestnamn.|sträng|
|**targetRuntime**  <br>*Krävs*||[TargetRuntime](#targetruntime)|
|**Version**  <br>*Valfritt*  <br>*skrivskyddad*|Tilldelats av tjänsten modellhantering version av klustermanifestet.|heltal|
|**webserviceType**  <br>*Valfritt*|Anger den önskade typen av webbtjänsten som skapas från manifestet.|Uppräkning (realtid)|


<a name="model"></a>
### <a name="model"></a>Modell
En instans av en Azure Machine Learning-modell.


|Namn|Beskrivning|Schema|
|---|---|---|
|**createdAt**  <br>*Valfritt*  <br>*skrivskyddad*|Modellen Skapandetid (UTC).|sträng (datum / tid)|
|**Beskrivning**  <br>*Valfritt*|Beskrivningstext för modellen.|sträng|
|**ID**  <br>*Valfritt*  <br>*skrivskyddad*|Modell-ID.|sträng|
|**mimeType**  <br>*Krävs*|MIME-typ av modellinnehåll. Mer information om MIME-typen finns i den [lista över medietyper IANA](https://www.iana.org/assignments/media-types/media-types.xhtml).|sträng|
|**Namn**  <br>*Krävs*|Modellnamn.|sträng|
|**tagg**  <br>*Valfritt*|Tagglista för modellen.|<string> Matris|
|**Packa upp**  <br>*Valfritt*|Anger om vi behöver packa upp modellen när Docker-avbildning skapas.|boolesk|
|**URL: en**  <br>*Krävs*|URL för modellen. Vanligtvis placerar vi här du en URL för signatur för delad åtkomst.|sträng|
|**Version**  <br>*Valfritt*  <br>*skrivskyddad*|Modellversion som tilldelats av tjänsten modellhantering.|heltal|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Modellen data samlingsinformation.


|Namn|Beskrivning|Schema|
|---|---|---|
|**eventHubEnabled**  <br>*Valfritt*|Aktivera en händelsehubb för en tjänst.|boolesk|
|**storageEnabled**  <br>*Valfritt*|Aktivera lagring för en tjänst.|boolesk|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
En sidnumrerade lista över avbildningar.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfritt*|Fortsättning länk (absolut URI) till nästa sida i resultatet i listan.|sträng|
|**värde**  <br>*Valfritt*|Matris med modellobjekt.|<[Bild](#image)> matris|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
En sidnumrerade listan med manifest.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfritt*|Fortsättning länk (absolut URI) till nästa sida i resultatet i listan.|sträng|
|**värde**  <br>*Valfritt*|Matris med manifest objekt.|<[Manifest](#manifest)> matris|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
En sidnumrerade lista över modeller.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfritt*|Fortsättning länk (absolut URI) till nästa sida i resultatet i listan.|sträng|
|**värde**  <br>*Valfritt*|Matris med modellobjekt.|<[Modellen](#model)> matris|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
En sidnumrerade lista över tjänster.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfritt*|Fortsättning länk (absolut URI) till nästa sida i resultatet i listan.|sträng|
|**värde**  <br>*Valfritt*|Matris med-tjänstobjekt.|<[ServiceResponse](#serviceresponse)> matris|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
En begäran att skapa en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*Valfritt*|Aktivera application insights för en tjänst.|boolesk|
|**Autoskalningen**  <br>*Valfritt*||[Autoskalningen](#autoscaler)|
|**ComputeResource**  <br>*Krävs*||[ComputeResource](#computeresource)|
|**ContainerResourceReservation**  <br>*Valfritt*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Valfritt*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Krävs*|Bild för att skapa tjänsten.|sträng|
|**maxConcurrentRequestsPerContainer**  <br>*Valfritt*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**: `1`|heltal|
|**Namn**  <br>*Krävs*|Tjänstnamn.|sträng|
|**numReplicas**  <br>*Valfritt*|Antalet poddrepliker som körs när som helst. Kan inte anges om Autoskalningen är aktiverad.  <br>**Minimivärdet**: `0`|heltal|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
En begäran att återskapa en nyckel för en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**KeyType**  <br>*Valfritt*|Anger vilken nyckel som ska återskapas.|Uppräkning (primär, sekundär)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Detaljerad status för tjänsten.


|Namn|Beskrivning|Schema|
|---|---|---|
|**createdAt**  <br>*Valfritt*|Tjänsten Skapandetid (UTC).|sträng (datum / tid)|
|**ID**  <br>*Valfritt*|Service-ID.|sträng|
|**bild**  <br>*Valfritt*||[Avbildning](#image)|
|**Manifest**  <br>*Valfritt*||[Manifest](#manifest)|
|**modeller**  <br>*Valfritt*|Listan över modeller.|<[Modellen](#model)> matris|
|**Namn**  <br>*Valfritt*|Tjänstnamn.|sträng|
|**scoringUri**  <br>*Valfritt*|URI för bedömning av tjänsten.|sträng|
|**tillstånd**  <br>*Valfritt*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*Valfritt*|Senaste Uppdateringstid (UTC).|sträng (datum / tid)|
|**appInsightsEnabled**  <br>*Valfritt*|Aktivera application insights för en tjänst.|boolesk|
|**Autoskalningen**  <br>*Valfritt*||[Autoskalningen](#autoscaler)|
|**ComputeResource**  <br>*Krävs*||[ComputeResource](#computeresource)|
|**ContainerResourceReservation**  <br>*Valfritt*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Valfritt*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*Valfritt*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**: `1`|heltal|
|**numReplicas**  <br>*Valfritt*|Antalet poddrepliker som körs när som helst. Kan inte anges om Autoskalningen är aktiverad.  <br>**Minimivärdet**: `0`|heltal|
|**Fel**  <br>*Valfritt*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
En begäran att uppdatera en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*Valfritt*|Aktivera application insights för en tjänst.|boolesk|
|**Autoskalningen**  <br>*Valfritt*||[Autoskalningen](#autoscaler)|
|**ContainerResourceReservation**  <br>*Valfritt*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*Valfritt*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Valfritt*|Bild för att skapa tjänsten.|sträng|
|**maxConcurrentRequestsPerContainer**  <br>*Valfritt*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**: `1`|heltal|
|**numReplicas**  <br>*Valfritt*|Antalet poddrepliker som körs när som helst. Kan inte anges om Autoskalningen är aktiverad.  <br>**Minimivärdet**: `0`|heltal|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Typ av mål-runtime.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Egenskaper**  <br>*Krävs*||< string, string > karta|
|**runtimeType**  <br>*Krävs*|Anger körningen.|Uppräkning (SparkPython, Python)|

