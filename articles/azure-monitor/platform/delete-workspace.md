---
title: Ta bort och återställa Azure Log Analytics-arbetsytan | Microsoft Docs
description: Lär dig hur du tar bort din Log Analytics arbets yta om du skapade en i en personlig prenumeration eller omstrukturering av arbets ytans modell.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/30/2020
ms.openlocfilehash: 7ed01a57a4c2a55d777907a6cc14b111fb2086e3
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/03/2020
ms.locfileid: "82731908"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Ta bort och återställa Azure Log Analytics-arbetsytan

Den här artikeln förklarar begreppet Azure Log Analytics arbets yta mjuk borttagning och hur du återställer den borttagna arbets ytan. 

## <a name="considerations-when-deleting-a-workspace"></a>Att tänka på när du tar bort en arbets yta

När du tar bort en Log Analytics arbets yta utförs en mjuk borttagnings åtgärd för att tillåta återställning av arbets ytan, inklusive data och anslutna agenter inom 14 dagar, oavsett om borttagningen var oavsiktligt eller avsiktligt. Efter den mjuka borttagnings perioden är arbets ytans resurs och dess data inte återställnings bara – data placeras i kö för permanent borttagning och rensas fullständigt inom 30 dagar. Namnet på arbets ytan är frigjord och du kan använda det för att skapa en ny arbets yta.

