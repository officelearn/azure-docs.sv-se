---
title: Scheman i Azure Automation | Microsoft Docs
description: "Automationsscheman används för att schemalägga runbooks i Azure Automation för att starta automatiskt. Beskriver hur du skapar och hanterar ett schema i så att automatiskt starta en runbook på en viss tidpunkt eller ett återkommande schema."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
ms.assetid: 1c2da639-ad20-4848-920b-88e471b2e1d9
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/29/2017
ms.author: magoedte
ms.openlocfilehash: c651ab70977367d0e41364120c89561a04a45cf4
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2017
---
# <a name="scheduling-a-runbook-in-azure-automation"></a>Schemaläggning av en Runbook i Azure Automation
Om du vill schemalägga en runbook i Azure Automation för att starta vid en viss tidpunkt länka du det till ett eller flera scheman. Ett schema kan konfigureras för att köras en gång eller på en igen timvis eller daglig schema för runbooks i den klassiska Azure-portalen och runbooks i Azure-portalen, du kan också schemalägga dem för varje vecka, månad, särskilda dagar i veckan eller dagar i månaden, eller en viss dag i månaden.  En runbook kan länkas till flera scheman och ett schema kan ha flera runbooks som är länkade till den.

> [!NOTE]
> Scheman stöder för närvarande inte Azure Automation DSC-konfigurationer.
> 
> 

## <a name="windows-powershell-cmdlets"></a>Windows PowerShell-Cmdlets
Cmdlets i följande tabell används för att skapa och hantera scheman med Windows PowerShell i Azure Automation. De levereras som en del av den [Azure PowerShell-modulen](/powershell/azure/overview).

