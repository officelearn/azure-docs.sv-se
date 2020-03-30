---
title: Prisnivåer – Azure Database for MariaDB
description: Lär dig mer om de olika prisnivåerna för Azure Database för MariaDB, inklusive beräkningsgenerationer, lagringstyper, lagringsstorlek, virtuella kärnor, minne och lagringsperioder för säkerhetskopiering.
author: jan-eng
ms.author: janeng
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 39af6850810fa471003cea27ed274972fb2ff046
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79528038"
---
# <a name="azure-database-for-mariadb-pricing-tiers"></a>Azure-databas för MariaDB-prisnivåer

Du kan skapa en Azure-databas för MariaDB-server på en av tre olika prisnivåer: Grundläggande, allmänt syfte och Minnesoptimerad. Prisnivåerna differentieras med mängden beräkning i virtuella kärnor som kan etableras, minne per vCore och lagringsteknik som används för att lagra data. Alla resurser etableras på MariaDB-servernivå. En server kan ha en eller flera databaser.

|    | **Basic** | **Allmänt syfte** | **Minnesoptimerat** |
|:---|:----------|:--------------------|:---------------------|
| Beräkna generering | Gen 5 |Gen 5 | Gen 5 |
| vCores (virtuella) | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Minne per vCore | 2 GB | 5 GB | 10 GB |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 4 TB | 5 GB till 4 TB |
| Lagringsperiod för säkerhetskopiering av databas | 7 till 35 dagar | 7 till 35 dagar | 7 till 35 dagar |

Om du vill välja en prisnivå använder du följande tabell som utgångspunkt.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Basic | Arbetsbelastningar som kräver ljusberäkning och I/O-prestanda. Exempel är servrar som används för utveckling eller testning eller småskaliga sällan använda program. |
| Generell användning | De flesta affärsarbetsbelastningar som kräver balanserad beräkning och minne med skalbar I/O-dataflöde. Exempel är servrar för att vara värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | Högpresterande databasarbetsbelastningar som kräver prestanda i minnet för snabbare transaktionsbearbetning och högre samtidighet. Exempel är servrar för bearbetning av realtidsdata och högpresterande transaktions- eller analysappar.|

