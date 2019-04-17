---
title: Skapa en bevakaraktivitet i Azure Automation-konto
description: Lär dig hur du skapar en bevakaraktivitet i Azure Automation-konto för att se upp för nya filer som skapas i en mapp.
services: automation
ms.service: automation
ms.subservice: process-automation
author: eamonoreilly
ms.author: eamono
ms.topic: conceptual
ms.date: 10/30/2018
ms.openlocfilehash: bee414ada61e2cfcf7609b02ef1da7323a0fe0e3
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2019
ms.locfileid: "59606930"
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Skapa en Azure Automation-watcher uppgifter för att spåra ändringar i filen på en lokal dator

Azure Automation använder bevakaraktiviteter att bevaka händelser och utlösa åtgärder med PowerShell-runbooks. Den här självstudien visar hur du skapar en bevakaraktivitet ska övervaka när en ny fil läggs till i en katalog.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Importera ett bevakarrunbookflöde
> * Skapa ett Automation-variabel
> * Skapa en åtgärd-runbook
> * Skapa en bevakaraktivitet
> * Utlösa en Övervakare
> * Granska utdata

## <a name="prerequisites"></a>Nödvändiga komponenter

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) bevakaren och åtgärdsrunbooks och Bevakaraktiviteten.
* En [hybrid runbook worker](automation-hybrid-runbook-worker.md) där bevakaraktiviteten körs.

> [!NOTE]
> Bevakaraktiviteter stöds inte i Azure Kina.

## <a name="import-a-watcher-runbook"></a>Importera ett bevakarrunbookflöde

Den här självstudien används ett bevakarrunbookflöde kallas **Watch NewFile** att söka efter nya filer i en katalog. Bevakarrunbookflöde hämtar senast kända skriva till filer i en mapp och tittar på alla filer som är nyare än den vattenstämpeln. I det här steget importerar du denna runbook till ditt automation-konto.

1. Öppna ditt Automation-konto och klicka på den **Runbooks** sidan.
2. Klicka på den **bläddringsgalleriegenskapen** knappen.
3. Sök efter ”bevakarrunbookflöde”, Välj **bevakarrunbookflöde som söker efter nya filer i en katalog** och välj **Import**.
  ![Importera automation-runbook från Användargränssnittet](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Ge runbooken namn och beskrivning och välj **OK** att importera runbooken till Automation-kontot.
1. Välj **redigera** och klicka sedan på **publicera**. När du uppmanas väljer **Ja** att publicera runbooken.

## <a name="create-an-automation-variable"></a>Skapa ett Automation-variabel

En [automation-variabel](automation-variables.md) används för att lagra tidsstämplar som föregående runbook läser och lagrar från varje fil.

1. Välj **variabler** under **delade resurser** och välj **+ Lägg till en variabel**.
1. Ange ”Watch NewFileTimestamp” för namn
1. Välj datum/tid för typen.
1. Klicka på den **skapa** knappen. Detta skapar automation-variabel.

## <a name="create-an-action-runbook"></a>Skapa en åtgärd-runbook

En åtgärd runbook används i en bevakaraktivitet för att kunna agera på data som skickas till den från ett bevakarrunbookflöde. PowerShell Workflow-runbooks stöds inte av bevakaraktiviteter måste du använda PowerShell-runbooks. I det här steget ska du uppdatera importera en fördefinierad åtgärd runbook med namnet ”Process NewFile”.

1. Gå till ditt automation-konto och välj **Runbooks** under den **PROCESSAUTOMATISERING** kategori.
1. Klicka på den **bläddringsgalleriegenskapen** knappen.
1. Sök efter ”bevakaråtgärden” och välj **bevakaråtgärden som bearbetar händelser som utlöses av ett bevakarrunbookflöde** och välj **Import**.
  ![Importera runbook åtgärd från Användargränssnittet](media/automation-watchers-tutorial/importsourceaction.png)
1. Ge runbooken namn och beskrivning och välj **OK** att importera runbooken till Automation-kontot.
1. Välj **redigera** och klicka sedan på **publicera**. När du uppmanas väljer **Ja** att publicera runbooken.

## <a name="create-a-watcher-task"></a>Skapa en bevakaraktivitet

Bevakaraktiviteten består av två delar. Bevakaren och åtgärden. Bevakaren körs vid ett intervall som anges i bevakaraktiviteten. Data från bevakarrunbookflöde skickas till runbooken åtgärd. I det här steget konfigurerar du bevakaraktiviteten refererar till bevakaren och åtgärdsrunbooks som definierats i föregående steg.

1. Gå till ditt automation-konto och välj **bevakaraktiviteter** under den **PROCESSAUTOMATISERING** kategori.
1. Välj sidan Watcher aktiviteter och klicka på **+ Lägg till en bevakaraktivitet** knappen.
1. Ange ”WatchMyFolder” som namn.

1. Välj **konfigurera watcher** och välj den **Watch NewFile** runbook.

1. Ange följande värden för parametrarna:

   * **FOLDERPATH** – en mapp på den hybrid worker där nya filer skapas. d:\examplefiles
   * **TILLÄGGET** – lämna tomt för att bearbeta alla filnamnstillägg.
   * **RECURSE** -lämna det här värdet som standard.
   * **INSTÄLLNINGAR för** -Välj hybrid worker.

1. Klicka på OK och välj sedan för att återgå till sidan watcher.
1. Välj **konfigurera åtgärden** och välj ”Process NewFile” runbook.
1. Ange följande värden för parametrarna:

   * **EVENTDATA** – lämna tomt. Data skickas från bevakarrunbookflöde.  
   * **Kör inställningar** -lämnar som Azure som denna runbook körs i Automation-tjänsten.

1. Klicka på **OK**, och välj sedan att gå tillbaka till sidan watcher.
1. Klicka på **OK** att skapa bevakaraktiviteten.

![Konfigurera bevakaråtgärden från Användargränssnittet](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Utlösa en Övervakare

Om du vill testa bevakaren fungerar som förväntat, måste du skapa en testfil.

Fjärranslut till hybrid worker. Öppna **PowerShell** och skapar en testfil i mappen.
  
```azurepowerShell-interactive
New-Item -Name ExampleFile1.txt
```

I följande exempel visar utdata som förväntas.

```output
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Granska utdata

1. Gå till ditt automation-konto och välj **bevakaraktiviteter** under den **PROCESSAUTOMATISERING** kategori.
1. Välj bevakaraktiviteten ”WatchMyFolder”.
1. Klicka på **visa bevakarströmmar** under **strömmar** att se att bevakaren hittades för den nya filen och startade åtgärden runbooken.
1. Om du vill se åtgärd runbook-jobb, klickar du på den **visa bevakaråtgärdsjobb**. Varje jobb kan vara valt vyn information om jobbet.

   ![Bevakaråtgärdsjobb från Användargränssnittet](media/automation-watchers-tutorial/WatcherActionJobs.png)

Utdata som förväntas när den nya filen hittas kan ses i följande exempel:

```output
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera ett bevakarrunbookflöde
> * Skapa ett Automation-variabel
> * Skapa en åtgärd-runbook
> * Skapa en bevakaraktivitet
> * Utlösa en Övervakare
> * Granska utdata

Följ den här länken om du vill veta mer om redigering egna runbooks.

> [!div class="nextstepaction"]
> [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).

