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
ms.date: 05/07/2019
ms.openlocfilehash: 2ab8f272fc264f153144803be772d381c1780512
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65143270"
---
# <a name="sql-database-serverless-preview"></a>SQL Database utan server (förhandsversion)

## <a name="what-is-the-serverless-compute-tier"></a>Vad är på nivån för beräkning utan Server

SQL-databas utan server (förhandsversion) är en Beräkningsnivån fakturor för ett belopp av compute används av en enskild databas per sekund. Serverlös är priset perf-optimerade för enskilda databaser med oregelbundna användningsmönstren som har råd med en fördröjning i beräkning värma upp efter inaktiva användning perioder.
Offentligt tillgängliga erbjudanden i SQL Database idag faktura för ett belopp av compute däremot etablerade per timme. Den här etablerade Beräkningsnivån är priset perf-optimerad för enskilda databaser eller elastiska pooler med högre genomsnittlig användning som inte har råd eventuell fördröjning av beräkning värma upp.

En databas på serverlös datornivå parametriserade av compute-intervallet som kan användas och en autopause fördröjning.

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

Serverlös är prisprestanda som optimerats för enskilda databaser med oregelbundna användningsmönstren som har råd med en fördröjning i beräkning värma upp efter inaktiva användning perioder. Den etablerade beräkning-nivån är prisprestanda som optimerats för enkel eller grupperade databaser med högre genomsnittlig användning som inte har råd eventuell fördröjning av beräkning värma upp.

I följande tabell jämförs beräkning utan Server-nivå med etablerade-Beräkningsnivån:

||Serverlös databearbetning|Etablerad beräkning|
|---|---|---|
|**Typiskt Användarscenario**|Databaser med oregelbundna, oförutsägbar användning bland inaktiva perioder|Databaser eller elastiska pooler med mer normala användning|
|**Prestanda hanteringsarbete**|Lägre|Högre|
|**Compute skalning**|Automatisk|Manuellt|
|**Compute svarstider**|Lägre efter inaktiva perioderna|Omedelbar|
|**Fakturering kornighet**|Per sekund|Per timme|
|

### <a name="scenarios-well-suited-for-serverless-compute"></a>Scenarier som passar bra för beräkning utan Server

- Enskilda databaser med oregelbundna användningsmönstren bland perioder av inaktivitet kan dra nytta av betalas utifrån fakturering per sekund för den beräkning som används.
- Enskilda databaser med resursbehov som är svårt att förutse och kunder som vill delegera compute storlek till tjänsten.
- Enskilda databaser i den etablerade Beräkningsnivån ändras så ofta prestandanivåer.

### <a name="scenarios-well-suited-for-provisioned-compute"></a>Scenarier som passar bra för etablerad beräkning

- Enskilda databaser med flera vanliga samt mer betydande compute användning över tid.
- Databaser som inte tolererar prestanda med de härrör från mer frekventa minne trimmar eller fördröjning i autoresuming från ett pausat tillstånd.
- Flera databaser med oregelbundna användningsmönstren som kan sammanställas till en enskild server och använder elastiska pooler för bättre prisoptimering.


## <a name="purchasing-model-and-service-tier"></a>Köp av modeller och tjänstnivå

SQL-databas utan Server är för närvarande stöds endast i nivån generell användning på Generation 5 maskinvara i vcore-köp av modellen.

## <a name="autoscaling"></a>Automatisk skalning

### <a name="scaling-responsiveness"></a>Skala svarstider

I allmänhet databaser körs på en dator med tillräckligt med kapacitet för att uppfylla resursbehov utan avbrott för vilken mängd begärts inom gränserna för beräkning i `maxVcores` värde. Ibland uppstår belastningsutjämning automatiskt om datorn inte kan uppfylla resursbehov inom några minuter. Databasen förblir online under belastningsbalansering utom en kort period i slutet av åtgärden när anslutningar tas bort.

### <a name="memory-management"></a>Minneshantering

Minne för serverlös databaser frigöras överstiger ofta för etablerade databaser. Det här beteendet är viktigt att kontrollera kostnader i utan server. Till skillnad från etablerade beräkningen frigöras minne från SQL-cache från en databas utan Server när CPU- eller cache förbrukningen är låg.

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
|Ändra vissa databasmetadata|Att lägga till den nya databasen taggar<br>Ändra max vcores, min virtuella kärnor, autopause fördröjning|
|SQL Server Management Studio (SSMS)|Med hjälp av SSMS version 18 eller senare och öppna ett nytt frågefönster för alla databaser i servern fortsätter alla automatisk pausats databaser på samma server. Det här problemet uppstår inte om med hjälp av SSMS version 17.9.1 med IntelliSense stänga av.|

### <a name="connectivity"></a>Anslutning

Om en serverlös databaser är pausad, kommer sedan den första inloggningen återuppta databasen och returnera ett felmeddelande om att databasen är inte tillgänglig. När databasen återupptas, måste inloggningen utföras igen om du vill upprätta en anslutning. Databas-klienter med logik behöver inte ändras.

### <a name="latency"></a>Svarstid

