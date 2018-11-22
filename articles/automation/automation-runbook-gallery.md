---
title: Runbook- och gallerier för Azure Automation
description: Runbooks och moduler från Microsoft och communityn är tillgängliga för dig att installera och använda i din miljö för Azure Automation.  Den här artikeln beskrivs hur du kan komma åt dessa resurser och för att bidra med dina runbooks i galleriet.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 09/11/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: a518d00a50c0fc6ec83626852f30f739d64b18ea
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52283305"
---
# <a name="runbook-and-module-galleries-for-azure-automation"></a>Runbook- och gallerier för Azure Automation
I stället för att skapa egna runbooks och moduler i Azure Automation kan du komma åt en mängd olika scenarier som redan har skapats av Microsoft och communityn.  Du kan antingen använda de här scenarierna utan ändringar eller du kan använda dem som en startpunkt och redigera dem efter behov.

Du kan hämta runbooks från den [Runbook-galleriet](#runbooks-in-runbook-gallery) och moduler från den [PowerShell-galleriet](#modules-in-powerShell-gallery).  Du kan också bidra till gruppen genom att dela scenarier som du utvecklar, se [att lägga till en runbook i galleriet](automation-runbook-gallery.md#adding-a-runbook-to-the-runbook-gallery)

## <a name="runbooks-in-runbook-gallery"></a>Runbooks i Runbook-galleriet
Den [Runbook-galleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f\[0\].Type=RootCategory&f\[0\].Value=WindowsAzure&f\[1\].Type=SubCategory&f\[1\].Value=WindowsAzure_automation&f\[1\].Text=Automation) erbjuder en mängd olika runbooks från Microsoft och communityn som du kan importera till Azure Automation. Du kan antingen hämta en runbook från galleriet, som ligger i den [TechNet Script Center](https://gallery.technet.microsoft.com/scriptcenter/site/upload), eller direkt kan du importera runbooks från galleriet i Azure-portalen.

Du kan bara importera direkt från Runbook-galleriet med hjälp av Azure portal. Du kan inte utföra den här funktionen med hjälp av Windows PowerShell.

> [!NOTE]
> Du bör kontrollera innehållet i runbooks som du får från Runbook-galleriet och mycket försiktig installera och köra dem i en produktionsmiljö.
> 
> 

### <a name="to-import-a-runbook-from-the-runbook-gallery-with-the-azure-portal"></a>Importera en runbook från Runbook-galleriet med Azure portal
1. Öppna ditt Automation-konto på Azure Portal.
2. Under **Processautomatisering**, klicka på **runbookgalleri**
3. Leta upp det Galleriobjekt som du vill och väljer den för att visa dess egenskaper. Du kan ange ytterligare sökparametrar för utgivaren och typen till vänster.
   
    ![Bläddra i galleri](media/automation-runbook-gallery/browse-gallery.png)
5. Klicka på **visa källprojekt** att visa objektet i den [TechNet Script Center](https://gallery.technet.microsoft.com/).
6. Om du vill importera ett objekt, klickar du på den för att visa dess egenskaper och klicka sedan på den **importera** knappen.
   
    ![Knappen Importera](media/automation-runbook-gallery/gallery-item-detail.png)
7. Du kan också ändra namnet på runbooken och klicka sedan på **OK** att importera runbooken.
8. Runbook visas på den **Runbooks** fliken för Automation-kontot.

### <a name="adding-a-runbook-to-the-runbook-gallery"></a>Att lägga till en runbook till runbook-galleriet
Microsoft rekommenderar att du vill lägga till runbooks i Runbook-galleriet som du tror kan vara användbar för andra kunder.  Du kan lägga till en runbook med [överföra den till Script Center](https://gallery.technet.microsoft.com/site/upload) med hänsyn till följande information.

* Du måste ange *Windows Azure* för den **kategori** och *Automation* för den **underkategori** för den runbook som ska visas i den guiden.  
* Överföringen måste vara en enskild .ps1 eller .graphrunbook-fil.  Om runbook kräver alla moduler, underordnade runbooks och tillgångar, ska du visa dem i beskrivningen av överföringen och i avsnittet med kommentarer om runbook.  Om du har ett scenario som kräver flera runbooks kan ladda upp varje separat och lista med namn på relaterade runbooks i var och en av deras beskrivningar. Se till att du använder samma taggar så att de visas i samma kategori. En användare måste läsa beskrivning om du vill veta att andra runbooks är nödvändiga scenariot ska fungera.
* Lägg till tagg ”GraphicalPS” om du publicerar en **grafisk runbook** (inte ett grafiskt arbetsflöde). 
* Infoga en PowerShell- eller PowerShell-arbetsflöde kodfragmentet i beskrivning med **Infoga avsnitt med exempelkod** ikon.
* Sammanfattning för överföringen visas i resultaten för Runbook-galleriet så att du ska ange detaljerad information som hjälper till att en användare som identifierar funktionerna i runbook.
* Du bör tilldela en till tre av följande taggar om du till överföringen.  Runbook visas i guiden under de kategorier som matchar dess taggar.  Taggar som inte finns i den här listan ignoreras av guiden. Om du inte anger någon matchande taggar som visas runbook under kategorin andra.
  
  * Backup
  * Kapacitetshantering
  * Ändringshantering
  * Efterlevnad
  * Dev / Test-miljöer
  * Haveriberedskap
  * Övervakning
  * Uppdatering
  * Etablering
  * Åtgärd
  * Livscykelhantering för virtuell dator
* Automation uppdaterar galleriet en gång i timmen, så att du inte se dina bidrag omedelbart.

## <a name="modules-in-powershell-gallery"></a>Moduler i PowerShell-galleriet
PowerShell-moduler innehåller cmdletar som du kan använda i dina runbooks och befintliga moduler som du kan installera i Azure Automation är tillgängliga i den [PowerShell-galleriet](https://www.powershellgallery.com).  Du kan starta det här galleriet från Azure-portalen och installera dem direkt i Azure Automation, eller du kan hämta dem och installera dem manuellt.  

### <a name="to-import-a-module-from-the-automation-module-gallery-with-the-azure-portal"></a>Importera en modul från galleriet för automatisering av modulen med Azure portal
1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **moduler** under **delade resurser** att öppna listan över moduler.
4. Klicka på **bläddringsgalleriegenskapen** högst upp på sidan.
   
    ![Modulgalleriet](media/automation-runbook-gallery/modules-blade.png) <br>
5. På den **bläddringsgalleriegenskapen** sidan som du kan söka efter följande fält:
   
   * Modulnamn
   * Taggar
   * Författare
   * Cmdlet/DSC-resursnamn
6. Hitta en modul som du är intresserad av och markera den för att visa dess egenskaper.  
   När du ökar detaljnivån i en specifik modul, kan du visa mer information om modulen, inklusive en länk tillbaka till PowerShell-galleriet alla obligatoriska beroenden och alla cmdletar och/eller DSC-resurser som innehåller modulen.
   
    ![Information om PowerShell-modulen](media/automation-runbook-gallery/gallery-item-details-blade.png) <br>
7. För att installera modulen direkt till Azure Automation, klickar du på den **Import** knappen.
8. När du klickar på knappen Importera på den **importera** fönstret du ser Modulnamn som du tänker importera. Om alla beroenden är installerade på **OK** knappen aktiveras. Om du saknar beroenden, måste du importera dem innan du kan importera den här modulen.
9. På den **importera** klickar du på **OK** att importera modulen. Medan Azure Automation importerar en modul till ditt konto, extraherar metadata om modulen och cmdletar. Det kan ta några minuter eftersom varje aktivitet måste ska extraheras.
10. Du får ett inledande meddelande om att modulen distribueras och ett nytt meddelande när den har slutförts.
11. När modulen har importerats kan du kan se de tillgängliga aktiviteterna och du kan använda dess resurser i dina runbooks och Desired State Configuration.

> [!NOTE]
> Moduler som har endast stöd för PowerShell core stöds inte i Azure Automation och kan inte importeras i Azure-portalen eller distribueras direkt från PowerShell-galleriet.

## <a name="python-runbooks"></a>Python-Runbooks

Python-Runbooks är tillgängliga i den [Script Center galleriet](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=RootCategory&f%5B0%5D.Value=WindowsAzure&f%5B1%5D.Type=ProgrammingLanguage&f%5B1%5D.Value=Python&f%5B1%5D.Text=Python&sortBy=Date&username=). Du kan bidra Python runbooks i Script Center-galleriet. När du, se till att du lägger till taggen **Python** när du laddar upp ditt bidrag.

## <a name="requesting-a-runbook-or-module"></a>Begär en runbook eller en modul
Du kan skicka begäranden till [User Voice](https://feedback.azure.com/forums/246290-azure-automation/).  Om du behöver hjälp skriver du en runbook eller har en fråga om PowerShell, ställa en fråga till vår [forum](https://social.msdn.microsoft.com/Forums/windowsazure/en-US/home?forum=azureautomation&filter=alltypes&sort=lastpostdesc).

## <a name="next-steps"></a>Nästa steg
* Kom igång med runbooks, se [skapa eller importera en runbook i Azure Automation](automation-creating-importing-runbook.md)
* Information om skillnaderna mellan PowerShell och PowerShell-arbetsflöde med runbooks finns i [Learning PowerShell-arbetsflöde](automation-powershell-workflow.md)

