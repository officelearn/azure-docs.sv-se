---
title: Ta bort och återställa Azure Log Analytics-arbetsytan | Microsoft-dokument
description: Läs om hur du tar bort din Log Analytics-arbetsyta om du har skapat en i en personlig prenumeration eller omstrukturerar din arbetsytemodell.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/14/2020
ms.openlocfilehash: ead0ac04fbd2244fce97dd043ebd44f24fb0f67f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80054923"
---
# <a name="delete-and-recover-azure-log-analytics-workspace"></a>Ta bort och återställa Azure Log Analytics-arbetsytan

I den här artikeln beskrivs konceptet med Azure Log Analytics-arbetsytan för mjuk borttagning och hur du återställer borttagen arbetsyta. 

## <a name="considerations-when-deleting-a-workspace"></a>Överväganden när du tar bort en arbetsyta

När du tar bort en Log Analytics-arbetsyta utförs en mjuk borttagningsåtgärd för att möjliggöra återställning av arbetsytan, inklusive dess data och anslutna agenter inom 14 dagar, oavsett om borttagningen var oavsiktlig eller avsiktlig. Efter den mjuka borttagningsperioden kan arbetsytans resurs och dess data inte återställas – dess data står i kö för permanent borttagning och rensas helt inom 30 dagar. Arbetsytans namn är "släppt" och du kan använda det för att skapa en ny arbetsyta.

