---
title: Skapa en bevakningsuppgift i Azure Automation-kontot
description: Lär dig hur du skapar en watcher-uppgift i Azure Automation-kontot för att hålla utkik efter nya filer som skapats i en mapp.
services: automation
ms.subservice: process-automation
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: 5dc6145940883ff6f4446ad67c399cdf4931d38e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75419743"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Skapa en Azure Automation watcher-uppgifter för att spåra filändringar på en lokal dator

Azure Automation använder watcher-uppgifter för att hålla utkik efter händelser och utlösa åtgärder med PowerShell-runbooks. Den här självstudien går igenom att skapa en bevakningsuppgift för att övervaka när en ny fil läggs till i en katalog.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Importera en bevakningskörningsbok
> * Skapa en automatiseringsvariabel
> * Skapa en åtgärdskörningsbok
> * Skapa en bevakaraktivitet
> * Utlösa en bevakning
> * Kontrollera utdata

## <a name="prerequisites"></a>Krav

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för att hålla watcher och action runbooks och Watcher Task.
* En [hybridrunbook arbetare](automation-hybrid-runbook-worker.md) där watcher aktiviteten körs.

> [!NOTE]
> Watcher-uppgifter stöds inte i Azure China.

## <a name="import-a-watcher-runbook"></a>Importera en bevakningskörningsbok

Den här självstudien använder en watcher runbook som heter **Watch-NewFile** för att leta efter nya filer i en katalog. Den watcher runbook hämtar den senast kända skrivtiden till filerna i en mapp och tittar på alla filer nyare än att vattenstämpel.

