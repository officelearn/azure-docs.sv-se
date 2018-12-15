---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: efa367157a8fd896cdc9680bf2ab6ba6a9e3dbb0
ms.sourcegitcommit: b254db346732b64678419db428fd9eb200f3c3c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2018
ms.locfileid: "53430049"
---
Transaktioner-nyckeln (maximalt antal transaktioner som är tillåtna om 10 sekunder valv per region<sup>1</sup>):

|Nyckeltyp|HSM-key<br>SKAPA nyckel|HSM-key<br>Alla andra transaktioner|Programvara-nyckel<br>SKAPA nyckel|Programvara-nyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2048-bitars|5|1000|10|2000|
|RSA 3072-bitars|5|250|10|500|
|RSA 4096-bitars|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P 521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

> [!NOTE]
> Om du tittar på tabellen nedan, ser du att för programvara-baserade nycklar tillåter vi 2000 transaktioner per 10 sekunder och för HSM-skyddade nycklar som tillåter vi 1000 transaktioner per 10 sekunder. Förhållandet mellan backas upp av programvara transaktioner för 3072 nycklar till 2048 nycklar är 500/2000 eller 0.4. Det innebär att de når sin gräns på högst om en kund gör 500 3072 viktiga transaktioner om 10 sekunder, och att du tar inte kan göra andra viktiga åtgärder. 
   
|Nyckeltyp  | Programnyckel |HSM-key  |
|---------|---------|---------|
|RSA 2048-bitars     |    2000     |   1000    |
|RSA 3072-bitars     |     500    |    250     |
|RSA 4096-bitars     |    125     |    250     |
|ECC P-256     |    2000     |  1000     |
|ECC P-384     |    2000     |  1000     |
|ECC P 521     |    2000     |  1000     |
|ECC SECP256K1     |    2000     |  1000     |


Hemligheter, hanterade Lagringskontonycklar och vault-transaktioner:
| Transaktioner-typ | Maximalt antal transaktioner som är tillåtna om 10 sekunder valv per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2000 |
|

Se [Azure Key Vault-begränsning vägledning](../articles/key-vault/key-vault-ovw-throttling.md) information om hur du hanterar begränsning när gränserna överskrids.

<sup>1</sup> det finns en gräns för alla transaktionstyper hela prenumerationen som är 5 gånger per nyckelvalv gränsen. Till exempel HSM - andra transaktioner per prenumeration är begränsade till 5000 transaktioner om 10 sekunder per prenumeration.
