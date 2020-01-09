---
title: Ta bort och återställa Azure Log Analytics-arbetsytan | Microsoft Docs
description: Lär dig hur du tar bort din Log Analytics arbets yta om du skapade en i en personlig prenumeration eller omstrukturering av arbets ytans modell.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/28/2019
ms.openlocfilehash: 2b54dd5161312a081d439b3e10d2cb4bf9014d52
ms.sourcegitcommit: f0dfcdd6e9de64d5513adf3dd4fe62b26db15e8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/26/2019
ms.locfileid: "75496542"
---
# <a name="delete-and-restore-azure-log-analytics-workspace"></a>Ta bort och återställa Azure Log Analytics-arbetsytan

Den här artikeln förklarar begreppet Azure Log Analytics arbets yta mjuk borttagning och hur du återställer den borttagna arbets ytan. 

## <a name="considerations-when-deleting-a-workspace"></a>Att tänka på när du tar bort en arbets yta

När du tar bort en Log Analytics arbets yta utförs en mjuk borttagnings åtgärd för att tillåta återställning av arbets ytan, inklusive data och anslutna agenter inom 14 dagar, oavsett om borttagningen var oavsiktligt eller avsiktligt. Efter den mjuka borttagnings perioden är arbets ytans resurs och dess data inte återställnings bara – data placeras i kö för permanent borttagning och rensas fullständigt inom 30 dagar. Namnet på arbets ytan är frigjord och du kan använda det för att skapa en ny arbets yta.

> [!NOTE]
> Det går inte att stänga av beteendet för mjuk borttagning. Vi kommer snart att lägga till ett alternativ för att åsidosätta mjuk borttagning när du använder en Force-tagg i borttagnings åtgärden.

Du bör vara försiktig när du tar bort en arbets yta eftersom det kan finnas viktiga data och konfiguration som kan påverka din tjänst åtgärd negativt. Granska vilka agenter, lösningar och andra Azure-tjänster och källor som lagrar data i Log Analytics, till exempel:

* Hanteringslösningar
* Azure Automatisering
* Agenter som körs på virtuella Windows-och Linux-datorer
* Agenter som körs på Windows-och Linux-datorer i din miljö
* System Center Operations Manager

Åtgärden mjuk borttagning tar bort arbets ytans resurs och alla tillhör ande användares behörighet är bruten. Om användarna är associerade med andra arbets ytor kan de fortsätta att använda Log Analytics med de andra arbets ytorna.

## <a name="soft-delete-behavior"></a>Beteende vid mjuk borttagning

Borttagnings åtgärden för arbets ytan tar bort resurs hanterarens Resource Manager-resurs, men konfigurationen och data sparas i 14 dagar, samtidigt som arbets ytans utseende tas bort. Alla agenter och System Center Operations Manager hanterings grupper som kon figurer ATS att rapportera till arbets ytan behålls i ett överblivna tillstånd under den mjuka borttagnings perioden. Tjänsten tillhandahåller ytterligare en mekanism för att återställa den borttagna arbets ytan, inklusive dess data och anslutna resurser, vilket i princip tar bort borttagningen.

> [!NOTE] 
> Installerade lösningar och länkade tjänster som ditt Azure Automation-konto tas bort permanent från arbets ytan vid borttagnings tillfället och kan inte återställas. Dessa bör konfigureras om efter återställnings åtgärden för att flytta arbets ytan till det tidigare konfigurerade läget.

Du kan ta bort en arbets yta med [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)eller i [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Azure portal

1. Logga in genom att gå till [Azure Portal](https://portal.azure.com). 
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.
3. I listan över Log Analytics arbets ytor väljer du en arbets yta och klickar sedan på **ta bort** längst upp i mitten av fönstret.
   ![ta bort alternativ från rutan Egenskaper för arbets ytan](media/delete-workspace/log-analytics-delete-workspace.png)
4. När fönstret bekräftelse meddelande visas och du uppmanas att bekräfta borttagningen av arbets ytan klickar du på **Ja**.
   ![Bekräfta borttagning av arbets ytan](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "ContosResourceGroup" -Name "MyWorkspace"
```

## <a name="recover-workspace"></a>Återställ arbets yta

Om du har deltagar behörighet till prenumerationen och resurs gruppen där arbets ytan var kopplad innan du mjuka borttagnings åtgärden, kan du återställa den under den mjuka borttagnings perioden, inklusive dess data, konfiguration och anslutna agenter. Efter den mjuka borttagnings perioden är arbets ytan inte återställnings bar och tilldelad för permanent borttagning. Namn på borttagna arbets ytor bevaras under perioden för mjuk borttagning och kan inte användas när du försöker skapa en ny arbets yta.  

Du kan återställa en arbets yta genom att skapa den på nytt med hjälp av följande arbets yta skapa metoder: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) eller [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) så länge följande egenskaper är ifyllda med den borttagna arbets ytans information:

* Prenumerations-ID
* Resurs grupps namn
* Namn på arbetsyta
* Region

Arbets ytan och alla dess data tas tillbaka efter återställnings åtgärden. Lösningar och länkade tjänster togs bort permanent från arbets ytan när den togs bort och de bör konfigureras om så att arbets ytan försätts i det tidigare konfigurerade läget. Vissa data kanske inte är tillgängliga för frågan efter arbets ytans återställning tills de associerade lösningarna har installerats om och deras scheman har lagts till i arbets ytan.

> [!NOTE]
> * Arbets ytans återställning stöds inte i [Azure Portal](https://portal.azure.com). 
> * När du återskapar en arbets yta under den mjuka borttagnings perioden visas en indikation på att namnet på arbets ytan redan används. 
> 
