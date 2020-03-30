---
title: Prestandaalternativ – Hyperskala (Citus) – Azure-databas för PostgreSQL
description: Alternativ för en citus-servergrupp (Hyperscale), inklusive nodberäkning, lagring och regioner.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462419"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure-databas för PostgreSQL – Prestandaalternativ för hyperskala (Citus)

## <a name="compute-and-storage"></a>Beräkning och lagring
 
Du kan välja beräknings- och lagringsinställningarna oberoende av varandra för arbetsnoder och koordinatornoden i en citus-servergrupp (Hyperscale).  Beräkningsresurser tillhandahålls som virtuella kärnor, som representerar den logiska processorn för den underliggande maskinvaran. Lagringsstorleken för etablering refererar till den kapacitet som är tillgänglig för koordinatorn och arbetsnoderna i servergruppen Hyperscale (Citus). Lagringen innehåller databasfiler, temporära filer, transaktionsloggar och Postgres-serverloggar. Den totala mängden lagringsutrymme som du etablerar definierar också I/O-kapaciteten som är tillgänglig för varje arbetar- och koordinatornod.
 
|                       | Arbetsnod           | Koordinatornod      |
|-----------------------|-----------------------|-----------------------|
| Beräkna, virtuella kärnor       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Minne per vCore, GiB | 8                     | 4                     |
| Lagringsstorlek, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Lagringstyp          | Allmänt syfte (SSD) | Allmänt syfte (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      | Upp till 3 IOPS/GiB      |


## <a name="regions"></a>Regioner
Citus-servergrupper (Hyperscale) är tillgängliga i följande Azure-regioner:

* Nord- och Sydamerika: 
    * Kanada, centrala
    * USA, centrala
    * USA, östra
    * USA, östra 2
    * USA, norra centrala
    * USA, västra 2
* Asien och Stillahavsområdet:
    * Australien, östra
    * Japan, östra
    * Sydkorea, centrala
    * Sydostasien
* Europa:
    * Europa, norra
    * Storbritannien, södra
    * Europa, västra

Vissa av dessa regioner kanske inte aktiveras från början på alla Azure-prenumerationer. Om du vill använda en region från listan ovan och inte ser den i din prenumeration, eller om du vill använda en region som inte finns med i den här listan, öppnar du en [supportbegäran](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Prissättning
Den senaste prisinformationen finns på sidan för [tjänstpriser](https://azure.microsoft.com/pricing/details/postgresql/).
Om du vill se kostnaden för den konfiguration du vill använda visar [Azure-portalen](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månadskostnaden på fliken **Konfigurera** baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan du använda Azure-priskalkylatorn för att få ett uppskattat pris. På webbplatsen [För Azure-priskalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt,** expanderar kategorin **Databaser** och väljer Azure Database **for PostgreSQL – Hyperscale (Citus)** för att anpassa alternativen.
 
## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapar en citus-servergrupp (Hyperscale) i portalen](quickstart-create-hyperscale-portal.md).
