---
title: Hantera runbooks i Azure Automation
description: Den här artikeln beskriver hur du hanterar runbooks i Azure Automation.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 02/14/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 5bb52e0547ed9bc18d67370ffb9db35942212aab
ms.sourcegitcommit: 24906eb0a6621dfa470cb052a800c4d4fae02787
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2019
ms.locfileid: "56887598"
---
# <a name="manage-runbooks-in-azure-automation"></a>Hantera runbooks i Azure Automation

Du kan lägga till en runbook i Azure Automation genom att antingen [skapar en ny](#create-a-runbook) eller genom att importera en befintlig runbook från en fil eller [Runbook-galleriet](automation-runbook-gallery.md). Den här artikeln innehåller information om att skapa och importera runbooks från en fil.  Du kan hämta alla detaljer om hur du använder community runbooks och moduler i [Runbook- och gallerier för Azure Automation](automation-runbook-gallery.md).

## <a name="create-a-runbook"></a>Skapa en runbook

Du kan skapa en ny runbook i Azure Automation med Azure-portalerna eller Windows PowerShell. När runbooken har skapats kan du redigera den med hjälp av informationen i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md) och [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

### <a name="create-a-runbook-in-the-azure-portal"></a>Skapa en runbook i Azure portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Från hubben, väljer **Runbooks** att öppna listan med runbooks.
3. Klicka på den **Lägg till en runbook** knappen och sedan **skapa en ny runbook**.
4. Ange ett **namn** för runbook och välj dess [typ](automation-runbook-types.md). Runbook-namn måste börja med en bokstav och kan ha bokstäver, siffror, understreck och bindestreck.
5. Klicka på **skapa** att skapa runbooken och öppna redigeraren.

### <a name="create-a-runbook-with-powershell"></a>Skapa en runbook med PowerShell

Du kan använda den [New-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/new-azurermautomationrunbook) cmdlet för att skapa en tom [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Använd den **typ** parametern för att ange en av de fyra typerna av runbooks.

Följande exempelkommandon visar hur du skapar en ny tom runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="import-a-runbook"></a>Importera en runbook

Du kan skapa en ny runbook i Azure Automation genom att importera ett PowerShell-skript eller PowerShell-arbetsflöde (.ps1-tillägg), ett exporterade grafisk runbook (.graphrunbook) eller ett Python 2-skript (.py-filtillägg).  Du måste ange den [typ av runbook](automation-runbook-types.md) som skapas under importen, med hänsyn till följande överväganden.

* En `.graphrunbook` filen kan bara importeras till en ny [grafisk runbook](automation-runbook-types.md#graphical-runbooks), och grafiska runbooks kan endast skapas från en `.graphrunbook` fil.
* En `.ps1` -fil som innehåller ett PowerShell-arbetsflöde kan bara importeras till en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Om filen innehåller flera PowerShell-arbetsflöden, misslyckas importen. Du måste spara varje arbetsflöde i en egen fil och importera var och en separat.
* En `.ps1` fil som inte innehåller ett arbetsflöde kan importeras till antingen en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Om den har importerats till en PowerShell Workflow-runbook, sedan konverteras till ett arbetsflöde och kommentarer ingår i denna runbook anger ändringar som gjorts.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Så här importerar du en runbook från en fil med Azure portal

Du kan använda följande procedur för att importera en skriptfil till Azure Automation.  

> [!NOTE]
> Observera att du bara kan importera en .ps1-fil till en PowerShell Workflow-runbook med hjälp av portalen.

1. Öppna ditt Automation-konto på Azure Portal.
2. Från hubben, väljer **Runbooks** att öppna listan med runbooks.
3. Klicka på den **Lägg till en runbook** knappen och sedan **Import**.
4. Klicka på **Runbook-filen** att välja filen som ska importeras
5. Om den **namn** fält är aktiverad och har möjlighet att ändra den.  Runbook-namn måste börja med en bokstav och kan ha bokstäver, siffror, understreck och bindestreck.
6. Den [runbooktyp](automation-runbook-types.md) väljs automatiskt, men du kan ändra typen efter att begränsningarna som gäller för hänsyn. 
7. Ny runbook visas i listan med runbookflöden för Automation-kontot.
8. Du måste [publicera runbooken](#publish-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk runbook eller en grafisk PowerShell workflow-runbook har möjlighet att konvertera till den andra typen om du vill. Du kan inte konvertera till en text runbook.

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Så här importerar du en runbook från en skriptfil med Windows PowerShell

Du kan använda den [Import-AzureRMAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/import-azurermautomationrunbook) att importera en skriptfil som ett utkast PowerShell Workflow-runbook. Om det finns redan en runbook, importen misslyckas om du inte använder den *-Force* parametern.

Följande exempelkommandon visar hur du importerar en skriptfil till en runbook.

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

När du testar en runbook i [utkastet](#publish-a-runbook) körs och alla åtgärder som den utför slutförs. Ingen jobbhistorik skapas, men [utdata](automation-runbook-output-and-messages.md#output-stream) och [varnings- och](automation-runbook-output-and-messages.md#message-streams) strömmar visas i testet utdata fönstret. Meddelanden till den [utförlig Stream](automation-runbook-output-and-messages.md#message-streams) visas i utdatafönstret endast om den [variabeln $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) är inställd på Fortsätt.

Även om utkastet körs körs normalt fortfarande i runbook och utför alla åtgärder mot resurser i miljön. Därför bör du bara testa runbooks på icke-produktionsresurser.

Proceduren för att testa varje [typ av runbook](automation-runbook-types.md) är samma och det är ingen skillnad i testet mellan textredigeraren och den grafiska redigeraren i Azure-portalen.  

1. Öppnar du Utkastversionen av runbooken i antingen den [textredigeraren](automation-edit-textual-runbook.md) eller [grafiska redigeraren](automation-graphical-authoring-intro.md).
1. Klicka på den **Test** knappen för att öppna sidan Test.
1. Om runbooken har parametrar visas de i det vänstra fönstret där du kan ange värden som ska användas för testet.
1. Om du vill köra test en [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md), ändra **inställningar** till **Hybrid Worker** och välj namnet på målgruppen.  Annars behåller du standardvärdet **Azure** att köra testet i molnet.
1. Klicka på den **starta** för att starta testet.
1. Om runbook är [PowerShell Workflow](automation-runbook-types.md#powershell-workflow-runbooks) eller [grafisk](automation-runbook-types.md#graphical-runbooks), du kan stoppa eller inaktivera den när den testas med hjälp av knapparna under Utdatarutan. När du pausar runbooken slutförs den aktuella aktiviteten innan den pausas. När runbooken har pausats kan du stoppa den eller starta om den.
1. Granska utdata från runbooken i utdatarutan.

## <a name="publish-a-runbook"></a>Publicera en runbook

När du skapar eller importera en ny runbook, måste du publicera den innan du kan köra den.  Varje runbook i Automation har ett utkast och publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När utkastet ska göras tillgängliga, sedan publicerar du det och som skriver över den publicerade versionen med utkastversionen.

### <a name="azure-portal"></a>Azure Portal

1. Öppna runbook i Azure-portalen.
2. Klicka på den **redigera** knappen.
3. Klicka på den **publicera** knappen och sedan **Ja** i verifieringsmeddelandet.

### <a name="powershell"></a>PowerShell

Du kan använda den [publicera-AzureRmAutomationRunbook](/powershell/module/azurerm.automation/publish-azurermautomationrunbook) cmdlet för att publicera en runbook med Windows PowerShell. Följande exempelkommandon visar hur du publicerar en exempel-runbook.

```azurepowershell-interactive
$automationAccountName =  "AutomationAccount"
$runbookName = "Sample_TestRunbook"
$RGName = "ResourceGroup"

Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
-Name $runbookName -ResourceGroupName $RGName
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du kan dra nytta av Runbook- och PowerShell-modulen-galleriet, i [Runbook- och gallerier för Azure Automation](automation-runbook-gallery.md)
* Mer information om hur du redigerar PowerShell och PowerShell Workflow-runbooks med en textredigeraren finns [redigera runbooks med text i Azure Automation](automation-edit-textual-runbook.md)
* Läs mer om grafisk runbook-redigering i [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md)
