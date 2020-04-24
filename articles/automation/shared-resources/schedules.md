---
title: Hantera scheman i Azure Automation
description: Automation-scheman används för att schemalägga Runbooks i Azure Automation att starta automatiskt. Beskriver hur du skapar och hanterar ett schema i så att du automatiskt kan starta en Runbook vid en viss tidpunkt eller enligt ett återkommande schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 97dc7cb90f5ffc577a007c23074a0e8a75788fab
ms.sourcegitcommit: f7d057377d2b1b8ee698579af151bcc0884b32b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2020
ms.locfileid: "82114028"
---
# <a name="manage-schedules-in-azure-automation"></a>Hantera scheman i Azure Automation

Om du vill schemalägga en Runbook i Azure Automation starta vid en viss tid länkar du den till ett eller flera scheman. Ett schema kan konfigureras att antingen köras en gång eller på ett varje timme eller ett dags schema för Runbooks i Azure Portal. Du kan också schemalägga dem för varje vecka, varje månad, specifika dagar i veckan eller dagar i månaden eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks.

> [!NOTE]
> Scheman stöder för närvarande inte Azure Automation DSC-konfigurationer.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar

Cmdletarna i följande tabell används för att skapa och hantera scheman med PowerShell i Azure Automation. De levereras som en del av [Azure PowerShell-modulen](/powershell/azure/overview).

