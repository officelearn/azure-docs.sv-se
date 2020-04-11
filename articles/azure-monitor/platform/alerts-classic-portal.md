---
title: Skapa och hantera klassiska måttaviseringar med Azure Monitor
description: Lär dig hur du använder Azure portal, CLI eller Powershell för att skapa, visa och hantera klassiska måttaviseringsregler.
author: yanivlavi
ms.author: yalavi
ms.topic: conceptual
ms.date: 09/18/2018
ms.openlocfilehash: e635c243a887690fb1f7a5dcd017c1130d74a747
ms.sourcegitcommit: fb23286d4769442631079c7ed5da1ed14afdd5fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/10/2020
ms.locfileid: "81114586"
---
# <a name="create-view-and-manage-classic-metric-alerts-using-azure-monitor"></a>Skapa, visa och hantera klassiska måttaviseringar med Azure Monitor

Klassiska måttaviseringar i Azure Monitor är ett sätt att få ett meddelande när ett av dina mått överskrider ett tröskelvärde. Klassiska måttaviseringar är en äldre funktion som endast gör det möjligt att varna på icke-dimensionella mått. Det finns en befintlig nyare funktionalitet som kallas Måttaviseringar som har förbättrad funktionalitet jämfört med klassiska måttaviseringar. Du kan läsa mer om de nya måttaviseringar funktionalitet i [måttaviseringar översikt](../../azure-monitor/platform/alerts-metric-overview.md). I den här artikeln beskriver vi hur du skapar, visar och hanterar klassiska måttaviseringsregler via Azure Portal, Azure CLI och Powershell.

## <a name="with-azure-portal"></a>Med Azure-portal

