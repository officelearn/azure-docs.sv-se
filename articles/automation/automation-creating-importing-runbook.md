---
title: Skapa eller importera en runbook i Azure Automation
description: Den här artikeln beskriver hur du skapar en ny runbook i Azure Automation eller importera en från en fil.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 08/06/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fdc064ab2b74424ce1e4e163c8843bfebc28bcf4
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435574"
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Skapa eller importera en runbook i Azure Automation

Du kan lägga till en runbook i Azure Automation genom att antingen [skapar en ny](#creating-a-new-runbook) eller genom att importera en befintlig runbook från en fil eller från den [Runbook-galleriet](automation-runbook-gallery.md). Den här artikeln innehåller information om att skapa och importera runbooks från en fil.  Du kan hämta alla detaljer om hur du använder community runbooks och moduler i [Runbook- och gallerier för Azure Automation](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Skapa en ny runbook

Du kan skapa en ny runbook i Azure Automation med Azure-portalerna eller Windows PowerShell. När runbooken har skapats kan du redigera den med hjälp av informationen i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md) och [grafisk redigering i Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Skapa en ny Azure Automation-runbook med Azure portal

1. Öppna ditt Automation-konto på Azure Portal.
1. Från hubben, väljer **Runbooks** att öppna listan med runbooks.
1. Klicka på den **Lägg till en runbook** knappen och sedan **skapa en ny runbook**.
1. Ange ett **namn** för runbook och välj dess [typ](automation-runbook-types.md). Runbook-namn måste börja med en bokstav och kan ha bokstäver, siffror, understreck och bindestreck.
1. Klicka på **skapa** att skapa runbooken och öppna redigeraren.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Att skapa en ny Azure Automation-runbook med Windows PowerShell
Du kan använda den [New-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/new-azurermautomationrunbook) cmdlet för att skapa en tom [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks). Den **typ** parametern bör också ingå att ange någon av de fyra typerna av runbooks.

Följande exempelkommandon visar hur du skapar en ny tom runbook.

```azurepowershell-interactive
New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
-Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell
```

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importera en runbook från en fil till Azure Automation

Du kan skapa en ny runbook i Azure Automation genom att importera ett PowerShell-skript eller PowerShell-arbetsflöde (.ps1-tillägg), ett exporterade grafisk runbook (.graphrunbook) eller ett Python 2-skript (.py-filtillägg).  Du måste ange den [typ av runbook](automation-runbook-types.md) som skapas under importen, med hänsyn till följande överväganden.

* En .graphrunbook-fil kan bara importeras till en ny [grafisk runbook](automation-runbook-types.md#graphical-runbooks), och grafiska runbooks kan endast skapas från en .graphrunbook-fil.
* En .ps1-fil som innehåller ett PowerShell-arbetsflöde kan bara importeras till en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Om filen innehåller flera PowerShell-arbetsflöden, misslyckas importen. Du måste spara varje arbetsflöde i en egen fil och importera var och en separat.
* En .ps1-fil som inte innehåller ett arbetsflöde kan importeras till antingen en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell Workflow-runbook](automation-runbook-types.md#powershell-workflow-runbooks).  Om den har importerats till en PowerShell Workflow-runbook, sedan konverteras till ett arbetsflöde och kommentarer ingår i denna runbook anger ändringar som gjorts.

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
8. Du måste [publicera runbooken](#publishing-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk runbook eller en grafisk PowerShell workflow-runbook har möjlighet att konvertera till den andra typen om du vill. Du kan inte konvertera till en text runbook.
>  
> 

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

## <a name="publishing-a-runbook"></a>Publicera en runbook

När du skapar eller importera en ny runbook, måste du publicera den innan du kan köra den.  Varje runbook i Automation har ett utkast och publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När utkastet ska göras tillgängliga, sedan publicerar du det och som skriver över den publicerade versionen med utkastversionen.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Publicera en runbook med hjälp av Azure portal

1. Öppna runbook i Azure-portalen.
1. Klicka på den **redigera** knappen.
1. Klicka på den **publicera** knappen och sedan **Ja** i verifieringsmeddelandet.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Publicera en runbook med Windows PowerShell
Du kan använda den [publicera-AzureRmAutomationRunbook](https://docs.microsoft.com/powershell/module/azurerm.automation/publish-azurermautomationrunbook) cmdlet för att publicera en runbook med Windows PowerShell. Följande exempelkommandon visar hur du publicerar en exempel-runbook.

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
