---
title: Scheman i Azure Automation
description: Automationsscheman används för att schemalägga runbooks i Azure Automation för att starta automatiskt. Beskriver hur du skapar och hanterar ett schema i så att automatiskt starta en runbook på en viss tidpunkt eller ett återkommande schema.
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/08/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 6c7bd4d4249d304ee7c1df4ae4b8fc0af476b99c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Schemaläggning av en Runbook i Azure Automation

Om du vill schemalägga en runbook i Azure Automation för att starta vid en viss tidpunkt länka du det till ett eller flera scheman. Ett schema kan konfigureras för att köras en gång eller på en igen timvis eller daglig schema för runbooks i Azure-portalen. Du kan också schemalägga dem för varje vecka, månad, särskilda dagar i veckan eller dagar i månaden, eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan ha flera runbooks som är länkade till den.

> [!NOTE]
> Scheman stöder för närvarande inte Azure Automation DSC-konfigurationer.

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets

Cmdlets i följande tabell används för att skapa och hantera scheman med Windows PowerShell i Azure Automation. De levereras som en del av den [Azure PowerShell-modulen](/powershell/azure/overview).

| Cmdlet: ar | Beskrivning |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Hämtar ett schema. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Skapar ett nytt schema. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Tar bort ett schema. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Anger egenskaperna för ett befintligt schema. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Hämtar schemalagda runbooks. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associerar en runbook med ett schema. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates en runbook från ett schema. |

## <a name="creating-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för runbooks i Azure-portalen eller med Windows PowerShell.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  För att undvika att påverka dina runbooks och processer som de automatisera, bör du testa alla runbooks som har länkats scheman med ett Automation-konto som är dedikerad för testning.  Detta verifierar din schemalagda runbooks fortsätter att fungera korrekt om inte, du kan och ytterligare felsöka tillämpar ändringar som krävs innan du migrerar den uppdaterade runbook-versionen till produktionen.
> Automation-kontot inte automatiskt får nya versioner av moduler om du har uppdaterat dem manuellt genom att välja den [Update Azure moduler](automation-update-azure-modules.md) alternativet från den **moduler**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure-portalen

1. Välj i Azure-portalen från ditt automation-konto **scheman** under avsnittet **delade resurser** till vänster.
1. Klicka på **lägga till ett schema** överst på sidan.
1. På den **nytt schema** rutan Ange ett **namn** och eventuellt en **beskrivning** för det nya schemat.
1. Välj om schemat har körts en gång, eller på ett reoccurring schema genom att välja **när** eller **återkommande**. Om du väljer **när** ange en **starttid**, och klicka sedan på **skapa**. Om du väljer **återkommande**, ange en **starttid** och frekvens för hur ofta du vill att runbook ska upprepas - av **timme**, **dag**, **vecka**, eller av **månad**. Om du väljer **vecka** eller **månad** från den nedrullningsbara listan den **upprepning alternativet** visas i fönstret och vid val av den **upprepning alternativet** fönstret visas och du kan välja dag i veckan om du har valt **vecka**. Om du har valt **månad**, du kan välja efter **veckodagar** eller särskilda dagar i månaden i kalendern och slutligen vill du köra den på den sista dagen i månaden eller inte och klicka sedan på **OK**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Skapa ett nytt schema med Windows PowerShell

Du använder den [ny AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) för att skapa scheman. Du anger starttiden för schemat och frekvensen ska köras.

Följande exempelkommandon visar hur du skapar ett schema för den 15: e och 30: e i månaden med en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Länka ett schema till en runbook

En runbook kan länkas till flera scheman och ett schema kan ha flera runbooks som är länkade till den. Om en runbook har parametrar, kan du ange värden för dessa. Du måste ange värden för alla obligatoriska parametrar och kan ange värden för valfria parametrar. Dessa värden används varje gång runbook startas med det här schemat. Du kan koppla samma runbook till ett annat schema och ange olika parametervärden.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en runbook med Azure-portalen

1. Välj i Azure-portalen från ditt automation-konto **Runbooks** under avsnittet **Processautomatisering** till vänster.
1. Klicka på namnet på runbooken som ska schemaläggas.
1. Om runbook inte är för närvarande kopplad till ett schema, erbjuds du alternativet för att skapa ett nytt schema eller länka till ett befintligt schema.
1. Om runbooken har parametrar, kan du välja alternativet **ändra körningsinställningar (standard: Azure)** och **parametrar** fönstret visas där du kan ange information i enlighet med detta.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Länka ett schema till en runbook med Windows PowerShell

Du kan använda den [registrera AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern parametrar. Mer information om specificering av parametervärden finns [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).
Följande exempelkommandon visar hur du länkar ett schema till en runbook med en Azure Resource Manager-cmdlet med parametrar.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-more-frequently"></a>Schemalägga runbooks oftare

Ett schema i Azure Automation kan konfigureras för de vanligaste intervallet är en timme. Om du behöver scheman för att köra oftare än som finns det två alternativ:

* Skapa en [webhook](automation-webhooks.md) för runbook och Använd [Azure Scheduler](../scheduler/scheduler-get-started-portal.md) att anropa webhooken. Azure Scheduler ger mer detaljerad granularitet när du definierar ett schema.

* Skapa fyra scheman alla startar inom 15 minuter från varandra körs en gång i timmen. Det här scenariot kan runbook körs var 15: e minut med olika scheman.

## <a name="disabling-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs valfri runbook som är länkad till den inte längre på schemat. Du kan manuellt inaktivera ett schema eller ange en förfallotid för scheman med en frekvens när de skapas. När förfallotid har uppnåtts har schemat inaktiverats.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure-portalen

1. Välj i Azure-portalen från ditt Automation-konto **scheman** under avsnittet **delade resurser** till vänster.
1. Klicka på namnet på ett schema för att öppna informationsfönstret.
1. Ändra **aktiverat** till **nr**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Inaktivera ett schema med Windows PowerShell

Du kan använda den [Set AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat, ange **FALSKT** för den **IsEnabled** parameter.

Följande exempelkommandon visar hur du inaktiverar ett schema för en runbook med en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Kom igång med runbooks i Azure Automation finns [starta en Runbook i Azure Automation](automation-starting-a-runbook.md)