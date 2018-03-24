---
title: Skapa en watcher uppgift i Azure Automation-konto
description: Lär dig hur du skapar en watcher aktivitet i Azure Automation-konto kan du titta på för nya filer som skapas i en mapp.
services: automation
ms.service: automation
author: eamonoreilly
ms.author: eamono
ms.topic: article
ms.date: 03/19/2017
ms.openlocfilehash: 8cd5f77d9711ffc95e6a55e97297a23fd87c6bb7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="create-an-azure-automation-watcher-tasks-to-track-file-changes-on-a-local-machine"></a>Skapa en Azure Automation-watcher uppgifter att spåra ändringar på en lokal dator

Azure Automation använder uppgifter för hälsotillståndsövervakare att bevaka händelser och utlösa åtgärder. Den här självstudiekursen vägleder dig genom att skapa en watcher uppgift att övervaka när en ny fil har lagts till i en katalog.

I den här guiden får du lära dig hur man:

> [!div class="checklist"]
> * Importera en watcher runbook
> * Skapa ett Automation-variabel
> * Skapa en åtgärd-runbook
> * Skapa en aktivitet för webbgränssnitt
> * Utlös en watcher
> * Granska utdata

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för att genomföra kursen:

* En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Automation-konto](automation-offering-get-started.md) för watcher och åtgärden runbooks och Watcher uppgiften.
* En [runbook worker-hybrid](automation-hybrid-runbook-worker.md) där watcher aktiviteten körs.

## <a name="import-a-watcher-runbook"></a>Importera en watcher runbook

Den här kursen använder en watcher runbook med namnet **titta på NewFile** att söka efter nya filer i en katalog. Watcher runbook hämtar senast kända skriva till filer i en mapp och tittar på alla filer som är nyare än vattenstämpeln. I det här steget kan importera du runbook till ditt automation-konto.

1. Öppna ditt Automation-konto och klicka på den **Runbooks** sidan.
1. Klicka på den **Bläddra galleriet** knappen.
1. Sök efter ”Watcher runbook” Välj **Watcher runbook som söker efter nya filer i en katalog** och välj **importera**.
  ![Importera automation-runbook från Gränssnittet](media/automation-watchers-tutorial/importsourcewatcher.png)
1. Ge runbook ett namn, beskrivning och välj **OK** att importera runbook i Automation-konto.
1. Välj **redigera** och klicka sedan på **publicera**. När du uppmanas väljer **Ja** publicera en runbook.

## <a name="create-an-automation-variable"></a>Skapa ett Automation-variabel

En [automation-variabel](automation-variables.md) används för att lagra tidsstämplar som föregående runbook läser och lagrar från varje fil. 

1. Välj **variabler** under **delade resurser** och välj **+ Lägg till en variabel**.
1. Ange ”titta på NewFileTimestamp” för namnet
1. Välj datum och tid för typen.
1. Klicka på den **skapa** knappen. Detta skapar automation-variabel.

## <a name="create-an-action-runbook"></a>Skapa en åtgärd-runbook

En åtgärd runbook används i en watcher uppgift för att fungera på data som skickas till den från en watcher runbook. I det här steget uppdaterar du importera en fördefinierad åtgärd runbook med namnet ”processen NewFile”.

1. Navigera till ditt automation-konto och markera **Runbooks** under den **PROCESSAUTOMATISERING** kategori.
1. Klicka på den **Bläddra galleriet** knappen.
1. Sök efter ”Watcher åtgärd” och markera **Watcher åtgärd som bearbetar händelser som utlösts av en watcher runbook** och välj **importera**.
  ![Importera åtgärden runbook från Gränssnittet](media/automation-watchers-tutorial/importsourceaction.png)
1. Ge runbook ett namn, beskrivning och välj **OK** att importera runbook i Automation-konto.
1. Välj **redigera** och klicka sedan på **publicera**. När du uppmanas väljer **Ja** publicera en runbook.

## <a name="create-a-watcher-task"></a>Skapa en aktivitet för webbgränssnitt

Watcher innehåller två delar. Övervakarnoden och åtgärden. Watcher körs vid ett intervall som anges i aktiviteten watcher. Data från en watcher runbook skickas till åtgärden runbook. I det här steget konfigurerar du aktiviteten watcher refererar till runbooks watcher och åtgärder som definierats i föregående steg.

1. Navigera till ditt automation-konto och markera **uppgifter för Hälsotillståndsövervakare** under den **PROCESSAUTOMATISERING** kategori.
1. Välj sidan Watcher aktiviteter och klicka på **+ Lägg till en aktivitet watcher** knappen.
1. Ange ”WatchMyFolder” som namn.

1. Välj **konfigurera watcher** och välj den **titta på NewFile** runbook.

1. Ange följande värden för parametrarna:

   * **FOLDERPATH** -en mapp på worker-hybriden där nya filer skapas. d:\examplefiles
   * **TILLÄGGET** -lämna tomt för att bearbeta alla filnamnstillägg.
   * **RECURSE** -lämna det här värdet som standard.
   * **INSTÄLLNINGAR för** -Välj worker-hybriden.

1. Klicka på OK och välj sedan att gå tillbaka till sidan watcher.
1. Välj **konfigurerar åtgärd** och välj ”processen NewFile” runbook.
1. Ange följande värden för parametrar:

   *    **EVENTDATA** -lämna tomt. Data skickas från watcher runbook.  
   *    **Kör inställningar** -lämnar som Azure som denna runbook körs i Automation-tjänsten.

1. Klicka på **OK**, och välj sedan att gå tillbaka till sidan watcher.
1. Klicka på **OK** watcher uppgift.

![Konfigurera watcher åtgärd från Gränssnittet](media/automation-watchers-tutorial/watchertaskcreation.png)

## <a name="trigger-a-watcher"></a>Utlös en watcher

Om du vill testa watcher fungerar som förväntat, måste du skapa en testfil.

Fjärråtkomst till worker-hybriden. Öppna **PowerShell** och skapa en testfil i mappen.
  
   ```PowerShell-interactive
   New-Item -Name ExampleFile1.txt
   ```

I följande exempel visas utdata som förväntas.

```
    Directory: D:\examplefiles


Mode                LastWriteTime         Length Name
----                -------------         ------ ----
-a----       12/11/2017   9:05 PM              0 ExampleFile1.txt
```

## <a name="inspect-the-output"></a>Granska utdata

1. Navigera till ditt automation-konto och markera **uppgifter för Hälsotillståndsövervakare** under den **PROCESSAUTOMATISERING** kategori.
1. Välj aktiviteten watcher ”WatchMyFolder”.
1. Klicka på **visa watcher dataströmmar** under **dataströmmar** att se att watcher hitta den nya filen och startade åtgärden runbook.
1. Klicka på åtgärden runbook-jobb visas den **watcher åtgärd jobben**. Varje jobb kan vara valt vyn information om jobbet.

   ![Watcher åtgärdsjobb från Gränssnittet](media/automation-watchers-tutorial/WatcherActionJobs.png)

Förväntad utdata när den nya filen hittas kan ses i följande exempel:

```
Message is Process new file...



Passed in data is @{FileName=D:\examplefiles\ExampleFile1.txt; Length=0}
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Importera en watcher runbook
> * Skapa ett Automation-variabel
> * Skapa en åtgärd-runbook
> * Skapa en aktivitet för webbgränssnitt
> * Utlös en watcher
> * Granska utdata

Den här länken om du vill veta mer om att redigera egna runbook.

> [!div class="nextstepaction"]
> [Min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).
