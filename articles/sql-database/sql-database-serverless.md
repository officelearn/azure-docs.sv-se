---
title: Utan server
description: I den här artikeln beskrivs den nya beräkningsnivån utan server och den jämförs med den befintliga etablerade beräkningsnivån
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: test
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 3/11/2020
ms.openlocfilehash: 5c36dbfbe63314ef97edfa3dfbaae34667db002d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79268709"
---
# <a name="azure-sql-database-serverless"></a>Azure SQL Database serverlös

Azure SQL Database serverless är en beräkningsnivå för enskilda databaser som automatiskt skalar beräkning baserat på arbetsbelastningsbehov och räkningar för mängden beräkning som används per sekund. Den serverlösa beräkningsnivån pausar också automatiskt databaser under inaktiva perioder när endast lagring faktureras och automatiskt återupptar databaser när aktiviteten returnerar.

## <a name="serverless-compute-tier"></a>Serverlös beräkningsnivå

Den serverlösa beräkningsnivån för en enskild databas parametrieras av ett automatiskt beräkningsintervall och en fördröjning för automatisk paus.  Konfigurationen av dessa parametrar formar databasens prestandaupplevelse och beräkningskostnader.

![serverlös fakturering](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Prestandakonfiguration

- De **lägsta virtuella kärnorna** och högsta virtuella kärnor är konfigurerbara parametrar som definierar det intervall av beräkningskapacitet som är tillgängligt för databasen. **maximum vCores** Minnes- och IO-gränser är proportionella mot det angivna vCore-intervallet.  
- Fördröjningen **för automatisk paus** är en konfigurerbar parameter som definierar den tidsperiod som databasen måste vara inaktiv innan den pausas automatiskt. Databasen återupptas automatiskt när nästa inloggning eller annan aktivitet inträffar.  Alternativt kan automatisk pausning inaktiveras.

### <a name="cost"></a>Kostnad

- Kostnaden för en serverlös databas är summeringen av beräkningskostnaden och lagringskostnaden.
- När beräkningsanvändningen är mellan de min- och maxgränser som konfigurerats baseras beräkningskostnaden på vCore och minne som används.
- När beräkningsanvändningen är lägre än de mingränser som konfigurerats baseras beräkningskostnaden på de min vCores och minminne som konfigurerats.
- När databasen pausas är beräkningskostnaden noll och endast lagringskostnader uppstår.
- Lagringskostnaden bestäms på samma sätt som på den etablerade beräkningsnivån.

Mer kostnadsinformation finns i [Fakturering](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenarier

Serverlös är prisprestandaoptimerad för enskilda databaser med intermittenta, oförutsägbara användningsmönster som har råd med viss fördröjning i beräkningsuppvärmning efter inaktiva användningsperioder. Den etablerade beräkningsnivån är däremot prisprestandaoptimerad för enskilda databaser eller flera databaser i elastiska pooler med högre genomsnittlig användning som inte har råd med någon fördröjning i beräkningsuppvärmning.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenarier som är väl lämpade för serverlös beräkning

- Enskilda databaser med intermittenta, oförutsägbara användningsmönster varvas med perioder av inaktivitet och lägre genomsnittlig beräkningsanvändning över tiden.
- Enskilda databaser i den etablerade beräkningsnivån som ofta skalas om och kunder som föredrar att delegera beräkningsskalning till tjänsten.
- Nya enskilda databaser utan användningshistorik där beräkningsstorlek är svårt eller inte möjligt att uppskatta före distributionen i SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenarier som är väl lämpade för etablerad beräkning

- Enskilda databaser med mer regelbundna, förutsägbara användningsmönster och högre genomsnittlig beräkningsanvändning över tid.
- Databaser som inte kan tolerera prestanda kompromisser till följd av mer frekvent minne trimning eller fördröjning i autosvar från ett pausat tillstånd.
- Flera databaser med intermittenta, oförutsägbara användningsmönster som kan konsolideras i elastiska pooler för bättre prisprestandaoptimering.

## <a name="comparison-with-provisioned-compute-tier"></a>Jämförelse med etablerad beräkningsnivå

I följande tabell sammanfattas distinktionerna mellan den serverlösa beräkningsnivån och den etablerade beräkningsnivån:

| | **Serverlös databearbetning** | **Etablerad beräkning** |
|:---|:---|:---|
|**Användningsmönster för databaser**| Intermittent, oförutsägbar användning med lägre genomsnittlig beräkningsanvändning över tid. |  Mer regelbundna användningsmönster med högre genomsnittlig beräkningsanvändning över tid, eller flera databaser med elastiska pooler.|
| **Prestandahanteringsarbete** |Lower|Högre|
|**Beräkningskalning**|Automatisk|Manuell|
|**Beräkna svarstider**|Lägre efter inaktiva perioder|Omedelbar|
|**Faktureringsgranularitet**|Per sekund|Per timme|

## <a name="purchasing-model-and-service-tier"></a>Inköpsmodell och tjänstnivå

SQL Database serverless stöds för närvarande endast i den allmänna nivån på Generation 5-maskinvara i vCore-inköpsmodellen.

## <a name="autoscaling"></a>Automatisk skalning

### <a name="scaling-responsiveness"></a>Skala svarstider

I allmänhet körs serverlösa databaser på en dator med tillräcklig kapacitet för att tillgodose resursbehovet utan avbrott för en beräkningsmängd som begärs inom gränser som anges av värdet max vCores. Ibland sker belastningsutjämning automatiskt om datorn inte kan tillgodose resursbehovet inom några minuter. Om resursbehovet till exempel är 4 virtuella kärnor, men endast 2 virtuella kärnor är tillgängliga, kan det ta upp till några minuter att läsa in balansen innan 4 virtuella kärnor tillhandahålls. Databasen förblir online under belastningsutjämning förutom en kort period i slutet av åtgärden när anslutningarna tas bort.

### <a name="memory-management"></a>Minneshantering

Minne för serverlösa databaser återvinns oftare än för etablerade beräkningsdatabaser. Detta är viktigt för att kontrollera kostnaderna i serverlös och kan påverka prestanda.

#### <a name="cache-reclamation"></a>Cache återvinning

Till skillnad från etablerade beräkningsdatabaser återvinns minne från SQL-cachen från en serverlös databas när CPU- eller cacheanvändningen är låg.

- Cacheanvändning anses vara låg när den totala storleken på de senast använda cacheposterna understiger ett tröskelvärde under en tidsperiod.
- När cacheåtervinning utlöses minskas målcachens storlek stegvis till en bråkdel av dess tidigare storlek och återkravet fortsätter endast om användningen förblir låg.
- När cacheåterkning inträffar är principen för att välja cacheposter att ta bort samma urvalsprincip som för etablerade beräkningsdatabaser när minnestrycket är högt.
- Cachestorleken minskas aldrig under minminnesgränsen enligt definitionen i min vCores som kan konfigureras.

I både serverlösa och etablerade beräkningsdatabaser kan cacheposterna vräkas om allt tillgängligt minne används.

#### <a name="cache-hydration"></a>Cache återfuktning

SQL-cachen växer när data hämtas från disken på samma sätt och med samma hastighet som för etablerade databaser. När databasen är upptagen tillåts cacheminnet att växa utan begränsning upp till maxminnesgränsen.

## <a name="autopausing-and-autoresuming"></a>Automatisk pausning och autosvarelse

### <a name="autopausing"></a>Automatisk paus

Automatisk pausning utlöses om alla följande villkor är uppfyllda under den automatiska fördröjningen:

- Antal sessioner = 0
- CPU = 0 för användararbetsbelastning som körs i användarpoolen

Ett alternativ finns för att inaktivera automatisk paus om så önskas.

Följande funktioner stöder inte automatisk pausning.  Det vill än, om någon av följande funktioner används, förblir databasen online oavsett hur länge databasens inaktivitet är:

- Geo-replikering (aktiva geo-replikering och automatisk redundansgrupper).
- Långsiktig lagring av säkerhetskopiering (LTR).
- Synkroniseringsdatabasen som används i SQL-datasynkronisering.  Till skillnad från synkroniseringsdatabaser stöder hubb- och medlemsdatabaser automatisk paus.
- Jobbdatabasen som används i elastiska jobb.

Automatisk pausning förhindras tillfälligt under distributionen av vissa tjänstuppdateringar som kräver att databasen är online.  I sådana fall tillåts automatisk pausning igen när serviceuppdateringen är klar.

### <a name="autoresuming"></a>Autosvar

Autosvar utlöses om något av följande villkor är sant när som helst:

|Funktion|Utlösare för autosvar|
|---|---|
|Autentisering och auktorisering|Inloggning|
|Hotidentifiering|Aktivera/inaktivera inställningar för identifiering av hot på databas- eller servernivå.<br>Ändra inställningar för identifiering av hot på databas- eller servernivå.|
|Dataidentifiering och -klassificering|Lägga till, ändra, ta bort eller visa känslighetsetiketter|
|Granskning|Visa granskningsposter.<br>Uppdatera eller visa granskningsprincip.|
|Datamaskning|Lägga till, ändra, ta bort eller visa datamaskeringsregler|
|Transparent datakryptering|Visa tillstånd eller status för transparent datakryptering|
|Datalager för frågedata (prestanda)|Ändra eller visa inställningar för frågearkiv|
|Autotuning|Tillämpning och verifiering av automatisk rekommendationer som automatisk indexering|
|Databaskopiering|Skapa databas som kopia.<br>Exportera till en BACPAC-fil.|
|SYNKRONISERING AV SQL-data|Synkronisering mellan nav- och medlemsdatabaser som körs enligt ett konfigurerbart schema eller utförs manuellt|
|Ändra vissa databasmetadata|Lägga till nya databastaggar.<br>Ändra max vCores, min vCores eller automatisk fördröjning.|
|SQL Server Management Studio (SSMS)|Om du använder SSMS-versioner tidigare än 18.1 och öppnar ett nytt frågefönster för alla databaser på servern återupptas alla automatiskt pausade databaser på samma server. Detta inträffar inte om du använder SSMS version 18.1 eller senare.|

Autosvarning utlöses också under distributionen av vissa tjänstuppdateringar som kräver att databasen är online.

### <a name="connectivity"></a>Anslutning

Om en serverlös databas pausas återupptas databasen och returnerar ett fel som anger att databasen inte är tillgänglig med felkod 40613. När databasen har återupptagits måste inloggningen göras om för att upprätta anslutningen. Databasklienter med logik för återförsök för anslutning behöver inte ändras.

### <a name="latency"></a>Svarstid

Svarstiden för autosvar och autopause en serverlös databas är i allmänhet ordning 1 minut till autosvar och 1-10 minuter till autopause.

### <a name="customer-managed-transparent-data-encryption-byok"></a>Kundhanterad transparent datakryptering (BYOK)

Om du använder [klienthanterad transparent datakryptering](transparent-data-encryption-byok-azure-sql.md) (BYOK) och den serverlösa databasen pausas automatiskt när nyckelborttagning eller återkallning sker, förblir databasen i automatiskt pausat tillstånd.  I det här fallet, när återuppta nästa försök, förblir databasen pausad tills dess status övergår till otillgänglig efter cirka 10 minuter eller mindre.  När databasen blir otillgänglig är återställningsprocessen densamma som för etablerade beräkningsdatabaser.  Om den serverlösa databasen är online när nyckelborttagning eller återkallning sker blir databasen också otillgänglig efter cirka 10 minuter eller mindre på samma sätt som med etablerade beräkningsdatabaser.

## <a name="onboarding-into-serverless-compute-tier"></a>Introduktion till serverlös beräkningsnivå

Skapa en ny databas eller flytta en befintlig databas till en serverlös beräkningsnivå följer samma mönster som att skapa en ny databas i etablerad beräkningsnivå och innebär följande två steg.

1. Ange tjänstmålsnamnet. Servicemålet föreskriver tjänstnivå, maskinvarugenerering och max vCores. I följande tabell visas alternativ för servicemål:

   |Namn på tjänstens mål|Tjänstenivå|Generering av maskinvara|Max vCores|
   |---|---|---|---|
   |GP_S_Gen5_1|Generell användning|Gen5 (På andra)|1|
   |GP_S_Gen5_2|Generell användning|Gen5 (På andra)|2|
   |GP_S_Gen5_4|Generell användning|Gen5 (På andra)|4|
   |GP_S_Gen5_6|Generell användning|Gen5 (På andra)|6|
   |GP_S_Gen5_8|Generell användning|Gen5 (På andra)|8|
   |GP_S_Gen5_10|Generell användning|Gen5 (På andra)|10|
   |GP_S_Gen5_12|Generell användning|Gen5 (På andra)|12|
   |GP_S_Gen5_14|Generell användning|Gen5 (På andra)|14|
   |GP_S_Gen5_16|Generell användning|Gen5 (På andra)|16|

2. Du kan också ange min vCores och autopause-fördröjningen för att ändra deras standardvärden. I följande tabell visas tillgängliga värden för dessa parametrar.

   |Parameter|Värdeval|Standardvärde|
   |---|---|---|---|
   |Min vCores|Beror på max vCores konfigurerade - se [resursgränser](sql-database-vcore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).|0,5 virtuella kärnor|
   |Fördröjning för automatisk paus|Minimum: 60 minuter (1 timme)<br>Max: 10080 minuter (7 dagar)<br>Steg: 60 minuter<br>Inaktivera autopause: -1|60 minuter|


### <a name="create-new-database-in-serverless-compute-tier"></a>Skapa ny databas på serverlös beräkningsnivå 

I följande exempel skapas en ny databas på beräkningsnivån för serverlös. Exemplen anger uttryckligen min vCores, max vCores och autopause fördröjning.

#### <a name="use-azure-portal"></a>Använda Azure-portalen

Se [Snabbstart: Skapa en enda databas i Azure SQL Database med Azure-portalen](sql-database-single-database-get-started.md).


#### <a name="use-powershell"></a>Använd PowerShell

```powershell
New-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -ComputeModel Serverless -Edition GeneralPurpose -ComputeGeneration Gen5 `
  -MinVcore 0.5 -MaxVcore 2 -AutoPauseDelayInMinutes 720
```
#### <a name="use-azure-cli"></a>Använda Azure CLI

```azurecli
az sql db create -g $resourceGroupName -s $serverName -n $databaseName `
  -e GeneralPurpose -f Gen5 -min-capacity 0.5 -c 2 --compute-model Serverless --auto-pause-delay 720
```


#### <a name="use-transact-sql-t-sql"></a>Använda Transact-SQL (T-SQL)

I följande exempel skapas en ny databas på den serverlösa beräkningsnivån.

```sql
CREATE DATABASE testdb
( EDITION = 'GeneralPurpose', SERVICE_OBJECTIVE = 'GP_S_Gen5_1' ) ;
```

Mer information finns i [SKAPA DATABAS](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current).  

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Flytta databas från etablerad beräkningsnivå till serverlös beräkningsnivå

I följande exempel flyttas en databas från den etablerade beräkningsnivån till den serverlösa beräkningsnivån. Exemplen anger uttryckligen min vCores, max vCores och autopause fördröjning.

#### <a name="use-powershell"></a>Använd PowerShell


```powershell
Set-AzSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName `
  -Edition GeneralPurpose -ComputeModel Serverless -ComputeGeneration Gen5 `
  -MinVcore 1 -MaxVcore 4 -AutoPauseDelayInMinutes 1440
```

#### <a name="use-azure-cli"></a>Använda Azure CLI

```azurecli
az sql db update -g $resourceGroupName -s $serverName -n $databaseName `
  --edition GeneralPurpose --min-capacity 1 --capacity 4 --family Gen5 --compute-model Serverless --auto-pause-delay 1440
```


#### <a name="use-transact-sql-t-sql"></a>Använda Transact-SQL (T-SQL)

I följande exempel flyttas en databas från den etablerade beräkningsnivån till den serverlösa beräkningsnivån.

```sql
ALTER DATABASE testdb 
MODIFY ( SERVICE_OBJECTIVE = 'GP_S_Gen5_1') ;
```

Mer information finns i [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current).

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Flytta databas från serverlös beräkningsnivå till etablerad beräkningsnivå

En serverlös databas kan flyttas till en etablerad beräkningsnivå på samma sätt som att flytta en etablerad beräkningsdatabas till en serverlös beräkningsnivå.

## <a name="modifying-serverless-configuration"></a>Ändra serverlös konfiguration

### <a name="use-powershell"></a>Använd PowerShell

Ändra den maximala eller minsta vCores, och automatisk paus fördröjning, utförs med hjälp av [kommandot Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase) i PowerShell med `MaxVcore`hjälp av , `MinVcore`och `AutoPauseDelayInMinutes` argument.

### <a name="use-azure-cli"></a>Använda Azure CLI

Ändra den maximala eller minsta vCores, och automatisk paus fördröjning, utförs med hjälp av `capacity`az `min-capacity`sql `auto-pause-delay` [db update](/cli/azure/sql/db#az-sql-db-update) kommandot i Azure CLI med hjälp av , och argument.


## <a name="monitoring"></a>Övervakning

### <a name="resources-used-and-billed"></a>Resurser som används och faktureras

Resurserna i en serverlös databas kapslas in av apppaket, SQL-instans och användarresurspoolentiteter.

#### <a name="app-package"></a>Appaket

Apppaketet är den yttre resurshanteringsgränsen för en databas, oavsett om databasen finns på en serverlös eller etablerad beräkningsnivå. Apppaketet innehåller SQL-instansen och externa tjänster som tillsammans omfattar alla användar- och systemresurser som används av en databas i SQL Database. Exempel på externa tjänster är R och fulltextsökning. SQL-instansen dominerar i allmänhet det totala resursutnyttjandet i hela appaketet.

#### <a name="user-resource-pool"></a>Användarresurspool

Användarresurspoolen är den inre mest resurshanteringsgränsen för en databas, oavsett om databasen finns på en serverlös eller etablerad beräkningsnivå. Användarresurspoolen scopes CPU och IO för användararbetsbelastning som genereras av DDL-frågor som SKAPA och ÄNDRA och DML-frågor som VÄLJ, INFOGA, UPPDATERA och TA BORT. Dessa frågor utgör i allmänhet den mest betydande andelen av utnyttjandet i appaketet.

### <a name="metrics"></a>Mått

Mått för att övervaka resursanvändningen för apppaketet och användarpoolen för en serverlös databas visas i följande tabell:

|Entitet|Mått|Beskrivning|Enheter|
|---|---|---|---|
|Appaket|app_cpu_percent|Procentandel av virtuella kärnor som används av appen i förhållande till max vCores som tillåts för appen.|Procent|
|Appaket|app_cpu_billed|Mängden beräkning som faktureras för appen under rapporteringsperioden. Det belopp som betalas under denna period är produkten av det här måttet och vCore-enhetspriset. <br><br>Värdena för det här måttet bestäms genom att samla över tiden det maximala processor som används och minnet som används varje sekund. Om det använda beloppet är mindre än det minsta belopp som etablerats enligt min vCores och min minne, faktureras det minsta belopp som etablerats.För att jämföra CPU med minne för faktureringsändamål normaliseras minnet till enheter av virtuella kärnor genom att mängden minne i GB ska beräknas med 3 GB per vCore.|vCore sekunder|
|Appaket|app_memory_percent|Procentandel minne som används av appen i förhållande till maximalt minne som tillåts för appen.|Procent|
|Användarpool|cpu_percent|Procentandel av virtuella kärnor som används av användararbetsbelastning i förhållande till max vCores som tillåts för användararbetsbelastning.|Procent|
|Användarpool|data_IO_percent|Procentandel data IOPS som används av användararbetsbelastningen i förhållande till max data IOPS tillåts för användararbetsbelastning.|Procent|
|Användarpool|log_IO_percent|Procentandel av logg MB/s som används av användararbetsbelastningen i förhållande till max logg MB/s tillåts för användararbetsbelastning.|Procent|
|Användarpool|workers_percent|Procentandel av arbetare som används av användararbetsbelastningen i förhållande till max arbetare som tillåts för användararbetsbelastning.|Procent|
|Användarpool|sessions_percent|Procentandel av sessioner som används av användararbetsbelastningen i förhållande till max sessioner som tillåts för användararbetsbelastning.|Procent|

### <a name="pause-and-resume-status"></a>Pausa och återuppta status

I Azure-portalen visas databasstatusen i översiktsfönstret för servern som visar de databaser som den innehåller. Databasstatusen visas också i översiktsfönstret för databasen.

Använda följande kommandon för att fråga efter en databass paus- och återställningsstatus:

#### <a name="use-powershell"></a>Använd PowerShell

```powershell
Get-AzSqlDatabase -ResourceGroupName $resourcegroupname -ServerName $servername -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

#### <a name="use-azure-cli"></a>Använda Azure CLI

```azurecli
az sql db show --name $databasename --resource-group $resourcegroupname --server $servername --query 'status' -o json
```


## <a name="resource-limits"></a>Resursbegränsningar

För resursgränser finns i [serverlös beräkningsnivå](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).

## <a name="billing"></a>Fakturering

Mängden beräknad faktureras är den maximala cpu som används och minne som används varje sekund. Om mängden processor som används och det minne som används är mindre än det minsta belopp som etablerats för varje, faktureras det etablerade beloppet. För att jämföra CPU med minne för faktureringsändamål normaliseras minnet till enheter av virtuella kärnor genom att mängden minne i GB ska beräknas med 3 GB per vCore.

- **Fakturerad resurs**: CPU och minne
- **Fakturerat belopp:** vCore-enhetspris * max (min vCores, vCores som används, min minne GB * 1/3, minne GB används * 1/3) 
- **Faktureringsfrekvens:** Per sekund

VCore-enhetspriset är kostnaden per vCore per sekund. Se [prissidan](https://azure.microsoft.com/pricing/details/sql-database/single/) för Azure SQL Database för specifika enhetspriser i en viss region.

Mängden beräknad faktureras exponeras av följande mått:

- **Mått:** app_cpu_billed (vCore sekunder)
- **Definition:** max (min vCores, vCores används, min minne GB * 1 / 3, minne GB används * 1 / 3)
- **Rapporteringsfrekvens**: Per minut

Denna kvantitet beräknas varje sekund och aggregeras över 1 minut.

Tänk dig en serverlös databas konfigurerad med 1 min vCore och 4 max vCores.  Detta motsvarar cirka 3 GB min minne och 12 GB max minne.  Anta att fördröjningen för automatisk paus är inställd på 6 timmar och databasarbetsbelastningen är aktiv under de första 2 timmarna av en 24-timmarsperiod och i övrigt inaktiv.    

I det här fallet faktureras databasen för beräkning och lagring under de första 8 timmarna.  Även om databasen är inaktiv från och med den andra timmen faktureras den fortfarande för beräkning under de efterföljande 6 timmarna baserat på den minsta beräkning som etablerats medan databasen är online.  Endast lagringsutrymme faktureras under resten av 24-timmarsperioden medan databasen pausas.

Mer exakt beräknas beräkningsfakturan i det här exemplet på följande sätt:

|Tidsintervall|vCores används varje sekund|GB används varje sekund|Beräkna dimensioner fakturerad|vCore sekunder faktureras över tidsintervall|
|---|---|---|---|---|
|0:00-1:00|4|9|vCores används|4 vCores * 3600 sekunder = 14400 vCore sekunder|
|1:00-2:00|1|12|Minne som används|12 GB * 1/3 * 3600 sekunder = 14400 vCore sekunder|
|2:00-8:00|0|0|Minsta minne har etablerats|3 GB * 1/3 * 21600 sekunder = 21600 vCore sekunder|
|8:00-24:00|0|0|Ingen beräkning faktureras medan pausad|0 vCore sekunder|
|Totalt antal vCore-sekunder fakturerade över 24 timmar||||50400 vCore sekunder|

Anta att beräkningsenhetens pris är $0.000145/vCore/second.  Sedan beräknas faktureras för denna 24-timmarsperiod är produkten av beräkningsenheten pris och vCore sekunder faktureras: $ 0,000145/vCore / andra * 50400 vCore sekunder ~ $ 7,31

### <a name="azure-hybrid-benefit-and-reserved-capacity"></a>Azure Hybrid-förmån och reserverad kapacitet

Azure Hybrid Benefit (AHB) och reserverade kapacitetsrabatter gäller inte för den serverlösa beräkningsnivån.

## <a name="available-regions"></a>Tillgängliga regioner

Den serverlösa beräkningsnivån är tillgänglig över hela världen utom följande regioner: China East, China North, Germany Central, Germany Northeast, UK North, UK South 2, West Central US och US Gov Central (Iowa).

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer igång finns [i Snabbstart: Skapa en enda databas i Azure SQL Database med Azure-portalen](sql-database-single-database-get-started.md).
- För resursbegränsningar finns i [Resursgränser för serverlös beräkningsnivå](sql-database-vCore-resource-limits-single-databases.md#general-purpose---serverless-compute---gen5).
