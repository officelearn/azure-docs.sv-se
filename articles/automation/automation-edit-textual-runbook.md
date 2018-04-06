---
title: Redigera textrepresentation runbooks i Azure Automation
description: Den här artikeln innehåller olika procedurer för att arbeta med PowerShell och PowerShell-arbetsflöde runbooks i Azure Automation med hjälp av textrepresentation editor.
services: automation
ms.service: automation
author: georgewallace
ms.author: gwallace
ms.date: 04/02/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 055bd2a7607b8cab9c7ca417c7c3f57c2e569f77
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Redigera textrepresentation runbooks i Azure Automation

Textrepresentation redigeraren i Azure Automation kan användas för att redigera [PowerShell runbooks](automation-runbook-types.md#powershell-runbooks) och [PowerShell-arbetsflöde runbooks](automation-runbook-types.md#powershell-workflow-runbooks). Detta har vanliga funktioner i andra koden redigerare, till exempel intellisense och syntax i färger med ytterligare särskilda funktioner som hjälper dig att komma åt resurser som är gemensamma för runbooks. Den här artikeln innehåller detaljerade anvisningar för att utföra olika funktioner med den här redigeraren.

Textrepresentation redigeraren innehåller en funktion för att infoga kod för cmdlets, tillgångar och underordnade runbooks i en runbook. I stället för att skriva i koden själv, kan du välja från en lista över tillgängliga resurser och lämplig kod infogas i runbooken.

Varje runbook i Azure Automation har två versioner: utkast och publicerad. Du redigerar Utkastversionen av runbooken och publicerar det så att den kan köras. Den publicerade versionen kan inte redigeras. Se [publicera en runbook](automation-creating-importing-runbook.md#publishing-a-runbook) för mer information.

Arbeta med [grafiska runbook-flöden](automation-runbook-types.md#graphical-runbooks), se [grafiska redigering i Azure Automation](automation-graphical-authoring-intro.md).

## <a name="to-edit-a-runbook-with-the-azure-portal"></a>Så här redigerar du en runbook med Azure-portalen

Använd följande procedur för att öppna en runbook för redigering i redigeraren textrepresentation.

1. Välj ditt automation-konto i Azure-portalen.
2. Under **PROCESSAUTOMATISERING**väljer **Runbooks** att öppna listan över runbooks.
3. Välj den runbook du vill redigera och klicka sedan på den **redigera** knappen.
4. Utför den redigering som krävs.
5. Klicka på **spara** när ändringarna har slutförts.
6. Klicka på **publicera** om du vill att den senaste Utkastversionen av runbooken ska publiceras.

### <a name="to-insert-a-cmdlet-into-a-runbook"></a>Infoga en cmdlet i en runbook

1. Placera markören där du vill placera cmdlet i arbetsytan textrepresentation redigeraren.
2. Expandera den **Cmdlets** nod i biblioteket kontrollen.
3. Expandera den modul som innehåller den cmdlet som du vill använda.
4. Högerklicka på cmdlet om du vill infoga och välj **Lägg till arbetsytan**. Om cmdleten har mer än en parameter har angetts, läggs en standarduppsättning. Du kan också expandera cmdlet om du vill välja en annan parameteruppsättning.
5. Koden för cmdleten infogas med hela listan över parametrar.
6. Ange ett lämpligt värde i stället för datatypen som omges av klammerparenteser <> för alla obligatoriska parametrar. Ta bort alla parametrar som du behöver.

### <a name="to-insert-code-for-a-child-runbook-into-a-runbook"></a>Infoga kod för en underordnad runbook i en runbook

1. I arbetsytan redigeraren textrepresentation placera markören där du vill att koden för den [underordnad runbook](automation-child-runbooks.md).
2. Expandera den **Runbooks** nod i biblioteket kontrollen.
3. Högerklicka på runbooken du vill infoga och välj **Lägg till arbetsytan**.
4. Koden för den underordnade runbooken infogas med några platshållare för alla runbookparametrar.
5. Ersätt platshållarna med lämpliga värden för varje parameter.

### <a name="to-insert-an-asset-into-a-runbook"></a>Infoga en tillgång i en runbook

1. Placera markören där du vill att koden för den underordnade runbooken i arbetsytan textrepresentation redigeraren.
2. Expandera den **tillgångar** nod i biblioteket kontrollen.
3. Expandera noden för den tillgång som du vill använda.
4. Högerklicka på tillgång för att infoga och välj **Lägg till arbetsytan**. För [variabeln tillgångar](automation-variables.md), väljer du antingen **Lägg till ”hämta variabel” på arbetsytan** eller **Lägg till ”ange variabel” på arbetsytan** beroende på om du vill hämta eller ange variabeln.
5. Koden för tillgången infogas i runbook.

## <a name="to-edit-an-azure-automation-runbook-using-windows-powershell"></a>Så här redigerar du en Azure Automation-runbook med Windows PowerShell

Om du vill redigera en runbook med Windows PowerShell, använda valfri redigerare och spara den till en .ps1-fil. Du kan använda den [Get-AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/getazurerunbookdefinition) för att hämta innehållet i runbook och sedan [Set AzureAutomationRunbookDefinition](http://aka.ms/runbookauthor/cmdlet/setazurerunbookdefinition) för att ersätta den befintliga utkast runbook med det ändrade.

### <a name="to-retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Att hämta innehållet i en Runbook med Windows PowerShell

Följande exempelkommandon visar hur du hämtar skriptet för en runbook och spara den till en skriptfil. I det här exemplet hämtas utkastversionen. Det är också möjligt att hämta den publicerade versionen av runbooken även om den här versionen inte kan ändras.

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

$runbookDefinition = Get-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Slot Draft
$runbookContent = $runbookDefinition.Content

Out-File -InputObject $runbookContent -FilePath $scriptPath
```

### <a name="to-change-the-contents-of-a-runbook-using-windows-powershell"></a>Så här ändrar du innehållet i en Runbook med Windows PowerShell

Följande exempelkommandon visar hur du ersätter det befintliga innehållet i en runbook med innehållet i en skriptfil. Observera att det här är exempel samma procedur som i [att importera en runbook från en skriptfil med Windows PowerShell](automation-creating-importing-runbook.md).

```powershell-interactive
$automationAccountName = "MyAutomationAccount"
$runbookName = "Sample-TestRunbook"
$scriptPath = "c:\runbooks\Sample-TestRunbook.ps1"

Set-AzureAutomationRunbookDefinition -AutomationAccountName $automationAccountName -Name $runbookName -Path $scriptPath -Overwrite
Publish-AzureAutomationRunbook –AutomationAccountName $automationAccountName –Name $runbookName
```

## <a name="related-articles"></a>Relaterade artiklar

* [Skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md)
* [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md)
* [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Scheman](automation-schedules.md)
* [Variabler](automation-variables.md)
