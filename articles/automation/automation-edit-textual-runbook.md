---
title: Redigera runbooks med text i Azure Automation
description: Den här artikeln innehåller olika procedurer för att arbeta med PowerShell och PowerShell Workflow-runbooks i Azure Automation med textredigeraren.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 9c684e9d1bf6cec12024cedfb5360d10e400e139
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416012"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Redigera runbooks med text i Azure Automation

Textredigeraren i Azure Automation kan användas för att redigera [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) och [PowerShell Workflow-runbooks](automation-runbook-types.md#powershell-workflow-runbooks). Detta har vanliga funktioner i andra kod-redigerare till exempel intellisense och färgkodning med fler särskilda funktioner som hjälper dig att komma åt resurser som är gemensamma för runbooks. Den här artikeln innehåller detaljerade anvisningar för att utföra olika funktioner med den här redigeraren.

Textredigeraren innehåller en funktion för att infoga kod för cmdlet: ar, tillgångar och underordnade runbooks i en runbook. I stället för att skriva i koden själv, kan du välja från en lista med tillgängliga resurser och lämplig kod som infogats i runbook.

Varje runbook i Azure Automation har två versioner: utkast och publicerad. Du redigerar Utkastversionen av runbooken och publicerar det så att den kan köras. Den publicerade versionen kan inte redigeras. Mer information finns i [publicera en runbook](manage-runbooks.md#publish-a-runbook).

Att arbeta med [grafiska Runbooks](automation-runbook-types.md#graphical-runbooks), se [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Redigera en runbook med Azure portal

Använd följande procedur för att öppna en runbook för redigering i textredigeraren.

1. I Azure-portalen väljer du ditt automation-konto.
2. Under **PROCESSAUTOMATISERING**väljer **Runbooks** att öppna listan med runbooks.
3. Välj den runbook som du vill redigera och klicka sedan på den **redigera** knappen.
4. Redigera runbook.
5. Klicka på **spara** när ändringarna har slutförts.
6. Klicka på **publicera** om du vill att den senaste Utkastversionen av runbooken som ska publiceras.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Infoga en cmdlet i en runbook

1. Placera markören där du vill att cmdleten på arbetsytan i textredigeraren.
2. Expandera den **cmdletar** nod i bibliotekskontrollen.
3. Expandera den modul som innehåller den cmdlet som du vill använda.
4. Högerklicka på cmdleten du vill infoga och välj **Lägg till på ytan**. Om cmdleten finns mer än en parameter som anger läggas en standarduppsättning. Du kan också expandera cmdlet: en för att välja en annan parameteruppsättning.
5. Koden för cmdlet: en infogas med hela listan över parametrar.
6. Ange ett lämpligt värde i stället för datatypen som omges av klammerparenteser <> för eventuella obligatoriska parametrar. Ta bort alla parametrar som du inte behöver.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Att infoga kod för en underordnad runbook i en runbook

1. I arbetsytan i textredigeraren, placerar du markören där du vill att koden för den [underordnad runbook](automation-child-runbooks.md).
2. Expandera den **Runbooks** nod i bibliotekskontrollen.
3. Högerklicka på runbooken du vill infoga och välj **Lägg till på ytan**.
4. Koden för den underordnade runbooken infogas med några platshållare för alla runbookparametrar.
5. Ersätt platshållarna med lämpliga värden för varje parameter.

### <a name="to-insert-an-asset-into-a-runbook"></a>Infoga en tillgång i en runbook

1. Placera markören där du vill att koden för den underordnade runbooken i arbetsytan i textredigeraren.
2. Expandera den **tillgångar** nod i bibliotekskontrollen.
3. Expandera noden för typ av tillgång som du vill.
4. Högerklicka på tillgången du vill infoga och välj **Lägg till på ytan**. För [variabler för tillgångar](automation-variables.md), väljer du antingen **Lägg till ”hämta variabel” på ytan** eller **Lägg till ”ange variabel” på ytan** beroende på om du vill hämta eller ange variabeln.
5. Koden för tillgången infogas i runbooken.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Så här redigerar du en Azure Automation-runbook med Windows PowerShell

Om du vill redigera en runbook med Windows PowerShell måste du använda valfri redigerare och spara den i en `.ps1` fil. Du kan använda den [Export-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/Export-AzureRmAutomationRunbook) cmdlet för att hämta innehållet i runbook och sedan [Import-AzureRmAutomationRunbook](/powershell/module/AzureRM.Automation/import-azurermautomationrunbook) cmdlet för att ersätta den befintliga utkast runbook med den ändra något.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Att hämta innehållet i en Runbook med Windows PowerShell

Följande exempelkommandon visar hur du hämtar skriptet för en runbook och spara det i en skriptfil. I det här exemplet hämtas utkastversionen. Du kan också hämta den publicerade versionen av runbooken även om den här versionen inte kan ändras.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Ändra innehållet i en Runbook med Windows PowerShell

Följande exempelkommandon visar hur du ersätter det befintliga innehållet i en runbook med innehållet i en skriptfil. Det här är samma exempel procedur som i [att importera en runbook från en skriptfil med Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzureRmAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzureRmAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Relaterade artiklar

* [Hantera runbooks i Azure Automation](manage-runbooks.md)
* [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md)
* [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Scheman](automation-schedules.md)
* [Variabler](automation-variables.md)

