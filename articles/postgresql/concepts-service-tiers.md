---
title: "Prisnivåer i Azure PostgreSQL-databas"
description: "Prisnivåer i Azure PostgreSQL-databas"
services: postgresql
author: kamathsun
ms.author: sukamat
manager: jhubbard
editor: jasonwhowell
ms.custom: mvc
ms.service: postgresql
ms.topic: article
ms.date: 11/03/2017
ms.openlocfilehash: 2c0ed6b58fe3e354da3cf58cd0c504d72bb0f421
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2017
---
# <a name="azure-database-for-postgresql-options-and-performance-understand-whats-available-in-each-pricing-tier"></a>Azure-databas för PostgreSQL-alternativ och prestanda: förstå vad som är tillgängliga i varje prisnivå
När du skapar en Azure-databas för PostgreSQL-servern, besluta om tre huvudsakliga alternativ för att konfigurera de resurser som allokerats för servern. Dessa alternativ kan påverka prestanda och skalning av servern.
- Prisnivå
- Compute-enheter
- Storage (GB)

Varje prisnivå har flera olika prestandanivåer (Compute-enheter) för att välja mellan, beroende på dina arbetsbelastningar krav. Högre prestandanivåer ange ytterligare resurser för din server som utformats för att ge högre genomströmning. Du kan ändra serverns prestanda nivån i en prisnivå praktiskt taget inga driftavbrott.

> [!IMPORTANT]
> Tjänsten är tillgänglig som förhandsversion, är det inte en garanterad serviceavtalet (SLA).

Du kan ha en eller flera databaser i en Azure-databas för PostgreSQL-servern. Du kan välja om du vill skapa en enskild databas per server databasen använda alla serverresurserna eller skapa flera databaser att dela serverresurser. 

## <a name="choose-a-pricing-tier"></a>Välj en prisnivå
Under förhandsgranskning, Azure-databas för PostgreSQL erbjuder två prisnivåer: Basic och Standard. Premium-nivån är inte tillgänglig ännu, men blir snart tillgänglig. 

Följande tabell innehåller exempel på prisnivåer som bäst passar för olika programbelasningar.

| Prisnivå | Målbelastningar |
| :----------- | :----------------|
| Basic | Bäst för små arbetsbelastningar som kräver skalbara beräkning och lagring utan IOPS garantera. Exempel är servrar som används för utveckling och testning eller småskaliga program som inte används. |
| Standard | Gå till alternativet för molnet program som behöver IOPS garantera med hög genomströmning. Exempel webb- eller analytiska applikationer. |
| Premium | Bäst för arbetsbelastningar som behöver låg latens för transaktioner och IO. Ger bästa stöd för många samtidiga användare. Gäller för databaser som stöder verksamhetskritiska program.<br />Premium-prisnivån är inte tillgängliga i förhandsversionen. |

Om du vill välja en prisnivå, först starta genom att fastställa om din arbetsbelastning behöver en IOPS-garanti. I så fall använder du Standard som prisnivå.

| **Priser för nivån funktioner** | **Basic** | **Standard** |
| :------------------------ | :-------- | :----------- |
| Maximal beräknings-enheter | 100 | 800 | 
| Maximalt totalt lagringsutrymme | 1 TB | 1 TB | 
| Lagring IOPS garanti | Saknas | Ja | 
| Maximalt lagringsutrymme IOPS | Saknas | 3 000 | 
| Databasen period för lagring av säkerhetskopior. | 7 dagar | 35 dagar | 

Du kan inte ändra prisnivån när servern har skapats under tidsramen för förhandsgranskning. I framtiden, ska det vara möjligt att uppgradera eller nedgradera en server från en prisnivå till ett annat skikt.

