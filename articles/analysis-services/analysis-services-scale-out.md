---
title: Azure Analysis Services skalbar | Microsoft Docs
description: Replikera Azure Analysis Services-servrar med skalbara
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: owend
ms.openlocfilehash: a97f9648efef7f07659110d720c200dcd0a241a9
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="azure-analysis-services-scale-out"></a>Azure Analysis Services skalbar

Med skalbar, klientfrågor kan fördelas mellan flera *fråga repliker* i poolen fråga minskar svarstider under hög frågan arbetsbelastningar. Du kan också dela bearbetning från poolen frågan, säkerställer klientfrågor inte påverkas negativt av bearbetningsåtgärder. Skalbar kan konfigureras i Azure-portalen eller med hjälp av Analysis Services REST API.

## <a name="how-it-works"></a>Hur det fungerar

I en typisk serverdistribution fungerar en servern som både bearbetning servern och frågeservern. Om antalet klientfrågor modeller på servern överskrider den fråga bearbetning av enheter (QPU) för din server-plan eller modellbearbetning sker samtidigt som hög frågan arbetsbelastningar, kan försämra prestanda. 

Med skalbar, kan du skapa en fråga pool med upp till sju ytterligare fråga repliker (åtta totalt, inklusive din server). Du kan skala antalet frågan repliker att uppfylla krav på QPU kritisk tiden och du kan dela en bearbetning server från poolen frågan när som helst. 

Oavsett vilket antal av frågan repliker i en pool med frågan fördelas bearbetningsbelastningar inte mellan frågan repliker. En enda server som fungerar som server för bearbetning. Frågan repliker fungerar endast frågor modeller synkroniseras mellan varje replik i poolen för frågan. 

När bearbetningen är klar måste du utföra en synkronisering mellan bearbetning-servern och replikservern frågan. När automatisera bearbetning, är det viktigt att konfigurera synkroniseringen vid slutförande av bearbetningsåtgärder. Synkroniseringen kan utföras manuellt i portalen eller med hjälp av PowerShell eller REST API.

> [!NOTE]
> Skalbar är tillgänglig för servrar i den Standardprisnivån. Varje fråga replik debiteras med samma hastighet som din server.

> [!NOTE]
> Skalbar ökar inte mängden tillgängligt minne för servern. Du behöver uppgradera din plan för att öka minne.

## <a name="monitor-qpu-usage"></a>Övervakaren QPU användning

 Övervaka servern i Azure-portalen för att avgöra om skalbar för din server är nödvändigt, med hjälp av mått. Om din QPU maximerar regelbundet, innebär det antal frågor mot modeller överskrider gränsvärdet QPU för planen. Frågan poolen jobbet kön längd mått ökar även när antalet frågor i frågekön tråd poolen överskrider tillgänglig QPU. Läs mer i [övervaka serverstatistik](analysis-services-monitor.md).

## <a name="configure-scale-out"></a>Konfigurera skalbar

### <a name="in-azure-portal"></a>I Azure-portalen

1. I portalen klickar du på **skalbar**. Använd skjutreglaget för att välja antalet frågan replikservern. Antal repliker som du väljer är ett tillägg till din befintliga server.

2. I **separata bearbetning server från poolen frågar**, Välj Ja om du vill undanta bearbetning servern från frågeservrar.

   ![Skalbar skjutreglaget](media/analysis-services-scale-out/aas-scale-out-slider.png)

3. Klicka på **spara** att etablera din nya replikservern för frågan. 

Tabellmodeller på den primära servern synkroniseras med replikservrarna. När synkroniseringen är klar börjar poolen frågan distribuerar inkommande frågor mellan replikservrarna. 


## <a name="synchronization"></a>Synkronisering 

När du etablerar den nya frågan repliker replikerar automatiskt modeller över alla repliker Azure Analysis Services. Du kan också utföra en manuell synkronisering med hjälp av portalen eller REST API. När du bearbetar modeller bör du utföra en synkronisering så att uppdateringar synkroniseras mellan din fråga repliker.

### <a name="in-azure-portal"></a>I Azure-portalen

I **översikt** > modellen > **synkronisera modellen**.

![Skalbar skjutreglaget](media/analysis-services-scale-out/aas-scale-out-sync.png)

### <a name="rest-api"></a>REST API
Använd den **sync** igen.

#### <a name="synchronize-a-model"></a>Synkronisera en modell   
`POST https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

#### <a name="get-sync-status"></a>Hämta synkroniseringsstatus  
`GET https://<region>.asazure.windows.net/servers/<servername>:rw/models/<modelname>/sync`

### <a name="powershell"></a>PowerShell
För att kunna köra synkronisering från PowerShell [uppdatera till senast](https://github.com/Azure/azure-powershell/releases) 5.01 eller högre AzureRM-modulen. Använd [Sync AzureAnalysisServicesInstance](https://docs.microsoft.com/en-us/powershell/module/azurerm.analysisservices/sync-azureanalysisservicesinstance).

## <a name="connections"></a>Anslutningar

Det finns två servernamn på översiktssidan för din server. Om du ännu inte har konfigurerat skalbar för en server fungerar på samma sätt både servernamn. När du har konfigurerat skalbar för en server behöver du att ange namnet på lämplig server beroende på vilken typ av anslutning. 

För slutanvändaren klientanslutningar som används för Power BI Desktop-, Excel- och anpassade appar **servernamn**. 

SSMS SSDT och anslutningssträngar i PowerShell, Azure-funktion appar och AMO, använder du **hanteringsservernamnet**. Hanteringsserverns namn innehåller ett särskilt `:rw` kvalificerare (skrivskyddad). Alla bearbetning åtgärder vidtas på hanteringsservern.

![Servernamn](media/analysis-services-scale-out/aas-scale-out-name.png)

## <a name="related-information"></a>Relaterad information

[Övervakaren serverstatistik](analysis-services-monitor.md)   
[Hantera Azure Analysis Services](analysis-services-manage.md) 

