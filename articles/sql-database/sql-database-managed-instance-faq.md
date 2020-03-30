---
title: Vanliga frågor och svar om hanterade instanser
description: SQL Database hanterad instans vanliga frågor och svar (FAQ)
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 393d67b200a4f8d44cb001b3a7e2e491209e9d58
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80364170"
---
# <a name="sql-database-managed-instance-frequently-asked-questions-faq"></a>SQL Database hanterad instans vanliga frågor och svar (FAQ)

Den här artikeln innehåller många av de vanligaste frågorna om [SQL Database-hanterad instans](sql-database-managed-instance.md).

## <a name="supported-features"></a>Funktioner som stöds

**Var hittar jag en lista över funktioner som stöds vid hanterad instans?**

En lista över funktioner som stöds i hanterade instans finns i [Azure SQL Database kontra SQL Server](sql-database-features.md).

Skillnader i syntax och beteende mellan Azure SQL Database-hanterad instans och lokal SQL Server finns i [T-SQL-skillnader från SQL Server](sql-database-managed-instance-transact-sql-information.md).


## <a name="tech-spec--resource-limits"></a>Teknisk specifikation & resursgränser
 
**Var hittar jag tekniska egenskaper och resursgränser för hanterad instans?**

För tillgängliga maskinvarugenereringsegenskaper, se [tekniska skillnader i maskinvarugenerationer](sql-database-managed-instance-resource-limits.md#hardware-generation-characteristics).
För tillgängliga tjänstnivåer och deras egenskaper, se [tekniska skillnader mellan tjänstnivåer](sql-database-managed-instance-resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Kända problem & buggar

**Var hittar jag kända problem och buggar?**

För buggar och kända problem se [kända problem](sql-database-release-notes.md#known-issues).

## <a name="new-features"></a>Nya funktioner

**Var hittar jag de senaste funktionerna och funktionerna i den offentliga förhandsversionen?**

Nya funktioner och förhandsgranskningsfunktioner finns [i viktig information](sql-database-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Driftsättningstider 

**Hur lång tid tar det att skapa eller uppdatera instansen eller återställa en databas?**

Förväntad tid för att skapa ny hanterad instans eller ändra tjänstnivå (vCores, lagring) beror på flera faktorer. Ta en titt på [ledningens verksamhet](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations) 

## <a name="naming-convention"></a>Namnkonvention

**Kan en hanterad instans ha samma namn som lokal SQL Server?**

Det går inte att ändra namnet på hanterade instanser.

Den hanterade instansstandard-DNS-zonen *.database.windows.net* kan ändras. 

Så här använder du en annan DNS-zon i stället för standard, till exempel *.contoso.com:* 
- Använd CliConfig för att definiera ett alias. Verktyget är bara ett registerinställningar omslag, så det kan göras med grupprincip eller skript också.
- Använd *alternativet CNAME* med *TrustServerCertificate=true.*

## <a name="move-db-from-mi"></a>Flytta DB från MI 

**Hur kan jag flytta tillbaka databasen från hanterad instans till SQL Server eller Azure SQL Database?**

Du kan [exportera databasen till BACPAC](sql-database-export.md) och sedan [importera BACPAC-filen]( sql-database-import.md). Detta är en rekommenderad metod om databasen är mindre än 100 GB.

Transaktionsreplikering kan användas om alla tabeller i databasen har primära nycklar.

Inbyggda `COPY_ONLY` säkerhetskopior som hämtas från hanterad instans kan inte återställas till SQL Server eftersom hanterad instans har en högre databasversion jämfört med SQL Server.

## <a name="migrate-instance-db"></a>Migrera instans DB

**Hur migrerar jag instansdatabasen till en enda Azure SQL-databas?**

Ett alternativ är att [exportera databasen till en BACPAC](sql-database-export.md) och sedan importera [BACPAC-filen](sql-database-import.md). 

Detta är den rekommenderade metoden om databasen är mindre än 100 GB. Transaktionsreplikering kan användas om alla tabeller i databasen har primära nycklar.

## <a name="switch-hardware-generation"></a>Växla hårdvarugenerering 

**Kan jag byta min hanterade instans maskinvarugenerering mellan Gen 4 och Gen 5 online?**

Automatisk onlineväxling mellan maskinvarugenerationer är möjlig om båda maskinvarugenerationerna är tillgängliga i den region där din hanterade instans etableras. I det här fallet kan du kontrollera [vCore-modellens översiktssida](sql-database-service-tiers-vcore.md) som förklarar hur du växlar mellan maskinvarugenerationer.

Detta är en tidskrävande åtgärd eftersom en ny hanterad instans etableras i bakgrunden och databaser överförs automatiskt mellan den gamla och den nya instansen med en snabb redundans i slutet av processen. 

Om båda maskinvarugenerationerna inte stöds i samma region är det möjligt att ändra maskinvarugenereringen, men måste göras manuellt. Detta kräver att du etablerar en ny instans i den region där den önskade maskinvarugenereringen är tillgänglig och säkerhetskopierar och återställer data manuellt mellan den gamla och den nya instansen.


## <a name="tune-performance"></a>Justera prestanda

**Hur justerar jag prestanda för min hanterade instans?**

Generell hanterad instans använder fjärrlagring på grund av vilken storlek på data och loggfiler som är viktiga för prestanda. Mer information finns i [Inverkan av loggfilsstorleken på prestanda för hanterad allmänt syfte](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Om din arbetsbelastning består av många små transaktioner kan du överväga att byta anslutningstyp från proxy till omdirigeringsläge.

## <a name="maximum-storage-size"></a>Maximal lagringsstorlek

**Vilken är den maximala lagringsstorleken för hanterad instans?**

Lagringsstorleken för hanterad instans beror på den valda tjänstnivån (Allmänt syfte eller Affärskritisk). För lagringsbegränsningar för dessa tjänstnivåer, se [Egenskaper för tjänstnivå](sql-database-service-tiers-general-purpose-business-critical.md).

## <a name="back-up-storage-cost"></a>Säkerhetskopiera lagringskostnader 

**Dras säkerhetskopieringslagringen från min hanterade instanslagring?**

Nej, lagring av säkerhetskopiering dras inte från ditt hanterade instanslagringsutrymme. Lagringsplatsen för säkerhetskopiering är oberoende av instanslagringsutrymmet och är inte begränsad i storlek. Lagring av säkerhetskopior begränsas av tidsperioden för att behålla säkerhetskopian av instansdatabaserna, som kan konfigureras från 7 till 35 dagar. Mer information finns i [Automatiska säkerhetskopior](https://docs.microsoft.com/azure/sql-database/sql-database-automated-backups).

## <a name="track-billing"></a>Spåra fakturering

**Finns det något sätt att spåra min faktureringskostnad för min hanterade instans?**

Du kan göra det med hjälp av [Azure Cost Management-lösningen](/azure/cost-management/). Navigera till **Prenumerationer** i [Azure-portalen](https://portal.azure.com) och välj **Kostnadsanalys**. 

Använd alternativet **Ackumulerade kostnader** och filtrera sedan `microsoft.sql/managedinstances`efter **resurstypen** som . 
  
## <a name="inbound-nsg-rules"></a>Inkommande NSG-regler

**Hur ställer jag in inkommande NSG-regler för hanteringsportar?**

Hanterat instanskontrollplan upprätthåller NSG-regler som skyddar hanteringsportar.

Här är vad hanteringsportar används för:

Portarna 9000 och 9003 används av Infrastrukturen för Service Fabric. Service Fabric primära roll är att hålla det virtuella klustret felfritt och hålla måltillstånd när det gäller antalet komponentrepliker.

Portarna 1438, 1440 och 1452 används av nodagenten. Nodagent är ett program som körs i klustret och används av kontrollplanet för att köra hanteringskommandon.

Förutom NSG-regler skyddar den inbyggda brandväggen instansen på nätverkslagret. På programlagerkommunikationen skyddas med certifikaten.
  
Mer information och hur du verifierar den inbyggda brandväggen finns [i Azure SQL Database-den inbyggda brandväggen för hanterade instanser](sql-database-managed-instance-management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Minska dataexfiltrationsrisker  

**Hur kan jag minska dataexfiltrationsrisker?**

För att minska eventuella dataexfiltrationsrisker rekommenderas kunder att tillämpa en uppsättning säkerhetsinställningar och kontroller:

- Aktivera [Transparent datakryptering (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) i alla databaser.
- Inaktivera COMMON Language Runtime (CLR). Detta rekommenderas lokalt också.
- Använd endast Azure Active Directory-autentisering (AAD).
- Åtkomstinstans med lågt privilegierat DBA-konto.
- Konfigurera JiT jumpbox-åtkomst för sysadmin-konto.
- Aktivera [SQL-granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)och integrera den med varningsmekanismer.
- Aktivera [hotidentifieringen](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) från ads-paketet [(Advanced Data Security).](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security)


## <a name="cost-saving-use-cases"></a>Användningsfall för kostnadsbesparande

**Var hittar jag användningsfall och resulterande kostnadsbesparingar med hanterad instans?**

Hanterade instansfallstudier:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [Kmd](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS (30)](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)  
För att få en bättre förståelse för fördelarna, kostnaderna och riskerna med distributionen av azure SQL Database-hanterad instans finns det också en Forresters studie: [Total ekonomisk påverkan av MI](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS-uppdatering 

**Kan jag uppdatera DNS?**

För närvarande tillhandahåller vi inte en funktion för att uppdatera DNS-serverkonfigurationen för hanterad instans.

DNS-konfigurationen uppdateras så småningom:

- När DHCP-lånet går ut.
- På plattformsuppgradering.

Som en lösning nedgraderar du den hanterade instansen till 4 vCore och uppgraderar den igen efteråt. Detta har en bieffekt av att uppdatera DNS-konfigurationen.


## <a name="ip-address"></a>IP-adress

**Kan jag ansluta till hanterad instans med IP-adress?**

Det går inte att ansluta till hanterad instans med IP-adress. Hanterad instans värdnamn mappar till belastningsutjämnare framför hanterad instans virtuellt kluster. Eftersom ett virtuellt kluster kan vara värd för flera hanterade instanser kunde anslutningen inte dirigeras till korrekt hanterad instans utan att ange dess namn.

Mer information om virtuell klusterarkitektur för hanterade instanser finns i [Arkitektur för virtuell klusteranslutning](sql-database-managed-instance-connectivity-architecture.md#virtual-cluster-connectivity-architecture).

**Kan en hanterad instans ha en statisk IP-adress?**

I sällsynta men nödvändiga situationer kan vi behöva göra en onlinemigrering av en hanterad instans till ett nytt virtuellt kluster. Om det behövs beror den här migreringen på förändringar i vår teknikstack som syftar till att förbättra säkerheten och tillförlitligheten i tjänsten. Migrera till ett nytt virtuellt kluster resulterar i att ändra IP-adressen som mappas till den hanterade instansen värdnamn. Tjänsten hanterad instans kräver inte statisk IP-adresssupport och förbehåller sig rätten att ändra den utan föregående meddelande som en del av regelbundna underhållscykler.

Av denna anledning avråder vi starkt från att förlita sig på immutability av IP-adressen eftersom det kan orsaka onödiga driftstopp.

## <a name="change-time-zone"></a>Ändra tidszon

**Kan jag ändra tidszonen för en befintlig hanterad instans?**

Tidszonskonfiguration kan ställas in när en hanterad instans etableras för första gången. Det går inte att ändra tidszonen för den befintliga hanterade instansen. Mer information finns i [tidszonsbegränsningar](sql-database-managed-instance-timezone.md#limitations).

Lösningarna omfattar att skapa en ny hanterad instans med rätt tidszon och sedan antingen utföra en manuell säkerhetskopiering och återställning, eller vad vi rekommenderar, utföra en [cross-instance point-in-time restore](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Lösa prestandaproblem

**Hur löser jag prestandaproblem med min hanterade instans?**

För en prestandajämförelse mellan hanterad instans och SQL Server är en bra utgångspunkt [metodtips för prestandajämförelse mellan Azure SQL-hanterad instans och SQL Server-artikel.](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210)

Datainläsning är ofta långsammare på hanterad instans än i SQL Server på grund av obligatorisk fullständig återställningsmodell och [begränsningar](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) för transaktionsloggskrivningsdataflöde. Ibland kan detta bearbetas genom att läsa in tillfälliga data i tempdb i stället för användardatabas, eller med hjälp av klustrade columnstore eller minnesoptimerade tabeller.


## <a name="restore-encrypted-backup"></a>Återställa krypterad säkerhetskopiering

**Kan jag återställa min krypterade databas till hanterad instans?**

Ja, du behöver inte dekryptera databasen för att kunna återställa den till hanterad instans. Du måste ange ett certifikat/en nyckel som används som krypteringsnyckelskydd i källsystemet till den hanterade instansen för att kunna läsa data från den krypterade säkerhetskopian. Det finns två möjliga sätt att göra det:

- *Överför certifikatskydd till den hanterade instansen*. Det kan göras med PowerShell bara. [Exempelskriptet](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) beskriver hela processen.
- *Ladda upp asymmetrisk nyckelskydd till Akv (Azure Key Vault) och peka hanterad instans till den*. Den här metoden liknar byok-användningsfall (bring-your-own-key) som också använder AKV-integrering för att lagra krypteringsnyckeln. Om du inte vill använda nyckeln som ett krypteringsnyckelskydd och bara vill göra nyckeln tillgänglig för hanterad instans för att återställa krypterade databaser följer du instruktionerna för att [konfigurera BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)och markerar inte kryssrutan Gör den markerade nyckeln till *standard-TDE-skydd*.

När du har gjort krypteringsskyddet tillgängligt för hanterad instans kan du fortsätta med standardproceduren för återställning av databasen.

## <a name="migrate-from-single-db"></a>Migrera från en enda DB 

**Hur migrerar jag från en eller elastisk azure SQL-databas till hanterad instans?**

Hanterad instans erbjuder samma prestandanivåer per beräknings- och lagringsstorlek som andra distributionsalternativ för Azure SQL Database. Om du vill konsolidera data på en enda instans, eller om du bara behöver en funktion som stöds uteslutande i hanterad instans, kan du migrera dina data med hjälp av funktionen export/import (BACPAC).
