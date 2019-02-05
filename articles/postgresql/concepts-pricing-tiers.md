---
title: Prisnivåer för Azure Database for PostgreSQL
description: Den här artikeln beskrivs olika prisnivåer för Azure Database för PostgreSQL.
author: jan-eng
ms.author: janeng
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/01/2019
ms.openlocfilehash: 8cc7d7d93f483289ab7faa3f5a7193fc218b57bc
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695035"
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Azure Database för PostgreSQL prisnivåer

Du kan skapa en Azure Database for PostgreSQL-server i någon av tre olika tjänstnivåer: Grundläggande, generell användning och minnesoptimerad. Prisnivåerna åtskiljs av mängden beräkning i vCores som kan etableras och minne per vCore lagringsteknik som används för att lagra data. Alla resurser etableras på servernivå för PostgreSQL. En server kan ha en eller flera databaser.

|    | **Basic** | **Generell användning** | **Optimerat minne** |
|:---|:----------|:--------------------|:---------------------|
| Compute-generering | Gen 4, generation 5 | Gen 4, generation 5 | Generation 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Minne per vCore | 2 GB | 5 GB | 10 GB |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 4 TB | 5 GB till 4 TB |
| Lagringstyp | Azure Standard-lagring | Azure Premium Storage | Azure Premium Storage |
| Kvarhållningsperiod för databasen | 7 till 35 dagar | 7 till 35 dagar | 7 till 35 dagar |

Använd följande tabell som utgångspunkt för att välja en prisnivå.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Basic | Arbetsbelastningar som kräver lätt beräkning och i/o-prestanda. Exempel är servrar som används för utveckling eller testning eller sällan småskaliga program. |
| Generellt syfte | De flesta företags arbetsbelastningar som kräver belastningsutjämnade beräknings- och minnesresurser med skalbart i/o-dataflöde. Exempel är servrar som värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | Högpresterande arbetsbelastningar som kräver minnesprestanda för snabbare bearbetning av transaktioner och högre samtidighet. Exempel är servrar för att bearbeta realtidsdata och transaktionsappar eller analysappar appar med höga prestanda.|

