---
title: Redigera text-Runbooks i Azure Automation
description: Den här artikeln innehåller olika procedurer för att arbeta med PowerShell-och PowerShell Workflow-Runbooks i Azure Automation med hjälp av text redigeraren.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 08/01/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: c00ab8a5e0d307d89ae483db55bdb0b1258ae6a4
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2020
ms.locfileid: "78246411"
---
# <a name="editing-textual-runbooks-in-azure-automation"></a>Redigera text-Runbooks i Azure Automation

Text redigeraren i Azure Automation kan användas för att redigera [PowerShell-Runbooks](automation-runbook-types.md#powershell-runbooks) och [PowerShell-arbetsflöden](automation-runbook-types.md#powershell-workflow-runbooks). Den här redigeraren har typiska funktioner i andra kod redigerare, t. ex. IntelliSense. Den har också färg kodning med ytterligare special funktioner som hjälper dig att komma åt resurser som är gemensamma för Runbooks. 

Text redigeraren innehåller en funktion för att infoga kod för cmdlets, till gångar och underordnade Runbooks i en Runbook. I stället för att skriva in koden själv kan du välja från en lista över tillgängliga resurser och redigeraren infogar lämplig kod i runbooken.

Varje Runbook i Azure Automation har två versioner, utkast och publicerat. Du redigerar utkast versionen av runbooken och publicerar den så att den kan köras. Den publicerade versionen kan inte redigeras. Mer information finns i [publicera en Runbook](manage-runbooks.md#publish-a-runbook).

Den här artikeln innehåller detaljerade anvisningar för hur du utför olika funktioner i den här redigeraren. Dessa gäller inte för [grafiska runbooks](automation-runbook-types.md#graphical-runbooks). Information om hur du arbetar med dessa Runbooks finns i [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="editing-a-runbook-with-the-azure-portal"></a>Redigera en Runbook med Azure Portal

Använd följande procedur för att öppna en Runbook för redigering i text redigeraren.

1. I Azure Portal väljer du ditt Automation-konto.
2. Under **process automatisering**väljer du **Runbooks** för att öppna listan över Runbooks.
3. Välj den Runbook som du vill redigera och klicka sedan på **Redigera**.
4. Redigera runbooken.
5. Klicka på **Spara** när du har redigerat klart.
6. Klicka på **publicera** om du vill publicera den senaste utkast versionen av runbooken.

### <a name="insert-a-cmdlet-into-a-runbook"></a>Infoga en cmdlet i en Runbook

1. Placera markören där du vill placera cmdleten på arbets ytan i text redigeraren.
2. Expandera noden **cmdlets** i biblioteks kontrollen.
3. Expandera modulen som innehåller den cmdlet som ska användas.
4. Högerklicka på det cmdlet-namn som du vill infoga och välj **Lägg till i arbets ytan**. Om cmdleten har fler än en parameter uppsättning läggs standard uppsättningen till i redigeraren. Du kan också expandera cmdleten om du vill välja en annan parameter uppsättning.
5. Observera att koden för cmdleten infogas med hela listan med parametrar.
6. Ange ett lämpligt värde i stället för värdet omgiven av vinkelparenteser (< >) för alla obligatoriska parametrar. Ta bort alla parametrar som du inte behöver.

### <a name="insert-code-for-a-child-runbook-into-a-runbook"></a>Infoga kod för en underordnad Runbook i en Runbook

1. Placera markören där du vill placera koden för [underordnad Runbook](automation-child-runbooks.md)på arbets ytan i text redigeraren.
2. Expandera noden **Runbooks** i biblioteks kontrollen.
3. Högerklicka på den Runbook som du vill infoga och välj **Lägg till på arbets ytan**.
4. Koden för den underordnade runbooken infogas med plats hållare för alla Runbook-parametrar.
5. Ersätt plats hållarna med lämpliga värden för varje parameter.

### <a name="insert-an-asset-into-a-runbook"></a>Infoga en till gång i en Runbook

1. Placera markören där du vill placera koden för underordnad Runbook på arbets ytan i text redigeraren.
2. Expandera noden **till gångar** i biblioteks kontrollen.
3. Expandera noden för önskad till gångs typ.
4. Högerklicka på namnet på den till gång som du vill infoga och välj **Lägg till i arbets ytan**. För [variabel till gångar](automation-variables.md)väljer du antingen **Lägg till "Hämta variabel" på arbets ytan** eller **Lägg till "ange variabel" på arbets ytan**, beroende på om du vill hämta eller ange variabeln.
5. Observera att koden för till gången infogas i runbooken.

## <a name="editing-an-azure-automation-runbook-using-windows-powershell"></a>Redigera en Azure Automation Runbook med hjälp av Windows PowerShell

Om du vill redigera en Runbook med Windows PowerShell kan du använda valfri redigerare och spara runbooken i en ". ps1"-fil. Du kan använda cmdleten [export-AzAutomationRunbook](/powershell/module/Az.Automation/Export-AzAutomationRunbook) för att hämta innehållet i runbooken. Du kan använda cmdleten [import-AzAutomationRunbook](/powershell/module/Az.Automation/import-azautomationrunbook) för att ersätta den befintliga utkast-runbooken med den ändrade.

### <a name="retrieve-the-contents-of-a-runbook-using-windows-powershell"></a>Hämta innehållet i en Runbook med hjälp av Windows PowerShell

Följande exempelkommandon visar hur du hämtar skriptet för en runbook och spara det i en skriptfil. I det här exemplet hämtas utkastversionen. Det är också möjligt att hämta den publicerade versionen av Runbook, men den här versionen kan inte ändras.

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Export-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -OutputFolder $scriptFolder -Slot Draft
```

### <a name="change-the-contents-of-a-runbook-using-windows-powershell"></a>Ändra innehållet i en Runbook med hjälp av Windows PowerShell

Följande exempelkommandon visar hur du ersätter det befintliga innehållet i en Runbook med innehållet från en skriptfil. Det här är samma exempel procedur som i [för att importera en Runbook från en skript fil med Windows PowerShell](manage-runbooks.md#import-a-runbook).

```powershell-interactive
$resourceGroupName = "MyResourceGroup"
$automationAccountName = "MyAutomatonAccount"
$runbookName = "Hello-World"
$scriptFolder = "c:\runbooks"

Import-AzAutomationRunbook -Path "$scriptfolder\Hello-World.ps1" -Name $runbookName -Type PowerShell -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName -Force
Publish-AzAutomationRunbook -Name $runbookName -AutomationAccountName $automationAccountName -ResourceGroupName $resourceGroupName
```

## <a name="related-articles"></a>Relaterade artiklar

* [Hantera Runbooks i Azure Automation](manage-runbooks.md)
* [Utbildnings-PowerShell-arbetsflöde](automation-powershell-workflow.md)
* [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
* [Certifikat](automation-certificates.md)
* [Anslutningar](automation-connections.md)
* [Autentiseringsuppgifter](automation-credentials.md)
* [Scheman](automation-schedules.md)
* [Variabler](automation-variables.md)