Den här importprocessen kan göras via [PowerShell Gallery](https://www.powershellgallery.com).

1. Navigera till gallerisidan för [Watch-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-runbook-that-looks-36fc82cd).
2. Klicka på **Distribuera till Azure Automation**under fliken Azure **Automation** .

Du kan också importera den här runbooken till ditt automatiseringskonto från portalen med hjälp av följande steg.

1. Öppna ditt Automation-konto och klicka på sidan **Runbooks.**
2. Klicka på knappen **Bläddra i galleri.**
3. Sök efter "Watcher runbook", välj **Watcher runbook som söker efter nya filer i en katalog** och välj **Importera**.
  ![Importera automationskörningsbok från användargränssnittet](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Ge runbooken ett namn och en beskrivning och välj **OK** för att importera runbooken till ditt Automation-konto.
1. Välj **Redigera** och klicka sedan på **Publicera**. När du uppmanas till det väljer **du Ja** om du vill publicera runbooken.

## <a name="create-an-automation-variable"></a>Skapa en automatiseringsvariabel

En [automatiseringsvariabel](automation-variables.md) används för att lagra de tidsstämplar som föregående runbook läser och lagrar från varje fil.

1. Välj **Variabler** under **DELADE RESURSER** och välj + Lägg till en **variabel**.
1. Ange "Watch-NewFileTimestamp" för namnet
1. Välj DateTime för typ.
1. Klicka på knappen **Skapa.** Detta skapar automatiseringsvariabeln.

## <a name="create-an-action-runbook"></a>Skapa en åtgärdskörningsbok

En åtgärdskörningsbok används i en watcher-uppgift för att agera på de data som skickas till den från en watcher-runbook. PowerShell Workflow runbooks stöds inte av watcher-uppgifter, du måste använda PowerShell-runbooks. Du måste importera en fördefinierad åtgärdskörningsbok med namnet **Process-NewFile**.

Den här importprocessen kan göras via [PowerShell Gallery](https://www.powershellgallery.com).

1. Navigera till gallerisidan för [Process-NewFile.ps1](https://gallery.technet.microsoft.com/scriptcenter/Watcher-action-that-b4ff7cdf).
2. Klicka på **Distribuera till Azure Automation**under fliken Azure **Automation** .

Du kan också importera den här runbooken till ditt automatiseringskonto från portalen med hjälp av följande steg.

1. Navigera till ditt automationskonto och välj **Runbooks** under kategorin **PROCESS AUTOMATION.**
1. Klicka på knappen **Bläddra i galleri.**
1. Sök efter "Watcher action" och välj **Watcher-åtgärd som bearbetar händelser som utlöses av en watcher-runbook** och välj **Importera**.
  ![Importera åtgärdskörningsbok från användargränssnittet](media/automation-watchers-tutorial/importsourceaction.png)
1. Ge runbooken ett namn och en beskrivning och välj **OK** för att importera runbooken till ditt Automation-konto.
1. Välj **Redigera** och klicka sedan på **Publicera**. När du uppmanas till det väljer **du Ja** om du vill publicera runbooken.

## <a name="create-a-watcher-task"></a>Skapa en bevakaraktivitet

Bevakningsuppgiften innehåller två delar. Väktaren och handlingen. Bevakaren körs med ett intervall som definieras i bevakningsuppgiften. Data från watcher-runbooken skickas till åtgärdskörningsboken. I det här steget konfigurerar du bevakningsuppgiften som refererar till watcher- och åtgärdskörningsböckerna som definierats i föregående steg.

1. Navigera till ditt automationskonto och välj **Watcher-uppgifter** under kategorin **PROCESS AUTOMATION.**
1. Välj sidan Bevakareuppgifter och klicka på **+ Lägg till en bevakningsuppgiftsknapp.**
1. Ange "WatchMyFolder" som namn.

1. Välj **Konfigurera watcher** och välj **runbooken Watch-NewFile.**

1. Ange följande värden för parametrarna:

   * **FOLDERPATH** - En mapp på hybridarbetaren där nya filer skapas. d:\exempelfiler
   * **EXTENSION** - Lämna tomt för att bearbeta alla filnamnstillägg.
   * **RECURSE** - Lämna det här värdet som standard.
   * **KÖR INSTÄLLNINGAR** - Välj hybridarbetaren.

1. Klicka på OK och sedan välj för att gå tillbaka till watcher-sidan.
1. Välj **Konfigurera åtgärd** och välj runbooken "Process-NewFile".
1. Ange följande värden för parametrar:

   * **EVENTDATA** - Lämna tomt. Data skickas in från watcher runbook.
   * **Kör inställningar** – Lämna som Azure när den här runbooken körs i tjänsten Automation.

1. Klicka på **OK**och sedan välj för att gå tillbaka till watcher-sidan.
1. Klicka på **OK** om du vill skapa bevakningsuppgiften.

![Konfigurera watcher-åtgärd från användargränssnittet](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Utlösa en bevakning

Om du vill testa att bevakningen fungerar som förväntat måste du skapa en testfil.

Fjärr till hybridarbetaren. Öppna **PowerShell** och skapa en testfil i mappen.

```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

I följande exempel visas den förväntade utdata.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Kontrollera utdata

1. Navigera till ditt automationskonto och välj **Watcher-uppgifter** under kategorin **PROCESS AUTOMATION.**
1. Välj bevakningsuppgiften "WatchMyFolder".
1. Klicka på **Visa watcher strömmar** under **Strömmar** för att se att watcher hittade den nya filen och startade åtgärden runbook.
1. Om du vill se actionkörningsjobben klickar du på **åtgärdsjobben Visa bevakare**. Varje jobb kan väljas visa detaljerna för jobbet.

   ![Action-jobb för watcher från användargränssnittet](media/automation-watchers-tutorial/WatcherActionJobs.png)

Den förväntade utdata när den nya filen hittas kan ses i följande exempel:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera en bevakningskörningsbok
> * Skapa en automatiseringsvariabel
> * Skapa en åtgärdskörningsbok
> * Skapa en bevakaraktivitet
> * Utlösa en bevakning
> * Kontrollera utdata

Följ den här länken om du vill veta mer om hur du skapar en egen runbook.

> [!div class="nextstepaction"]
> [Min första PowerShell runbook](automation-first-runbook-textual-powershell.md).

