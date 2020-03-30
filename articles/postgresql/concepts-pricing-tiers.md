---
title: Prisnivåer - Azure-databas för PostgreSQL - Single Server
description: I den här artikeln beskrivs beräknings- och lagringsalternativen i Azure Database för PostgreSQL - Single Server.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: 2e5b01a271eb290229904fc98d1268760e01620d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243567"
---
# <a name="pricing-tiers-in-azure-database-for-postgresql---single-server"></a>Prisnivåer i Azure Database for PostgreSQL – Enskild server

Du kan skapa en Azure-databas för PostgreSQL-server på en av tre olika prisnivåer: Grundläggande, allmänt syfte och Minnesoptimerad. Prisnivåerna differentieras med mängden beräkning i virtuella kärnor som kan etableras, minne per vCore och lagringsteknik som används för att lagra data. Alla resurser etableras på PostgreSQL-servernivå. En server kan ha en eller flera databaser.

|    | **Basic** | **Allmänt syfte** | **Minnesoptimerat** |
|:---|:----------|:--------------------|:---------------------|
| Beräkna generering | Gen 4, Gen 5 | Gen 4, Gen 5 | Gen 5 |
| vCores (virtuella) | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Minne per vCore | 2 GB | 5 GB | 10 GB |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 16 TB | 5 GB till 16 TB |
| Lagringsperiod för säkerhetskopiering av databas | 7 till 35 dagar | 7 till 35 dagar | 7 till 35 dagar |

Om du vill välja en prisnivå använder du följande tabell som utgångspunkt.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Basic | Arbetsbelastningar som kräver ljusberäkning och I/O-prestanda. Exempel är servrar som används för utveckling eller testning eller småskaliga sällan använda program. |
| Generell användning | De flesta affärsarbetsbelastningar som kräver balanserad beräkning och minne med skalbar I/O-dataflöde. Exempel är servrar för att vara värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | Högpresterande databasarbetsbelastningar som kräver prestanda i minnet för snabbare transaktionsbearbetning och högre samtidighet. Exempel är servrar för bearbetning av realtidsdata och högpresterande transaktions- eller analysappar.|