När du har skapat en server kan antalet virtuella kärnor och prisnivå (förutom till och från Basic) ändras upp eller ned inom några sekunder. Du kan också självständigt justera mängden lagringsutrymme upp och lagringsperioden för säkerhetskopiering upp eller ned utan programstopp. Du kan inte ändra lagringstypen för säkerhetskopiering när en server har skapats. Mer information finns i avsnittet [Skala resurser.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Beräkna generationer och virtuella kärnor

Beräkningsresurser tillhandahålls som virtuella kärnor, som representerar den logiska processorn för den underliggande maskinvaran. Gen 5 logiska processorer är baserade på Intel E5-2673 v4 (Broadwell) 2,3 GHz-processorer.

## <a name="storage"></a>Lagring

Lagringen som du etablerar är den mängd lagringskapacitet som är tillgänglig för din Azure-databas för MariaDB-server. Lagringen används för databasfiler, temporära filer, transaktionsloggar och MariaDB-serverloggarna. Den totala mängden lagringsutrymme som du etablerar definierar också den I/O-kapacitet som är tillgänglig för servern.

|    | **Basic** | **Allmänt syfte** | **Minnesoptimerat** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Grundläggande lagring | Förvaring av allmänt ändamål | Förvaring av allmänt ändamål |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 4 TB | 5 GB till 4 TB |
| Storlek för lagringssteg | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 6000 IOPS |

Du kan lägga till ytterligare lagringskapacitet under och efter att servern har skapats och tillåta att systemet växer lagring automatiskt baserat på lagringsförbrukningen för din arbetsbelastning.

>[!NOTE]
> Lagring kan bara skalas upp, inte ner.

Basic-nivån ger ingen IOPS-garanti. I de allmänna nivåerna för ändamål och minnesoptimerade prisnivåer skalas IOPS-skalan med den etablerade lagringsstorleken i ett 3:1-förhållande.

Du kan övervaka din I/O-förbrukning i Azure-portalen eller med hjälp av Azure CLI-kommandon. De relevanta måtten som ska övervakas är [lagringsgräns, lagringsprocent, lagring som används och IO procent](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Nå lagringsgränsen

Servrar med mindre än 100 GB etablerad lagring markeras endast med skrivskydd om det kostnadsfria lagringsutrymmet är mindre än 5 % av den etablerade lagringsstorleken. Servrar med mer än 100 GB allokerat lagringsutrymme markeras bara som skrivskyddade när det lediga lagringsutrymmet är mindre än 5 GB.

Om du till exempel har etablerat 110 GB lagringsutrymme och det faktiska utnyttjandet går över 105 GB markeras servern skrivskyddad. Alternativt, om du har etablerat 5 GB lagringsutrymme, markeras servern skrivskyddad när det kostnadsfria lagringsutrymmet når mindre än 256 MB.

När tjänsten försöker göra så att servern blir skrivskyddad blockeras alla nya skrivtransaktionsbegäranden och befintliga aktiva transaktioner fortsätter att köras. När servern är i skrivskyddat läge misslyckas alla efterföljande skrivåtgärder och transaktioner. Läsfrågor fortsätter att fungera utan avbrott. När du har ökat lagringen är servern redo att acceptera skrivtransaktioner igen.

Vi rekommenderar att du aktiverar automatisk lagringsförväxning eller ställer in en avisering för att meddela dig när serverlagringen närmar sig tröskelvärdet så att du kan undvika att hamna i skrivskyddat tillstånd. Mer information finns i dokumentationen om [hur du ställer in en avisering](howto-alert-metric.md).

### <a name="storage-auto-grow"></a>Lagring växer automatiskt

Lagring som växer automatiskt förhindrar att servern får på lagringsutrymme och blir skrivskyddad. Om automatisk lagring växer aktiveras, växer lagringen automatiskt utan att påverka arbetsbelastningen. För servrar med mindre än 100 GB etablerad lagring ökas den etablerade lagringsstorleken med 5 GB när det kostnadsfria lagringsutrymmet är lägre än 10 % av den etablerade lagringen. För servrar med mer än 100 GB etablerad lagring ökas den etablerade lagringsstorleken med 5 % när det lediga lagringsutrymmet är lägre än 10 GB av den etablerade lagringsstorleken. Maximala lagringsgränser enligt ovan gäller.

Om du till exempel har etablerat 1000 GB lagringsutrymme och det faktiska utnyttjandet går över 990 GB, ökas serverlagringsstorleken till 1050 GB. Alternativt, om du har etablerat 10 GB lagringsutrymme, ökar lagringsstorleken till 15 GB när mindre än 1 GB lagringsutrymme är gratis.

Kom ihåg att lagring bara kan skalas upp, inte ner.

## <a name="backup"></a>Säkerhetskopiering

Tjänsten tar automatiskt säkerhetskopior av servern. Du kan välja en kvarhållningsperiod från intervallet 7 till 35 dagar. Allmänna servrar och minnesoptimerade servrar kan välja att ha geouppsagbar lagring för säkerhetskopior. Läs mer om säkerhetskopior i [begreppsartikeln](concepts-backup.md).

## <a name="scale-resources"></a>Skala resurser

När du har skapat servern kan du självständigt ändra virtuella kärnor, prisnivån (förutom till och från Basic), mängden lagringsutrymme och lagringsperioden för säkerhetskopiering. Du kan inte ändra lagringstypen för säkerhetskopiering när en server har skapats. Antalet virtuella kärnor kan skalas upp eller ned. Lagringsperioden för säkerhetskopiering kan skalas upp eller ned från 7 till 35 dagar. Lagringsstorleken kan bara ökas. Skalning av resurserna kan göras antingen via portalen eller Azure CLI. 

<!--For an example of scaling by using Azure CLI, see [Monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->

När du ändrar antalet virtuella kärnor, eller prisnivån, skapas en kopia av den ursprungliga servern med den nya beräkningsallokeringen. När den nya servern är igång växlar anslutningarna över till den nya servern. Under tiden då systemet växlar över till den nya servern kan inga nya anslutningar upprättas, och transaktioner som inte allokerats återställs. Hur lång tid detta tar varierar, men i de flesta fall tar det mindre än en minut.

Skalning av lagring och ändring av lagringsperioden för säkerhetskopiering är sanna onlineåtgärder. Det finns ingen driftstopp och ditt program påverkas inte. När IOPS skalar med storleken på den etablerade lagringen kan du öka IOPS som är tillgängligt för servern genom att utöka lagringen.

## <a name="pricing"></a>Prissättning

Den senaste prisinformationen finns på sidan för [tjänstpriser](https://azure.microsoft.com/pricing/details/mariadb/). Om du vill se kostnaden för den konfiguration du vill använda visar [Azure-portalen](https://portal.azure.com/#create/Microsoft.MariaDBServer) månadskostnaden på fliken **Prisnivå** baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan du använda Azure-priskalkylatorn för att få ett uppskattat pris. På webbplatsen [För Azure-priskalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt,** expanderar kategorin **Databaser** och väljer **Azure-databas för MariaDB för** att anpassa alternativen.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [servicebegränsningarna](concepts-limits.md).
- Lär dig hur du [skapar en MariaDB-server i Azure-portalen](quickstart-create-mariadb-server-database-using-azure-portal.md).

<!--
- Learn how to [monitor and scale an Azure Database for MariaDB server by using Azure CLI](scripts/sample-scale-server.md).-->
