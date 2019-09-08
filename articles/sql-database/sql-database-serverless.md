---
title: Azure SQL Database utan server (för hands version) | Microsoft Docs
description: Den här artikeln beskriver den nya server lös beräknings nivån och jämför den med den befintliga allokerade beräknings nivån
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: moslake
ms.author: moslake
ms.reviewer: sstein, carlrab
ms.date: 09/06/2019
ms.openlocfilehash: 5b13c3c93f8b2d6f3eed5a32c49baf1b9d1b201e
ms.sourcegitcommit: a4b5d31b113f520fcd43624dd57be677d10fc1c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773344"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL Database utan server (för hands version)

Azure SQL Database utan server (för hands version) är en beräknings nivå för enskilda databaser som automatiskt skalar beräkning baserat på arbets belastnings behov och räkningar för mängden data bearbetning som används per sekund. Server lös beräknings nivån pausar också automatiskt databaser under inaktiva perioder när endast lagring faktureras och återupptar automatiskt databaser när aktiviteten returnerar.

## <a name="serverless-compute-tier"></a>Serverlös beräkningsnivå

Server lösa beräknings nivåer för en enskild databas är parameterstyrda av ett intervall för automatisk skalning av beräkning och en fördröjning för automatisk paus.  Konfigurationen av dessa parametrar formar databasens prestanda upplevelse och beräknings kostnader.

