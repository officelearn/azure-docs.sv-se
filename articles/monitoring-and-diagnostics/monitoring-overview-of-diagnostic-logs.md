---
title: "Översikt över Azure diagnostikloggar | Microsoft Docs"
description: "Lär dig vad Azure diagnostikloggar är och hur du kan använda dem för att förstå händelser som inträffar inom en Azure-resurs."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/21/2017
ms.author: johnkem; magoedte
ms.openlocfilehash: d59abde29fc7b73a799e5bf3659b02f824b693de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Samla in och använda loggdata från resurserna i Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Vad är Azure-resurs diagnostikloggar
**Azure resursnivå diagnostikloggar** är loggar orsakat av en resurs som innehåller omfattande, ofta data om användningen av den här resursen. Innehållet i de här loggarna varierar beroende på resurstypen. Till exempel finns Nätverkssäkerhetsgruppen regeln räknare och Key Vault granskningar två typer av loggar för resursen.

Resursnivå diagnostikloggar skiljer sig från den [aktivitetsloggen](monitoring-overview-activity-logs.md). Aktivitetsloggen ger inblick i åtgärder som utfördes på resurser i prenumerationen med Resource Manager, till exempel skapa en virtuell dator eller ta bort en logikapp. Aktivitetsloggen är en prenumeration på objektnivå logg. Resursnivå diagnostikloggar ger kunskaper om åtgärder som utförts i den här resursen, till exempel en hemlighet komma från ett Nyckelvalv.

Resursnivå diagnostikloggar också skilja sig från diagnostikloggar för gäst-OS-nivå. Gästoperativsystem diagnostikloggar är de som samlats in av en agent som körs i en virtuell dator eller andra stöd för resurstypen. Resursnivå diagnostikloggar kräver ingen agent och avbilda resurs-specifika data från Azure-plattformen, medan gäst-OS-nivå diagnostikloggar samla in data från operativsystemet och program som körs på en virtuell dator.

Inte alla resurser stöd för den nya typen av resurs diagnostikloggar som beskrivs här. Den här artikeln innehåller ett avsnitt som visar en lista över vilka typer av resurser stöd för de nya resursnivå diagnostikloggarna.

