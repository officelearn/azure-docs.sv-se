---
title: Arkivera & rapport med Azure Monitor – Azure AD-berättigandehantering
description: Lär dig hur du arkiverar loggar och skapar rapporter med Azure Monitor i Azure Active Directory-berättigandehantering.
services: active-directory
documentationCenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/22/2020
ms.author: barclayn
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: 070b7c5e0fef7d50f84271190432a65d29699bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80128624"
---
# <a name="archive-logs-and-reporting-on-azure-ad-entitlement-management-in-azure-monitor"></a>Arkivera loggar och rapportering om Hantering av Azure AD-berättigande i Azure Monitor

Azure AD lagrar granskningshändelser i upp till 30 dagar i granskningsloggen. Du kan dock behålla granskningsdata längre än standardlagringsperioden, som beskrivs i [Hur länge azure AD lagrar rapporteringsdata?](../reports-monitoring/reference-reports-data-retention.md) Du kan sedan använda arbetsböcker och anpassade frågor och rapporter om dessa data.


## <a name="configure-azure-ad-to-use-azure-monitor"></a>Konfigurera Azure AD för att använda Azure Monitor
Innan du använder Azure Monitor-arbetsböckerna måste du konfigurera Azure AD för att skicka en kopia av dess granskningsloggar till Azure Monitor.

Arkivering av Azure AD-granskningsloggar kräver att du har Azure Monitor i en Azure-prenumeration. Du kan läsa mer om förutsättningarna och uppskattade kostnader för att använda Azure Monitor i [Azure AD-aktivitetsloggar i Azure Monitor](../reports-monitoring/concept-activity-logs-azure-monitor.md).

**Viktig roll**: Global Admin

1. Logga in på Azure-portalen som en användare som är global administratör. Kontrollera att du har åtkomst till resursgruppen som innehåller Arbetsytan Azure Monitor.
 
1. Välj **Azure Active Directory** och klicka sedan på **Diagnostikinställningar** under Övervakning på menyn för vänster navigering. Kontrollera om det redan finns en inställning för att skicka granskningsloggarna till arbetsytan.