1. Leta reda på den resurs som du vill övervaka i [portalen](https://portal.azure.com/)och markera den sedan.

2. Välj **Aviseringar (Klassisk)** i avsnittet **ÖVERVAKNING** . Texten och ikonen kan variera något för olika resurser. Om du inte hittar **aviseringar (klassiskt)** här kan du hitta den i aviseringar eller **varningsregler** . **Alert Rules**

    ![Övervakning](media/alerts-classic-portal/AlertRulesButton.png)

3. Markera kommandot **Lägg till måttavisering (klassisk)** och fyll sedan i fälten.

    ![Lägg till avisering](media/alerts-classic-portal/AddAlertOnlyParamsPage.png)

4. **Namnge** din aviseringsregel. Välj sedan en **beskrivning**, som också visas i e-postmeddelanden.

5. Välj det **mått** som du vill övervaka. Välj sedan ett **villkor** **och tröskelvärde** för måttet. Välj också **den** tidsperiod som måttregeln måste uppfyllas innan aviseringen utlöses. Om du till exempel använder perioden "Under de senaste 5 minuterna" och din avisering söker efter en CPU över 80 %, utlöser aviseringen när processorn har varit konsekvent över 80 % i 5 minuter. När den första utlösaren inträffar utlöses den igen när processorn förblir under 80 % i 5 minuter. Cpu-måttmätningen sker varje minut.

6. Välj **E-postägare...** om du vill att administratörer och medadministratörer ska få e-postmeddelanden när aviseringen utlöses.

7. Om du vill skicka meddelanden till ytterligare e-postadresser när aviseringen utlöses lägger du till dem i fältet **Ytterligare administratörsmeddelanden.** Separera flera e-postmeddelanden med semikolon, i följande format: *e-post\@contoso.com;email2\@contoso.com*

8. Placera en giltig URI i **webhook-fältet** om du vill att den ska anropas när aviseringen utlöses.

9. Om du använder Azure Automation kan du välja en runbook som ska köras när aviseringen utlöses.

10. Skapa aviseringen genom att välja **OK**.

Inom några minuter är aviseringen aktiv och utlöser som tidigare beskrivits.

När du har skapat en avisering kan du markera den och utföra någon av följande uppgifter:

* Visa ett diagram som visar tröskelvärdet för mått och de faktiska värdena från föregående dag.
* Redigera eller ta bort den.
* **Inaktivera** eller **Aktivera** det om du tillfälligt vill stoppa eller återuppta mottagningsmeddelanden för den aviseringen.

## <a name="with-azure-cli"></a>Med Azure CLI

I föregående avsnitt beskrivs hur du skapar, visar och hanterar måttaviseringsregler med Azure-portalen. I det här avsnittet beskrivs hur du gör samma sak med Azure [CLI.](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest) Snabbaste sättet att börja använda Azure CLI är via [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview?view=azure-cli-latest).

### <a name="get-all-classic-metric-alert-rules-in-a-resource-group"></a>Hämta alla klassiska måttvarningsregler i en resursgrupp

```azurecli
az monitor alert list --resource-group <group name>
```

### <a name="see-details-of-a-particular-classic-metric-alert-rule"></a>Se information om en viss klassisk måttvarningsregel

```azurecli
az monitor alert show --resource-group <group name> --name <alert name>
```

### <a name="create-a-classic-metric-alert-rule"></a>Skapa en klassisk måttvarningsregel

```azurecli
az monitor alert create --name <alert name> --resource-group <group name> \
    --action email <email1 email2 ...> \
    --action webhook <URI> \
    --target <target object ID> \
    --condition "<METRIC> {>,>=,<,<=} <THRESHOLD> {avg,min,max,total,last} ##h##m##s"
```

### <a name="delete-a-classic-metric-alert-rule"></a>Ta bort en klassisk måttvarningsregel

```azurecli
az monitor alert delete --name <alert name> --resource-group <group name>
```

## <a name="with-powershell"></a>Med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

I det här avsnittet visas hur du använder PowerShell-kommandon skapa, visa och hantera klassiska måttaviseringar. Exemplen i artikeln visar hur du kan använda Azure Monitor-cmdlets för klassiska måttaviseringar.

1. Om du inte redan har gjort det konfigurerar du PowerShell så att det körs på datorn. Mer information finns i [Så här installerar och konfigurerar du PowerShell](/powershell/azure/overview). Du kan också granska hela listan över Azure Monitor PowerShell-cmdlets på [Azure Monitor (Insights) Cmdlets](https://docs.microsoft.com/powershell/module/az.applicationinsights).

2. Logga först in på din Azure-prenumeration.

    ```powershell
    Connect-AzAccount
    ```

3. Du ser en inloggningsskärm. När du loggar in visas ditt konto, TenantID och standardprenumerations-ID. Alla Azure-cmdlets fungerar i samband med din standardprenumeration. Om du vill visa listan över prenumerationer som du har åtkomst till använder du följande kommando:

    ```powershell
    Get-AzSubscription
    ```

4. Om du vill ändra arbetskontexten till en annan prenumeration använder du följande kommando:

    ```powershell
    Set-AzContext -SubscriptionId <subscriptionid>
    ```

5. Du kan hämta alla klassiska måttvarningsregler för en resursgrupp:

    ```powershell
    Get-AzAlertRule -ResourceGroup montest
    ```

6. Du kan visa information om en klassisk måttvarningsregel

    ```powershell
    Get-AzAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
    ```

7. Du kan hämta alla varningsregler som angetts för en målresurs. Till exempel alla varningsregler som anges på en virtuell dator.

    ```powershell
    Get-AzAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
    ```

8. Klassiska varningsregler kan inte längre skapas via PowerShell. För att skapa en aviseringsregel måste du använda det nya kommandot [Add-AzMetricAlertRule.](/powershell/module/az.monitor/add-azmetricalertrule)

## <a name="next-steps"></a>Nästa steg

- [Skapa en klassisk måttavisering med en Resource Manager-mall](../../azure-monitor/platform/alerts-enable-template.md).
- [Låt en klassisk måttavisering meddela ett icke-Azure-system med hjälp av en webhook](../../azure-monitor/platform/alerts-webhooks.md).
