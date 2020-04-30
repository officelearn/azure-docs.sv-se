---
title: Skapa en bevakare-uppgift i Azure Automation kontot
description: Lär dig hur du skapar en bevakare-aktivitet i Azure Automation-kontot och titta efter nya filer som skapats i en mapp.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 1175350e7f9f4db92d7d59eba0cc66ac4bb49f5f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81617359"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Skapa en Azure Automation bevakare-aktiviteter för att spåra fil ändringar på en lokal dator

Azure Automation använder en bevakare-uppgift för att söka efter händelser och utlösa åtgärder med PowerShell-Runbooks. Bevakaren innehåller två delar, bevakaren och åtgärden. En bevakad runbook körs enligt ett intervall som definierats i övervaknings aktiviteten och matar ut data till en åtgärds-Runbook. 

Den här självstudien vägleder dig genom att skapa en bevakare-uppgift som ska övervakas när en ny fil läggs till i en katalog. Lär dig att:

> [!div class="checklist"]
> * Importera en övervakare Runbook
> * Skapa en Automation-variabel
> * Skapa en åtgärds-Runbook
> * Skapa en bevakaraktivitet
> * Utlös en övervakare
> * Granska utdata

## <a name="prerequisites"></a>Krav

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att lagra övervakare och åtgärds-Runbooks och övervaknings aktiviteten.
* En [hybrid Runbook Worker](automation-hybrid-runbook-worker.md) där övervaknings aktiviteten körs.
* PowerShell-Runbooks. PowerShell Workflow-Runbooks stöds inte av bevakade uppgifter.

> [!NOTE]
> Övervaknings uppgifter stöds inte i Azure Kina.

## <a name="import-a-watcher-runbook"></a>Importera en övervakare Runbook

I den här självstudien används en bevakad Runbook som kallas **Watch-NewFile** för att leta efter nya filer i en katalog. Övervakarens Runbook hämtar den senast kända Skriv tiden till filerna i en mapp och tittar på filer som är nyare än den vattenstämpeln.

