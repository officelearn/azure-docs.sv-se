---
title: Prisnivåer – Azure Database for MySQL
description: Lär dig mer om de olika pris nivåerna för Azure Database for MySQL inklusive Compute-generationer, lagrings typer, lagrings storlek, virtuella kärnor, minne och kvarhållning av säkerhets kopior.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/25/2020
ms.openlocfilehash: b9cf070e1431c04a7e899a3b8875c8db796608d3
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/08/2020
ms.locfileid: "86119896"
---
# <a name="azure-database-for-mysql-pricing-tiers"></a>Azure Database for MySQL pris nivåer

Du kan skapa en Azure Database for MySQL-server på en av tre olika pris nivåer: Basic, Generell användning och Minnesoptimerade. Pris nivåerna åtskiljs av mängden data bearbetning i virtuella kärnor som kan tillhandahållas, minne per vCore och lagrings teknik som används för att lagra data. Alla resurser har allokerats på MySQL-server nivå. En server kan ha en eller flera databaser.

| Attribut   | **Basic** | **Generell användning** | **Minnesoptimerade** |
|:---|:----------|:--------------------|:---------------------|
| Beräknings generation | Gen 4, gen 5 | Gen 4, gen 5 | Gen 5 |
| Virtuella kärnor | 1, 2 | 2, 4, 8, 16, 32, 64 |2, 4, 8, 16, 32 |
| Minne per vCore | 2 GB | 5 GB | 10 GB |
| Lagrings storlek | 5 GB till 1 TB | 5 GB till 16 TB | 5 GB till 16 TB |
| Kvarhållningsperiod för databas säkerhets kopior | 7 till 35 dagar | 7 till 35 dagar | 7 till 35 dagar |

Om du vill välja en pris nivå använder du följande tabell som utgångs punkt.

| Prisnivå | Målbelastningar |
|:-------------|:-----------------|
| Basic | Arbets belastningar som kräver ljus beräkning och I/O-prestanda. Exempel på detta är servrar som används för utveckling eller testning eller småskaliga program som inte används ofta. |
| Generell användning | De flesta företags arbets belastningar som kräver balanserade beräkning och minne med skalbart I/O-genomflöde. Exempel på det är servrar som är värdar för webb-och mobilappar och andra företags program.|
| Minnesoptimerad | Databas arbets belastningar med höga prestanda som kräver minnes prestanda för snabbare bearbetning av transaktioner och högre samtidighet. Exempel på detta är servrar för bearbetning av real tids data och högpresterande transaktionella eller analytiska appar.|