![Resursen diagnostik loggar jämfört med andra typer av loggar ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Vad du kan göra med resursnivå diagnostikloggar
Här följer några av de saker som du kan göra med resurs diagnostikloggar:

![Logiska placeringen av resursen diagnostikloggar](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)


* Spara dem på en [ **Lagringskonto** ](monitoring-archive-diagnostic-logs.md) för granskning eller Manuell kontroll. Du kan ange kvarhållning tid (i dagar) med **diagnostiska resursinställningar**.
* [Strömma dem till **Händelsehubbar** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) för införandet av en tjänst från tredje part eller anpassade analytics lösning, till exempel PowerBI.
* Analysera dem med [OMS logganalys](../log-analytics/log-analytics-azure-storage.md)

Du kan använda ett lagringskonto eller Händelsehubbar namnområde som inte är i samma prenumeration som avger loggar. Den användare som konfigurerar inställningen måste ha RBAC lämplig åtkomst till båda prenumerationer.

## <a name="resource-diagnostic-settings"></a>Diagnostikinställningar för resurs
Resursen diagnostikloggar för icke-beräkning resurser konfigureras med hjälp av diagnostikinställningar för resursen. **Diagnostiska resursinställningar** för en resurskontroll:

* Där resursen diagnostikloggar och mått som skickas (Storage-konto, Event Hubs och/eller OMS logganalys).
* Vilka kategorier loggen skickas och om mått data skickas också.
* Hur länge varje logg kategori ska behållas i ett lagringskonto
    - En kvarhållning av noll dagar innebär loggar behålls alltid. I annat fall kan värdet vara valfritt antal dagar mellan 1 och 2147483647.
    - Om bevarandeprinciper har angetts men lagring loggar i ett Storage-konto har inaktiverats (till exempel om endast Händelsehubbar eller OMS-alternativen är markerade), har bevarandeprinciper ingen effekt.
    - Bevarandeprinciper är tillämpade per dag, så i slutet av dagen (UTC) loggar från den dagen är nu utöver kvarhållning princip tas bort. Till exempel om du har en bevarandeprincip på en dag skulle i början av dagen idag loggar från dag före igår tas bort.

Dessa inställningar konfigureras enkelt via diagnostikinställningar för en resurs i Azure-portalen, via Azure PowerShell och CLI-kommandon eller via den [REST-API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!WARNING]
> Diagnostikloggar och mått för från gästen OS lager av beräkning resurser (till exempel virtuella datorer eller Service Fabric) Använd [en separat mekanism för konfiguration och val av utdata](../azure-diagnostics.md).
>
>

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Så här aktiverar du samling resurs diagnostikloggar
Samling av resursen diagnostikloggar kan aktiveras [som en del av att skapa en resurs i en Resource Manager-mall](./monitoring-enable-diagnostic-logs-using-template.md) eller när en resurs har skapats från den här resursen sida i portalen. Du kan också aktivera samlingen när som helst med Azure PowerShell eller CLI-kommandon eller med Azure-Monitor REST-API.

> [!TIP]
> Dessa instruktioner kan inte tillämpas direkt på alla resurser. Schemat finns längst ned på den här sidan om du vill förstå särskilda åtgärder som kan gälla för vissa typer av resurser.
>
>

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Aktivera insamling av resursen diagnostikloggar i portalen
Du kan aktivera insamling av resursen diagnostikloggar i Azure-portalen när en resurs har skapats genom att gå till en viss resurs eller genom att gå till Azure-Monitor. Aktivera detta via Azure-Monitor:

1. I den [Azure-portalen](http://portal.azure.com), navigera till Azure-Monitor och klicka på **diagnostikinställningar**

    ![Avsnittet av Azure-Monitor övervakning](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Om du vill filtrera listan efter resursgrupp eller resurstyp, och klicka sedan på resursen som du vill ange en diagnostikinställningen.

3. Om det finns inga inställningar på resursen har du valt, uppmanas du för att skapa en inställning. Klicka på ”Aktivera diagnostik”.

   ![Lägg till diagnostikinställningen - inga befintliga inställningar](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Om det finns befintliga inställningarna på resursen, visas en lista över inställningar som redan har konfigurerats på den här resursen. Klicka på ”Lägg till diagnostikinställningen”.

   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Ge din ange ett namn, markera kryssrutorna för varje mål som du vill skicka data och konfigurera vilken resurs som används för varje mål. Du kan också ange ett antal dagar att behålla dessa loggar med hjälp av den **bevarande (dagar)** skjutreglage (gäller endast för kontot lagringsplats). En kvarhållning av noll dagar lagrar loggarna på obestämd tid.
   
   ![Lägg till diagnostikinställningen - befintliga inställningar](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)
    
4. Klicka på **Spara**.

Den nya inställningen visas i din lista över inställningar för den här resursen efter en liten stund och diagnostikloggar skickas till de angivna målen när nya händelsedata genereras.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Aktivera insamling av resursen diagnostikloggar via PowerShell
Om du vill aktivera insamling av resursen diagnostikloggar via Azure PowerShell använder du följande kommandon:

Använd följande kommando för att aktivera lagring av diagnostiska loggar i ett lagringskonto:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

Storage-konto-ID är resurs-ID för lagringskontot som du vill skicka loggar.

Om du vill aktivera strömning av diagnostiska loggar till en händelsehubb, Använd följande kommando:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

Regel-ID för service bus är en sträng med formatet: `{Service Bus resource ID}/authorizationrules/{key name}`.

Använd följande kommando för att aktivera sändning av diagnostiska loggar till en logganalys-arbetsyta:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Du kan hämta resurs-ID för logganalys-arbetsytan med följande kommando:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

### <a name="enable-collection-of-resource-diagnostic-logs-via-cli"></a>Aktivera insamling av resursen diagnostikloggar via CLI
Om du vill aktivera insamling av resursen diagnostikloggar via Azure CLI, använder du följande kommandon:

Använd följande kommando för att aktivera lagring av diagnostiska loggar i ett Lagringskonto:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
```

Storage-konto-ID är resurs-ID för lagringskontot som du vill skicka loggar.

Om du vill aktivera strömning av diagnostiska loggar till en händelsehubb, Använd följande kommando:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Regel-ID för service bus är en sträng med formatet: `{Service Bus resource ID}/authorizationrules/{key name}`.

Använd följande kommando för att aktivera sändning av diagnostiska loggar till en logganalys-arbetsyta:

```azurecli
azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
```

Du kan kombinera dessa parametrar för att aktivera flera alternativ för utdata.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Aktivera insamling av resursen diagnostikloggar via REST API
Om du vill ändra diagnostikinställningar med hjälp av REST API för Azure-Monitor [dokumentet](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Hantera diagnostikinställningar för resurs i portalen
Se till att alla dina resurser har ställts in för diagnostikinställningar. Gå till **övervakaren** i portalen och öppna **diagnostikinställningar**.

![Den diagnostiska loggar bladet i portal](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Du kanske måste klicka på ”fler tjänster” efter avsnittet övervakaren.

Här kan du visa och filtrera alla resurser som stöder diagnostikinställningar för att se om de har aktiverat diagnostik. Du kan också öka detaljnivån till se om flera inställningar som är inställt på en resurs och kontrollera vilka storage-konto, Händelsehubbar namnområde och/eller logganalys-arbetsytan som data flödar till.

![Den diagnostiska loggar resulterar i portalen](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Lägga till en diagnostikinställningen öppnar vyn diagnostikinställningar, där du kan aktivera, inaktivera eller ändra inställningarna för diagnostik för den markerade resursen.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Tjänster som stöds, kategorier och scheman för resource diagnostikloggar
[Finns den här artikeln](monitoring-diagnostic-logs-schema.md) för en fullständig lista över tjänster som stöds och logg kategorier och scheman som används av tjänsterna.

## <a name="next-steps"></a>Nästa steg

* [Strömma resurs diagnostiska loggar till **Händelsehubbar**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Ändra resurs diagnostiska inställningar med hjälp av REST API för Azure-Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analysera loggar från Azure storage med logganalys](../log-analytics/log-analytics-azure-storage.md)
