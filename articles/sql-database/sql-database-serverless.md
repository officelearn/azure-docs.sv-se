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
ms.date: 05/20/2019
ms.openlocfilehash: 57f2c38ce0479f43d7f24de8d1feb554517bcc69
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2019
ms.locfileid: "65951476"
---
# <a name="sql-database-serverless-preview"></a>SQL Database utan server (förhandsversion)

## <a name="what-is-the-serverless-compute-tier"></a>Vad är på nivån för beräkning utan Server

SQL-databas utan server (förhandsversion) är en Beräkningsnivån fakturor för ett belopp av compute används av en enskild databas per sekund. Serverlös är prisprestanda som optimerats för enskilda databaser med återkommande, oförutsägbara användningsmönster som har råd med en fördröjning i beräkning värma upp efter inaktiva användning perioder.

En databas på nivån för beräkning utan Server parametriserade av compute-intervallet som kan användas och en autopause fördröjning.

![serverlös fakturering](./media/sql-database-serverless/serverless-billing.png)

### <a name="performance"></a>Prestanda

- `MinVcore` och `MaxVcore` är konfigurerbara parametrar som definierar en uppsättning beräkningskapacitet som är tillgängliga för databasen. Minne och i/o-gränserna är proportion till vCore-intervallet som angetts.  
- Autopause fördröjningen är en konfigurerbar parameter som definierar tidsperioden som databasen måste vara inaktiv innan den pausas automatiskt. Databasen återupptas automatiskt vid nästa inloggning inträffar.

### <a name="pricing"></a>Prissättning

- Den totala avgiften för en databas utan Server är summan av beräkning faktura och storage-fakturan.
Fakturering för beräkningen baseras på mängden virtuella kärnor som används och minne som används per sekund.
- Minsta beräkningarna debiteras baseras på min virtuella kärnor och minsta minnesmängd.
- När databasen är pausat debiteras endast lagring.

## <a name="scenarios"></a>Scenarier

Serverlös är prisprestanda som optimerats för enskilda databaser med återkommande, oförutsägbara användningsmönster som har råd med en fördröjning i beräkning värma upp efter inaktiva användning perioder. Den etablerade beräkning-nivån är däremot prisprestanda som optimerats för enkel eller grupperade databaser med högre genomsnittlig användning som inte har råd eventuell fördröjning av beräkning värma upp.

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenarier som passar bra för beräkning utan Server

- Enskilda databaser med återkommande, oförutsägbar användning mönster bland perioder av inaktivitet kan dra nytta av betalas utifrån fakturering per sekund för den beräkning som används.
- Enskilda databaser med resursbehov som är svårt att förutse och kunder som vill delegera compute storlek till tjänsten.
- Enskilda databaser i den etablerade Beräkningsnivån ändras så ofta prestandanivåer.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenarier som passar bra för etablerad beräkning

- Enskilda databaser med flera vanliga samt mer betydande compute användning över tid.
- Databaser som inte tolererar prestanda med de härrör från mer frekventa minne trimmar eller fördröjning i autoresuming från ett pausat tillstånd.
- Flera databaser med återkommande, oförutsägbara användningsmönster som kan sammanställas till en enskild server och använder elastiska pooler för bättre prisoptimering.

## <a name="comparison-with-provisioned-compute-tier"></a>Jämförelse med etablerade Beräkningsnivån

I följande tabell sammanfattas skillnader mellan den beräkning utan server och den etablerade Beräkningsnivån:

| | **Beräkning utan Server** | **Etablerad beräkning** |
|:---|:---|:---|
|**Typiskt Användarscenario**| Databaser med återkommande, oförutsägbar användning bland inaktiva perioder. | Databaser eller elastiska pooler med mer normala användning.|
| **Prestanda hanteringsarbete** |Lägre|Högre|
|**Compute skalning**|Automatiskt|Manuell|
|**Compute svarstider**|Lägre efter inaktiva perioderna|Omedelbar|
|**Fakturering kornighet**|Per sekund|Per timme|

## <a name="purchasing-model-and-service-tier"></a>Köp av modeller och tjänstnivå

SQL-databas utan Server är för närvarande stöds endast i nivån generell användning på Generation 5 maskinvara i vCore-köp av modellen.

## <a name="autoscaling"></a>Automatisk skalning

### <a name="scaling-responsiveness"></a>Skala svarstider

