---
title: "Återställer filer till en virtuell dator med Azure Backup | Microsoft Docs"
description: "Lär dig mer om att utföra återställningar på filnivå på en virtuell Azure-dator med säkerhetskopiering och Recovery Services."
services: backup, virtual-machines
documentationcenter: virtual-machines
author: markgalioto
manager: carmonm
editor: 
tags: azure-resource-manager, virtual-machine-backup
ms.assetid: 
ms.service: backup, virtual-machines
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/29/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: e1bbae56b70c50fcf691db47efd9dc587686e7da
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2017
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Återställ filer till en virtuell dator i Azure
Azure-säkerhetskopiering skapar återställningspunkter som är lagrade i geo-redundant recovery-valv. När du återställer från en återställningspunkt kan du återställa hela VM eller enskilda filer. Den här artikeln beskriver hur du kan återställa enskilda filer. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Listan och väljer återställningspunkter
> * Ansluta en återställningspunkt till en virtuell dator
> * Återställa filer från en återställningspunkt

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Krav
Den här kursen kräver en Linux VM som har skyddats med Azure Backup. Om du vill simulera en oavsiktlig filborttagning och återställningen kan du ta bort en sida från en webbserver. Om du behöver en Linux VM som kör en webbserver och som har skyddats med Azure Backup finns [säkerhetskopiera en virtuell dator i Azure med CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Översikt över Backup
När Azure initierar en säkerhetskopiering, tar en ögonblicksbild av en tidpunkt i säkerhetskopiering tillägget på den virtuella datorn. Sekundär anknytning installeras på den virtuella datorn när den första säkerhetskopieringen har begärts. Azure Backup kan också ta en ögonblicksbild av det underliggande lagringsutrymmet om den virtuella datorn inte körs när säkerhetskopieringen sker.

Som standard tar Azure Backup en konsekvent filsystemsäkerhetskopia. När Azure Backup tar ögonblicksbilden, överförs data till Recovery Services-valvet. För att maximera effektiviteten, Azure Backup identifierar och överför endast block av data som har ändrats sedan den tidigare säkerhetskopian.

När dataöverföringen har slutförts ögonblicksbilden tas bort och skapa en återställningspunkt.


## <a name="delete-a-file-from-a-vm"></a>Ta bort en fil från en virtuell dator
Om du tar bort av misstag eller göra ändringar i en fil, kan du återställa enskilda filer från en återställningspunkt. Den här processen kan du bläddra till filerna säkerhetskopieras i en återställningspunkt och återställer endast de filer som du behöver. I det här exemplet tar vi bort en fil från en webbserver för att demonstrera filnivå återställningsprocessen.

1. Om du vill ansluta till den virtuella datorn få IP-adressen för den virtuella datorn med [az vm visa](/cli/azure/vm?view=azure-cli-latest#az_vm_show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Öppna en webbläsare till den offentliga IP-adressen på den virtuella datorn för att bekräfta att webbplatsen fungerar för närvarande. Lämna webbläsarfönstret öppen.

    ![Standard NGINX-webbsida](./media/tutorial-restore-files/nginx-working.png)

3. Ansluta till den virtuella datorn med SSH. Ersätt *publicIpAddress* med offentliga IP-adress som du hämtade i föregående kommando:

    ```bash
    ssh publicIpAddress
    ```

4. Ta bort sidan från servern vid */var/www/html/index.nginx-debian.html* på följande sätt:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Uppdatera webbsidan i webbläsaren. Webbplatsen laddas inte längre sidan som visas i följande exempel:

    ![NGINX-webbplats laddas inte längre standardsida](./media/tutorial-restore-files/nginx-broken.png)

6. Stäng SSH-session till den virtuella datorn på följande sätt:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Generera återställningsskript
Om du vill återställa filer, tillhandahåller Azure Backup ett skript köras på den virtuella datorn som ansluter återställningspunkten som en lokal enhet. Du kan bläddra lokala enheten, återställa filer till Virtuellt datorn och sedan koppla från återställningspunkten. Azure-säkerhetskopiering fortsätter att säkerhetskopiera dina data baserat på den tilldelade principen för schema och lagring.

1. Att lista återställningspunkter för den virtuella datorn, Använd [az säkerhetskopiering recoverypoint listan](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az_backup_recoverypoint_list). I det här exemplet väljer vi den senaste återställningspunkten för den virtuella datorn med namnet *myVM* som är skyddad i *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Hämta skriptet som ansluter eller monterar återställningspunkten till den virtuella datorn med [az återställningsverktyg filer montera rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp). I följande exempel hämtar skriptet för den virtuella datorn med namnet *myVM* som är skyddad i *myRecoveryServicesVault*.

    Ersätt *myRecoveryPointName* med namnet på den återställningspunkt som du fick i det föregående kommandot:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Ladda ned skriptet och ett lösenord visas som i följande exempel:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Använda säkra kopia (SCP) för att överföra skriptet till den virtuella datorn. Ange namnet på din hämtade skriptet och Ersätt *publicIpAddress* med offentliga IP-adressen för den virtuella datorn. Kontrollera att du inkluderar avslutande `:` i slutet av SCP kommandot på följande sätt:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Återställa filen till den virtuella datorn
Du kan nu ansluta återställningspunkten och återställa filer med skriptet Systemåterställning kopieras till den virtuella datorn.

1. Ansluta till den virtuella datorn med SSH. Ersätt *publicIpAddress* med offentliga IP-adressen för den virtuella datorn på följande sätt:

    ```bash
    ssh publicIpAddress
    ```

2. Lägg till om du vill att skriptet ska köras korrekt körbehörighet med **chmod**. Ange namnet på ett eget skript:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Kör skriptet för att montera återställningspunkten. Ange namnet på ett eget skript:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    När skriptet körs, uppmanas du att ange ett lösenord för att komma åt återställningspunkten. Ange lösenordet som visas i utdata från den tidigare [az återställningsverktyg filer montera rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_mount_rp) kommando som genererade skriptet återställning.

    Utdata från skriptet ger sökvägen för återställningspunkten. Följande exempel visas att återställningspunkten är monterad på */home/azureuser/myVM-20170919213536/Volume1*:

    ```
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : c068a041ce12465
    
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
    
    ************ Volumes of the recovery point and their mount paths on this machine ************
    
    Sr.No.  |  Disk  |  Volume  |  MountPath
    
    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170919213536/Volume1
    
    ************ Open File Explorer to browse for files. ************
    ```

4. Använd **cp** kopiera NGINX standardwebbsidan från den monterade återställningspunkten tillbaka till den ursprungliga platsen. Ersätt den */home/azureuser/myVM-20170919213536/Volume1* monteringspunkt med din egen plats:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. Uppdatera webbsidan i webbläsaren. Webbplatsen nu läses in korrekt igen, som visas i följande exempel:

    ![NGINX webbplats nu läses in korrekt](./media/tutorial-restore-files/nginx-restored.png)

7. Stäng SSH-session till den virtuella datorn på följande sätt:

    ```bash
    exit
    ```

8. Demontera återställningspunkten från den virtuella datorn med [az återställningsverktyg filer demontera rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az_backup_restore_files_unmount_rp). I följande exempel demonterar återställningspunkten från den virtuella datorn med namnet *myVM* i *myRecoveryServicesVault*.

    Ersätt *myRecoveryPointName* med namnet på återställningspunkten som du fick i de föregående kommandona:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen ansluten en återställningspunkt till en virtuell dator och återställa filer för en webbserver. Du har lärt dig att:

> [!div class="checklist"]
> * Listan och väljer återställningspunkter
> * Ansluta en återställningspunkt till en virtuell dator
> * Återställa filer från en återställningspunkt

Gå vidare till nästa kurs att lära dig hur du säkerhetskopierar Windows Server till Azure.

> [!div class="nextstepaction"]
> [Säkerhetskopiera Windows Server till Azure](tutorial-backup-windows-server-to-azure.md)

