---
title: Prisnivåer i Azure MySQL-databas
description: Den här artikeln beskriver prisnivåerna i Azure-databas för MySQL.
services: mysql
author: jan-eng
ms.author: janeng
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/20/2018
ms.openlocfilehash: ec475648f1da4420e86bf59053d95770409bed8e
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure-databas för MySQL prisnivåer

En Azure-databas för MySQL-server kan skapas i ett av tre olika prisnivåer - Basic generella och Minnesoptimerade. Alla prisnivåer särskiljs med hjälp av mängden beräkning i vCores som kan etableras och minne per vCore lagringsteknik som används för att lagra data. Alla resurser etableras på servernivå MySQL. En server kan ha en eller flera databaser.

|    | **Basic** | **Generella** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Compute-generering | Gen 4 Gen 5 | Gen 4 Gen 5 | Generation 5 |
| vCores | 1, 2 | 2, 4, 8, 16, 32 |2, 4, 8, 16 |
| Minne per vCore | 1x | 2x Basic | 2 x generella |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 1 TB | 5 GB till 1 TB |
| Lagringstyp | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Databasen period för lagring av säkerhetskopior. | 7 - 35 dagar | 7 - 35 dagar | 7 - 35 dagar |

I följande tabell kan användas som utgångspunkt för att välja en prisnivå:

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Basic | Arbetsbelastningar kräver lätt beräkning och I/O-prestanda. Exempel på det är servrar som används för utveckling eller testning eller småskaliga program som används sällan. |
| Generellt syfte | De flesta företags arbetsbelastningar kräver beräkning och minne i balans med skalbart I/O-dataflöde. Exempel är servern som värd för webb- och mobilappar, och andra företagsprogram.|
| Minnesoptimerad | Högpresterande arbetsbelastningar som kräver InMemory-prestanda för snabbare transaktionsbearbetning och högre samtidighet. Exempel är server för att bearbeta data i realtid och högpresterande Transaktionsreplikering eller analytiska appar.|

När du har skapat en server, kan antalet vCores ändras uppåt eller nedåt inom några sekunder. Du kan också oberoende Justera mängden lagringsutrymme upp och säkerhetskopiering kvarhållningsperioden uppåt eller nedåt utan avbrott för programmet. Avsnittet skalning nedan för mer information.

## <a name="compute-generations-vcores-and-memory"></a>Beräkna generationer, vCores och minne

Beräkningsresurser tillhandahålls som vCores, som representerar den underliggande maskinvaran logiska Processorn. För närvarande erbjuds två beräkning generationer Gen 4 och 5 Gen som du kan välja från. Logiska generation 4-CPU:er baseras på Intel E5-2673 v3 (Haswell) 2,4 GHz-processorer. Logiska generation 5-CPU:er baseras på Intel E5-2673 v4 (Haswell) 2,3 GHz-processorer. Gen 4 och 5 Gen finns i följande regioner (”X” anger tillgängliga): 

| **Azure Region** | **Generation 4** | **Generation 5** |
|:---|:----------:|:--------------------:|
| Centrala USA |  | X |
| Östra USA | X | X |
| Östra USA 2 | X |  |
| Norra centrala USA | X |  |
| Södra centrala USA | X |  |
| Västra USA | X | X |
| Västra USA 2 |  | X |
| Centrala Kanada | X | X |
| Östra Kanada | X | X |
| Södra Brasilien | X |  |
| Norra Europa | X | X |
| Västra Europa | X | X |
| Storbritannien, västra |  | X |
| Storbritannien, södra |  | X |
| Östasien | X |  |
| Sydostasien | X |  |
| Östra Australien |  | X |
| Indien, centrala | X |  |
| Indien, västra | X |  |
| Östra Japan | X |  |
| Västra Japan | X |  |
| Sydkorea |  | X |

Beroende på prisnivå, är varje vCore utrustad med en viss mängd minne. Om du ökar eller minskar antalet vCores serverns minne ökar eller minskar proportionerligt. Generella nivån innehåller dubbla mängden minne per vCore jämfört med den grundläggande nivån. Den Minnesoptimerade nivån innehåller dubbla mängden minne jämfört med generella nivån.

## <a name="storage"></a>Lagring

