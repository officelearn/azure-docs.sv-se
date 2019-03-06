---
title: Scheman i Azure Automation
description: Automationsscheman används för att schemalägga runbooks i Azure Automation för att starta automatiskt. Beskriver hur du skapar och hanterar ett schema i så att du kan automatiskt starta en runbook vid en given tidpunkt eller enligt ett återkommande schema.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 09/18/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: d6677733339babf45e7351e10d6de1c002a6ea93
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57436518"
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Schemaläggning av en Runbook i Azure Automation

Om du vill schemalägga en runbook i Azure Automation ska starta vid en viss tid länkar du det till ett eller flera scheman. Ett schema kan konfigureras att köras en gång eller enligt ett tillståndet per timme eller dagligen schema för runbooks i Azure-portalen. Du kan också schemalägga dem för varje vecka, månadsvis, särskilda dagar i veckan eller dagar i månaden eller en viss dag i månaden. En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks.

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
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/get-azurermautomationscheduledrunbook) |Hämtar schemalagda runbook-flöden. |
| [Register-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associerar en runbook med ett schema. |
| [Unregister-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates en runbook från ett schema. |

## <a name="creating-a-schedule"></a>Skapa ett schema

Du kan skapa ett nytt schema för runbooks i Azure portal eller med Windows PowerShell.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  För att undvika att påverka dina runbooks och de processer som de automatisera, bör du testa alla runbooks som har länkade scheman med ett Automation-konto som är dedikerad för testning.  Detta bekräftar din schemalagda runbook-flöden fungerar korrekt och om inte, du kan ytterligare felsöka och tillämpa de ändringar som krävs innan du migrerar den uppdaterade runbook-versionen till produktionen.
> Automation-kontot automatiskt får inte nya versioner av moduler, såvida inte du har uppdaterat dem manuellt genom att välja den [uppdatera Azure-moduler](automation-update-azure-modules.md) alternativet från den **moduler**.

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure portal

1. I Azure-portalen från ditt automation-konto väljer **scheman** under avsnittet **delade resurser** till vänster.
1. Klicka på **lägga till ett schema** överst på sidan.
1. På den **nytt schema** rutan Ange ett **namn** och eventuellt en **beskrivning** för det nya schemat.
1. Välj om schemat har körts en gång, eller enligt ett återkommande schema genom att välja **när** eller **återkommande**. Om du väljer **när** anger en **starttid**, och klicka sedan på **skapa**. Om du väljer **återkommande**, ange en **starttid** och för **Upprepa varje**, väljer du frekvens för hur ofta du vill att runbook ska upprepas - av **timme**, **dag**, **vecka**, eller av **månad**.
    1. Om du väljer **vecka**, du får en lista över de veckodagar att välja bland. Välj så många dagar som du vill ha. Den första körningen av schemat sker på den första dagen efter starttiden.
    2. Om du väljer **månad**, ges du olika alternativ. För den **månadsförekomster** alternativet, väljer du antingen **dagar i månaden** eller **veckodagar**. Om du väljer **dagar i månaden** en kalender visas där du kan välja så många dagar som du vill. Om du väljer ett datum, till exempel den 31 inte inträffar i den aktuella månaden, körs inte schemat. Om du vill att schemat ska köras den sista dagen väljer **Ja** under **kör sista dagen i månaden**. Om du väljer **veckodagar**, **Upprepa varje** alternativ visas. Välj **första**, **andra**, **tredje**, **fjärde**, eller **senaste**. Välj slutligen en dag till Upprepa på.
1. När klar klickar du på **skapa**.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Att skapa ett nytt schema med Windows PowerShell

Du använder den [New-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet för att skapa scheman. Du kan ange starttid för schemat och frekvensen som det ska köras.

Följande exempelkommandon visar hur du skapar ett schema för den 15: e och 30 i varje månad med hjälp av en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
$scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
-DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"
```

## <a name="linking-a-schedule-to-a-runbook"></a>Länka ett schema till en runbook

En runbook kan länkas till flera scheman och ett schema kan vara kopplat till flera runbooks. Om en runbook har parametrar, kan du ange värden för dessa. Du måste ange värden för alla obligatoriska parametrar och kan ange värden för parametrar. De här värdena används varje gång runbook startas med det här schemat. Du kan koppla samma runbook till ett annat schema och ange olika parametervärden.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en runbook med Azure-portalen

1. I Azure-portalen från ditt automation-konto väljer **Runbooks** under avsnittet **Processautomatisering** till vänster.
2. Klicka på namnet på runbooken som ska schemaläggas.
3. Om runbook inte för närvarande är länkad till ett schema, erbjuds du alternativet för att skapa ett nytt schema eller länka till ett befintligt schema.
4. Om runbooken har parametrar, kan du välja alternativet **ändra körningsinställningar (standard: Azure)** och **parametrar** fönstret visas där du kan ange informationen i enlighet med detta.

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Länka ett schema till en runbook med Windows PowerShell

Du kan använda den [registrera AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet för att länka ett schema. Du kan ange värden för runbookens parametrar med parametern parametrar. Mer information om specificering av parametervärden finns i [starta en Runbook i Azure Automation](automation-starting-a-runbook.md).
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

* Skapa en [webhook](automation-webhooks.md) för runbook och Använd [Azure Scheduler](../scheduler/scheduler-get-started-portal.md) du anropar webhooken. Azure Scheduler ger mer detaljerad kornighet när du definierar ett schema.

* Skapa fyra scheman alla startar inom 15 minuter från varandra som körs en gång i timmen. Det här scenariot gör att runbook körs varje kvart med olika scheman.

## <a name="disabling-a-schedule"></a>Inaktivera ett schema

När du inaktiverar ett schema körs valfri runbook som inte längre länkad till den på schemat. Du kan manuellt inaktivera ett schema eller ställer in en förfallotid för scheman med en frekvens när de skapas. När den upphör att gälla uppnås, har schemat inaktiverats.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure portal

1. I Azure-portalen från ditt Automation-konto väljer **scheman** under avsnittet **delade resurser** till vänster.
1. Klicka på namnet på ett schema för att öppna informationsfönstret.
1. Ändra **aktiverat** till **nr**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Inaktivera ett schema med Windows PowerShell

Du kan använda den [Set-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) cmdlet för att ändra egenskaperna för ett befintligt schema. Om du vill inaktivera schemat, ange **FALSKT** för den **IsEnabled** parametern.

Följande exempelkommandon visar hur du inaktiverar ett schema för en runbook med en Azure Resource Manager-cmdlet.

```azurepowershell-interactive
$automationAccountName = "MyAutomationAccount"
$scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
–Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"
```

## <a name="next-steps"></a>Nästa steg

* Kom igång med runbooks i Azure Automation, se [starta en Runbook i Azure Automation](automation-starting-a-runbook.md)

