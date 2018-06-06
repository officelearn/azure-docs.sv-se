---
title: Prisnivåer för Azure-databas för PostgreSQL
description: Den här artikeln beskrivs alla prisnivåer för Azure-databas för PostgreSQL.
services: postgresql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 9e01d3a69fe8814d4864bccf94c0d65ea573ada8
ms.sourcegitcommit: 4f9fa86166b50e86cf089f31d85e16155b60559f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34756644"
---
# <a name="azure-database-for-postgresql-pricing-tiers"></a>Azure-databas för PostgreSQL prisnivåer

Du kan skapa en Azure-databas för PostgreSQL-server i en av tre olika prisnivåer: Basic generella och Minnesoptimerade. Alla prisnivåer särskiljs med hjälp av mängden beräkning i vCores som kan etableras och minne per vCore lagringsteknik som används för att lagra data. Alla resurser etableras på servernivå PostgreSQL. En server kan ha en eller flera databaser.

|    | **Basic** | **Generella** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Compute-generering | Gen 4 Gen 5 | Gen 4 Gen 5 | Generation 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Minne per vCore | 2 GB | 5 GB | 10 GB |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 2 TB | 5 GB till 2 TB |
| Lagringstyp | Azure standardlagring | Azure Premium Storage | Azure Premium Storage |
| Databasen period för lagring av säkerhetskopior. | 7-35 dagar | 7-35 dagar | 7-35 dagar |

Använd följande tabell som utgångspunkt för att välja en prisnivå.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Basic | Arbetsbelastningar som kräver lätta beräknings- och i/o-prestanda. Exempel är servrar som används för utveckling och testning eller småskaliga program som inte används. |
| Generellt syfte | De flesta arbetsbelastningar som kräver belastningsutjämnade beräknings- och minneskapaciteten med skalbara i/o-genomströmning. Exempel är servrar som värd för webb- och mobilappar och andra företagsprogram.|
| Minnesoptimerad | För högpresterande databasarbetsbelastningar som kräver InMemory-prestanda för snabbare transaktionsbearbetning och högre samtidighet. Exempel: servrar för att bearbeta data i realtid och högpresterande Transaktionsreplikering eller analytiska appar.|

