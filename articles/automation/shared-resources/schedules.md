---
title: Scheman i Azure Automation
description: Automationsscheman används för att schemalägga runbooks i Azure Automation för att starta automatiskt. Beskriver hur du skapar och hanterar ett schema i så att du kan automatiskt starta en runbook vid en given tidpunkt eller enligt ett återkommande schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/04/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 483f9092d29fc40937ed9d54510269af2af30872
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62128812"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Schemaläggning av en Runbook i Azure Automation

Om du vill schemalägga en runbook i Azure Automation ska starta vid en viss tid länkar du det till ett eller flera scheman. Ett schema kan konfigureras att köras en gång eller enligt ett tillståndet per timme eller dagligen schema för runbooks i Azure-portalen. Du kan också schemalägga dem för varje vecka, månadsvis, särskilda dagar i veckan eller dagar i månaden eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks.

> [!NOTE]
> Scheman stöder för närvarande inte Azure Automation DSC-konfigurationer.

## <a name="powershell-cmdlets"></a>PowerShell-cmdletar

Cmdlets i följande tabell används för att skapa och hantera scheman med PowerShell i Azure Automation. De levereras som en del av den [Azure PowerShell-modulen](/powershell/azure/overview).

| Cmdlet: ar | Beskrivning |
|:--- |:--- |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Hämtar ett schema. |
| [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Skapar ett nytt schema. |
| [Remove-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Tar bort ett schema. |
| [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Anger egenskaperna för ett befintligt schema. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Hämtar schemalagda runbook-flöden. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associerar en runbook med ett schema. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates en runbook från ett schema. |

## <a name="creating-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för runbooks i Azure portal eller med PowerShell.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  För att undvika att påverka dina runbooks och de processer som de automatisera, bör du testa alla runbooks som har länkade scheman med ett Automation-konto som är dedikerad för testning.  Detta bekräftar din schemalagda runbook-flöden fungerar korrekt och om inte, du kan ytterligare felsöka och tillämpa de ändringar som krävs innan du migrerar den uppdaterade runbook-versionen till produktionen.
> Automation-kontot automatiskt får inte nya versioner av moduler, såvida inte du har uppdaterat dem manuellt genom att välja den [uppdatera Azure-moduler](../automation-update-azure-modules.md) alternativet från den **moduler**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure portal

1. I Azure-portalen från ditt automation-konto väljer **scheman** under avsnittet **delade resurser** till vänster.
2. Klicka på **lägga till ett schema** överst på sidan.
3. På den **nytt schema** rutan Ange ett **namn** och eventuellt en **beskrivning** för det nya schemat.
4. Välj om schemat har körts en gång, eller enligt ett återkommande schema genom att välja **när** eller **återkommande**. Om du väljer **när** anger en **starttid**, och klicka sedan på **skapa**. Om du väljer **återkommande**, ange en **starttid** och för **Upprepa varje**, väljer du frekvens för hur ofta du vill att runbook ska upprepas - av **timme**, **dag**, **vecka**, eller av **månad**.
    1. Om du väljer **vecka**, får du en lista över de veckodagar att välja bland. Välj så många dagar som du vill ha. Den första körningen av schemat sker på den första dagen efter starttiden. Till exempel om du vill välja en helg schema, välja **lördag** och **söndag**.

       ![Inställningen helgen återkommande schema](../media/schedules/week-end-weekly-recurrence.png)

    2. Om du väljer **månad**, får du olika alternativ. För den **månadsförekomster** alternativet, väljer du antingen **dagar i månaden** eller **veckodagar**. Om du väljer **dagar i månaden**, en kalender visas där du kan välja så många dagar som du vill. Om du väljer ett datum, till exempel den 31 inte inträffar i den aktuella månaden, körs inte schemat. Om du vill att schemat ska köras den sista dagen väljer **Ja** under **kör sista dagen i månaden**. Om du väljer **veckodagar**, **Upprepa varje** alternativ visas. Välj **första**, **andra**, **tredje**, **fjärde**, eller **senaste**. Välj slutligen en dag till Upprepa på.

       ![Månadsschema första femtonde och sista dagen i månaden](../media/schedules/monthly-first-fifteenth-last.png)

5. När klar klickar du på **skapa**.

### <a name="to-create-a-new-schedule-with-powershell"></a>Att skapa ett nytt schema med PowerShell

Du använder den [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet för att skapa scheman. Du kan ange starttid för schemat och frekvensen som det ska köras. I följande exempel visas hur du skapar många olika schema-scenarier.

#### <a name="create-a-one-time-schedule"></a>Skapa ett schema

Följande exempelkommandon skapar ett schema.

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

Följande exempelkommandon visar hur du skapar ett veckoschema som körs på vardag.

```azurepowershell-interactive
$StartTime = (Get-Date "13:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekDays = @([System.DayOfWeek]::Monday..[System.DayOfWeek]::Friday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Schedule03" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-weekly-recurring-schedule-for-weekends"></a>Skapa ett återkommande veckoschema för helger

Följande exempelkommandon visar hur du skapar ett veckoschema som körs på helger endast.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
[System.DayOfWeek[]]$WeekendDays = @([System.DayOfWeek]::Saturday,[System.DayOfWeek]::Sunday)
New-AzureRmAutomationSchedule -AutomationAccountName "ContosoAutomation" -Name "Weekends 6PM" -StartTime $StartTime -WeekInterval 1 -DaysOfWeek $WeekendDays -ResourceGroupName "ResourceGroup01"
```

#### <a name="create-a-recurring-schedule-for-first-15th-and-last-days-of-the-month"></a>Skapa ett återkommande schema för första, 15, och den sista dagen i månaden

Följande exempelkommandon visar hur du skapar ett återkommande schema som körs på 1, 15 och sista dagen i månaden.

```azurepowershell-interactive
$StartTime = (Get-Date "18:00:00").AddDays(1)
New-AzureRmAutomationSchedule -AutomationAccountName "TestAzureAuto" -Name "1st, 15th and Last" -StartTime $StartTime -DaysOfMonth @("One", "Fifteenth", "Last") -ResourceGroupName "TestAzureAuto" -MonthInterval 1
```

## <a name="linking-a-schedule-to-a-runbook"></a>Länka ett schema till en runbook

En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks. Om en runbook har parametrar, kan du ange värden för dessa. Du måste ange värden för alla obligatoriska parametrar och kan ange värden för parametrar. De här värdena används varje gång runbook startas med det här schemat. Du kan koppla samma runbook till ett annat schema och ange olika parametervärden.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en runbook med Azure-portalen

1. I Azure-portalen från ditt automation-konto väljer **Runbooks** under avsnittet **Processautomatisering** till vänster.
2. Klicka på namnet på runbooken som ska schemaläggas.
3. Om runbook inte är kopplad till ett schema, är sedan erbjuds du alternativet för att skapa ett nytt schema eller länka till ett befintligt schema.
4. Om runbooken har parametrar, kan du välja alternativet **ändra körningsinställningar (standard: Azure)** och **parametrar** fönstret visas där du kan ange informationen.

### <a name="to-link-a-schedule-to-a-runbook-with-powershell"></a>Länka ett schema till en runbook med PowerShell

Du kan använda den [registrera AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern parametrar. Mer information om specificering av parametervärden finns i [starta en Runbook i Azure Automation](../automation-starting-a-runbook.md).
Följande exempelkommandon visar hur du länka ett schema till en runbook med en Azure Resource Manager-cmdlet med parametrar.

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

Oftast förekommande intervallet ett schema i Azure Automation kan konfigureras för är en timme. Om du behöver scheman för att köra oftare än som finns det två alternativ:

* Skapa en [webhook](../automation-webhooks.md) för runbook och Använd [Azure Scheduler](../../scheduler/scheduler-get-started-portal.md) du anropar webhooken. Azure Scheduler ger mer detaljerad kornighet när du definierar ett schema.

* Skapa fyra scheman alla startar inom 15 minuter från varandra som körs en gång i timmen. Det här scenariot gör att runbook körs varje kvart med olika scheman.

## <a name="disabling-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs valfri runbook som inte längre länkad till den på schemat. Du kan manuellt inaktivera ett schema eller ställer in en förfallotid för scheman med en frekvens när de skapas. När den upphör att gälla har nåtts har schemat inaktiverats.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure portal

1. I Azure-portalen från ditt Automation-konto väljer **scheman** under avsnittet **delade resurser** till vänster.
2. Klicka på namnet på ett schema för att öppna informationsfönstret.
3. Ändra **aktiverat** till **nr**.

> [!NOTE]
> Om du vill inaktivera ett schema som har en starttid tidigare, måste du ändra startdatum till en tidpunkt i framtiden innan du sparar den.

### <a name="to-disable-a-schedule-with-powershell"></a>Inaktivera ett schema med PowerShell

Du kan använda den [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) cmdlet för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat, ange **FALSKT** för den **IsEnabled** parametern.

Följande exempelkommandon visar hur du inaktiverar ett schema för en runbook med en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Kom igång med runbooks i Azure Automation, se [starta en Runbook i Azure Automation](../automation-starting-a-runbook.md)

