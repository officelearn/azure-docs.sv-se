---
title: Återställa filer på en virtuell dator med Azure Backup
description: Lär dig att utföra återställningar på filnivå på en virtuell Azure-dator med Backup och Recovery Services.
services: backup
author: rayne-wiselman
manager: carmonm
tags: azure-resource-manager, virtual-machine-backup
ms.service: backup
ms.topic: tutorial
ms.date: 2/14/2018
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: a8f9fd5a81f8e95d9787d9800be9962484e8436a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871863"
---
# <a name="restore-files-to-a-virtual-machine-in-azure"></a>Återställa filer till en virtuell dator i Azure
Med Azure Backup skapas återställningspunkter som lagras i geo-redundanta återställningsvalv. När du återställer från en återställningspunkt kan du återställa hela den virtuella datorn eller enskilda filer. Den här artikeln beskriver hur du återställer enskilda filer. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * lista och välja återställningspunkter
> * ansluta en återställningspunkt till en virtuell dator
> * återställa filer från en återställningspunkt.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.18 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa informationen i [Installera Azure CLI](/cli/azure/install-azure-cli). 


## <a name="prerequisites"></a>Nödvändiga komponenter
För den här självstudiekursen måste du ha en virtuell Linux-dator som har skyddats med Azure Backup. Du simulerar en oavsiktlig filborttagning och återställning genom att ta bort en sida från en webbserver. Om du behöver en virtuell Linux-dator som kör en webbserver som skyddas med Azure Backup kan du läsa mer i [Säkerhetskopiera en virtuell dator i Azure med CLI](quick-backup-vm-cli.md).


## <a name="backup-overview"></a>Översikt över Backup
När Azure initierar en säkerhetskopiering tar tillägget på den virtuella datorn en ögonblicksbild. Säkerhetskopieringstillägget installeras på den virtuella datorn när den första säkerhetskopieringen begärs. Azure Backup kan också ta en ögonblicksbild av det underliggande lagringsutrymmet om den virtuella datorn inte körs när säkerhetskopieringen sker.

Som standard skapar Azure Backup en filsystemkonsekvent säkerhetskopia. När Azure Backup har tagit ögonblicksbilden överförs data till Recovery Services-valvet. För att maximera effektiviteten identifierar och överför Azure Backup endast de datablock som har ändrats sedan föregående säkerhetskopia.

När dataöverföringen har slutförts tas ögonblicksbilden bort och en återställningspunkt skapas.


## <a name="delete-a-file-from-a-vm"></a>Ta bort en fil från en virtuell dator
Om du av misstag tar bort eller gör ändringar i en fil kan du återställa enskilda filer från en återställningspunkt. Med den här processen kan du bläddra bland filerna som säkerhetskopierats i en återställningspunkt och sedan återställa endast de filer du behöver. I det här exemplet tar vi bort en fil från en webbserver för att demonstrera återställningsprocessen på filnivå.