När du har skapat en server kan du ändra antalet virtuella kärnor, maskin varu generation och pris nivå (förutom till och från Basic) på några sekunder. Du kan också självständigt justera mängden lagrings utrymme och säkerhets kopierings perioden upp eller ned utan avbrott i programmet. Du kan inte ändra lagrings typen för säkerhets kopia när en server har skapats. Mer information finns i avsnittet [Scale Resources](#scale-resources) .

## <a name="compute-generations-and-vcores"></a>Beräkna generationer och virtuella kärnor

Beräknings resurser tillhandahålls som virtuella kärnor, som representerar den underliggande maskin varans logiska processor. Kina, östra 1, Kina, norra 1, US DoD, centrala och US DoD, östra använda generation 4 logiska processorer som baseras på Intel E5-2673 v3-processorer (Haswell) 2,4 GHz. Alla andra regioner använder generation 5 logiska processorer som baseras på Intel E5-2673 v4-processorer (Broadwell) 2,3 GHz.

## <a name="storage"></a>Storage

Lagrings utrymmet du tillhandahåller är mängden lagrings kapacitet som är tillgänglig för din Azure Database for MySQL-server. Lagrings utrymmet används för databasfilerna, temporära filer, transaktions loggar och MySQL-server loggarna. Den totala mängden lagrings utrymme som du tillhandahåller definierar också den I/O-kapacitet som är tillgänglig för servern.

| Storage-attribut   | Basic | Generellt syfte | Minnesoptimerad |
|:---|:----------|:--------------------|:---------------------|
| Lagringstyp | Basic Storage | Generell användning lagring | Generell användning lagring |
| Lagrings storlek | 5 GB till 1 TB | 5 GB till 16 TB | 5 GB till 16 TB |
| Öknings storlek för lagring | 1 GB | 1 GB | 1 GB |
| IOPS | Variabel |3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20 000 IOPS | 3 IOPS/GB<br/>Min 100 IOPS<br/>Max 20 000 IOPS |

> [!NOTE]
> Det finns stöd för lagring på upp till 16TB och 20 000 IOPS i följande regioner: östra USA, östra USA 2, centrala USA, västra USA, norra centrala USA, södra centrala USA, norra Europa, västra Europa, Storbritannien, södra, Storbritannien, västra Sydostasien, Asien, östra, Östra Japan, västra Japan, centrala Korea, södra Australien, östra Australien, västra USA 2 och västra centrala USA.
>
> Alla andra regioner stöder upp till 4 TB lagring och upp till 6000 IOPS.
>

Du kan lägga till ytterligare lagrings kapacitet under och efter att servern har skapats och göra det möjligt för systemet att växa lagring automatiskt baserat på lagrings förbrukningen för din arbets belastning. 

>[!NOTE]
> Lagringen kan bara skalas upp, inte nedåt.

Basic-nivån ger ingen IOPS-garanti. På den Generell användning och minnesoptimerade pris nivån, skalar IOPS med den tillhandahållna lagrings storleken i ett 3:1-förhållande.

Du kan övervaka i/O-förbrukningen i Azure Portal eller genom att använda Azure CLI-kommandon. De relevanta måtten för övervakning är [lagrings gränser, lagrings utrymme, lagring och i/o procent](concepts-monitoring.md).

### <a name="reaching-the-storage-limit"></a>Nått lagrings gränsen

Servrar med mindre än lika med 100 GB allokerat lagrings utrymme markeras som skrivskyddade om det lediga lagrings utrymmet är mindre än 5% av den allokerade lagrings storleken. Servrar med mer än 100 GB allokerat lagringsutrymme markeras bara som skrivskyddade när det lediga lagringsutrymmet är mindre än 5 GB.

Om du till exempel har allokerat 110 GB lagrings utrymme och den faktiska användningen går över 105 GB, är servern markerad som skrivskyddad. Alternativt, om du har allokerat 5 GB lagring, markeras servern som skrivskyddad när den kostnads fria lagrings platsen når mindre än 256 MB.

När tjänsten försöker göra så att servern blir skrivskyddad blockeras alla nya skrivtransaktionsbegäranden och befintliga aktiva transaktioner fortsätter att köras. När servern är i skrivskyddat läge misslyckas alla efterföljande skrivåtgärder och transaktioner. Läsfrågor fortsätter att fungera utan avbrott. När du har ökat lagringen är servern redo att acceptera skrivtransaktioner igen.

Vi rekommenderar att du aktiverar automatisk storleks ökning för lagring eller ställer in en avisering som meddelar dig när Server lagringen närmar sig tröskelvärdet, så att du kan undvika att komma in i skrivskyddat läge. Mer information finns i dokumentationen om [hur du konfigurerar en avisering](howto-alert-on-metric.md).

### <a name="storage-auto-grow"></a>Utöka lagring automatiskt

Med automatisk storleks ökning i arkivet förhindrar du att servern kör lagrings utrymme och blir skrivskyddad. Om lagrings funktionen är aktive rad utökas lagringen automatiskt utan att arbets belastningen påverkas. För servrar med mindre än lika med 100 GB allokerat lagrings utrymme ökas den etablerade lagrings storleken med 5 GB när det lediga lagrings utrymmet är lägre än 10% av det allokerade lagrings utrymmet. För servrar som har mer än 100 GB allokerat lagrings utrymme ökas den allokerade lagrings storleken med 5% när det lediga lagrings utrymmet är mindre än 10 GB av den allokerade lagrings storleken. De maximala lagrings gränserna som anges ovan gäller.

Om du till exempel har allokerat 1000 GB lagrings utrymme och den faktiska användningen går över 990 GB, ökar server lagrings storleken till 1050 GB. Alternativt, om du har etablerad 10 GB lagrings utrymme, ökar lagrings storleken till 15 GB om mindre än 1 GB lagrings utrymme är kostnads fritt.

Kom ihåg att lagringen bara kan skalas upp, inte nedåt.

## <a name="backup"></a>Backup

Tjänsten tar automatiskt säkerhets kopior av servern. Du kan välja en kvarhållningsperiod från mellan 7 och 35 dagar. Generell användning-och Minnesoptimerade servrar kan välja att ha Geo-redundant lagring för säkerhets kopiering. Läs mer om säkerhets kopieringar i [artikeln begrepp](concepts-backup.md).

## <a name="scale-resources"></a>Skala resurser

När du har skapat din server kan du oberoende ändra virtuella kärnor, maskin varu genereringen, pris nivån (förutom till och från Basic), mängden lagring och kvarhållningsperioden för säkerhets kopior. Du kan inte ändra lagrings typen för säkerhets kopia när en server har skapats. Antalet virtuella kärnor kan skalas upp eller ned. Kvarhållningsperioden för säkerhets kopior kan skalas upp eller ned från 7 till 35 dagar. Det går bara att öka lagrings storleken. Skalning av resurserna kan göras via portalen eller Azure CLI. Ett exempel på skalning med hjälp av Azure CLI finns i [övervaka och skala en Azure Database for MySQL server med hjälp av Azure CLI](scripts/sample-scale-server.md).

När du ändrar antalet virtuella kärnor, maskin varu genereringen eller pris nivån skapas en kopia av den ursprungliga servern med den nya beräknings allokeringen. När den nya servern är igång växlar anslutningarna över till den nya servern. Under tiden då systemet växlar över till den nya servern kan inga nya anslutningar upprättas, och transaktioner som inte allokerats återställs. Hur lång tid detta tar varierar, men i de flesta fall tar det mindre än en minut.

Skalning av lagring och ändring av kvarhållning av säkerhets kopior är true online-åtgärder. Det finns ingen nedtid och ditt program påverkas inte. Som IOPS-skala med storleken på den allokerade lagringen kan du öka tillgängligheten för IOPS för servern genom att skala upp lagringen.

## <a name="pricing"></a>Prissättning

Den senaste pris informationen finns på [sidan med pris](https://azure.microsoft.com/pricing/details/mysql/)information för tjänsten. Om du vill se kostnaden för den konfiguration du vill ha, visar [Azure Portal](https://portal.azure.com/#create/Microsoft.MySQLServer) månads kostnaden på fliken **pris nivå** baserat på de alternativ du väljer. Om du inte har någon Azure-prenumeration kan du använda pris Kalkylatorn för Azure för att få ett uppskattat pris. På webbplatsen för [Azures pris kalkylator](https://azure.microsoft.com/pricing/calculator/) väljer du **Lägg till objekt**, expanderar kategorin **databaser** och väljer **Azure Database for MySQL** för att anpassa alternativen.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en MySQL-server i portalen](howto-create-manage-server-portal.md).
- Läs mer om [tjänst begränsningar](concepts-limits.md).
- Lär dig hur du [skalar ut med Läs repliker](howto-read-replicas-portal.md).
