---
title: Hantera Runbooks i Azure Automation
description: Den här artikeln beskriver hur du hanterar Runbooks i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: ec53c4b2f80fb095f58bee9c15ac5daafb8d59ef
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78372425"
---
# <a name="manage-runbooks-in-azure-automation"></a>Hantera Runbooks i Azure Automation

Du kan lägga till en Runbook i Azure Automation genom att antingen [skapa en ny](#create-a-runbook) eller [Importera en befintlig](#import-a-runbook) från en fil eller [Runbook-galleriet](automation-runbook-gallery.md). Den här artikeln innehåller information om hur du skapar och importerar Runbooks från en fil. Du kan få all information om hur du kommer åt community-Runbooks och moduler i [Runbook-och modul gallerier för Azure Automation](automation-runbook-gallery.md).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installations anvisningar för AZ-modulen på Hybrid Runbook Worker finns i [installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med hjälp av [hur du uppdaterar Azure PowerShell moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="create-a-runbook"></a>Skapa en runbook

Du kan skapa en ny Runbook i Azure Automation med någon av Azure-portalerna eller Windows PowerShell. När runbook har skapats kan du redigera den med hjälp av information i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md) och [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Skapa en Runbook i Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Från hubben väljer du **Runbooks** under **process automatisering** för att öppna listan över Runbooks.
3. Klicka på **skapa en Runbook**.
4. Ange ett namn för runbooken och välj dess [typ](automation-runbook-types.md). Run Book namnet måste börja med en bokstav och får innehålla bokstäver, siffror, under streck och bindestreck.
5. Klicka på **skapa** för att skapa runbooken och öppna redigeraren.

### <a name="create-a-runbook-with-powershell"></a>Skapa en Runbook med PowerShell

Du kan använda cmdleten [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) för att skapa en tom [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Använd *typ* parametern för att ange en av de definierade Runbook-typerna för **New-AzAutomationRunbook**.

I följande exempel visas hur du skapar en ny tom Runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importera en Runbook

Du kan skapa en ny Runbook i Azure Automation genom att importera ett PowerShell-skript eller ett PowerShell-arbetsflöde ( **. ps1**), en exporterad grafisk Runbook-fil ( **. graphrunbook**) eller ett python 2-skript ( **. py**).  Du måste ange vilken [typ av Runbook](automation-runbook-types.md) som skapas under importen, med hänsyn till följande överväganden.

* En **. ps1** -fil som inte innehåller ett arbets flöde kan importeras till antingen en [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Om du importerar den till en PowerShell Workflow-Runbook konverteras den till ett arbets flöde. I det här fallet ingår kommentarer i runbooken för att beskriva de ändringar som har gjorts.

* En **. ps1** -fil som innehåller ett PowerShell-arbetsflöde kan bara importeras till en [PowerShell Workflow-Runbook](automation-runbook-types.md#powershell-workflow-runbooks). Importen Miss lyckas om filen innehåller flera PowerShell-arbetsflöden. Du måste spara varje arbets flöde till en egen fil och importera varje separat.

* En **. ps1** -fil som innehåller ett PowerShell-arbetsflöde ska inte importeras till en [PowerShell-Runbook](automation-runbook-types.md#powershell-runbooks)eftersom PowerShell-skriptfilen inte kan identifiera den.

* En **. graphrunbook** -fil kan bara importeras till en ny [grafisk Runbook](automation-runbook-types.md#graphical-runbooks). Observera att du bara kan skapa en grafisk Runbook från en **. graphrunbook** -fil.

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importera en Runbook från en fil med Azure Portal

Du kan använda följande procedur för att importera en skript fil till Azure Automation.

> [!NOTE]
> Du kan bara importera en **. ps1** -fil till en PowerShell Workflow-Runbook med hjälp av portalen.

1. Öppna ditt Automation-konto på Azure Portal.
2. Från hubben väljer du **Runbooks** under **process automatisering** för att öppna listan över Runbooks.
3. Klicka på **Importera en Runbook**.
4. Klicka på **Runbook-fil** och välj den fil som ska importeras.
5. Om fältet **namn** är aktiverat har du möjlighet att ändra Runbook-namnet. Namnet måste börja med en bokstav och får innehålla bokstäver, siffror, under streck och bindestreck.
6. [Runbook-typen](automation-runbook-types.md) väljs automatiskt, men du kan ändra typen efter att ha vidtagit tillämpliga begränsningar i kontot.
7. Klicka på **Skapa**. Den nya runbooken visas i listan över Runbooks för Automation-kontot.
8. Du måste [publicera runbooken](#publish-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk Runbook eller en grafisk Runbook för PowerShell-arbetsflöde kan du konvertera den till en annan typ. Du kan dock inte konvertera en av dessa grafiska runbooks till en text-Runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importera en runbook från en skriptfil med Windows PowerShell

Använd cmdleten [import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) för att importera en skript fil som ett utkast till en PowerShell-Runbook för arbets flöde. Om runbooken redan finns, Miss lyckas importen om du inte använder *Force* -parametern med cmdleten.

I följande exempel visas hur du importerar en skript fil till en Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="test-a-runbook"></a>Testa en Runbook

När du testar en runbook körs [utkast versionen](#publish-a-runbook) och alla åtgärder som den utför slutförs. Ingen jobb historik skapas, men data strömmarna för [utdata](automation-runbook-output-and-messages.md#output-stream) och [fel](automation-runbook-output-and-messages.md#message-streams) visas i fönstret Testa utdata. Meddelanden till [utförlig data ström](automation-runbook-output-and-messages.md#message-streams) visas endast i fönstret utdata om variabeln *VerbosePreference* ] (Automation-Runbook-output-and-Messages. MD # Preference-variabler) är inställd på **Fortsätt**.

Även om utkastet körs körs inte runbooken normalt och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa Runbooks på icke-produktions resurser.

Proceduren för att testa varje [typ av Runbook](automation-runbook-types.md) är densamma. Det finns ingen skillnad i testning mellan text redigeraren och den grafiska redigeraren i Azure Portal.

1. Öppna utkast versionen av runbooken i antingen [text redigeraren](automation-edit-textual-runbook.md) eller den [grafiska redigeraren](automation-graphical-authoring-intro.md).
1. Öppna test sidan genom att klicka på knappen **testa** .
1. Om runbooken har parametrar visas de i den vänstra rutan där du kan ange värden som ska användas för testet.
1. Om du vill köra testet på en [hybrid Runbook Worker](automation-hybrid-runbook-worker.md)ändrar du **körnings inställningarna** till **hybrid Worker** och väljer namnet på mål gruppen.  Annars behåller du standard **Azure** att köra testet i molnet.
1. Starta testet genom att klicka på knappen **Starta** .
1. Du kan använda knapparna under fönstret utdata för att stoppa eller pausa ett [PowerShell-arbetsflöde](automation-runbook-types.md#powershell-workflow-runbooks) eller en [grafisk](automation-runbook-types.md#graphical-runbooks) Runbook medan den testas. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
1. Granska utdata från runbooken i fönstret utdata.

## <a name="publish-a-runbook"></a>Publicera en Runbook

När du skapar eller importerar en ny Runbook måste du publicera den innan du kan köra den. Varje Runbook i Azure Automation har en utkast version och en publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När du ska göra utkastet tillgänglig, publicerar du den och skriver över den aktuella publicerade versionen med utkast versionen.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publicera en Runbook i Azure Portal

1. Öppna runbooken i Azure Portal.
2. Klicka på **Redigera**.
3. Klicka på **publicera** och sedan **Ja** som svar på verifierings meddelandet.

### <a name="publish-a-runbook-using-powershell"></a>Publicera en runbook med hjälp av PowerShell

Använd cmdleten [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) för att publicera en Runbook med Windows PowerShell. I följande exempel visas hur du publicerar en exempel-Runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Nästa steg

* Information om hur du kan dra nytta av Runbook-och PowerShell-modulens Galleri finns i avsnittet [om Runbook och moduler för Azure Automation](automation-runbook-gallery.md).
* Mer information om hur du redigerar PowerShell-och PowerShell Workflow-Runbooks med en text redigerare finns i [Redigera text Runbooks i Azure Automation](automation-edit-textual-runbook.md).
* Mer information om grafisk redigering av Runbook finns [i grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
