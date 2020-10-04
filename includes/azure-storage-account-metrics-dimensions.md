---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: 6bda702a90d1204de6f2b80ced9a4704f1e8426d
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711429"
---
| Dimensions namn | Description |
| ------------------- | ----------------- |
| **Typ av typ** | Transaktion från primärt eller sekundärt kluster. De tillgängliga värdena är **primär** och **sekundär**. Den gäller för läsning av Geo-redundant lagring med Läs behörighet (RA-GRS) vid läsning av objekt från en sekundär klient. |
| **ResponseType** | Typ av transaktions svar. Tillgängliga värden är: <br/><br/> <li>**ServerOtherError**: Alla andra fel på serversidan förutom de beskrivna </li> <li>**ServerBusyError**: Autentiseringsbegäran som returnerat statuskoden HTTP 503. </li> <li>**ServerTimeoutError**: Autentiseringsbegäran som tagit för lång tid och returnerat statuskoden HTTP 500. Tidsgränsen överskreds på grund av ett serverfel. </li> <li>**AuthorizationError**: Autentiseringsbegäran som misslyckats på grund av obehörig åtkomst till data eller ett autentiseringsfel. </li> <li>**NetworkError**: Autentiseringsbegäran som misslyckats på grund av nätverksfel. Inträffar vanligen när klienten stänger en anslutning för tidigt innan tidsgränsen. </li><li>**ClientAccountBandwidthThrottlingError**: begäran begränsas på bandbredden för att överskrida [skalbarhets gränserna för lagrings kontot](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).</li><li>**ClientAccountRequestThrottlingError**: begäran begränsas enligt begär ande frekvens för överskridning av [skalbarhets gränser för lagrings kontot](https://docs.microsoft.com/azure/storage/common/scalability-targets-standard-account?toc=/azure/storage/blobs/toc.json).<li>**ClientThrottlingError**: andra begränsnings fel på klient sidan. ClientAccountBandwidthThrottlingError och ClientAccountRequestThrottlingError är undantagna.</li> <li>**ClientTimeoutError**: Autentiseringsbegäran som tagit för lång tid och returnerat statuskoden HTTP 500. Om klientens tidsgränser för nätverket eller förfrågningar är inställda på lägre värden än vad lagringstjänsten förväntar sig är det en förväntad timeout. Annars rapporteras den som ett ServerTimeoutError. </li> <li>**ClientOtherError**: Alla andra fel på klientsidan förutom de beskrivna. </li> <li>**Success**: Förfrågan utfördes</li> <li> **SuccessWithThrottling**: lyckad begäran när en SMB-klient får en begränsning i de första försöken (erna) men lyckas efter återförsök.</li> |
| **ApiName** | Åtgärdens namn. 
| **Autentisering** | Autentiseringstyp som används i transaktioner. Tillgängliga värden är: <br/> <li>**AccountKey**: transaktionen autentiseras med lagrings konto nyckeln.</li> <li>**SAS**: transaktionen autentiseras med signaturer för delad åtkomst.</li> <li>**OAuth**: transaktionen autentiseras med OAuth-åtkomsttoken.</li> <li>**Anonym**: transaktionen begärs anonymt. Den omfattar inte preflight-begäranden.</li> <li>**AnonymousPreflight**: transaktionen är en preflight-begäran.</li> |