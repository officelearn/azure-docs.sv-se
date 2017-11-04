---
title: "Säkerhetskopiera virtuella Azure Linux-datorer | Microsoft Docs"
description: "Skydda din virtuella Linux-datorer genom att säkerhetskopiera dem med Azure Backup."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: cynthn
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2017
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0e659c1906c99415ab1b53785a606330ef9068c8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="back-up-linux--virtual-machines-in-azure"></a>Säkerhetskopiera virtuella Linux-datorer i Azure

Du kan skydda dina data genom att säkerhetskopiera med jämna mellanrum. Azure-säkerhetskopiering skapar återställningspunkter som är lagrade i geo-redundant recovery-valv. När du återställer från en återställningspunkt kan du återställa hela VM eller bara vissa filer. Den här artikeln förklarar hur du återställer en fil till en Linux-VM kör nginx. Om du inte redan har en virtuell dator ska användas, kan du skapa en med hjälp av den [Linux quickstart](quick-create-cli.md). I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia



## <a name="backup-overview"></a>Översikt över Backup

När tjänsten Azure Backup initierar en säkerhetskopia, utlöser reservanknytning för att ta en ögonblicksbild i tidpunkt. Azure Backup-tjänsten används den _VMSnapshotLinux_ tillägget Linux. Tillägget installeras under den första säkerhetskopieringen om den virtuella datorn körs. Om inte den virtuella datorn körs, tar en ögonblicksbild av det underliggande lagringsutrymmet i Backup-tjänsten (eftersom inga program skrivningar inträffar när den virtuella datorn stoppas).