Svarstiden för autopause eller autoresume en serverlös databas är vanligtvis i storleksordningen 1 minut.

### <a name="feature-support"></a>Funktioner som stöds

Följande funktioner stöder inte autopausing och autoresuming. Det vill säga om någon av följande funktioner används förblir sedan databasen online oavsett tid av inaktivitet för databasen:

- GEO-replikering (aktiv geo-replikering och automatisk redundansgrupper)
- Långsiktig kvarhållning av säkerhetskopior (LTR)
- Synkroniseringsdatabasen som används i SQL data sync.


## <a name="on-boarding-into-the-serverless-compute-tier"></a>Registreringen till nivån för beräkning utan Server

Skapa en ny databas eller flytta en befintlig databas i en beräkning utan Server-nivå följer samma mönster som du skapar en ny databas i etablerats Beräkningsnivån och omfattar följande två steg:

1. Ange namn för tjänsten servicenivåmål. I följande tabell visar tillgängliga tjänstnivå och storlekar som är tillgängliga i den offentliga förhandsversionen.

   |Tjänstenivå|Beräkna storlek|
   |---|---|
   |Generellt syfte|GP_S_Gen5_1|
   |Generellt syfte|GP_S_Gen5_2|
   |Generellt syfte|GP_S_Gen5_4|

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

I följande exempel flyttas en befintlig enskild databas från den etablerade Beräkningsnivån till nivån för beräkning utan server. Det här exemplet används standardvärden för min virtuella kärnor, maximal virtuella kärnor och autopause fördröjning.

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

Ändra den maximala vCores utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `MinVcore` argumentet.

### <a name="autopause-delay"></a>Autopause fördröjning

Ändra den maximala vCores utförs med hjälp av den [Set-AzSqlDatabase](https://docs.microsoft.com/powershell/module/az.sql/set-azsqldatabase) i PowerShell med hjälp av den `AutoPauseDelay` argumentet.

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
|App-paket|app_cpu_billed|Mängden beräkning som faktureras för appen under rapporteringsperioden. Beloppet betalats under den här perioden är produkten av det här måttet och vCore per enhet.<br>Värdena för det här måttet bestäms genom att sammanställa över tid som används för det maximala antalet på CPU och minne som används varje sekund.<br>Om den mängd som används är mindre än den minsta mängden som etablerats som angetts av min virtuella kärnor och minsta minnesmängd, debiteras det minsta etablerade.  För att kunna jämföra CPU med minne för fakturering, normaliserade minne i enheter för virtuella kärnor av rescaling mängden minne i GB som 3 GB per vCore.|vCore-sekunder|
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

Mängden beräkning som faktureras varje sekund är det maximala antalet på Processorn som används och minne som används varje sekund. Om används för hur mycket Processorkraft och minne som används är mindre än den minsta mängden som tillhandahållits för var och en, debiteras den allokerade mängden. För att kunna jämföra CPU med minne för fakturering, normaliserade minne i enheter för virtuella kärnor av rescaling mängden minne i GB som 3 GB per vCore.

- **Resurs som faktureras**: CPU och minne
- **Mängden debiteras ($)**: vCore enhetspriset * max (min virtuella kärnor, virtuella kärnor som används, minsta minnesmängd GB * 1/3 minne GB används * 1/3) 
- **Fakturering frekvens**: Per sekund

Mängden beräkning som faktureras exponeras av följande mått:

- **Mått**: app_cpu_billed (vCore sekunder)
- **Definition**: max (min virtuella kärnor, virtuella kärnor som används, minsta minnesmängd GB * 1/3 minne GB används * 1/3) *
- **Telemetrirapportering**: Per minut

> [!NOTE]
> \* Den här datamängden beräknas varje sekund och aggregerat över 1 minut.

**Exempel**: Överväg en databas med GP_S_Gen5_4 med följande användning över en timme:

|Tid (timmar: minuter)|app_cpu_billed (vCore seconds)|
|---|---|
|0:01|63|
|0:02|123|
|0:03|95|
|0:04|54|
|0:05|41|
|0:06 - 1:00|1255|
||Totalt: 1631|

Anta att enhetspriset beräkning är $0.2609/vCore/hour. Sedan beräkningarna debiteras du för den här perioden för en timme bestäms med hjälp av följande formel: **$0.2609/vCore/hour * 1631 vCore sekunder * 1 timme/3 600 sekunder = $0.1232**

## <a name="available-regions"></a>Tillgängliga regioner

Nivå för beräkning utan Server är tillgänglig i alla regioner förutom följande regioner: Australien, centrala, Kina, östra, Kina, norra, Frankrike, södra, Tyskland, centrala, Tyskland, nordöstra, Indien, västra, Korea, södra, Sydafrika, västra, Storbritannien, norra, Storbritannien, södra, Storbritannien, västra och USA, västra centrala

## <a name="next-steps"></a>Nästa steg

Resursbegränsningar, se [serverlösa compute nivån resursbegränsningar](sql-database-vCore-resource-limits-single-databases.md#serverless-compute-tier).