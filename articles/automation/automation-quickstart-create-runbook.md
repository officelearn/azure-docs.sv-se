---
title: Azure-snabbstart – Skapa en Azure Automation-runbook | Microsoft Docs
description: Lär dig hur du skapar en Azure Automation-runbook
services: automation
ms.date: 02/05/2019
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 250f51c9f028dd55d8327259e35b82b0c392c1f6
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537006"
---
# <a name="create-an-azure-automation-runbook"></a>Skapa en Azure Automation-runbook

Azure Automation-runbooks kan skapas via Azure. Den här metoden ger ett webbläsarbaserat användargränssnitt för att skapa Automation-runbooks. Den här snabbstarten går igenom hur du skapar, testar och publicerar en Automation PowerShell-runbook.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på https://portal.azure.comAzure på .

## <a name="create-the-runbook"></a>Skapa runbooken

Skapa en runbook först. Exempelrunbooken som skapas i den här snabbstarten matar ut `Hello World` som standard.

1. Öppna ditt Automation-konto.

1. Klicka på **Runbooks** under **ProcessAutomation**. En lista med runbooks visas.

1. Klicka på **Skapa en runbook** högst upp i listan.

1. Ange `Hello-World` för runbooknamnet i fältet **Namn** och välj **PowerShell** för fältet **Runbook-typ.** 

   ![Ange information om din Automation-runbook på sidan](./media/automation-quickstart-create-runbook/automation-create-runbook-configure.png)

1. Klicka på **Skapa**. Runbooken skapas och sidan Redigera PowerShell-runbook öppnas.

    ![Skapa PowerShell-skript i runbook-redigeringsprogrammet](./media/automation-quickstart-create-runbook/automation-edit-runbook-empty.png)

1. Skriv in eller kopiera och klistra in följande kod i redigeringsrutan. Den skapar en valfri `Name` indataparameter `World`som anropas med ett standardvärde på och matar ut en sträng som använder det här indatavärdet:

   ```powershell-interactive
   param
   (
       [Parameter(Mandatory=$false)]
       [String] $Name = "World"
   )

   "Hello $Name!"
   ```

1. Klicka på **Spara** om du vill spara en utkastkopia av runbooken.

    ![Skapa PowerShell-skript i runbook-redigeringsprogrammet](./media/automation-quickstart-create-runbook/automation-edit-runbook.png)

## <a name="test-the-runbook"></a>Testa runbooken

När runbooken har skapats måste du testa runbooken för att verifiera att den fungerar.

1. Öppna testfönstret genom att klicka på **Testfönster**.

1. Ange ett värde för **Namn** och klicka på **Starta**. Testjobbet startar och jobbstatus och utdata visas.

    ![Testjobb för runbook](./media/automation-quickstart-create-runbook/automation-test-runbook.png)

1. Stäng testfönstret genom att klicka på **X** i det övre högra hörnet. Välj **OK** i popup-fönstret som visas.

1. På sidan Redigera PowerShell-runbook klickar du på **Publicera** för att publicera runbooken som den officiella versionen av runbooken på kontot.

   ![Testjobb för runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job.png)

## <a name="run-the-runbook"></a>Köra runbooken

När runbooken har publicerats visas översiktssidan.

1. På översiktssidan i runbooken klickar du på **Starta** för att öppna konfigurationssidan Starta runbook för denna runbook.

   ![Testjobb för runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-start.png)

1. Lämna **Namn** tomt, så standardvärdet används, och klicka på **OK**. Runbook-jobbet skickas och sidan Jobb visas.

   ![Testjobb för runbook](./media/automation-quickstart-create-runbook/automation-job-page.png)

1. När jobbstatusen `Running` `Completed`är eller klickar du på **Utdata** för att öppna utdatafönstret och visa runbookutdata.

   ![Testjobb för runbook](./media/automation-quickstart-create-runbook/automation-hello-world-runbook-job-output.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort din runbook när den inte längre behövs. Det gör du genom att markera runbooken i runbooklistan och klicka på **Ta bort**.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du skapat, redigerat, testat och publicerat en runbook och startat ett runbookjobb. Om du vill lära dig mer om Automation-runbooks fortsätter du till artikeln om olika runbooktyper du kan skapa och använda i Automation.

> [!div class="nextstepaction"]
> [Automation-anvisningar – Runbooktyper](./automation-runbook-types.md)
