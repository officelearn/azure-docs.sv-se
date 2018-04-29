---
title: Automatisk, geo-redundant säkerhetskopieringar i Azure SQL Database | Microsoft Docs
description: SQL-databasen och skapar en lokal databassäkerhetskopia med några minuters mellanrum automatiskt och använder Azure geo-redundant lagring med läsbehörighet för geo-redundans.
services: sql-database
author: anosov1960
manager: craigg
ms.service: sql-database
ms.custom: business continuity
ms.topic: article
ms.workload: Active
ms.date: 04/04/2018
ms.author: sashan
ms.reviewer: carlrab
ms.openlocfilehash: 37bbbf8ea5a5d8439b300d0740e4f1a048e98e91
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2018
---
# <a name="learn-about-automatic-sql-database-backups"></a>Lär dig mer om automatisk säkerhetskopiering i SQL-databas

SQL-databas skapas säkerhetskopiorna av databasen automatiskt och använder Azure-geo-redundant lagring med läsbehörighet (RA-GRS) för att ge geo-redundans. Dessa säkerhetskopior skapas automatiskt och utan extra kostnad. Du behöver inte göra något för att göra dem inträffa. Säkerhetskopiorna av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Du kan konfigurera en princip för långsiktig lagring av säkerhetskopior om du vill behålla säkerhetskopior i din egen lagringsbehållaren. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="what-is-a-sql-database-backup"></a>Vad är en säkerhetskopia av SQL-databasen?