> [!NOTE]
> Om du vill åsidosätta beteendet för mjuk borttagning och ta bort arbets ytan permanent, följer du stegen i den [permanenta arbets ytan ta bort](#permanent-workspace-delete).

Du bör vara försiktig när du tar bort en arbets yta eftersom det kan finnas viktiga data och konfiguration som kan påverka din tjänst åtgärd negativt. Granska vilka agenter, lösningar och andra Azure-tjänster och källor som lagrar data i Log Analytics, till exempel:

* Hanteringslösningar
* Azure Automation
* Agenter som körs på virtuella Windows-och Linux-datorer
* Agenter som körs på Windows-och Linux-datorer i din miljö
* System Center Operations Manager

Åtgärden mjuk borttagning tar bort arbets ytans resurs och alla tillhör ande användares behörighet är bruten. Om användarna är associerade med andra arbets ytor kan de fortsätta att använda Log Analytics med de andra arbets ytorna.

## <a name="soft-delete-behavior"></a>Beteende vid mjuk borttagning

Borttagnings åtgärden för arbets ytan tar bort resurs hanterarens Resource Manager-resurs, men konfigurationen och data sparas i 14 dagar, samtidigt som arbets ytans utseende tas bort. Alla agenter och System Center Operations Manager hanterings grupper som kon figurer ATS att rapportera till arbets ytan behålls i ett överblivna tillstånd under den mjuka borttagnings perioden. Tjänsten tillhandahåller ytterligare en mekanism för att återställa den borttagna arbets ytan, inklusive dess data och anslutna resurser, vilket i princip tar bort borttagningen.

> [!NOTE] 
> Installerade lösningar och länkade tjänster som ditt Azure Automation-konto tas bort permanent från arbets ytan vid borttagnings tillfället och kan inte återställas. Dessa bör konfigureras om efter återställnings åtgärden för att flytta arbets ytan till det tidigare konfigurerade läget.

Du kan ta bort en arbets yta med [PowerShell](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0), [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)eller i [Azure Portal](https://portal.azure.com).

### <a name="azure-portal"></a>Azure Portal

1. Logga in genom att gå till [Azure Portal](https://portal.azure.com). 
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics arbets ytor**.
3. I listan över Log Analytics arbets ytor väljer du en arbets yta och klickar sedan på **ta bort** längst upp i mitten av fönstret.
   ![Ta bort alternativ från fönstret Egenskaper för arbets yta](media/delete-workspace/log-analytics-delete-workspace.png)
4. När fönstret bekräftelse meddelande visas och du uppmanas att bekräfta borttagningen av arbets ytan klickar du på **Ja**.
   ![Bekräfta borttagning av arbets yta](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

### <a name="troubleshooting"></a>Felsökning

Du måste ha minst *Log Analytics deltagar* behörighet för att kunna ta bort en arbets yta.<br>
Om du får ett fel meddelande *är namnet på arbets ytan redan används eller är i* *konflikt* när du skapar en arbets yta. det kan vara sedan:
* Namnet på arbets ytan är inte tillgängligt och används av någon i din organisation eller av en annan kund.
* Arbets ytan har tagits bort under de senaste 14 dagarna och dess namn är reserverat för mjuk borttagnings perioden. Om du vill åsidosätta den mjuka borttagningen och ta bort arbets ytan permanent för att skapa en ny arbets yta med samma namn, följer du dessa steg för att återställa arbets ytan först och utföra permanent borttagning:<br>
   1. [Återställ](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#recover-workspace) din arbets yta.
   2. [Ta bort](https://docs.microsoft.com/azure/azure-monitor/platform/delete-workspace#permanent-workspace-delete) arbets ytan permanent.
   3. Skapa en ny arbets yta med samma arbets ytans namn.

## <a name="permanent-workspace-delete"></a>Permanent borttagning av arbets yta
Metoden mjuk borttagning får inte plats i vissa scenarier som utveckling och testning, där du måste upprepa en distribution med samma inställningar och arbets ytans namn. I sådana fall kan du ta bort arbets ytan permanent och "åsidosätta" den mjuka borttagnings perioden. Borttagnings åtgärden för permanent arbets yta frigör arbets ytans namn och du kan skapa en ny arbets yta med samma namn.


> [!IMPORTANT]
> Använd permanent borttagnings åtgärd för arbets ytor med försiktighet eftersom den inte kan återställas och att du inte kan återställa din arbets yta och dess data.

Borttagning av permanent arbets yta kan för närvarande utföras via REST API.

> [!NOTE]
> Alla API-förfrågningar måste innehålla en token Authorization-token i begär ande huvudet.
>
> Du kan hämta token med:
> - [Appregistreringar](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navigera till Azure Portal med hjälp av Developer-konsolen (F12) i webbläsaren. Titta i en av **batchen?** instanser för Authentication-strängen under **begärandehuvuden**. Detta kommer att finnas i mönstret *auktorisering: Bearer <token> *. Kopiera och Lägg till detta i API-anropet som visas i exemplen.
> - Gå till webbplatsen för Azure REST-dokumentation. Tryck på **prova** på valfritt API, kopiera Bearer-token och Lägg till den i ditt API-anrop.
Om du vill ta bort arbets ytan permanent använder du [arbets ytorna – ta bort REST API-]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) anrop med en tvingande tagg:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Där ' eyJ0eXAiOiJKV1Qi... ' representerar fullständig token för autentisering.

## <a name="recover-workspace"></a>Återställ arbets yta

Om du har deltagar behörighet till prenumerationen och resurs gruppen där arbets ytan var kopplad innan du mjuka borttagnings åtgärden, kan du återställa den under den mjuka borttagnings perioden, inklusive dess data, konfiguration och anslutna agenter. Efter den mjuka borttagnings perioden är arbets ytan inte återställnings bar och tilldelad för permanent borttagning. Namn på borttagna arbets ytor bevaras under perioden för mjuk borttagning och kan inte användas när du försöker skapa en ny arbets yta.  

Du kan återställa arbets ytan genom att skapa en arbets yta med information om den borttagna arbets ytan, bland annat *prenumerations-ID*, *resurs grupp namn*, *namn på arbets yta* och *region*. Om din resurs grupp också har tagits bort och inte finns skapar du en resurs grupp med samma namn som användes före borttagningen och sedan skapar du en arbets yta med någon av följande metoder: [Azure Portal](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace), [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) eller [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate).

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Arbets ytan och alla dess data tas tillbaka efter återställnings åtgärden. Lösningar och länkade tjänster togs bort permanent från arbets ytan när den togs bort och de bör konfigureras om så att arbets ytan försätts i det tidigare konfigurerade läget. Vissa data kanske inte är tillgängliga för frågan efter arbets ytans återställning tills de associerade lösningarna har installerats om och deras scheman har lagts till i arbets ytan.

> [!NOTE]
> * Arbets ytans återställning stöds inte i [Azure Portal](https://portal.azure.com). 
> * När du återskapar en arbets yta under den mjuka borttagnings perioden visas en indikation på att namnet på arbets ytan redan används. 
> 
