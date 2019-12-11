---
title: Prestanda alternativ – storskalig (citus) – Azure Database for PostgreSQL
description: Alternativ för en citus-Server (), inklusive Node Compute, Storage och regions.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975643"
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
    * Kanada, centrala *
    * USA, östra
    * USA, östra 2
    * Norra centrala USA *
    * USA, västra 2
* Asien och stillahavsområdet:
    * Östra Australien *
    * Asien, sydöstra
* Östeuropa
    * Europa, norra
    * Storbritannien, södra
    * Europa, västra

Regioner med en asterisk (\*) har ännu inte stöd för [hög tillgänglighet](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Prissättning
Den senaste pris informationen finns på [sidan med pris](https://azure.microsoft.com/pricing/details/postgresql/)information för tjänsten.
Om du vill se kostnaden för den konfiguration du vill använda visar [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månads kostnaden på fliken **Konfigurera** baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda pris Kalkylatorn för Azure för att få ett uppskattat pris. På webbplatsen för [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt**, expanderar kategorin **databaser** och väljer **Azure Database for PostgreSQL – citus)** för att anpassa alternativen.
 
## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapar en citus-Server (för skalning) i portalen](quickstart-create-hyperscale-portal.md).
