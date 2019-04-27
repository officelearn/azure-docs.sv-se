---
title: Runbook- och gallerier för Azure Automation
description: Runbooks och moduler från Microsoft och communityn är tillgängliga för dig att installera och använda i din miljö för Azure Automation.  Den här artikeln beskrivs hur du kan komma åt dessa resurser och för att bidra med dina runbooks i galleriet.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/20/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 20aafc117ad8b6bd625894180fdfe79bd86192bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60737412"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook- och gallerier för Azure Automation

I stället för att skapa egna runbooks och moduler i Azure Automation kan du komma åt scenarier som redan har skapats av Microsoft och communityn.

Du kan hämta PowerShell-runbooks och [moduler](#modules-in-powershell-gallery) från PowerShell-galleriet och [Python runbooks](#python-runbooks) från Script Center-galleriet. Du kan också bidra till gruppen genom att dela scenarier som du utvecklar kan se att lägga till en runbook i galleriet

## <a name="runbooks-in-powershell-gallery"></a>Runbooks i PowerShell-galleriet

Den [PowerShell-galleriet](https://www.powershellgallery.com/packages) erbjuder en mängd olika runbooks från Microsoft och communityn som du kan importera till Azure Automation. Om du vill använda ett, ladda ned en runbook från galleriet eller direkt importera runbooks från galleriet eller från ditt Automation-konto i Azure-portalen.

Du kan bara importera direkt från PowerShell-galleriet med hjälp av Azure portal. Du kan inte utföra den här funktionen med hjälp av PowerShell.

> [!NOTE]
> Du bör kontrollera innehållet i alla runbooks som du får från PowerShell-galleriet och mycket försiktig installera och köra dem i en produktionsmiljö.

### <a name="to-import-a-powershell-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Så här importerar du en PowerShell-runbook från Runbook-galleriet med Azure portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Under **Processautomatisering**, klicka på **runbookgalleri**
3. Välj **källa: PowerShell-galleriet**.
4. Leta upp det Galleriobjekt som du vill och väljer den för att visa dess egenskaper. Du kan ange ytterligare sökparametrar för utgivaren och typen till vänster.

   ![Bläddra i galleri](media/automation-runbook-gallery/browse-gallery.png)

5. Klicka på **visa källprojekt** att visa objektet i den [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Om du vill importera ett objekt, klickar du på den för att visa dess egenskaper och klicka sedan på den **importera** knappen.

   ![Knappen Importera](media/automation-runbook-gallery/gallery-item-detail.png)

7. Du kan också ändra namnet på runbooken och klicka sedan på **OK** att importera runbooken.
8. Runbook visas på den **Runbooks** fliken för Automation-kontot.

### <a name="adding-a-powershell-runbook-to-the-gallery"></a>Att lägga till en PowerShell-runbook i galleriet

Microsoft rekommenderar att du vill lägga till runbooks i PowerShell-galleriet som du tror kan vara användbar för andra kunder. PowerShell-galleriet accepterar PowerShell-moduler och PowerShell-skript. Du kan lägga till en runbook med [överföra den till PowerShell-galleriet](/powershell/gallery/how-to/publishing-packages/publishing-a-package).

> [!NOTE]
> Grafiska runbooks stöds inte i PowerShell-galleriet.

## <a name="modules-in-powershell-gallery"></a>Moduler i PowerShell-galleriet

PowerShell-moduler innehåller cmdletar som du kan använda i dina runbooks och befintliga moduler som du kan installera i Azure Automation är tillgängliga i den [PowerShell-galleriet](https://www.powershellgallery.com). Du kan starta det här galleriet från Azure-portalen och installera dem direkt i Azure Automation. Du kan även hämta dem och installera dem manuellt.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Importera en modul från galleriet för automatisering av modulen med Azure portal

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **moduler** under **delade resurser** att öppna listan över moduler.
3. Klicka på **bläddringsgalleriegenskapen** högst upp på sidan.

   ![Modulgalleriet](media/automation-runbook-gallery/modules-blade.png)

4. På den **bläddringsgalleriegenskapen** sidan som du kan söka efter följande fält:

   * Modulnamn
   * Taggar
   * Författare
   * Cmdlet/DSC-resursnamn

5. Hitta en modul som du är intresserad av och markera den för att visa dess egenskaper.  

   När du ökar detaljnivån i en specifik modul, kan du visa mer information. Den här informationen innehåller en länk tillbaka till PowerShell-galleriet alla obligatoriska beroenden och alla cmdletar eller DSC-resurser som innehåller modulen.

   ![Information om PowerShell-modulen](media/automation-runbook-gallery/gallery-item-details-blade.png)

6. För att installera modulen direkt till Azure Automation, klickar du på den **Import** knappen.
7. När du klickar på knappen Importera på den **importera** fönstret du ser Modulnamn som du håller på att importera. Om alla beroenden är installerade på **OK** knappen aktiveras. Om du saknar beroenden, måste du importera dessa beroenden innan du kan importera den här modulen.
8. På den **importera** klickar du på **OK** att importera modulen. Medan Azure Automation importerar en modul till ditt konto, extraherar metadata om modulen och cmdletar. Den här åtgärden kan ta några minuter eftersom varje aktivitet måste ska extraheras.
9. Du får ett inledande meddelande om att modulen distribueras och ett nytt meddelande när den har slutförts.
10. Du kan se de tillgängliga aktiviteterna när modulen har importerats. Du kan använda dess resurser i dina runbooks och Desired State Configuration.

> [!NOTE]
> Moduler som har endast stöd för PowerShell core stöds inte i Azure Automation och kan inte importeras i Azure-portalen eller distribueras direkt från PowerShell-galleriet.

## <a name="python-runbooks"></a>Python Runbooks

Python-Runbooks är tillgängliga i den [Script Center galleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Du kan bidra Python runbooks i Script Center-galleriet genom att klicka på **ladda upp ett bidrag**. När du, se till att du lägger till taggen **Python** när du laddar upp ditt bidrag.

> [!NOTE]
> För att överföra innehåll till [Script Center](https://gallery.technet.microsoft.com/scriptcenter) minst 100 punkter krävs. 

## <a name="requesting-a-runbook-or-module"></a>Begär en runbook eller en modul

Du kan skicka begäranden till [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Om du behöver hjälp med att skriva en runbook eller har en fråga om PowerShell, ställa en fråga till vår [forum](https://social.msdn.microsoft.com/Forums/windowsazure/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Nästa steg

* Kom igång med runbooks, se [hantera runbook i Azure Automation](manage-runbooks.md)
* Information om skillnaderna mellan PowerShell och PowerShell-arbetsflöde med runbooks finns i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md)
