---
title: Hämta tjänståtkomsttoken
description: Beskriver hur du skapar token för åtkomst till ARR REST API:er
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681199"
---
# <a name="get-service-access-tokens"></a>Hämta tjänståtkomsttoken

Åtkomst till ARR REST API:er beviljas endast för behöriga användare. För att bevisa din auktorisering måste du skicka en *åtkomsttoken* tillsammans med REST-begäranden. Dessa token utfärdas av *Secure Token Service* (STS) i utbyte mot en kontonyckel. Tokens har en **livstid på 24 timmar** och kan därför utfärdas till användare utan att ge dem full tillgång till tjänsten.

I den här artikeln beskrivs hur du skapar en sådan åtkomsttoken.

## <a name="prerequisites"></a>Krav

[Skapa ett ARR-konto](create-an-account.md)om du inte har ett ännu.

## <a name="token-service-rest-api"></a>REST-API för tokentjänst

För att skapa åtkomsttoken tillhandahåller *secure token-tjänsten* ett enda REST API. URL:en för ARR STS-tjänsten är [https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com).

### <a name="get-token-request"></a>Begäran om get token

| URI | Metod |
|-----------|:-----------|
| /accounts/**accountId**/token | HÄMTA |

| Huvud | Värde |
|--------|:------|
| Auktorisering | "Bärare **accountId**:**accountKey**" |

Ersätt *accountId* och *accountKey* med dina respektive data.

### <a name="get-token-response"></a>"Hämta token"-svar

| Statuskod | JSON nyttolast | Kommentarer |
|-----------|:-----------|:-----------|
| 200 | AccessToken: sträng | Lyckades |

| Huvud | Syfte |
|--------|:------|
| MS-CV | Det här värdet kan användas för att spåra anropet inom tjänsten |

## <a name="getting-a-token-using-powershell"></a>Hämta en token med PowerShell

PowerShell-koden nedan visar hur du skickar den nödvändiga REST-begäran till STS. Token skrivs sedan ut till PowerShell-prompten.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skriptet skriver bara ut token till utdata, varifrån du kan kopiera & klistra in den. För ett riktigt projekt bör du automatisera den här processen.

## <a name="next-steps"></a>Nästa steg

* [Exempel på PowerShell-skript](../samples/powershell-example-scripts.md)
* [Azure Frontend API:er](../how-tos/frontend-apis.md)
* [REST-API för sessionshantering](../how-tos/session-rest-api.md)
