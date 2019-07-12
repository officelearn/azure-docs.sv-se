---
title: SQL Database managed instance vanliga frågor och svar | Microsoft Docs
description: SQL Database-hanterad instans vanliga frågor (och svar FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 07/08/2019
ms.openlocfilehash: c3a070eb7e1435055b47b39985cf8cb0b182a514
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798071"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database-hanterad instans vanliga frågor (och svar FAQ)

Den här artikeln innehåller många av de vanligaste frågorna om [SQL Database-hanterad instans](sql-database-managed-instance.md).

## <a name="where-can-i-find-a-list-of-features-that-are-supported-on-managed-instance"></a>Var hittar jag en lista över funktioner som stöds på hanterad instans?

En lista över funktioner som stöds i hanterade instansen finns i [Azure SQL Database jämfört med SQL Server](sql-database-features.md).

Skillnader i syntaxen och beteende mellan Azure SQL Database-hanterad instans och en lokal SQL Server, se [T-SQL-skillnader från SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="where-can-i-find-technical-characteristics-and-resource-limits-for-managed-instance"></a>Var hittar jag teknisk egenskaper och resursgränser för den hanterade instansen?

Egenskaper för generering av tillgänglig maskinvara, se [tekniska skillnader i maskinvarugenerationer](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
Tillgängliga tjänstnivåer och deras egenskaper finns i [tekniska skillnader mellan tjänstnivåer](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="where-can-i-find-known-issues-and-bugs"></a>Var hittar jag kända problem och buggar?

Buggar och kända problem finns i [förändringar i beteendet](sql-database-managed-instance-transact-sql-information.md#Changes) och [kända problem](sql-database-managed-instance-transact-sql-information.md#Issues).


## <a name="can-a-managed-instance-have-the-same-name-as-on-premises-sql-server"></a>En hanterad instans kan ha samma namn som en lokal SQL Server?

Hanterade instansen måste ha ett namn som slutar med *database.windows.net*. Du använder en annan DNS-zon i stället för standardvärdet, till exempel **mi ett annat namn**. contoso.com: 
- Använda CliConfig för att definiera ett alias (verktyget är bara en registret inställningar omslutning, så det kan också göras med hjälp av Grupprincip eller skript).
- Använd *CNAME* med *TrustServerCertificate = true* alternativet.


## <a name="how-can-i-move-database-from-managed-instance-back-to-sql-server-or-azure-sql-database"></a>Hur kan jag flytta databasen från hanterad instans tillbaka till SQL Server eller Azure SQL Database?

Du kan [exportera databasen till bacpac](sql-database-export.md) och sedan [importera bacpac-fil]( sql-database-import.md). Den här metoden rekommenderas om din databas är mindre än 100 GB.

Transaktionsreplikering kan användas om alla tabeller i databasen har primärnycklar.

Interna `COPY_ONLY` säkerhetskopieringar från hanterade instansen kan inte återställas till SQL Server eftersom hanterad instans har en högre databasversionen jämfört med SQL Server.

## <a name="how-can-i-migrate-my-instance-database-to-a-single-azure-sql-database"></a>Hur kan jag migrera min instans-databas till en Azure SQL-databas?

Ett alternativ är att [exportera databasen till en bacpac](sql-database-export.md) och sedan [importera bacpac-fil]( sql-database-import.md). 

Det här är den rekommenderade metoden om din databas är mindre än 100 GB. Transaktionsreplikering kan användas om alla tabeller i databasen har primärnycklar.

## <a name="how-do-i-choose-between-gen-4-and-gen-5-hardware-generation-for-managed-instance"></a>Hur väljer jag mellan Gen 4 och 5 Gen maskinvara generation för den hanterade instansen?

Detta beror på din arbetsbelastning enligt vissa maskinvara generation är bättre för vissa typer av arbetsbelastningar än den andra. Ämnet för prestanda är i stället en komplex som ska förenkla, följande skillnader mellan maskinvarugenerationer påverkar arbetsbelastningens prestanda:
- Gen 4 ger en bättre stöd för beräkning som den är baserad på fysiska processorer jämfört med Gen 5 som baseras på vCore-processorer. Det kan vara mer fördelaktigt för beräkningsintensiva arbetsbelastningar.
- Gen 5 stöder accelerated networking vilket ger en bättre i/o-bandbredd för Fjärrlagring. Det kan vara fördelaktigt för i/o-intensiva arbetsbelastningar om tjänstnivåer för generell användning. Gen 5 använder snabbare SSD-lokala diskar jämfört med Gen 4. Det kan vara fördelaktigt för i/o-intensiva arbetsbelastningar på företag kritiska-nivåerna.

Kunder bör testa prestanda för faktiska arbetsbelastningar som är avsedda för produktion innan lanseringen för att avgöra vilken maskinvara generation fungerar bättre i ditt fall.

## <a name="can-i-switch-my-managed-instance-hardware-generation-between-gen-4-and-gen-5-online"></a>Kan jag byta min hanterad instans maskinvara generation mellan Gen 4 och 5 för Gen online? 

Det är möjligt om båda maskinvarugenerationer finns i samma region där din hanterade instans etableras automatiserade online växlar mellan maskinvarugenerationer. I det här fallet har du ett alternativ i prissättningsavsnittet nivån av Azure portal för att växla mellan maskinvarugenerationer.

Det här är en långvarig åtgärd som körs som den nya hantera instansen kommer att tillhandahållas på backend-servrar och databaser automatiskt överförs mellan den gamla och nya instansen. Den här processen kommer att sömlös för kunder.

Om båda maskinvarugenerationer inte stöds i samma region, ändra maskinvara generation går men måste göras manuellt. Detta måste du etablera en ny instans i regionen där den önskade maskinvara generationen är tillgänglig, och manuellt säkerhetskopiera och återställa data mellan den gamla och nya instansen.


## <a name="how-do-i-tune-performance-of-my-managed-instance"></a>Hur jag för att finjustera prestanda för min hanterad instans? 

Generell användning hanterade instansen använder Fjärrlagring på grund av som storleken på data och loggfiler som är viktigt för prestanda. Om du vill justera prestanda för generell användning service-nivå, följer du anvisningarna i det här blogginlägget.

Överväg att använda Gen 5 maskinvara, jämfört med Gen 4 för beräkningsintensiva arbetsbelastningar för i/o-intensiva arbetsbelastningar. Mer information finns i vanliga frågor och svar om att välja mellan maskinvarugenerationer.

Om din arbetsbelastning består av många små transaktioner, Överväg att anslutningstypen från proxy för att omdirigera läge.

## <a name="what-is-the-maximum-storage-size-for-managed-instance"></a>Vad är den maximala lagringsstorleken för den hanterade instansen? 

Lagringsstorlek för den hanterade instansen beror den valda tjänstnivån (generell användning eller affärskritiska). Storage-begränsningar av dessa tjänstnivåer, se [Service nivån egenskap](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="is-the-backup-storage-deducted-from-my-managed-instance-storage"></a>Är lagringen av säkerhetskopior dras av från min lagring för hanterad instans? 

Nej, lagring av säkerhetskopior inte dras av från ditt lagringsutrymme för hanterad instans. Backup storage är oberoende från lagringsutrymmet instans och inte har begränsad storlek. Lagring av säkerhetskopior begränsas av tid för bevarande av säkerhetskopian av databasen instans kan konfigureras från 7 till 35 dagar. Mer information finns i [automatiska säkerhetskopior](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).
  
## <a name="how-can-i-set-inbound-nsg-rules-on-management-ports"></a>Hur ställer jag inkommande NSG-regler på hanteringsportar?

Inbyggda brandväggsfunktionen konfigurerar Windows-brandväggen på alla virtuella datorer i klustret för att tillåta inkommande anslutningar från IP-adressintervall som är associerade endast för Microsoft management/distribution. virtuella datorer och säkra administrativa arbetsstationer effektivt förhindrar intrång via nätverket.

Här är vilka portar som används för:

Portarna 9000 och 9003 som används av Service Fabric-infrastrukturen. Service Fabric primära roll är att hålla det virtuella klustret felfritt och hålla målstatusen när det gäller antal repliker för komponenten.

Portar 1438 och 1440 1452 som används av Nodagentens. Nodagentens är ett program som körs i klustret och används av kontrollplanet för att köra kommandon för hantering.

Förutom de inbyggda brandväggen på nätverksnivå skyddas också kommunikation med certifikat.
  
Mer information och hur du verifierar den inbyggda brandväggen finns i [Azure SQL Database managed instance inbyggda brandväggen](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="how-can-i-mitigate-networking-risks"></a>Hur kan jag minska nätverkets riskerna? 

För att undvika eventuella nätverk ställer rekommenderas kunder för att tillämpa olika säkerhetsinställningar och kontroller:

- Aktivera alla databaser på Transparent datakryptering (TDE).
- Stäng av Runtime CLR (Common Language). Detta rekommenderas lokala samt.
- Använda Azure AD-konton.
- Åtkomst SQL MI med lågprivilegierade DBA kontot.
- Konfigurera JiT jumpbox åtkomst för sysadmin-konto.
- Aktivera SQL-granskning och integrera det med aviseringar mekanismer.
- Aktivera identifiering av hot från ATS suite.


## <a name="where-can-i-find-use-cases-and-resulting-cost-savings-with-managed-instance"></a>Var hittar jag användningsfall och resulterande kostnadsbesparingar med hanterad instans?

Fallstudier för hanterad instans:

- [Komatsu](http://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [powerdetails](http://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](http://customers.microsoft.com/story/allscripts-partner-professional-services-azure)   
Om du vill få en bättre förståelse av fördelarna, kostnaderna och riskerna med att distribuera Azure SQL Database-hanterad instans, finns det också en Forrester-studien: [Total Economic Impact av MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="can-i-do-dns-refresh"></a>Kan jag göra DNS-uppdateringen? 
  
Vi tillhandahåller för närvarande inte en funktion för att uppdatera konfigurationen för DNS-server för den hanterade instansen.

DNS-konfiguration uppdateras till slut:

- När DHCP-lån förfaller.
- Vid plattformsuppgradering.

Som en lösning kan nedgradera den hanterade instansen till 4 vCore och uppgraderar den igen efteråt. Detta har en sidoeffekt av uppdatera DNS-konfigurationen.


## <a name="can-a-managed-instance-have-a-static-ip-address"></a>En hanterad instans kan ha en statisk IP-adress?

I sällsynta, behövs situationer, kan vi behöva göra en online migrering av en hanterad instans till ett nytt virtuellt kluster. Om det behövs, är den här migreringen på grund av ändringar i våra teknikstacken som syftar till att förbättra säkerheten och pålitligheten för tjänsten. Migrera till ett nytt virtuellt kluster resultat ändring av IP-adressen som är mappad till den hanterade instansen värdnamn. Hanterad instans-tjänsten anspråk inte stöd för statisk IP-adress och förbehåller sig rätten att ändra den utan föregående meddelande som en del av regelbundet underhåll cykler.

Vi avråder förlita dig på oföränderlighetsprincip för IP-adressen eftersom det kan medföra onödig stilleståndstid därför.


## <a name="can-i-change-the-time-zone-for-an-existing-managed-instance"></a>Kan jag ändra tidszonen för en befintlig hanterad instans?

Konfigurationen för tidszon kan anges när en hanterad instans etableras för första gången. Det går inte att ändra tidszonen för den befintliga hanterade instansen. Mer information finns i [tidszon begränsningar](sql-database-managed-instance-timezone.md#limitations).

Lösningar omfattar att skapa en ny hanterad instans med rätt tidszon och sedan kan du utföra en manuell säkerhetskopiering och återställning, eller vad rekommenderar vi att, utföra en [mellan instans point-in-time-återställning](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="how-do-i-resolve-performance-issues-with-my-managed-instance"></a>Hur löser jag prestandaproblem med min hanterad instans

En prestanda jämförelse mellan hanterad instans och SQL Server är en bra utgångspunkt [bästa praxis för prestanda jämförelse mellan Azure SQL managed instance och SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Datainläsningen är ofta långsammare på hanterad instans än i SQL Server på grund av obligatoriska fullständiga återställningsmodellen och [gränser](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) på transaction log genomströmning för skrivning. Ibland kan går det att arbeta runt genom att läsa in tillfälliga data i tempdb i stället för databasen eller använda grupperat kolumnlagrings- eller minnesoptimerade tabeller.


## <a name="can-i-restore-my-encrypted-database-to-managed-instance"></a>Kan jag återställa krypterade databasen till managed instance?

Ja, du behöver inte dekryptera din databas om du vill kunna återställa den till managed instance. Du behöver ange ett certifikat/nyckel som används som ett nyckelskydd i källsystemet till den hanterade instansen för att kunna läsa data från den krypterade säkerhetskopiera filen. Det finns två sätt att göra detta:

- Ladda upp certifikat-skydd till den hanterade instansen. Detta kan göras med hjälp av PowerShell endast. Exempelskriptet beskriver hela processen.
- Ladda upp asymmetrisk nyckelskydd till Azure Key Vault (AKV) och peka den hanterade instansen. Den här metoden liknar bring-your-own-key (BYOK) TDE användningsfall som också använder AKV-integreringen för att lagra krypteringsnyckeln. Om du bara vill nyckeln överförs till AKV som är tillgängliga för hanterad instans för återställning av krypterade databaser utan att använda nyckeln som en nyckelskydd, följer du anvisningarna för att konfigurera BYOK TDE och inte markerar du kryssrutan gör den valda nyckeln i standard TDE-skydd.

När du gör krypteringen skydd tillgängligt för hanterad instans, kan du fortsätta med återställningsprocedur för standard-databas.

## <a name="how-can-i-migrate-from-azure-sql-database-single-or-elastic-pool-to-managed-instance"></a>Hur kan jag migrera från Azure SQL Database enkel eller elastisk pool till managed instance? 

Hanterade instans erbjuder samma prestandanivåer per beräknings- och storlek som andra distributionsalternativ i Azure SQL Database. Om du vill konsolidera data på en enda instans, eller du behöver en funktion som stöds endast i hanterad instans, kan du migrera dina data med hjälp av export/import (BACPAC)-funktioner.
