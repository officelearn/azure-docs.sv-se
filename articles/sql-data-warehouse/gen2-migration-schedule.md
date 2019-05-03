---
title: Migrera din befintliga Azure SQL Data Warehouse till Gen2 | Microsoft Docs
description: Anvisningar för att migrera ett befintligt informationslager Gen2 och migreringen schemalägga per region.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
manager: craigg
ms.assetid: 04b05dea-c066-44a0-9751-0774eb84c689
ms.service: sql-data-warehouse
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: a5d93a77652f540fde44b33963b13df04b45ecee
ms.sourcegitcommit: 60606c5e9a20b2906f6b6e3a3ddbcb6c826962d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2019
ms.locfileid: "64987948"
---
# <a name="upgrade-your-data-warehouse-to-gen2"></a>Uppgradera ditt informationslager till Gen2

Microsoft hjälper sänka på ingångsnivå kostnaden för att köra ett data warehouse.  Compute lägre nivåer kan hantera krävande frågor är nu tillgängliga för Azure SQL Data Warehouse. Läs hela tillkännagivandet [lägre compute nivå av support för Gen2](https://azure.microsoft.com/blog/azure-sql-data-warehouse-gen2-now-supports-lower-compute-tiers/). Nytt erbjudande är tillgängligt i de regioner som anges i tabellen nedan. För regioner som stöds, kan befintliga Gen1 informationslager uppgraderas till Gen2 via antingen:

- **Automatiska uppgraderingen:** Automatiska uppgraderingar starta inte så snart som tjänsten är tillgänglig i en region.  När automatiska uppgraderingar startar i en viss region, ska enskilda DW-uppgraderingar äga rum under ditt valda underhållsschema.
- [**Lokal uppgradera till Gen2:**](#self-upgrade-to-gen2) Du kan styra när du ska uppgradera genom att göra en lokal uppgradera till Gen2. Om din region inte stöds ännu, kan du återställa från en återställningspunkt direkt till en Gen2-instans i en region som stöds.

## <a name="automated-schedule-and-region-availability-table"></a>Automatiserad schema och tabellen för Region tillgänglighet

I följande tabell sammanfattas per region när lägre Gen2-Beräkningsnivån är tillgänglig och när automatiska uppgraderingar startar. Datumen kan ändras. Kontrollera tillbaka när din region blir tillgänglig.

\* Anger ett visst schema för regionen är inte tillgänglig för tillfället.

| **Region** | **Lägre Gen2 som är tillgängliga** | **Automatiska uppgraderingar börjar** |
|:--- |:--- |:--- |
| Östra Australien |Tillgängligt |Den 1 juni 2019 |
| Sydöstra Australien |Tillgängligt |Den 1 maj 2019 |
| Södra Brasilien |Tillgängligt |Den 1 juni 2019 |
| Centrala Kanada |Tillgängligt |Den 1 juni 2019 |
| Östra Kanada |\* |\* |
| Centrala USA |Tillgängligt |Den 1 juni 2019 |
| Östra Kina |\* |\* |
| Kina, östra 2 |\* |\* |
| Norra Kina |\* |\* |
| Kina, norra 2 |\* |\* |
| Östasien |Tillgängligt |Den 1 juni 2019 |
| Östra USA |Tillgängligt |Den 1 juni 2019 |
| USA, östra 2 |Tillgängligt |Den 1 juni 2019 |
| Frankrike, centrala |\* |Den 1 juni 2019 |
| Centrala Tyskland |\* |\* |
| Tyskland, västra centrala |Den 1 september 2019|Den 2 januari 2020 |
| Centrala Indien |Tillgängligt |Den 1 juni 2019 |
| Södra Indien |Tillgängligt |Den 1 juni 2019 |
| Östra Japan |Tillgängligt |Den 1 juni 2019 |
| Västra Japan |Tillgängligt |Den 1 maj 2019 |
| Sydkorea, centrala |Tillgängligt |Den 1 juni 2019 |
| Sydkorea, södra |Tillgängligt |Den 1 maj 2019 |
| Norra centrala USA |Tillgängligt |Den 1 maj 2019 |
| Norra Europa |Tillgängligt |Den 1 juni 2019 |
| Södra centrala USA |Tillgängligt |Den 1 juni 2019 |
| Sydostasien |Tillgängligt |Den 1 juni 2019 |
| Storbritannien, södra |Tillgänglig, 2019 |Den 1 juni 2019 |
| Storbritannien, västra |\*|\* |
| Västra centrala USA |2 september 2019 |Den 2 januari 2020|
| Västra Europa |Tillgängligt |Den 1 juni 2019 |
| Västra USA |Tillgängligt |Den 1 juni 2019 |
| Västra USA 2 |Tillgängligt |Den 1 juni 2019 |

## <a name="automatic-upgrade-process"></a>Automatisk uppgraderingsprocessen

Baserat på Tillgänglighetsdiagrammet ovan kan kommer vi att schemalägga automatiska uppgraderingar för Gen1-instanser. För att undvika oväntade avbrott på tillgängligheten för datalagret, schemaläggs automatiserade uppgraderingar under ditt underhållsschema. Möjligheten att skapa en ny instans av Gen1 inaktiveras i regioner som håller på att automatiskt uppgraderas till Gen2. Mer information om scheman finns [visa ett underhållsschema](viewing-maintenance-schedule.md)

Uppgraderingen kommer att omfatta en kort minskning i anslutningen (cirka 5 minuter) som vi starta om ditt informationslager.  När ditt informationslager har startats om, kommer det att helt tillgängliga för användning. Försämrade prestanda kan dock uppstå medan uppgraderingen fortsätter att uppgradera datafiler i bakgrunden. Den totala tiden för försämrade prestanda varierar beroende på storleken på dina filer.

Du kan också påskynda uppgraderingsprocessen för data-filen genom att köra [Alter Index rebuild](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller med hjälp av en större SLO och resurs klass efter omstarten.

> [!NOTE]
> ALTER Index rebuild är en offline-åtgärd och tabellerna är inte tillgängliga förrän återskapandet har slutförts.

## <a name="self-upgrade-to-gen2"></a>Lokal uppgradera till Gen2

Du kan välja att uppgradera själv genom att följa dessa steg på ett befintligt Gen1-informationslager. Om du väljer att själva uppgradera måste du slutföra den innan den automatiska uppgraderingen påbörjas i din region. På så sätt kan du undvika att risk för automatiska uppgraderingar som orsakar en konflikt.

Det finns två alternativ när du utför en lokal uppgradering.  Du kan antingen uppgradera din aktuella data warehouse på plats eller du kan återställa ett informationslager för Gen1 till en Gen2-instans.

- [Uppgradera på plats](upgrade-to-latest-generation.md) – det här alternativet kommer att uppgradera befintliga Gen1 informationslagret till Gen2. Uppgraderingen kommer att omfatta en kort minskning i anslutningen (cirka 5 minuter) som vi starta om ditt informationslager.  När ditt informationslager har startats om, kommer det att helt tillgängliga för användning. Om det uppstår problem under uppgraderingen kan du öppna en [supportförfrågan](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-get-started-create-support-ticket) och referera till ”Gen2 uppgraderingen” som möjlig orsak.
- [Uppgradera från återställningspunkt](sql-data-warehouse-restore.md) – skapa en användardefinierad återställningspunkt i aktuella Gen1 data warehouse och Återställ sedan direkt till en Gen2-instans. Befintliga Gen1 datalagret förblir på plats. När återställningen har slutförts, blir informationslager Gen2 fullt tillgänglig för användning.  När du har kört alla processer för testning och validering på den återställda Gen2-instansen, kan den ursprungliga Gen1 instansen tas bort.

   - Steg 1: Från Azure-portalen [skapa en återställningspunkt för en användardefinierad](sql-data-warehouse-restore.md#create-a-user-defined-restore-point-using-the-azure-portal).
   - Steg 2: När du återställer från en användardefinierad återställningspunkt genom att ange ”prestanda nivån” Gen2 önskad nivå.

Medan uppgraderingen fortsätter att uppgradera datafiler i bakgrunden kan det uppstå en tidsperiod försämrade prestanda. Den totala tiden för försämrade prestanda varierar beroende på storleken på dina filer.

Om du vill påskynda bakgrundsprocess för migrering av data, kan du omedelbart tvinga dataförflyttning genom att köra [Alter Index rebuild](sql-data-warehouse-tables-index.md) på alla primära columnstore-tabeller du skulle köras mot en större SLO och resurs-klass.

> [!NOTE]
> ALTER Index rebuild är en offline-åtgärd och tabellerna är inte tillgängliga förrän återskapandet har slutförts.

Om du får problem med ditt data warehouse, skapa en [supportförfrågan](sql-data-warehouse-get-started-create-support-ticket.md) och referera till ”Gen2 uppgraderingen” som möjlig orsak.

Mer information finns i [uppgradera till Gen2](upgrade-to-latest-generation.md).

## <a name="migration-frequently-asked-questions"></a>Vanliga och frågor svar om migrering

**F: Kostar Gen2 identiskt Gen1?**

- S: Ja.

**F: Hur påverkar uppgraderingarna Mina automatiserade skript?**

- S: Alla automationsskript som refererar till ett servicenivåmål ska ändras så att de motsvarar Gen2-motsvarighet.  Se information [här](upgrade-to-latest-generation.md#sign-in-to-the-azure-portal).

**F: Hur lång tid en lokal uppgradera normalt tar?**

- S: Du kan uppgradera på plats eller uppgradera från en återställningspunkt.  
   - Uppgradering på plats innebär ditt informationslager att pausa och återuppta under ett ögonblick.  En bakgrundsprocess fortsätter medan datalagret är online.  
   - Det tar längre tid om du uppgraderar via en återställningspunkt eftersom uppgraderingen går igenom processen fullständig återställning.

**F: Hur lång tid tar automatiskt uppgraderingen?**

- S: Den faktiska stilleståndstiden för uppgraderingen är bara den tid det tar att pausa och återuppta tjänsten, som är mellan 5-10 minuter. Efter det kort driftstopp körs bakgrunden en lagringsmigrering. Hur lång tid för bakgrundsprocessen beror på storleken på ditt informationslager.

**F: När den här automatiska uppgraderingen sker?**

- S: Under ditt underhållsschema. Utnyttja ditt valda underhållsschema minimerar avbrott för din verksamhet.

**F: Vad gör jag om min bakgrund uppgraderingsprocessen verkar ha fastnat?**

 - S: Starta en reindex av Columnstore-tabeller. Observera att omindexering i tabellen kommer vara offline under den här åtgärden.

**F: Vad händer om Gen2 har inte den servicenivåmål jag har på Gen1?**
- S: Om du kör en DW600 eller DW1200 på Gen1, är det bäst att använda DW500c eller DW1000c respektive eftersom Gen2 ger mer minne, resurser och högre prestanda än Gen1.

**F: Kan jag inaktivera geo-säkerhetskopiering?**
- S: Nej. GEO-säkerhetskopiering är en företagsfunktion att bevara dina data warehouse tillgänglighet om en region blir otillgänglig. Öppna en [supportförfrågan](sql-data-warehouse-get-started-create-support-ticket.md) om du har fler frågor.

**F: Finns det någon skillnad i T-SQL-syntaxen mellan Gen1 och Gen2?**

- S: Det finns ingen ändring i syntaxen T-SQL-språk från Gen1 till Gen2.

**F: Stöder Gen2 Underhåll Windows?**

- S: Ja.

**F: Kommer jag att kunna skapa en ny instans för Gen1 när du har uppgraderat min region?**

- S: Nej. När du har uppgraderat en region kommer skapandet av nya Gen1 instanser att inaktiveras.

## <a name="next-steps"></a>Nästa steg

- [Uppgraderingssteg](upgrade-to-latest-generation.md)
- [Underhållsperioder](maintenance-scheduling.md)
- [Övervakare för hälsa för resursen](https://docs.microsoft.com/azure/service-health/resource-health-overview)
- [Granska innan du påbörjar en migrering](upgrade-to-latest-generation.md#before-you-begin)
- [Uppgradera på plats och uppgradera från en återställningspunkt](upgrade-to-latest-generation.md)
- [Skapa en användardefinierad återställningspunkt](sql-data-warehouse-restore.md#restore-through-the-azure-portal)
- [Lär dig hur du återställer till Gen2](sql-data-warehouse-restore.md#restore-an-active-or-paused-database-using-the-azure-portal)
- [Öppna en supportbegäran för SQL Data Warehouse](https://go.microsoft.com/fwlink/?linkid=857950)
