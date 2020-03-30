---
title: Skala ut Azure Analysis Services| Microsoft-dokument
description: Replikera Azure Analysis Services-servrar med utskalning. Klientfrågor kan sedan fördelas mellan flera frågerepliker i en utskalningsfrågepool.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 03/02/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3ea304d038618fc428f20e7ad72b398f593d09a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78247997"
---
# <a name="azure-analysis-services-scale-out"></a>Utskalning för Azure Analysis Services

Med utskalning kan klientfrågor fördelas mellan flera *frågerepliker* i en *frågepool,* vilket minskar svarstiderna under hög frågearbetsbelastningar. Du kan också separera bearbetningen från frågepoolen, vilket säkerställer att klientfrågor inte påverkas negativt av bearbetningsåtgärder. Utskalning kan konfigureras i Azure-portalen eller med hjälp av ANALYSIS Services REST API.

Utskalning är tillgängligt för servrar på prisnivån Standard. Varje frågereplik faktureras i samma takt som servern. Alla frågerepliker skapas i samma region som servern. Antalet frågerepliker som du kan konfigurera begränsas av den region som servern befinner sig i. Mer information finns i [Tillgänglighet per region](analysis-services-overview.md#availability-by-region). Utskalning ökar inte mängden tillgängligt minne för servern. För att öka minnet måste du uppgradera din plan. 

## <a name="why-scale-out"></a>Varför skala ut?

I en vanlig serverdistribution fungerar en server som både bearbetningsserver och frågeserver. Om antalet klientfrågor mot modeller på servern överskrider QPU (Query Processing Units) för serverns plan, eller om modellbearbetning sker samtidigt som hög frågearbetsbelastningar, kan prestandan försämras. 

Med utskalning kan du skapa en frågepool med upp till sju ytterligare frågereplikresurser (åtta totalt, inklusive den *primära* servern). Du kan skala antalet repliker i frågepoolen för att uppfylla QPU-krav vid kritiska tidpunkter, och du kan när som helst separera en bearbetningsserver från frågepoolen. 

Oavsett antalet frågerepliker som du har i en frågepool fördelas inte bearbetning av arbetsbelastningar mellan frågerepliker. Den primära servern fungerar som bearbetningsserver. Frågerepliker betjänar endast frågor mot modelldatabaserna som synkroniseras mellan den primära servern och varje replik i frågepoolen. 

När du skalar ut kan det ta upp till fem minuter innan nya frågerepliker läggs till stegvis i frågepoolen. När alla nya frågerepliker är igång läses nya klientanslutningar upp för att balansera resurser i frågepoolen. Befintliga klientanslutningar ändras inte från den resurs som de för närvarande är anslutna till. När du skalar in avslutas alla befintliga klientanslutningar till en frågepoolresurs som tas bort från frågepoolen. Klienter kan återansluta till en återstående frågepoolresurs.

## <a name="how-it-works"></a>Hur det fungerar

När du konfigurerar utskalning första gången synkroniseras modelldatabaser på den primära servern *automatiskt* med nya repliker i en ny frågepool. Automatisk synkronisering sker bara en gång. Under automatisk synkronisering kopieras den primära serverns datafiler (krypterade i vila i blob-lagring) till en andra plats, även krypterade i vila i blob-lagring. Repliker i frågepoolen är sedan *hydrerade* med data från den andra uppsättningen filer. 

En automatisk synkronisering utförs endast när du skalar ut en server för första gången, men du kan också utföra en manuell synkronisering. Synkronisering säkerställer att data på repliker i frågepoolen matchar den primära serverns. När modeller för bearbetning (uppdatering) på den primära servern bearbetas måste en synkronisering utföras *när* bearbetningen har slutförts. Den här synkroniseringen kopierar uppdaterade data från den primära serverns filer i blob-lagring till den andra uppsättningen filer. Repliker i frågepoolen är sedan hydrerade med uppdaterade data från den andra uppsättningen filer i blob-lagring. 

När du utför en efterföljande skalningsåtgärd, till exempel, vilket ökar antalet repliker i frågepoolen från två till fem, är de nya replikerna hydrerade med data från den andra uppsättningen filer i blob-lagring. Det finns ingen synkronisering. Om du sedan utför en synkronisering efter skalning ut, de nya replikerna i frågepoolen skulle hydreras två gånger - en redundant hydrering. När du utför en efterföljande utskalningsåtgärd är det viktigt att tänka på följande:

* Utför en synkronisering *före utskalningsåtgärden* för att undvika redundant återfuktning av de tillagda replikerna. Samtidig synkronisering och utskalningsåtgärder som körs samtidigt är inte tillåtna.

* När du automatiserar både *bearbetnings- och* utskalningsåtgärder är det viktigt att först bearbeta data på den primära servern, sedan utföra en synkronisering och sedan utföra skalningsåtgärden. Den här sekvensen säkerställer minimal påverkan på QPU och minnesresurser.

* Synkronisering tillåts även när det inte finns några repliker i frågepoolen. Om du skalar ut från noll till en eller flera repliker med nya data från en bearbetning på den primära servern utför du synkroniseringen först utan repliker i frågepoolen och skalar sedan ut. Synkronisering innan skalning ut undviker redundant hydrering av de nyligen tillagda replikerna.

* När du tar bort en modelldatabas från den primära servern tas den inte automatiskt bort från repliker i frågepoolen. Du måste utföra en synkroniseringsåtgärd med kommandot [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) PowerShell som tar bort filen/filerna för databasen från replikens delade blob-lagringsplats och sedan tar bort modelldatabasen på replikerna i frågepoolen. Om du vill ta reda på om det finns en modelldatabas på repliker i frågepoolen men inte på den primära servern kontrollerar du **att**inställningen **Separera bearbetningsservern från frågar är** ja . Använd sedan SSMS för att ansluta `:rw` till den primära servern med kvalificeraren för att se om databasen finns. Anslut sedan till repliker i frågepoolen genom att ansluta utan `:rw` kvalificeraren för att se om samma databas också finns. Om databasen finns på repliker i frågepoolen men inte på den primära servern kör du en synkroniseringsåtgärd.   

* När du byter namn på en databas på den primära servern finns det ytterligare ett steg som krävs för att säkerställa att databasen är korrekt synkroniserad med alla repliker. När du har bytt namn utför du en synkronisering med kommandot [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance) som anger parametern `-Database` med det gamla databasnamnet. Den här synkroniseringen tar bort databasen och filerna med det gamla namnet från alla repliker. Utför sedan en annan `-Database` synkronisering som anger parametern med det nya databasnamnet. Den andra synkroniseringen kopierar den nyligen namngivna databasen till den andra uppsättningen filer och återfuktar eventuella repliker. Dessa synkroniseringar kan inte utföras med kommandot Synkronisera modell i portalen.

### <a name="synchronization-mode"></a>Synkroniseringsläge

Som standard är frågerepliker rehydrerade i sin helhet, inte stegvis. Rehydrering sker i etapper. De är fristående och bifogade två åt gången (förutsatt att det finns minst tre repliker) för att säkerställa att minst en replik hålls online för frågor vid en given tidpunkt. I vissa fall kan klienter behöva återansluta till en av onlinerepliker medan den här processen pågår. Genom att använda (i förhandsgranskning) **ReplicaSyncMode-inställningen** kan du nu ange att frågerepliker synkronisering sker parallellt. Parallell synkronisering ger följande fördelar: 

- Betydande minskning av synkroniseringstiden. 
- Data över repliker är mer benägna att vara konsekventa under synkroniseringsprocessen. 
- Eftersom databaser hålls online på alla repliker under synkroniseringsprocessen behöver klienterna inte återansluta. 
- Cacheminnet uppdateras stegvis med endast ändrade data, vilket kan vara snabbare än att helt rehydrera modellen. 

#### <a name="setting-replicasyncmode"></a>Ange ReplicaSyncMode

Använd SSMS för att ange ReplicaSyncMode i avancerade egenskaper. Möjliga värden är: 

- `1`(standard): Fullständig replikdatabas rehydrering i steg (inkrementell). 
- `2`: Optimerad synkronisering parallellt. 

![Inställning för RelicaSyncMode](media/analysis-services-scale-out/aas-scale-out-sync-mode.png)

När du ställer in **ReplicaSyncMode=2**, beroende på hur mycket av cachen som behöver uppdateras, kan ytterligare minne förbrukas av frågereplörerna. Om du vill hålla databasen online och tillgänglig för frågor, beroende på hur mycket av data som har ändrats, kan åtgärden kräva upp till *dubbla minnet* på repliken eftersom både gamla och nya segment hålls i minnet samtidigt. Repliknoder har samma minnesallokering som den primära noden, och det finns normalt extra minne på den primära noden för uppdateringsåtgärder, så det kan vara osannolikt att replikerna skulle få på minne. Dessutom är det gemensamma scenariot att databasen uppdateras stegvis på den primära noden, och därför bör kravet på dubbla minne vara ovanligt. Om synkroniseringsåtgärden uppstår påträffen ett fel i minnet försöker den igen med standardtekniken (anslut/koppla bort två åt gången). 

### <a name="separate-processing-from-query-pool"></a>Separat bearbetning från frågepool

För maximal prestanda för både bearbetning och frågeåtgärder kan du välja att separera din bearbetningsserver från frågepoolen. När de är separerade tilldelas nya klientanslutningar endast till frågerepliker i frågepoolen. Om bearbetningsåtgärderna bara tar upp en kort tid kan du välja att bara separera bearbetningsservern från frågepoolen under den tid det tar att utföra bearbetnings- och synkroniseringsåtgärder och sedan inkludera den i frågepoolen igen. När det tar upp till fem minuter innan bearbetningsservern separeras från frågepoolen eller lägga till den i frågepoolen kan det ta upp till fem minuter innan åtgärden slutförs.

## <a name="monitor-qpu-usage"></a>Övervaka QPU-användning

För att avgöra om utskalning för servern är nödvändig [övervakar du servern](analysis-services-monitor.md) i Azure-portalen med hjälp av mått. Om din QPU regelbundet maxar ut betyder det att antalet frågor mot dina modeller överskrider QPU-gränsen för din plan. Måttet Frågepooljobbkölängd ökar också när antalet frågor i frågetrådspoolkön överskrider tillgängligt QPU. 

Ett annat bra mått att titta på är genomsnittliga QPU av ServerResourceType. Det här måttet jämför genomsnittlig QPU för den primära servern med frågepoolen. 

![Frågeskala ut mått](media/analysis-services-scale-out/aas-scale-out-monitor.png)

**Så här konfigurerar du QPU av ServerResourceType**

1. Klicka på Lägg till **mått**i ett måttlinjediagram . 
2. I **RESURS**väljer du din server och väljer sedan **Standardmått för Analysis Services** **i**RESURS och välj sedan Medel i MÄTTJÄNSTER i **17,** välj **QPU**och välj sedan **Medel**i **aggregation**. 
3. Klicka på **Använd delning**. 
4. Välj **ServerResourceType**i **VÄRDEN**.  

### <a name="detailed-diagnostic-logging"></a>Detaljerad diagnosloggning

Använd Azure Monitor Logs för mer detaljerad diagnostik av utskalade serverresurser. Med loggar kan du använda Log Analytics-frågor för att bryta ut QPU och minne efter server och replik. Mer information finns i exempelfrågor i [Analysis Services diagnostikloggning](analysis-services-logging.md#example-queries).


## <a name="configure-scale-out"></a>Konfigurera utskalning

### <a name="in-azure-portal"></a>I Azure-portalen

1. Klicka på **Skala ut**i portalen. Använd skjutreglaget för att välja antalet frågereplikservrar. Antalet repliker du väljer är utöver din befintliga server.  

2. I **Separera bearbetningsservern från frågepoolen**väljer du ja för att utesluta din bearbetningsserver från frågeservrar. [Klientanslutningar](#connections) med standardanslutningssträngen (utan `:rw`) omdirigeras till repliker i frågepoolen. 

   ![Utskalningsreglage](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klicka på **Spara** om du vill etablera de nya frågereplikservrarna. 

När du konfigurerar utskalning för en server första gången synkroniseras modeller på den primära servern automatiskt med repliker i frågepoolen. Automatisk synkronisering sker bara en gång när du först konfigurerar utskalning till en eller flera repliker. Efterföljande ändringar av antalet repliker på samma server *utlöser inte en annan automatisk synkronisering*. Automatisk synkronisering sker inte igen även om du ställer in servern på noll repliker och sedan skala ut igen till valfritt antal repliker. 

## <a name="synchronize"></a>Synkronisera 

Synkroniseringsåtgärder måste utföras manuellt eller med hjälp av REST API.

### <a name="in-azure-portal"></a>I Azure-portalen

I **Översikt** > modell > **Synkronisera modell**.

![Utskalningsreglage](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API

Använd **sync** synkroniseringsåtgärden.

#### <a name="synchronize-a-model"></a>Synkronisera en modell   

`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Hämta synkroniseringsstatus  

`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

Statuskoder för retur:


|Kod  |Beskrivning  |
|---------|---------|
|-1     |  Ogiltig       |
|0     | Replikering        |
|1     |  Rehydrning       |
|2     |   Slutfört       |
|3     |   Misslyckades      |
|4     |    Slutföra     |
|||


### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installera [eller uppdatera den senaste Azure PowerShell-modulen](/powershell/azure/install-az-ps)innan du använder PowerShell . 

Om du vill köra synkronisering använder du [Sync-AzAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/az.analysisservices/sync-AzAnalysisServicesinstance).

Om du vill ange antalet frågerepliker använder du [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Ange den `-ReadonlyReplicaCount` valfria parametern.

Om du vill separera bearbetningsservern från frågepoolen använder du [Set-AzAnalysisServicesServer](https://docs.microsoft.com/powershell/module/az.analysisservices/set-azanalysisservicesserver). Ange den `-DefaultConnectionMode` valfria `Readonly`parameter som ska användas .

Mer information finns i [Använda ett tjänsthuvudnamn med modulen Az.AnalysisServices](analysis-services-service-principal.md#azmodule).

## <a name="connections"></a>Anslutningar

På sidan Översikt på servern finns det två servernamn. Om du ännu inte har konfigurerat utskalning för en server fungerar båda servernamnen på samma sätt. När du har konfigurerat utskalning för en server måste du ange lämpligt servernamn beroende på anslutningstypen. 

För klientanslutningar för slutanvändare som Power BI Desktop, Excel och anpassade appar använder du **Servernamn**. 

För SSMS-, Visual Studio- och anslutningssträngar i PowerShell, Azure Function-appar och AMO använder du **Servernamn för Hantering**. Hanteringsserverns namn `:rw` innehåller en särskild (läs-och skriv)-kvalificerare. Alla bearbetningsåtgärder utförs på (primär) hanteringsservern.

![Servernamn](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="scale-up-scale-down-vs-scale-out"></a>Skala upp, skala ned kontra skala ut

Du kan ändra prisnivån på en server med flera repliker. Samma prisnivå gäller för alla repliker. En skalningsåtgärd kommer först att få ner alla repliker på en gång och sedan ta upp alla repliker på den nya prisnivån.

## <a name="troubleshoot"></a>Felsöka

**Problem:** Användare får fel **Det\<går inte att hitta serverns namn>-instans i anslutningsläge "ReadOnly".**

**Lösning:** När du väljer **alternativet Separera bearbetningsservern från frågepoolen** omdirigeras klientanslutningar med standardanslutningssträngen (utan) `:rw`till fråga poolrepliker. Om repliker i frågepoolen ännu inte är online eftersom synkroniseringen ännu inte har slutförts, kan omdirigerade klientanslutningar misslyckas. För att förhindra misslyckade anslutningar måste det finnas minst två servrar i frågepoolen när du utför en synkronisering. Varje server synkroniseras individuellt medan andra är online. Om du väljer att inte ha bearbetningsservern i frågepoolen under bearbetningen kan du välja att ta bort den från poolen för bearbetning och sedan lägga till den i poolen när bearbetningen är klar, men innan synkroniseringen. Använd minnes- och QPU-mått för att övervaka synkroniseringsstatus.



## <a name="related-information"></a>Relaterad information

[Övervaka servermått](analysis-services-monitor.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md) 