När du har skapat en server, kan antalet vCores ändras uppåt eller nedåt (inom samma prisnivån) inom några sekunder. Du kan även oberoende Justera mängden lagringsutrymme upp och säkerhetskopiering kvarhållningsperioden uppåt eller nedåt utan avbrott för programmet. Du kan inte ändra prisnivån eller typen lagring för säkerhetskopiering när en server har skapats. Mer information finns i [skala](#scale-resources) avsnitt.


## <a name="compute-generations-and-vcores"></a>Beräkna generationer och vCores

Beräkna resurser tillhandahålls som vCores som representerar den underliggande maskinvaran logiska Processorn. För närvarande kan kan du välja mellan två beräkning generationer Gen 4 och 5 Gen. Gen 4 logiska processorer är baserade på Intel E5-2673 v3 (Haswell) 2,4 GHz processorer. Gen 5 logiska processorer är baserade på Intel E5-2673 v4 (Broadwell) 2.3 GHz-processorer. Gen 4 och 5 Gen finns i följande regioner (”X” anger tillgänglig). 

| **Azure-region** | **Gen 4** | **Gen 5** |
|:---|:----------:|:--------------------:|
| Centrala USA | X |  |
| Östra USA | X | X |
| Östra USA 2 | X | X |
| Norra centrala USA | X |  |
| Södra centrala USA | X | X |
| Västra USA | X | X |
| Västra USA 2 |  | X |
| Centrala Kanada | X | X |
| Östra Kanada | X | X |
| Södra Brasilien | X | X |
| Norra Europa | X | X |
| Västra Europa |  | X |
| Storbritannien, västra |  | X |
| Storbritannien, södra |  | X |
| Östasien | X |  |
| Sydostasien | X | X |
| Östra Australien |  | X |
| Sydöstra Australien |  | X |
| Indien, centrala | X | X |
| Indien, västra | X | X |
| Södra Indien |  | X |
| Östra Japan | X | X |
| Västra Japan | X | X |
| Sydkorea |  | X |

## <a name="storage"></a>Storage

Lagring som du etablerar är mängden lagringskapacitet som är tillgängliga för din Azure-databas för PostgreSQL-server. Lagringsutrymmet som används för databasfilerna, temporära filer, transaktionsloggar och PostgreSQL-servern loggar. Den totala mängden lagringsutrymme som du etablerar definierar även i/o-kapaciteten tillgänglig till servern.

|    | **Basic** | **Generella** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Azure standardlagring | Azure Premium Storage | Azure Premium Storage |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 2 TB | 5 GB till 2 TB |
| Öka lagringsstorlek | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS | 3 IOPS/GB<br/>Min 100 IOPS |

Du kan lägga till ytterligare lagringskapacitet under och efter skapandet av servern. Den grundläggande nivån innehåller inte en IOPS-garanti. I den generella och Minnesoptimerade prisnivåer, skala IOPS med den etablerade lagringsstorleken i förhållandet 3:1.

Du kan övervaka dina i/o-användning i Azure-portalen eller genom att använda Azure CLI-kommandona. Mätvärdena som är relevanta för att övervaka är [lagringsgräns, lagringsprocent, lagringsutrymme som används och IO-procent](concepts-monitoring.md).

### <a name="reaching-the-store-limit"></a>Att lagra gränsen

Servern är skrivskyddad när mängden ledigt utrymme uppnår mindre än 5 GB eller 5% av etablerade lagring, som är minst. Om du har etablerat 100 GB lagringsutrymme och den faktiska användningen går till exempel 95 GB, servern är skrivskyddad. Alternativt, om du har tillhandahållit 5 GB lagringsutrymme, servern är skrivskyddad när mängden ledigt utrymme når mindre än 250 MB.  

När servern har angetts till skrivskyddad kopplas bort alla befintliga sessioner och ogenomförda transaktioner återställs. Alla efterföljande skrivåtgärder och transaktionen genomför misslyckas. Alla efterföljande skrivskyddade frågor fungerar utan avbrott.  

Du kan öka mängden lagringsutrymme som är allokerade till servern, eller så kan du starta en ny session i läsa / skriva-läge och släpp data att frigöra ledigt utrymme. Kör `SET SESSION CHARACTERISTICS AS TRANSACTION READ WRITE;` anger den aktuella sessionen med read-write-läge. För att undvika att data skadas, utför inte någon skrivåtgärder när servern är i skrivskyddat läge status.

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhetskopior av servern. Minsta kvarhållningsperioden för säkerhetskopiering är sju dagar. Du kan ange en kvarhållningsperiod på upp till 35 dagar. Kvarhållning kan justeras när som helst under livslängden för servern. Du kan välja mellan lokalt redundant och geo-redundant säkerhetskopieringar. GEO-redundant säkerhetskopior lagras också i den [geo länkas region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) för den region där din server har skapats. Detta ger en nivå av skydd vid en katastrof. Du kan också få möjlighet att återställa servern till några andra Azure-region där tjänsten är tillgänglig med geo-redundant säkerhetskopior. Det går inte att ändra mellan de två säkerhetskopieringslagring alternativen när servern har skapats.

## <a name="scale-resources"></a>Skala resurser

När du har skapat din server ändra du oberoende vCores, mängden lagringsutrymme och säkerhetskopiering kvarhållningsperioden. Du kan inte ändra prisnivån eller typen lagring för säkerhetskopiering när en server har skapats. Antalet vCores kan skalas upp eller ned inom samma prisnivå. Säkerhetskopiering kvarhållningsperioden kan skalas upp eller ned från 7 till 35 dagar. Lagringsstorleken kan bara ökas.  Skalning av resurser kan göras antingen via portalen eller Azure CLI. Ett exempel på skalning med Azure CLI, se [Övervakare och skala en Azure-databas för PostgreSQL-servern med hjälp av Azure CLI](scripts/sample-scale-server-up-or-down.md).

När du ändrar antalet vCores, skapas en kopia av den ursprungliga servern för den nya beräknings-fördelningen. När den nya servern är igång, växlas anslutningar till den nya servern. Inga nya anslutningar kan upprättas vid den tidpunkt då när datorn växlar till den nya servern, och alla ogenomförda transaktioner återställs. Det här fönstret varierar, men i de flesta fall är mindre än en minut.

Skala lagring och ändra säkerhetskopiering kvarhållningsperiod är true online åtgärder. Det finns inget driftstopp och programmet påverkas inte. Som IOPS skala med storleken på lagringsplatsen etablerade öka du antalet IOPS som är tillgängliga till servern genom att skala upp lagring.

## <a name="pricing"></a>Prissättning

Den senaste prisinformation finns tjänsten [sida med priser](https://azure.microsoft.com/pricing/details/PostgreSQL/). Att se kostnaden för den konfiguration du vill ha den [Azure-portalen](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) visar månadskostnaden på den **prisnivå** fliken baserat på de alternativ du väljer. Om du inte har en Azure-prenumeration kan använda du Azure prisnivå Kalkylatorn för att hämta ett beräknat pris. På den [Azure prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/) webbplatsen, väljer **lägga till objekt**, expandera den **databaser** kategori, och välj **Azure-databas för PostgreSQL** du anpassar alternativen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en PostgreSQL-server i portalen](tutorial-design-database-using-azure-portal.md).
- Lär dig hur du [övervaka och skala en Azure-databas för PostgreSQL-servern med hjälp av Azure CLI](scripts/sample-scale-server-up-or-down.md).
- Lär dig mer om den [tjänsten begränsningar](concepts-limits.md).
