---
title: Skapa eller importera en runbook i Azure Automation
description: Den här artikeln beskriver hur du skapar en ny runbook i Azure Automation eller importera en från en fil.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/15/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: f3d372938a4efb8701ab6916cd391794dbddd1f1
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="creating-or-importing-a-runbook-in-azure-automation"></a>Skapa eller importera en runbook i Azure Automation
Du kan lägga till en runbook i Azure Automation genom att antingen [skapar en ny](#creating-a-new-runbook) eller genom att importera en befintlig runbook från en fil eller från den [Runbook-galleriet](automation-runbook-gallery.md). Den här artikeln innehåller information om hur du skapar och importerar runbooks från en fil.  Du kan hämta alla detaljer om hur du använder community runbooks och moduler i [Azure Automation Runbook- och stänga](automation-runbook-gallery.md).

## <a name="creating-a-new-runbook"></a>Skapa en ny runbook
Du kan skapa en ny runbook i Azure Automation med Azure portaler eller Windows PowerShell. När runbooken har skapats kan du redigera den med hjälp av informationen i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md) och [grafiska redigering i Azure Automation](automation-graphical-authoring-intro.md).

### <a name="to-create-a-new-azure-automation-runbook-with-the-azure-portal"></a>Skapa en ny Azure Automation-runbook med Azure-portalen
1. Öppna ditt Automation-konto på Azure Portal.
2. Hubben, Välj **Runbooks** att öppna listan över runbooks.
3. Klicka på den **lägga till en runbook** knappen och sedan **skapa en ny runbook**.
4. Ange en **namn** för runbook och välj dess [typen](automation-runbook-types.md). Runbook-namnet måste börja med en bokstav och kan innehålla bokstäver, siffror, understreck och bindestreck.
5. Klicka på **skapa** att skapa runbook och öppna redigeraren.

