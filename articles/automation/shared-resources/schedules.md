---
title: Scheman i Azure Automation
description: Automatiseringsscheman används för att schemalägga runbooks i Azure Automation för att starta automatiskt. Beskriver hur du skapar och hanterar ett schema så att du automatiskt kan starta en runbook vid en viss tidpunkt eller på ett återkommande schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c4898ba62abdc42d95b77b9a77387bfe71fb4771
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79252667"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Schemaläggning av en Runbook i Azure Automation

Om du vill schemalägga en runbook i Azure Automation så att den startar vid en viss tidpunkt länkar du den till ett eller flera scheman. Ett schema kan konfigureras för att antingen köras en gång eller på ett återkommande tim- eller dagligt schema för runbooks i Azure-portalen. Du kan också schemalägga dem för veckovisa, månadsvisa, specifika dagar i veckan eller dagarna i månaden, eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks.

> [!NOTE]
> Scheman stöder för närvarande inte Azure Automation DSC-konfigurationer.

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar

Cmdlets i följande tabell används för att skapa och hantera scheman med PowerShell i Azure Automation. De levereras som en del av [Azure PowerShell-modulen](/powershell/azure/overview).

| Cmdletar | Beskrivning |
|:--- |:--- |
| [Hämta-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Hämtar ett schema. |
| [Ny-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Skapar ett nytt schema. |
| [Ta bort AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Tar bort ett schema. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Anger egenskaperna för ett befintligt schema. |
| [Hämta AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Hämtar schemalagda runbooks. |
| [Registrera-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associerar en runbook med ett schema. |
| [Avregistrera-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Tar bort en runbook från ett schema. |

## <a name="creating-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för runbooks i Azure-portalen eller med PowerShell.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  Om du vill undvika att påverka dina runbooks och de processer som de automatiserar bör du först testa alla runbooks som har länkade scheman med ett Automation-konto som är dedikerat för testning.  Detta verifierar att dina schemalagda runbooks fortsätter att fungera korrekt och om inte, kan du ytterligare felsöka och tillämpa alla ändringar som krävs innan du migrerar den uppdaterade runbook-versionen till produktion.
> Ditt Automation-konto får inte automatiskt några nya versioner av moduler om du inte har uppdaterat dem manuellt genom att välja alternativet [Uppdatera Azure-moduler](../automation-update-azure-modules.md) från **modulerna**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Så här skapar du ett nytt schema i Azure-portalen

1. I Azure-portalen, från ditt **automatiseringskonto,** väljer du Scheman under avsnittet **Delade resurser** till vänster.
2. Klicka på **Lägg till ett schema** högst upp på sidan.
3. Skriv ett **namn** i fönstret **Nytt schema** och eventuellt en **beskrivning** för det nya schemat.
4. Välj om schemat körs en gång eller på ett återkommande schema genom att välja **En gång** eller **Återkommande**. Om du väljer **En gång** anger du en **starttid**och klickar sedan på **Skapa**. Om du väljer **Återkommande**anger du en **starttid** och för **Upprepa varje**väljer du frekvensen för hur ofta du vill att runbooken ska upprepas - **per timme,** **dag,** **vecka**eller **månad**.
    1. Om du väljer **vecka**får du en lista över de veckodagar du ska välja mellan. Välj så många dagar du vill. Den första körningen av ditt schema kommer att ske den första dagen som valts efter starttiden. Om du till exempel vill välja ett helgschema väljer du **lördag** och **söndag**.

       ![Ställa in återkommande schema för helg](../media/schedules/week-end-weekly-recurrence.png)

    2. Om du väljer **månad**får du olika alternativ. För alternativet **Månatliga förekomster** väljer du antingen **Månadsdagar** eller **Veckodagar**. Om du väljer **Månadsdagar**visas en kalender som gör att du kan välja så många dagar du vill. Om du väljer ett datum som den 31:a som inte inträffar under den aktuella månaden körs inte schemat. Om du vill att schemat ska köras den sista dagen väljer du **Ja** under **Kör den sista dagen i månaden**. Om du väljer **Veckodagar**visas **alternativet Upprepa varje** alternativ. Välj **Första**, **Andra**, **Tredje**, **Fjärde**eller **Sista**. Slutligen välja en dag att upprepa på.

       ![Månadsschema på första, femtonde och sista dagen i månaden](../media/schedules/monthly-first-fifteenth-last.png)

5. När du är klar klickar du på **Skapa**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Så här skapar du ett nytt schema med PowerShell

Du använder cmdleten [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) för att skapa scheman. Du anger starttiden för schemat och hur ofta det ska köras. Följande exempel visar hur du skapar många olika schemascenarier.

#### <a name="create-a-one-time-schedule"></a>Skapa ett tidsschema

Följande exempelkommandon skapar ett tidsschema.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Skapa ett återkommande schema

Följande exempelkommandon visar hur du skapar ett återkommande schema som körs varje dag klockan 13:00 under ett år.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Skapa ett återkommande veckoschema

Följande exempelkommandon visar hur du skapar ett veckoschema som endast körs på vardagar.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Skapa ett återkommande veckoschema för helger

Följande exempelkommandon visar hur du skapar ett veckoschema som endast körs på helger.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Skapa ett återkommande schema för första, 15:e och sista dagarna i månaden

Följande exempelkommandon visar hur du skapar ett återkommande schema som körs den 1:a, 15:e och sista dagen i en månad.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Länka ett schema till en runbook

En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks. Om en runbook har parametrar kan du ange värden för dem. Du måste ange värden för alla obligatoriska parametrar och kan ange värden för valfria parametrar. Dessa värden används varje gång runbooken startas av det här schemat. Du kan koppla samma runbook till ett annat schema och ange olika parametervärden.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Så här länkar du ett schema till en runbook med Azure-portalen

1. I Azure-portalen, från ditt automatiseringskonto, väljer du **Runbooks** under avsnittet **Process Automation** till vänster.
2. Klicka på namnet på den runbook som ska schemaläggas.
3. Om runbooken för närvarande inte är länkad till ett schema erbjuds du alternativet att skapa ett nytt schema eller länka till ett befintligt schema.
4. Om runbooken har parametrar kan du välja alternativet **Ändra körinställningar (Standard:Azure)** och **fönstret Parametrar** visas där du kan ange informationen.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Så här länkar du ett schema till en runbook med PowerShell

Du kan använda [cmdleten Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern Parametrar . Mer information om hur du anger parametervärden finns [i Starta en runbook i Azure Automation](../automation-starting-a-runbook.md).
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

Det vanligaste intervallet ett schema i Azure Automation kan konfigureras för är en timme. Om du behöver scheman för att köra oftare än så finns det två alternativ:

* Skapa en [webhook](../automation-webhooks.md) för runbook och använda [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) för att anropa webhook. Azure Logic Apps ger mer detaljerad granularitet när du definierar ett schema.

* Skapa fyra scheman som alla börjar inom 15 minuter från varandra som körs en gång i timmen. Det här scenariot gör att runbooken kan köras var 15:e minut med de olika schemana.

## <a name="disabling-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs inte längre någon runbook som är länkad till det schemat. Du kan inaktivera ett schema manuellt eller ange en förfallotid för scheman med en frekvens när du skapar dem. När utgångsdatumet har uppnåtts inaktiveras schemat.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Så här inaktiverar du ett schema från Azure-portalen

1. I Azure-portalen, från ditt Automation-konto, väljer du **Scheman** under avsnittet **Delade resurser** till vänster.
2. Klicka på namnet på ett schema för att öppna informationsfönstret.
3. Ändra **Aktiverad** till **Nr**.

> [!NOTE]
> Om du vill inaktivera ett schema som har en starttid tidigare måste du ändra startdatumet till en tid i framtiden innan du sparar det.

### <a name="to-disable-a-schedule-with-powershell"></a>Så här inaktiverar du ett schema med PowerShell

Du kan använda cmdleten [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat anger du **false** för parametern **IsEnabled.**

Följande exempelkommandon visar hur du inaktiverar ett schema för en runbook med en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med runbooks i Azure Automation finns [i Starta en runbook i Azure Automation](../automation-starting-a-runbook.md)

