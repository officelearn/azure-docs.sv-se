---
title: Konfigurera Oracle ASM på en virtuell Azure Linux-dator | Microsoft Docs
description: Snabbt Oracle ASM dig och kom igång med Azure-miljön.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 8cf977f9c5bbf10c6a4d862a29fda98d3ce71844
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755704"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Konfigurera Oracle ASM på en virtuell Linux-dator för Azure  

Med virtuella Azure-datorer får du en fullständigt konfigurerbar och flexibel datormiljö. Den här självstudien beskriver grundläggande Azure VM-distribution som kombineras med installation och konfiguration av Oracle automatiserad Storage Management (ASM).  Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell Oracle Database dator
> * Installera och konfigurera Oracle automatisk lagringshantering
> * Installera och konfigurera infrastrukturen för Oracle-rutnät
> * Initiera en Oracle ASM-installation
> * Skapa en Oracle-databas som hanteras av ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Förbereda miljön

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du skapar en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk behållare där Azure resurser distribueras och hanteras. I det här exemplet, en resursgrupp med namnet *myResourceGroup* i den *eastus* region.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator baserat på avbildningen som Oracle Database och konfigurera den om du vill använda Oracle ASM genom att använda den [az vm skapa](/cli/azure/vm) kommando. 

I följande exempel skapas en virtuell dator med namnet myVM som är en Standard_DS2_v2 storlek med fyra anslutna datadiskar på 50 GB. Om de inte redan finns på standardplatsen för nyckeln, skapas även SSH-nycklar.  Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

När du har skapat den virtuella datorn visar Azure CLI information som liknar följande exempel. Anteckna värdet för `publicIpAddress`. Du kan använda den här adressen för att få åtkomst till den virtuella datorn.

   ```azurecli
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>Anslut till VM:en

För att skapa en SSH-session med den virtuella datorn och konfigurera ytterligare inställningar, använder du följande kommando. Ersätt IP-adressen med den `publicIpAddress` värde för den virtuella datorn.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installera Oracle ASM

Utför följande steg för att installera Oracle ASM. 

Läs mer om hur du installerar Oracle ASM [Oracle ASMLib hämtningar för Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Du måste logga in som rot för att kunna fortsätta med ASM-installation:

   ```bash
   sudo su -
   ```
   
2. Kör dessa ytterligare kommandon för att installera Oracle ASM-komponenter:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Kontrollera att Oracle ASM har installerats:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Resultatet av det här kommandot bör innehålla följande komponenter:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM kräver särskilda användare och roller för att fungera korrekt. Följande kommandon för skapar nödvändiga användarkonton och grupper: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Verifiera användare och grupper har skapats korrekt:

   ```bash
   id grid
   ```

    Resultatet av det här kommandot bör innehålla följande användare och grupper:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Skapa en mapp för användaren *grid* och ändra ägare:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Konfigurera Oracle ASM

Den här självstudien standardanvändaren är *grid* och standardgruppen är *asmadmin*. Se till att den *oracle* användare är medlem i gruppen asmadmin. Om du vill konfigurera Oracle ASM-installationen, gör du följande:

1. Konfigurera Oracle ASM-biblioteket drivrutinen innebär att definiera standardanvändaren (rutnät) och standard-gruppen (asmadmin) samt konfigurera enheten att starta vid start (Välj y) och för att söka efter diskar vid start (Välj y). Du måste svara på frågorna i följande kommando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Kommandots utdata bör se ut ungefär så här, stoppas med uppmanar som ska besvaras.

    ```bash
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. Visa diskkonfigurationen:
   ```bash
   cat /proc/partitions
   ```

   Kommandots utdata bör likna följande listor över tillgängliga diskar

   ```bash
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. Formatera disk */dev/sdc* genom att köra följande kommando och svara på frågorna med:
   - *n* för ny partition
   - *p* för primär partition
   - *1* att välja den första partitionen
   - Tryck på `enter` för standard första 3D-cylinder
   - Tryck på `enter` för standard senaste 3D-cylinder
   - Tryck på *w* att skriva ändringar till partitionstabellen  

   ```bash
   fdisk /dev/sdc
   ```
   
   Med hjälp av de svar som anges ovan, det bör se ut fdisk kommandots utdata som liknar följande:

   ```bash
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. Upprepa det föregående kommandot fdisk för `/dev/sdd`, `/dev/sde`, och `/dev/sdf`.

