---
title: Azure Analysis Services-utskalning | Microsoft Docs
description: Replikera Azure Analysis Services-servrar med skala ut
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 4cb7b165311f57fadd63770646907ddfc0378844
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37445028"
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services-utskalning

Med skalbar, klientfrågor kan fördelas mellan flera *frågerepliker* minskar svarstiderna under stora frågearbetsbelastningar arbetsbelastningar i en frågepool. Du kan också avgränsa bearbetning från frågepoolen, säkerställer att klientfrågor inte påverkas negativt av bearbetningsåtgärder. Skala ut kan konfigureras i Azure portal eller med hjälp av Analysis Services REST API.

## <a name="how-it-works"></a>Hur det fungerar

En server fungerar som både bearbetningsservern och fråge-i en typisk serverdistribution. Om antalet klientfrågor mot modeller på servern överskrider den enheter QPU (Query Processing) för din server plan, eller modellbearbetning inträffar samtidigt som hög frågearbetsbelastningar, kan försämra prestanda. 

Med skalbar, kan du skapa en frågepool med upp till sju ytterligare frågerepliker (åtta totalt, inklusive din server). Du kan skala antalet frågerepliker att uppfylla QPU krav vid kritiska tidpunkter och du kan avgränsa en bearbetningsservern från frågepoolen när som helst. Alla frågerepliker skapas i samma region som din server.

Oavsett hur många frågerepliker som du har i en frågepool fördelas bearbetningsbelastningar inte mellan frågerepliker. En enskild server fungerar som bearbetningsservern. Frågerepliker fungerar bara frågor mot modeller som synkroniseras mellan varje replik i frågepoolen. 

När bearbetningen är avslutad, måste du utföra en synkronisering mellan bearbetning av servern och replikservern fråga. Det är viktigt att konfigurera en synkroniseringsåtgärd vid slutförande av bearbetningsåtgärder vid automatisering av bearbetningsåtgärder. Synkronisering kan utföras manuellt i portalen eller med hjälp av PowerShell eller REST API.

> [!NOTE]
> Skala ut är tillgänglig för servrar i Standardprisnivån. Varje fråga replik debiteras till samma pris som din server.

> [!NOTE]
> Skala ut ökar inte mängden tillgängligt minne för servern. Om du vill öka minne, måste du uppgradera din plan.

## <a name="region-limits"></a>Region gränser

Antal frågerepliker som du kan konfigurera begränsas av den region som din server finns i. Följande begränsningar gäller:

|Region  |Maxantal repliker  |
|---------|---------|
|Östra USA 2    |    7     |
|Västra centrala USA     |    7     |
|Västra Europa     |    7     |
|Västra USA     |     7    |
|Centrala USA     |     3    |
|Sydostasien    |     3    |
|Alla andra regioner  |   1    |



## <a name="monitor-qpu-usage"></a>Övervaka QPU-användning

 Övervaka din server i Azure portal för att avgöra om skalbarhet för din server är nödvändiga, med hjälp av mått. Om din QPU maximerar regelbundet, innebär det att antalet frågor mot dina modeller överskriden QPU-gränsen för din plan. Fråga pool jobbet kö längd mått ökar även när antalet frågor i frågekön tråd pool överskrider tillgängliga QPU. Läs [Övervaka servermått](analysis-services-monitor.md) för mer information.

## <a name="configure-scale-out"></a>Konfigurera utskalning

### <a name="in-azure-portal"></a>I Azure-portalen

1. I portalen klickar du på **skalbar**. Använd skjutreglaget för att välja antalet fråga replikservern. Antal repliker som du väljer är ett tillägg till din befintliga server.

2. I **separera bearbetningsservern från frågepoolen**, Välj Ja om du vill undanta dina bearbetningsservern från frågeservrar.

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
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
Innan du använder PowerShell [installera eller uppdatera den senaste AzureRM-modulen](https://github.com/Azure/azure-powershell/releases). 

Ange antal frågerepliker och [Set-azurermanalysisservicesserver har](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/set-azurermanalysisservicesserver). Ange den valfria `-ReadonlyReplicaCount` parametern.

Kör synkronisering med [synkronisering AzureAnalysisServicesInstance](https://docs.microsoft.com/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).



## <a name="connections"></a>Anslutningar

På översiktssidan för din server finns det två servernamn. Om du inte har konfigurerat skalbar för en server, fungerar på samma både servernamn. När du konfigurerar skalbar för en server, måste du ange namnet på lämplig servern beroende på vilken typ av anslutning. 

För slutanvändaren klientanslutningar som Power BI Desktop, Excel och anpassade appar, Använd **servernamn**. 

SSMS SSDT och anslutningssträngar i PowerShell, Azure-funktionsappar och AMO, använder du **hanteringsservernamnet**. Hanteringsserverns namn innehåller en särskild `:rw` kvalificerare (skrivskyddad). Bearbetningsåtgärder för alla inträffar på hanteringsservern.

![Servernamn](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Relaterad information

[Övervaka servermått](analysis-services-monitor.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md) 

