---
title: Azure Analysis Services-utskalning | Microsoft Docs
description: Replikera Azure Analysis Services-servrar med skala ut
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/13/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8cfbc72e239a7a5b38cee6752803e79735e2adc9
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49321282"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services-utskalning

Med skalbar, klientfrågor kan fördelas mellan flera *frågerepliker* minskar svarstiderna under stora frågearbetsbelastningar arbetsbelastningar i en frågepool. Du kan också avgränsa bearbetning från frågepoolen, säkerställer att klientfrågor inte påverkas negativt av bearbetningsåtgärder. Skala ut kan konfigureras i Azure portal eller med hjälp av Analysis Services REST API.

## <a name="how-it-works"></a>Hur det fungerar

En server fungerar som både bearbetningsservern och fråge-i en typisk serverdistribution. Om antalet klientfrågor mot modeller på servern överskrider den enheter QPU (Query Processing) för din server plan, eller modellbearbetning inträffar samtidigt som hög frågearbetsbelastningar, kan försämra prestanda. 

Med skalbar, kan du skapa en frågepool med upp till sju ytterligare repliken resurser (åtta totalt, inklusive din server). Du kan skala antalet frågerepliker att uppfylla QPU krav vid kritiska tidpunkter och du kan avgränsa en bearbetningsservern från frågepoolen när som helst. Alla frågerepliker skapas i samma region som din server.

Oavsett hur många frågerepliker som du har i en frågepool fördelas bearbetningsbelastningar inte mellan frågerepliker. En enskild server fungerar som bearbetningsservern. Frågerepliker fungerar bara frågor mot modeller som synkroniseras mellan varje fråga replik i frågepoolen. 

Vid utskalning, läggs nya frågerepliker till frågepoolen inkrementellt. Det kan ta upp till fem minuter efter nya resurser som frågan repliken som ska ingå i frågepoolen. När alla nya frågerepliker är igång och körs, att nya klientanslutningar finns belastningsutjämnas mellan alla poolresurser för frågan. Befintliga klientanslutningar ändras inte från de för närvarande är anslutna till resursen.  När skalning i avslutas de befintliga klientanslutningar i en poolresurs för frågan som tas bort från frågepoolen. De återansluts till en återstående fråga poolresurs när skalan i åtgärden har slutförts, vilket kan ta upp till fem minuter.

Vid bearbetning av modeller, när bearbetningen är avslutad, måste du utföra en synkronisering mellan bearbetningsservern och fråga replikerna. Det är viktigt att konfigurera en synkroniseringsåtgärd vid slutförande av bearbetningsåtgärder vid automatisering av bearbetningsåtgärder. Synkronisering kan utföras manuellt i portalen eller med hjälp av PowerShell eller REST API. 

### <a name="separate-processing-from-query-pool"></a>Separata bearbetning från frågepool

Du kan välja att dela din bearbetningsservern från frågepoolen för maximal prestanda för såväl bearbetning frågeåtgärder. När separerade kan tilldelas befintliga och nya klientanslutningar till frågerepliker i poolen fråga. Om bearbetningsåtgärder bara tar upp en kort tidsperiod, kan du avgränsa din bearbetningsservern från frågepoolen endast för den tid det tar att utföra åtgärder för bearbetning och synkronisering och Lägg sedan tillbaka till frågepoolen. 

> [!NOTE]
> Skala ut är tillgänglig för servrar i Standardprisnivån. Varje fråga replik debiteras till samma pris som din server.

> [!NOTE]
> Skala ut ökar inte mängden tillgängligt minne för servern. Om du vill öka minne, måste du uppgradera din plan.

## <a name="region-limits"></a>Region gränser

