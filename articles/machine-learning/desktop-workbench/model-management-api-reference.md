---
title: Skapa en Docker-avbildning för hantering av modellen i Azure Machine Learning | Microsoft Docs
description: Den här artikeln beskriver stegen för att skapa en Docker-avbildning för webbtjänsten.
services: machine-learning
author: chhavib
ms.author: chhavib
manager: hjerez
editor: jasonwhowell
ms.reviewer: jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 3efc351861a7d5b3c02c08b96494e83a3ccb99f6
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Azure Machine Learning modellen Management konto API-referens

Information om hur du konfigurerar distributionsmiljön finns [modellen Management kontoinställning](deployment-setup-configuration.md).

Azure Machine Learning modellen Management konto API implementerar följande åtgärder:

- Modellregistreringen
- Skapa en manifest
- Skapa en docker bild
- Skapa en Web service

Du kan använda den här avbildningen för att skapa en webbtjänst antingen lokalt eller på en fjärransluten Azure Container Service-kluster eller en annan Docker-stödd miljö önskat.

## <a name="prerequisites"></a>Förutsättningar
Kontrollera att du har gått igenom följande steg i den [installera och skapa Quickstart](../service/quickstart-installation.md) dokumentet.

Följande krävs innan du går vidare:
1. Modell kontoetablering
2. Skapa en miljö för att distribuera och hantera modeller
3. En Machine Learning-modell

### <a name="azure-ad-token"></a>Azure AD-token
När du använder Azure CLI, logga in med hjälp av `az login`. CLI använder Azure Active Directory (Azure AD)-token från filen .azure. Om du vill använda API: erna har du följande alternativ.

##### <a name="acquire-the-azure-ad-token-manually"></a>Hämta Azure AD-token manuellt
Du kan använda `az login` och hämta senast token från filen .azure på arbetskatalogen.

##### <a name="acquire-the-azure-ad-token-programmatically"></a>Hämta Azure AD-token programmässigt
```
az ad sp create-for-rbac --scopes /subscriptions/<SubscriptionId>/resourcegroups/<ResourceGroupName> --role Contributor --years <length of time> --name <MyservicePrincipalContributor>
```
När du har skapat tjänstens huvudnamn spara utdata. Nu kan du använda som för att hämta en token från Azure AD:

```cs
 private static async Task<string> AcquireTokenAsync(string clientId, string password, string authority, string resource)
{
        var creds = new ClientCredential(clientId, password);
        var context = new AuthenticationContext(authority);
        var token = await context.AcquireTokenAsync(resource, creds).ConfigureAwait(false);
        return token.AccessToken;
}
```
Token placeras i ett authorization-huvud för API-anrop.


## <a name="register-a-model"></a>Registrera en modell

Registreringssteget modellen registrerar din modell för Machine Learning med Azure modellen Management-kontot som du skapade. Denna registrering aktiverar spårning modeller och versioner som är tilldelade vid tiden för registrering. Användaren anger namnet på modellen. Efterföljande registrering av modeller med samma namn skapas en ny version och -ID.

### <a name="request"></a>Förfrågan

| Metod | Förfrågans URI |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modeller 
 |
