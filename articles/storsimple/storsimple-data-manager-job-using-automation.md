---
title: Använd Azure Automation för att starta ett jobb i StorSimple Data Manager | Microsoft Docs
description: Lär dig hur du använder Azure Automation för att utlösa StorSimple Data Manager-jobb
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/16/2018
ms.author: alkohli
ms.openlocfilehash: b837aab871827c468295a365727a282f6c8a1a4b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634258"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Använd Azure Automation för att utlösa ett jobb

Den här artikeln förklarar hur du kan använda funktionen transformering i StorSimple Data Manager-tjänsten för att omvandla data för StorSimple-enheten. Du kan starta ett jobb för omvandling av data på två sätt: 

 - Använda .NET SDK
 - Använda Azure Automation-runbook
 
Den här artikeln beskriver hur du skapar en Azure Automation-runbook och sedan använda den för att initiera ett omvandlingsjobb. Om du vill veta mer om hur du startar Dataomvandling via .NET SDK kan du gå till [Använd .NET SDK till utlösaren datatransformeringsjobb](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar måste du kontrollera att du har:

*   Azure PowerShell installerad på klientdatorn. [Hämta Azure PowerShell](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps).
*   En korrekt konfigurerad jobbdefinition i en StorSimple Data Manager-tjänst inom en resursgrupp.
*   Ladda ned [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) filen från GitHub-lagringsplatsen. 
*   Ladda ned [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skriptet från GitHub-lagringsplatsen.

## <a name="step-by-step-procedure"></a>Stegvisa anvisningar

### <a name="set-up-the-automation-account"></a>Konfigurera Automation-konto

1. Skapa ett Azure kör som automation-konto i Azure-portalen. Du gör detta genom att gå till **Azure marketplace > allt** och söker sedan efter **Automation**. Välj **automationskonton**.

    ![Skapa kör som-konto för automation](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Om du vill lägga till ett nytt automation-konto, klickar du på **+ Lägg till**.

    ![Skapa kör som-konto för automation](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. I den **Lägg till automatisering**:

   1. Ange den **namn** av ditt automation-konto.
   2. Välj den **prenumeration** länkad till StorSimple Data Manager-tjänsten.
   3. Skapa en ny resursgrupp eller välj en befintlig resursgrupp.
   4. Välj en **Plats**.
   5. Låt standardvärdet **skapa kör som-konto** alternativ som valts.
   6. Får du en länk för snabb åtkomst på instrumentpanelen **fäst på instrumentpanelen**. Klicka på **Skapa**.

      ![Skapa kör som-konto för automation](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
      När automation-kontot har skapats, du får ett meddelande.
    
      ![Meddelande för distribution av automation-konto](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

      Mer information går du till [skapa ett kör som-konto](../automation/automation-create-runas-account.md).

3. I det nya kontot går du till **delade resurser > moduler** och klicka på **+ Lägg till modulen**.

    ![Importera modul 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Bläddra till platsen för `DataTransformationApp.zip` fil från din lokala dator och välj och öppna modulen. Klicka på **OK** att importera modulen.

    ![Importera modulen 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   När Azure Automation importerar en modul till ditt konto, extraherar metadata om modulen. Den här åtgärden kan ta några minuter.

   ![Importera modulen 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Du får ett meddelande att modulen distribueras och ett nytt meddelande när processen är klar.  Statusen i **moduler** ändras till **tillgänglig**.

    ![Importera modulen 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importera, publicera och köra Automation-runbook

Utför följande steg om du vill importera, publicera och kör en runbook för att utlösa jobbdefinitionen.

1. Öppna ditt Automation-konto på Azure Portal. Gå till **Processautomatisering > Runbooks** och klicka på **+ Lägg till en runbook**.

    ![Lägg till runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. I **Lägg till runbook**, klickar du på **importera en befintlig runbook**.

3. Peka på Azure PowerShell-skriptfil `Trigger-DataTransformation-Job.ps1` för den **Runbook-filen**. Runbook-typen väljs automatiskt. Ange ett namn och en valfri beskrivning för runbook. Klicka på **Skapa**.

    ![Lägg till runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Ny runbook visas i listan med runbook-flöden för Automation-kontot. Välj och klicka på denna runbook.

    ![Lägg till runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Redigera runbooken och klicka på **Test** fönstret.

    ![Lägg till runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Ange parametrar, till exempel namnet på StorSimple Data Manager-tjänsten, den associerade resursgruppen och namnet på jobbdefinitionen. **Starta** testet. Rapporten genereras när körningen är klar. Mer information finns i så här [testar en runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Lägg till runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Granska utdata från runbooken i rutan. Stäng fönstret om klar. Klicka på **publicera** och när du uppmanas att bekräfta bekräfta och publicera en runbook.

    ![Lägg till runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Gå tillbaka till **Runbooks** och välj den nyligen skapade runbooken.

    ![Lägg till runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Starta** runbook. I **starta runbook**, ange alla parametrar. Klicka på **OK** att skicka och starta omvandlingsjobb.

10. Du kan övervaka jobbförloppet i Azure-portalen, gå till **jobb** i StorSimple Data Manager-tjänsten. Välj och klicka på jobbet om du vill visa Jobbinformationen.

    ![Lägg till runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI och omvandla data](storsimple-data-manager-ui.md).