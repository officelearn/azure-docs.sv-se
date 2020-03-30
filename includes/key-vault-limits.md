---
author: rothja
ms.service: cost-management-billing
ms.topic: include
ms.date: 11/09/2018
ms.author: jroth
ms.openlocfilehash: 2d16febd4676ca7ba763eb7bc6dcecda4608ebb5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74224340"
---
#### <a name="key-transactions-maximum-transactions-allowed-in-10-seconds-per-vault-per-regionsup1sup"></a>Nyckeltransaktioner (högsta tillåtna transaktioner på 10 sekunder, per valv per region<sup>1</sup>):

|Nyckeltyp|HSM-nyckel<br>SKAPA-tangenten|HSM-nyckel<br>Alla andra transaktioner|Programvarunyckel<br>SKAPA-tangenten|Programvarunyckel<br>Alla andra transaktioner|
|:---|---:|---:|---:|---:|
|RSA 2 048-bitars|5|1,000|10|2 000|
|RSA 3 072-bitars|5|250|10|500|
|RSA 4 096-bitars|5|125|10|250|
|ECC P-256|5|1,000|10|2 000|
|ECC P-384|5|1,000|10|2 000|
|ECC P-521|5|1,000|10|2 000|
|ECC SEKP256K1|5|1,000|10|2 000|

> [!NOTE]
> I föregående tabell ser vi att för RSA 2.048-bitars programvarunycklar, 2.000 GET transaktioner per 10 sekunder är tillåtna. För RSA 2 048-bitars HSM-nycklar är 1 000 GET-transaktioner per 10 sekunder tillåtna.
>
> Begränsningströsklarna viktas och verkställigheten är på deras summa. Som visas i föregående tabell är det till exempel åtta gånger dyrare att använda 4 096-bitarsnycklar när du utför GET-åtgärder på RSA HSM-nycklar. Det beror på att 1000/125 = 8.
>
> I ett givet 10-sekundersintervall kan en Azure Key Vault-klient bara `429` utföra *en* av följande åtgärder innan en begränsning av HTTP-statuskoden:
> - 2 000 RSA 2 048-bitars get-transaktioner med programvarunyckel
> - 1 000 RSA 2 048-bitars HSM-nyckel GET-transaktioner
> - 125 RSA 4 096-bitars HSM-nyckel GET-transaktioner
> - 124 RSA 4 096-bitars HSM-nyckel GET-transaktioner och 8 RSA 2 048-bitars HSM-nyckel GET-transaktioner

#### <a name="secrets-managed-storage-account-keys-and-vault-transactions"></a>Hemligheter, hanterade lagringskontonycklar och arkivtransaktioner:
| Typ av transaktioner | Högsta tillåtna transaktioner på 10 sekunder, per valv per region<sup>1</sup> |
| --- | --- |
| Alla transaktioner |2 000 |

Information om hur du hanterar begränsning när dessa gränser överskrids finns i [Azure Key Vault begränsningsvägledning](../articles/key-vault/key-vault-ovw-throttling.md).

<sup>1</sup> En gräns för hela prenumerationen för alla transaktionstyper är fem gånger per nyckelvalvsgräns. Till exempel är HSM-andra transaktioner per prenumeration begränsade till 5 000 transaktioner på 10 sekunder per prenumeration.