1. Om det inte redan finns någon inställning klickar du på **Lägg till diagnostikinställning**. Följ instruktionerna i artikeln [Integrera Azure AD-loggar med Azure Monitor-loggar](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md#send-logs-to-azure-monitor) för att skicka Azure AD-granskningsloggen till Arbetsytan Azure Monitor.

    ![Fönstret Diagnostikinställningar](./media/entitlement-management-logs-and-reporting/audit-log-diagnostics-settings.png)


1. När loggen har skickats till Azure Monitor väljer du **Log Analytics-arbetsytor**och väljer arbetsytan som innehåller Azure AD-granskningsloggarna.

1. Välj **Användning och uppskattade kostnader** och klicka på **Datakvarhållning**. Ändra skjutreglaget till hur många dagar du vill behålla data för att uppfylla granskningskraven.

    ![Fönstret Logga Analytics-arbetsytor](./media/entitlement-management-logs-and-reporting/log-analytics-workspaces.png)

## <a name="create-custom-azure-monitor-queries-using-the-azure-portal"></a>Skapa anpassade Azure Monitor-frågor med Azure-portalen
Du kan skapa egna frågor om Azure AD-granskningshändelser, inklusive rättighetshanteringshändelser.  

1. I Azure Active Directory på Azure-portalen klickar du på **Loggar** under avsnittet Övervakning på den vänstra navigeringsmenyn för att skapa en ny frågesida.

1. Arbetsytan ska visas längst upp till vänster på frågesidan. Om du har flera Azure Monitor-arbetsytor och arbetsytan du använder för att lagra Azure AD-granskningshändelser visas klickar du på **Välj omfattning**. Välj sedan rätt prenumeration och arbetsyta.

1. Ta sedan bort strängen "sök *" i frågetextområdet och ersätt den med följande fråga:

    ```
    AuditLogs | where Category == "EntitlementManagement"
    ```

1. Klicka sedan på **Kör**. 

    ![Klicka på Kör för att starta frågan](./media/entitlement-management-logs-and-reporting/run-query.png)

Tabellen visar granskningslogghändelserna för rättighetshantering från den senaste timmen som standard. Du kan ändra inställningen "Tidsintervall" för att visa äldre händelser. Om du ändrar den här inställningen visas dock bara händelser som inträffade efter att Azure AD konfigurerats för att skicka händelser till Azure Monitor.

Om du vill veta de äldsta och senaste granskningshändelserna som finns i Azure Monitor använder du följande fråga:

```
AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type
```

Mer information om kolumnerna som lagras för granskningshändelser i Azure Monitor finns [i Tolka Azure AD-granskningsloggarschemat i Azure Monitor](../reports-monitoring/reference-azure-monitor-audit-log-schema.md).

## <a name="create-custom-azure-monitor-queries-using-azure-powershell"></a>Skapa anpassade Azure Monitor-frågor med Azure PowerShell

Du kan komma åt loggar via PowerShell när du har konfigurerat Azure AD för att skicka loggar till Azure Monitor. Skicka sedan frågor från skript eller PowerShell-kommandoraden, utan att behöva vara global administratör i klienten. 

### <a name="ensure-the-user-or-service-principal-has-the-correct-role-assignment"></a>Kontrollera att användarens eller tjänstens huvudnamn har rätt rolltilldelning

Se till att du, användaren eller tjänstens huvudnamn som autentiserar till Azure AD, är i lämplig Azure-roll i Log Analytics-arbetsytan. Rollalternativen är antingen Log Analytics Reader eller Log Analytics Contributor. Om du redan har en av dessa roller går du till [Hämta Logganalys-ID med en Azure-prenumeration](#retrieve-log-analytics-id-with-one-azure-subscription).

Så här anger du rolltilldelningen och skapar en fråga:
1. Leta reda på [arbetsytan Log Analytics](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces
)i Azure-portalen .

1. Välj **Åtkomstkontroll (IAM)**.

1. Klicka sedan på **Lägg till** om du vill lägga till en rolltilldelning.

    ![Lägg till en rolltilldelning](./media/entitlement-management-logs-and-reporting/workspace-set-role-assignment.png)

### <a name="install-azure-powershell-module"></a>Installera Azure PowerShell-modul

När du har rätt rolltilldelning startar du PowerShell och [installerar Azure PowerShell-modulen](/powershell/azure/install-az-ps?view=azps-3.3.0) (om du inte redan har gjort det) genom att skriva:

```azurepowershell
install-module -Name az -allowClobber -Scope CurrentUser
```
    
Nu är du redo att autentisera till Azure AD och hämta id:t för den Log Analytics-arbetsyta som du frågar.

### <a name="retrieve-log-analytics-id-with-one-azure-subscription"></a>Hämta Log Analytics-ID med en Azure-prenumeration
Om du bara har en enda Azure-prenumeration och en enda Log Analytics-arbetsyta skriver du följande för att autentisera till Azure AD, ansluta till den prenumerationen och hämta arbetsytan:
 
```azurepowershell
Connect-AzAccount
$wks = Get-AzOperationalInsightsWorkspace
```
 
### <a name="retrieve-log-analytics-id-with-multiple-azure-subscriptions"></a>Hämta Log Analytics-ID med flera Azure-prenumerationer

 [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) fungerar i en prenumeration i taget. Så om du har flera Azure-prenumerationer, du vill se till att du ansluter till den som har Log Analytics arbetsyta med Azure AD-loggar. 
 
 Följande cmdlets visar en lista över prenumerationer och hittar id:n för prenumerationen som har log analytics-arbetsytan:
 
```azurepowershell
Connect-AzAccount
$subs = Get-AzSubscription
$subs | ft
```
 
Du kan återauktorisera och associera din PowerShell-session med `Connect-AzAccount –Subscription $subs[0].id`den prenumerationen med ett kommando, till exempel . Mer information om hur du autentiserar till Azure från PowerShell, inklusive icke-interaktivt, finns [i Logga in med Azure PowerShell](/powershell/azure/authenticate-azureps?view=azps-3.3.0&viewFallbackFrom=azps-2.5.0
).

Om du har flera Log Analytics-arbetsytor i den prenumerationen returnerar cmdlet [Get-AzOperationalInsightsWorkspace](/powershell/module/Az.OperationalInsights/Get-AzOperationalInsightsWorkspace) listan över arbetsytor. Sedan kan du hitta den som har Azure AD-loggar. Fältet `CustomerId` som returneras av den här cmdleten är detsamma som värdet för "Workspace id" som visas i Azure-portalen i logganalysarbetsytaöversikten.
 
```powershell
$wks = Get-AzOperationalInsightsWorkspace
$wks | ft CustomerId, Name
```

### <a name="send-the-query-to-the-log-analytics-workspace"></a>Skicka frågan till arbetsytan Log Analytics
När du har identifierat en arbetsyta kan du slutligen använda [Invoke-AzOperationalInsightsQuery](/powershell/module/az.operationalinsights/Invoke-AzOperationalInsightsQuery?view=azps-3.3.0
) för att skicka en Kusto-fråga till arbetsytan. Dessa frågor är skrivna i [Kusto frågespråk](https://docs.microsoft.com/azure/kusto/query/).
 
Du kan till exempel hämta datumintervallet för granskningshändelseposterna från log analytics-arbetsytan, med PowerShell-cmdletar för att skicka en fråga som:
 
```powershell
$aQuery = "AuditLogs | where TimeGenerated > ago(3653d) | summarize OldestAuditEvent=min(TimeGenerated), NewestAuditEvent=max(TimeGenerated) by Type"
$aResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $aQuery
$aResponse.Results |ft
```

Du kan också hämta rättighetshanteringshändelser med en fråga som:

```azurepowershell
$bQuery = = 'AuditLogs | where Category == "EntitlementManagement"'
$bResponse = Invoke-AzOperationalInsightsQuery -WorkspaceId $wks[0].CustomerId -Query $Query
$bResponse.Results |ft 
```

## <a name="next-steps"></a>Nästa steg:
- [Skapa interaktiva rapporter med Azure Monitor-arbetsböcker](../../azure-monitor/app/usage-workbooks.md) 

