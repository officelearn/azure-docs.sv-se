---
title: Runbook-och modul-gallerier för Azure Automation
description: Runbooks och moduler från Microsoft och communityn är tillgängliga så att du kan installera och använda i din Azure Automation-miljö.  I den här artikeln beskrivs hur du kan komma åt dessa resurser och bidra med Runbooks till galleriet.
services: automation
ms.service: automation
ms.subservice: process-automation
author: bobbytreed
ms.author: robreed
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 86eebf33f870780871e4c873936e491772c73b63
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231615"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook-och modul-gallerier för Azure Automation

I stället för att skapa egna runbooks och moduler i Azure Automation kan du komma åt scenarier som redan har skapats av Microsoft och communityn.

Du kan hämta PowerShell-Runbooks och [moduler](#modules-in-powershell-gallery) från PowerShell-galleriet-och [python-Runbooks](#python-runbooks) från Script Center-galleriet. Du kan också bidra till communityn genom att dela scenarier som du utvecklar, se lägga till en Runbook i galleriet

## <a name="runbooks-in-powershell-gallery"></a>Runbooks i PowerShell-galleriet

[PowerShell-galleriet](https://www.powershellgallery.com/packages) tillhandahåller en mängd olika Runbooks från Microsoft och communityn som du kan importera till Azure Automation. Om du vill använda en, laddar du ned en Runbook från galleriet eller så kan du importera runbooks direkt från galleriet eller från ditt Automation-konto i Azure Portal.

Du kan bara importera direkt från PowerShell-galleriet med hjälp av Azure Portal. Du kan inte utföra den här funktionen med PowerShell.

> [!NOTE]
> Du bör kontrol lera innehållet i alla Runbooks som du får från PowerShell-galleriet och använda yttersta försiktighet i att installera och köra dem i en produktions miljö.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Så här importerar du en PowerShell-Runbook från Runbook-galleriet med Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Under **process automatisering**klickar du på **Galleri för Runbooks**
3. Välj **Källa: PowerShell-galleriet**.
4. Leta upp det Galleri objekt som du vill använda och markera det för att visa dess information. Till vänster kan du ange ytterligare Sök parametrar för utgivaren och typen.

   ![Bläddra i galleriet](media/automation-runbook-gallery/browse-gallery.png)

5. Klicka på **Visa käll projekt** om du vill visa objektet i [TechNets skript Center](https://gallery.technet.microsoft.com/).
6. Om du vill importera ett objekt klickar du på det för att visa information om det och klickar sedan på knappen **Importera** .

   ![Knappen Importera](media/automation-runbook-gallery/gallery-item-detail.png)

7. Du kan också ändra namnet på runbooken och sedan klicka på **OK** för att importera runbooken.
8. Runbooken visas på fliken **Runbooks** för Automation-kontot.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Lägga till en PowerShell-Runbook i galleriet

Microsoft uppmuntrar dig att lägga till Runbooks i de PowerShell-galleriet som du tror kan vara användbara för andra kunder. PowerShell-galleriet accepterar PowerShell-moduler och PowerShell-skript. Du kan lägga till en Runbook genom [att ladda upp den till PowerShell-galleriet](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafiska runbooks stöds inte i PowerShell-galleriet.

## <a name="modules-in-powershell-gallery"></a>Moduler i PowerShell-galleriet

PowerShell-moduler innehåller cmdletar som du kan använda i dina runbooks och befintliga moduler som du kan installera i Azure Automation är tillgängliga i [PowerShell-galleriet](https://www.powershellgallery.com). Du kan starta galleriet från Azure Portal och installera dem direkt i Azure Automation. Du kan också hämta dem och installera dem manuellt.

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Så här importerar du en modul från Automation module-galleriet med Azure Portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **moduler** under **delade resurser** för att öppna listan över moduler.
3. Klicka på **Bläddra i galleriet** överst på sidan.

   ![Modul Galleri](media/automation-runbook-gallery/modules-blade.png)

4. På sidan **Bläddra i galleriet** kan du söka efter följande fält:

   * Modulnamn
   * Taggar
   * Författare
   * Namn på cmdlet/DSC-resurs

5. Leta upp en modul som du är intresse rad av och välj den för att visa information om den.

   När du ökar detalj nivån i en speciell modul kan du Visa mer information. Den här informationen innehåller en länk tillbaka till PowerShell-galleriet, eventuella nödvändiga beroenden och alla cmdletar eller DSC-resurser som modulen innehåller.

   ![Information om PowerShell-modul](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Klicka på knappen **Importera** för att installera modulen direkt i Azure Automation.
7. När du klickar på knappen Importera visas namnet på den modul som du håller på att importera i fönstret **Importera** . Om alla beroenden är installerade aktive ras knappen **OK** . Om du saknar beroenden måste du importera dessa beroenden innan du kan importera den här modulen.
8. På sidan **Importera** klickar du på **OK** för att importera modulen. Medan Azure Automation importerar en modul till ditt konto extraheras metadata om modulen och cmdletarna. Den här åtgärden kan ta några minuter eftersom varje aktivitet behöver extraheras.
9. Du får ett första meddelande om att modulen har distribuerats och en annan avisering när den har slutförts.
10. När modulen har importer ATS kan du se tillgängliga aktiviteter. Du kan använda dess resurser i dina runbooks och önskad tillstånds konfiguration.

> [!NOTE]
> Moduler som bara stöder PowerShell-kärnan stöds inte i Azure Automation och kan inte importeras i Azure Portal eller distribueras direkt från PowerShell-galleriet.

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks är tillgängliga i [Script Center-galleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Du kan bidra med python-Runbooks till Script Center-galleriet genom att klicka på **överför ett bidrag**. När du gör det, se till att du lägger till taggen **python** när du laddar upp ditt bidrag.

> [!NOTE]
> För att ladda upp innehåll till [skript Center](https://gallery.technet.microsoft.com/scriptcenter) krävs minst 100 punkter.

## <a name="requesting-a-runbook-or-module"></a>Begär en Runbook eller modul

Du kan skicka förfrågningar till [användar rösten](https://feedback.azure.com/forums/246290-azure-automation/).  Om du behöver hjälp med att skriva en Runbook eller om du har en fråga om PowerShell, så publicera en fråga i vårt [Forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="common-solutions-available-in-the-runbook-gallery"></a>Vanliga lösningar som är tillgängliga i Runbook-galleriet

Listan nedan innehåller några Runbooks som innehåller lösningar på vanliga scenarier. En fullständig lista över Runbooks som skapats av Azure Automation-teamet finns i [AzureAutomationTeam Profile](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

* [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) – importerar den senaste versionen på PowerShell-galleriet av alla moduler i ett Automation-konto.
* [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) – det här skriptet konfigurerar Azure-diagnostik och Log Analytics att ta emot Azure Automation loggar som innehåller jobb status och jobb strömmar.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) – denna Runbook kopierar en fjärrfil från en virtuell Windows Azure-dator.
* [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) – denna Runbook kopierar en lokal fil till en virtuell Azure-dator.

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med Runbooks finns [i hantera Runbook i Azure Automation](manage-runbooks.md)
* Information om skillnaderna mellan PowerShell-och PowerShell-arbetsflöde med Runbooks finns i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md)
* Mer information om PowerShell, inklusive språk referens-och inlärnings moduler finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/overview).