När du har skapat en server, antalet virtuella kärnor, skapande av maskinvara och priser nivå (förutom till och från Basic) kan ändras upp eller ned på några sekunder. Du kan även oberoende Justera mängden lagringsutrymme upp och kvarhållningsperioden för säkerhetskopior upp eller ned utan någon nedtid. Du kan inte ändra lagringstypen säkerhetskopiering när en server har skapats. Mer information finns i den [skala resurser](#scale-resources) avsnittet.


## <a name="compute-generations-and-vcores"></a>Beräkningsgenereringar och virtuella kärnor

Compute-resurser som tillhandahålls som vCores, som representerar en logisk CPU som den underliggande maskinvaran. För närvarande kan du välja mellan två beräkningsgenereringar Gen 4 och 5 för Gen. Gen 4 logiska CPU baseras på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer. Gen 5 logiska CPU baseras på Intel E5-2673 v4-processorn (Broadwell) 2.3 GHz-processorer. Gen 4 och 5 Gen finns i följande regioner (”X” anger tillgängliga). 

> [!IMPORTANT]
> Från och 12 December 2018 kommer nya kunder inte att kunna etablera compute generation 4 servrar i södra Brasilien, centrala Kanada, Östra Kanada, östra Asien, östra USA 2, centrala Indien, västra Indien, västra Japan, norra centrala USA, västra USA. Tidigare skapade compute generation 4 servrar kommer att migreras till beräknings-generation 5 med början den 1 februari 2019 i dessa regioner.

| **Azure-region** | **Generation 4** | **5: e generationen** |
|:---|:----------:|:--------------------:|
| Centrala USA |  | X |
| Östra USA |  | X |
| USA, östra 2 | X | X |
| Norra centrala USA | X | X |
| Södra centrala USA | X | X |
| Västra USA | X | X |
| Västra USA 2 |  | X |
| Södra Brasilien | X | X |
| Centrala Kanada | X | X |
| Östra Kanada | X | X |
| Norra Europa | X | X |
| Västra Europa |  | X |
| Frankrike, centrala |  | X |
| Storbritannien, södra |  | X |
| Storbritannien, västra |  | X |
| Östasien | X | X |
| Sydostasien | X | X |
| Östra Australien |  | X |
| Australien, centrala |  | X |
| Australien, centrala 2 |  | X |
| Sydöstra Australien |  | X |
| Indien, centrala | X | X |
| Södra Indien |  | X |
| Indien, västra | X | X |
| Östra Japan | X | X |
| Västra Japan | X | X |
| Sydkorea, centrala |  | X |
| Sydkorea, södra |  | X |
| Östra Kina 1 | X |  |
| Kina, östra 2 |  | X |
| Norra Kina 1 | X |  |
| Kina, norra 2 |  | X |
| Centrala Tyskland |  | X |
| US DoD, centrala  | X |  |
| US DoD, östra  | X |  |
| Arizona (USA-förvaltad region) |  | X |
| Texas (USA-förvaltad region) |  | X |
| Virginia (USA-förvaltad region) |  | X |

## <a name="storage"></a>Storage

Lagring som du etablerar är mängden lagringskapacitet som är tillgängliga för din Azure Database for PostgreSQL-server. Lagringsutrymmet används för databasfilerna, temporära filer, transaktionsloggar och PostgreSQL-servern loggar. Den totala mängden lagring som du etablerar definierar också tillgänglig i/o-kapacitet till din server.

|    | **Basic** | **Generell användning** | **Optimerat minne** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Azure Standard-lagring | Azure Premium Storage | Azure Premium Storage |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 4 TB | 5 GB till 4 TB |
| Öka lagringsstorleken | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS<br/>Maximalt antal 6000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Maximalt antal 6000 IOPS |

Du kan lägga till ytterligare lagringskapacitet under och efter skapandet av servern. Basic-nivån ger inte en garanti för IOPS. I generell användning och Minnesoptimerad prisnivåer, skala IOPS med den allokerade lagringsstorleken i ett 3:1-förhållande.

Du kan övervaka dina i/o-användningen i Azure portal eller med hjälp av Azure CLI-kommandon. De mått som är relevanta för att övervaka är [gränsen för lagring, lagringsprocent, lagringsutrymme och IO-procent](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Når gränsen för lagring

Servern markeras som skrivskyddad när mängden ledigt utrymme är mindre än 5 GB eller 5 % av lagringen, beroende på vilket som är minst. Exempel: Om du har etablerat 100 GB lagringsutrymme och den faktiska användningen går över 95 GB, servern är skrivskyddad. Eller om du har etablerat 5 GB lagringsutrymme blir servern markerad som skrivskyddad om det lediga utrymmet understiger 250 MB.  

När servern har angetts till skrivskyddat läge kopplas bort alla befintliga sessioner och ogenomförda transaktioner återställs. Alla efterföljande skrivåtgärder och transaktionen genomför misslyckas. Alla efterföljande skrivskyddade frågor fungerar utan avbrott.  

Du kan öka mängden etablerat lagringsutrymme till din server, eller så kan du starta en ny session i skrivskyddad läge och släpp information för att frigöra ledigt utrymme. Kör `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` anger den aktuella sessionen att läsa skrivläge. För att undvika att data skadas, utför inte alla skrivåtgärder när servern är fortfarande i skrivskyddad status.

Vi rekommenderar att du ställer in en avisering som meddelar dig när tröskelvärdet närmar sig din serverlagring för att undvika hämta till skrivskyddat läge. Mer information finns i dokumentationen på [hur du ställer in en avisering](howto-alert-on-metric.md).

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhetskopior av din server. Minsta kvarhållningsperioden för säkerhetskopior är sju dagar. Du kan ange en kvarhållningsperiod på upp till 35 dagar. Kvarhållning kan justeras när som helst under livslängden för servern. Du kan välja mellan lokalt redundant och geo-redundanta säkerhetskopieringar. GEO-redundanta säkerhetskopieringar lagras också i den [geoparats ihop region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) för den region där din server skapas. Den här redundans ger en nivå av skydd i händelse av en katastrof. Du får också möjlighet att återställa servern till alla andra Azure-regioner där tjänsten är tillgänglig med geo-redundanta säkerhetskopieringar. Det går inte att ändra mellan de två säkerhetskopieringslagring alternativen när servern har skapats.

## <a name="scale-resources"></a>Skala resurser

När du har skapat din server du oberoende av varandra kan ändra den virtuella kärnor, maskinvara generation, prisnivån (förutom till och från Basic), hur mycket lagringsutrymme och kvarhållningsperioden för säkerhetskopior. Du kan inte ändra lagringstypen säkerhetskopiering när en server har skapats. Antalet virtuella kärnor kan skalas upp eller ned. Kvarhållningsperiod för säkerhetskopiering kan skalas upp eller ned från 7 till 35 dagar. Lagringsutrymmet kan bara ökas. Skalning av resurser kan göras antingen via portalen eller Azure CLI. Ett exempel på skalning med hjälp av Azure CLI, se [övervaka och skala en Azure Database for PostgreSQL-server med hjälp av Azure CLI](scripts/sample-scale-server-up-or-down.md).

När du ändrar antalet virtuella kärnor, skapas kontot maskinvara eller prisnivån, en kopia av den ursprungliga servern för den nya compute-fördelningen. När den nya servern är igång kan växlas anslutningar till den nya servern. Inga nya anslutningar kan upprättas vid den tidpunkt då när systemet växlar till den nya servern, och alla ogenomförda transaktioner återställs. Det här fönstret varierar, men i de flesta fall är mindre än en minut.

Skala lagring och ändra kvarhållningsperioden för säkerhetskopior är SANT online åtgärder. Stilleståndstid ingen och ditt program påverkas inte. När IOPS skalar med storleken på den etablerade lagringen, kan du öka IOPS som är tillgängliga till servern genom att skala upp lagring.

## <a name="pricing"></a>Prissättning

Den senaste prisinformationen, finns i tjänsten [prissättningssidan](https://azure.microsoft.com/pricing/details/PostgreSQL/). Att se kostnaden för den konfiguration du vill ha den [Azure-portalen](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) visar den månatliga kostnaden på den **prisnivå** fliken baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan använda du Azures priskalkylator för att få ett beräknat pris. På den [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator/) webbplats, väljer **lägga till objekt**, expandera den **databaser** kategori, och välj **Azure Database for PostgreSQL** att anpassa alternativ.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en PostgreSQL-server i portalen](tutorial-design-database-using-azure-portal.md).
- Lär dig mer om [tjänstbegränsningar](concepts-limits.md). 
- Lär dig hur du [skala ut med skrivskyddade repliker](howto-read-replicas-portal.md).
