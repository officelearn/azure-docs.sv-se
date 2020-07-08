---
title: Vanliga frågor och svar
titleSuffix: Azure SQL Managed Instance
description: Vanliga frågor och svar om Azure SQL Managed instance
services: sql-database
ms.service: sql-managed-instance
ms.subservice: operations
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein, carlrab
ms.date: 03/17/2020
ms.openlocfilehash: 9295c6e1daaad6346581b959a9b94a7ab74da44c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84708866"
---
# <a name="azure-sql-managed-instance-frequently-asked-questions-faq"></a>Vanliga frågor och svar om Azure SQL Managed instance
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Den här artikeln innehåller de vanligaste frågorna om [Azure SQL-hanterad instans](sql-managed-instance-paas-overview.md).

## <a name="supported-features"></a>Funktioner som stöds

**Var kan jag hitta en lista över funktioner som stöds på SQL-hanterad instans?**

En lista över funktioner som stöds i SQL-hanterad instans finns i [funktioner för Azure SQL-hanterad instans](../database/features-comparison.md).

Skillnader i syntax och beteende mellan Azure SQL-hanterad instans och SQL Server finns i [skillnader i T-SQL från SQL Server](transact-sql-tsql-differences-sql-server.md).


## <a name="tech-spec--resource-limits"></a>Teknisk specifikation & resurs gränser
 
**Var kan jag hitta tekniska egenskaper och resurs gränser för SQL-hanterad instans?**