## <a name="understand-the-price"></a>Förstå priset
När du skapar en ny Azure-databas för PostgreSQL inuti den [Azure-portalen](https://portal.azure.com/#create/Microsoft.PostgreSQLServer), klicka på den **prisnivå** sida och månadskostnaden visas baserat på de alternativ som du har valt. Om du inte har en Azure-prenumeration kan du använda Azure prisnivå Kalkylatorn för att få ett beräknat pris. Besök den [Azure prisnivå Kalkylatorn](https://azure.microsoft.com/pricing/calculator/) webbplats, klicka sedan på **lägga till objekt**, expandera den **databaser** kategori, och välj **Azure-databas för PostgreSQL**  du anpassar alternativen.

## <a name="choose-a-performance-level-compute-units"></a>Välj prestandanivå (Compute-enheter)
När du har konstaterat prisnivån för din Azure-databas för PostgreSQL-server, är du redo att fastställa prestandanivå genom att välja antalet Compute enheter behövs. En bra utgångspunkt är 200 eller 400 Compute-enheter för program som kräver högre samtidig användning för webb- eller analytiska arbetsbelastningar och justera inkrementellt efter behov. 

Beräkna enheter är ett mått på CPU bearbetning dataflödet som garanterat ska vara tillgängliga för en Azure-databas för PostgreSQL-servern. En Compute-enhet är en blandning av processor-och minnesresurser.  Mer information finns i [förklarar Compute enheter](concepts-compute-unit-and-storage.md).

### <a name="basic-pricing-tier-performance-levels"></a>Grundläggande prisnivå nivå prestandanivåer:

| **Prestandanivå** | **50** | **100** |
| :-------------------- | :----- | :------ |
| Max beräknings-enheter | 50 | 100 |
| Inkluderade lagringsstorlek | 50 GB | 50 GB |
| Maxstorlek för storage server\* | 1 TB | 1 TB |

### <a name="standard-pricing-tier-performance-levels"></a>Standard prisnivå nivå prestandanivåer:

| **Prestandanivå** | **100** | **200** | **400** | **800** |
| :-------------------- | :------ | :------ | :------ | :------ |
| Max beräknings-enheter | 100 | 200 | 400 | 800 |
| Inkluderade lagringsstorlek och etablerade IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS | 125 GB<br/> 375 IOPS |
| Maxstorlek för storage server\* | 1 TB | 1 TB | 1 TB | 1 TB |
| Konfigurera max servern IOPS | 3 000 IOPS | 3 000 IOPS | 3 000 IOPS | 3 000 IOPS |
| Konfigurera max servern IOPS per GB | Fast 3 IOPS per GB | Fast 3 IOPS per GB | Fast 3 IOPS per GB | Fast 3 IOPS per GB |

\*Maxstorlek server lagring refererar till etablerade lagringsstorleken för servern.

## <a name="storage"></a>Lagring 
Lagringskonfigurationen definierar mängden lagringskapacitet till en Azure-databas för PostgreSQL-servern. Det lagringsutrymme som används av tjänsten innehåller databasfiler och transaktionsloggar PostgreSQL i serverloggen. Överväg storleken på lagring som krävs för att vara värd för dina databaser och prestandakrav (IOPS) när du väljer lagringskonfigurationen.

Vissa lagringskapacitet ingår minst med varje prisnivå som anges i tabellen ovan som ”ingår lagringsstorlek”. Ytterligare lagringskapacitet kan läggas till när servern har skapats i steg om 125 GB, upp till maximalt tillåtna lagringsutrymme. Ytterligare lagringskapaciteten kan konfigureras oberoende av konfigurationen Compute-enheter. Kursen ändras baserat på hur mycket lagringsutrymme som valts.

IOPS-konfigurationen i varje prestandanivå relaterar till prisnivån och lagringsstorlek valt. Grundläggande nivån innehåller inte en IOPS-garanti. Inom Standardprisnivån, skala IOPS proportionerligt till maximala lagringsstorleken i en fast 3:1-förhållande. Inkluderade lagring av 125 GB garantier för 375 etablerats IOPS, var och en med en i/o-storlek på upp till 256 KB. Du kan välja ytterligare lagringsutrymme till 1 TB maximalt att garantera 3 000 etablerade IOPS.

Övervaka diagrammet mätvärden i Azure-portalen eller skriva Azure CLI-kommandona för att mäta användningen av lagringsutrymme och IOPS. Relevanta är övervaka lagringsgräns, lagringsprocent, lagringsutrymme som används och IO-procent.

>[!IMPORTANT]
> Välj mängden lagringsutrymme som vid den tidpunkt då servern skapas i förhandsgranskningen. Ändrar lagringsstorlek på på en befintlig server stöds inte ännu. 

## <a name="scaling-a-server-up-or-down"></a>Skala en server uppåt eller nedåt
Du välja först vilken prisnivå tjänstnivå och prestandanivå när du skapar din Azure-databas för PostgreSQL. Senare kan du skala beräknings-enheter uppåt eller nedåt dynamiskt, inom intervallet för samma prisnivå. Dra Compute-enheter på serverns priser nivå sida i Azure-portalen eller skript genom att följa det här exemplet: [Övervakare och skala en enskild PostgreSQL-server med hjälp av Azure CLI](scripts/sample-scale-server-up-or-down.md)

Skalning Compute-enheter sker oberoende av den maximala lagringsstorleken som du har valt.

I bakgrunden, ändra prestandanivåerna för en databas skapas en kopia av den ursprungliga servern på prestandanivån och sedan växlar anslutningar till den kopierade servern. Inga data går förlorade under den här processen. Under kort nu när systemet växlar till den nya kopian av servern inaktiveras anslutningar till databasen, så vissa transaktioner som rör sig kan återställas. Det här fönstret varierar, men i genomsnitt är än 4 sekunder, och i mer än 99% fall är mindre än 30 sekunder. Om många transaktioner sker just då anslutningarna är inaktiverade kan det ta lite längre tid.

Varaktighet för hela skalan processen är beroende av både storlek och prisnivån på servern före och efter ändringen. Till exempel bör en server som ändrar Compute enheter inom Standard prisnivån slutföras inom några minuter. Nya egenskaper för servern börjar inte gälla förrän ändringarna är klara.

## <a name="next-steps"></a>Nästa steg
- Mer information om Compute enheter finns i [förklarar Compute-enheter](concepts-compute-unit-and-storage.md)
- Lär dig hur du [Övervakare och skala en enskild PostgreSQL-server med hjälp av Azure CLI](scripts/sample-scale-server-up-or-down.md)
