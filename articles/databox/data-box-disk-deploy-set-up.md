---
title: Konfigurera Microsoft Azure Data Box Disk | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du konfigurerar Azure Data Box Disk
services: databox
documentationcenter: NA
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: databox
ms.devlang: NA
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/28/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 6fcc7823a7e2f2f1e280622a1fa05d4417a71546
ms.sourcegitcommit: a1140e6b839ad79e454186ee95b01376233a1d1f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2018
ms.locfileid: "43143490"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Självstudie: Packa upp, ansluta och låsa upp en Azure Data Box-disk

Den här självstudien beskriver hur du packar upp, ansluter och låser upp en Azure Data Box-disk.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Packa upp en Data Box-disk
> * Ansluta och låsa upp Data Box-disken.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

1. Du har slutfört [självstudien Beställa en Azure Data Box-disk](data-box-disk-deploy-ordered.md).
2. Du har tagit emot diskarna och jobbstatusen på portalen har uppdaterats till **Levererade**.
3. Du har en värddator som du kan installera Data Box Disk-upplåsningsverktyget på. Värddatorn måste
    - Köra ett [operativsystem som stöds](data-box-disk-system-requirements.md).
    - Ha [Windows PowerShell 4 installerat](https://www.microsoft.com/download/details.aspx?id=40855).
    - Ha [.NET Framework 4.5.1 installerat](https://www.microsoft.com/download/details.aspx?id=30653).
    - Ha [BitLocker aktiverat](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-how-to-deploy-on-windows-server).
    - Ha [Windows Management Framework 4 installerat](https://www.microsoft.com/en-us/download/details.aspx?id=40855). 

## <a name="unpack-your-disks"></a>Packa upp diskarna

 Packa upp diskarna genom att följa stegen nedan.

1. Data Box-diskarna skickas i en liten leveransbox. Öppna boxen och ta ur innehållet. Kontrollera att boxen innehåller 1 till 5 SSD-diskar (Solid-State Disk) och en USB-anslutningskabel per disk. Inspektera boxen och leta efter tecken på manipulation eller andra uppenbara skador. 

    ![Leveranspaket för Data Box-disk](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Öppna inte leveransboxen om den har manipulerats eller är allvarligt skadad. Kontakta Microsoft Support för att få hjälp med att bedöma om diskarna är i gott skick eller om de bör ersättas.
3. Kontrollera att boxen har en genomskinlig ficka som innehåller en fraktsedel (under den befintliga etiketten) för returförsändelse. Om etiketten är skadad eller saknas kan du ladda ned och skriva ut en ny fraktsedel från Azure Portal. 

    ![Fraktsedel för Data Box-disk](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Spara boxen och förpackningsskummet för returförsändelse av diskarna.

## <a name="connect-and-unlock-your-disks"></a>Ansluta och låsa upp diskarna

Anslut och lås upp diskarna genom att följa stegen nedan.

1. Använd den medföljande kabeln för att ansluta disken till en Windows-dator som kör ett operativsystem som stöds (se systemkraven). 

    ![Ansluta en Data Box-disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Gå till **Allmänt > Enhetsinformation**. 
3. Klicka på **Download Data Box Disk unlock tool** (Ladda ned Data Box Disk-upplåsningsverktyget). 

    ![Nedladdning av Data Box Disk-upplåsningsverktyget](media/data-box-disk-deploy-set-up/data-box-disk-unload1.png)     

4. Extrahera verktyget på samma dator som du använder för att kopiera data.
5. Öppna ett kommandotolksfönster eller kör Windows PowerShell som administratör på samma dator.
6. (Valfritt) Kontrollera att den dator som du använder för att låsa upp disken uppfyller operativsystemskraven genom att köra systemkontrollkommandot. Ett exempel på utdata visas nedan. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

7. Gå till **Allmänt > Enhetsinformation**. Kopiera nyckeln med hjälp av kopieringsikonen.
8. Kör `DataBoxDiskUnlock.exe` och ange nyckeln. Diskens tilldelade enhetsbeteckning visas. Ett exempel på utdata visas nedan.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkeys (format: passkey1;passkey2;passkey3):
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

9. Upprepa steg 6–8 för eventuella framtida återinsättningar av diskar. Använd hjälpkommandot om du behöver hjälp med Data Box Disk-upplåsningsverktyget.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKeys:<passkey_list_separated_by_semicolon>
    
    Example: DataBoxUnlock /PassKeys:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKeys:       Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
10. Du kan visa innehållet på disken när disken har låsts upp.    

    ![Data Box-diskinnehåll](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
> * Packa upp en Data Box-disk
> * Ansluta och låsa upp Data Box-diskar


Gå vidare till nästa självstudiekurs och lär dig hur du kopierar data på en Data Box-disk.

> [!div class="nextstepaction"]
> [Kopiera data på en Data Box-disk](./data-box-disk-deploy-copy-data.md)