I allmänhet databaser körs på en dator med tillräckligt med kapacitet för att uppfylla resursbehov utan avbrott för vilken mängd begärts inom gränserna för beräkning i `maxVcores` värde. Ibland uppstår belastningsutjämning automatiskt om datorn inte kan uppfylla resursbehov inom några minuter. Databasen förblir online under belastningsbalansering utom en kort period i slutet av åtgärden när anslutningar tas bort.

### <a name="memory-management"></a>Minneshantering

Minne för serverlös databaser frigöras överstiger ofta för etablerade beräkningen databaser. Detta är viktigt att kontrollera kostnader i utan server och kan påverka prestanda.

#### <a name="cache-reclaiming"></a>Cachelagra frigöra

Till skillnad från etablerade beräkningen databaser frigöras minne från SQL-cache från en databas utan Server när CPU- eller cache förbrukningen är låg.

- Cache-användning betraktas som låg när den totala storleken på de senast använda cache poster är lägre än ett tröskelvärde för en viss tidsperiod.
- När cachen frigöring utlöses cache Målstorlek reduceras stegvis till en bråkdel av den föregående storleken och frigöra endast fortsätter om användningen förblir låg.
- När cachen frigöring inträffar är principen för att välja cacheposter att ta bort samma val av princip för etablerade beräkningen databaser när minnesbelastning är hög.
- Cachestorleken minskar aldrig under det minsta minnet som definieras av minsta virtuella kärnor, som kan konfigureras.

I både utan server och etablerade compute-databaser, cache poster kan tas bort om allt tillgängligt minne används.

#### <a name="cache-hydration"></a>Cachelagra hydrering

SQL-cacheminnet växer när data hämtas från disken på samma sätt och med samma hastighet som etablerade databaser. När databasen är upptagen, kan cacheminnet växer obegränsad upp till högst minnesgränsen.

## <a name="autopause-and-autoresume"></a>Autopause och autoresume

### <a name="autopause"></a>Autopause

Autopause utlöses om samtliga följande villkor är uppfyllda under hela autopause fördröjningen:

- Antalet sessioner = 0
- CPU = 0 (för användare av arbetsbelastningar som körs i poolen för användare)

Ett alternativ har angetts för att inaktivera autopause om så önskas.

### <a name="autoresume"></a>Autoresume

Autoresume utlöses om något av följande villkor är uppfyllt när som helst:

|Funktion|Autoresume utlösare|
|---|---|
|Autentisering och auktorisering|Inloggning|
|Hotidentifiering|Aktivering/inaktivering av inställningarna för hotidentifiering på databas- eller servernivå<br>Ändra inställningarna för hotidentifiering på databas- eller servernivå|
|Dataidentifiering och -klassificering|Att lägga till, ändra, ta bort eller genom att visa etiketter för känslighet|
|Granskning|Visa granskningsposterna.<br>Uppdatera eller visa granskningsprincip|
|Datamaskning|Att lägga till, ändra, ta bort eller genom att visa datamaskning regler|
|Transparent datakryptering|Visa status eller status för transparent datakryptering|
|Query store är data (prestanda)|Ändra eller visa query store-inställningar. automatisk justering|
|Autotuning|Program- och verifiering av autotuning rekommendationer, till exempel automatisk indexering|
|Databasen kopieras|Skapa databas som en kopia<br>Exportera till en BACPAC-fil|
|SQL-datasynkronisering|Synkronisering mellan hub och medlemmen databaser som körs på ett schema som konfigureras eller utförs manuellt|
|Ändra vissa databasmetadata|Att lägga till den nya databasen taggar<br>Ändra max vCores, min virtuella kärnor, autopause fördröjning|
|SQL Server Management Studio (SSMS)|Med hjälp av SSMS version 18 eller senare och öppna ett nytt frågefönster för alla databaser i servern fortsätter alla automatisk pausats databaser på samma server. Det här problemet uppstår inte om med hjälp av SSMS version 17.9.1 med IntelliSense stänga av.|

### <a name="connectivity"></a>Anslutningar

Om en databas utan Server är pausad, kommer sedan den första inloggningen återuppta databasen och returnera ett felmeddelande om att databasen är inte tillgänglig med felkod 40613. När databasen återupptas, måste inloggningen utföras igen om du vill upprätta en anslutning. Databas-klienter med logik behöver inte ändras.

### <a name="latency"></a>Svarstid

Svarstiden för autopause eller autoresume en serverlös databas är vanligtvis i storleksordningen 1 minut.

### <a name="feature-support"></a>Funktioner som stöds

Följande funktioner stöder inte autopausing och autoresuming. Det vill säga om någon av följande funktioner används förblir sedan databasen online oavsett tid av inaktivitet för databasen:

