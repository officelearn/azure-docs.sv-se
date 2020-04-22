---
title: Hantera runbooks i Azure Automation
description: I den här artikeln beskrivs hur du hanterar runbooks i Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 02/14/2019
ms.topic: conceptual
ms.openlocfilehash: a1229ee389b41625554fb2869089b08a3cb9cb6d
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676519"
---
# <a name="manage-runbooks-in-azure-automation"></a>Hantera runbooks i Azure Automation

Du kan lägga till en runbook i Azure Automation genom att antingen [skapa en ny](#creating-a-runbook) eller importera en [befintlig](#importing-a-runbook) från en fil eller [Runbook Gallery](automation-runbook-gallery.md). Den här artikeln innehåller information om hur du skapar och importerar runbooks från en fil. Du kan få all information om åtkomst till community-runbooks och-moduler i [Runbook och modulgallerier för Azure Automation](automation-runbook-gallery.md).

>[!NOTE]
>Den här artikeln har uppdaterats till att använda den nya Azure PowerShell Az-modulen. Du kan fortfarande använda modulen AzureRM som kommer att fortsätta att ta emot felkorrigeringar fram till december 2020 eller längre. Mer information om den nya Az-modulen och AzureRM-kompatibilitet finns i [Introduktion till den nya Azure PowerShell Az-modulen](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Installationsinstruktioner för Az-modul på hybridkörningsarbetaren finns [i Installera Azure PowerShell-modulen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). För ditt Automation-konto kan du uppdatera dina moduler till den senaste versionen med [så här uppdaterar du Azure PowerShell-moduler i Azure Automation](automation-update-azure-modules.md).

## <a name="creating-a-runbook"></a>Skapa en runbook

Du kan skapa en ny runbook i Azure Automation med en av Azure-portalerna eller Windows PowerShell. När runbooken har skapats kan du redigera den med hjälp av information i [Learning PowerShell Workflow](automation-powershell-workflow.md) och [Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Skapa en runbook i Azure-portalen

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbooks** under **Process Automation** i navet för att öppna listan över runbooks.
3. Klicka på **Skapa en runbook**.
4. Ange ett namn för runbooken och välj dess [typ](automation-runbook-types.md). Runbook-namnet måste börja med en bokstav och innehålla bokstäver, siffror, understreck och streck.
5. Klicka på **Skapa** om du vill skapa runbooken och öppna redigeraren.

### <a name="create-a-runbook-with-powershell"></a>Skapa en runbook med PowerShell

Du kan använda cmdleten [New-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationrunbook?view=azps-3.5.0) för att skapa en tom [PowerShell-arbetsflödeskörningsbok](automation-runbook-types.md#powershell-workflow-runbooks). Använd `Type` parametern för att ange en `New-AzAutomationRunbook`av de runbooktyper som definierats för .

I följande exempel visas hur du skapar en ny tom runbook.

```azurepowershell-interactive
New-AzAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook"></a>Importera en runbook

Du kan skapa en ny runbook i Azure Automation genom att importera ett PowerShell-skript eller PowerShell-arbetsflöde (**.ps1**), en exporterad grafisk runbook (**.graphrunbook**) eller ett Python2-skript (**.py**).  Du måste ange vilken [typ av runbook](automation-runbook-types.md) som skapas under importen, med beaktande av följande överväganden.

* Du kan importera en **PS1-fil** som inte innehåller ett arbetsflöde till en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell-arbetsflödeskörningsbok](automation-runbook-types.md#powershell-workflow-runbooks). Om du importerar den till en PowerShell Workflow-runbook konverteras den till ett arbetsflöde. I det här fallet inkluderas kommentarer i runbooken för att beskriva de ändringar som har gjorts.

* Du kan bara importera en **PS1-fil** som innehåller ett PowerShell-arbetsflöde till en [PowerShell-arbetsflödeskörningsbok](automation-runbook-types.md#powershell-workflow-runbooks). Om filen innehåller flera PowerShell-arbetsflöden misslyckas importen. Du måste spara varje arbetsflöde i en egen fil och importera var och en separat.

* Importera inte en **PS1-fil** som innehåller ett PowerShell-arbetsflöde till en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks), eftersom PowerShell-skriptmotorn inte kan känna igen den.

* Du kan bara importera en **GRAPHRUNBOOK-fil** till en ny [grafisk runbook](automation-runbook-types.md#graphical-runbooks). Observera att du bara kan skapa en grafisk runbook från en **.graphrunbook-fil.**

### <a name="import-a-runbook-from-a-file-with-the-azure-portal"></a>Importera en runbook från en fil med Azure-portalen

Du kan använda följande procedur för att importera en skriptfil till Azure Automation.

> [!NOTE]
> Du kan bara importera en **PS1-fil** till en PowerShell-arbetsflödeskörningsbok med hjälp av portalen.

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbooks** under **Process Automation** i navet för att öppna listan över runbooks.
3. Klicka på **Importera en runbook**.
4. Klicka på **Runbook-filen** och markera den fil som ska importeras.
5. Om fältet **Namn** är aktiverat kan du välja att ändra runbooknamnet. Namnet måste börja med en bokstav och innehålla bokstäver, siffror, understreck och streck.
6. [Runbook-typen](automation-runbook-types.md) väljs automatiskt, men du kan ändra typen efter att du har tagit hänsyn till tillämpliga begränsningar.
7. Klicka på **Skapa**. Den nya runbooken visas i listan över runbooks för Automation-kontot.
8. Du måste [publicera runbooken](#publishing-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk runbook eller en grafisk PowerShell-arbetsflödeskörningsbok kan du konvertera den till en annan typ. Du kan dock inte konvertera någon av dessa grafiska runbooks till en text runbook.

### <a name="import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importera en runbook från en skriptfil med Windows PowerShell

Använd cmdleten [Import-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/az.automation/import-azautomationrunbook?view=azps-3.5.0) för att importera en skriptfil som ett utkast till PowerShell-arbetsflödeskörningsbok. Om runbooken redan finns misslyckas importen `Force` om du inte använder parametern med cmdleten.

I följande exempel visas hur du importerar en skriptfil till en runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
$RGName = "ResourceGroup"

Import-AzAutomationRunbook -Name $runbookName -Path $scriptPath `
-ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
-Type PowerShellWorkflow
```

## <a name="testing-a-runbook"></a>Testa en runbook

När du testar en runbook körs [utkastversionen](#publishing-a-runbook) och alla åtgärder som utförs slutförs. Ingen jobbhistorik skapas, men [utdata-](automation-runbook-output-and-messages.md#output-stream) och [varnings- och felströmmarna](automation-runbook-output-and-messages.md#message-streams) visas i fönstret Testutdata. Meddelanden till [verboseströmmen](automation-runbook-output-and-messages.md#message-streams) visas bara i utdatafönstret om variabeln [VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) är inställd på `Continue`.

Även om utkastversionen körs körs runbooken fortfarande normalt och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa runbooks på icke-produktionsresurser.

Proceduren för att testa varje [typ av runbook](automation-runbook-types.md) är densamma. Det finns ingen skillnad i testning mellan textredigeraren och den grafiska redigeraren i Azure-portalen.

1. Öppna utkastversionen av runbooken i [textredigeraren](automation-edit-textual-runbook.md) eller den [grafiska redigeraren](automation-graphical-authoring-intro.md).
1. Klicka på knappen **Testa** för att öppna sidan Testa.
1. Om runbooken har parametrar visas de i den vänstra rutan, där du kan ange värden som ska användas för testet.
1. Om du vill köra testet på en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md)ändrar du **Körinställningar till** **Hybridarbetare** och väljer namnet på målgruppen.  Annars behåller du **standard-Azure** för att köra testet i molnet.
1. Klicka på **Start för** att starta testet.
1. Du kan använda knapparna under utdatafönstret för att stoppa eller pausa ett [PowerShell-arbetsflöde](automation-runbook-types.md#powershell-workflow-runbooks) eller [en grafisk](automation-runbook-types.md#graphical-runbooks) runbook medan den testas. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
1. Kontrollera utdata från runbooken i utdatafönstret.

## <a name="publishing-a-runbook"></a>Publicera en runbook

När du skapar eller importerar en ny runbook måste du publicera den innan du kan köra den. Varje runbook i Azure Automation har en utkastversion och en publicerad version. Det är bara den Publicerade versionen som kan köras och bara Utkastet som kan redigeras. Den Publicerade versionen påverkas inte av ändringar i Utkastet. När utkastversionen ska göras tillgänglig publicerar du den och skriver över den aktuella publicerade versionen med utkastversionen.

### <a name="publish-a-runbook-in-the-azure-portal"></a>Publicera en runbook i Azure-portalen

1. Öppna runbooken i Azure-portalen.
2. Klicka på **Redigera**.
3. Klicka på **Publicera** och sedan **Ja** som svar på verifieringsmeddelandet.

### <a name="publish-a-runbook-using-powershell"></a>Publicera en runbook med PowerShell

Använd cmdleten [Publish-AzAutomationRunbook](https://docs.microsoft.com/powershell/module/Az.Automation/Publish-AzAutomationRunbook?view=azps-3.5.0) för att publicera en runbook med Windows PowerShell. I följande exempel visas hur du publicerar en exempelkörningsbok.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="scheduling-a-runbook-in-the-azure-portal"></a>Schemalägga en runbook i Azure-portalen

När runbooken har publicerats kan du schemalägga den för drift.

1. Öppna runbooken i Azure-portalen.
2. Välj **Scheman** under **Resurser**.
3. Välj **Lägg till ett schema**.
4. I fönstret Schemakörning väljer du **Länka ett schema till runbooken**.
5. Välj **Skapa ett nytt schema** i fönstret Schema.
6. Ange ett namn, en beskrivning och andra parametrar i fönstret Nytt schema. 
7. När schemat har skapats markerar du det och klickar på **OK**. Det bör nu kopplas till din runbook.
8. Leta efter ett e-postmeddelande i postlådan för att meddela dig om runbook-statusen.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du kan dra nytta av Runbook- och PowerShell-modulgalleriet finns i [Runbook- och modulgallerier för Azure Automation](automation-runbook-gallery.md).
* Mer information om hur du redigerar PowerShell- och PowerShell-arbetsflödeskörningsböcker med en textredigerare finns [i Redigera textkörningsböcker i Azure Automation](automation-edit-textual-runbook.md).
* Mer information om grafisk runbook-redigering finns [i Grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).
