---
title: "Använd Azure Automation för att utlösa ett jobb | Microsoft Docs"
description: "Lär dig hur du använder Azure Automation för att utlösa StorSimple Data Manager-jobb (privat förhandsvisning)"
services: storsimple
documentationcenter: NA
author: vidarmsft
manager: syadav
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 03/16/2017
ms.author: vidarmsft
ms.openlocfilehash: 9691408bcd80afb6eba534f26749b76dd3bfe315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-azure-automation-to-trigger-a-job-private-preview"></a>Använd Azure Automation för att utlösa ett jobb (privat förhandsvisning)

Den här artikeln beskriver hur du använder Azure Automation för att utlösa ett StorSimple Data Manager-jobb.

## <a name="prerequisites"></a>Krav

Innan du börjar bör du kontrollera att du har:

*   Azure Powershell har installerats. [Hämta Azure Powershell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
*   Konfigurationsinställningarna för att initiera Data Transformation jobbet (instruktioner för att hämta inställningarna ingår).
*   En jobbdefinition av som har konfigurerats på rätt sätt i en Hybrid Dataresurs i en resursgrupp.
*   Hämta `DataTransformationApp.zip` [zip](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/raw/master/Azure%20Automation%20For%20Data%20Manager/DataTransformationApp.zip) från github-databasen.
*   Hämta `Get-ConfigurationParams.ps1` [skriptet](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Get-ConfigurationParams.ps1) från github-lagringsplatsen.
*   Hämta `Trigger-DataTransformation-Job.ps1` [skriptet](https://github.com/Azure-Samples/storsimple-dotnet-data-manager-get-started/blob/master/Azure%20Automation%20For%20Data%20Manager/Trigger-DataTransformation-Job.ps1) från github-lagringsplatsen.

## <a name="step-by-step"></a>Steg för steg

### <a name="get-azure-active-directory-permissions-for-the-automation-job-to-run-the-job-definition"></a>Hämta Azure Active Directory-behörigheter för automation-jobb att köra jobbdefinitionen

1. Om du vill hämta konfigurationsparametrarna för Active Directory, gör du följande steg:

    1. Öppna Windows PowerShell i din lokala dator. Se till att [Azure PowerShell](https://azure.microsoft.com/downloads/) är installerad.
    1. Kör den `Get-ConfigurationParams.ps1` skript (i den mapp som du hämtade ovan). Skriv följande kommando i PowerShell-fönstret:

        ```
        .\Get-ConfigurationParams.ps1 -SubscriptionName "AzureSubscriptionName" -ActiveDirectoryKey "AnyRandomPassword" -AppName "ApplicationName"
         ```

        ActiveDirectoryKey är ett lösenord som du använder senare. Ange ett lösenord som du väljer. Programnamn kan vara en sträng.

2. Det här skriptet matar ut följande värden som ska användas när utlösa automation-runbook. Anteckna dessa värden.

    - Klient-ID
    - Klient-ID:t
    - Active Directory-nyckel (samma som den som anges ovan)
    - Prenumerations-ID:t

### <a name="set-up-the-automation-account"></a>Konfigurera Automation-kontot

1. Logga in på Azure och öppna ditt Automation-konto.
2. Klicka på **tillgångar** rutan för att öppna en lista över tillgångar.
3. Klicka på **moduler** öppna listan över moduler.
4. Klicka på **+ Lägg till en modul** knappen och bladet Lägg till modulen har startats.

    ![Inställningarna för Automation-konto](./media/storsimple-data-manager-job-using-automation/add-module1m.png)

5. När du har valt den `DataTransformationApp.zip` filen från den lokala datorn, klickar på **OK** att importera modulen.

   När Azure Automation importerar en modul till ditt konto, extraherar metadata om modulen. Den här åtgärden kan ta några minuter.

   ![Inställningarna för Automation-konto](./media/storsimple-data-manager-job-using-automation/add-module2m.png)

   

6. Du får ett meddelande som modulen distribueras och ett annat meddelande när uppdateringen är klar.  Du kan också kontrollera statusen **moduler** panelen.

### <a name="to-import-the-runbook-that-triggers-the-job-definition"></a>Om du vill importera en runbook utlöser som jobbdefinitionen

1. Öppna ditt Automation-konto på Azure Portal.
2. Klicka på **Runbooks** öppna listan över runbooks.
3. Klicka på **+ Lägg till en runbook** och sedan **importera en befintlig runbook**.

   ![Importera en befintlig runbook](./media/storsimple-data-manager-job-using-automation/import-a-runbook.png)

4. Klicka på **Runbook-filen** och Välj fil att importera `Trigger-DataTransformation-Job.ps1`.
5. Klicka på **skapa** att importera runbook. Ny runbook visas i listan över runbooks för Automation-kontot.
7. Klicka på **utlösaren DataTransformation jobbet** runbook och klicka sedan på **redigera**.
8. Klicka på **publicera** och sedan **Ja** när du uppmanas att bekräfta.


### <a name="to-run-the-runbook"></a>Att köra runbook:
1. Öppna ditt Automation-konto på Azure Portal.
2. Öppna listan med runbookflöden genom att klicka på panelen med **runbookflöden**.
3. Klicka på **utlösaren DataTransformation jobbet**.
4. Starta runbooken genom att klicka på **Starta**.

   ![Starta runbooken](./media/storsimple-data-manager-job-using-automation/run-runbook1m.png)

5. I den **startar runbook** bladet ange alla parametrar. Klicka på **OK** att skicka Data Transformation-jobbet.

   ![Starta runbooken](./media/storsimple-data-manager-job-using-automation/run-runbook2m.png)


## <a name="next-steps"></a>Nästa steg

[Använd StorSimple Data Manager UI att omvandla data](storsimple-data-manager-ui.md).