- GEO-replikering (aktiv geo-replikering och automatisk redundansgrupper)
- Långsiktig kvarhållning av säkerhetskopior (LTR)
- Synkroniseringsdatabasen som används i SQL data sync.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Registreringen till nivån för beräkning utan Server

Skapa en ny databas eller flytta en befintlig databas i en beräkning utan Server-nivå följer samma mönster som du skapar en ny databas i etablerats Beräkningsnivån och omfattar följande två steg:

1. Ange namn för tjänsten servicenivåmål. Tjänstmålet behandlar den tjänstnivå och maskinvara generation maximala virtuella kärnor. I följande tabell visar service objektiva alternativen:

   |Namn på servicenivåmål för tjänsten|Tjänstnivå|Maskinvara-generering|Max virtuella kärnor|
   |---|---|---|---|
   |GP_S_Gen5_1|Allmän|Gen5|1|
   |GP_S_Gen5_2|Allmän|Gen5|2|
   |GP_S_Gen5_4|Allmän|Gen5|4|

2. Du kan också ange den minsta virtuella kärnor och autopause fördröjningen om du vill ändra standardvärdena. I följande tabell visas de tillgängliga värdena för dessa parametrar.

   |Parameter|Alternativ för värde|Standardvärde|
   |---|---|---|---|
   |Lägsta antal virtuella kärnor|Något av {0,5, 1, 2, 4} som inte överstiger max virtuella kärnor|0,5 virtuella kärnor|
   |Autopause fördröjning|Min: 360 minuter (6 timmar)<br>Max: 10 080 minuter (7 dagar)<br>Steg: 60 minuter<br>Inaktivera autopause: -1|360 minuter|

> [!NOTE]
> Med T-SQL för att flytta en befintlig databas till utan server eller ändra dess beräkningsstorleken stöds inte för närvarande men kan göras via Azure portal eller PowerShell.

### <a name="create-new-database-using-the-azure-portal"></a>Skapa ny databas med Azure portal

Gå till [Snabbstart: Skapa en enskild databas i Azure SQL Database med Azure portal](sql-database-single-database-get-started.md).

### <a name="create-new-database-using-powershell"></a>Skapa ny databas med hjälp av PowerShell

I följande exempel skapas en ny databas på nivån för beräkning utan server som definieras av tjänstmål med namnet GP_S_Gen5_4 med standardvärden för min virtuella kärnor och autopause fördröjningen.

Serverlös kräver en nyare version av PowerShell än vad som är för närvarande i galleriet, så kör `Update-Module Az.Sql` att hämta de senaste utan serverlösa-aktiverade cmdletarna.

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
  -AutoPauseDelay 720
```

### <a name="move-existing-database-into-the-serverless-compute-tier"></a>Flytta befintlig databas till nivån för beräkning utan Server

I följande exempel flyttas en befintlig enskild databas från den etablerade Beräkningsnivån till nivån för beräkning utan server. Det här exemplet anger uttryckligen virtuella kärnor min, max virtuella kärnor och autopause fördröjning.

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
  -AutoPauseDelay 1440
```

### <a name="move-a-database-out-of-the-serverless-compute-tier"></a>Flytta en databas från beräkning utan Server-nivå

En databas utan Server kan flyttas till en etablerad Beräkningsnivån på samma sätt som att flytta en databas med etablerad beräkning i en beräkning utan Server-nivå.

## <a name="modify-serverless-configuration-parameters"></a>Ändra serverlös konfigurationsparametrar

### <a name="maximum-vcores"></a>Högsta antal virtuella kärnor

Ändra den maximala vCores utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `MaxVcore` argumentet.

### <a name="minimum-vcores"></a>Lägsta antal virtuella kärnor

Ändra den minsta virtuella kärnor utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `MinVcore` argumentet.

### <a name="autopause-delay"></a>Autopause fördröjning

Ändra autopause fördröjningen utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `AutoPauseDelay` argumentet.

## <a name="monitor-serverless-database"></a>Övervaka utan Server-databas

### <a name="resources-used-and-billed"></a>Resurser som används och faktureras

Resurser för en serverlös databas är inkapslade av följande enheter:

#### <a name="app-package"></a>App-paket

App-paket är den yttersta de flesta resource management gränsen för en databas, oavsett om databasen är i en serverlös eller etablerade Beräkningsnivån. App-paketet innehåller SQL-instansen och externa tjänster som tillsammans omfång alla användar- och resurser som används av en databas i SQL-databas. Exempel på externa tjänster är R- och fulltextsökning. SQL-instansen dominerar Allmänt övergripande Resursanvändning i app-paket.