Lagring som du etablerar är mängden lagringskapacitet som är tillgängliga för din Azure-databas för MySQL-servern. Lagringsutrymmet som används för databasfilerna, temporära filer, transaktionsloggar och MySQL-servern loggar. Den totala mängden lagringsutrymme som du etablerar definierar också den tillgängliga kapaciteten i i/o för servern:

|    | **Basic** | **Generella** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Azure Standard Storage | Azure Premium Storage | Azure Premium Storage |
| Lagringsstorlek | 5 GB till 1 TB | 5 GB till 1 TB | 5 GB till 1 TB |
| Öka lagringsstorlek | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS | 3 IOPS/GB<br/>Min 100 IOPS |

Ytterligare lagringskapacitet kan läggas till under och efter skapandet av servern. Den grundläggande nivån innehåller inte en IOPS-garanti. I den generella och Minnesoptimerade prisnivåer, skala IOPS med den etablerade lagringsstorleken i förhållandet 3:1.

Du kan övervaka dina i/o-användning i Azure-portalen eller med hjälp av Azure CLI-kommandona. Mätvärdena som är relevanta för att övervaka är [lagringsgräns, lagringsprocent, lagringsutrymme som används och IO-procent](concepts-monitoring.md).

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhetskopior av servern. Minsta kvarhållningsperioden för säkerhetskopiering är sju dagar. Du kan ange en kvarhållningsperiod på upp till 35 dagar. Kvarhållning kan justeras när som helst under livslängden för servern. Du kan välja mellan lokalt redundant och geo-redundant säkerhetskopieringar. GEO-redundant säkerhetskopior lagras också den [geo länkas region](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) av region skapas servern i. Detta ger en nivå av skydd vid en katastrof. Du kan också få möjlighet att återställa servern till några andra Azure-region där tjänsten är tillgänglig med geo-redundant säkerhetskopior. Observera att det inte går att ändra mellan de två säkerhetskopieringslagring alternativen när servern har skapats.

## <a name="scale-resources"></a>Skala resurser

När du har skapat din server, kan du ändra vCores, lagring och säkerhetskopiering kvarhållningsperiod oberoende av varandra. Du kan inte ändra prisnivån eller typen lagring för säkerhetskopiering när en server har skapats. vCores och säkerhetskopiering kvarhållningsperioden kan skalas upp eller ned. Lagringsstorleken kan bara ökas. Skalning av resurser kan göras antingen via portalen eller Azure CLI. Ett exempel för att skala med hjälp av CLI hittar [här](scripts/sample-scale-server.md).

När du ändrar antalet vCores, skapas en kopia av den ursprungliga servern för den nya beräknings-fördelningen. När den nya servern är igång, växlas anslutningar till den nya servern. Inga nya anslutningar kan upprättas under kort nu när datorn växlar till den nya servern, och alla ogenomförda transaktioner återställs. Det här fönstret varierar, men i de flesta fall är mindre än en minut.

Skala lagring och ändra säkerhetskopiering kvarhållningsperiod är true online åtgärder. Det finns ingen nedtid eller påverkan på ditt program. Som IOPS skala med storleken på lagringsplatsen etablerade öka du antalet IOPS som är tillgängliga till servern genom att skala upp lagring.

## <a name="pricing"></a>Prissättning

Granska tjänsten [sida med priser](https://azure.microsoft.com/pricing/details/mysql/) för den senaste information om priser. Att se vilka dina kostnader för önskad konfigurationshantering, den [Azure-portalen](https://portal.azure.com/#create/Microsoft.MySQLServer) visar månadskostnaden i den **prisnivå** fliken utifrån de alternativ som du har valt. Om du inte har en Azure-prenumeration kan du använda Azure prisnivå Kalkylatorn för att hämta ett beräknat pris. Besök den [Azure prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/) webbplats, klicka sedan på **lägga till objekt**, expandera den **databaser** kategori, och välj **Azure-databas för MySQL** du anpassar alternativen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en MySQL-server i portalen](howto-create-manage-server-portal.md)
- Lär dig hur du [övervaka och skala en Azure-databas för MySQL-server med Azure CLI](scripts/sample-scale-server.md)
- Lär dig mer om den [tjänsten begränsningar](concepts-limits.md)
