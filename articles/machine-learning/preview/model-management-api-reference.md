---
title: "Skapa en Docker-avbildning för hantering av modellen i Azure Machine Learning | Microsoft Docs"
description: "Den här artikeln beskriver stegen för att skapa en Docker-avbildning för webbtjänsten."
services: machine-learning
author: chhavib
ms.author: chhavib
manager: neerajkh
editor: jasonwhowell
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/20/2017
ms.openlocfilehash: 1939a18fbaf0aed0f62ac9e7641b9901ec4762ea
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="azure-machine-learning-model-management-account-api-reference"></a>Azure Machine Learning modellen Management konto API-referens

Information om hur du konfigurerar distributionsmiljön finns [modellen Management kontoinställning](model-management-configuration.md).

Azure Machine Learning modellen Management konto API implementerar följande åtgärder:

- Modellregistreringen
- Skapa en manifest
- Skapa en docker bild
- Skapa en Web service

Du kan använda den här avbildningen för att skapa en webbtjänst antingen lokalt eller på en fjärransluten Azure Container Service-kluster eller en annan Docker-stödd miljö önskat.

## <a name="prerequisites"></a>Krav
Kontrollera att du har gått igenom följande steg i den [installera och skapa Quickstart](quickstart-installation.md) dokumentet.

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

| Metod | URI-begäran |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modeller 
 |
