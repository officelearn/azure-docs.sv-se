---
title: Hantera scheman i Azure Automation
description: Automatiseringsscheman används för att schemalägga runbooks i Azure Automation för att starta automatiskt. Beskriver hur du skapar och hanterar ett schema så att du automatiskt kan starta en runbook vid en viss tidpunkt eller på ett återkommande schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3603e76186ce30fb491d829d3a804837f4ac2e6d
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732799"
---
# <a name="manage-schedules-in-azure-automation"></a>Hantera scheman i Azure Automation

Om du vill schemalägga en runbook i Azure Automation så att den startar vid en viss tidpunkt länkar du den till ett eller flera scheman. Ett schema kan konfigureras för att antingen köras en gång eller på ett återkommande tim- eller dagligt schema för runbooks i Azure-portalen. Du kan också schemalägga dem för veckovisa, månadsvisa, specifika dagar i veckan eller dagarna i månaden, eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks.

> [!NOTE]
> Scheman stöder för närvarande inte Azure Automation DSC-konfigurationer.

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](../automation-update-azure-modules.md).

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar

Cmdlets i följande tabell används för att skapa och hantera scheman med PowerShell i Azure Automation. De levereras som en del av [Azure PowerShell-modulen](/powershell/azure/overview).

| Cmdletar | Beskrivning |
|:--- |:--- |
| [Få-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Get-AzAutomationSchedule?view=azps-3.7.0) |Hämtar ett schema. |
| [Hämta-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationscheduledrunbook?view=azps-3.7.0) |Hämtar schemalagda runbooks. |
| [Ny-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) |Skapar ett nytt schema. |
| [Registrera-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) |Associerar en runbook med ett schema. |
| [Ta bort-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationSchedule?view=azps-3.7.0) |Tar bort ett schema. |
| [Set-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) |Anger egenskaperna för ett befintligt schema. |
| [Avregistrera-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Unregister-AzAutomationScheduledRunbook?view=azps-3.7.0) |Tar bort en runbook från ett schema. |

## <a name="creating-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för runbooks i Azure-portalen eller med PowerShell.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  Om du vill undvika att påverka dina runbooks och de processer som de automatiserar bör du först testa alla runbooks som har länkade scheman med ett Automation-konto som är dedikerat för testning.  Detta verifierar att dina schemalagda runbooks fortsätter att fungera korrekt och om inte, kan du ytterligare felsöka och tillämpa alla ändringar som krävs innan du migrerar den uppdaterade runbook-versionen till produktion.
> Ditt Automation-konto får inte automatiskt några nya versioner av moduler om du inte har uppdaterat dem manuellt genom att välja alternativet [Uppdatera Azure-moduler](../automation-update-azure-modules.md) från **modulerna**.

### <a name="create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure-portalen

1. I Azure-portalen, från ditt **automatiseringskonto,** väljer du Scheman under avsnittet **Delade resurser** till vänster.
2. Klicka på **Lägg till ett schema** högst upp på sidan.
3. Skriv ett namn i fönstret Nytt schema och eventuellt en beskrivning av det nya schemat.
4. Välj om schemat körs en gång eller på ett återkommande schema genom att välja **En gång** eller **Återkommande**. Om du väljer **En gång**anger du en starttid och klickar sedan på **Skapa**. Om du väljer **Återkommande**anger du en starttid. För **Upprepa varje**väljer du hur ofta du vill att runbooken ska upprepas - per timme, dag, vecka eller månad.
    1. Om du väljer **vecka**visas veckodagarna för dig att välja mellan. Välj så många dagar du vill. Den första körningen av ditt schema kommer att ske den första dagen som valts efter starttiden. Om du till exempel vill välja ett helgschema väljer du lördag och söndag. 
    
       ![Ställa in återkommande schema för helg](../media/schedules/week-end-weekly-recurrence.png)

    2. Om du väljer **månad**får du olika alternativ. För alternativet **Månatliga förekomster** väljer du antingen **Månadsdagar** eller **Veckodagar**. Om du väljer **Månadsdagar**visas en kalender som gör att du kan välja så många dagar du vill. Om du väljer ett datum som den 31:a som inte inträffar under den aktuella månaden körs inte schemat. Om du vill att schemat ska köras den sista dagen väljer du **Ja** under **Kör den sista dagen i månaden**. Om du väljer **Veckodagar**visas **alternativet Upprepa varje** alternativ. Välj **Första**, **Andra**, **Tredje**, **Fjärde**eller **Sista**. Slutligen välja en dag att upprepa på.

       ![Månadsschema på första, femtonde och sista dagen i månaden](../media/schedules/monthly-first-fifteenth-last.png)

5. När du är klar klickar du på **Skapa**.

### <a name="create-a-new-schedule-with-powershell"></a>Skapa ett nytt schema med PowerShell

