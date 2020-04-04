---
title: Redigera textkörningsböcker i Azure Automation
description: Den här artikeln innehåller olika procedurer för att arbeta med PowerShell- och PowerShell-arbetsflödeskörningsböcker i Azure Automation med hjälp av textredigeraren.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 3bbfe26f336a25ee85f2223226d6eb513ae21736
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632149"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Redigera textkörningsböcker i Azure Automation

Textredigeraren i Azure Automation kan användas för att redigera [PowerShell-runbooks](automation-runbook-types.md#powershell-runbooks) och [PowerShell Workflow runbooks](automation-runbook-types.md#powershell-workflow-runbooks). Den här redigeraren har de typiska funktionerna i andra kodredigerare, till exempel IntelliSense. Den har också färgkodning med ytterligare specialfunktioner som hjälper dig att komma åt resurser som är gemensamma för runbooks. 

Textredigeraren innehåller en funktion för att infoga kod för cmdlets, tillgångar och underordnade runbooks i en runbook. I stället för att skriva in koden själv kan du välja från en lista över tillgängliga resurser och redigeraren infogar lämplig kod i runbooken.

Varje runbook i Azure Automation har två versioner, Utkast och Publicerat. Du redigerar utkastversionen av runbooken och publicerar den så att den kan köras. Den publicerade versionen kan inte redigeras. Mer information finns i [Publicera en runbook](manage-runbooks.md#publishing-a-runbook).

Den här artikeln innehåller detaljerade steg för att utföra olika funktioner med den här redigeraren. Dessa gäller inte [för grafiska runbooks](automation-runbook-types.md#graphical-runbooks). Information om hur du arbetar med dessa runbooks finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Redigera en runbook med Azure-portalen

Använd följande procedur för att öppna en runbook för redigering i textredigeraren.

1. Välj ditt Automation-konto i Azure-portalen.
2. Under **PROCESS AUTOMATION**väljer du **Runbooks** för att öppna listan över runbooks.
3. Välj den runbook som ska redigeras och klicka sedan på **Redigera**.
4. Redigera runbooken.
5. Klicka på **Spara** när ändringarna är klara.
6. Klicka på **Publicera** om du vill publicera den senaste utkastversionen av runbooken.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Infoga en cmdlet i en runbook

1. Placera markören på arbetsytan på textredigeraren där du vill placera cmdleten.
2. Expandera **noden Cmdlets** i bibliotekskontrollen.
3. Expandera modulen som innehåller den cmdlet som ska användas.
4. Högerklicka på cmdletnamnet för att infoga och välj **Lägg till på arbetsyta .** Om cmdleten har fler än en parameteruppsättning läggs standarduppsättningen till i redigeraren. Du kan också expandera cmdleten för att välja en annan parameteruppsättning.
5. Observera att koden för cmdleten infogas med hela listan med parametrar.
6. Ange ett lämpligt värde i stället för värdet omgivet av vinkelparenteser (<>) för alla nödvändiga parametrar. Ta bort alla parametrar som du inte behöver.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Infoga kod för en underordnad runbook i en runbook

1. Placera markören på arbetsytan i textredigeraren där du vill placera koden för den [underordnade runbooken](automation-child-runbooks.md).
2. Expandera noden **Runbooks** i bibliotekskontrollen.
3. Högerklicka på runbooken om du vill infoga och välj **Lägg till på arbetsyta .**
4. Koden för den underordnade runbooken infogas med alla platshållare för alla runbook-parametrar.
5. Ersätt platshållarna med lämpliga värden för varje parameter.

### <a name="insert-an-asset-into-a-runbook"></a>Infoga en tillgång i en runbook

1. Placera markören där du vill placera koden för den underordnade runbooken i canvaskontrollen för textredigeraren.
2. Expandera noden **Resurser** i bibliotekskontrollen.
3. Expandera noden för önskad tillgångstyp.
4. Högerklicka på tillgångsnamnet för att infoga och välj **Lägg till på arbetsyta .** För [variabla tillgångar](automation-variables.md)väljer du antingen **Lägg till "Hämta variabel" på arbetsytan** eller **Lägg till "Ange variabel" på arbetsytan,** beroende på om du vill hämta eller ange variabeln.
5. Observera att koden för tillgången infogas i runbooken.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Redigera en Azure Automation-runbook med Windows PowerShell

Om du vill redigera en runbook med Windows PowerShell använder du valfri redigerare och sparar runbooken i en **PS1-fil.** Du kan använda cmdleten [Exportera-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) för att hämta innehållet i runbooken. Du kan använda cmdleten [Import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) för att ersätta den befintliga utkastkörningsboken med den ändrade.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Hämta innehållet i en runbook med Windows PowerShell

Följande exempelkommandon visar hur du hämtar skriptet för en runbook och sparar den till en skriptfil. I det här exemplet hämtas utkastversionen. Det är också möjligt att hämta den publicerade versionen av runbooken, även om den här versionen inte kan ändras.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Ändra innehållet i en runbook med Windows PowerShell

Följande exempelkommandon visar hur du ersätter det befintliga innehållet i en runbook med innehållet i en skriptfil. Det här är samma exempelprocedur som i [Importera en runbook från en skriptfil med Windows PowerShell](manage-runbooks.md#importing-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Relaterade artiklar

* [Hantera runbooks i Azure Automation](manage-runbooks.md)
* [PowerShell-arbetsflöde för utbildning](automation-powershell-workflow.md)
* [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Scheman](automation-schedules.md)
* [Variabler](automation-variables.md)

