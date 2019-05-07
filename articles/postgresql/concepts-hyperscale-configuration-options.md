---
title: Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) Prestandaalternativ
description: Alternativ för en servergrupp hyperskala (Citus), inklusive noden beräkning, lagring och regioner.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077297"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Azure Database för PostgreSQL – hyperskala (Citus) (förhandsversion) Prestandaalternativ

## <a name="compute-and-storage"></a>Beräkning och lagring
 
Du kan välja inställningarna för beräkning och lagring oberoende av varandra för arbetarnoder och koordinatornoden i en servergrupp i hyperskala (Citus).  Compute-resurser som tillhandahålls som vCores, som representerar en logisk CPU som den underliggande maskinvaran. Lagringsstorlek för etablering refererar till kapaciteten som är tillgängliga för coordinator- och arbetsroller noder i din servergrupp i hyperskala (Citus). Lagringen innehåller databasfiler, temporära filer, transaktionsloggar och Postgres server-loggar. Den totala mängden lagring som du etablerar också definierar den i/o-kapaciteten som är tillgängliga till varje worker, coordinator nod.
 
|                       | Arbetsnod           | Koordinatornod      |
|-----------------------|-----------------------|-----------------------|
| Beräkning, virtuella kärnor       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Minne per vCore, GiB | 8                     | 4                     |
| Lagringsstorleken, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Lagringstyp          | Generell användning (SSD) | Generell användning (SSD) |
| IOPS                  | Upp till 3 IOPS/GiB      | Upp till 3 IOPS/GiB      |


## <a name="regions"></a>Regioner
Servergrupper i hyperskala (Citus) är tillgängliga i följande Azure-regioner:
* USA, östra 2
* Sydostasien
* Västra Europa
* Västra USA 2

## <a name="pricing"></a>Prissättning
Den senaste prisinformationen, finns i tjänsten [prissättningssidan](https://azure.microsoft.com/pricing/details/postgresql/).
Att se kostnaden för den konfiguration du vill ha den [Azure-portalen](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) visar den månatliga kostnaden på den **konfigurera** fliken baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan använda du Azures priskalkylator för att få ett beräknat pris. På den [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator/) webbplats, väljer **lägga till objekt**, expandera den **databaser** kategori, och välj **Azure Database för PostgreSQL – Hyperskala (Citus)** att anpassa alternativ.
 
## <a name="next-steps"></a>Nästa steg
Lär dig hur du [skapa en servergrupp i hyperskala (Citus) i portalen](quickstart-create-hyperscale-portal.md).