![utan server fakturering](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Prestandakonfiguration

- **Lägsta virtuella kärnor** och **maximal virtuella kärnor** är konfigurerbara parametrar som definierar intervallet för beräknings kapacitet som är tillgängliga för databasen. Minnes-och IO-gränser är proportionella till det vCore-intervall som angetts.  
- Den automatiska **paus fördröjningen** är en konfigurerbar parameter som definierar den tids period som databasen måste vara inaktiv innan den pausas automatiskt. Databasen återupptas automatiskt när nästa inloggning eller annan aktivitet sker.  Du kan också inaktivera AutoPause.

### <a name="cost"></a>Kostnad

- Kostnaden för en server lös databas är summan av beräknings kostnaden och lagrings kostnaden.
- När beräknings användningen är mellan den minsta och högsta gränsen som kon figurer ATS baseras beräknings kostnaden på vCore och använt minne.
- När beräknings användningen är under de lägsta gränser som har kon figurer ATS, baseras beräknings kostnaden på den minsta virtuella kärnor och minimalt minne som har kon figurer ATS.
- När databasen har pausats är beräknings kostnaden noll och endast lagrings kostnader uppkommer.
- Lagrings kostnaden fastställs på samma sätt som i den allokerade beräknings nivån.

Mer kostnads information finns i [fakturering](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenarier

Server lös är pris – prestanda som är optimerad för enkla databaser med ett oförutsägbart användnings mönster som kan ge en fördröjning i beräknings belastningen efter inaktiva användnings perioder. Den etablerade beräknings nivån är däremot pris-och prestanda optimerad för enskilda databaser eller flera databaser i elastiska pooler med en högre genomsnittlig användning som inte ger någon fördröjning i data bearbetningen.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Lämpliga scenarier för Server lös data behandling

- Enkla databaser med intermittent, oförutsägbara användnings mönster som blandats med perioder av inaktivitet och lägre genomsnittlig data bearbetning över tid.
- Enskilda databaser i den allokerade beräknings nivån som ofta skalas om och kunder som föredrar att delegera beräkning av skalbarhet till tjänsten.
- Nya enkla databaser utan användnings historik där beräknings storlek är svårt eller inte går att uppskatta innan distributionen i SQL Database.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenarier som passar bra för allokerad beräkning

- Enkla databaser med mer vanliga, förutsägbara användnings mönster och större genomsnittlig beräknings användning över tid.
- Databaser som inte kan tolerera prestanda kompromisser som orsakas av frekvent minnes trimning eller fördröjning i att återupptas från paus läge.
- Flera databaser med intermittent, oförutsägbara användnings mönster som kan konsol IDE ras i elastiska pooler för bättre pris-och prestanda optimering.

## <a name="comparison-with-provisioned-compute-tier"></a>Jämförelse med allokerad beräknings nivå

I följande tabell sammanfattas skillnader mellan server lös beräknings nivå och den allokerade beräknings nivån:

| | **Data behandling utan Server** | **Allokerad beräkning** |
|:---|:---|:---|
|**Användnings mönster för databas**| Intermittent, oförutsägbar användning med lägre genomsnittlig beräknings användning över tid. |  Vanliga användnings mönster med högre genomsnittlig beräknings användning över tid, eller flera databaser med elastiska pooler.|
| **Prestanda hanterings ansträngning** |Sämre|Högre|
|**Beräknings skalning**|Automatiskt|Manuell|
|**Beräknings svars tid**|Lägre efter inaktiva perioder|Omedelbar|
|**Fakturerings precision**|Per sekund|Per timme|

## <a name="purchasing-model-and-service-tier"></a>Inköps modell och tjänst nivå

SQL Database Server lös stöds för närvarande bara på Generell användning nivå på generation 5-maskin vara i vCore inköps modell.

## <a name="autoscaling"></a>Automatisk skalning

### <a name="scaling-responsiveness"></a>Skalnings svars tid

I allmänhet körs serverbaserade databaser på en dator som har tillräckligt med kapacitet för att uppfylla resurs behovet utan avbrott för en mängd data som begärs inom gränser som anges av Max värdet för virtuella kärnor. Ibland sker belastnings utjämning automatiskt om datorn inte kan uppfylla resurs behovet inom några minuter. Om till exempel resurs behovet är 4 virtuella kärnor, men bara 2 virtuella kärnor är tillgängliga, kan det ta upp till några minuter att belastningsutjämna innan 4 virtuella kärnor tillhandahålls. Databasen är online under belastnings utjämning, förutom en kort period i slutet av åtgärden när anslutningarna bryts.

### <a name="memory-management"></a>Minnes hantering

Minne för serverbaserade databaser frigörs oftare än för etablerade beräknings databaser. Det här beteendet är viktigt för att kontrol lera kostnader i Server lös och kan påverka prestandan.

#### <a name="cache-reclamation"></a>Cache regenering

Till skillnad från etablerade data bearbetnings databaser frigörs minne från SQL-cachen från en databas utan server när processor-eller cache-användningen är låg.

- Användningen av cacheminnet anses låg när den totala storleken på de senast använda cacheposter unders tiger ett tröskelvärde under en viss tids period.
- När cache regenering utlöses, minskas storleken på målets cachestorlek stegvis till en bråkdel av den tidigare storleken och återställningen fortsätter bara om användningen är låg.
- När cache regenering sker är principen för att välja cacheposter att ta bort samma princip som för etablerade beräknings databaser när minnes trycket är hög.
- Cachestorleken minskas aldrig under den minsta minnes gränsen som definieras av den minsta virtuella kärnor som kan konfigureras.

I både server lösa och etablerade beräknings databaser kan cacheposter avlägsnas om allt tillgängligt minne används.

#### <a name="cache-hydration"></a>Cachelagra hydrering

SQL-cachen växer när data hämtas från disk på samma sätt och med samma hastighet som för etablerade databaser. När databasen är upptagen får cachen öka obegränsad till högsta minnes gräns.

## <a name="autopausing-and-autoresuming"></a>Autopausa och Autoåteruppta

### <a name="autopausing"></a>Autopausa

AutoPause utlöses om samtliga följande villkor är uppfyllda för varaktigheten för den automatiskt pausade fördröjningen:

- Antal sessioner = 0
- CPU = 0 för användar arbets belastning som körs i anslutningspoolen

Det finns ett alternativ för att inaktivera autopausen om du vill.

Följande funktioner stöder inte AutoPause.  Det innebär att om någon av följande funktioner används är databasen online oavsett hur lång tid det tar för databas inaktivitet:

- Geo-replikering (aktiv geo-replikering och grupper för automatisk redundans).
- Långsiktig kvarhållning av säkerhets kopior (brv).
- Den synkroniserade databasen som används i SQL Data Sync.  Till skillnad från Sync-databaser, nav och medlems databaser har stöd för automatisk paus.
- Jobb databasen som används i elastiska jobb.

AutoPause förhindras tillfälligt under distributionen av vissa tjänste uppdateringar som kräver att databasen är online.  I sådana fall tillåts autopausen igen när tjänsten har uppdaterats.

### <a name="autoresuming"></a>Återupptar

Autoåterupptagande utlöses om något av följande villkor är uppfyllt när som helst:

|Funktion|Autoresume-utlösare|
|---|---|
|Autentisering och auktorisering|Logga in|
|Identifiering av hot|Aktivera/inaktivera inställningar för hot identifiering på databas-eller server nivå.<br>Ändra inställningarna för hot identifiering på databas-eller server nivå.|
|Dataidentifiering och -klassificering|Lägga till, ändra, ta bort eller Visa känslighets etiketter|
|Granskning|Visa gransknings poster.<br>Uppdaterar eller visar gransknings principen.|
|Datamaskning|Lägga till, ändra, ta bort eller Visa regler för data maskering|
|Transparent datakryptering|Visa status eller status för transparent data kryptering|
|Fråga (prestanda) data lager|Ändra eller Visa inställningar för frågearkivet; automatisk justering|
|Autojustera|Program och verifiering av rekommendationer för automatisk justering, till exempel automatisk indexering|
|Databas kopiering|Skapa databas som kopia.<br>Exportera till en BACPAC-fil.|
|SQL Data Sync|Synkronisering mellan hubb och medlems databaser som körs enligt ett konfigurerbart schema eller som utförs manuellt|
|Ändra vissa metadata för databasen|Lägger till nya Database-taggar.<br>Ändra Max virtuella kärnor, min virtuella kärnor eller AutoPause fördröjning.|
|SQL Server Management Studio (SSMS)|Om du använder SSMS-versioner som är äldre än 18,1 och öppnar ett nytt frågefönster för en databas på servern återupptas alla automatiskt pausade databaser på samma server. Det här problemet uppstår inte om du använder SSMS version 18,1 eller senare.|

Funktionen för att återuppta automatiskt utlöses även under distributionen av vissa tjänste uppdateringar som kräver att databasen är online.

### <a name="connectivity"></a>Anslutningar

Om en server lös databas har pausats kommer den första inloggningen att återuppta databasen och returnera ett fel som anger att databasen inte är tillgänglig med felkoden 40613. När databasen har återupptagits måste inloggningen göras om för att upprätta anslutningen. Databas klienter med logik för anslutnings försök ska inte behöva ändras.

### <a name="latency"></a>Svarstid

Svars tiden för autoresume och autopausning av en server lös databas är i regel 1 minut för att autoaktivera och 1-10 minuter att pausa.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding in in på Server lös beräknings nivå

Att skapa en ny databas eller flytta en befintlig databas till en server lös beräknings nivå följer samma mönster som att skapa en ny databas i en allokerad beräknings nivå och omfattar följande två steg.

1. Ange namnet på tjänst målet. Tjänst målet föreskriver tjänst nivån, maskin varu genereringen och Max virtuella kärnor. I följande tabell visas alternativen för tjänst målet:

   |Namn på tjänst målet|Tjänstnivå|Maskin varu generering|Max virtuella kärnor|
   |---|---|---|---|
   |GP_S_Gen5_1|Generellt syfte|Gen5|1|
   |GP_S_Gen5_2|Generellt syfte|Gen5|2|
   |GP_S_Gen5_4|Generellt syfte|Gen5|4|

2. Alternativt kan du ange den minsta virtuella kärnor och den automatiskt paus fördröjningen för att ändra standardvärdena. I följande tabell visas de tillgängliga värdena för dessa parametrar.

   |Parameter|Värde alternativ|Standardvärde|
   |---|---|---|---|
   |Minsta virtuella kärnor|Alla {0,5, 1, 2, 4} överskrider max virtuella kärnor|0,5 virtuella kärnor|
   |Pausa fördröjning|Lägst 60 minuter (1 timme)<br>Maximihalter 10080 minuter (7 dagar)<br>Steg om 60 minuter<br>Inaktivera autopausen:-1|60 minuter|

> [!NOTE]
> Att använda T-SQL för att flytta en befintlig databas till Server lös eller ändra dess beräknings storlek stöds inte för närvarande, men kan göras via Azure Portal eller PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Skapa ny databas i Server lös beräknings nivå 

#### <a name="use-azure-portal"></a>Använda Azure-portalen

Gå till [Snabbstart: Skapa en enskild databas i Azure SQL Database med hjälp av](sql-database-single-database-get-started.md)Azure Portal.

#### <a name="use-powershell"></a>Använd PowerShell

I följande exempel skapas en ny databas i Server lös beräknings nivån.  I det här exemplet anges det minsta virtuella kärnor, Max virtuella kärnor och den här paus fördröjningen.

```powershell
New-AzSqlDatabase `
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -ComputeModel Serverless `
  -Edition GeneralPurpose `
  -ComputeGeneration Gen5 `
  -MinVcore 0.5 `
  -MaxVcore 2 `
  -AutoPauseDelayInMinutes 720
```

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Flytta databasen från etablerade beräknings nivåer till Server lös beräknings nivå

#### <a name="use-powershell"></a>Använd PowerShell

I följande exempel flyttas en databas från den allokerade beräknings nivån till Server lös beräknings nivån. I det här exemplet anges det minsta virtuella kärnor, Max virtuella kärnor och den här paus fördröjningen.

```powershell
Set-AzSqlDatabase
  -ResourceGroupName $resourceGroupName `
  -ServerName $serverName `
  -DatabaseName $databaseName `
  -Edition GeneralPurpose `
  -ComputeModel Serverless `
  -ComputeGeneration Gen5 `
  -MinVcore 1 `
  -MaxVcore 4 `
  -AutoPauseDelayInMinutes 1440
```

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Flytta databasen från Server lös beräknings nivån till en allokerad beräknings nivå

En server lös databas kan flyttas till en allokerad beräknings nivå på samma sätt som en allokerad beräknings databas flyttas till en server lös beräknings nivå.

## <a name="modifying-serverless-configuration"></a>Ändra konfiguration utan Server

### <a name="maximum-vcores"></a>Högsta antal virtuella kärnor

#### <a name="use-powershell"></a>Använd PowerShell

Att ändra Max virtuella kärnor utförs med kommandot [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp `MaxVcore` av argumentet.

### <a name="minimum-vcores"></a>Lägsta antal virtuella kärnor

#### <a name="use-powershell"></a>Använd PowerShell

Att ändra den minsta virtuella kärnor utförs med kommandot [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp `MinVcore` av argumentet.

### <a name="autopause-delay"></a>Pausa fördröjning

#### <a name="use-powershell"></a>Använd PowerShell

Ändring av paus fördröjningen utförs med hjälp av kommandot [set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med `AutoPauseDelayInMinutes` argumentet.

## <a name="monitoring"></a>Övervakning

### <a name="resources-used-and-billed"></a>Använda och fakturerade resurser

Resurserna för en server lös databas kapslas av appaket, SQL-instans och entiteter för resurspooler för användare.

#### <a name="app-package"></a>Appaket

Appaketet är den yttre mest resurs hanterings gränserna för en databas, oavsett om databasen finns i en server lös eller allokerad beräknings nivå. Appaketet innehåller SQL-instansen och externa tjänster som tillsammans omfattar alla användar-och system resurser som används av en databas i SQL Database. Exempel på externa tjänster är R och full texts ökning. SQL-instansen dominerar vanligt vis den övergripande resursutnyttjande över Appaketet.

#### <a name="user-resource-pool"></a>Resurspool för användare

Resurspoolen är den inre resurs hanterings gränserna för en databas, oavsett om databasen finns i en server lös eller allokerad beräknings nivå. Användarens resurspool omfångerar CPU och IO för användar arbets belastningar som skapats av DDL-frågor som skapa och ändra och DML-frågor, till exempel SELECT, INSERT, UPDATE och DELETE. Dessa frågor representerar vanligt vis den mest betydande delen av användningen i Appaketet.

### <a name="metrics"></a>Mått

Mät värden för att övervaka resursanvändningen för Appaketet och poolen för en server lös databas visas i följande tabell:

|Entitet|Mått|Beskrivning|Enheter|
|---|---|---|---|
|Appaket|app_cpu_percent|Procent andelen av virtuella kärnor som används av appen i förhållande till högsta tillåtna virtuella kärnor för appen.|Procent|
|Appaket|app_cpu_billed|Mängden data som debiteras för appen under rapporterings perioden. Det belopp som betalas under perioden är produkten av det här måttet och vCore enhets pris. <br><br>Värdena för det här måttet bestäms genom agg regering över tid för maximalt CPU-använt och minne som används varje sekund. Om det använda beloppet är mindre än det lägsta belopp som har angetts som den lägsta virtuella kärnor och minsta mängden minne, faktureras det lägsta mängd som har allokerats. För att kunna jämföra CPU med minne i fakturerings syfte normaliseras minnet till enheter av virtuella kärnor genom att skala om mängden minne i GB med 3 GB per vCore.|vCore sekunder|
|Appaket|app_memory_percent|Procent andelen minne som används av appen i förhållande till maximalt minne som tillåts för appen.|Procent|
|Adresspool|cpu_percent|Procent andelen av virtuella kärnor som används av användar arbets belastning i förhållande till högsta tillåtna virtuella kärnor för användar arbets belastning.|Procent|
|Adresspool|data_IO_percent|Procent andel data IOPS som används av användar arbets belastning i förhållande till maximal data-IOPS som tillåts för användar arbets belastning.|Procent|
|Adresspool|log_IO_percent|Procent andel logg MB/s som används av användar arbets belastning i förhållande till maximalt antal loggar i MB/s som tillåts för användar arbets belastning.|Procent|
|Adresspool|workers_percent|Procent andel arbetare som används av användar arbets belastning i förhållande till max arbetare som tillåts för användar arbets belastning.|Procent|
|Adresspool|sessions_percent|Procent andel sessioner som används av användar arbets belastning i förhållande till högsta antal sessioner som tillåts för användar arbets belastning.|Procent|

### <a name="pause-and-resume-status"></a>Pausa och återuppta status

I Azure Portal visas databasens status i fönstret Översikt på servern som innehåller en lista över de databaser som den innehåller. Databasens status visas också i fönstret Översikt för-databasen.

Använd följande PowerShell-kommando för att fråga efter paus och återuppta status för en databas:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Resursbegränsningar

För resurs gränser, se [Server lös beräknings nivå](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).

## <a name="billing"></a>Fakturering

Den mängd data som faktureras är det högsta antal CPU-som används och minne som används varje sekund. Om mängden använt CPU och använt minne är mindre än den lägsta mängd som har skapats för varje, faktureras det etablerade beloppet. För att kunna jämföra CPU med minne i fakturerings syfte normaliseras minnet till enheter av virtuella kärnor genom att skala om mängden minne i GB med 3 GB per vCore.

- **Resurs debiterad**: CPU och minne
- **Fakturerat belopp**: vCore enhets pris * Max (min virtuella kärnor, virtuella kärnor som används, minimalt minne GB * 1/3, använt minnes utrymme * 1/3) 
- **Fakturerings frekvens**: Per sekund

VCore enhets pris i kostnad per vCore per sekund. Mer information finns på [sidan med Azure SQL Database priser](https://azure.microsoft.com/pricing/details/sql-database/single/) för vissa enhets priser i en specifik region.

Den totala mängden data som faktureras exponeras enligt följande mått:

- **Mått**: App_cpu_billed (vCore sekunder)
- **Definition**: Max (min-virtuella kärnor, virtuella kärnor som används, minimalt minne gb * 1/3, använt minnes utrymme * 1/3)
- **Rapport frekvens**: Per minut

Den här kvantiteten beräknas varje sekund och sammanställs över 1 minut.

Överväg en server lös databas som kon figurer ATS med 1 min vCore och 4 Max virtuella kärnor.  Detta motsvarar cirka 3 GB min minne och maximalt 12 GB minne.  Antag att fördröjningen för automatisk paus är inställd på 6 timmar och databasens arbets belastning är aktiv under de första två timmarna av en 24-timmarsperiod och annars inaktiv.    

I det här fallet faktureras databasen för beräkning och lagring under de första 8 timmarna.  Även om databasen är inaktiv från och med den andra timmen, faktureras den fortfarande för beräkning under de följande 6 timmarna baserat på den lägsta beräkning som etablerades när databasen är online.  Endast lagring faktureras under resten av den 24-timmarsperiod medan databasen pausas.

Mer exakt, beräknings fakturan i det här exemplet beräknas enligt följande:

|Tidsintervall|Virtuella kärnor som används varje sekund|GB som används varje sekund|Beräknings dimension faktureras|vCore sekunder debiteras över tidsintervallet|
|---|---|---|---|---|
|0:00-1:00|4|9|Virtuella kärnor som används|4 virtuella kärnor * 3600 sekunder = 14400 vCore sekunder|
|1:00-2:00|1|12|Använt minne|12 GB * 1/3 * 3600 sekunder = 14400 vCore sekunder|
|2:00-8:00|0|0|Minsta allokerade minne|3 GB * 1/3 * 21600 sekunder = 21600 vCore sekunder|
|8:00-24:00|0|0|Ingen beräkning har pausats|0 vCore sekunder|
|Totalt antal vCore sekunder debiteras över 24 timmar||||50400 vCore sekunder|

Anta att priset för beräknings enheten är $0.000073/vCore/Second.  Sedan debiteras beräkningen för den här 24-timmarsperiod produkten av priset för beräknings enheten och vCore sekunder fakturerat: $0.000073/vCore/Second * 50400 vCore sekunder = $3,68

## <a name="available-regions"></a>Tillgängliga regioner

Server lös beräknings nivån är tillgänglig i hela världen utom följande regioner: Australien, centrala, Kina, östra, Kina, norra, Frankrike, södra, Tyskland, centrala, Tyskland nordöstra, västra Indien, södra Korea, södra, västra, Storbritannien, norra, Storbritannien, södra, Storbritannien, västra och västra centrala USA.

## <a name="next-steps"></a>Nästa steg

- Information om hur du kommer [igång finns i snabb start: Skapa en enskild databas i Azure SQL Database med hjälp av](sql-database-single-database-get-started.md)Azure Portal.
- För resurs gränser, se [resurs gränser för Server lös beräknings nivå](sql-database-vCore-resource-limits-single-databases.md#general-purpose-service-tier-for-serverless-compute).
