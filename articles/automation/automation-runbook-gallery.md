---
title: Runbook- och modulgallerier för Azure Automation
description: Runbooks och moduler från Microsoft och communityn är tillgängliga för dig att installera och använda i din Azure Automation-miljö.  I den här artikeln beskrivs hur du kan komma åt dessa resurser och bidra med dina runbooks till galleriet.
services: automation
ms.subservice: process-automation
ms.date: 03/20/2019
ms.topic: conceptual
ms.openlocfilehash: 90b475e275598363314c8f131911fe12650cd3df
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535561"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook- och modulgallerier för Azure Automation

I stället för att skapa egna runbooks och moduler i Azure Automation kan du komma åt scenarier som redan har skapats av Microsoft och communityn. Du kan hämta PowerShell-runbooks [och-moduler](#modules-in-powershell-gallery) från PowerShell Gallery- och [Python-runbooks](#use-python-runbooks) från Skriptcentergalleriet. Du kan också bidra till communityn genom att dela [scenarier som du utvecklar.](#add-a-powershell-runbook-to-the-gallery) 

## <a name="runbooks-in-powershell-gallery"></a>Runbooks i PowerShell-galleriet

[PowerShell-galleriet](https://www.powershellgallery.com/packages) innehåller en mängd olika runbooks från Microsoft och den grupp som du kan importera till Azure Automation. Om du vill använda en kan du hämta en runbook från galleriet eller importera runbooks direkt från galleriet eller från ditt Automation-konto i Azure-portalen.

> [!NOTE]
> Grafiska runbooks stöds inte i PowerShell Gallery.

Du kan bara importera direkt från PowerShell-galleriet med Azure-portalen. Du kan inte utföra den här funktionen med PowerShell.

> [!NOTE]
> Du bör validera innehållet i alla runbooks som du får från PowerShell Gallery och vara mycket försiktig när du installerar och kör dem i en produktionsmiljö.

## <a name="modules-in-powershell-gallery"></a>Moduler i PowerShell-galleriet

PowerShell-moduler innehåller cmdlets som du kan använda i dina runbooks och befintliga moduler som du kan installera i Azure Automation är tillgängliga i [PowerShell-galleriet](https://www.powershellgallery.com). Du kan starta det här galleriet från Azure-portalen och installera dem direkt i Azure Automation. Du kan också ladda ner dem och installera dem manuellt.

## <a name="common-solutions-available-in-powershell-gallery"></a>Vanliga lösningar som finns i PowerShell-galleriet

Listan nedan innehåller några runbooks som ger lösningar på vanliga scenarier. En fullständig lista över runbooks som skapats av Azure Automation-teamet finns i [AzureAutomationTeam-profilen](https://www.powershellgallery.com/profiles/AzureAutomationTeam).

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - Importerar den senaste versionen av alla moduler i ett Automation-konto från PowerShell Gallery.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Konfigurerar Azure Diagnostics och Log Analytics för att ta emot Azure Automation-loggar som innehåller jobbstatus och jobbströmmar.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Kopierar en fjärrfil från en virtuell Windows Azure-dator.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Kopierar en lokal fil till en virtuell Azure-dator.

## <a name="import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importera en PowerShell-runbook från runbook-galleriet med Azure-portalen

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Runbooks-galleri** under **ProcessAutomation**.
3. Välj **Källa: PowerShell-galleriet**.
4. Leta reda på det galleriobjekt du vill använda och markera det för att visa dess information. Till vänster kan du ange ytterligare sökparametrar för utgivaren och typen.

   ![Bläddra i galleriet](media/automation-runbook-gallery/browse-gallery.png)

5. Klicka på **Visa källprojekt** om du vill visa objektet i [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Om du vill importera ett objekt klickar du på det för att visa dess information och klickar sedan på **Importera**.

   ![Knappen Importera](media/automation-runbook-gallery/gallery-item-detail.png)

7. Du kan också ändra namnet på runbooken och sedan klicka på **OK** för att importera runbooken.
8. Runbooken visas på fliken **Runbooks** för Automation-kontot.

## <a name="add-a-powershell-runbook-to-the-gallery"></a>Lägga till en PowerShell-runbook i galleriet

Microsoft uppmuntrar dig att lägga till runbooks i PowerShell Gallery som du tror skulle vara användbart för andra kunder. PowerShell-galleriet accepterar PowerShell-moduler och PowerShell-skript. Du kan lägga till en runbook genom [att ladda upp den i PowerShell Gallery](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package).

## <a name="import-a-module-from-the-module-gallery-with-the-azure-portal"></a>Importera en modul från modulgalleriet med Azure-portalen

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Moduler** under **Delade resurser** för att öppna listan över moduler.
3. Klicka på **Bläddra i galleriet** högst upp på sidan.

   ![Galleri för modul](media/automation-runbook-gallery/modules-blade.png)

4. På sidan Bläddra i galleri kan du söka efter följande fält:

   * Modulnamn
   * Taggar
   * Författare
   * Namn på Cmdlet/DSC-resurs

5. Leta reda på en modul som du är intresserad av och välj den för att visa dess information.

   När du detaljgransar till en viss modul kan du visa mer information. Den här informationen innehåller en länk tillbaka till PowerShell-galleriet, eventuella nödvändiga beroenden och alla cmdlets- eller DSC-resurser som modulen innehåller.

   ![Information om PowerShell-modul](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. Om du vill installera modulen direkt i Azure Automation klickar du på **Importera**.
7. I fönstret Importera kan du se namnet på den modul som ska importeras. Om alla beroenden är **OK** installerade aktiveras OK-knappen. Om du saknar beroenden måste du importera dessa beroenden innan du kan importera den här modulen.
8. Importera modulen genom att klicka på **OK** i importfönstret. Medan Azure Automation importerar en modul till ditt konto extraheras metadata om modulen och cmdlets. Den här åtgärden kan ta ett par minuter eftersom varje aktivitet måste extraheras.
9. Du får ett första meddelande om att modulen distribueras och ett annat meddelande när den har slutförts.
10. När modulen har importerats kan du se tillgängliga aktiviteter. Du kan använda modulresurser i runbooks och DSC-resurser.

> [!NOTE]
> Moduler som bara stöder PowerShell-kärna stöds inte i Azure Automation och kan inte importeras i Azure-portalen eller distribueras direkt från PowerShell-galleriet.

## <a name="use-python-runbooks"></a>Använda Python-runbooks

Python Runbooks finns i [galleriet Skriptcenter](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Du kan bidra med Python-runbooks till skriptcentergalleriet genom att klicka på **Ladda upp ett bidrag**. När du gör det lägger `Python` du till taggen när du laddar upp ditt bidrag.

> [!NOTE]
> Om du vill ladda upp innehåll till [Skriptcenter](https://gallery.technet.microsoft.com/scriptcenter)behöver du minst 100 poäng.

## <a name="request-a-runbook-or-module"></a>Begära en runbook eller modul

Du kan skicka förfrågningar till [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Om du behöver hjälp med att skriva en runbook eller har en fråga om PowerShell, skicka en fråga till vårt [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Nästa steg

* Information om hur du kommer igång med runbooks finns [i Hantera runbook i Azure Automation](manage-runbooks.md).
* Information om skillnaderna mellan PowerShell och PowerShell-arbetsflöde med runbooks finns i [PowerShell-arbetsflödet](automation-powershell-workflow.md)för inlärning .
* Mer information om PowerShell, inklusive språkreferens- och utbildningsmoduler, finns i [PowerShell Docs](https://docs.microsoft.com/powershell/scripting/overview).