När du har skapat en server kan antalet virtuella kärnor, maskinvarugenerering och prisnivå (förutom till och från Basic) ändras upp eller ned inom några sekunder. Du kan också självständigt justera mängden lagringsutrymme upp och lagringsperioden för säkerhetskopiering upp eller ned utan programstopp. Du kan inte ändra lagringstypen för säkerhetskopiering när en server har skapats. Mer information finns i avsnittet [Skala resurser.](#scale-resources)

## <a name="compute-generations-and-vcores"></a>Beräkna generationer och virtuella kärnor

Beräkningsresurser tillhandahålls som virtuella kärnor, som representerar den logiska processorn för den underliggande maskinvaran. China East 1, China North 1, US DoD Central och US DoD East använder Gen 4 logiska processorer som är baserade på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer. Alla andra regioner använder logiska gen 5-processorer som är baserade på Intel E5-2673 v4 (Broadwell) 2,3 GHz-processorer.

## <a name="storage"></a>Lagring

Lagringen som du etablerar är den mängd lagringskapacitet som är tillgänglig för din Azure-databas för PostgreSQL-server. Lagringen används för databasfiler, temporära filer, transaktionsloggar och PostgreSQL-serverloggar. Den totala mängden lagringsutrymme som du etablerar definierar också den I/O-kapacitet som är tillgänglig för servern.

|    | **Basic** | **Allmänt syfte** | **Minnesoptimerat** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Grundläggande lagring | Förvaring av allmänt ändamål | Förvaring av allmänt ändamål |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 16 TB | 5 GB till 16 TB |
| Storlek för lagringssteg | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20 000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20 000 IOPS |

> [!NOTE]
> Lagring upp till 16 TB och 20 000 IOPS stöds i följande regioner: Östra USA, Östra USA 2, Centrala USA, Västra USA, Norra CENTRALA USA, Södra Centrala USA, Nordeurop, Västeuropa, Storbritannien syd, västra Storbritannien, Sydostasien, Östasien, Östra Japan, Västra Japan, Korea Central , Korea South, Östra Australien, Sydöstra Australien.
>
> Alla andra regioner stöder upp till 4 TB lagringsutrymme och 6000 IOPS.
>

Du kan lägga till ytterligare lagringskapacitet under och efter att servern har skapats och tillåta att systemet växer lagring automatiskt baserat på lagringsförbrukningen för din arbetsbelastning. 

>[!NOTE]
> Lagring kan bara skalas upp, inte ner.

Basic-nivån ger ingen IOPS-garanti. I de allmänna nivåerna för ändamål och minnesoptimerade prisnivåer skalas IOPS-skalan med den etablerade lagringsstorleken i ett 3:1-förhållande.

Du kan övervaka din I/O-förbrukning i Azure-portalen eller med hjälp av Azure CLI-kommandon. De relevanta måtten som ska övervakas är [lagringsgräns, lagringsprocent, lagring som används och IO procent](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Nå lagringsgränsen

Servrar med mindre än 100 GB etablerad lagring markeras skrivskyddat om det kostnadsfria lagringsutrymmet är mindre än 512 MB eller 5 % av den etablerade lagringsstorleken. Servrar med mer än 100 GB allokerat lagringsutrymme markeras bara som skrivskyddade när det lediga lagringsutrymmet är mindre än 5 GB.

Om du till exempel har etablerat 110 GB lagringsutrymme och det faktiska utnyttjandet går över 105 GB markeras servern skrivskyddad. Alternativt, om du har etablerat 5 GB lagringsutrymme, är servern märkt skrivskyddad när den kostnadsfria lagringen når mindre än 512 MB.

När servern är skrivskyddad kopplas alla befintliga sessioner från och oengagerade transaktioner återställs. Alla efterföljande skrivåtgärder och transaktionsavst har misslyckats. Alla efterföljande läsfrågor fungerar oavbrutet.  

Du kan antingen öka mängden etablerad lagring till servern eller starta en ny session i skriv-skriv-läge och släppa data för att frigöra gratis lagringsutrymme. När `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` du kör ställs den aktuella sessionen in på att läsa skrivläge. För att undvika dataskador ska du inte utföra några skrivåtgärder när servern fortfarande är skrivskyddad.

Vi rekommenderar att du aktiverar automatisk lagringsförväxning eller ställer in en avisering för att meddela dig när serverlagringen närmar sig tröskelvärdet så att du kan undvika att hamna i skrivskyddat tillstånd. Mer information finns i dokumentationen om [hur du ställer in en avisering](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Lagring växer automatiskt

Lagring som växer automatiskt förhindrar att servern får på lagringsutrymme och blir skrivskyddad. Om automatisk lagring växer aktiveras, växer lagringen automatiskt utan att påverka arbetsbelastningen. För servrar med mindre än 100 GB etablerad lagring ökas den etablerade lagringsstorleken med 5 GB så snart det kostnadsfria lagringsutrymmet är lägre än 1 GB eller 10 % av den etablerade lagringen. För servrar med mer än 100 GB av etablerad lagring ökas den etablerade lagringsstorleken med 5 % när det lediga lagringsutrymmet är lägre än 10 GB eller 5 % av den etablerade lagringsstorleken. Maximala lagringsgränser enligt ovan gäller.

Om du till exempel har etablerat 1000 GB lagringsutrymme och det faktiska utnyttjandet går över 950 GB, ökas serverlagringsstorleken till 1050 GB. Alternativt, om du har etablerat 10 GB lagringsutrymme, ökar lagringsstorleken till 15 GB när mindre än 1 GB lagringsutrymme är gratis.

Kom ihåg att lagring bara kan skalas upp, inte ner.

## <a name="backup"></a>Säkerhetskopiering

Tjänsten tar automatiskt säkerhetskopior av servern. Du kan välja en kvarhållningsperiod från intervallet 7 till 35 dagar. Allmänna servrar och minnesoptimerade servrar kan välja att ha geouppsagbar lagring för säkerhetskopior. Läs mer om säkerhetskopior i [begreppsartikeln](concepts-backup.md).

## <a name="scale-resources"></a>Skala resurser

När du har skapat servern kan du självständigt ändra virtuella kärnor, maskinvarugenerering, prisnivå (förutom till och från Basic), mängden lagringsutrymme och lagringsperioden för säkerhetskopiering. Du kan inte ändra lagringstypen för säkerhetskopiering när en server har skapats. Antalet virtuella kärnor kan skalas upp eller ned. Lagringsperioden för säkerhetskopiering kan skalas upp eller ned från 7 till 35 dagar. Lagringsstorleken kan bara ökas. Skalning av resurserna kan göras antingen via portalen eller Azure CLI. Ett exempel på skalning med hjälp av Azure CLI finns i [Övervaka och skala en Azure-databas för PostgreSQL-server med hjälp av Azure CLI](scripts/sample-scale-server-up-or-down.md).

> [!NOTE] 
> Lagringsstorleken kan bara ökas. Du kan inte gå tillbaka till en mindre lagringsstorlek efter ökningen.

När du ändrar antalet virtuella kärnor, maskinvarugenerering eller prisnivå skapas en kopia av den ursprungliga servern med den nya beräkningsallokeringen. När den nya servern är igång växlar anslutningarna över till den nya servern. Under tiden då systemet växlar över till den nya servern kan inga nya anslutningar upprättas, och transaktioner som inte allokerats återställs. Hur lång tid detta tar varierar, men i de flesta fall tar det mindre än en minut.

Skalning av lagring och ändring av lagringsperioden för säkerhetskopiering är sanna onlineåtgärder. Det finns ingen driftstopp och ditt program påverkas inte. När IOPS skalar med storleken på den etablerade lagringen kan du öka IOPS som är tillgängligt för servern genom att utöka lagringen.

## <a name="pricing"></a>Prissättning

Den senaste prisinformationen finns på sidan för [tjänstpriser](https://azure.microsoft.com/pricing/details/PostgreSQL/). Om du vill se kostnaden för den konfiguration du vill använda visar [Azure-portalen](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) månadskostnaden på fliken **Prisnivå** baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan du använda Azure-priskalkylatorn för att få ett uppskattat pris. På webbplatsen [För Azure-priskalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt,** expanderar kategorin **Databaser** och väljer **Azure-databas för PostgreSQL för** att anpassa alternativen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en PostgreSQL-server i portalen](tutorial-design-database-using-azure-portal.md).
- Läs mer om [servicegränser](concepts-limits.md). 
- Lär dig hur du [skalar ut med läsrepliker](howto-read-replicas-portal.md).