5. Kontrollera diskkonfigurationen:

   ```bash
   cat /proc/partitions
   ```

   Kommandots utdata bör se ut så här:

   ```bash
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Kontrollera status för Oracle ASM-tjänsten och starta Oracle ASM-tjänsten:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Kommandots utdata bör se ut så här:
   
   ```bash
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Skapa Oracle ASM diskar:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```    

   Kommandots utdata bör se ut så här:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Lista över Oracle ASM diskar:

   ```bash
   service oracleasm listdisks
   ```   

   Kommandots utdata bör lista ut följande Oracle ASM diskar:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Ändra lösenord för användare rot, oracle och rutnät. **Anteckna dessa nya lösenord** som du använder dem senare under installationen.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Ändra behörigheter för mappen:

   ```bash
   chmod -R 775 /opt 
   chown grid:oinstall /opt 
   chown oracle:oinstall /dev/sdc1 
   chown oracle:oinstall /dev/sdd1 
   chown oracle:oinstall /dev/sde1 
   chown oracle:oinstall /dev/sdf1 
   chmod 600 /dev/sdc1 
   chmod 600 /dev/sdd1 
   chmod 600 /dev/sde1 
   chmod 600 /dev/sdf1
   ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Ladda ned och förbereda infrastrukturen för Oracle-rutnät

Om du vill hämta och Förbered Grid infrastruktur för Oracle-programvara, gör du följande:

1. Ladda ned Oracle Grid infrastruktur från den [Oracle ASM hämtningssidan](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Under nedladdningen benämnt **Oracle Database 12c version 1 Grid infrastruktur (12.1.0.2.0) för Linux x86-64**, ladda ned två ZIP-filer.

2. Du kan använda protokollet SCP (Secure Copy) för att kopiera filerna till den virtuella datorn när du har hämtat .zip-filer till en klientdator:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH till din Oracle VM i Azure för att flytta ZIP-filerna till den / opt mapp. Sedan kan ändra ägaren till filerna:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Packa upp filerna. (Installera Linux packa upp verktyget om den inte redan är installerat.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Ändra behörigheter:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Uppdateringen som konfigurerats växlingsutrymme i procent. Oracle Grid komponenter måste minst 6,8 GB växlingsutrymme för att installera rutnätet. Filstorleken för standard-växling för Oracle Linux-avbildningar i Azure är bara 2 048 MB. Du måste öka `ResourceDisk.SwapSizeMB` i den `/etc/waagent.conf` filen och starta om tjänsten WALinuxAgent för de uppdaterade inställningarna ska börja gälla. Eftersom det är en skrivskyddad fil, måste du ändra behörigheten för att aktivera skrivåtkomst.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Sök efter `ResourceDisk.SwapSizeMB` och ändra värdet till **8192**. Behöver du trycker på `insert` Skriv i värdet för att ange infogningsläge **8192** och tryck sedan på `esc` att återgå till kommandoläge. För att skriva ändringar och avsluta filen, skriver `:wq` och tryck på `enter`.
   
   > [!NOTE]
   > Vi rekommenderar starkt att du alltid använder `WALinuxAgent` konfigurera växlingsutrymme så att den alltid har skapats på den lokala tillfälliga disken (tillfällig disk) för bästa prestanda. Läs mer på [hur du lägger till en växlingsfil på Linux Azure-datorer](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Förbereda lokal klient och virtuell dator för att köra x11
Konfigurera Oracle ASM kräver ett grafiskt gränssnitt för att slutföra installationen och konfigurationen. Vi använder x11 protokoll för att underlätta installationen. Om du använder ett klientsystem (Mac eller Linux) som redan har X11 funktioner aktiverat och konfigurerat – du kan hoppa över det här konfigurations- och exklusiva till Windows-datorer. 

1. [Ladda ned PuTTY](http://www.putty.org/) och [hämta Xming](https://xming.en.softonic.com/) till din Windows-dator. Du behöver att slutföra installationen av båda dessa program med standardvärden innan du fortsätter.

2. När du har installerat PuTTY, öppna Kommandotolken, ändra till mappen PuTTY (till exempel c:\Program\Microsoft Files\PuTTY) och kör `puttygen.exe` för att generera en nyckel.

3. I PuTTY-Nyckelgenerator:
   
   1. Generera en nyckel genom att välja den `Generate` knappen.
   2. Kopiera innehållet i nyckeln (Ctrl + C).
   3. Välj knappen `Save private key`.
   4. Ignorera varningen om hur du skyddar nyckeln med en lösenfras och välj sedan `OK`.

   ![Skärmbild av PuTTY-Nyckelgenerator](./media/oracle-asm/puttykeygen.png)

4. I den virtuella datorn kör du följande kommandon:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Skapa en fil som heter `authorized_keys`. Klistra in innehållet i nyckeln i den här filen och spara filen.

   > [!NOTE]
   > Nyckeln måste innehålla strängen `ssh-rsa`. Innehållet i nyckeln måste dessutom vara en enskild rad med text.
   >  

6. Ditt klientsystem starta PuTTY. I den **kategori** rutan, gå till **anslutning** > **SSH** > **Auth**. I den **fil för privat nyckel för autentisering** rutan, bläddra till den nyckel som du skapade tidigare.

   ![Skärmbild av alternativ för SSH-autentisering](./media/oracle-asm/setprivatekey.png)

7. I den **kategori** rutan, gå till **anslutning** > **SSH** > **X11**. Välj den **aktivera X11 vidarebefordran** markerar du kryssrutan.

   ![Skärmbild av SSH X11 vidarebefordran alternativ](./media/oracle-asm/enablex11.png)

8. I den **kategori** rutan, gå till **Session**. Ange Oracle ASM-VM `<publicIPaddress>` i dialogrutan värden namn fyller du i en ny `Saved Session` namn och klicka sedan på `Save`.  När du har sparat klickar du på `open` att ansluta till din virtuella dator för Oracle ASM.  Första gången du ansluter ett meddelande om fjärrdatorn inte cachelagras i registret. Klicka på `yes` att lägga till den och fortsätta.

   ![Skärmbild av alternativ för PuTTY-sessionen](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installera infrastrukturen för Oracle-rutnät

Om du vill installera Oracle Grid infrastruktur, gör du följande:

1. Logga in som **grid**. (Du bör kunna logga in utan att behöva ange ett lösenord.) 

   > [!NOTE]
   > Om du kör Windows, kontrollera att du har startat Xming innan du påbörjar installationen.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid infrastruktur 12c version 1 installationsprogrammet öppnas. (Det kan ta några minuter att starta installationsprogrammet.)

2. På den **väljer installationsalternativet** väljer **installera och konfigurera Oracle Grid infrastrukturen för en fristående Server**.

   ![Skärmbild av sidan för den installationsprogrammet väljer installationsalternativet](./media/oracle-asm/install01.png)

3. På den **Välj språk för produkten** kontrollerar **engelska** eller det språk som du vill har valts.  Klicka på `next`.

4. På den **skapa ASM diskgruppen** sidan:
   - Ange ett namn för diskgruppen.
   - Under **redundans**väljer **externa**.
   - Under **storlek på allokeringsenhet**väljer **4**.
   - Under **Lägg till diskar**väljer **ORCLASMSP**.
   - Klicka på `next`.

5. På den **ange ASM lösenord** väljer den **använda samma lösenord för dessa konton** alternativet och ange ett lösenord.

   ![Skärmbild av sidan för installationsprogrammets ange ASM-lösenord](./media/oracle-asm/install04.png)

6. På den **ange hanteringsalternativ** sidan har möjlighet att konfigurera EM molnet kontroll. Vi hoppar över det här alternativet – Klicka på `next` att fortsätta. 

7. På den **Privilegierade Operativsystemgrupper** kan du använda standardinställningarna. Klicka på `next` att fortsätta.

8. På den **ange installationsplats** kan du använda standardinställningarna. Klicka på `next` att fortsätta.

9. På den **skapa** , ändra katalogen inventering `/u01/app/grid/oraInventory`. Klicka på `next` att fortsätta.

   ![Skärmbild av sidan för den installationsprogrammet skapa](./media/oracle-asm/install08.png)

10. På den **rotkonfiguration skriptet körning** väljer den **kör automatiskt konfigurationsskript** markerar du kryssrutan. Välj den **använda ”rot” användarens autentiseringsuppgifter** alternativet och ange rotlösenordet för användaren.

    ![Skärmbild av konfigurationssidan för installationsprogrammets rot skriptet körning](./media/oracle-asm/install09.png)

11. På den **utför Kravkontroller** sidan nuvarande konfiguration misslyckas med fel. Det här är ett förväntat beteende. Välj `Fix & Check Again`.

12. I den **korrigering skriptet** dialogrutan klickar du på `OK`.

13. På den **sammanfattning** sidan Granska de angivna inställningarna och klicka sedan på `Install`.

    ![Skärmbild av installationsprogrammets sammanfattningssida](./media/oracle-asm/install12.png)

14. Ett varningsmeddelande visas upplysande du konfigurationen skript måste köras som en privilegierad användare. Klicka på `Yes` att fortsätta.

15. På den **Slutför** klickar du på `Close` att slutföra installationen.

## <a name="set-up-your-oracle-asm-installation"></a>Konfigurera Oracle ASM-installation

Om du vill konfigurera Oracle ASM-installationen, gör du följande:

1. Se till att du fortfarande är inloggad som **grid**, från din X11 session. Du kan behöva når `enter` att återskapa terminalen. Starta sedan den Oracle automatiserad Storage Management Configuration Assistant:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM Configuration Assistant öppnas.

2. I den **konfigurera ASM: Disk grupper** dialogrutan klickar du på den `Create` knappen och klicka sedan på `Show Advanced Options`.

3. I den **skapa diskgruppen** dialogrutan:

   - Ange namnet på disken **DATA**.
   - Under **Välj medlem diskar**väljer **ORCL_DATA** och **ORCL_DATA1**.
   - Under **storlek på allokeringsenhet**väljer **4**.
   - Klicka på `ok` att skapa en disk.
   - Klicka på `ok` att stänga bekräftelsefönstret.

   ![Skärmbild av dialogrutan Skapa diskgruppen](./media/oracle-asm/asm02.png)

4. I den **konfigurera ASM: Disk grupper** dialogrutan klickar du på den `Create` knappen och klicka sedan på `Show Advanced Options`.

5. I den **skapa diskgruppen** dialogrutan:

   - Ange namnet på disken **FRA**.
   - Under **redundans**väljer **externt (ingen)**.
   - Under **Välj medlem diskar**väljer **ORCL_FRA**.
   - Under **storlek på allokeringsenhet**väljer **4**.
   - Klicka på `ok` att skapa en disk.
   - Klicka på `ok` att stänga bekräftelsefönstret.

   ![Skärmbild av dialogrutan Skapa diskgruppen](./media/oracle-asm/asm04.png)

6. Välj **avsluta** att Stäng ASM Configuration Assistant.

   ![Skärmbild av den konfigurerar ASM: Dialogrutan för disk grupper med Stäng-knappen](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Skapa en databas

Oracle-databas som är redan installerad på Azure Marketplace-avbildning. Om du vill skapa en databas, gör du följande:

1. Växla användare till Superanvändare för Oracle och initiera lyssnaren för loggning:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Databasen Configuration Assistant öppnas.

2. På den **Databasåtgärden** klickar du på `Create Database`.

3. På den **läget Skapa** sidan:

   - Ange ett namn för databasen.
   - För **lagringstyp**, se till att **automatisk Storage Management (ASM)** har valts.
   - För **plats för databasfiler**, använder du standardvärdet ASM förslag på plats.
   - För **snabb återställning området**, använder du standardvärdet ASM förslag på plats.
   - Ange en **administratörslösenord** och **Bekräfta lösenord**.
   - Se till att `create as container database` har valts.
   - Ange en `pluggable database name` värde.

4. På den **sammanfattning** sidan Granska de angivna inställningarna och klicka sedan på `Finish` att skapa databasen.

   ![Skärmbild av sidan Sammanfattning](./media/oracle-asm/createdb03.png)

5. Databasen har skapats. På den **Slutför** sidan har möjlighet att låsa upp ytterligare konton om du vill använda den här databasen och ändra lösenord. Om du vill göra det väljer **lösenordshantering** -Klicka annars på `close`.

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Du har konfigurerat automatisk lagringshantering i Oracle i Oracle DB-avbildning från Azure Marketplace.  När du inte längre behöver den här virtuella datorn kan använda du följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudier: Konfigurera Oracle DataGuard](configure-oracle-dataguard.md)

[Självstudier: Konfigurera Oracle GoldenGate](Configure-oracle-golden-gate.md)

Granska [om arkitekturen i en Oracle-databas](oracle-design.md)