Information om tillgängliga egenskaper för maskin varu skapande finns i [tekniska skillnader i maskin varu generationer](resource-limits.md#hardware-generation-characteristics).
För tillgängliga tjänst nivåer och deras egenskaper, se [tekniska skillnader mellan tjänst nivåer](resource-limits.md#service-tier-characteristics).

## <a name="known-issues--bugs"></a>Kända problem & buggar

**Var hittar jag kända problem och buggar?**

Information om buggar och kända problem finns i [kända problem](../database/doc-changes-updates-release-notes.md#known-issues).

## <a name="new-features"></a>Nya funktioner

**Var hittar jag de senaste funktionerna och funktionerna i den offentliga för hands versionen?**

För nya och förhands gransknings funktioner, se [viktig information](../database/doc-changes-updates-release-notes.md?tabs=managed-instance).

## <a name="deployment-times"></a>Distributions tider 

**Hur lång tid tar det att skapa eller uppdatera en instans eller återställa en databas?**

Förväntad tid för att skapa en hanterad instans eller ändra tjänst nivå (virtuella kärnor, Storage) beror på flera faktorer. Ta en titt på [hanterings åtgärderna](/azure/sql-database/sql-database-managed-instance#managed-instance-management-operations). 

## <a name="naming-conventions"></a>Namngivningskonventioner

**Kan en hanterad instans ha samma namn som en SQL Server lokal instans?**

Det finns inte stöd för att ändra namnet på en hanterad instans.

Standard-DNS-zonen *. Database.Windows.net* för en hanterad instans kan ändras. 

Om du vill använda en annan DNS-zon i stället för standard, till exempel *. contoso.com*: 
- Använd CliConfig för att definiera ett alias. Verktyget är bara ett gränssnitt för register inställningar, så det kan göras med hjälp av en grup princip eller ett skript.
- Använd *CNAME* med alternativet *TrustServerCertificate = True* .

## <a name="move-a-database-from-sql-managed-instance"></a>Flytta en databas från SQL-hanterad instans 

**Hur kan jag flytta en databas från SQL-hanterad instans tillbaka till SQL Server eller Azure SQL Database?**

Du kan [Exportera en databas till BACPAC](../database/database-export.md) och sedan [Importera BACPAC-filen](../database/database-import.md). Detta är den rekommenderade metoden om databasen är mindre än 100 GB.

Transaktionsreplikering kan användas om alla tabeller i databasen har primära nycklar.

`COPY_ONLY`Det går inte att återställa inbyggda säkerhets kopior från SQL Managed Instance till SQL Server eftersom SQL-hanterad instans har en högre databas version jämfört med SQL Server.

## <a name="migrate-an-instance-database"></a>Migrera en instans databas

**Hur kan jag migrera min instans databas till Azure SQL Database?**

Ett alternativ är att [Exportera databasen till en BACPAC](../database/database-export.md) och sedan [Importera BACPAC-filen](../database/database-import.md). 

Detta är den rekommenderade metoden om databasen är mindre än 100 GB. Transaktionsreplikering kan användas om alla tabeller i databasen har primära nycklar.

## <a name="switch-hardware-generation"></a>Växla maskin varu generering 

**Kan jag byta maskin varu generering för SQL-hanterad instans mellan gen 4 och gen 5 Online?**

Automatisk Online-växling mellan maskin varu generationer är möjlig om båda maskin varu generationerna är tillgängliga i den region där SQL-hanterad instans är etablerad. I det här fallet kan du kontrol lera [sidan vCore modell översikt](../database/service-tiers-vcore.md), som förklarar hur du växlar mellan maskin varu generationer.

Detta är en tids krävande åtgärd, eftersom en ny hanterad instans tillhandahålls i bakgrunden och databaserna automatiskt överförs mellan de gamla och nya instanserna, med en snabb redundans i slutet av processen. 

**Vad händer om båda maskin varu generationerna inte stöds i samma region?**

Om båda maskin varu generationerna inte stöds i samma region, är det möjligt att ändra maskin varu generationen, men måste utföras manuellt. Detta kräver att du etablerar en ny instans i den region där den önskade maskin varu generationen är tillgänglig och manuellt säkerhetskopierar och återställer data mellan de gamla och nya instanserna.

**Vad händer om det inte finns tillräckligt med IP-adresser för att utföra uppdaterings åtgärden?**

Om det inte finns tillräckligt med IP-adresser i under nätet där din hanterade instans är etablerad måste du skapa ett nytt undernät och en ny hanterad instans i det. Vi föreslår också att det nya under nätet skapas med fler IP-adresser allokerade så att framtida uppdaterings åtgärder undviker liknande situationer. (För rätt under näts storlek kontrollerar du [hur storleken på ett VNet-undernät ska fastställas](vnet-subnet-determine-size.md).) När den nya instansen har allokerats kan du manuellt säkerhetskopiera och återställa data mellan de gamla och nya instanserna eller utföra återställning mellan olika [tidpunkter](point-in-time-restore.md?tabs=azure-powershell). 


## <a name="tune-performance"></a>Justera prestanda

**Hur gör jag för att finjustera prestanda för SQL-hanterad instans?**

SQL-hanterad instans i Generell användning nivån använder Fjärrlagring, så storleken på data-och loggfiler är till för prestanda. Mer information finns i [effekten av logg fils storleken på generell användning prestanda för SQL-hanterad instans](https://medium.com/azure-sqldb-managed-instance/impact-of-log-file-size-on-general-purpose-managed-instance-performance-21ad170c823e).

Om din arbets belastning består av många små transaktioner bör du överväga att byta Anslutnings typ från proxy till omdirigeringsläge.

## <a name="maximum-storage-size"></a>Maximal lagrings storlek

**Vad är den maximala lagrings storleken för SQL-hanterad instans?**

Lagrings storleken för SQL-hanterad instans beror på den valda tjänst nivån (Generell användning eller Affärskritisk). Information om lagrings begränsningar för dessa tjänst nivåer finns i [Egenskaper för tjänst nivå](../database/service-tiers-general-purpose-business-critical.md).

## <a name="backup-storage-cost"></a>Reserv lagrings kostnad 

**Dras lagringen av säkerhets kopian från min SQL-hanterade instans lagring?**

Nej, lagring av säkerhets kopior dras inte av från ditt lagrings utrymme för SQL-hanterad instans. Lagrings utrymmet för säkerhets kopian är oberoende från instans lagrings utrymmet och är inte begränsat. Säkerhets kopierings lagringen begränsas av tids perioden för att behålla säkerhets kopieringen av dina instans databaser, vilket kan konfigureras från 7 till 35 dagar. Mer information finns i [automatiska säkerhets kopieringar](../database/automated-backups-overview.md).

## <a name="track-billing"></a>Spåra fakturering

**Finns det något sätt att spåra min fakturerings kostnad för SQL-hanterad instans?**

Du kan göra det med hjälp av [Azure Cost Management-lösningen](/azure/cost-management/). Gå till **prenumerationer** i [Azure Portal](https://portal.azure.com) och välj **kostnads analys**. 

Använd alternativet **ackumulerade kostnader** och filtrera sedan efter **resurs typ** som `microsoft.sql/managedinstances` . 
  
## <a name="inbound-nsg-rules"></a>Regler för inkommande NSG

**Hur kan jag ange regler för inkommande NSG på hanterings portar?**

Kontroll planet för SQL-hanterad instans upprätthåller NSG-regler som skyddar hanterings portar.

Här är vilka hanterings portar som används för:

Portarna 9000 och 9003 används av Azure Service Fabric-infrastrukturen. Service Fabric primära rollen är att hålla det virtuella klustret felfritt och hålla mål tillståndet i förhållande till antalet komponent repliker.

Portarna 1438, 1440 och 1452 används av Node-agenten. Node agent är ett program som körs i klustret och används av kontroll planet för att köra hanterings kommandon.

Förutom NSG-regler skyddar den inbyggda brand väggen instansen på nätverks nivån. I program lagret skyddas kommunikationen med certifikaten.

Mer information och information om hur du verifierar den inbyggda brand väggen finns i den [inbyggda brand väggen för Azure SQL Managed instance](management-endpoint-verify-built-in-firewall.md).


## <a name="mitigate-data-exfiltration-risks"></a>Minimera data exfiltrering-risker  

**Hur kan jag minimera data exfiltrering riskerna?**

För att minimera eventuella data exfiltrerings risker rekommenderar vi att du använder en uppsättning säkerhets inställningar och kontroller:

- Aktivera [Transparent datakryptering (TDE)](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql) på alla databaser.
- Inaktivera CLR (Common Language Runtime). Detta rekommenderas även lokalt.
- Använd endast Azure Active Directory (Azure AD)-autentisering.
- Få åtkomst till instansen med ett konto med låg privilegie rad DBA.
- Konfigurera åtkomst till JIT-bygel för kontot sysadmin.
- Aktivera [SQL-granskning](https://docs.microsoft.com/sql/relational-databases/security/auditing/sql-server-audit-database-engine)och integrera den med aviserings metoder.
- Aktivera [hot identifiering](https://docs.microsoft.com/azure/sql-database/sql-database-threat-detection) från paketet [Advanced Data Security (Ads)](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security) .


## <a name="cost-saving-use-cases"></a>Användnings fall med kostnads besparingar

**Var kan jag hitta användnings fall och resulterande kostnads besparingar med SQL-hanterad instans?**

Fallstudier om SQL-hanterade instanser:

- [Komatsu](https://customers.microsoft.com/story/komatsu-australia-manufacturing-azure)
- [KMD](https://customers.microsoft.com/en-ca/story/kmd-professional-services-azure-sql-database)
- [PowerDETAILS](https://customers.microsoft.com/story/powerdetails-partner-professional-services-azure-sql-database-managed-instance)
- [Allscripts](https://customers.microsoft.com/story/allscripts-partner-professional-services-azure)

För att få en bättre förståelse för de fördelar, kostnader och risker som är kopplade till distributionen av den hanterade Azure SQL-instansen finns det också en Forrester-undersökning: [den totala ekonomiska effekten av Microsoft Azure SQL Database hanterade instansen](https://azure.microsoft.com/resources/forrester-tei-sql-database-managed-instance).


## <a name="dns-refresh"></a>DNS-uppdatering 

**Kan jag göra DNS-uppdatering?**

För närvarande tillhandahåller vi inte någon funktion för att uppdatera DNS-serverkonfigurationen för SQL-hanterad instans.

DNS-konfigurationen uppdateras slutligen:

- När DHCP-lånet upphör att gälla.
- Vid plattforms uppgradering.

Som en lösning kan du nedgradera SQL-hanterad instans till 4 virtuella kärnor och uppgradera den igen efteråt. Detta har en sido effekt på att uppdatera DNS-konfigurationen.


## <a name="ip-address"></a>IP-adress

**Kan jag ansluta till SQL-hanterad instans med en IP-adress?**

Det finns inte stöd för att ansluta till SQL-hanterad instans med en IP-adress. Det SQL-hanterade instans värd namnet mappar till en belastningsutjämnare framför det virtuella SQL-hanterade instans klustret. Eftersom ett virtuellt kluster kan vara värd för flera hanterade instanser, kan inte anslutningar dirigeras till rätt hanterad instans utan att namnet uttryckligen anges.

Mer information om arkitektur för virtuella kluster i SQL-hanterad instans finns i [arkitektur för virtuella kluster anslutningar](connectivity-architecture-overview.md#virtual-cluster-connectivity-architecture).

**Kan SQL-hanterad instans ha en statisk IP-adress?**

I sällsynta men nödvändiga situationer kan vi behöva utföra en online-migrering av SQL-hanterad instans till ett nytt virtuellt kluster. Vid behov är migreringen på grund av ändringar i vår teknik stack som syftar till att förbättra säkerheten och tillförlitligheten för tjänsten. Om du migrerar till ett nytt virtuellt kluster ändras IP-adressen som är mappad till värd namnet för SQL-hanterad instans. SQL Managed instance service har inte stöd för statisk IP-adress och förbehåller sig rätten att ändra den utan att Observera att den är en del av vanliga underhålls cykler.

Därför förhindrar vi starkt att du förlitar dig på oföränderlighets i IP-adressen eftersom det kan orsaka onödigt drift stopp.

## <a name="change-time-zone"></a>Ändra tidszon

**Kan jag ändra tids zonen för en befintlig hanterad instans?**

Tids zons konfigurationen kan ställas in när en hanterad instans skapas för första gången. Det finns inte stöd för att ändra tids zonen för en befintlig hanterad instans. Mer information finns i [begränsningar för tids zoner](timezones-overview.md#limitations).

I lösningarna ingår att skapa en ny hanterad instans med rätt tidszon och sedan antingen utföra en manuell säkerhets kopiering och återställning, eller vad vi rekommenderar, utföra en [kors inaktive rad återställning av en viss tidpunkt](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2018/06/07/cross-instance-point-in-time-restore-in-azure-sql-database-managed-instance/).


## <a name="resolve-performance-issues"></a>Lösa prestanda problem

**Hur gör jag för att lösa prestanda problem med SQL-hanterad instans?**

För en prestanda jämförelse mellan SQL-hanterad instans och SQL Server är en bra start punkt [bästa praxis för prestanda jämförelse mellan Azure SQL-hanterad instans och SQL Server](https://techcommunity.microsoft.com/t5/Azure-SQL-Database/The-best-practices-for-performance-comparison-between-Azure-SQL/ba-p/683210).

Data inläsning är ofta långsammare på SQL-hanterad instans än i SQL Server på grund av den obligatoriska fullständiga återställnings modellen och [gränser](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-resource-limits#service-tier-characteristics) för skrivningar i transaktions loggen. Ibland kan detta bearbetas genom att läsa in tillfälliga data i tempdb i stället för användar databasen, eller genom att använda grupperade columnstore-eller minnesoptimerade tabeller.


## <a name="restore-encrypted-backup"></a>Återställa krypterad säkerhets kopia

**Kan jag återställa min krypterade databas till SQL-hanterad instans?**

Ja, du behöver inte dekryptera databasen för att återställa den till SQL-hanterad instans. Du måste ange ett certifikat/en nyckel som används som krypterings nyckel skydd på käll systemet till SQL-hanterad instans för att kunna läsa data från den krypterade säkerhets kopierings filen. Det finns två möjliga sätt att göra det:

- *Ladda upp certifikat – skydd mot SQL-hanterad instans*. Det kan bara göras med PowerShell. I [exempel skriptet](https://docs.microsoft.com/azure/sql-database/sql-database-managed-instance-migrate-tde-certificate) beskrivs hela processen.
- *Överför asymmetrisk nyckel-skydd till Azure Key Vault-och Point SQL-hanterad instans till den*. Den här metoden liknar BYOK-TDE, som också använder Key Vault-integrering för att lagra krypterings nyckeln. Om du inte vill använda nyckeln som krypterings nyckel skydd och bara vill göra nyckeln tillgänglig för SQL-hanterad instans för att återställa krypterade databaser, följ instruktionerna för att [Konfigurera BYOK TDE](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-azure-sql#manage-transparent-data-encryption)och markera inte kryss rutan **gör den markerade nyckeln till standard TDE-skyddet**.

När du har gjort krypterings skyddet tillgängligt för SQL-hanterad instans kan du fortsätta med standard proceduren för databas återställning.

## <a name="migrate-from-sql-database"></a>Migrera från SQL Database 

**Hur kan jag migrera från Azure SQL Database till SQL-hanterad instans?**

SQL-hanterad instans har samma prestanda nivåer per beräknings-och lagrings storlek som Azure SQL Database. Om du vill konsolidera data på en enskild instans, eller om du bara behöver en funktion som stöds exklusivt i SQL-hanterad instans, kan du migrera dina data med hjälp av funktionen för att exportera/importera (BACPAC).

## <a name="password-policy"></a>Lösen ords princip 

**Vilka lösen ords principer används för SQL-inloggningar med SQL-hanterade instanser?**

SQL-hanterad instans lösen ords princip för SQL-inloggningar ärver Azures plattforms principer som tillämpas på virtuella datorer som utgör den hanterade instansen. För tillfället går det inte att ändra någon av dessa inställningar eftersom inställningarna definieras av Azure och ärvs av en hanterad instans.

 > [!IMPORTANT]
 > Azure-plattformen kan ändra princip kraven utan att meddela tjänster som förlitar sig på dessa principer.

**Vad är nuvarande Azure Platform-principer?**

Varje inloggning måste ange sitt lösen ord vid inloggningen och ändra dess lösen ord när den når maximal ålder.

| **Princip** | **Säkerhetsinställning** |
| --- | --- |
| Högsta ålder för lösen ord | 42 dagar |
| Lägsta ålder för lösen ord | 1 dag |
| Minsta längd på lösenord | 10 tecken |
| Lösen ordet måste uppfylla komplexitets kraven | Enabled |

**Är det möjligt att inaktivera lösen ords komplexitet och förfallo datum i SQL-hanterad instans på inloggnings nivå?**

Ja, det är möjligt att kontrol lera CHECK_POLICY och CHECK_EXPIRATION fält på inloggnings nivå. Du kan kontrol lera aktuella inställningar genom att köra följande T-SQL-kommando:

```sql
SELECT *
FROM sys.sql_logins
```

Därefter kan du ändra angivna inloggnings inställningar genom att köra:

```sql
ALTER LOGIN <login_name> WITH CHECK_POLICY = OFF;
ALTER LOGIN <login_name> WITH CHECK_EXPIRATION = OFF;
```

(Ersätt "test" med önskat inloggnings namn och justera princip-och förfallo värden)
