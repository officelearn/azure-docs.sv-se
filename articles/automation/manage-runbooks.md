---
title: Hantera Runbooks i Azure Automation
description: Den här artikeln beskriver hur du hanterar Runbooks i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5a477811e46d97375d4dce4d83072dda60ca797c
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2019
ms.locfileid: "68717216"
---
# <a name="manage-runbooks-in-azure-automation"></a>Hantera Runbooks i Azure Automation

Du kan lägga till en Runbook i Azure Automation genom att antingen [skapa en ny](#create-a-runbook) eller genom att importera en befintlig Runbook från en fil eller [Runbook](automation-runbook-gallery.md)-galleriet. Den här artikeln innehåller information om hur du skapar och importerar Runbooks från en fil.  Du kan få all information om hur du kommer åt community-Runbooks och moduler i [Runbook-och modul gallerier för Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Skapa en runbook

Du kan skapa en ny Runbook i Azure Automation med någon av Azure-portalerna eller Windows PowerShell. När runbook har skapats kan du redigera den med hjälp av information i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md) och [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Skapa en Runbook i Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Öppna listan över runbooks genom att välja **Runbooks** från hubben.
3. Klicka på knappen **Lägg till en Runbook** och **skapa sedan en ny Runbook**.
4. Ange ett **namn** för runbooken och välj dess [typ](automation-runbook-types.md). Runbook-namn måste börja med en bokstav och kan ha bokstäver, siffror, understreck och bindestreck.
5. Klicka på **skapa** för att skapa runbooken och öppna redigeraren.

### <a name="create-a-runbook-with-powershell"></a>Skapa en Runbook med PowerShell

Du kan använda cmdleten [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) för att skapa en tom [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Använd **typ** parametern för att ange någon av de fyra typerna av Runbook.

Följande exempel kommandon visar hur du skapar en ny tom Runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importera en Runbook

Du kan skapa en ny Runbook i Azure Automation genom att importera ett PowerShell-skript eller ett PowerShell-arbetsflöde (. ps1-tillägg), en exporterad grafisk Runbook-fil (. graphrunbook) eller ett python 2-skript (. py-tillägg).  Du måste ange vilken [typ av Runbook](automation-runbook-types.md) som skapas under importen, med hänsyn till följande överväganden.

* En `.graphrunbook` fil kan bara importeras till en ny [grafisk Runbook](automation-runbook-types.md#graphical-runbooks)och grafiska runbooks kan bara skapas från en `.graphrunbook` fil.
* En `.ps1` fil som innehåller ett PowerShell-arbetsflöde kan bara importeras till en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Om filen innehåller flera PowerShell-arbetsflöden går det inte att importera. Du måste spara varje arbets flöde till en egen fil och importera varje separat.
* En `.ps1` fil som innehåller ett PowerShell-arbetsflöde ska inte importeras till en [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks)eftersom den inte kan identifieras av PowerShell-skriptet.
* En `.ps1` fil som inte innehåller ett arbets flöde kan importeras till antingen en [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Om den importeras till en PowerShell Workflow-Runbook, konverteras den till ett arbets flöde, och kommentarer ingår i den Runbook som anger de ändringar som har gjorts.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Så här importerar du en Runbook från en fil med Azure Portal

Du kan använda följande procedur för att importera en skript fil till Azure Automation.

> [!NOTE]
> Observera att du bara kan importera en. ps1-fil till en PowerShell Workflow-Runbook med hjälp av portalen.

1. Öppna ditt Automation-konto på Azure Portal.
2. Öppna listan över runbooks genom att välja **Runbooks** från hubben.
3. Klicka på knappen **Lägg till en Runbook** och sedan på **Importera**.
4. Klicka på **Runbook-fil** för att välja den fil som ska importeras
5. Om fältet **namn** är aktiverat har du möjlighet att ändra det.  Runbook-namn måste börja med en bokstav och kan ha bokstäver, siffror, understreck och bindestreck.
6. [Runbook-typen](automation-runbook-types.md) väljs automatiskt, men du kan ändra typen efter att ha vidtagit tillämpliga begränsningar i kontot.
7. Den nya runbooken visas i listan över Runbooks för Automation-kontot.
8. Du måste [publicera runbooken](#publish-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk Runbook eller en grafisk PowerShell-arbetsflöde, har du möjlighet att konvertera till den andra typen om det önskas. Det går inte att konvertera till en text-Runbook.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Så här importerar du en Runbook från en skript fil med Windows PowerShell

Du kan använda cmdleten [import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) för att importera en skript fil som ett utkast till en PowerShell-Runbook för arbets flöde. Om det redan finns en Runbook Miss lyckas importen om du inte använder parametern *-Force* .

Följande exempel kommandon visar hur du importerar en skript fil till en Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testa en Runbook

När du testar en runbook körs [utkast versionen](#publish-a-runbook) och alla åtgärder som den utför slutförs. Ingen jobb historik skapas, men data strömmarna för [utdata](automation-runbook-output-and-messages.md#output-stream) och [fel](automation-runbook-output-and-messages.md#message-streams) visas i fönstret Testa utdata. Meddelanden till [utförlig data ström](automation-runbook-output-and-messages.md#message-streams) visas endast i fönstret utdata om [variabeln $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) är inställd på Fortsätt.

Även om utkastet körs körs inte runbooken normalt och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa Runbooks på icke-produktions resurser.

Proceduren för att testa varje [typ av Runbook](automation-runbook-types.md) är densamma och det finns ingen skillnad i testningen mellan text redigeraren och den grafiska redigeraren i Azure Portal.

1. Öppna utkast versionen av runbooken i [text redigeraren](automation-edit-textual-runbook.md) eller i en [grafisk redigerare](automation-graphical-authoring-intro.md).
1. Öppna test sidan genom att klicka på knappen **testa** .
1. Om runbooken har parametrar visas de i den vänstra rutan där du kan ange värden som ska användas för testet.
1. Om du vill köra testet på en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md)ändrar du **inställningarna för körning** till **hybrid Worker** och väljer namnet på mål gruppen.  Annars behåller du standard **Azure** att köra testet i molnet.
1. Starta testet genom att klicka på knappen **Starta** .
1. Om Runbook är ett [PowerShell-arbetsflöde](automation-runbook-types.md#powershell-workflow-runbooks) eller [grafiskt](automation-runbook-types.md#graphical-runbooks)kan du stoppa eller inaktivera det medan det testas med knapparna under fönstret utdata. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
1. Granska utdata från runbooken i fönstret utdata.

## <a name="publish-a-runbook"></a>Publicera en runbook

När du skapar eller importerar en ny Runbook måste du publicera den innan du kan köra den.  Varje Runbook i Automation har ett utkast och en publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När utkast versionen ska göras tillgänglig publicerar du den, vilket skriver över den publicerade versionen med utkastet.

### <a name="azure-portal"></a>Azure Portal

1. Öppna runbooken i Azure Portal.
2. Klicka på knappen **Redigera** .
3. Klicka på knappen **publicera** och sedan på **Ja** till verifierings meddelandet.

### <a name="powershell"></a>PowerShell

Du kan använda cmdleten [Publish-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) för att publicera en Runbook med Windows PowerShell. Följande exempel kommandon visar hur du publicerar en exempel-Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du kan dra nytta av Runbook-och PowerShell-modulens galleri i [Runbook-och modul gallerier för Azure Automation](automation-runbook-gallery.md)
* Mer information om hur du redigerar PowerShell-och PowerShell Workflow-Runbooks med en text redigerare finns i [Redigera text Runbooks i Azure Automation](automation-edit-textual-runbook.md)
* Mer information om grafisk redigering av Runbook finns [i grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
