---
author: rothja
ms.service: billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 9a39abf77a7396302f93e5a423271402b7c3edb3
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54084012"
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
> Tröskelvärdena ovan viktas och tvingande finns på deras summa. Du kan göra 125 RSA-HSM - 4k-åtgärder och 0 RSA-HSM - 2k, eller 124 RSA-HSM - 4k och 16 RSA-HSM - 2 kB. Därefter, i samma 10 sekunder intervall genereras andra åtgärder ett AKV-klienten.

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