Den här import processen kan göras via [PowerShell-galleriet](https://www.powershellgallery.com).

1. Gå till Galleri sidan för [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Klicka på **distribuera i Azure Automation**på fliken **Azure Automation** .

Du kan också importera denna Runbook till ditt Automation-konto från portalen med hjälp av följande steg.

1. Öppna ditt Automation-konto och klicka på sidan Runbooks.
2. Klicka på **Bläddra i galleriet**.
3. Sök efter **övervakare Runbook**, Välj **övervakare Runbook som söker efter nya filer i en katalog**och klicka på **Importera**.
  ![Importera Automation-Runbook från UI](media/automation-watchers-tutorial/importsourcewatcher.png)
4. Ge runbooken ett namn och en beskrivning och klicka på **OK** för att importera runbooken till ditt Automation-konto.
5. Välj **Redigera** och klicka sedan på **publicera**. När du uppmanas till det klickar du på **Ja** för att publicera runbooken.

## <a name="create-an-automation-variable"></a>Skapa en Automation-variabel

En [Automation-variabel](automation-variables.md) används för att lagra tidsstämplar som föregående Runbook läser och lagrar från varje fil.

1. Välj **variabler** under **delade resurser** och klicka på **+ Lägg till en variabel**.
1. Ange Watch-NewFileTimestamp som namn.
1. Välj DateTime för typen.
1. Skapa Automation-variabeln genom att klicka på **skapa** .

## <a name="create-an-action-runbook"></a>Skapa en åtgärds-Runbook

En åtgärds-Runbook används i en bevakare-uppgift för att agera på de data som skickas till den från en bevakad Runbook. Du måste importera en fördefinierad Runbook som kallas **process-NewFile** från [PowerShell-galleriet](https://www.powershellgallery.com). 

Så här skapar du en åtgärds-Runbook:

1. Gå till Galleri sidan för [process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Klicka på **distribuera i Azure Automation**på fliken **Azure Automation** .

Du kan också importera denna Runbook till ditt Automation-konto från Azure Portal:

1. Navigera till ditt Automation-konto och välj **Runbooks** under **process automatisering**.
1. Klicka på **Bläddra i galleriet**.
1. Sök efter **övervaknings åtgärd**, Välj **övervaknings åtgärd som bearbetar händelser som utlöses av en bevakad Runbook**och klicka på **Importera**.
  ![Importera åtgärds-Runbook från UI](media/automation-watchers-tutorial/importsourceaction.png)
1. Ge runbooken ett namn och en beskrivning och klicka på **OK** för att importera runbooken till ditt Automation-konto.
1. Välj **Redigera** och klicka sedan på **publicera**. När du uppmanas till det klickar du på **Ja** för att publicera runbooken.

## <a name="create-a-watcher-task"></a>Skapa en bevakaraktivitet

I det här steget konfigurerar du övervaknings aktiviteten som refererar till övervakare och åtgärds-Runbooks som definieras i föregående avsnitt.

1. Navigera till ditt Automation-konto och välj **övervaknings uppgifter** under **process automatisering**.
1. Välj sidan övervaknings uppgifter och klicka på **+ Lägg till en bevakare-uppgift**.
1. Ange **WatchMyFolder** som namn.

1. Välj **Konfigurera övervakare** och välj **NewFile** Runbook.

1. Ange följande värden för parametrarna:

   * **FOLDERPATH** – en mapp i hybrid Runbook Worker där nya filer skapas, till exempel **d:\examplefiles**.
   * **Tillägg** -anknytning för konfigurationen. Lämna tomt om du vill bearbeta alla fil namns tillägg.
   * **Rekursivt** – rekursiv åtgärd. Lämna det här värdet som standard.
   * **Kör inställningar** – inställningen för att köra runbooken. Välj hybrid Worker.

1. Klicka på **OK**och **Välj** sedan för att återgå till sidan övervakare.
1. Välj **Konfigurera åtgärd** och välj **processen NewFile** Runbook.
1. Ange följande värden för parametrarna:

   * **EVENTDATA** – händelse data. Lämna tomt. Data skickas från övervaknings-runbooken.
   * **Kör inställningar** – inställningen för att köra runbooken. Lämna som Azure, eftersom denna Runbook körs i Azure Automation.

1. Klicka på **OK**och **Välj** sedan för att återgå till sidan övervakare.
1. Klicka på **OK** för att skapa bevakare-aktiviteten.

![Konfigurera övervaknings åtgärd från användar gränssnittet](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Utlös en övervakare

Du måste köra ett test enligt beskrivningen nedan för att se till att övervaknings aktiviteten fungerar som förväntat. 

1. Fjärran slut till Hybrid Runbook Worker. 
2. Öppna **PowerShell** och skapa en test fil i mappen.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

I följande exempel visas förväntade utdata.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Granska utdata

1. Navigera till ditt Automation-konto och välj **övervaknings uppgifter** under **process automatisering**.
1. Välj aktiviteten bevakare **WatchMyFolder**.
1. Klicka på **Visa övervakare strömmar** under **strömmar** för att se att bevakaren har hittat den nya filen och startat Runbook-åtgärden.
1. Om du vill visa Runbook-jobb klickar du på **Visa övervakare åtgärds jobb**. Varje jobb kan väljas för att visa information om jobbet.

   ![Övervaknings åtgärds jobb från användar gränssnittet](media/automation-watchers-tutorial/WatcherActionJobs.png)

Förväntade utdata när den nya filen hittas kan visas i följande exempel:

```output
Message is Process new file...

Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera en övervakare Runbook
> * Skapa en Automation-variabel
> * Skapa en åtgärds-Runbook
> * Skapa en bevakaraktivitet
> * Utlös en övervakare
> * Granska utdata

Följ den här länken om du vill veta mer om hur du redigerar din egen Runbook.

> [!div class="nextstepaction"]
> [Min första PowerShell-Runbook](automation-first-runbook-textual-powershell.md).