### <a name="description"></a>Beskrivning
Registrerar en modell.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| modell | brödtext | -Nyttolast som används för att registrera en modell. | Ja | [Modellen](#model) |


### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | OKEJ. Modellregistreringen lyckades. | [Modellen](#model) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Fråga efter listan över modeller i ett konto
### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modeller 
 |
### <a name="description"></a>Beskrivning
Frågar i listan över modeller på ett konto. Du kan filtrera resultatlistan av taggen och namn. Om inget filter skickas anges i fråga alla modeller i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| namn | DocumentDB | Objektnamnet. | Nej | sträng |
| tagg | DocumentDB | Modellen tagg. | Nej | sträng |
| antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedModelList](#paginatedmodellist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Hämta modellinformation om
### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /models/ {id}  
 |

### <a name="description"></a>Beskrivning
Hämtar en modell med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Modellen](#model) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registrera ett manifest med registrerad modell och alla beroenden

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / visar | 

### <a name="description"></a>Beskrivning
Registrerar ett manifest med registrerad modell och dess beroenden.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| manifestRequest | brödtext | -Nyttolast som används för att registrera ett manifest. | Ja | [Manifestet](#manifest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Manifestet registreringen har lyckats. | [Manifestet](#manifest) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Fråga efter listan över manifesten i ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / visar | 

### <a name="description"></a>Beskrivning
Söka i manifesten i ett konto. Du kan filtrera resultatlistan av modell-ID och manifest namn. Om inget filter skickas visar frågan i manifesten i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| %{ModelID/ | DocumentDB | Modell-ID. | Nej | sträng |
| ManifestName | DocumentDB | Manifestfilens namn. | Nej | sträng |
| antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedManifestList](#paginatedmanifestlist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Hämta information om manifest

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /manifests/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar manifestet efter-ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Manifestet](#manifest) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Skapa en avbildning

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / bilder | 

### <a name="description"></a>Beskrivning
Skapar en avbildning som en Docker-avbildning i Azure Container registret.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| imageRequest | brödtext | -Nyttolast som används för att skapa en avbildning. | Ja | [imageRequest](#imagerequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Sidhuvuden | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden-URL: en. GET-anrop visas statusen för aktiviteten skapa avbildningen. | Platsen igen |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Fråga efter listan över bilder i ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / bilder | 

### <a name="description"></a>Beskrivning
Frågar listan över bilder i ett konto. Du kan filtrera resultatlistan av manifestet ID och namn. Om inget filter skickas visar frågan alla bilder i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| Manifest | DocumentDB | Manifest-ID. | Nej | sträng |
| ManifestName | DocumentDB | Manifestfilens namn. | Nej | sträng |
| antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
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
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /images/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar en avbildning med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Bild-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Avbildning](#image) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Skapa en tjänst

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} eller-tjänster | 

### <a name="description"></a>Beskrivning
Skapar en tjänst från en avbildning.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| serviceRequest | brödtext | -Nyttolast som används för att skapa en tjänst. | Ja | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Sidhuvuden | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden-URL: en. GET-anrop visas statusen för aktiviteten skapa tjänst. | Platsen igen |
| 409 | Det finns redan en tjänst med det angivna namnet. |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Fråga efter listan över tjänster i ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} eller-tjänster | 

### <a name="description"></a>Beskrivning
Frågar listan över tjänster på ett konto. Du kan filtrera resultatet av modellen namn/ID, manifestet namn-ID, avbildnings-ID, namn eller Machine Learning beräkning resurs-ID. Om inget filter skickas visar frågan alla tjänster i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| Tjänstnamn | DocumentDB | Tjänstnamn. | Nej | sträng |
| %{ModelID/ | DocumentDB | Modellnamnet. | Nej | sträng |
| %{ModelName/ | DocumentDB | Modell-ID. | Nej | sträng |
| Manifest | DocumentDB | Manifest-ID. | Nej | sträng |
| ManifestName | DocumentDB | Manifestfilens namn. | Nej | sträng |
| imageId | DocumentDB | Bild-ID. | Nej | sträng |
| computeResourceId | DocumentDB | Machine Learning beräkning resurs-ID. | Nej | sträng |
| antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedServiceList](#paginatedservicelist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Hämta information om tjänst

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar en tjänst med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [ServiceResponse](#serviceresponse) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Uppdatera en tjänst

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| PUT |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Uppdaterar en befintlig tjänst.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| serviceUpdateRequest | brödtext | -Nyttolast som används för att uppdatera en befintlig tjänst. | Ja |  [serviceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Sidhuvuden | Schema |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden-URL: en. GET-anrop visas statusen för aktiviteten update-tjänst. | Platsen igen |
| 404 | Tjänsten med angivet ID finns inte. |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Ta bort en tjänst

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| DELETE |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Tar bort en tjänst.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Objekt-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

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
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / nycklar | 

### <a name="description"></a>Beskrivning
Hämtar för tjänsten.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Tjänst-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [AuthKeys](#authkeys)
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Återskapa för tjänsten

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / regenerateKeys | 

### <a name="description"></a>Beskrivning
Återskapar för tjänsten och returnerar dem.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Tjänst-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| regenerateKeyRequest | brödtext | -Nyttolast som används för att uppdatera en befintlig tjänst. | Ja | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [AuthKeys](#authkeys)
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Fråga efter listan över distributioner på ett konto

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / distributioner | 

### <a name="description"></a>Beskrivning
Frågar i listan över distributioner på ett konto. Du kan filtrera resultatlistan av tjänst-ID som returneras av distributioner som har skapats för tjänsten. Om inget filter skickas visar frågan alla distributioner i kontot.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |
| serviceId | DocumentDB | Tjänst-ID. | Nej | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [DeploymentList](#deploymentlist) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Hämta distributionsinformation om

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /deployments/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar för distributionen med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Distributions-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Distribution](#deployment) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Hämta åtgärdsinformation om

### <a name="request"></a>Förfrågan
| Metod | Förfrågans URI |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /operations/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar Åtgärdsstatus asynkrona efter åtgärden-ID.

### <a name="parameters"></a>Parametrar
| Namn | Belägen i | Beskrivning | Krävs | Schema
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | sökväg | Azure prenumerations-ID. | Ja | sträng |
| resourceGroupName | sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | sträng |
| Kontonamn | sökväg | Namnet på kontot för hantering av modellen. | Ja | sträng |
| id | sökväg | Åtgärds-ID. | Ja | sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schema |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [OperationStatus](#asyncoperationstatus) |
| standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definitioner

<a name="asset"></a>
### <a name="asset"></a>Tillgång
Tillgångsinformation-objekt som behövs under skapande av Docker-bild.


|Namn|Beskrivning|Schema|
|---|---|---|
|**ID**  <br>*Valfria*|Tillgångsinformation-ID.|sträng|
|**mimeType**  <br>*Valfria*|MIME-typ av modellinnehåll. Mer information om MIME-typ, finns det [lista över IANA medietyper](https://www.iana.org/assignments/media-types/media-types.xhtml).|sträng|
|**Packa upp**  <br>*Valfria*|Anger när vi behöver packa upp innehållet när Docker avbildningen skapas.|boolesk|
|**URL: en**  <br>*Valfria*|Tillgångsinformation-URL: en.|sträng|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Det asynkrona åtgärden tillståndet.

*Typen*: uppräkning (NotStarted, körs, avbrott, lyckades, misslyckades)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Åtgärdsstatus för.


|Namn|Beskrivning|Schema|
|---|---|---|
|**createdTime**  <br>*Valfria*  <br>*Skrivskyddad*|Asynkrona åtgärden Skapandetid (UTC).|sträng (tid)|
|**endTime**  <br>*Valfria*  <br>*Skrivskyddad*|Asynkrona åtgärden slutfördes (UTC).|sträng (tid)|
|**Fel**  <br>*Valfria*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Valfria*|Asynkrona åtgärds-ID.|sträng|
|**Åtgärdstyp**  <br>*Valfria*|Asynkrona åtgärdstyp.|Uppräkning (bild, Service)|
|**resourceLocation**  <br>*Valfria*|Resursen skapas eller uppdateras av den asynkron åtgärden.|sträng|
|**Tillstånd**  <br>*Valfria*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Autentiseringsnycklar för en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**primaryKey**  <br>*Valfria*|Primärnyckel.|sträng|
|**sekundär nyckel**  <br>*Valfria*|Sekundärnyckeln.|sträng|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Inställningar för autoscaler.


|Namn|Beskrivning|Schema|
|---|---|---|
|**autoscaleEnabled**  <br>*Valfria*|Aktivera eller inaktivera autoscaler.|boolesk|
|**maxReplicas**  <br>*Valfria*|Maximalt antal baljor repliker att skala upp till.  <br>**Minimivärdet**: `1`|heltal|
|**minReplicas**  <br>*Valfria*|Minsta antal baljor repliker att skala ned till.  <br>**Minimivärdet**: `0`|heltal|
|**refreshPeriodInSeconds**  <br>*Valfria*|Uppdateringstiden för autoskalning utlösare.  <br>**Minimivärdet**: `1`|heltal|
|**targetUtilization**  <br>*Valfria*|Utnyttjandeprocent som utlöser autoskalning.  <br>**Minimivärdet**: `0`  <br>**Maximivärdet**: `100`|heltal|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Machine Learning beräkningsresursen.


|Namn|Beskrivning|Schema|
|---|---|---|
|**ID**  <br>*Valfria*|Resurs-ID.|sträng|
|**typ**  <br>*Valfria*|Typ av resurs.|Uppräkning (kluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Konfigurationen för reservera resurser för en behållare i klustret.


|Namn|Beskrivning|Schema|
|---|---|---|
|**CPU**  <br>*Valfria*|Anger CPU-reservation. Format för Kubernetes: se [betydelse CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|sträng|
|**Minne**  <br>*Valfria*|Anger minnet reservation. Format för Kubernetes: se [betydelse minne](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|sträng|


<a name="deployment"></a>
### <a name="deployment"></a>Distribution
En instans av en Azure Machine Learning-distribution.


|Namn|Beskrivning|Schema|
|---|---|---|
|**CreatedAt**  <br>*Valfria*  <br>*Skrivskyddad*|Skapa tidpunkten för distribution (UTC).|sträng (tid)|
|**expiredAt**  <br>*Valfria*  <br>*Skrivskyddad*|Distributionen har upphört att gälla tid (UTC).|sträng (tid)|
|**ID**  <br>*Valfria*|Distributions-ID.|sträng|
|**imageId**  <br>*Valfria*|Avbildnings-ID som är associerade med den här distributionen.|sträng|
|**Tjänstnamn**  <br>*Valfria*|Tjänstnamn.|sträng|
|**status**  <br>*Valfria*|Aktuell distributionsstatus.|sträng|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
En matris med objekt.

*Typen*: <[distribution](#deployment)> matris


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Modellen felinformation för Management-tjänsten.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Koden**  <br>*Krävs*|Felkod.|sträng|
|**Meddelande**  <br>*Krävs*|Felmeddelande.|sträng|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Modellen Management service felobjekt.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Koden**  <br>*Krävs*|Felkod.|sträng|
|**Information**  <br>*Valfria*|Matris med felobjekt.|<[ErrorDetail](#errordetail)> matris|
|**Meddelande**  <br>*Krävs*|Felmeddelande.|sträng|
|**statusCode**  <br>*Valfria*|HTTP-statuskod.|heltal|


<a name="image"></a>
### <a name="image"></a>Bild
Azure Machine Learning-bild.


|Namn|Beskrivning|Schema|
|---|---|---|
|**computeResourceId**  <br>*Valfria*|ID för miljön som skapats i Machine Learning beräkningsresurser.|sträng|
|**createdTime**  <br>*Valfria*|Bild skapelsetid (UTC).|sträng (tid)|
|**creationState**  <br>*Valfria*||[AsyncOperationState](#asyncoperationstate)|
|**Beskrivning**  <br>*Valfria*|Bild beskrivningstext.|sträng|
|**Fel**  <br>*Valfria*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*Valfria*|Bild-ID.|sträng|
|**imageBuildLogUri**  <br>*Valfria*|URI för de uppladdade loggarna från image build.|sträng|
|**imageLocation**  <br>*Valfria*|Azure Container registret plats sträng för den skapade avbildningen.|sträng|
|**ImageType**  <br>*Valfria*||[ImageType](#imagetype)|
|**Manifestet**  <br>*Valfria*||[Manifestet](#manifest)|
|**Namn**  <br>*Valfria*|Avbildningens namn.|sträng|
|**Version**  <br>*Valfria*|Bildversion som anges av modellen Management-tjänsten.|heltal|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
En begäran om att skapa en Azure Machine Learning-avbildning.


|Namn|Beskrivning|Schema|
|---|---|---|
|**computeResourceId**  <br>*Krävs*|ID för miljön som skapats i Machine Learning beräkningsresurser.|sträng|
|**Beskrivning**  <br>*Valfria*|Bild beskrivningstext.|sträng|
|**ImageType**  <br>*Krävs*||[ImageType](#imagetype)|
|**Manifest**  <br>*Krävs*|ID för manifestet som avbildningen ska skapas.|sträng|
|**Namn**  <br>*Krävs*|Avbildningens namn.|sträng|


<a name="imagetype"></a>
### <a name="imagetype"></a>Bildtyp
Anger vilken typ av avbildningen.

*Typen*: uppräkning (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
Azure Machine Learning-manifestet.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Tillgångar**  <br>*Krävs*|Lista över tillgångar.|<[Tillgångsinformation](#asset)> matris|
|**createdTime**  <br>*Valfria*  <br>*Skrivskyddad*|Manifestet skapelsetid (UTC).|sträng (tid)|
|**Beskrivning**  <br>*Valfria*|Manifest beskrivningstexten.|sträng|
|**driverProgram**  <br>*Krävs*|Driver-program i manifestet.|sträng|
|**ID**  <br>*Valfria*|Manifest-ID.|sträng|
|**modelIds**  <br>*Valfria*|Lista över modell-ID för de registrerade modellerna. Begäran att misslyckas om någon av ingår inte registrerats.|<string> matris|
|**Modelltypen**  <br>*Valfria*|Anger att modeller redan är registrerade med hanteringstjänsten för modellen.|Uppräkning (registrerad)|
|**Namn**  <br>*Krävs*|Manifestfilens namn.|sträng|
|**TargetRuntime**  <br>*Krävs*||[TargetRuntime](#targetruntime)|
|**Version**  <br>*Valfria*  <br>*Skrivskyddad*|ManifestVersion som tilldelats av modellen Management-tjänsten.|heltal|
|**webserviceType**  <br>*Valfria*|Anger den önskade typen av webbtjänsten som skapas från manifestet.|Uppräkning (realtid)|


<a name="model"></a>
### <a name="model"></a>Modell
En instans av en Azure Machine Learning-modell.


|Namn|Beskrivning|Schema|
|---|---|---|
|**CreatedAt**  <br>*Valfria*  <br>*Skrivskyddad*|Modellen skapelsetid (UTC).|sträng (tid)|
|**Beskrivning**  <br>*Valfria*|Modellen beskrivningstexten.|sträng|
|**ID**  <br>*Valfria*  <br>*Skrivskyddad*|Modell-ID.|sträng|
|**mimeType**  <br>*Krävs*|MIME-typ av modellinnehåll. Mer information om MIME-typ, finns det [lista över IANA medietyper](https://www.iana.org/assignments/media-types/media-types.xhtml).|sträng|
|**Namn**  <br>*Krävs*|Modellnamnet.|sträng|
|**tagg**  <br>*Valfria*|Modellen tagglista.|<string> matris|
|**Packa upp**  <br>*Valfria*|Anger om vi behöver packa upp modellen när Docker avbildningen skapas.|boolesk|
|**URL: en**  <br>*Krävs*|URL till modellen. Vanligtvis placera vi en signatur för delad åtkomst-URL här.|sträng|
|**Version**  <br>*Valfria*  <br>*Skrivskyddad*|Modellversion som tilldelats av modellen Management-tjänsten.|heltal|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Modellen samling informationen.


|Namn|Beskrivning|Schema|
|---|---|---|
|**eventHubEnabled**  <br>*Valfria*|Aktivera en händelsehubb för en tjänst.|boolesk|
|**storageEnabled**  <br>*Valfria*|Aktivera lagring för en tjänst.|boolesk|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
En sidnumrerade lista över avbildningar.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|sträng|
|**värde**  <br>*Valfria*|Matris med modellobjekt.|<[Bild](#image)> matris|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
En sidnumrerade lista över manifest.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|sträng|
|**värde**  <br>*Valfria*|Matris med manifest-objekt.|<[Manifest](#manifest)> matris|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
En sidnumrerade listan över modeller.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|sträng|
|**värde**  <br>*Valfria*|Matris med modellobjekt.|<[Modellen](#model)> matris|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
En sidnumrerade lista över tjänster.


|Namn|Beskrivning|Schema|
|---|---|---|
|**nextLink**  <br>*Valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|sträng|
|**värde**  <br>*Valfria*|Matris med service-objekt.|<[ServiceResponse](#serviceresponse)> matris|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
En begäran att skapa en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*Valfria*|Aktivera application insights för en tjänst.|boolesk|
|**AutoScaler**  <br>*Valfria*||[AutoScaler](#autoscaler)|
|**ComputeResource**  <br>*Krävs*||[ComputeResource](#computeresource)|
|**ContainerResourceReservation**  <br>*Valfria*||[ContainerResourceReservation](#containerresourcereservation)|
|**DataCollection**  <br>*Valfria*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Krävs*|Bild för att skapa tjänsten.|sträng|
|**maxConcurrentRequestsPerContainer**  <br>*Valfria*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**: `1`|heltal|
|**Namn**  <br>*Krävs*|Tjänstnamn.|sträng|
|**numReplicas**  <br>*Valfria*|Antal baljor repliker som körs när som helst. Kan inte anges om Autoscaler är aktiverad.  <br>**Minimivärdet**: `0`|heltal|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
En begäran om att återskapa en nyckel för en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**KeyType**  <br>*Valfria*|Anger vilken nyckel att generera om.|Uppräkning (primära, sekundära)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Detaljerad status för tjänsten.


|Namn|Beskrivning|Schema|
|---|---|---|
|**CreatedAt**  <br>*Valfria*|En tjänst Skapandetid (UTC).|sträng (tid)|
|**ID**  <br>*Valfria*|Tjänst-ID.|sträng|
|**Bild**  <br>*Valfria*||[Avbildning](#image)|
|**Manifestet**  <br>*Valfria*||[Manifestet](#manifest)|
|**modeller**  <br>*Valfria*|Listan över modeller.|<[Modellen](#model)> matris|
|**Namn**  <br>*Valfria*|Tjänstnamn.|sträng|
|**scoringUri**  <br>*Valfria*|URI för poängsättning av tjänsten.|sträng|
|**Tillstånd**  <br>*Valfria*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*Valfria*|Senaste Uppdateringstid (UTC).|sträng (tid)|
|**appInsightsEnabled**  <br>*Valfria*|Aktivera application insights för en tjänst.|boolesk|
|**AutoScaler**  <br>*Valfria*||[AutoScaler](#autoscaler)|
|**ComputeResource**  <br>*Krävs*||[ComputeResource](#computeresource)|
|**ContainerResourceReservation**  <br>*Valfria*||[ContainerResourceReservation](#containerresourcereservation)|
|**DataCollection**  <br>*Valfria*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*Valfria*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**: `1`|heltal|
|**numReplicas**  <br>*Valfria*|Antal baljor repliker som körs när som helst. Kan inte anges om Autoscaler är aktiverad.  <br>**Minimivärdet**: `0`|heltal|
|**Fel**  <br>*Valfria*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>ServiceUpdateRequest
En begäran om att uppdatera en tjänst.


|Namn|Beskrivning|Schema|
|---|---|---|
|**appInsightsEnabled**  <br>*Valfria*|Aktivera application insights för en tjänst.|boolesk|
|**AutoScaler**  <br>*Valfria*||[AutoScaler](#autoscaler)|
|**ContainerResourceReservation**  <br>*Valfria*||[ContainerResourceReservation](#containerresourcereservation)|
|**DataCollection**  <br>*Valfria*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*Valfria*|Bild för att skapa tjänsten.|sträng|
|**maxConcurrentRequestsPerContainer**  <br>*Valfria*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**: `1`|heltal|
|**numReplicas**  <br>*Valfria*|Antal baljor repliker som körs när som helst. Kan inte anges om Autoscaler är aktiverad.  <br>**Minimivärdet**: `0`|heltal|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Typ av mål för körning.


|Namn|Beskrivning|Schema|
|---|---|---|
|**Egenskaper**  <br>*Krävs*||< sträng, sträng > karta|
|**runtimeType**  <br>*Krävs*|Anger körningsmiljön.|Uppräkning (SparkPython, Python)|