#### <a name="user-resource-pool"></a>Användarresurspool

Resurspoolen användare är inre de flesta resource management-gränsen för en databas, oavsett om databasen är i en serverlös eller etablerade Beräkningsnivån. Användaren resurspoolen scope processor- och IO för arbetsbelastning för användare som genererats av DDL-frågor (till exempel CREATE, ALTER, etc.) och DML-frågor (till exempel väljer, infoga, uppdatera, ta bort osv). De här frågorna representerar vanligtvis den mest väsentlig del av användning i app-paket.

### <a name="metrics"></a>Mått

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
____

> [!NOTE]
> Mått i Azure-portalen är tillgängliga i fönstret databas för en enskild databas under **övervakning**.

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

- **Resurs som faktureras**: CPU och minne
- **Mängden debiteras ($)**: vCore enhetspriset * max (min virtuella kärnor, virtuella kärnor som används, minsta minnesmängd GB * 1/3 minne GB används * 1/3) 
- **Fakturering frekvens**: Per sekund

Enhetspriset vCore i kostnaden per vCore per sekund. Referera till den [sidan med priser för Azure SQL Database](https://azure.microsoft.com/pricing/details/sql-database/single/) för specifika a-priserna i en viss region.

Mängden beräkning som faktureras exponeras av följande mått:

- **Mått**: app_cpu_billed (vCore sekunder)
- **Definition**: max (min virtuella kärnor, virtuella kärnor som används, minsta minnesmängd GB * 1/3 minne GB används * 1/3)
- **Telemetrirapportering**: Per minut

Den här datamängden beräknas varje sekund och aggregerat över 1 minut.

Överväg en serverlös databas som har konfigurerats med 1 min vCore och 4 max virtuella kärnor.  Detta motsvarar cirka 3 GB minne för min och max 12 GB-minne.  Anta att automatisk pausning fördröjningen är inställd på 6 timmar och databas-arbetsbelastning är aktiv under de första 2 timmarna på en 24-timmarsperiod och annars inaktiva.    

I det här fallet debiteras databasen för beräkning och lagring under de första 8 timmarna.  Även om databasen är inaktiv startar efter den andra timmen, debiteras det fortfarande för beräkning i de efterföljande 6 timmar baserat på minsta compute etableras när databasen är online.  När databasen är pausat debiteras endast lagring under resten av 24-timmarsperiod.

Mer exakt beräknas beräkning fakturan i det här exemplet enligt följande:

|Tidsintervall|virtuella kärnor som används för varje sekund|GB används varje sekund|Compute-dimension som faktureras|vCore-sekunder debiteras under tidsintervall|
|---|---|---|---|---|
|0:00-1:00|4|9|virtuella kärnor som används|4 virtuella kärnor * 3600 sekunder = 14400 vCore sekunder|
|1:00-2:00|1|12|Använt minne|12 Gb * 1/3 * 3 600 sekunder = 14400 vCore sekunder|
|2:00-8:00|0|0|Minsta-minnesmängd som etablerats|3 Gb * 1/3 * 21600 sekunder = 21600 vCore sekunder|
|8:00-24:00|0|0|Ingen beräkning debiteras medan pausats|0 vCore sekunder|
|Totalt antal vCore sekunder debiteras under 24 timmar||||50400 vCore sekunder|

Anta att enhetspriset beräkning är $0.000073/vCore/second.  Beräkningarna debiteras du för den här 24-timmarsperiod är produkten av beräkning pris- och vCore enhetssekunder faktureras: $0.000073/vCore/second * 50400 vCore sekunder = $3.68

## <a name="available-regions"></a>Tillgängliga regioner

Nivå för beräkning utan Server är tillgänglig i alla regioner förutom följande regioner: Australien, centrala, Kina, östra, Kina, norra, Frankrike, södra, Tyskland, centrala, Tyskland, nordöstra, Indien, västra, Korea, södra, Sydafrika, västra, Storbritannien, norra, Storbritannien, södra, Storbritannien, västra och USA, västra centrala

## <a name="next-steps"></a>Nästa steg

- Kom igång genom att se [snabbstarten: Skapa en enskild databas i Azure SQL Database med Azure portal](sql-database-single-database-get-started.md).
- Resursbegränsningar, se [serverlösa compute nivån resursbegränsningar](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).
