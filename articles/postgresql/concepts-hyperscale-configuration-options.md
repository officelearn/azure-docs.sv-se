---
title: Konfigurationsalternativ – Hyperskala (Citus) – Azure-databas för PostgreSQL
description: Alternativ för en citus-servergrupp (Hyperscale), inklusive nodberäkning, lagring och regioner.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804596"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Konfigurationsalternativ för Azure-databas för PostgreSQL – Citus-konfigurationsalternativ (Hyperscale)

## <a name="compute-and-storage"></a>Beräkning och lagring
 
Du kan välja beräknings- och lagringsinställningarna oberoende av varandra för arbetsnoder och koordinatornoden i en citus-servergrupp (Hyperscale).  Beräkningsresurser tillhandahålls som virtuella kärnor, som representerar den logiska processorn för den underliggande maskinvaran. Lagringsstorleken för etablering refererar till den kapacitet som är tillgänglig för koordinatorn och arbetsnoderna i servergruppen Hyperscale (Citus). Lagringen innehåller databasfiler, temporära filer, transaktionsloggar och Postgres-serverloggar.
 
|                       | Arbetsnod           | Koordinatornod      |
|-----------------------|-----------------------|-----------------------|
| Beräkna, virtuella kärnor       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Minne per vCore, GiB | 8                     | 4                     |
| Lagringsstorlek, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Lagringstyp          | Allmänt syfte (SSD) | Allmänt syfte (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      | Upp till 3 IOPS/GiB      |

Den totala mängden RAM-minne i en enda Citus-nod (Hyperscale) baseras på det valda antalet virtuella kärnor.

| vCores (virtuella) | En arbetsnod, GiB RAM | Koordinatornod, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

Den totala mängden lagringsutrymme som du etablerar definierar också I/O-kapaciteten som är tillgänglig för varje arbetar- och koordinatornod.

| Lagringsstorlek, TiB | Maximalt IOPS |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

För hela Citus-klustret (Hyperscale) fungerar det aggregerade IOPS till följande värden:

| Arbetsnoder | 0.5 TiB, totalt IOPS | 1 TiB, totalt IOPS | 2 TiB, totalt IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

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