Standard Azure Backup tar en konsekvent filsystemsäkerhetskopia för Linux VM men den kan konfigureras för att ta [konsekvent säkerhetskopiering av program med skript före och efter skript framework](https://docs.microsoft.com/azure/backup/backup-azure-linux-app-consistent). När Azure Backup-tjänsten tar ögonblicksbilden kan överföra data till valvet. För att maximera effektiviteten, tjänsten identifierar och överför endast block av data som har ändrats sedan den tidigare säkerhetskopian.

När dataöverföringen har slutförts ögonblicksbilden tas bort och skapa en återställningspunkt.


## <a name="create-a-backup"></a>Skapa en säkerhetskopia
Skapa en enkel schemalagd daglig säkerhetskopiering till ett Recovery Services-valv. 

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Välj **Virtuella datorer** på menyn till vänster. 
3. Välj en virtuell dator som du vill säkerhetskopiera i listan.
4. På VM-blad i den **inställningar** klickar du på **säkerhetskopiering**. Den **Aktivera säkerhetskopiering** blad öppnas.
5. I **Recovery Services-valvet**, klickar du på **Skapa nytt** och ange namn för det nya valvet. Ett nytt valv skapas i samma resursgrupp och plats som den virtuella datorn.
6. Klicka på **säkerhetskopiera princip**. I det här exemplet att behålla standardinställningarna och klickar på **OK**.
7. På den **Aktivera säkerhetskopiering** bladet, klickar du på **Aktivera säkerhetskopiering**. Detta skapar en daglig säkerhetskopiering baserat på standardschemat.
10. Skapa en första återställningspunkten på den **säkerhetskopiering** bladet och klickar på **Säkerhetskopiera nu**.
11. På den **säkerhetskopiering nu** bladet klickar du på kalenderikonen, Använd kalender för att välja den sista dagen i den här återställningspunkten behålls Klicka på **säkerhetskopiering**.
12. I den **säkerhetskopiering** bladet för den virtuella datorn, visas antalet återställningspunkter som är klar.

    ![Återställningspunkter](./media/tutorial-backup-vms/backup-complete.png)

Den första säkerhetskopieringen tar ungefär 20 minuter. När säkerhetskopieringen är klar fortsätter du till nästa del av den här kursen.

## <a name="restore-a-file"></a>Återställa en fil

Om du tar bort av misstag eller göra ändringar i en fil, kan du använda filåterställning för att återställa filen från din säkerhetskopieringsvalvet. Filåterställning använder ett skript som körs på den virtuella datorn att montera återställningspunkten som lokal enhet. Dessa enheter förblir monterade 12 timmar så att du kan kopiera filer från återställningspunkten och återställa dem till den virtuella datorn.  

I det här exemplet visar vi hur du återställer standard nginx webbsida /var/www/html/index.nginx-debian.html. Offentliga IP-adressen för våra VM i det här exemplet är *13.69.75.209*. Du kan hitta IP-adressen för din virtuella datorn med hjälp av:

 ```bash 
 az vm show --resource-group myResourceGroup --name myVM -d --query [publicIps] --o tsv
 ```

 
1. Öppna en webbläsare och Skriv i offentliga IP-adressen för den virtuella datorn till standard nginx webbsidan visas på den lokala datorn.

    ![Standard nginx-webbsida](./media/tutorial-backup-vms/nginx-working.png)

1. SSH till den virtuella datorn.

    ```bash
    ssh 13.69.75.209
    ```
2. Ta bort /var/www/html/index.nginx-debian.html.

    ```bash
    sudo rm /var/www/html/index.nginx-debian.html
    ```
    
4. Uppdatera webbläsaren genom att träffa CTRL + F5 för att se den standardsidan nginx är borta på den lokala datorn.

    ![Standard nginx-webbsida](./media/tutorial-backup-vms/nginx-broken.png)
    
1. Logga in på den lokala datorn till den [Azure-portalen](https://portal.azure.com/).
6. Välj **Virtuella datorer** på menyn till vänster. 
7. I listan, väljer du den virtuella datorn.
8. På VM-blad i den **inställningar** klickar du på **säkerhetskopiering**. Den **säkerhetskopiering** blad öppnas. 
9. Välj på menyn längst upp på bladet **filåterställning**. Den **filåterställning** blad öppnas.
10. I **steg 1: Välj återställningspunkt**, Välj en återställningspunkt från listrutan.
11. I **steg 2: hämta skript för att bläddra och återställa filerna**, klicka på den **hämta körbara** knappen. Spara den hämta filen till den lokala datorn.
7. Klicka på **hämta skriptet** att ladda ned skriptfilen lokalt.
8. Öppna Kommandotolken Bash och Skriv följande, där du ersätter *Linux_myVM_05-05-2017.sh* med rätt sökväg och filnamn för det skript som du hämtade *azureuser* med användarnamnet för den virtuella datorn och *13.69.75.209* med offentliga IP-adressen för den virtuella datorn.
    
    ```bash
    scp Linux_myVM_05-05-2017.sh azureuser@13.69.75.209:
    ```
    
9. Öppna en SSH-anslutning till den virtuella datorn på den lokala datorn.

    ```bash
    ssh 13.69.75.209
    ```
    
10. Lägg till på den virtuella datorn kör-behörighet till skriptfilen.

    ```bash
    chmod +x Linux_myVM_05-05-2017.sh
    ```
    
11. Kör skriptet för att montera återställningspunkten som ett filsystem på den virtuella datorn.

    ```bash
    ./Linux_myVM_05-05-2017.sh
    ```
    
12. Utdata från skriptet ger sökvägen för monteringspunkten. Resultatet ser ut ungefär så här:

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
                          
    Connecting to recovery point using ISCSI service...
    
    Connection succeeded!
    
    Please wait while we attach volumes of the recovery point to this machine...
                         
    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath 

    1)  | /dev/sdc  |  /dev/sdc1  |  /home/azureuser/myVM-20170505191055/Volume1

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

12. På den virtuella datorn kopieras nginx standardwebbsidan monteringspunkten tillbaka till där du tagit bort filen.

    ```bash
    sudo cp ~/myVM-20170505191055/Volume1/var/www/html/index.nginx-debian.html /var/www/html/
    ```
    
17. Öppna fliken webbläsare där du är ansluten till IP-adressen för den virtuella datorn som visar sidan nginx standard på den lokala datorn. Tryck på CTRL + F5 för att uppdatera sidan webbläsare. Du bör nu se att sidan fungerar igen.

    ![Standard nginx-webbsida](./media/tutorial-backup-vms/nginx-working.png)

18. På den lokala datorn, gå tillbaka till fliken för Azure-portalen och i **steg3: demontera diskarna efter återställningen** klickar du på den **demontera diskar** knappen. Om du glömmer att göra det här steget är anslutningen till monteringspunkt Stäng automatiskt efter 12 timmar. Du måste hämta ett nytt skript om du vill skapa en ny monteringspunkt efter dessa 12 timmar.


## <a name="next-steps"></a>Nästa steg

I den här självstudiekursen lärde du dig att:

> [!div class="checklist"]
> * Skapa en säkerhetskopia av en virtuell dator
> * Schemalägga en daglig säkerhetskopiering
> * Återställa en fil från en säkerhetskopia

Gå vidare till nästa kurs mer information om övervakning av virtuella datorer.

> [!div class="nextstepaction"]
> [Övervaka virtuella datorer](tutorial-monitoring.md)

