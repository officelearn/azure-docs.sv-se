---
title: Prestanda alternativ – storskalig (citus) – Azure Database for PostgreSQL
description: Alternativ för en citus-Server (), inklusive Node Compute, Storage och regions.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77462419"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Prestanda alternativ för Azure Database for PostgreSQL – citus)

## <a name="compute-and-storage"></a>Beräkning och lagring
 
Du kan välja beräknings-och lagrings inställningar oberoende för arbetsnoder och koordinator-noden i en citus-Server grupp.  Beräknings resurser tillhandahålls som virtuella kärnor, som representerar den underliggande maskin varans logiska processor. Lagrings storleken för etablering avser kapaciteten som är tillgänglig för koordinatorn och arbetsnoder i citus-servergruppen (). Lagrings utrymmet inkluderar databasfiler, temporära filer, transaktions loggar och postgres-Server loggarna. Den totala mängden lagrings utrymme som du tillhandahåller definierar också den I/O-kapacitet som är tillgänglig för varje anställd och koordinator-nod.
 
|                       | Arbetsnoden           | Koordinator-nod      |
|-----------------------|-----------------------|-----------------------|
| Compute, virtuella kärnor       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Minne per vCore, GiB | 8                     | 4                     |
| Lagrings storlek, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Lagringstyp          | Generell användning (SSD) | Generell användning (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      | Upp till 3 IOPS/GiB      |


## <a name="regions"></a>Regioner
Citus-Server grupper är tillgängliga i följande Azure-regioner:

* Amerika
    * Kanada, centrala
    * USA, centrala
    * USA, östra
    * USA, östra 2
    * USA, norra centrala
    * USA, västra 2
* Asien och stillahavsområdet:
    * Australien, östra
    * Japan, östra
    * Sydkorea, centrala
    * Sydostasien
* Östeuropa
    * Europa, norra
    * Storbritannien, södra
    * Europa, västra

Vissa av dessa regioner kanske inte inlednings vis aktive ras på alla Azure-prenumerationer. Om du vill använda en region från listan ovan och inte ser den i din prenumeration, eller om du vill använda en region som inte finns med i listan, öppnar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Priser
Den senaste pris informationen finns på [sidan med pris](https://azure.microsoft.com/pricing/details/postgresql/)information för tjänsten.
Om du vill se kostnaden för den konfiguration du vill använda visar [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månads kostnaden på fliken **Konfigurera** baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda pris Kalkylatorn för Azure för att få ett uppskattat pris. På webbplatsen för [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt**, expanderar kategorin **databaser** och väljer **Azure Database for PostgreSQL – citus)** för att anpassa alternativen.
 
## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapar en citus-Server (för skalning) i portalen](quickstart-create-hyperscale-portal.md).
