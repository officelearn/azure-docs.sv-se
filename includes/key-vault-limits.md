---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: ed0c387f9785336fbf18b3fd3c0cd9a7b09df633
ms.sourcegitcommit: 8d88a025090e5087b9d0ab390b1207977ef4ff7c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52279970"
---
Transaktioner-nyckeln (maximalt antal transaktioner som är tillåtna om 10 sekunder valv per region<sup>1</sup>):

|Nyckeltyp|HSM-nyckel<br>SKAPA nyckel|HSM-key<br>Alla andra transaktioner|Programvara-nyckel<br>SKAPA nyckel|Programvara-nyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2048-bitars|5|1000|10|2000|
|RSA 3072-bitars|5|250|10|500|
|RSA 4096-bitars|5|125|10|250|
|ECC P-256|5|1000|10|2000|
|ECC P-384|5|1000|10|2000|
|ECC P 521|5|1000|10|2000|
|ECC SECP256K1|5|1000|10|2000|
|

Hemligheter, hanterade Lagringskontonycklar och vault-transaktioner:
| Transaktioner-typ | Maximalt antal transaktioner som är tillåtna om 10 sekunder valv per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2000 |
|

Se [Azure Key Vault-begränsning vägledning](../articles/key-vault/key-vault-ovw-throttling.md) information om hur du hanterar begränsning när gränserna överskrids.

<sup>1</sup> det finns en gräns för alla transaktionstyper hela prenumerationen som är 5 gånger per nyckelvalv gränsen. Till exempel HSM - andra transaktioner per prenumeration är begränsade till 5000 transaktioner om 10 sekunder per prenumeration.
