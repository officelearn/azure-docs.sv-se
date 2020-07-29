---
title: Hämta token för tjänståtkomst
description: 'Beskriver hur du skapar token för att få åtkomst till ARR REST-API: er'
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: fd510f90887353d7486908ee076d5308db72c59d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "81687072"
---
# <a name="get-service-access-tokens"></a>Hämta token för tjänståtkomst

Åtkomst till ARR REST-API: er beviljas bara för behöriga användare. För att bevisa din auktorisering måste du skicka en *åtkomsttoken* tillsammans med rest-begäranden. Dessa token utfärdas av *Secure token service* (STS) i Exchange för en konto nyckel. Token har en **livs längd på 24 timmar** och kan därför utfärdas till användare utan att ge dem fullständig åtkomst till tjänsten.

Den här artikeln beskriver hur du skapar en sådan åtkomsttoken.

## <a name="prerequisites"></a>Krav

[Skapa ett arr-konto](create-an-account.md)om du inte har något ännu.

## <a name="token-service-rest-api"></a>Token service-REST API

För att skapa åtkomsttoken ger *Secure token service* en enda REST API. URL: en för ARR STS-tjänsten är https: \/ /STS.mixedreality.Azure.com.

### <a name="get-token-request"></a>Hämta token-begäran

| URI | Metod |
|-----------|:-----------|
| /Accounts/**accountId**/token | HÄMTA |

| Sidhuvud | Värde |
|--------|:------|
| Auktorisering | "Bearer **accountId**:**accountKey**" |

Ersätt *accountId* och *accountKey* med dina respektive data.

### <a name="get-token-response"></a>Hämta token-svar

| Statuskod | JSON-nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | AccessToken: sträng | Klart |

| Sidhuvud | Syfte |
|--------|:------|
| MS-CV | Det här värdet kan användas för att spåra anropet inom tjänsten |

## <a name="getting-a-token-using-powershell"></a>Hämta en token med PowerShell

PowerShell-koden nedan visar hur du skickar nödvändig REST-begäran till STS. Den skriver sedan ut token till PowerShell-prompten.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skriptet skriver bara ut token till utdata, där du kan kopiera & klistra in den. För ett verkligt projekt bör du automatisera processen.

## <a name="next-steps"></a>Nästa steg

* [PowerShell-exempelskript](../samples/powershell-example-scripts.md)
* [API: er för Azure-frontend](../how-tos/frontend-apis.md)
* [Sessions hanterings REST API](../how-tos/session-rest-api.md)
