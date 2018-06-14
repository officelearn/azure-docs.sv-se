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
ms.openlocfilehash: 1e5fcbee664271058ac1c7fa80bb285e09b8579a
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
ms.locfileid: "27862204"
---
# <a name="use-azure-automation-to-trigger-a-job"></a>Använd Azure Automation för att utlösa ett jobb

Den här artikeln förklarar hur du kan använda funktionen omvandling i StorSimple Data Manager-tjänsten för att omvandla data för StorSimple-enhet. Du kan starta ett jobb för omvandling av data på två sätt: 

 - Använda .NET SDK
 - Använd Azure Automation-runbook
 
Den här artikeln beskrivs hur du skapar en Azure Automation-runbook och sedan använda den för att initiera ett jobb för omvandling av data. Om du vill veta mer om hur du initierar Dataomvandling av via .NET SDK kan du gå till [Använd .NET SDK till utlösaren data transformation jobb](storsimple-data-manager-dotnet-jobs.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar bör du kontrollera att du har:

*   Azure PowerShell är installerat på klientdatorn. [Hämta Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps).
*   En korrekt konfigurerad jobbdefinitionen i en StorSimple Data Manager-tjänst inom en resursgrupp.
*   Hämta [ `DataTransformationApp.zip` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) från GitHub-databasen. 
*   Hämta [ `Trigger-DataTransformation-Job.ps1` ](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) skriptet från GitHub-lagringsplatsen.

## <a name="step-by-step-procedure"></a>Stegvisa anvisningar

### <a name="set-up-the-automation-account"></a>Konfigurera Automation-kontot

1. Skapa ett kör som i Azure automation-konto i Azure-portalen. Det gör du genom att gå till **Azure marketplace > allt** och sök sedan efter **Automation**. Välj **Automation-konton**.

    ![Skapa kör som-automation-konto](./media/storsimple-data-manager-job-using-automation/search-automation-account1.png)

2. Om du vill lägga till ett nytt automation-konto, klickar du på **+ Lägg till**.

    ![Skapa kör som-automation-konto](./media/storsimple-data-manager-job-using-automation/add-automation-account1.png)

3. I den **lägga till Automation**:

    1. Ange den **namn** för automation-konto.
    2. Välj den **prenumeration** kopplade till StorSimple Data Manager-tjänsten.
    3. Skapa en ny resursgrupp eller välj en befintlig resursgrupp.
    4. Välj en **Plats**.
    5. Låt standardvärdet **skapa kör som-konto** alternativ som valts.
    6. Om du vill hämta en länk för snabb åtkomst på instrumentpanelen, kontrollera **fäst på instrumentpanelen**. Klicka på **Skapa**.

    ![Skapa kör som-automation-konto](./media/storsimple-data-manager-job-using-automation/create-automation-run-as-account.png)
    
    När automation-kontot har skapats visas ett meddelande.
    
    ![Meddelande för distribution av automation-konto](./media/storsimple-data-manager-job-using-automation/deployment-automation-account-notification1.png)

    Mer information finns på [skapa ett kör som-konto](../automation/automation-create-runas-account.md).

3. I det nya kontot, går du till **delade resurser > moduler** och på **+ Lägg till modulen**.

    ![Importera modul 1](./media/storsimple-data-manager-job-using-automation/import-module-1.png)

4. Bläddra till platsen för `DataTransformationApp.zip` filen från den lokala datorn och markera och öppna modulen. Klicka på **OK** att importera modulen.

    ![Importera modulen 2](./media/storsimple-data-manager-job-using-automation/import-module-2.png)

   När Azure Automation importerar en modul till ditt konto, extraherar metadata om modulen. Den här åtgärden kan ta några minuter.

   ![Importera modulen 4](./media/storsimple-data-manager-job-using-automation/import-module-4.png)

5. Du får ett meddelande som modulen distribueras och ett annat meddelande när uppdateringen är klar.  Statusen i **moduler** ändras till **tillgänglig**.

    ![Importera modulen 5](./media/storsimple-data-manager-job-using-automation/import-module-5.png)

### <a name="import-publish-and-run-automation-runbook"></a>Importera, publicera och köra Automation-runbook

Utför följande steg om du vill importera, publicera och köra runbook för att utlösa jobbdefinitionen.

1. Öppna ditt Automation-konto på Azure Portal. Gå till **Processautomatisering > Runbooks** och på **+ Lägg till en runbook**.

    ![Lägga till runbook 1](./media/storsimple-data-manager-job-using-automation/add-runbook-1.png)

2. I **lägga till runbook**, klickar du på **importera en befintlig runbook**.

3. Peka på Azure PowerShell-skriptfil `Trigger-DataTransformation-Job.ps1` för den **Runbook-filen**. Runbook-typen väljs automatiskt. Ange ett namn och en valfri beskrivning för runbook. Klicka på **Skapa**.

    ![Lägga till runbook 2](./media/storsimple-data-manager-job-using-automation/add-runbook-2.png)

4. Ny runbook visas i listan över runbooks för Automation-kontot. Välj och klicka på denna runbook.

    ![Lägga till runbook 3](./media/storsimple-data-manager-job-using-automation/add-runbook-3.png)

5. Redigera runbook och klicka på **Test** fönstret.

    ![Lägga till runbook 4](./media/storsimple-data-manager-job-using-automation/add-runbook-4.png)

6. Ange parametrar, till exempel namnet på din StorSimple Data Manager-tjänsten, gruppen associerade resurs av typen och definition jobbnamn. **Starta** testet. Rapporten genereras när körningen har slutförts. Mer information finns på hur du [testar en runbook](../automation/automation-first-runbook-textual-powershell.md#step-3---test-the-runbook).

    ![Lägga till runbook 8](./media/storsimple-data-manager-job-using-automation/add-runbook-8.png)    

7. Granska utdata från runbooken i rutan. Stäng fönstret om uppfyllt. Klicka på **publicera** och när du uppmanas att bekräfta bekräfta och publicera en runbook.

    ![Lägga till runbook 6](./media/storsimple-data-manager-job-using-automation/add-runbook-6.png)

8. Gå tillbaka till **Runbooks** och markera den nyligen skapade runbooken.

    ![Lägga till runbook 7](./media/storsimple-data-manager-job-using-automation/add-runbook-7.png)

9. **Starta** runbook. I **starta runbook**, ange alla parametrar. Klicka på **OK** att skicka och starta data transformation jobbet.

10. Du kan övervaka jobbförloppet i Azure-portalen går du till **jobb** i StorSimple Data Manager-tjänsten. Välj och klicka på jobbet för att visa jobbinformation.

    ![Lägga till runbook 10](./media/storsimple-data-manager-job-using-automation/add-runbook-10.png)

## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md).