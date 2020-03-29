---
title: Använd Azure Automation för att starta ett jobb i StorSimple Data Manager
description: Lär dig hur du använder Azure Automation för att utlösa StorSimple Data Manager-jobb
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: 034b4996672f0961cf31d342aa6055482f099b9f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273995"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Använda Azure Automation för att utlösa ett jobb

I den här artikeln beskrivs hur du kan använda dataomvandlingsfunktionen i StorSimple Data Manager-tjänsten för att omvandla StorSimple-enhetsdata. Du kan starta ett dataomvandlingsjobb på två sätt: 

 - Använda .NET SDK
 - Använda Azure Automation-runbook
 
I den här artikeln beskrivs hur du skapar en Azure Automation-runbook och sedan använder den för att initiera ett dataomvandlingsjobb. Mer information om hur du initierar dataomvandling via .NET SDK går du till [Använda .NET SDK för att utlösa dataomvandlingsjobb](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Krav

Innan du börjar, se till att du har:

*   Azure PowerShell installerat på klientdatorn. [Ladda ned Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   En korrekt konfigurerad jobbdefinition i en StorSimple Data Manager-tjänst inom en resursgrupp.
*   Hämta [`DataTransformationApp.zip`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) fil från GitHub-databasen. 
*   Hämta [`Trigger-DataTransformation-Job.ps1`](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skript från GitHub-databasen.

## <a name="step-by-step-procedure"></a>Steg-för-steg-procedur

### <a name="set-up-the-automation-account"></a>Konfigurera Automation-kontot

1. Skapa ett Azure Run As-automatiseringskonto i Azure-portalen. Det gör du genom att gå till **Azure marketplace > Everything** och sedan söka efter **Automation**. Välj **Automation-konton**.

    ![Skapa körning som automationskonto](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Om du vill lägga till ett nytt automatiseringskonto klickar du på **+ Lägg till**.

    ![Skapa körning som automationskonto](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. I **Lägg till automatisering:**

   1. Ange **namnet** på ditt automationskonto.
   2. Välj den **prenumeration** som är länkad till tjänsten StorSimple Data Manager.
   3. Skapa en ny resursgrupp eller välj från en befintlig resursgrupp.
   4. Välj en **plats**.
   5. Låt standardalternativet **Skapa kör som** konto markerat.
   6. Om du vill få en länk för snabb åtkomst på instrumentpanelen markerar du **Fäst på instrumentpanelen**. Klicka på **Skapa**.

      ![Skapa körning som automationskonto](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      När automatiseringskontot har skapats meddelas du.
    
      ![Meddelande för distribution av automation-konto](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Mer information finns i [Skapa ett Kör som-konto](../automation/automation-create-runas-account.md).

3. Gå till Delade resurser **> moduler i** det nyligen skapade kontot och klicka på **+ Lägg till modul**.

    ![Importera modul 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Bläddra till `DataTransformationApp.zip` platsen för filen från den lokala datorn och välj och öppna modulen. Importera modulen genom att klicka på **OK.**

    ![Importera modul 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   När Azure Automation importerar en modul till ditt konto extraheras metadata om modulen. Den här åtgärden kan ta ett par minuter.

   ![Importera modul 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Du får ett meddelande om att modulen distribueras och ett annat meddelande när processen är klar.  Statusen i **Moduler ändras** till **Tillgänglig**.

    ![Importera modul 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importera, publicera och köra Automation-runbook

Utför följande steg för att importera, publicera och köra runbooken för att utlösa jobbdefinition.

1. Öppna ditt Automation-konto på Azure Portal. Gå till **ProcessAutomatisering > Runbooks** och klicka på **+ Lägg till en runbook**.

    ![Lägg till runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. Klicka på Importera **en befintlig runbook**i Lägg till **runbook.**

3. Peka på Azure PowerShell-skriptfilen `Trigger-DataTransformation-Job.ps1` för **Runbook-filen**. Runbook-typen väljs automatiskt. Ange ett namn och en valfri beskrivning för runbooken. Klicka på **Skapa**.

    ![Lägg till runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Den nya runbooken visas i listan över runbooks för Automation-kontot. Markera och klicka på den här runbooken.

    ![Lägg till runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Redigera runbooken och klicka på **Testfönstret.**

    ![Lägg till runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Ange parametrar som namnet på Tjänsten StorSimple Data Manager, den associerade resursgruppen och jobbdefinitionsnamnet. **Starta** testet. Rapporten genereras när körningen är klar. Mer information finns i hur du [testar en runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Lägg till runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Kontrollera utdata från runbooken i testfönstret. Om du är nöjd stänger du fönstret. Klicka på **Publicera** och bekräfta och publicera runbooken när du uppmanas att bekräfta.

    ![Lägg till runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Gå tillbaka till **Runbooks** och välj den nyskapade runbooken.

    ![Lägg till runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Starta** runbooken. Ange alla parametrar i **Startkörningsbok.** Klicka på **OK** om du vill skicka och starta dataomvandlingsjobbet.

10. Om du vill övervaka jobbförloppet i Azure-portalen går du till **Jobb** i tjänsten StorSimple Data Manager. Markera och klicka på jobbet om du vill visa jobbinformationen.

    ![Lägg till runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager-användargränssnittet för att omvandla dina data](storsimple-data-manager-ui.md).
