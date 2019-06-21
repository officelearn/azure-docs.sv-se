---
title: Azure SQL-databas utan server (förhandsversion) | Microsoft Docs
description: Den här artikeln beskriver den nya nivån för beräkning utan server och jämför den med den befintliga etablerade beräkning-nivån
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein, carlrab
manager: craigg
ms.date: 06/12/2019
ms.openlocfilehash: afa575c9015cbb21386d23101b74456822dfa33c
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67275476"
---
# <a name="azure-sql-database-serverless-preview"></a>Azure SQL-databas utan server (förhandsversion)

Azure SQL-databas utan server (förhandsversion) är en Beräkningsnivån för enskilda databaser som automatiskt skalar beräkning baserat på arbetsbelastningen begäran och fakturor för den beräkning som används per sekund. Serverlös Beräkningsnivån pausar automatiskt databaser under inaktiva perioder när endast storage faktureras och återupptas automatiskt databaser när aktiviteten returnerar.

## <a name="serverless-compute-tier"></a>Serverlös beräkningsnivå

Beräkning utan Server-nivå för en enskild databas parametriserade genom ett intervall för automatisk skalning av beräkning och en autopause fördröjning.  Konfigurationen av dessa parametrar forma prestandaupplevelse databasen och beräkningar.

![serverlös fakturering](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance-configuration"></a>Prestandakonfiguration

- Den **minsta vCores** och **maximala vCores** är konfigurerbara parametrar som definierar en uppsättning beräkningskapacitet som är tillgängliga för databasen. Minne och i/o-gränserna är proportion till vCore-intervallet som angetts.  
- Den **autopause fördröjning** är en konfigurerbar parameter som definierar tidsperioden databasen måste vara inaktiv innan den pausas automatiskt. Databasen återupptas automatiskt vid nästa inloggning eller andra aktiviteter inträffar.  Du kan också kan autopausing inaktiveras.

### <a name="cost"></a>Kostnad

- Kostnaden för en databas utan Server är summan av beräkningskostnaden och kostnaden för lagring.
- När beräkning användningen är mellan lägsta och högsta gränsen som har konfigurerats, baseras beräkningskostnaden på vCore och minne som används.
- När beräkning användningen är under den konfigurerade min, baseras beräkningskostnaden på min virtuella kärnor och minsta minnesmängd som har konfigurerats.
- När databasen har pausats visas beräkningskostnaden är noll och endast lagringskostnader tillkommer.
- Kostnaden för lagring definieras i på samma sätt som den etablerade beräkning-nivån.

Mer kostnadsinformation, finns i [fakturering](sql-database-serverless.md#billing).

## <a name="scenarios"></a>Scenarier

Serverlös är prisprestanda som optimerats för enskilda databaser med återkommande, oförutsägbara användningsmönster som har råd med en fördröjning i beräkning värma upp efter inaktiva användning perioder. Den etablerade beräkning-nivån är däremot prisprestanda som är optimerad för enskilda databaser eller flera databaser i elastiska pooler med högre genomsnittlig användning som inte har råd eventuell fördröjning av beräkning värma upp.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenarier som passar bra för beräkning utan Server

- Enskilda databaser med återkommande, oförutsägbara användningsmönster bland perioder av inaktivitet och lägre genomsnittlig beräkning användning över tid.
- Enkla databaser på den etablerade Beräkningsnivån som ofta skala och kunder som vill delegera compute skalas om till tjänsten.
- Nya enskilda databaser utan användningshistorik där beräkning storlek är svårt eller omöjligt att uppskatta innan den distribueras i SQL-databas.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenarier som passar bra för etablerad beräkning

- Enskilda databaser med jämnare och förutsägbara användningsmönster och högre average compute användning över tid.
- Databaser som inte tolererar prestanda med de härrör från mer frekventa minne trimmar eller fördröjning i autoresuming från ett pausat tillstånd.
- Flera databaser med återkommande, oförutsägbara användningsmönster som kan sammanställas till elastiska pooler för bättre pris-och prestandaoptimering.

## <a name="comparison-with-provisioned-compute-tier"></a>Jämförelse med etablerade Beräkningsnivån

I följande tabell sammanfattas skillnader mellan den beräkning utan server och den etablerade Beräkningsnivån:

| | **Beräkning utan Server** | **Etablerad beräkning** |
|:---|:---|:---|
|**Databasanvändningsmönstret**| Tillfälligt, oförutsägbar användning med lägre genomsnittlig beräkning användning över tid. |  Jämnare användningsmönster med högre medeltal compute användning över tid eller flera databaser med elastiska pooler.|
| **Prestanda hanteringsarbete** |Lägre|Högre|
|**Compute skalning**|Automatisk|Manuellt|
|**Compute svarstider**|Lägre efter inaktiva perioderna|Omedelbar|
|**Fakturering kornighet**|Per sekund|Per timme|

## <a name="purchasing-model-and-service-tier"></a>Köp av modeller och tjänstnivå

SQL-databas utan Server är för närvarande stöds endast i nivån generell användning på Generation 5 maskinvara i vCore-köp av modellen.

## <a name="autoscaling"></a>Automatisk skalning

### <a name="scaling-responsiveness"></a>Skala svarstider

I allmänhet körs utan Server databaser på en dator med tillräckligt med kapacitet för att uppfylla resursbehov utan avbrott för något beräkning som begärts inom de gränser som anges av värdet för max virtuella kärnor. Ibland uppstår belastningsutjämning automatiskt om datorn inte kan uppfylla resursbehov inom några minuter. Till exempel om resursbehovet är 4 virtuella kärnor, men endast 2 virtuella kärnor är tillgängliga, kan sedan det ta upp till ett par minuter att belastningsutjämna innan 4 vCores tillhandahålls. Databasen förblir online under belastningsbalansering utom en kort period i slutet av åtgärden när anslutningar tas bort.

### <a name="memory-management"></a>Minneshantering

Minne för serverlös databaser frigöras överstiger ofta för etablerade beräkningen databaser. Detta är viktigt att kontrollera kostnader i utan server och kan påverka prestanda.

#### <a name="cache-reclamation"></a>Cache frigöring

Till skillnad från etablerade beräkningen databaser frigöras minne från SQL-cache från en databas utan Server när CPU- eller cache förbrukningen är låg.

- Cache-användning betraktas som låg när den totala storleken på de senast använda cache poster är lägre än ett tröskelvärde för en viss tidsperiod.
- När cachen frigöring utlöses cache Målstorlek reduceras stegvis till en bråkdel av den föregående storleken och frigöra endast fortsätter om användningen förblir låg.
- När cachen frigöring inträffar är principen för att välja cacheposter att ta bort samma val av princip för etablerade beräkningen databaser när minnesbelastning är hög.
- Cachestorleken minskar aldrig nedan minnesgräns min som definieras av min virtuella kärnor som kan konfigureras.

I både utan server och etablerade compute-databaser, cache poster kan tas bort om allt tillgängligt minne används.

#### <a name="cache-hydration"></a>Cachelagra hydrering

SQL-cacheminnet växer när data hämtas från disken på samma sätt och med samma hastighet som etablerade databaser. När databasen är upptagen, kan cacheminnet växer obegränsad upp till högst minnesgränsen.

## <a name="autopausing-and-autoresuming"></a>Autopausing och autoresuming

### <a name="autopausing"></a>Autopausing

Autopausing utlöses om samtliga följande villkor är uppfyllda under hela autopause fördröjningen:

- Antalet sessioner = 0
- CPU = 0 för användare av arbetsbelastningar som körs i poolen för användare

Ett alternativ har angetts för att inaktivera autopausing om så önskas.

Följande funktioner stöder inte autopausing.  Det vill säga om någon av följande funktioner används förblir sedan databasen online oavsett varaktigheten för inaktivitet för databasen:

- GEO-replikering (aktiv geo-replikering och automatisk redundans grupper).
- Långsiktig kvarhållning av säkerhetskopior (LTR).
- Synkroniseringsdatabasen som används i SQL data sync.

Autopausing är tillfälligt inte under distributionen av vissa uppdateringar av tjänsten som kräver att databasen är online.  I sådana fall kan blir autopausing tillåtet igen när tjänstuppdateringen har slutförts.

### <a name="autoresuming"></a>Autoresuming

Autoresuming utlöses om något av följande villkor är uppfyllt när som helst:

|Funktion|Autoresume utlösare|
|---|---|
|Autentisering och auktorisering|Inloggning|
|Identifiering av hot|Aktivering/inaktivering inställningarna för hotidentifiering på databas- eller servernivå.<br>Ändra inställningarna för hotidentifiering på databas- eller servernivå.|
|Dataidentifiering och -klassificering|Att lägga till, ändra, ta bort eller genom att visa etiketter för känslighet|
|Granskning|Visa granskningsposterna.<br>Uppdaterar eller visar granskningsprincip.|
|Datamaskning|Att lägga till, ändra, ta bort eller genom att visa datamaskning regler|
|Transparent datakryptering|Visa status eller status för transparent datakryptering|
|Query store är data (prestanda)|Ändra eller visa query store-inställningar. automatisk justering|
|Autotuning|Program- och verifiering av autotuning rekommendationer, till exempel automatisk indexering|
|Databasen kopieras|Skapa databas som kopia.<br>Exportera till en BACPAC-fil.|
|SQL-datasynkronisering|Synkronisering mellan hub och medlemmen databaser som körs på ett schema som konfigureras eller utförs manuellt|
|Ändra vissa databasmetadata|Lägger till den nya databasen taggar.<br>Ändra max virtuella kärnor, min virtuella kärnor eller autopause fördröjning.|
|SQL Server Management Studio (SSMS)|Med hjälp av SSMS version 18 eller senare och öppna ett nytt frågefönster för alla databaser i servern fortsätter alla automatisk pausats databaser på samma server. Det här problemet uppstår inte om med hjälp av SSMS version 17.9.1 med IntelliSense stänga av.|

Autoresuming utlöses också under distributionen av vissa uppdateringar av tjänsten som kräver att databasen är online.

### <a name="connectivity"></a>Anslutning

Om en databas utan Server är pausad, kommer sedan den första inloggningen återuppta databasen och returnera ett felmeddelande om att databasen är inte tillgänglig med felkod 40613. När databasen återupptas, måste inloggningen utföras igen om du vill upprätta en anslutning. Databas-klienter med logik behöver inte ändras.

### <a name="latency"></a>Svarstid

Svarstiden för autoresume och autopause en serverlös databas är vanligtvis ordningen på 1 minut till autoresume och 1 – 10 minuter att autopause.

## <a name="onboarding-into-serverless-compute-tier"></a>Onboarding till nivån för beräkning utan Server

Skapa en ny databas eller flytta en befintlig databas i en beräkning utan Server-nivå följer samma mönster som du skapar en ny databas i etablerad Beräkningsnivån och omfattar följande två steg.

1. Ange namn för tjänsten servicenivåmål. Tjänstmålet behandlar den tjänstnivån, maskinvara generation och max virtuella kärnor. I följande tabell visar service objektiva alternativen:

   |Namn på servicenivåmål för tjänsten|Tjänstenivå|Maskinvara-generering|Maximalt antal virtuella kärnor|
   |---|---|---|---|
   |GP_S_Gen5_1|Generellt syfte|Gen5|1|
   |GP_S_Gen5_2|Generellt syfte|Gen5|2|
   |GP_S_Gen5_4|Generellt syfte|Gen5|4|

2. Du kan också ange min virtuella kärnor och autopause fördröjning om du vill ändra standardvärdena. I följande tabell visas de tillgängliga värdena för dessa parametrar.

   |Parameter|Alternativ för värde|Standardvärde|
   |---|---|---|---|
   |Min vCores|Något av {0,5, 1, 2, 4} som inte överstiger max virtuella kärnor|0,5 virtuella kärnor|
   |Autopause fördröjning|Minimum: 360 minuter (6 timmar)<br>Maximalt: 10 080 minuter (7 dagar)<br>Steg: 60 minuter<br>Inaktivera autopause: -1|360 minuter|

> [!NOTE]
> Med T-SQL för att flytta en befintlig databas till utan server eller ändra dess beräkningsstorleken stöds inte för närvarande men kan göras via Azure portal eller PowerShell.

### <a name="create-new-database-in-serverless-compute-tier"></a>Skapa ny databas på nivån för beräkning utan Server 

#### <a name="use-azure-portal"></a>Använda Azure-portalen

Gå till [Snabbstart: Skapa en enskild databas i Azure SQL Database med Azure portal](sql-database-single-database-get-started.md).

#### <a name="use-powershell"></a>Använd PowerShell

I följande exempel skapas en ny databas på nivån för beräkning utan server.  Det här exemplet anger uttryckligen virtuella kärnor min, max virtuella kärnor och autopause fördröjning.

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

### <a name="move-database-from-provisioned-compute-tier-into-serverless-compute-tier"></a>Flytta databasen från etablerade Beräkningsnivån till nivån för beräkning utan Server

#### <a name="use-powershell"></a>Använd PowerShell

I följande exempel flyttar en databas från den etablerade Beräkningsnivån till beräkning utan Server-nivå. Det här exemplet anger uttryckligen virtuella kärnor min, max virtuella kärnor och autopause fördröjning.

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

### <a name="move-database-from-serverless-compute-tier-into-provisioned-compute-tier"></a>Flytta databasen från beräkning utan Server nivå till etablerade Beräkningsnivån

En databas utan Server kan flyttas till en etablerad Beräkningsnivån på samma sätt som att flytta en databas med etablerad beräkning i en beräkning utan Server-nivå.

## <a name="modifying-serverless-configuration"></a>Ändra utan Server-konfiguration

### <a name="maximum-vcores"></a>Högsta antal virtuella kärnor

#### <a name="use-powershell"></a>Använd PowerShell

Ändra den maximala vCores utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `MaxVcore` argumentet.

### <a name="minimum-vcores"></a>Lägsta antal virtuella kärnor

#### <a name="use-powershell"></a>Använd PowerShell

Ändra min vCores utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `MinVcore` argumentet.

### <a name="autopause-delay"></a>Autopause fördröjning

#### <a name="use-powershell"></a>Använd PowerShell

Ändra autopause fördröjningen utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `AutoPauseDelayInMinutes` argumentet.

## <a name="monitoring"></a>Övervakning

### <a name="resources-used-and-billed"></a>Resurser som används och faktureras

Resurser för en serverlös databas är inkapslade av app-paket och SQL-instans användarentiteter resource pool.

#### <a name="app-package"></a>App-paket

App-paket är den yttersta de flesta resource management gränsen för en databas, oavsett om databasen är i en serverlös eller etablerade Beräkningsnivån. App-paketet innehåller SQL-instansen och externa tjänster som tillsammans omfång alla användar- och resurser som används av en databas i SQL-databas. Exempel på externa tjänster är R- och fulltextsökning. SQL-instansen dominerar Allmänt övergripande Resursanvändning i app-paket.

#### <a name="user-resource-pool"></a>Användarresurspool

Resurspoolen användare är inre de flesta resource management-gränsen för en databas, oavsett om databasen är i en serverlös eller etablerade Beräkningsnivån. Användaren resource pool-scope processor- och IO för arbetsbelastning för användare som genererats av DDL-frågor, till exempel skapa och ändra och DML-frågor som Markera, infoga, uppdatera och ta bort. De här frågorna representerar vanligtvis den mest väsentlig del av användning i app-paket.

### <a name="metrics"></a>Mått

Mått för övervakning av resursanvändningen för programpoolen för paketet och användaren av en databas utan Server finns i följande tabell:

|Entitet|Mått|Beskrivning|Enheter|
|---|---|---|---|
|App-paket|app_cpu_percent|Procentandel virtuella kärnor som används av appen i förhållande till max virtuella kärnor tillåts för appen.|Procent|
|App-paket|app_cpu_billed|Mängden beräkning som faktureras för appen under rapporteringsperioden. Beloppet betalats under den här perioden är produkten av det här måttet och vCore per enhet. <br><br>Värdena för det här måttet bestäms genom att sammanställa över tid som används för det maximala antalet på CPU och minne som används varje sekund. Om den mängd som används är mindre än den minsta mängden som etablerats som angetts av min virtuella kärnor och minsta minnesmängd, debiteras det minsta etablerade. För att kunna jämföra CPU med minne för fakturering, normaliserade minne i enheter för virtuella kärnor av rescaling mängden minne i GB som 3 GB per vCore.|vCore-sekunder|
|App-paket|app_memory_percent|Procentandelen minne som används av appen i förhållande till högsta mängd minne som tillåts för appen.|Procent|
|Användare-pool|cpu_percent|Procentandel virtuella kärnor som används av arbetsbelastning för användare i förhållande till max virtuella kärnor tillåts för arbetsbelastning per användare.|Procent|
|Användare-pool|data_IO_percent|Procentandel av data IOPS som används av arbetsbelastning för användare i förhållande till max data IOPS tillåten för arbetsbelastning per användare.|Procent|
|Användare-pool|log_IO_percent|Procent av loggen MB/s som används av arbetsbelastning för användare i förhållande till max log MB/s tillåts för arbetsbelastning per användare.|Procent|
|Användare-pool|workers_percent|Procentandel arbetare som används av arbetsbelastning för användare i förhållande till max arbetare som tillåts för arbetsbelastning per användare.|Procent|
|Användare-pool|sessions_percent|Procentandel sessioner som används av arbetsbelastning för användare i förhållande till maximalt antal sessioner som tillåts för arbetsbelastning per användare.|Procent|

### <a name="pause-and-resume-status"></a>Pausa och återuppta status

I Azure-portalen visas databasens status i översiktsfönstret för den server som visar en lista över databaser som den innehåller. Databasens status visas också i översiktsfönstret för databasen.

Använd följande PowerShell-kommando för att fråga pausen och återuppta status för en databas:

```powershell
Get-AzSqlDatabase `
  -ResourceGroupName $resourcegroupname `
  -ServerName $servername `
  -DatabaseName $databasename `
  | Select -ExpandProperty "Status"
```

## <a name="resource-limits"></a>Resursbegränsningar

Resursbegränsningar, se [beräkning utan Server-nivå](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier)

## <a name="billing"></a>Fakturering

Mängden beräkning som faktureras är det maximala antalet på Processorn som används och minne som används varje sekund. Om används för hur mycket Processorkraft och minne som används är mindre än den minsta mängden som tillhandahållits för var och en, debiteras den allokerade mängden. För att kunna jämföra CPU med minne för fakturering, normaliserade minne i enheter för virtuella kärnor av rescaling mängden minne i GB som 3 GB per vCore.

- **Resurs som faktureras**: Processor och minne
- **Fakturerat belopp**: vCore enhetspriset * max (min virtuella kärnor, virtuella kärnor som används, minsta minnesmängd GB * 1/3 minne GB används * 1/3) 
- **Fakturering frekvens**: Per sekund

Enhetspriset vCore i kostnaden per vCore per sekund. Referera till den [sidan med priser för Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) för specifika a-priserna i en viss region.

Mängden beräkning som faktureras exponeras av följande mått:

- **Mått**: app_cpu_billed (vCore sekunder)
- **Definition**: max (min virtuella kärnor, virtuella kärnor som används, minsta minnesmängd GB * 1/3 minne GB används * 1/3)
- **Telemetrirapportering**: Per minut

Den här datamängden beräknas varje sekund och aggregerat över 1 minut.

Överväg en serverlös databas som har konfigurerats med 1 min vCore och 4 max virtuella kärnor.  Detta motsvarar cirka 3 GB minne för min och max 12 GB-minne.  Anta att automatisk pausning fördröjningen är inställd på 6 timmar och databas-arbetsbelastning är aktiv under de första 2 timmarna på en 24-timmarsperiod och annars inaktiva.    

I det här fallet debiteras databasen för beräkning och lagring under de första 8 timmarna.  Även om databasen är inaktiv startar efter den andra timmen, debiteras det fortfarande för beräkning i de efterföljande 6 timmar baserat på minsta compute etableras när databasen är online.  När databasen är pausat debiteras endast lagring under resten av 24-timmarsperiod.

Mer exakt beräknas beräkning fakturan i det här exemplet enligt följande:

|Tidsintervall|virtuella kärnor som används för varje sekund|GB används varje sekund|Compute-dimension som faktureras|vCore-sekunder debiteras under tidsintervall|
|---|---|---|---|---|
|0:00-1:00|4|9|virtuella kärnor som används|4 virtuella kärnor * 3600 sekunder = 14400 vCore sekunder|
|1:00-2:00|1|12|Använt minne|12 GB * 1/3 * 3 600 sekunder = 14400 vCore sekunder|
|2:00-8:00|0|0|Minsta-minnesmängd som etablerats|3 GB * 1/3 * 21600 sekunder = 21600 vCore sekunder|
|8:00-24:00|0|0|Ingen beräkning debiteras medan pausats|0 vCore sekunder|
|Totalt antal vCore sekunder debiteras under 24 timmar||||50400 vCore sekunder|

Anta att enhetspriset beräkning är $0.000073/vCore/second.  Beräkningarna debiteras du för den här 24-timmarsperiod är produkten av beräkning pris- och vCore enhetssekunder faktureras: $0.000073/vCore/second * 50400 vCore sekunder = $3.68

## <a name="available-regions"></a>Tillgängliga regioner

Nivå för beräkning utan Server är tillgänglig över hela världen utom följande regioner: Australien, centrala, Kina, östra, Kina, norra, Frankrike, södra, Tyskland, centrala, Tyskland, nordöstra, Indien, västra, Korea, södra, Sydafrika, västra, Storbritannien, norra, Storbritannien, södra, Storbritannien, västra och USA, västra centrala.

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att se [snabbstarten: Skapa en enskild databas i Azure SQL Database med Azure portal](sql-database-single-database-get-started.md).
- Resursbegränsningar, se [serverlösa compute nivån resursbegränsningar](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