> [!NOTE]
> Om du vill åsidosätta funktionen för mjuk borttagning och ta bort arbetsytan permanent följer du stegen i [Permanent arbetsytan ta bort](#permanent-workspace-delete).

Du vill vara försiktig när du tar bort en arbetsyta eftersom det kan finnas viktiga data och konfigurationer som kan påverka tjänsten negativt. Granska vilka agenter, lösningar och andra Azure-tjänster och källor som lagrar sina data i Logganalys, till exempel:

* Hanteringslösningar
* Azure Automation
* Agenter som körs på virtuella Windows- och Linux-datorer
* Agenter som körs på Windows- och Linux-datorer i din miljö
* System Center Operations Manager

Åtgärden för mjuk borttagning tar bort arbetsytans resurs och alla associerade användares behörighet har brutits. Om användare är associerade med andra arbetsytor kan de fortsätta att använda Log Analytics med de andra arbetsytorna.

## <a name="soft-delete-behavior"></a>Mjuk-delete-beteende

Åtgärden för borttagning av arbetsytan tar bort resursen för arbetsyteresurshanteraren, men dess konfiguration och data sparas i 14 dagar, samtidigt som arbetsytan tas bort. Alla agenter och hanteringsgrupper för System Center Operations Manager som konfigurerats för att rapportera till arbetsytan förblir i ett övergivet tillstånd under den mjuka borttagningsperioden. Tjänsten tillhandahåller vidare en mekanism för att återställa den borttagna arbetsytan inklusive dess data och anslutna resurser, vilket i huvudsak ångrar borttagningen.

> [!NOTE] 
> Installerade lösningar och länkade tjänster som ditt Azure Automation-konto tas bort permanent från arbetsytan vid borttagningstillfället och kan inte återställas. Dessa bör konfigureras om efter återställningsåtgärden för att föra arbetsytan till sitt tidigare konfigurerade tillstånd.

Du kan ta bort en arbetsyta med [PowerShell,](https://docs.microsoft.com/powershell/module/azurerm.operationalinsights/remove-azurermoperationalinsightsworkspace?view=azurermps-6.13.0) [REST API](https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete)eller i [Azure-portalen](https://portal.azure.com).

### <a name="azure-portal"></a>Azure Portal

1. Om du vill logga in går du till [Azure-portalen](https://portal.azure.com). 
2. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.
3. I listan över log analytics-arbetsytor väljer du en arbetsyta och klickar sedan på **Ta bort** högst upp i mittenfönstret.
   ![Alternativet Ta bort från egenskapsfönstret Arbetsyta](media/delete-workspace/log-analytics-delete-workspace.png)
4. När bekräftelsemeddelandefönstret visas där du uppmanas att bekräfta att arbetsytan har tagits bort klickar du på **Ja**.
   ![Bekräfta borttagning av arbetsyta](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Remove-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

## <a name="permanent-workspace-delete"></a>Permanent borttagning av arbetsyta
Metoden för mjuk borttagning kanske inte får plats i vissa scenarier, till exempel utveckling och testning, där du måste upprepa en distribution med samma inställningar och arbetsytans namn. I sådana fall kan du permanent ta bort arbetsytan och "åsidosätta" den mjuka borttagningsperioden. Den permanenta borttagningsåtgärden för arbetsytan släpper arbetsytans namn och du kan skapa en ny arbetsyta med samma namn.


> [!IMPORTANT]
> Använd permanent borttagning av arbetsytan med försiktighet eftersom den är oåterkallelig och du inte kan återställa arbetsytan och dess data.

Den permanenta arbetsytan kan för närvarande utföras via REST API.

> [!NOTE]
> Alla API-begäranden måste innehålla en innehavarauktoriseringstoken i begäranhuvudet.
>
> Du kan hämta token med:
> - [Appregistreringar](https://docs.microsoft.com/graph/auth/auth-concepts#access-tokens)
> - Navigera till Azure-portalen med utvecklarens konsol (F12) i webbläsaren. Titta i en av **batchen?** instanser för autentiseringssträngen under **Begär rubriker**. Detta kommer att vara i mönstret *tillstånd: Bärare <token> *. Kopiera och lägg till detta i ditt API-anrop som visas i exemplen.
> - Navigera till azure REST-dokumentationswebbplatsen. tryck på **Prova det** på alla API, kopiera Innehavstoken och lägg till det i ditt API-anrop.
Om du vill ta bort arbetsytan permanent använder du problemet [Arbetsytor - Ta bort REST]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/delete) API med en krafttagg:
>
> ```rst
> DELETE https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/workspaces/<workspace-name>?api-version=2015-11-01-preview&force=true
> Authorization: Bearer eyJ0eXAiOiJKV1Qi….
> ```
Om 'eyJ0eXAiOiJKV1Qi..." representerar den fullständiga auktoriseringstoken.

## <a name="recover-workspace"></a>Återskapa arbetsyta

Om du har deltagarbehörighet till prenumerations- och resursgruppen där arbetsytan var associerad före åtgärden för mjuk borttagning kan du återställa den under dess mjuka borttagningsperiod, inklusive dess data, konfiguration och anslutna agenter. Efter den mjuka borttagningsperioden kan arbetsytan inte återställas och tilldelas för permanent borttagning. Namn på borttagna arbetsytor bevaras under den mjuka borttagningsperioden och kan inte användas när du försöker skapa en ny arbetsyta.  

Du kan återställa en arbetsyta genom att återskapa den med hjälp av följande metoder för att skapa arbetsyta: [PowerShell](https://docs.microsoft.com/powershell/module/az.operationalinsights/New-AzOperationalInsightsWorkspace) eller [REST API]( https://docs.microsoft.com/rest/api/loganalytics/workspaces/createorupdate) så länge följande egenskaper fylls i med information om den borttagna arbetsytan:

* Prenumerations-ID:t
* Namn på resursgrupp
* Namn på arbetsyta
* Region

### <a name="powershell"></a>PowerShell
```PowerShell
PS C:\>Select-AzSubscription "subscription-name-the-workspace-was-in"
PS C:\>New-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name-the-workspace-was-in" -Name "deleted-workspace-name" -Location "region-name-the-workspace-was-in"
```

Arbetsytan och alla dess data återförs efter återställningen. Lösningar och länkade tjänster togs bort permanent från arbetsytan när den togs bort och dessa bör konfigureras om för att få arbetsytan till sitt tidigare konfigurerade tillstånd. Vissa av data kanske inte är tillgängliga för frågor efter återställningen av arbetsytan förrän de associerade lösningarna installeras om och deras scheman läggs till på arbetsytan.

> [!NOTE]
> * Återställning av arbetsyta stöds inte i [Azure-portalen](https://portal.azure.com). 
> * Återskapa en arbetsyta under den mjuka borttagningsperioden ger en indikation på att det här arbetsytenamnet redan används. 
> 