Antal frågerepliker som du kan konfigurera begränsas av den region som din server finns i. Mer information finns i [tillgänglighet efter region](analysis-services-overview.md#availability-by-region).

## <a name="monitor-qpu-usage"></a>Övervaka QPU-användning

 Övervaka din server i Azure portal för att avgöra om skalbarhet för din server är nödvändiga, med hjälp av mått. Om din QPU maximerar regelbundet, innebär det att antalet frågor mot dina modeller överskriden QPU-gränsen för din plan. Fråga pool jobbet kö längd mått ökar även när antalet frågor i frågekön tråd pool överskrider tillgängliga QPU. Läs [Övervaka servermått](analysis-services-monitor.md) för mer information.

## <a name="configure-scale-out"></a>Konfigurera utskalning

### <a name="in-azure-portal"></a>I Azure-portalen

1. I portalen klickar du på **skalbar**. Använd skjutreglaget för att välja antalet fråga replikservern. Antal repliker som du väljer är ett tillägg till din befintliga server.

2. I **separera bearbetningsservern från frågepoolen**, Välj Ja om du vill undanta dina bearbetningsservern från frågeservrar. Klientanslutningar med hjälp av standard-anslutningssträngen (utan: rw) omdirigeras till repliker i frågepoolen. 

   ![Skala ut skjutreglaget](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klicka på **spara** att etablera din nya replikservern för frågan. 

Tabellmodeller på den primära servern är synkroniserade med replikservrarna. När synkroniseringen är klar börjar frågepoolen distribuerar inkommande frågor bland replikservrarna. 

## <a name="synchronization"></a>Synkronisering 

När du etablerar nya frågerepliker replikerar automatiskt dina modeller i alla repliker Azure Analysis Services. Du kan också utföra en manuell synkronisering med hjälp av portalen eller REST API. När du bearbetar dina modeller, bör du utföra en synkronisering så att programuppdateringarna är synkroniserade bland dina frågerepliker.

### <a name="in-azure-portal"></a>I Azure-portalen

I **översikt** > modellen > **synkronisera modellen**.

![Skala ut skjutreglaget](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST-API
Använd den **synkronisering** igen.

#### <a name="synchronize-a-model"></a>Synkronisera en modell   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Hämta synkroniseringsstatus för  
`GET https://<region>.asazure.windows.net/servers/<servername>/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Innan du använder PowerShell [installera eller uppdatera den senaste AzureRM-modulen](https://github.com/Azure/azure-powershell/releases). 

Ange antal frågerepliker och [Set-azurermanalysisservicesserver har](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Ange den valfria `-ReadonlyReplicaCount` parametern.

Kör synkronisering med [synkronisering AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>Anslutningar

På översiktssidan för din server finns det två servernamn. Om du inte har konfigurerat skalbar för en server, fungerar på samma både servernamn. När du konfigurerar skalbar för en server, måste du ange namnet på lämplig servern beroende på vilken typ av anslutning. 

För slutanvändaren klientanslutningar som Power BI Desktop, Excel och anpassade appar, Använd **servernamn**. 

SSMS SSDT och anslutningssträngar i PowerShell, Azure-funktionsappar och AMO, använder du **hanteringsservernamnet**. Hanteringsserverns namn innehåller en särskild `:rw` kvalificerare (skrivskyddad). Bearbetningsåtgärder för alla inträffar på hanteringsservern.

![Servernamn](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="troubleshoot"></a>Felsöka

**Problem:** användare får fel **servern hittades inte ”\<namnet på servern >' instansen i anslutningsläge” skrivskyddad ”.**

**Lösning:** när du väljer den **separera bearbetningsservern från frågepoolen** alternativet klientanslutningar med hjälp av standard-anslutningssträngen (utan: rw) omdirigeras till frågerepliker för poolen. Om repliker i frågepoolen inte är ännu online eftersom synkronisering inte har ännu har slutförts, kan omdirigerad klientanslutningar misslyckas. Om du vill förhindra att misslyckade anslutningar, Välj att inte separera bearbetningsservern från frågepoolen tills en skalbar och synkronisering åtgärd har slutförts. Du kan använda minne och QPU mått för att övervaka synkroniseringsstatus för.

## <a name="related-information"></a>Relaterad information

[Övervaka servermått](analysis-services-monitor.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md) 

