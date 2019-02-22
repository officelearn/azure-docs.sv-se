---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: a8979edf94c0dd0271293feb28c18530faeba09c
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2019
ms.locfileid: "56660472"
---
## <a name="key-transactions-max-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Transaktioner-nyckeln (maximalt antal transaktioner som är tillåtna om 10 sekunder valv per region<sup>1</sup>):

|Nyckeltyp|HSM-key<br>SKAPA nyckel|HSM-key<br>Alla andra transaktioner|Programvara-nyckel<br>SKAPA nyckel|Programvara-nyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2048-bitars|5|1000|10|2000|
|RSA 3072-bitars|5|250|10|500|
|RSA 4096-bitars|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P-521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|

> [!NOTE]
> I tabellen ovan visas att för RSA 2048-bitars-programvarunycklar vi tillåter 2000 GET transaktioner per 10: e sekund och att för RSA 2048-bitars HSM-nycklar, vi tillåter 1000 GET transaktioner per 10 sekunder.
>
> Observera att tröskelvärden för begränsningar viktas och tvingande finns på deras summa. Till exempel i tabellen ovan, vi kan se att när du utför GET-åtgärder på HSM-RSA-nycklar, det är 8 gånger dyrare att använda 4 096-bitars nycklar jämfört med 2048-bitars nycklar (sedan 1000/125 = 8). Därför i ett givet intervall om 10 sekunder, en AKV-klient kan göra exakt ett av följande innan du påträffar en `429` begränsning HTTP-statuskod:
> - 2000 RSA 2048-bitars programvarunyckel GET-transaktioner, **eller**
> - 1000 RSA 2048-bitars HSM-nyckel GET transaktioner **eller**
> - 125 RSA 4096-bitars HSM-nyckel GET-transaktioner, **eller**
> - 124 RSA 4096-bitars HSM-nyckel GET-transaktioner och 8 RSA 2048-bitars HSM-nyckel GET-transaktioner.

## <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Hemligheter, hanterade Lagringskontonycklar och vault-transaktioner:
| Transaktioner-typ | Maximalt antal transaktioner som är tillåtna om 10 sekunder valv per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2000 |

Se [Azure Key Vault-begränsning vägledning](../articles/key-vault/key-vault-ovw-throttling.md) information om hur du hanterar begränsning när gränserna överskrids.

<sup>1</sup> det finns en gräns för alla transaktionstyper hela prenumerationen som är 5 gånger per nyckelvalv gränsen. Till exempel HSM - andra transaktioner per prenumeration är begränsade till 5000 transaktioner om 10 sekunder per prenumeration.
