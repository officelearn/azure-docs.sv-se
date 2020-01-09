---
title: Skapa en bevakare-uppgift i Azure Automation kontot
description: Lär dig hur du skapar en bevakare-aktivitet i Azure Automation-kontot och titta efter nya filer som skapats i en mapp.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75419743"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Skapa en Azure Automation bevakare-aktiviteter för att spåra fil ändringar på en lokal dator

Azure Automation använder övervaknings uppgifter för att övervaka händelser och utlösa åtgärder med PowerShell-Runbooks. Den här självstudien vägleder dig genom att skapa en bevakare-uppgift som ska övervakas när en ny fil läggs till i en katalog.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Importera en övervakare Runbook
> * Skapa en Automation-variabel
> * Skapa en åtgärds-Runbook
> * Skapa en bevakare-uppgift
> * Utlös en övervakare
> * Granska utdata

## <a name="prerequisites"></a>Krav

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att lagra övervakare och åtgärds-Runbooks och övervaknings aktiviteten.
* En [hybrid Runbook Worker](automation-hybrid-runbook-worker.md) där övervaknings aktiviteten körs.

> [!NOTE]
> Övervaknings uppgifter stöds inte i Azure Kina.

## <a name="import-a-watcher-runbook"></a>Importera en övervakare Runbook

I den här självstudien används en bevakad Runbook som kallas **Watch-NewFile** för att leta efter nya filer i en katalog. Övervakarens Runbook hämtar den senast kända Skriv tiden till filerna i en mapp och tittar på filer som är nyare än den vattenstämpeln.

Den här import processen kan göras via [PowerShell-galleriet](https://www.powershellgallery.com).

1. Gå till Galleri sidan för [Watch-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Klicka på **distribuera i Azure Automation**på fliken **Azure Automation** .

Du kan också importera denna Runbook till ditt Automation-konto från portalen med hjälp av följande steg.

1. Öppna ditt Automation-konto och klicka på sidan **Runbooks** .
2. Klicka på knappen **Bläddra i galleriet** .
3. Sök efter "övervakare Runbook", Välj **övervakare Runbook som söker efter nya filer i en katalog** och välj **Importera**.
  ![importera Automation-Runbook från UI](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Ge runbooken ett namn och en beskrivning och välj **OK** för att importera runbooken till ditt Automation-konto.
1. Välj **Redigera** och klicka sedan på **publicera**. När du tillfrågas väljer du **Ja** för att publicera runbooken.

## <a name="create-an-automation-variable"></a>Skapa en Automation-variabel

En [Automation-variabel](automation-variables.md) används för att lagra tidsstämplar som föregående Runbook läser och lagrar från varje fil.

1. Välj **variabler** under **delade resurser** och välj **+ Lägg till en variabel**.
1. Ange "Watch-NewFileTimestamp" som namn
1. Välj DateTime som typ.
1. Klicka på knappen **skapa** . Detta skapar Automation-variabeln.

## <a name="create-an-action-runbook"></a>Skapa en åtgärds-Runbook

En åtgärds-Runbook används i en bevakare-uppgift för att agera på de data som skickas till den från en bevakad Runbook. PowerShell Workflow-Runbooks stöds inte av bevakade uppgifter. du måste använda PowerShell-Runbooks. Du måste importera en fördefinierad åtgärd Runbook som kallas **process-NewFile**.

Den här import processen kan göras via [PowerShell-galleriet](https://www.powershellgallery.com).

1. Gå till Galleri sidan för [process-NewFile. ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Klicka på **distribuera i Azure Automation**på fliken **Azure Automation** .

Du kan också importera denna Runbook till ditt Automation-konto från portalen med hjälp av följande steg.

1. Navigera till ditt Automation-konto och välj **Runbooks** under kategorin **process automatisering** .
1. Klicka på knappen **Bläddra i galleriet** .
1. Sök efter "övervaknings åtgärd" och välj **bevakare åtgärd som bearbetar händelser som utlöses av en bevakad Runbook** och välj **Importera**.
  ![importera åtgärds-Runbook från UI](media/automation-watchers-tutorial/importsourceaction.png)
1. Ge runbooken ett namn och en beskrivning och välj **OK** för att importera runbooken till ditt Automation-konto.
1. Välj **Redigera** och klicka sedan på **publicera**. När du tillfrågas väljer du **Ja** för att publicera runbooken.

## <a name="create-a-watcher-task"></a>Skapa en bevakare-uppgift

Övervaknings aktiviteten innehåller två delar. Övervakare och åtgärd. Bevakaren körs med ett intervall som definierats i övervaknings aktiviteten. Data från övervaknings-runbooken skickas till Runbook-åtgärden. I det här steget konfigurerar du aktiviteten Övervakare som refererar till övervakare och åtgärds-Runbooks som definierats i föregående steg.

1. Navigera till ditt Automation-konto och välj **övervaknings uppgifter** under kategorin **process automatisering** .
1. Välj sidan övervakare och klicka på **+ Lägg till en bevakare-aktivitets** knapp.
1. Ange "WatchMyFolder" som namn.

1. Välj **Konfigurera övervakare** och välj **NewFile-** runbooken.

1. Ange följande värden för parametrarna:

   * **FOLDERPATH** – en mapp på Hybrid Worker där nya filer skapas. d:\examplefiles
   * **Tillägg** – lämna tomt om du vill bearbeta alla fil namns tillägg.
   * **Rekursivt** – lämna det här värdet som standard.
   * **Kör inställningar** – Välj hybrid Worker.

1. Klicka på OK och välj sedan för att återgå till sidan övervakare.
1. Välj **Konfigurera åtgärd** och välj "bearbeta-NewFile"-Runbook.
1. Ange följande värden för parametrar:

   * **EVENTDATA** – lämna tomt. Data skickas från övervaknings-runbooken.
   * **Kör inställningar** -lämna som Azure eftersom denna Runbook körs i Automation-tjänsten.

1. Klicka på **OK**och välj sedan för att återgå till sidan övervakare.
1. Klicka på **OK** för att skapa bevakare-aktiviteten.

![Konfigurera övervaknings åtgärd från användar gränssnittet](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Utlös en övervakare

För att testa bevakaren fungerar som förväntat måste du skapa en test fil.

Fjärran slut till hybrid Worker. Öppna **PowerShell** och skapa en test fil i mappen.

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

1. Navigera till ditt Automation-konto och välj **övervaknings uppgifter** under kategorin **process automatisering** .
1. Välj bevakad uppgift "WatchMyFolder".
1. Klicka på **Visa övervakare strömmar** under **strömmar** för att se att bevakaren hittade den nya filen och startade åtgärden Runbook.
1. Klicka på **Åtgärds jobben Visa övervakare**för att se Runbook-jobb. Varje jobb kan väljas Visa information om jobbet.

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
> * Skapa en bevakare-uppgift
> * Utlös en övervakare
> * Granska utdata

Följ den här länken om du vill veta mer om hur du redigerar din egen Runbook.

> [!div class="nextstepaction"]
> [Min första PowerShell-Runbook](automation-first-runbook-textual-powershell.md).