| Cmdlet: ar | Beskrivning |
|:--- |:--- |
| **Azure Resource Manager-cmdlets** | |
| [Get-AzureRmAutomationSchedule](/powershell/module/azurerm.automation/get-azurermautomationschedule) |Hämtar ett schema. |
| [Ny AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) |Skapar ett nytt schema. |
| [Ta bort AzureRmAutomationSchedule](/powershell/module/azurerm.automation/remove-azurermautomationschedule) |Tar bort ett schema. |
| [Ange AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) |Anger egenskaperna för ett befintligt schema. |
| [Get-AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/set-azurermautomationscheduledrunbook) |Hämtar schemalagda runbooks. |
| [Registrera AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) |Associerar en runbook med ett schema. |
| [Avregistrera AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/unregister-azurermautomationscheduledrunbook) |Dissociates en runbook från ett schema. |
| **Azure Service Management-cmdlets** | |
| [Get-AzureAutomationSchedule](/powershell/module/azure/get-azureautomationschedule?view=azuresmps-3.7.0) |Hämtar ett schema. |
| [Ny AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) |Skapar ett nytt schema. |
| [Ta bort AzureAutomationSchedule](/powershell/module/azure/remove-azureautomationschedule?view=azuresmps-3.7.0) |Tar bort ett schema. |
| [Ange AzureAutomationSchedule](/powershell/module/azure/set-azureautomationschedule?view=azuresmps-3.7.0) |Anger egenskaperna för ett befintligt schema. |
| [Get-AzureAutomationScheduledRunbook](/powershell/module/azure/get-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Hämtar schemalagda runbooks. |
| [Registrera AzureAutomationScheduledRunbook](/powershell/module/azure/register-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Associerar en runbook med ett schema. |
| [Avregistrera AzureAutomationScheduledRunbook](/powershell/module/azure/unregister-azureautomationscheduledrunbook?view=azuresmps-3.7.0) |Dissociates en runbook från ett schema. |

## <a name="creating-a-schedule"></a>Skapa ett schema
Du kan skapa ett nytt schema för runbooks i Azure-portalen i den klassiska portalen eller med Windows PowerShell. Du har också möjlighet att skapa ett nytt schema när du länkar en runbook till ett schema med Azure klassiska eller Azure-portalen.

> [!NOTE]
> Azure Automation använder de senaste modulerna i ditt Automation-konto när ett nytt schemalagt jobb körs.  För att undvika att påverka dina runbooks och processer som de automatisera, bör du testa alla runbooks som har länkats scheman med ett Automation-konto som är dedikerad för testning.  Detta verifierar din schemalagda runbooks fortsätter att fungera korrekt om inte, du kan och ytterligare felsöka tillämpar ändringar som krävs innan du migrerar den uppdaterade runbook-versionen till produktionen.  
>  Automation-kontot inte automatiskt får nya versioner av moduler om du har uppdaterat dem manuellt genom att välja den [Update Azure moduler](automation-update-azure-modules.md) alternativet från den **moduler** . 
>  

### <a name="to-create-a-new-schedule-in-the-azure-portal"></a>Skapa ett nytt schema i Azure-portalen
1. Välj i Azure-portalen från ditt automation-konto **scheman** under avsnittet **delade resurser** till vänster. 
2. Klicka på **lägga till ett schema** överst på sidan.
4. På den **nytt schema** rutan Ange ett **namn** och eventuellt en **beskrivning** för det nya schemat.
5. Välj om schemat har körts en gång, eller på ett reoccurring schema genom att välja **när** eller **återkommande**.  Om du väljer **när** ange en **starttid** och klicka sedan på **skapa**.  Om du väljer **återkommande**, ange en **starttid** och frekvens för hur ofta du vill att runbook ska upprepas - av **timme**, **dag**, **vecka**, eller av **månad**.  Om du väljer **vecka** eller **månad** från den nedrullningsbara listan den **upprepning alternativet** visas i fönstret och vid val av den **upprepning alternativet** fönstret visas och du kan välja dag i veckan om du har valt **vecka**.  Om du har valt **månad**, du kan välja efter **veckodagar** eller särskilda dagar i månaden i kalendern och slutligen vill du köra den på den sista dagen i månaden eller inte och klicka sedan på **OK**.   

### <a name="to-create-a-new-schedule-in-the-azure-classic-portal"></a>Skapa ett nytt schema i den klassiska Azure-portalen
1. Välj Automation och välj sedan namnet på ett Automation-konto i den klassiska Azure-portalen.
2. Välj den **tillgångar** fliken.
3. Längst ned i fönstret klickar du på **Lägg till inställning**.
4. Klicka på **Lägg till schema**.
5. Ange en **namn** och eventuellt en **beskrivning** för det nya schemat. Schemat kan köra **en gång**, **timvis**, **dagliga**, **veckovisa**, eller **månatliga**.
6. Ange en **starttid** och andra alternativ beroende på vilken typ av schema som du har valt.

### <a name="to-create-a-new-schedule-with-windows-powershell"></a>Skapa ett nytt schema med Windows PowerShell
Du kan använda den [ny AzureAutomationSchedule](/powershell/module/azure/new-azureautomationschedule?view=azuresmps-3.7.0) för att skapa ett nytt schema i Azure Automation för klassiska runbooks eller [ny AzureRmAutomationSchedule](/powershell/module/azurerm.automation/new-azurermautomationschedule) cmdlet för runbooks i Azure-portalen. Du måste ange starttiden för schemat och frekvensen ska köras.

Följande exempelkommandon visar hur du skapar ett schema för den 15: e och 30: e i månaden med en Azure Resource Manager-cmdlet.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    New-AzureRMAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName -StartTime "7/01/2016 15:30:00" -MonthInterval 1 `
    -DaysOfMonth Fifteenth,Thirtieth -ResourceGroupName "ResourceGroup01"

Följande exempelkommandon visar hur du skapar ett nytt schema som körs varje dag klockan 3:30 startar på 20 januari 2015 med Azure Service Management-cmdlet.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    New-AzureAutomationSchedule –AutomationAccountName $automationAccountName –Name `
    $scheduleName –StartTime "1/20/2016 15:30:00" –DayInterval 1

## <a name="linking-a-schedule-to-a-runbook"></a>Länka ett schema till en runbook
En runbook kan länkas till flera scheman och ett schema kan ha flera runbooks som är länkade till den. Om en runbook har parametrar, kan du ange värden för dessa. Du måste ange värden för alla obligatoriska parametrar och kan ange värden för valfria parametrar.  Dessa värden används varje gång runbook startas med det här schemat.  Du kan koppla samma runbook till ett annat schema och ange olika parametervärden.

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-portal"></a>Länka ett schema till en runbook med Azure-portalen
1. Välj i Azure-portalen från ditt automation-konto **Runbooks** under avsnittet **Processautomatisering** till vänster.
2. Klicka på namnet på runbooken som ska schemaläggas.
3. Om runbook inte är för närvarande kopplad till ett schema, erbjuds du alternativet för att skapa ett nytt schema eller länka till ett befintligt schema.  
4. Om runbooken har parametrar, kan du välja alternativet **ändra körningsinställningar (standard: Azure)** och **parametrar** fönstret visas där du kan ange information i enlighet med detta.  

### <a name="to-link-a-schedule-to-a-runbook-with-the-azure-classic-portal"></a>Länka ett schema till en runbook med den klassiska Azure-portalen
1. I den klassiska Azure-portalen väljer **Automation** och klicka sedan på namnet på ett Automation-konto.
2. Välj den **Runbooks** fliken.
3. Klicka på namnet på runbooken som ska schemaläggas.
4. Klicka på den **schema** fliken.
5. Om runbook inte är för närvarande kopplad till ett schema så att du har möjlighet att **länk till ett nytt schema** eller **länk till ett befintligt schema**.  Om runbooken för närvarande är länkad till ett schema, klickar du på **länk** längst ned i fönstret för att komma åt dessa alternativ.
6. Om runbooken har parametrar uppmanas för deras värden.  

### <a name="to-link-a-schedule-to-a-runbook-with-windows-powershell"></a>Länka ett schema till en runbook med Windows PowerShell
Du kan använda den [registrera AzureAutomationScheduledRunbook](http://msdn.microsoft.com/library/azure/dn690265.aspx) länka ett schema till en klassisk runbook eller [registrera AzureRmAutomationScheduledRunbook](/powershell/module/azurerm.automation/register-azurermautomationscheduledrunbook) cmdlet för runbooks i Azure-portalen.  Du kan ange värden för runbookens parametrar med parametern parametrar. Se [starta en Runbook i Azure Automation](automation-starting-a-runbook.md) mer information om specificering av parametervärden.

Följande exempelkommandon visar hur du länkar ett schema till en runbook med en Azure Resource Manager-cmdlet med parametrar.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureRmAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params `
    -ResourceGroupName "ResourceGroup01"
Följande exempelkommandon visar hur du länkar ett schema med en Azure Service Management-cmdlet med parametrar.

    $automationAccountName = "MyAutomationAccount"
    $runbookName = "Test-Runbook"
    $scheduleName = "Sample-DailySchedule"
    $params = @{"FirstName"="Joe";"LastName"="Smith";"RepeatCount"=2;"Show"=$true}
    Register-AzureAutomationScheduledRunbook –AutomationAccountName $automationAccountName `
    –Name $runbookName –ScheduleName $scheduleName –Parameters $params

## <a name="disabling-a-schedule"></a>Inaktivera ett schema
När du inaktiverar ett schema körs alla runbooks inte längre länkad till den på schemat. Du kan manuellt inaktivera ett schema eller ange en förfallotid för scheman med en frekvens när de skapas. När förfallotid har uppnåtts har schemat inaktiverats.

### <a name="to-disable-a-schedule-from-the-azure-portal"></a>Inaktivera ett schema från Azure-portalen
1. Välj i Azure-portalen från ditt Automation-konto **scheman** under avsnittet **delade resurser** till vänster.
2. Klicka på namnet på ett schema för att öppna informationsfönstret.
3. Ändra **aktiverat** till **nr**.

### <a name="to-disable-a-schedule-from-the-azure-classic-portal"></a>Inaktivera ett schema från den klassiska Azure-portalen
Du kan inaktivera ett schema i den klassiska Azure-portalen från sidan Schemadetaljer för schemat.

1. Välj Automation och klicka sedan på namnet på ett Automation-konto i den klassiska Azure-portalen.
2. Välj fliken tillgångar.
3. Klicka på namnet på ett schema för att öppna dess detaljsida.
4. Ändra **aktiverat** till **nr**.

### <a name="to-disable-a-schedule-with-windows-powershell"></a>Inaktivera ett schema med Windows PowerShell
Du kan använda den [Set AzureAutomationSchedule](http://msdn.microsoft.com/library/azure/dn690270.aspx) för att ändra egenskaperna för ett befintligt schema för en klassiska runbook eller [Set AzureRmAutomationSchedule](/powershell/module/azurerm.automation/set-azurermautomationschedule) cmdlet för runbooks i Azure-portalen. Om du vill inaktivera schemat, ange **FALSKT** för den **IsEnabled** parameter.

Följande exempelkommandon visar hur du inaktiverar ett schema för en runbook med en Azure Resource Manager-cmdlet.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-MonthlyDaysOfMonthSchedule"
    Set-AzureRmAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false -ResourceGroupName "ResourceGroup01"

Följande exempelkommandon visar hur du inaktiverar ett schema med Azure Service Management-cmdlet.

    $automationAccountName = "MyAutomationAccount"
    $scheduleName = "Sample-DailySchedule"
    Set-AzureAutomationSchedule –AutomationAccountName $automationAccountName `
    –Name $scheduleName –IsEnabled $false

## <a name="next-steps"></a>Nästa steg
* Kom igång med runbooks i Azure Automation finns [starta en Runbook i Azure Automation](automation-starting-a-runbook.md) 

