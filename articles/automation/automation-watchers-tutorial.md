---
title: Skapa en watcher uppgift i Azure Automation-konto | Microsoft Docs
description: "Lär dig hur du skapar en watcher aktivitet i Azure Automation-konto kan du titta på för nya filer som skapas i en mapp."
services: automation
documentationcenter: 
author: eamonoreilly
manager: 
editor: 
ms.assetid: 0dd95270-761f-448e-af48-c8b1e82cd821
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/15/2017
ms.author: eamono
ms.openlocfilehash: 0ddd31f7ce2217c1136eccd391bb30bd4461c3e5
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2017
---
# <a name="azure-automation-watcher-tasks-enable-you-to-respond-to-events-happening-in-your-local-datacenter"></a>Azure Automation watcher uppgifter gör det möjligt för dig att svara på händelser som händer i ditt lokala datacenter

I kursen får du lära dig hur du skapar en ny watcher uppgift att:

> [!div class="checklist"]
> * Skapa en watcher runbook som söker efter nya filer i en katalog.
> * Skapa ett Automation-variabel att behålla en fil bearbetades av watcher senast.
> * Skapa en åtgärd runbook som anropas när runbook watcher hittar en ny fil.
> * Skapa en watcher aktivitet som väljer watcher runbook och åtgärden runbook.
> * Utlös ett webbgränssnitt genom att lägga till en ny fil i en katalog.
> * Granska utdata från åtgärden runbook som visar information om den nya filen.  

## <a name="prerequisites"></a>Krav

Den här kursen krävs följande.
+ En Azure-prenumeration. Om du inte redan har ett konto kan du [aktivera dina MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) eller registrera dig för ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
+ [Automation-konto](automation-offering-get-started.md) för watcher och åtgärden runbooks och Watcher uppgiften.
+ En [runbook worker-hybrid](automation-hybrid-runbook-worker.md) där watcher aktiviteten körs.

## <a name="create-a-watcher-runbook-that-looks-for-new-files"></a>Skapa en Watcher runbook som söker efter nya filer
1.  Öppna Automation-kontot och klicka på sidan Runbooks.
2.  Klicka på knappen ”Bläddra galleriet”.
![Runbook-listan från Gränssnittet](media/automation-watchers-tutorial/WatcherTasksRunbookList.png)
3.  Sök efter ”titta på NewFile” och importera runbook i Automation-kontot.
![Publicera runbook från Gränssnittet](media/automation-watchers-tutorial/Watch-NewFileRunbook.png)
4.  Klicka på ”Edit” visa källan Runbook och klicka på knappen ”Publicera”.

## <a name="create-an-automation-variable-to-keep-the-last-time-a-file-was-processed-by-the-watcher"></a>Skapa ett Automation-variabel att behålla en fil bearbetades av watcher senast
1.  Öppna sidan variabler under delade resurser och klicka på ”Lägg till variabeln” ![lista variablerna från Gränssnittet](media/automation-watchers-tutorial/WatcherVariableList.png)
2.  Ange ”titta på NewFileTimestamp” för namnet
3.  Välj typ som DateTime och klicka sedan på knappen ”Skapa”.
![Skapa vattenstämpel variabel från Gränssnittet](media/automation-watchers-tutorial/WatcherWatermarkVariable.png)

## <a name="create-an-action-runbook-that-is-called-when-the-watcher-runbook-finds-a-new-file"></a>Skapa en åtgärd runbook som anropas när runbook watcher hittar en ny fil
1.  Klicka på sidan Runbooks under kategorin ”PROCESSAUTOMATISERING”.
2.  Klicka på knappen ”Bläddra galleriet”.
3.  Sök efter ”processen NewFile” och importera runbook i Automation-kontot.
4.  Klicka på ”Edit” visa källan Runbook och klicka på knappen ”Publicera”.
![Övervakare för processen från Gränssnittet](media/automation-watchers-tutorial/Watch-ProcessNewFile.png)


## <a name="create-a-watcher-task-that-selects-the-watcher-runbook-and-action-runbook"></a>Skapa en watcher uppgift som väljer watcher runbook och åtgärden runbook
1.  Öppna sidan Watcher aktiviteter och klicka på ”Lägg till en aktivitet watcher”.
![Watcher listan från Gränssnittet](media/automation-watchers-tutorial/WatchersList.png)
2.  Ange ”WatchMyFolder” som namn.
3.  Välj ”Konfigurera watcher” och ”titta på NewFile” runbook.
![Konfigurera watcher från Gränssnittet](media/automation-watchers-tutorial/ConfigureWatcher.png)
4.  Ange följande värden för parametrar:
    *   FOLDERPATH. En mapp på worker-hybriden där nya filer skapas
    *   TILLÄGGET. Lämna tomt för att bearbeta alla filnamnstillägg.
    *   RECURSE. Låt standardvärdet.
    *   KÖR INSTÄLLNINGAR. Välj worker-hybriden.
5.  Klicka på OK och välj sedan att gå tillbaka till sidan watcher.
6.  Välj ”Konfigurera åtgärd” och ”processen NewFile” runbook.
![Konfigurera watcher åtgärd från Gränssnittet](media/automation-watchers-tutorial/ConfigureAction.png)
7.  Ange följande värden för parametrar:
    *   EVENTDATA. Lämna tomt. Data skickas från watcher runbook.
    *   Kör inställningar. Lämna som Azure som denna runbook körs i Automation-tjänsten.
8.  Klicka på OK och välj sedan att gå tillbaka till sidan watcher.
9.  Klicka på OK om du vill skapa uppgiften watcher.

## <a name="trigger-a-watcher-by-adding-a-new-file-to-a-directory"></a>Utlös en watcher genom att lägga till en ny fil i en katalog
1.  Fjärråtkomst till hybrid worker
2.  Lägg till en ny textfil till den mapp som övervakas av aktiviteten watcher.

## <a name="inspect-the-output-from-the-action-runbook-that-shows-information-on-the-new-file"></a>Inspektera resultatet från åtgärden runbook som visar information om den nya filen
1.  Klicka på aktiviteten watcher för ”WatchMyFolder”
2.  Klicka på ”Visa watcher strömmar” att se att watcher hitta den nya filen och startade åtgärden runbook.
3.  Klicka på ”Visa watcher åtgärdsjobb” att åtgärden runbook-jobbet.
![Watcher åtgärdsjobb från Gränssnittet](media/automation-watchers-tutorial/WatcherActionJobs.png)


## <a name="next-steps"></a>Nästa steg:

Mer information finns i [min första PowerShell-runbook](automation-first-runbook-textual-powershell.md).








