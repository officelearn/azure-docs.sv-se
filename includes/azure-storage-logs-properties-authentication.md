---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711406"
---
| Egenskap | Beskrivning |
|:--- |:---|
|**identitet/typ** | Den typ av autentisering som användes för att utföra begäran. Till exempel: `OAuth` , `SAS Key` , `Account Key` eller `Anonymous` |
|**identitets-tokenHash**|Det här fältet är reserverat för internt bruk. |
|**auktorisering/åtgärd** | Den åtgärd som har tilldelats begäran. |
|**auktoriserings-roleAssignmentId** | Roll tilldelnings-ID: t. Till exempel: `4e2521b7-13be-4363-aeda-111111111111`.|
|**auktoriserings-roleDefinitionId** | Roll Definitions-ID: t. Till exempel: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**huvud namn/ID** | Säkerhets objektets ID. Till exempel: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**objekt/typ** | Typ av säkerhets objekt. Till exempel: `ServicePrincipal`. |
|**beställare/appID** | Det Open Authorization (OAuth) program-ID som används som beställare. <br> Till exempel: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**beställare/mål grupp** | OAuth-mål för begäran. Till exempel: `https://storage.azure.com`. |
|**beställare/objectId** | OAuth-objekt-ID för beställaren. I händelse av Kerberos-autentisering representerar objekt identifieraren för Kerberos-autentiserad användare. Till exempel: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**beställare/tenantId** | Identitet för OAuth-klient-ID. Till exempel: `72f988bf-86f1-41af-91ab-222222222222`.|
|**beställare/tokenIssuer** | Utfärdaren av OAuth-token. Till exempel: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**beställare/UPN** | Användarens huvud namn (UPN) för begär Ande. Till exempel: `someone@contoso.com`. |
|**beställare/användar namn** | Det här fältet är reserverat för internt bruk.|