### <a name="to-create-a-new-azure-automation-runbook-with-windows-powershell"></a>Skapa en ny Azure Automation-runbook med Windows PowerShell
Du kan använda den [ny AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt619376.aspx) för att skapa en tom [PowerShell-arbetsflödesrunbook](automation-runbook-types.md#powershell-workflow-runbooks). Du kan antingen ange den **namn** parametern för att skapa en tom runbook som du kan redigera eller du kan ange den **sökväg** parameter för att importera en runbook-fil. Den **typen** parametern ska också tas med att ange någon av de fyra typerna av runbook.

Följande exempelkommandon visar hur du skapar en ny tom runbook.

    New-AzureRmAutomationRunbook -AutomationAccountName MyAccount `
    -Name NewRunbook -ResourceGroupName MyResourceGroup -Type PowerShell

## <a name="importing-a-runbook-from-a-file-into-azure-automation"></a>Importera en runbook från en fil till Azure Automation
Du kan skapa en ny runbook i Azure Automation genom att importera ett PowerShell-skript eller PowerShell-arbetsflöde (.ps1-tillägg), exporterade grafisk runbook (.graphrunbook) eller 2 Python-skript (.py tillägg).  Du måste ange den [typ av runbook](automation-runbook-types.md) som skapas under import, med hänsyn till följande.

* En .graphrunbook-fil kan bara importeras till en ny [grafisk runbook](automation-runbook-types.md#graphical-runbooks), och grafiska runbook-flöden kan endast skapas från en .graphrunbook-fil.
* En .ps1-fil som innehåller ett PowerShell-arbetsflöde kan bara importeras till en [PowerShell-arbetsflödesrunbook](automation-runbook-types.md#powershell-workflow-runbooks).  Om filen innehåller flera PowerShell-arbetsflöden, misslyckas importen. Du måste spara varje arbetsflöde i en egen fil och importera varje separat.
* En .ps1-fil som inte innehåller ett arbetsflöde kan importeras till antingen en [PowerShell-runbook](automation-runbook-types.md#powershell-runbooks) eller en [PowerShell-arbetsflödesrunbook](automation-runbook-types.md#powershell-workflow-runbooks).  Om den har importerats till en PowerShell-arbetsflödesrunbook sedan konverteras det till ett arbetsflöde och kommentarer ingår i denna runbook anger ändringar som gjorts.

### <a name="to-import-a-runbook-from-a-file-with-the-azure-portal"></a>Så här importerar du en runbook från en fil med Azure-portalen
Du kan använda följande procedur för att importera en skriptfil till Azure Automation.  

> [!NOTE]
> Observera att du kan bara importera en .ps1-fil till en PowerShell-arbetsflödesrunbook med hjälp av portalen.
> 
> 

1. Öppna ditt Automation-konto på Azure Portal.
2. Hubben, Välj **Runbooks** att öppna listan över runbooks.
3. Klicka på den **lägga till en runbook** knappen och sedan **importera**.
4. Klicka på **Runbook-filen** att välja filen som ska importeras
5. Om den **namn** fältet aktiveras och sedan har du möjlighet att ändra den.  Runbook-namnet måste börja med en bokstav och kan innehålla bokstäver, siffror, understreck och bindestreck.
6. Den [runbooktyp](automation-runbook-types.md) väljs automatiskt, men du kan ändra typ efter att tillämpliga begränsningarna i beräkningen. 
7. Ny runbook visas i listan över runbooks för Automation-kontot.
8. Du måste [publicera en runbook](#publishing-a-runbook) innan du kan köra den.

> [!NOTE]
> När du har importerat en grafisk runbook eller en grafisk PowerShell-arbetsflödesrunbook har du möjlighet att konvertera till den andra typen om du vill. Du kan inte konvertera till text-runbook.
>  
> 

### <a name="to-import-a-runbook-from-a-script-file-with-windows-powershell"></a>Importera en runbook från en skriptfil med Windows PowerShell
Du kan använda den [importera AzureRMAutomationRunbook](https://msdn.microsoft.com/library/mt603735.aspx) för att importera en skriptfil som utkast PowerShell-arbetsflödesrunbook. Om det finns redan en runbook, importen misslyckas om du inte använder den *-Force* parameter. 

Följande exempelkommandon visar hur du importerar en skriptfil till en runbook.

    $automationAccountName =  "AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $scriptPath = "C:\Runbooks\Sample_TestRunbook.ps1"
    $RGName = "ResourceGroup"

    Import-AzureRMAutomationRunbook -Name $runbookName -Path $scriptPath `
    -ResourceGroupName $RGName -AutomationAccountName $automationAccountName `
    -Type PowerShellWorkflow 


## <a name="publishing-a-runbook"></a>Publicera en runbook
När du skapar eller importera en ny runbook, måste du publicera den innan du kan köra den.  Varje runbook i Automation har ett utkast och en publicerad version. Bara den publicerade versionen är tillgängligt och kan köras och bara utkastet som kan redigeras. Den publicerade versionen påverkas inte av ändringar i utkastet. När utkastet ska göras tillgängligt publicerar du det som skriver över den publicerade versionen med utkastet.

## <a name="to-publish-a-runbook-using-the-azure-portal"></a>Publicera en runbook med hjälp av Azure portal
1. Öppna runbook i Azure-portalen.
2. Klicka på den **redigera** knappen.
3. Klicka på den **publicera** knappen och sedan **Ja** i verifieringsmeddelandet.

## <a name="to-publish-a-runbook-using-windows-powershell"></a>Att publicera en runbook med Windows PowerShell
Du kan använda den [publicera AzureRmAutomationRunbook](https://msdn.microsoft.com/library/mt603705.aspx) för att publicera en runbook med Windows PowerShell. Följande exempelkommandon visar hur du publicerar en exempel-runbook.

    $automationAccountName =  AutomationAccount"
    $runbookName = "Sample_TestRunbook"
    $RGName = "ResourceGroup"

    Publish-AzureRmAutomationRunbook -AutomationAccountName $automationAccountName `
    -Name $runbookName -ResourceGroupName $RGName


## <a name="next-steps"></a>Nästa steg
* Läs om hur du kan dra nytta av Runbook och PowerShell-modulen galleriet i [Azure Automation Runbook- och stänga](automation-runbook-gallery.md)
* Mer information om hur du redigerar PowerShell och PowerShell-arbetsflöde runbooks med en textrepresentation editor finns [redigera textrepresentation runbooks i Azure Automation](automation-edit-textual-runbook.md)
* Mer information om redigering av grafisk runbook finns [grafiska redigering i Azure Automation](automation-graphical-authoring-intro.md)