SQL-databasen använder SQL Server-teknik för att skapa [fullständig](https://msdn.microsoft.com/library/ms186289.aspx), [differentiella](https://msdn.microsoft.com/library/ms175526.aspx), och [transaktionsloggen](https://msdn.microsoft.com/library/ms191429.aspx) säkerhetskopior i syfte att tidpunkts-återställning (PITR). Säkerhetskopieringarna av transaktionsloggen inträffa normalt var 5-10 minut, ofta baserat på prestanda och mycket Databasaktivitet. Säkerhetskopiering av transaktionsloggar, med fullständig och differentiell säkerhetskopiering kan du återställa en databas till en specifik i tidpunkt till samma server som är värd för databasen. När du återställer en databas räknat tjänsten ut där full, differentiella och transaktionen loggfiler säkerhetskopior som ska återställas.


Du kan använda dessa säkerhetskopieringar till:

* Återställa en databas till en i tidpunkt inom kvarhållningsperioden. Den här åtgärden skapar en ny databas på samma server som den ursprungliga databasen.
* Återställa en borttagen databas till den tid som det har tagits bort eller inom kvarhållningsperioden. Borttagen databas kan bara återställas på samma server som var den ursprungliga databasen har skapats.
* Återställa en databas till en annan geografisk region. På så sätt kan du återställa en geografisk katastrofåterställning när du inte kommer åt din server och databas. En ny databas skapas i en befintlig server som helst i världen. 
* Återställa en databas från en specifik långsiktig säkerhetskopiering om databasen har konfigurerats med en långsiktig bevarandeprincip. På så sätt kan du återställa en gammal version av databasen att uppfylla en begäran om kompatibilitet eller kör en gammal version av programmet. Se [långsiktig kvarhållning](sql-database-long-term-retention.md).
* Om du vill utföra en återställning, se [återställa databasen från säkerhetskopior](sql-database-recovery-using-backups.md).

> [!NOTE]
> I Azure storage termen *replikering* refererar till kopiera filer från en plats till en annan. SQL- *databasreplikering* refererar till att hålla till flera sekundära databaser som synkroniseras med en primär databas. 
> 

## <a name="how-often-do-backups-happen"></a>Hur ofta hända säkerhetskopieringar?
Fullständiga databassäkerhetskopieringar inträffa varje vecka, differentiella säkerhetskopieringar sker vanligtvis var några timmar och transaktionsloggen säkerhetskopieringar göras normalt var 5-10: e minut. Den första kompletta säkerhetskopian kommer omedelbart när en databas har skapats. Det slutförs vanligtvis inom 30 minuter, men det kan ta längre tid när databasen är av betydande storlek. Den första säkerhetskopian kan till exempel ta längre tid på en återställd databas eller en databaskopia. Efter den första kompletta säkerhetskopian alla ytterligare säkerhetskopior schemaläggs automatiskt och hanteras tyst i bakgrunden. Exakt tidtagningen av alla databassäkerhetskopieringar bestäms av SQL Database-tjänsten som det. balanserar den allmänna belastningen på systemet. 

Backup storage geo-replikering inträffar baserat på schemat för Azure Storage-replikering.

## <a name="how-long-do-you-keep-my-backups"></a>Hur länge håller du Mina säkerhetskopior?
Varje säkerhetskopiering i SQL-databas har en kvarhållningsperiod som baseras på tjänstnivån för databasen och skiljer sig mellan den [DTU-baserade inköpsmodell](sql-database-service-tiers-dtu.md) och [vCore-baserade inköpsmodell (förhandsgranskning)](sql-database-service-tiers-vcore.md). 


### <a name="database-retention-for-dtu-based-purchasing-model"></a>Databasen kvarhållning för DTU-baserade inköpsmodell
Kvarhållningsperiod för en databas i DTU-baserade inköpsmodell beror på tjänstnivån. Kvarhållningsperiod för en databas på den:

* Grundläggande tjänstnivå är 7 dagar.
* Standard-tjänstnivå är 35 dagar.
* Premiumnivån är 35 dagar.
* Allmänna nivån kan konfigureras med 35 dagar maximala (7 dagar som standard) *
* Kritiska affärsnivå (förhandsversion) kan konfigureras med 35 dagar maximala (7 dagar som standard) *

\* Under förhandsgranskningen gör säkerhetskopior kvarhållningsperioden kan inte konfigureras och löses till 7 dagar.

Om du konverterar en databas med längre säkerhetskopior bevaras till en databas med kortare bevaras inte längre alla befintliga säkerhetskopior som är äldre än kvarhållningsperioden för mål-nivå.

Om du uppgraderar en databas med en kortare period till en databas med en längre period, håller SQL Database befintliga säkerhetskopior tills längre kvarhållningsperioden har uppnåtts. 

Om du tar bort en-databas håller säkerhetskopieringar på samma sätt som för en onlinedatabas SQL-databas. Anta att du tar bort en grundläggande databas som har en kvarhållningsperiod på sju dagar. En säkerhetskopia som är fyra dagar gamla sparas för tre dagar.

> [!IMPORTANT]
> Om du tar bort Azure SQL-server som är värd för SQL-databaser, tas även bort alla databaser som hör till servern och kan inte återställas. Du kan inte återställa en server som har tagits bort.

### <a name="database-retention-for-the-vcore-based-purchasing-model-preview"></a>Databasen kvarhållning för vCore-baserade inköpsmodell (förhandsgranskning)

Lagring för säkerhetskopiering av databaser har allokerats för att stödja punkten i tiden återställa (PITR) och lång sikt Kvarhållning (LTR) funktioner i SQL-databas. Denna lagring är allokerade separat för varje databas och debiteras som två separata per databas avgifter. 

- **PITR**: individuell databas säkerhetskopiorna kopieras till RA-GRS lagring är automatiskt. Lagringsutrymme ökar storleken dynamiskt när nya säkerhetskopior skapas.  Lagringsutrymmet används av veckovisa, fullständiga säkerhetskopior, dagliga differentiella säkerhetskopior och säkerhetskopior av transaktionsloggar var femte minut. Användningen av lagringsutrymme beror på frekvensen för ändring av databasen och kvarhållningsperioden. Du kan konfigurera en separat kvarhållningsperiod för varje databas mellan 7 och 35 dagar. En minsta lagringsutrymme mängd lika med 1 x datastorleken tillhandahålls utan extra kostnad. För de flesta databaser är beloppet tillräckligt för att lagra 7 dagar för säkerhetskopior. Mer information finns i [Point-in-time-återställning](sql-database-recovery-using-backups.md#point-in-time-restore)
- **LTR**: SQL-databas kan konfigurera långsiktig kvarhållning av fullständiga säkerhetskopieringar för upp till 10 år. Om LTR principen är aktiverad kommer de här säkerhetskopior lagras i RA-GRS lagring automatiskt, men du kan styra hur ofta säkerhetskopiorna kopieras. För att uppfylla olika krav kan välja du olika kvarhållningsperioder för säkerhetskopiering varje vecka, månad och år. Den här konfigurationen definierar hur mycket lagringsutrymme som ska användas för LTR säkerhetskopieringar. Du kan använda LTR prisnivå Kalkylatorn för att beräkna kostnaden för LTR lagring. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

## <a name="how-to-extend-the-backup-retention-period"></a>Hur du utökar säkerhetskopiering loggperioden?

Om ditt program kräver att säkerhetskopiorna är tillgänglig under längre tid än den maximala PITR-kvarhållningsperioden för säkerhetskopiering, kan du konfigurera en långsiktig säkerhetskopiering bevarandeprincip för enskilda databaser (LTR princip). På så sätt kan du utöka inbyggda it kvarhållningsperioden från maximala 35 dagar till upp till 10 år. Mer information finns i avsnittet om [långsiktig kvarhållning](sql-database-long-term-retention.md).

När du lägger till LTR principen till en databas med Azure-portalen eller API kopieras veckovisa fullständiga databassäkerhetskopieringar automatiskt till en behållare för lagring av separata RA-GRS för långsiktig Kvarhållning (LTR lagring). Om databasen är krypterad med TDE krypteras automatiskt säkerhetskopior i vila. Säkerhetskopiorna har upphört att gälla, baserat på deras tidsstämpel och LTR principen tas bort automatiskt i SQL-databas. När principen har konfigurerats, behöver du inte hantera schemat för säkerhetskopiering eller bekymra dig om rensning av de gamla filerna. Du kan använda Azure-portalen eller PowerShell för att visa, återställa eller ta bort dessa säkerhetskopior.

## <a name="are-backups-encrypted"></a>Krypteras säkerhetskopieringar

När TDE är aktiverat för en Azure SQL database, är säkerhetskopior också krypterade. Alla nya Azure SQL-databaser har konfigurerats med TDE aktiverad som standard. Mer information om TDE finns [Transparent datakryptering med Azure SQL Database](/sql/relational-databases/security/encryption/transparent-data-encryption-azure-sql).

## <a name="are-the-automatic-backups-compliant-with-gdpr"></a>Följer automatiska säkerhetskopieringar BNPR?
Om säkerhetskopian innehåller personliga data, vilket är föremål allmänna Data Protection förordning (BNPR) krävs att vidta åtgärder för ökad säkerhet för att skydda data från obehörig åtkomst. För att följa BNPR behöver du ett sätt att hantera databegäranden från en dataägare utan att behöva komma åt säkerhetskopieringar.  För kortsiktig säkerhetskopiering till en lösning kan vara att förkorta säkerhetskopieringen fönstret till under 30 dagar, vilket är tid som tillåts för att slutföra åtkomstbegäranden data.  Om längre sikt säkerhetskopieringar krävs, rekommenderas att lagra endast ”sysselsättningsuppgifter” data i säkerhetskopieringar. Till exempel om information om en person behöver tas bort eller uppdatera kräver det inte ta bort eller uppdatera befintliga säkerhetskopior. Du hittar mer information om metodtips för BNPR i [datastyrning BNPR kompatibilitet](https://info.microsoft.com/DataGovernanceforGDPRCompliancePrinciplesProcessesandPractices-Registration.html).

## <a name="next-steps"></a>Nästa steg

- Säkerhetskopiorna av databasen är en viktig del av alla disaster recovery strategi för affärskontinuitet och eftersom de skyddar dina data från oavsiktliga skador eller tas bort. Mer information om andra Azure SQL Database business continuity lösningarna, se [översikt över verksamhetskontinuitet](sql-database-business-continuity.md).
- Om du vill återställa till en tidpunkt med hjälp av Azure portal finns [återställa databasen till en tidpunkt med hjälp av Azure portal](sql-database-recovery-using-backups.md).
- Om du vill återställa till en tidpunkt med hjälp av PowerShell Se [återställa databasen till en tidpunkt med hjälp av PowerShell](scripts/sql-database-restore-database-powershell.md).
- Konfigurera, hantera och återställa från långsiktig lagring av automatiserad säkerhetskopieringar i ett Azure Recovery Services-valv med Azure-portalen, se [hantera långsiktig lagring av säkerhetskopior med Azure-portalen](sql-database-long-term-backup-retention-configure.md).
- Konfigurera, hantera och återställa från långsiktig lagring av automatiserad säkerhetskopieringar i ett Azure Recovery Services-valv med hjälp av PowerShell, se [hantera långsiktig lagring av säkerhetskopior med hjälp av PowerShell](sql-database-long-term-backup-retention-configure.md).