Använd cmdleten [New-AzAutomationSchedule](https://docs.microsoft.com/powershell/module/Az.Automation/New-AzAutomationSchedule?view=azps-3.7.0) för att skapa scheman. Du anger starttiden för schemat och hur ofta det ska köras. Följande exempel visar hur du skapar många olika schemascenarier.

#### <a name="create-a-one-time-schedule"></a>Skapa ett engångsschema

Följande exempelkommandon skapar ett tidsschema.

```azurepowershell-interactive
$TimeZone = ([System.TimeZoneInfo]::Local).Id
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule01" -StartTime "23:00" -OneTime -ResourceGroupName "ResourceGroup01" -TimeZone $TimeZone
```

#### <a name="create-a-recurring-schedule"></a>Skapa ett återkommande schema

I följande exempel visas hur du skapar ett återkommande schema som körs varje dag klockan 13:00 för ett år.

```azurepowershell-interactive
$StartTime = Get-Date "13:00:00"
$EndTime = $StartTime.AddYears(1)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule02" -StartTime $StartTime -ExpiryTime $EndTime -DayInterval 1 -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule"></a>Skapa ett återkommande veckoschema

I följande exempel visas hur du skapar ett veckoschema som endast körs på vardagar.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Skapa ett återkommande veckoschema för helger

Följande exempelkommandon visar hur du skapar ett veckoschema som endast körs på helger.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Skapa ett återkommande schema för första, 15:e och sista dagarna i månaden

I följande exempel visas hur du skapar ett återkommande schema som körs den 1:a, 15:e och sista dagen i en månad.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Länka ett schema till en runbook

En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks. Om en runbook har parametrar kan du ange värden för dem. Du måste ange värden för alla obligatoriska parametrar och kan ange värden för valfria parametrar. Dessa värden används varje gång runbooken startas av det här schemat. Du kan koppla samma runbook till ett annat schema och ange olika parametervärden.

### <a name="link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en runbook med Azure-portalen

1. Välj **Runbooks** i Azure-portalen från ditt automatiseringskonto under **Process Automation**.
2. Klicka på namnet på den runbook som ska schemaläggas.
3. Om runbooken för närvarande inte är länkad till ett schema erbjuds du alternativet att skapa ett nytt schema eller länka till ett befintligt schema.
4. Om runbooken har parametrar kan du välja alternativet **Ändra körinställningar (Standard:Azure)** och fönstret Parametrar visas. Du kan ange parameterinformation här.

### <a name="link-a-schedule-to-a-runbook-with-powershell"></a>Länka ett schema till en runbook med PowerShell

Använd [cmdleten Register-AzAutomationScheduledRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Register-AzAutomationScheduledRunbook?view=azps-3.7.0) för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern Parametrar . Mer information om hur du anger parametervärden finns [i Starta en runbook i Azure Automation](../automation-starting-a-runbook.md).
I följande exempel visas hur du länkar ett schema till en runbook med en Azure Resource Manager-cmdlet med parametrar.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Test-Runbook"
$scheduleName = "Sample-DailySchedule"
$params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
Register-AzAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
–Name $runbookName –ScheduleName $scheduleName –Parameters $params `
-ResourceGroupName "ResourceGroup01"
```

## <a name="scheduling-runbooks-to-run-more-frequently"></a>Schemalägga runbooks som ska köras oftare

Det vanligaste intervallet ett schema i Azure Automation kan konfigureras för är en timme. Om du behöver scheman för att köra oftare än så finns det två alternativ:

* Skapa en [webhook](../automation-webhooks.md) för runbook och använda [Azure Logic Apps](../../logic-apps/logic-apps-overview.md) för att anropa webhook. Azure Logic Apps ger mer detaljerad granularitet när du definierar ett schema.

* Skapa fyra scheman som alla börjar inom 15 minuter från varandra som körs en gång i timmen. Det här scenariot gör att runbooken kan köras var 15:e minut med de olika schemana.

## <a name="disabling-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs inte längre någon runbook som är länkad till det schemat. Du kan inaktivera ett schema manuellt eller ange en förfallotid för scheman med en frekvens när du skapar dem. När utgångsdatumet har uppnåtts inaktiveras schemat.

### <a name="disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure-portalen

1. I ditt Automation-konto väljer du **Scheman** under **Delade resurser**.
2. Klicka på namnet på ett schema för att öppna informationsfönstret.
3. Ändra **Aktiverad** till **Nr**.

> [!NOTE]
> Om du vill inaktivera ett schema som har en starttid tidigare måste du ändra startdatumet till en tid i framtiden innan du sparar det.

### <a name="disable-a-schedule-with-powershell"></a>Inaktivera ett schema med PowerShell

Använd [cmdlet set-azautomationschedule](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationSchedule?view=azps-3.7.0) för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat `IsEnabled` anger du Falskt för parametern.

I följande exempel visas hur du inaktiverar ett schema för en runbook med hjälp av en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med runbooks i Azure Automation finns [i Starta en runbook i Azure Automation](../automation-starting-a-runbook.md).