1. Anslut till den virtuella datorn genom att hämta dess IP-adress med [az vm show](/cli/azure/vm?view=azure-cli-latest#az-vm-show):

     ```azurecli-interactive
     az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
     ```

2. Kontrollera att webbplatsen fungerar för närvarande genom att öppna en webbläsare på den virtuella datorns offentliga IP-adress. Lämna webbläsarfönstret öppen.

    ![Standardversion av NGINX-webbsida](./media/tutorial-restore-files/nginx-working.png)

3. Anslut till den virtuella datorn med SSH. Ersätt *publicIpAddress* med den offentliga IP-adress du hämtade i föregående kommando:

    ```bash
    ssh publicIpAddress
    ```

4. Ta bort standardsidan från servern vid */var/www/html/index.nginx-debian.html* på följande sätt:

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```

5. Uppdatera webbsidan i webbläsaren. Webbplatsen läser inte längre in sidan, som i följande exempel:

    ![NGINX-webbplats läser inte längre in standardsida](./media/tutorial-restore-files/nginx-broken.png)

6. Stäng SSH-sessionen till den virtuella datorn på följande sätt:

    ```bash
    exit
    ```


## <a name="generate-file-recovery-script"></a>Generera filåterställningsskript
För att återställa filerna tillhandahåller Azure Backup ett skript som ska köras på den virtuella datorn, som ansluter återställningspunkten som en lokal enhet. Du kan bläddra i den här lokala enheten, återställa filer till den virtuella datorn och sedan koppla bort återställningspunkten. Azure Backup fortsätter att säkerhetskopiera dina data baserat på den angivna principen för schema och kvarhållning.

1. Visa en lista med återställningspunkterna för den virtuella datorn med [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list). I det här exemplet väljer vi den senaste återställningspunkten för den virtuella datorn med namnet *myVM* som är skyddad i *myRecoveryServicesVault*:

    ```azurecli-interactive
    az backup recoverypoint list \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --query [0].name \
        --output tsv
    ```

2. Hämta skriptet som ansluter, eller monterar, återställningspunkten till den virtuella datorn med [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp). I följande exempel hämtas skriptet för en virtuell dator med namnet *myVM* som är skyddad i *myRecoveryServicesVault*.

    Ersätt *myRecoveryPointName* med namnet på den återställningspunkt som du fick med det tidigare kommandot:

    ```azurecli-interactive
    az backup restore files mount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

    Skriptet laddas ned och ett lösenord visas som i följande exempel:

    ```
    File downloaded: myVM_we_1571974050985163527.sh. Use password c068a041ce12465
    ```

3. Använd SCP (Secure Copy) när du överför skriptet till den virtuella datorn. Ange namnet på det nedladdade skriptet och ersätt *publicIpAddress* med den virtuella datorns offentliga IP-adress. Se till att du får med det avslutande `:` i slutet av SCP-kommandot enligt följande:

    ```bash
    scp myVM_we_1571974050985163527.sh 52.174.241.110:
    ```


## <a name="restore-file-to-your-vm"></a>Återställa filen till den virtuella datorn
När återställningsskriptet har kopierats till den virtuella datorn kan du nu ansluta återställningspunkten och återställa filer.

1. Anslut till den virtuella datorn med SSH. Ersätt *publicIPAddress* med den virtuella datorns offentliga IP-adress enligt följande:

    ```bash
    ssh publicIpAddress
    ```

2. Lägg till körbehörighet med **chmod** så att skriptet kan köras korrekt. Ange namnet på ditt eget skript:

    ```bash
    chmod +x myVM_we_1571974050985163527.sh
    ```

3. Montera återställningspunkten genom att köra skriptet. Ange namnet på ditt eget skript:

    ```bash
    ./myVM_we_1571974050985163527.sh
    ```

    När skriptet har körts uppmanas du att ange ett lösenord för att komma åt återställningspunkten. Ange lösenordet som visas i utdata från det tidigare kommandot [az backup restore files mount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-mount-rp) som genererade återställningsskriptet.

    Utdata från skriptet ger dig sökvägen till återställningspunkten. Följande exempel visar att återställningspunkten är monterad på */home/azureuser/myVM-20170919213536/Volume1*:

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

4. Använd **cp** för att kopiera NGINX-standardwebbsidan från den monterade återställningspunkten tillbaka till filens ursprungliga plats. Ersätt monteringspunkten */home/azureuser/myVM-20170919213536/Volume1* med din egen plats:

    ```bash
    sudo cp /home/azureuser/myVM-20170919213536/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```

6. Uppdatera webbsidan i webbläsaren. Nu läser webbplatsen in sidan korrekt igen, som i följande exempel:

    ![NGINX-webbplats läses nu in korrekt](./media/tutorial-restore-files/nginx-restored.png)

7. Stäng SSH-sessionen till den virtuella datorn på följande sätt:

    ```bash
    exit
    ```

8. Demontera återställningspunkten från den virtuella datorn med [az backup restore files unmount-rp](https://docs.microsoft.com/cli/azure/backup/restore/files?view=azure-cli-latest#az-backup-restore-files-unmount-rp). I följande exempel demonteras återställningspunkten från den virtuella datorn med namnet *myVM* i *myRecoveryServicesVault*.

    Ersätt *myRecoveryPointName* med namnet på din återställningspunkt som du fick i tidigare kommandon:
    
    ```azurecli-interactive
    az backup restore files unmount-rp \
        --resource-group myResourceGroup \
        --vault-name myRecoveryServicesVault \
        --container-name myVM \
        --item-name myVM \
        --rp-name myRecoveryPointName
    ```

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen anslöt du en återställningspunkt till en virtuell dator och återställde filer för en webbserver. Du har lärt dig att:

> [!div class="checklist"]
> * lista och välja återställningspunkter
> * ansluta en återställningspunkt till en virtuell dator
> * återställa filer från en återställningspunkt.

Gå vidare till nästa självstudiekurs där du får lära hur du säkerhetskopierar Windows Server till Azure.

> [!div class="nextstepaction"]
> [Säkerhetskopiera Windows Server till Azure](tutorial-backup-windows-server-to-azure.md)

