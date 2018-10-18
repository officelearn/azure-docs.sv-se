---
title: Konfigurera Microsoft Azure Data Box Disk | Microsoft Docs
description: I den här självstudiekursen lär du dig hur du konfigurerar Azure Data Box Disk
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: alkohli
Customer intent: As an IT admin, I need to be able to order Data Box Disk to upload on-premises data from my server onto Azure.
ms.openlocfilehash: 24de05a60c3cda47a1968bb1f83f9cb91e4e03de
ms.sourcegitcommit: 4047b262cf2a1441a7ae82f8ac7a80ec148c40c4
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2018
ms.locfileid: "49091206"
---
# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Självstudie: Packa upp, ansluta och låsa upp en Azure Data Box-disk

Den här självstudien beskriver hur du packar upp, ansluter och låser upp en Azure Data Box-disk.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Packa upp en Data Box-disk
> * Ansluta till diskar och hämta nyckeln
> * Låsa upp diskar Windows-klient
> * Låsa upp diskar på Linux-klient

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du börjar ska du kontrollera att:

1. Du har slutfört [självstudien Beställa en Azure Data Box-disk](data-box-disk-deploy-ordered.md).
2. Du har tagit emot diskarna och jobbstatusen på portalen har uppdaterats till **Levererade**.
3. Du har en klientdator som du kan installera Data Box Disk-upplåsningsverktyget på. Klientdatorn måste:
    - Köra ett [operativsystem som stöds](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Ha en annan[programvara som krävs](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) installerad om det är en Windows-klient.  

## <a name="unpack-your-disks"></a>Packa upp diskarna

 Packa upp diskarna genom att följa stegen nedan.

1. Data Box-diskarna skickas i en liten leveransbox. Öppna boxen och ta ur innehållet. Kontrollera att boxen innehåller 1 till 5 SSD-diskar (Solid-State Disk) och en USB-anslutningskabel per disk. Inspektera boxen och leta efter tecken på manipulation eller andra uppenbara skador. 

    ![Leveranspaket för Data Box-disk](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Öppna inte leveransboxen om den har manipulerats eller är allvarligt skadad. Kontakta Microsoft Support för att få hjälp med att bedöma om diskarna är i gott skick eller om de bör ersättas.
3. Kontrollera att boxen har en genomskinlig ficka som innehåller en fraktsedel (under den befintliga etiketten) för returförsändelse. Om etiketten är skadad eller saknas kan du ladda ned och skriva ut en ny fraktsedel från Azure Portal. 

    ![Fraktsedel för Data Box-disk](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Spara boxen och förpackningsskummet för returförsändelse av diskarna.

## <a name="connect-to-disks-and-get-the-passkey"></a>Ansluta till diskar och hämta nyckeln 

1. Använd den medföljande kabeln för att ansluta disken till en klientdator som kör ett operativsystem som stöds (se systemkraven). 

    ![Ansluta en Data Box-disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)    
    
2. Gå till **Allmänt > Enhetsinformation**. Kopiera nyckeln med hjälp av kopieringsikonen. Nyckeln används för att låsa upp diskarna.

    ![Nyckel för upplåsning av Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png) 

Beroende på om du är ansluten till en Windows- eller Linux-klient är skiljer sig stegen.

## <a name="unlock-disks-on-windows-client"></a>Låsa upp diskar Windows-klient

Anslut och lås upp diskarna genom att följa stegen nedan.
     
1. Gå till **Allmänt > Enhetsinformation**. 
2. Ladda ned de Data Box Disk-verktyg som motsvarar Windows-klienten. Den här verktygsuppsättningen innehåller 3 verktyg: Data Box Disk Unlock, Data Box Disk Validation samt Data Box Disk Split Copy. 

    I den här proceduren använder du endast verktyget Data Box Disk Unlock. De andra två verktygen används senare.

    > [!div class="nextstepaction"]
    > [Ladda ned Data Box Disk-verktyg för Windows](http://aka.ms/databoxdisktoolswin)         

3. Extrahera verktygsuppsättningen på samma dator som du använder för att kopiera data. 
4. Öppna ett kommandotolksfönster eller kör Windows PowerShell som administratör på samma dator.
5. (Valfritt) Kontrollera att den dator som du använder för att låsa upp disken uppfyller operativsystemskraven genom att köra systemkontrollkommandot. Ett exempel på utdata visas nedan. 

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Kör `DataBoxDiskUnlock.exe` och ange nyckeln du hämtade i [Ansluta till diskar och hämta nyckeln](#Connect-to-disks-and-get-the-passkey). Diskens tilldelade enhetsbeteckning visas. Ett exempel på utdata visas nedan.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1
    
    Following volumes are unlocked and verified.
    Volume drive letters: D:
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Upprepa upplåsningsstegen varje gång du sätter tillbaka diskar. Använd kommandot `help` om du behöver hjälp med Data Box Disk-upplåsningsverktyget.   

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>
    
    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help
    
    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.
    
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  
8. Du kan visa innehållet på disken när disken har låsts upp.    

    ![Data Box-diskinnehåll](media/data-box-disk-deploy-set-up/data-box-disk-content.png) 

## <a name="unlock-disks-on-linux-client"></a>Låsa upp diskar på Linux-klient

1. Gå till **Allmänt > Enhetsinformation**. 
2. Ladda ned de Data Box Disk-verktyg som motsvarar Linux-klienten.  

    > [!div class="nextstepaction"]
    > [Ladda ned Data Box Disk-verktyg för Linux](http://aka.ms/databoxdisktoolslinux) 

3. Öppna en terminal på Linux-klienten. Gå till den mapp där du har laddat ned programvaran. Ändra filbehörigheterna så att du kan använda filerna. Ange följande kommando: 

    `chmod +x DataBoxDiskUnlock_x86_64` 
    
    `chmod +x DataBoxDiskUnlock_Prep.sh` 
 
    Ett exempel på utdata visas nedan. När kommandot chmod har körts kan du verifiera att filbehörigheterna har ändrats genom att köra kommandot `ls`. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```
4. Kör skriptet så att det installerar alla binärfiler som behövs för programvaran som låser upp Data Box Disk Unlock. Kör kommandot som rot med hjälp av `sudo`. När binärfilerna har installerats visas ett meddelande om det i terminalen.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Skriptet kontrollerar först om klientdatorn kör ett operativsystem som stöds. Ett exempel på utdata visas nedan. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 
    
        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```
    
 
5. Skriv `y` för att fortsätta installationen. Paketen som skripten installerar är: 
    - **epel-release** – Datalager som innehåller följande tre paket. 
    - **dislocker och fuse-dislocker** – Det här verktyget hjälper till att dekryptera BitLocker-krypterade diskar. 
    - **ntfs-3g** – Paket som hjälper till att montera NTFS-volymer. 
 
    När paketen har installerats visas ett meddelande om det i terminalen.     
    ```
    Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
    ruby-libs.x86 64 0:1.8.7.374-5.el6 
    Complete! 
    Loaded plugins: fastestmirror, refresh-packagekit, security 
    Setting up Remove Process 
    Resolving Dependencies 
    --> Running transaction check 
    ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
    Dependencies Resolved 
    Package        Architecture        Version        Repository        Size 
    Removing:  epel-release        noarch         6-8        @extras        22 k 
    Transaction Summary                                 
    Remove        1 Package(s) 
    Installed size: 22 k 
    Downloading Packages: 
    Running rpmcheckdebug 
    Running Transaction Test 
    Transaction Test Succeeded 
    Running Transaction 
    Erasing : epel-release-6-8.noarch 
    Verifying : epel-release-6-8.noarch 
    Removed: 
    epel-release.noarch 0:6-8 
    Complete! 
    Dislocker is installed by the script. 
    OpenSSL is already installed.
    ```

6. Kör upplåsningsverktyget för Data Box Disk. Ange nyckeln från Azure-portalen som du hämtade i [Ansluta till diskar och hämta nyckeln](#Connect-to-disks-and-get-the-passkey). Du kan också ange en lista med BitLocker-krypterade volymer som ska låsas upp. Nyckeln och volymlistan ska anges inom enkla citattecken. 

    Ange följande kommando.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:’<Your passkey from Azure portal>’ /Volumes:’<list of volumes>’`         

    Exemplet på utdata visas nedan. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:’qwerqwerqwer’ /Volumes:’/dev/sdbl’ 
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Monteringspunkten för volymen som du kan kopiera data för visas.

7. Upprepa upplåsningsstegen varje gång du sätter tillbaka diskar. Använd kommandot `help` om du behöver hjälp med Data Box Disk-upplåsningsverktyget. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Exemplet på utdata visas nedan. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:’<passkey from Azure_portal>’ 
    
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ 
    Example: sudo DataBoxDiskUnlock /PassKey:’passkey’ /Volumes:’/dev/sdbl’ 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. När disken är upplåst kan du gå till monteringspunkten och visa innehållet på disken. Nu är du redo att kopiera data till *BlockBlob*- eller *PageBlob*-mappen. 

    ![Data Box-diskinnehåll](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)

## <a name="next-steps"></a>Nästa steg

I den här självstudien om Azure Data Box Disk har du bland annat lärt dig att:

> [!div class="checklist"]
> * Packa upp en Data Box-disk
> * Ansluta till diskar och hämta nyckeln
> * Låsa upp diskar Windows-klient
> * Låsa upp diskar på Linux-klient


Gå vidare till nästa självstudiekurs och lär dig hur du kopierar data på en Data Box-disk.

> [!div class="nextstepaction"]
> [Kopiera data på en Data Box-disk](./data-box-disk-deploy-copy-data.md)