| Cmdletar | Beskrivning |
|:--- |:--- |
| [Get-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Hämtar ett schema. |
| [Get-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Hämtar schemalagda Runbooks. |
| [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Skapar ett nytt schema. |
| [Registrera – AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associerar en Runbook med ett schema. |
| [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Tar bort ett schema. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Anger egenskaperna för ett befintligt schema. |
| [Avregistrera-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Kopplar bort en Runbook från ett schema. |

## <a name="creating-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för Runbooks i Azure Portal eller med PowerShell.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  För att undvika att dina runbooks påverkas och de processer de automatiseras bör du först testa alla Runbooks som har länkade scheman med ett Automation-konto dedikerat för testning.  Detta validerar att dina schemalagda Runbooks fortsätter att fungera korrekt och om inte, kan du ytterligare felsöka och tillämpa eventuella ändringar som krävs innan du migrerar den uppdaterade Runbook-versionen till produktion.
> Ditt Automation-konto får inte automatiskt några nya versioner av moduler om du inte har uppdaterat dem manuellt genom att välja alternativet [Uppdatera Azure-moduler](../automation-update-azure-modules.md) från **modulerna**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure Portal

1. I Azure Portal, från ditt Automation-konto, väljer du **scheman** under avsnittet **delade resurser** till vänster.
2. Klicka på **Lägg till ett schema** högst upp på sidan.
3. I fönstret Nytt schema anger du ett namn och eventuellt en beskrivning för det nya schemat.
4. Välj om schemat ska köras en gång eller om schemat ska köras genom att välja **en gång** eller **återkommande**. Om du väljer en **gång**anger du en start tid och klickar sedan på **skapa**. Om du väljer **återkommande**anger du en start tid. För att **Upprepa var**väljer du hur ofta du vill att runbooken ska upprepas per timme, dag, vecka eller månad.
    1. Om du väljer **vecka**visas vecko dagarna som du kan välja bland. Välj så många dagar som du vill. Den första körningen av ditt schema sker på den första dagen som väljs efter start tiden. Om du till exempel vill välja ett helg schema väljer du lördag och söndag. 
    
       ![Inställning av återkommande helg schema](../media/schedules/week-end-weekly-recurrence.png)

    2. Om du väljer **månad**får du olika alternativ. Välj antingen **månads dagar** eller **vecko dagar**för alternativet **månatlig förekomst** . Om du väljer **månads dagar**visas en kalender som gör det möjligt att välja så många dagar som du vill. Om du väljer ett datum, till exempel 31 som inte inträffar under den aktuella månaden, körs inte schemat. Om du vill att schemat ska köras den senaste dagen väljer du **Ja** under **kör på sista dagen i månaden**. Om du väljer **vecko dagar**visas alternativet **Upprepa varje** . Välj **första**, **andra**, **tredje**, **fjärde**eller **sista**. Välj slutligen en dag som ska upprepas.

       ![Månads schema på första, femtonde och sista dagen i månaden](../media/schedules/monthly-first-fifteenth-last.png)

5. När du är färdig klickar du på **skapa**.

### <a name="create-a-new-schedule-with-powershell"></a>Skapa ett nytt schema med PowerShell

Använd cmdleten [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) för att skapa scheman. Du anger start tiden för schemat och hur ofta den ska köras. I följande exempel visas hur du skapar många olika schema scenarier.

#### <a name="create-a-one-time-schedule"></a>Skapa ett engångs schema

Följande exempel kommandon skapar ett schema för en tidpunkt.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Skapa ett återkommande schema

I följande exempel visas hur du skapar ett återkommande schema som körs varje dag 1:12:00 för ett år.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Skapa ett veckovis återkommande schema

I följande exempel visas hur du skapar ett vecko schema som bara körs på vardagar.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Skapa ett veckovis återkommande schema för helger

Följande exempel kommandon visar hur du skapar ett vecko schema som bara körs på helger.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Skapa ett återkommande schema för de första, 15: e och sista dagarna i månaden

I följande exempel visas hur du skapar ett återkommande schema som körs på den första, 15: e och sista dagen i månaden.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Länka ett schema till en Runbook

En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks. Om en Runbook har parametrar, kan du ange värden för dem. Du måste ange värden för alla obligatoriska parametrar och kan ange värden för eventuella valfria parametrar. Dessa värden används varje gång som Runbook startas med det här schemat. Du kan koppla samma Runbook till ett annat schema och ange olika parameter värden.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en Runbook med Azure Portal

1. I Azure Portal, från ditt Automation-konto, väljer du **Runbooks** under **process automatisering**.
2. Klicka på namnet på den runbook som ska schemaläggas.
3. Om runbooken inte är länkad till ett schema, kan du välja att skapa ett nytt schema eller länka till ett befintligt schema.
4. Om runbook har parametrar, kan du välja alternativet **ändra körnings inställningar (standard: Azure)** så visas fönstret parametrar. Du kan ange parameter information här.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Länka ett schema till en Runbook med PowerShell

Använd cmdleten [register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern Parametrar . Mer information om att ange parameter värden finns [i starta en Runbook i Azure Automation](../automation-starting-a-runbook.md).
I följande exempel visas hur du länkar ett schema till en Runbook med hjälp av en Azure Resource Manager-cmdlet med parametrar.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Schemalägga Runbooks att köras oftare

Det vanligaste intervallet som ett schema i Azure Automation kan konfigureras för är en timme. Om du behöver schemalägga körning oftare än så finns det två alternativ:

* Skapa en [webhook](../automation-webhooks.md) för runbooken och Använd [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) för att anropa webhooken. Azure Logic Apps ger mer detaljerade granularitet när du definierar ett schema.

* Skapa fyra scheman som börjar inom 15 minuter från varandra och som körs en gång i timmen. Det här scenariot gör att runbooken kan köras var 15: e minut med olika scheman.

## <a name="disabling-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs inte längre någon Runbook som är länkad till den schemat. Du kan manuellt inaktivera ett schema eller ange en förfallo tid för scheman med en frekvens när du skapar dem. När förfallo tiden nåtts inaktive ras schemat.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure Portal

1. I ditt Automation-konto väljer du **scheman** under **delade resurser**.
2. Klicka på namnet på ett schema för att öppna informations fönstret.
3. Ändringen har **Aktiver ATS** till **Nej**.

> [!NOTE]
> Om du vill inaktivera ett schema som har en start tid tidigare, måste du ändra start datumet till en gång i framtiden innan du sparar det.

### <a name="disable-a-schedule-with-powershell"></a>Inaktivera ett schema med PowerShell

Använd cmdleten [set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat anger du falskt för `IsEnabled` parametern.

I följande exempel visas hur du inaktiverar ett schema för en Runbook med hjälp av en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="removing-a-schedule"></a>Ta bort ett schema

När du är redo att ta bort dina scheman kan du antingen använda Azure Portal eller- `Remove-AzureRmAutomationSchedule` cmdleten. Kom ihåg att du bara kan ta bort ett schema som har inaktiverats enligt beskrivningen i föregående avsnitt.

### <a name="remove-a-schedule-using-the-azure-portal"></a>Ta bort ett schema med hjälp av Azure Portal

1. I ditt Automation-konto väljer du **scheman** under **delade resurser**.
2. Klicka på namnet på ett schema för att öppna informations fönstret.
3. Klicka på **ta bort**.

### <a name="remove-a-schedule-with-powershell"></a>Ta bort ett schema med PowerShell

Du kan använda cmdleten [Remove-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) för att ta bort ett befintligt schema. 

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Remove-AzureRmAutomationSchedule -AutomationAccountName $automationAccountName ` -Name $scheduleName -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med Runbooks i Azure Automation finns [i starta en Runbook i Azure Automation](../automation-starting-a-runbook.md).