### <a name="description"></a>Beskrivning
Registrerar en modell.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| Modellen | Brödtext | -Nyttolast som används för att registrera en modell. | Ja | [Modellen](#model) |


### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | OKEJ. Modellregistreringen lyckades. | [Modellen](#model) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-models-in-an-account"></a>Fråga efter listan över modeller i ett konto
### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / modeller 
 |
### <a name="description"></a>Beskrivning
Frågar i listan över modeller på ett konto. Du kan filtrera resultatlistan av taggen och namn. Om inget filter skickas anges i fråga alla modeller i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| namn | DocumentDB | Objektnamnet. | Nej | Sträng |
| Taggen | DocumentDB | Modellen tagg. | Nej | Sträng |
| Antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | Sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedModelList](#paginatedmodellist) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-model-details"></a>Hämta modellinformation om
### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /models/ {id}  
 |

### <a name="description"></a>Beskrivning
Hämtar en modell med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Objekt-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Modellen](#model) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="register-a-manifest-with-the-registered-model-and-all-dependencies"></a>Registrera ett manifest med registrerad modell och alla beroenden

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / visar | 

### <a name="description"></a>Beskrivning
Registrerar ett manifest med registrerad modell och dess beroenden.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| manifestRequest | Brödtext | -Nyttolast som används för att registrera ett manifest. | Ja | [Manifestet](#manifest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Manifestet registreringen har lyckats. | [Manifestet](#manifest) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-manifests-in-an-account"></a>Fråga efter listan över manifesten i ett konto

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / visar | 

### <a name="description"></a>Beskrivning
Söka i manifesten i ett konto. Du kan filtrera resultatlistan av modell-ID och manifest namn. Om inget filter skickas visar frågan i manifesten i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| %{ModelID/ | DocumentDB | Modell-ID. | Nej | Sträng |
| ManifestName | DocumentDB | Manifestfilens namn. | Nej | Sträng |
| Antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | Sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedManifestList](#paginatedmanifestlist) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-manifest-details"></a>Hämta information om manifest

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /manifests/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar manifestet efter-ID.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Objekt-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Manifestet](#manifest) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="create-an-image"></a>Skapa en avbildning

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / bilder | 

### <a name="description"></a>Beskrivning
Skapar en avbildning som en Docker-avbildning i Azure Container registret.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| imageRequest | Brödtext | -Nyttolast som används för att skapa en avbildning. | Ja | [ImageRequest](#imagerequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Rubriker | Schemat |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden-URL: en. GET-anrop visas statusen för aktiviteten skapa avbildningen. | Platsen igen |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-images-in-an-account"></a>Fråga efter listan över bilder i ett konto

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / bilder | 

### <a name="description"></a>Beskrivning
Frågar listan över bilder i ett konto. Du kan filtrera resultatlistan av manifestet ID och namn. Om inget filter skickas visar frågan alla bilder i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| Manifest | DocumentDB | Manifest-ID. | Nej | Sträng |
| ManifestName | DocumentDB | Manifestfilens namn. | Nej | Sträng |
| Antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | Sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedImageList](#paginatedimagelist) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-image-details"></a>Hämta avbildningsinformation

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /images/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar en avbildning med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Bild-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Bild](#image) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |


## <a name="create-a-service"></a>Skapa en tjänst

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} eller-tjänster | 

### <a name="description"></a>Beskrivning
Skapar en tjänst från en avbildning.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| serviceRequest | Brödtext | -Nyttolast som används för att skapa en tjänst. | Ja | [ServiceCreateRequest](#servicecreaterequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Rubriker | Schemat |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden-URL: en. GET-anrop visas statusen för aktiviteten skapa tjänst. | Platsen igen |
| 409 | Det finns redan en tjänst med det angivna namnet. |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="query-the-list-of-services-in-an-account"></a>Fråga efter listan över tjänster i ett konto

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} eller-tjänster | 

### <a name="description"></a>Beskrivning
Frågar listan över tjänster på ett konto. Du kan filtrera resultatet av modellen namn/ID, manifestet namn-ID, avbildnings-ID, namn eller Machine Learning beräkning resurs-ID. Om inget filter skickas visar frågan alla tjänster i kontot. Den returnerade listan sidbrytning är och antalet objekt i varje sida är en valfri parameter.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| Tjänstnamn | DocumentDB | Tjänstnamn. | Nej | Sträng |
| %{ModelID/ | DocumentDB | Modellnamnet. | Nej | Sträng |
| %{ModelName/ | DocumentDB | Modell-ID. | Nej | Sträng |
| Manifest | DocumentDB | Manifest-ID. | Nej | Sträng |
| ManifestName | DocumentDB | Manifestfilens namn. | Nej | Sträng |
| imageId | DocumentDB | Bild-ID. | Nej | Sträng |
| computeResourceId | DocumentDB | Machine Learning beräkning resurs-ID. | Nej | Sträng |
| Antal | DocumentDB | Antal objekt som ska hämtas på en sida. | Nej | Sträng |
| $skipToken | DocumentDB | Fortsättningstoken att hämta nästa sida. | Nej | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [PaginatedServiceList](#paginatedservicelist) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse) |

## <a name="get-service-details"></a>Hämta information om tjänst

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar en tjänst med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Objekt-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [ServiceResponse](#serviceresponse) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="update-a-service"></a>Uppdatera en tjänst

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| PLACERA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Uppdaterar en befintlig tjänst.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Objekt-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| serviceUpdateRequest | Brödtext | -Nyttolast som används för att uppdatera en befintlig tjänst. | Ja |  [ServiceUpdateRequest](#serviceupdaterequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Rubriker | Schemat |
|--------------------|--------------------|--------------------|--------------------|
| 202 | Asynkrona åtgärden-URL: en. GET-anrop visas statusen för aktiviteten update-tjänst. | Platsen igen |
| 404 | Tjänsten med angivet ID finns inte. |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="delete-a-service"></a>Ta bort en tjänst

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| TA BORT |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} | 

### <a name="description"></a>Beskrivning
Tar bort en tjänst.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Objekt-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. |  |
| 204 | Tjänsten med angivet ID finns inte. |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-service-keys"></a>Hämta för tjänsten

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / nycklar | 

### <a name="description"></a>Beskrivning
Hämtar för tjänsten.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Tjänst-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [AuthKeys](#authkeys)
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="regenerate-service-keys"></a>Återskapa för tjänsten

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| POST |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /services/ {id} / nycklar | 

### <a name="description"></a>Beskrivning
Återskapar för tjänsten och returnerar dem.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Tjänst-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| regenerateKeyRequest | Brödtext | -Nyttolast som används för att uppdatera en befintlig tjänst. | Ja | [ServiceRegenerateKeyRequest](#serviceregeneratekeyrequest) |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [AuthKeys](#authkeys)
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="query-the-list-of-deployments-in-an-account"></a>Fråga efter listan över distributioner på ett konto

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} / distributioner | 

### <a name="description"></a>Beskrivning
Frågar i listan över distributioner på ett konto. Du kan filtrera resultatlistan av tjänst-ID som returneras av distributioner som har skapats för tjänsten. Om inget filter skickas visar frågan alla distributioner i kontot.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |
| serviceId | DocumentDB | Tjänst-ID. | Nej | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [DeploymentList](#deploymentlist) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-deployment-details"></a>Hämta distributionsinformation om

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /deployments/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar för distributionen med ID: t.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Distributions-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [Distribution](#deployment) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)

## <a name="get-operation-details"></a>Hämta åtgärdsinformation om

### <a name="request"></a>Förfrågan
| Metod | URI-begäran |
|------------|------------|
| HÄMTA |  /API /subscriptions/ {subscriptionId} /resourceGroups/ {resourceGroupName} /accounts/ {accountName} /operations/ {id} | 

### <a name="description"></a>Beskrivning
Hämtar Åtgärdsstatus asynkrona efter åtgärden-ID.

### <a name="parameters"></a>Parametrar
| Namn | Finns i | Beskrivning | Krävs | Schemat
|--------------------|--------------------|--------------------|--------------------|--------------------|
| subscriptionId | Sökväg | Azure prenumerations-ID. | Ja | Sträng |
| resourceGroupName | Sökväg | Namnet på resursgruppen där modellen hanteringskontot finns. | Ja | Sträng |
| Kontonamn | Sökväg | Namnet på kontot för hantering av modellen. | Ja | Sträng |
| id | Sökväg | Åtgärds-ID. | Ja | Sträng |
| API-version | DocumentDB | Version av resursprovidern Microsoft.Machine.Learning API för att använda. | Ja | Sträng |
| Auktorisering | sidhuvud | Autentiseringstoken. Det bör vara ungefär ”ägar XXXXXX”. | Ja | Sträng |

### <a name="responses"></a>Svar
| Kod | Beskrivning | Schemat |
|--------------------|--------------------|--------------------|
| 200 | Lyckades. | [OperationStatus](#asyncoperationstatus) |
| Standard | Felsvar som beskriver varför detta misslyckades. | [ErrorResponse](#errorresponse)



<a name="definitions"></a>
## <a name="definitions"></a>Definitioner

<a name="asset"></a>
### <a name="asset"></a>Tillgångsinformation
Tillgångsinformation-objekt som behövs under skapande av Docker-bild.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**ID**  <br>*valfria*|Tillgångsinformation-ID.|Sträng|
|**mimeType**  <br>*valfria*|MIME-typ av modellinnehåll. Mer information om MIME-typ, finns det [lista över IANA medietyper](https://www.iana.org/assignments/media-types/media-types.xhtml).|Sträng|
|**Packa upp**  <br>*valfria*|Anger när vi behöver packa upp innehållet när Docker avbildningen skapas.|Booleskt värde|
|**URL: en**  <br>*valfria*|Tillgångsinformation-URL: en.|Sträng|


<a name="asyncoperationstate"></a>
### <a name="asyncoperationstate"></a>AsyncOperationState
Det asynkrona åtgärden tillståndet.

*Typen*: uppräkning (NotStarted, körs, avbrott, lyckades, misslyckades)


<a name="asyncoperationstatus"></a>
### <a name="asyncoperationstatus"></a>AsyncOperationStatus
Åtgärdsstatus för.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**createdTime**  <br>*valfria*  <br>*skrivskyddad*|Asynkrona åtgärden Skapandetid (UTC).|sträng (tid)|
|**Sluttid**  <br>*valfria*  <br>*skrivskyddad*|Asynkrona åtgärden slutfördes (UTC).|sträng (tid)|
|**fel**  <br>*valfria*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*valfria*|Asynkrona åtgärds-ID.|Sträng|
|**Åtgärdstyp**  <br>*valfria*|Asynkrona åtgärdstyp.|Uppräkning (bild, Service)|
|**resourceLocation**  <br>*valfria*|Resursen skapas eller uppdateras av den asynkron åtgärden.|Sträng|
|**tillstånd**  <br>*valfria*||[AsyncOperationState](#asyncoperationstate)|


<a name="authkeys"></a>
### <a name="authkeys"></a>AuthKeys
Autentiseringsnycklar för en tjänst.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**primaryKey**  <br>*valfria*|Primärnyckel.|Sträng|
|**sekundär nyckel**  <br>*valfria*|Sekundärnyckeln.|Sträng|


<a name="autoscaler"></a>
### <a name="autoscaler"></a>AutoScaler
Inställningar för autoscaler.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**autoscaleEnabled**  <br>*valfria*|Aktivera eller inaktivera autoscaler.|Booleskt värde|
|**maxReplicas**  <br>*valfria*|Maximalt antal baljor repliker att skala upp till.  <br>**Minimivärdet**:`1`|heltal|
|**minReplicas**  <br>*valfria*|Minsta antal baljor repliker att skala ned till.  <br>**Minimivärdet**:`0`|heltal|
|**refreshPeriodInSeconds**  <br>*valfria*|Uppdateringstiden för autoskalning utlösare.  <br>**Minimivärdet**:`1`|heltal|
|**targetUtilization**  <br>*valfria*|Utnyttjandeprocent som utlöser autoskalning.  <br>**Minimivärdet**:`0`  <br>**Maximivärdet**:`100`|heltal|


<a name="computeresource"></a>
### <a name="computeresource"></a>ComputeResource
Machine Learning beräkningsresursen.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**ID**  <br>*valfria*|Resurs-ID.|Sträng|
|**typ**  <br>*valfria*|Typ av resurs.|Uppräkning (kluster)|


<a name="containerresourcereservation"></a>
### <a name="containerresourcereservation"></a>ContainerResourceReservation
Konfigurationen för reservera resurser för en behållare i klustret.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**CPU**  <br>*valfria*|Anger CPU-reservation. Format för Kubernetes: se [betydelse CPU](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-cpu).|Sträng|
|**minne**  <br>*valfria*|Anger minnet reservation. Format för Kubernetes: se [betydelse minne](https://kubernetes.io/docs/concepts/configuration/manage-compute-resources-container/#meaning-of-memory).|Sträng|


<a name="deployment"></a>
### <a name="deployment"></a>Distribution
En instans av en Azure Machine Learning-distribution.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**createdAt**  <br>*valfria*  <br>*skrivskyddad*|Skapa tidpunkten för distribution (UTC).|sträng (tid)|
|**expiredAt**  <br>*valfria*  <br>*skrivskyddad*|Distributionen har upphört att gälla tid (UTC).|sträng (tid)|
|**ID**  <br>*valfria*|Distributions-ID.|Sträng|
|**imageId**  <br>*valfria*|Avbildnings-ID som är associerade med den här distributionen.|Sträng|
|**Tjänstnamn**  <br>*valfria*|Tjänstnamn.|Sträng|
|**status**  <br>*valfria*|Aktuell distributionsstatus.|Sträng|


<a name="deploymentlist"></a>
### <a name="deploymentlist"></a>DeploymentList
En matris med objekt.

*Typen*: <[distribution](#deployment)> matris


<a name="errordetail"></a>
### <a name="errordetail"></a>ErrorDetail
Modellen felinformation för Management-tjänsten.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**koden**  <br>*krävs*|Felkod.|Sträng|
|**meddelande**  <br>*krävs*|Felmeddelande.|Sträng|


<a name="errorresponse"></a>
### <a name="errorresponse"></a>ErrorResponse
Modellen Management service felobjekt.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**koden**  <br>*krävs*|Felkod.|Sträng|
|**information**  <br>*valfria*|Matris med felobjekt.|<[ErrorDetail](#errordetail)> matris|
|**meddelande**  <br>*krävs*|Felmeddelande.|Sträng|
|**statusCode**  <br>*valfria*|HTTP-statuskod.|heltal|


<a name="image"></a>
### <a name="image"></a>Bild
Azure Machine Learning-bild.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**computeResourceId**  <br>*valfria*|ID för miljön som skapats i Machine Learning beräkningsresurser.|Sträng|
|**createdTime**  <br>*valfria*|Bild skapelsetid (UTC).|sträng (tid)|
|**creationState**  <br>*valfria*||[AsyncOperationState](#asyncoperationstate)|
|**Beskrivning**  <br>*valfria*|Bild beskrivningstext.|Sträng|
|**fel**  <br>*valfria*||[ErrorResponse](#errorresponse)|
|**ID**  <br>*valfria*|Bild-ID.|Sträng|
|**imageBuildLogUri**  <br>*valfria*|URI för de uppladdade loggarna från image build.|Sträng|
|**imageLocation**  <br>*valfria*|Azure Container registret plats sträng för den skapade avbildningen.|Sträng|
|**imageType**  <br>*valfria*||[ImageType](#imagetype)|
|**Manifestet**  <br>*valfria*||[Manifestet](#manifest)|
|**Namn**  <br>*valfria*|Avbildningens namn.|Sträng|
|**version**  <br>*valfria*|Bildversion som anges av modellen Management-tjänsten.|heltal|


<a name="imagerequest"></a>
### <a name="imagerequest"></a>imageRequest
En begäran om att skapa en Azure Machine Learning-avbildning.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**computeResourceId**  <br>*krävs*|ID för miljön som skapats i Machine Learning beräkningsresurser.|Sträng|
|**Beskrivning**  <br>*valfria*|Bild beskrivningstext.|Sträng|
|**imageType**  <br>*krävs*||[ImageType](#imagetype)|
|**Manifest**  <br>*krävs*|ID för manifestet som avbildningen ska skapas.|Sträng|
|**Namn**  <br>*krävs*|Avbildningens namn.|Sträng|


<a name="imagetype"></a>
### <a name="imagetype"></a>Bildtyp
Anger vilken typ av avbildningen.

*Typen*: uppräkning (Docker)


<a name="manifest"></a>
### <a name="manifest"></a>Manifest
Azure Machine Learning-manifestet.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**tillgångar**  <br>*krävs*|Lista över tillgångar.|<[Tillgångsinformation](#asset)> matris|
|**createdTime**  <br>*valfria*  <br>*skrivskyddad*|Manifestet skapelsetid (UTC).|sträng (tid)|
|**Beskrivning**  <br>*valfria*|Manifest beskrivningstexten.|Sträng|
|**driverProgram**  <br>*krävs*|Driver-program i manifestet.|Sträng|
|**ID**  <br>*valfria*|Manifest-ID.|Sträng|
|**modelIds**  <br>*valfria*|Lista över modell-ID för de registrerade modellerna. Begäran att misslyckas om någon av ingår inte registrerats.|<string>matris|
|**Modelltypen**  <br>*valfria*|Anger att modeller redan är registrerade med hanteringstjänsten för modellen.|Uppräkning (registrerad)|
|**Namn**  <br>*krävs*|Manifestfilens namn.|Sträng|
|**targetRuntime**  <br>*krävs*||[TargetRuntime](#targetruntime)|
|**version**  <br>*valfria*  <br>*skrivskyddad*|ManifestVersion som tilldelats av modellen Management-tjänsten.|heltal|
|**webserviceType**  <br>*valfria*|Anger den önskade typen av webbtjänsten som skapas från manifestet.|Uppräkning (realtid)|


<a name="model"></a>
### <a name="model"></a>Modellen
En instans av en Azure Machine Learning-modell.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**createdAt**  <br>*valfria*  <br>*skrivskyddad*|Modellen skapelsetid (UTC).|sträng (tid)|
|**Beskrivning**  <br>*valfria*|Modellen beskrivningstexten.|Sträng|
|**ID**  <br>*valfria*  <br>*skrivskyddad*|Modell-ID.|Sträng|
|**mimeType**  <br>*krävs*|MIME-typ av modellinnehåll. Mer information om MIME-typ, finns det [lista över IANA medietyper](https://www.iana.org/assignments/media-types/media-types.xhtml).|Sträng|
|**Namn**  <br>*krävs*|Modellnamnet.|Sträng|
|**tagg**  <br>*valfria*|Modellen tagglista.|<string>matris|
|**Packa upp**  <br>*valfria*|Anger om vi behöver packa upp modellen när Docker avbildningen skapas.|Booleskt värde|
|**URL: en**  <br>*krävs*|URL till modellen. Vanligtvis placera vi en signatur för delad åtkomst-URL här.|Sträng|
|**version**  <br>*valfria*  <br>*skrivskyddad*|Modellversion som tilldelats av modellen Management-tjänsten.|heltal|


<a name="modeldatacollection"></a>
### <a name="modeldatacollection"></a>ModelDataCollection
Modellen samling informationen.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**eventHubEnabled**  <br>*valfria*|Aktivera en händelsehubb för en tjänst.|Booleskt värde|
|**storageEnabled**  <br>*valfria*|Aktivera lagring för en tjänst.|Booleskt värde|


<a name="paginatedimagelist"></a>
### <a name="paginatedimagelist"></a>PaginatedImageList
En sidnumrerade lista över avbildningar.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**nextLink**  <br>*valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|Sträng|
|**värdet**  <br>*valfria*|Matris med modellobjekt.|<[Bild](#image)> matris|


<a name="paginatedmanifestlist"></a>
### <a name="paginatedmanifestlist"></a>PaginatedManifestList
En sidnumrerade lista över manifest.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**nextLink**  <br>*valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|Sträng|
|**värdet**  <br>*valfria*|Matris med manifest-objekt.|<[Manifest](#manifest)> matris|


<a name="paginatedmodellist"></a>
### <a name="paginatedmodellist"></a>PaginatedModelList
En sidnumrerade listan över modeller.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**nextLink**  <br>*valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|Sträng|
|**värdet**  <br>*valfria*|Matris med modellobjekt.|<[Modellen](#model)> matris|


<a name="paginatedservicelist"></a>
### <a name="paginatedservicelist"></a>PaginatedServiceList
En sidnumrerade lista över tjänster.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**nextLink**  <br>*valfria*|Fortsättning länk (absolut URI) till nästa sida i resultat i listan.|Sträng|
|**värdet**  <br>*valfria*|Matris med service-objekt.|<[ServiceResponse](#serviceresponse)> matris|


<a name="servicecreaterequest"></a>
### <a name="servicecreaterequest"></a>ServiceCreateRequest
En begäran att skapa en tjänst.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**appInsightsEnabled**  <br>*valfria*|Aktivera application insights för en tjänst.|Booleskt värde|
|**autoScaler**  <br>*valfria*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*krävs*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*valfria*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*valfria*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*krävs*|Bild för att skapa tjänsten.|Sträng|
|**maxConcurrentRequestsPerContainer**  <br>*valfria*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**:`1`|heltal|
|**Namn**  <br>*krävs*|Tjänstnamn.|Sträng|
|**numReplicas**  <br>*valfria*|Antal baljor repliker som körs när som helst. Kan inte anges om Autoscaler är aktiverad.  <br>**Minimivärdet**:`0`|heltal|


<a name="serviceregeneratekeyrequest"></a>
### <a name="serviceregeneratekeyrequest"></a>ServiceRegenerateKeyRequest
En begäran om att återskapa en nyckel för en tjänst.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**keyType**  <br>*valfria*|Anger vilken nyckel att generera om.|Uppräkning (primära, sekundära)|


<a name="serviceresponse"></a>
### <a name="serviceresponse"></a>ServiceResponse
Detaljerad status för tjänsten.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**createdAt**  <br>*valfria*|En tjänst Skapandetid (UTC).|sträng (tid)|
|**ID**  <br>*valfria*|Tjänst-ID.|Sträng|
|**bild**  <br>*valfria*||[Bild](#image)|
|**Manifestet**  <br>*valfria*||[Manifestet](#manifest)|
|**modeller**  <br>*valfria*|Listan över modeller.|<[Modellen](#model)> matris|
|**Namn**  <br>*valfria*|Tjänstnamn.|Sträng|
|**scoringUri**  <br>*valfria*|URI för poängsättning av tjänsten.|Sträng|
|**tillstånd**  <br>*valfria*||[AsyncOperationState](#asyncoperationstate)|
|**updatedAt**  <br>*valfria*|Senaste Uppdateringstid (UTC).|sträng (tid)|
|**appInsightsEnabled**  <br>*valfria*|Aktivera application insights för en tjänst.|Booleskt värde|
|**autoScaler**  <br>*valfria*||[AutoScaler](#autoscaler)|
|**computeResource**  <br>*krävs*||[ComputeResource](#computeresource)|
|**containerResourceReservation**  <br>*valfria*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*valfria*||[ModelDataCollection](#modeldatacollection)|
|**maxConcurrentRequestsPerContainer**  <br>*valfria*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**:`1`|heltal|
|**numReplicas**  <br>*valfria*|Antal baljor repliker som körs när som helst. Kan inte anges om Autoscaler är aktiverad.  <br>**Minimivärdet**:`0`|heltal|
|**fel**  <br>*valfria*||[ErrorResponse](#errorresponse)|


<a name="serviceupdaterequest"></a>
### <a name="serviceupdaterequest"></a>serviceUpdateRequest
En begäran om att uppdatera en tjänst.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**appInsightsEnabled**  <br>*valfria*|Aktivera application insights för en tjänst.|Booleskt värde|
|**autoScaler**  <br>*valfria*||[AutoScaler](#autoscaler)|
|**containerResourceReservation**  <br>*valfria*||[ContainerResourceReservation](#containerresourcereservation)|
|**dataCollection**  <br>*valfria*||[ModelDataCollection](#modeldatacollection)|
|**imageId**  <br>*valfria*|Bild för att skapa tjänsten.|Sträng|
|**maxConcurrentRequestsPerContainer**  <br>*valfria*|Maximalt antal samtidiga begäranden.  <br>**Minimivärdet**:`1`|heltal|
|**numReplicas**  <br>*valfria*|Antal baljor repliker som körs när som helst. Kan inte anges om Autoscaler är aktiverad.  <br>**Minimivärdet**:`0`|heltal|


<a name="targetruntime"></a>
### <a name="targetruntime"></a>TargetRuntime
Typ av mål för körning.


|Namn|Beskrivning|Schemat|
|---|---|---|
|**Egenskaper**  <br>*krävs*||< sträng, sträng > karta|
|**runtimeType**  <br>*krävs*|Anger körningsmiljön.|Uppräkning (SparkPython, Python)|

