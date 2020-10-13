---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91711406"
---
| Egenskap | Beskrivning |
|:--- |:---|
|**identitet/typ** | Den typ av autentisering som användes för att utföra begäran. Till exempel: `OAuth` , `SAS Key` , `Account Key` eller `Anonymous` |
|**identitets-tokenHash**|Det här fältet är reserverat för internt bruk. |
|**auktorisering/åtgärd** | Den åtgärd som har tilldelats begäran. |
|**auktoriserings-roleAssignmentId** | Roll tilldelnings-ID: t. Exempel: `4e2521b7-13be-4363-aeda-111111111111`.|
|**auktoriserings-roleDefinitionId** | Roll Definitions-ID: t. Exempel: `ba92f5b4-2d11-453d-a403-111111111111"`.|
|**huvud namn/ID** | Säkerhets objektets ID. Exempel: `a4711f3a-254f-4cfb-8a2d-111111111111`.|
|**objekt/typ** | Typ av säkerhets objekt. Exempel: `ServicePrincipal`. |
|**beställare/appID** | Det Open Authorization (OAuth) program-ID som används som beställare. <br> Exempel: `d3f7d5fe-e64a-4e4e-871d-333333333333`.|
|**beställare/mål grupp** | OAuth-mål för begäran. Exempel: `https://storage.azure.com`. |
|**beställare/objectId** | OAuth-objekt-ID för beställaren. I händelse av Kerberos-autentisering representerar objekt identifieraren för Kerberos-autentiserad användare. Exempel: `0e0bf547-55e5-465c-91b7-2873712b249c`. |
|**beställare/tenantId** | Identitet för OAuth-klient-ID. Exempel: `72f988bf-86f1-41af-91ab-222222222222`.|
|**beställare/tokenIssuer** | Utfärdaren av OAuth-token. Exempel: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`.|
|**beställare/UPN** | Användarens huvud namn (UPN) för begär Ande. Exempel: `someone@contoso.com`. |
|**beställare/användar namn** | Det här fältet är reserverat för internt bruk.|