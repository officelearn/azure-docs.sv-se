---
title: "Ställa in Oracle ASM på en virtuell Azure Linux-dator | Microsoft Docs"
description: "Snabbt Oracle ASM upp och körs i Azure-miljön."
services: virtual-machines-linux
documentationcenter: virtual-machines
author: RicksterCDN
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/19/2017
ms.author: rclaus
ms.openlocfilehash: 117212a2e7e3da7c3e249798eec804a652e0ef58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Ställa in Oracle ASM på en virtuell Azure Linux-dator  

Virtuella datorer i Azure ger en fullständigt konfigurerbara och flexibel datormiljö. Den här självstudiekursen beskriver distributionen av grundläggande Azure virtuella datorer i kombination med installation och konfiguration av Oracle Automated Storage Management (ASM).  Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell dator i Oracle-databas
> * Installera och konfigurera automatisk lagringshantering för Oracle
> * Installera och konfigurera infrastrukturen för Oracle rutnätet
> * Initiera en Oracle ASM-installation
> * Skapa en Oracle-databas som hanteras av ASM


[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda CLI lokalt kursen krävs att du använder Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI 2.0]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Förbereda miljön

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du kan skapa en resursgrupp med det [az gruppen skapa](/cli/azure/group#create) kommando. En Azure-resursgrupp är en logisk behållare i vilka Azure resurser distribueras och hanteras. I det här exemplet en resursgrupp med namnet *myResourceGroup* i den *eastus* region.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator baserat på bilden Oracle-databas och konfigurera den för användning av Oracle ASM, Använd den [az vm skapa](/cli/azure/vm#create) kommando. 

I följande exempel skapas en virtuell dator med namnet myVM som har en Standard_DS2_v2 storlek med fyra bifogade datadiskar på 50 GB. Om de inte redan finns på standardplatsen för nyckeln, skapas också SSH-nycklar.  Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

När du har skapat den virtuella datorn, visar Azure CLI information som liknar följande exempel. Anteckna värdet för `publicIpAddress`. Du kan använda den här adressen för att få åtkomst till den virtuella datorn.

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

Om du vill skapa en SSH-session med den virtuella datorn och konfigurera ytterligare inställningar, använder du följande kommando. Ersätt IP-adressen med den `publicIpAddress` värde för den virtuella datorn.

```bash 
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installera Oracle ASM

Utför följande steg för att installera Oracle ASM. 

Mer information om hur du installerar Oracle ASM finns [Oracle ASMLib laddar ned för Oracle Linux 6](http://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Du måste logga in som rot för att kunna fortsätta med installationen ASM:

   ```bash
   sudo su -
   ```
   
2. Kör dessa ytterligare kommandon för att installera komponenter för Oracle ASM:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget http://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Kontrollera att Oracle ASM är installerad:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Utdata från kommandot ska visa en lista med följande komponenter:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM kräver särskilda användare och roller för att fungera korrekt. Följande kommandon skapar förutvärdering användarkonton och grupper: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Kontrollera att användare och grupper har skapats på rätt sätt:

   ```bash
   id grid
   ```

    Utdata från kommandot ska visa en lista med följande användare och grupper:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Skapa en mapp för användaren *rutnätet* och ändra ägare:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Ställ in Oracle ASM

Den här självstudien standardanvändaren är *rutnätet* och standardgruppen är *asmadmin*. Se till att den *oracle* användaren tillhör gruppen asmadmin. Ställ in Oracle ASM-installation, utföra följande steg:

1. Ställa in Oracle ASM biblioteket drivrutinen innebär att definiera standardanvändaren (rutnätet) och standardgruppen (asmadmin) samt hur du konfigurerar enheten att starta vid start (Välj y) och för att söka efter diskar på Start (Välj y). Du behöver för att besvara anvisningarna från följande kommando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Utdata från kommandot ska se ut ungefär så här, stoppas med uppmanar som ska besvaras.

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

   Kommandots utdata bör likna följande lista över tillgängliga diskar

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

3. Formatera disk */dev/sdc* genom att köra följande kommando och svara på dem med:
   - *n*för nya partitionen
   - *p* för primär partition
   - *1* att välja den första partitionen
   - Tryck på `enter` för standard första cylinder
   - Tryck på `enter` för standard senaste cylinder
   - Tryck på *w* att skriva ändringar till partitionstabellen  

   ```bash
   fdisk /dev/sdc
   ```
   
   Med de svar som anges ovan bör utdata för kommandot fdisk ut ungefär så här:

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

   Resultatet av kommandot ska se ut ungefär så här:

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

6. Kontrollera status för Oracle ASM-tjänsten och starta tjänsten Oracle ASM:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Resultatet av kommandot ska se ut ungefär så här:
   
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

   Resultatet av kommandot ska se ut ungefär så här:

   ```bash
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Visa en lista med Oracle ASM diskar:

   ```bash
   service oracleasm listdisks
   ```   

   Resultatet av kommandot ska lista av följande Oracle ASM diskar:

   ```bash
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Ändra lösenord för användare rot, oracle och rutnätet. **Anteckna dessa nya lösenord** som du använder dem senare under installationen.

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Hämta och förbereda infrastrukturen för Oracle-rutnätet

Du kan hämta och förbereder du programvaran Oracle rutnätet infrastruktur gör du följande:

1. Hämta Oracle rutnätet infrastruktur från den [hämtningssidan för Oracle ASM](http://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Under nedladdningen med titeln **Oracle-databas 12c Versionsinfrastruktur 1 rutnätet (12.1.0.2.0) för Linux x86-64**, hämta två ZIP-filer.

2. När du har hämtat ZIP-filer till en klientdator, kan du använda säkra kopia Protocol (SCP) för att kopiera filerna till den virtuella datorn:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH tillbaka till din Oracle VM i Azure för att flytta ZIP-filerna till de / opt mapp. Sedan kan ändra ägare för filer:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Packa upp filerna. (Installera Linux packa upp verktyget om den inte redan är installerad.)
   
   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Ändra behörigheter:
   
   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Uppdatera konfigurerats växlingsutrymme. Oracle rutnätet komponenter måste minst 6,8 GB växlingsutrymme installera rutnätet. Standard växlingen filstorleken för Oracle Linux avbildningar i Azure är endast 2 048 MB. Du måste öka `ResourceDisk.SwapSizeMB` i den `/etc/waagent.conf` filen och starta om tjänsten WALinuxAgent för de uppdaterade inställningarna ska börja gälla. Eftersom det är en skrivskyddad fil, måste du ändra behörigheten för att aktivera skrivåtkomst.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```
   
   Sök efter `ResourceDisk.SwapSizeMB` och ändra värdet till **8192**. Du måste trycka på `insert` Skriv i värdet för att ange infogningsläge **8192** och tryck sedan på `esc` att återgå till kommandoläge. För att skriva ändringar och stäng filen, Skriv `:wq` och tryck på `enter`.
   
   > [!NOTE]
   > Vi rekommenderar starkt att du alltid använder `WALinuxAgent` att konfigurera växlingsutrymme så att den alltid har skapats på den lokala tillfälliga disken (tillfällig disk) för bästa prestanda. Mer information om finns [hur du lägger till en växlingsfil på Linux Azure-datorer](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Förbered din lokala klient och VM för att köra x11
Konfigurera Oracle ASM kräver ett grafiskt gränssnitt för att slutföra installationen och konfigurationen. Vi använder x11 protokoll för att underlätta installationen. Om du använder ett klientsystem (Mac eller Linux) som redan har X11 funktioner aktiverat och konfigurerat – du kan hoppa över den här konfigurationen och installationen exklusiv till Windows-datorer. 

1. [Hämta PuTTY](http://www.putty.org/) och [hämta Xming](https://xming.en.softonic.com/) till Windows-datorn. Du måste slutföra installationen av båda dessa program med standardvärden innan du fortsätter.

2. När du har installerat PuTTY, öppna Kommandotolken, ändra till mappen PuTTY (till exempel C:\Program Files\PuTTY) och kör `puttygen.exe` för att generera en nyckel.

3. I PuTTY Nyckelgenerator:
   
   1. Generera en nyckel genom att välja den `Generate` knappen.
   2. Kopiera innehållet i nyckeln (Ctrl + C).
   3. Välj den `Save private key` knappen.
   4. Ignorera varningen om att skydda nyckeln med en lösenfras och välj sedan `OK`.

   ![Skärmbild av PuTTY Nyckelgenerator](./media/oracle-asm/puttykeygen.png)

4. I den virtuella datorn köra följande kommandon:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Skapa en fil med namnet `authorized_keys`. Klistra in innehållet i nyckeln i den här filen och spara sedan filen.

   > [!NOTE]
   > Nyckeln måste innehålla strängen `ssh-rsa`. Innehållet i nyckeln måste också vara en textrad.
   >  

6. Starta PuTTY på din klientsystemet. I den **kategori** rutan, gå till **anslutning** > **SSH** > **Auth**. I den **fil för privat nyckel för autentisering** rutan, bläddra till den nyckel som du skapade tidigare.

   ![Skärmbild av alternativ för SSH-autentisering](./media/oracle-asm/setprivatekey.png)

7. I den **kategori** rutan, gå till **anslutning** > **SSH** > **X11**. Välj den **aktivera X11 vidarebefordran** kryssrutan.

   ![Skärmbild av SSH X11 vidarebefordran alternativ](./media/oracle-asm/enablex11.png)

8. I den **kategori** rutan, gå till **Session**. Ange den virtuella datorn ASM Oracle `<publicIPaddress>` i dialogrutan värden namn fyller du i en ny `Saved Session` namn och klicka sedan på `Save`.  När du sparat klickar du på `open` att ansluta till den virtuella datorn för Oracle ASM.  Första gången du ansluter varnas du fjärrsystemet inte cachelagras i registret. Klicka på `yes` att lägga till den och fortsätta.

   ![Skärmbild av alternativen PuTTY-session](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installera infrastrukturen för Oracle-rutnätet

Om du vill installera infrastrukturen för Oracle-rutnätet, gör du följande:

1. Logga in som **rutnätet**. (Du ska kunna logga in utan att ange ett lösenord.) 

   > [!NOTE]
   > Om du kör Windows, kontrollera att du har startat Xming innan du påbörjar installationen.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle rutnätet infrastruktur 12c version 1 Installer öppnas. (Det kan ta några minuter för att kunna starta.)

2. På den **väljer installationsalternativet** väljer **installera och konfigurera infrastrukturen för Oracle rutnät för en fristående Server**.

   ![Skärmbild av installationsprogrammets väljer installationsalternativet sida](./media/oracle-asm/install01.png)

3. På den **Välj språk för produkten** ser du till **engelska** eller det språk som du vill att markeras.  Klicka på `next`.

4. På den **skapa ASM diskgruppen** sidan:
   - Ange ett namn för diskgruppen.
   - Under **redundans**väljer **externa**.
   - Under **storlek på allokeringsenhet**väljer **4**.
   - Under **Lägg till diskar**väljer **ORCLASMSP**.
   - Klicka på `next`.

5. På den **ange ASM lösenord** väljer den **använda samma lösenord för dessa konton** alternativet och ange ett lösenord.

   ![Skärmbild av lösenordssidan för installationsprogrammets ange ASM](./media/oracle-asm/install04.png)

6. På den **ange alternativ för hantering av** , har möjlighet att konfigurera EM molnet kontroll. Vi vill hoppa över det här alternativet – Klicka på `next` att fortsätta. 

7. På den **Privilegierade Operativsystemgrupper** använder standardinställningarna. Klicka på `next` att fortsätta.

8. På den **ange installationsplatsen** använder standardinställningarna. Klicka på `next` att fortsätta.

9. På den **skapa** ändrar inventering katalog `/u01/app/grid/oraInventory`. Klicka på `next` att fortsätta.

   ![Skärmbild av sidan för den installationsprogrammet skapa](./media/oracle-asm/install08.png)

10. På den **rot skript körning configuration** väljer den **kör automatiskt konfigurationsskript** kryssrutan. Markera den **använder ”rot” användarens autentiseringsuppgifter** alternativet och ange rotlösenordet för användaren.

    ![Skärmbild av konfigurationssidan för installationsprogrammets rot skript för körning](./media/oracle-asm/install09.png)

11. På den **utföra nödvändiga kontrollerar** sidan nuvarande konfiguration kommer att misslyckas med fel. Det här är ett förväntat beteende. Välj `Fix & Check Again`.

12. I den **korrigering skriptet** dialogrutan klickar du på `OK`.

13. På den **sammanfattning** sidan Granska de angivna inställningarna och klicka sedan på `Install`.

    ![Skärmbild av installationsprogrammets sammanfattningssida](./media/oracle-asm/install12.png)

14. Ett varningsmeddelande visas informera du konfigurationen skript måste köras som en Privilegierade användare. Klicka på `Yes` att fortsätta.

15. På den **Slutför** klickar du på `Close` att slutföra installationen.

## <a name="set-up-your-oracle-asm-installation"></a>Ställ in Oracle ASM-installation

Ställ in Oracle ASM-installation, utföra följande steg:

1. Se till att du fortfarande är inloggad som **rutnätet**, från din X11 session. Du kan behöva nådde `enter` till revive terminalen. Starta i Oracle Automated Storage Management Configuration assistenten:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM Configuration Assistant öppnas.

2. I den **konfigurera ASM: diskgrupper** dialogrutan klickar du på den `Create` knappen och klicka sedan på `Show Advanced Options`.

3. I den **skapa diskgruppen** dialogrutan:

   - Ange namnet på disken **DATA**.
   - Under **Välj medlem diskar**väljer **ORCL_DATA** och **ORCL_DATA1**.
   - Under **storlek på allokeringsenhet**väljer **4**.
   - Klicka på `ok` att skapa diskgruppen.
   - Klicka på `ok` att stänga bekräftelsefönstret.

   ![Skärmbild av dialogrutan Skapa diskgruppen](./media/oracle-asm/asm02.png)

4. I den **konfigurera ASM: diskgrupper** dialogrutan klickar du på den `Create` knappen och klicka sedan på `Show Advanced Options`.

5. I den **skapa diskgruppen** dialogrutan:

   - Ange namnet på disken **FRA**.
   - Under **redundans**väljer **externt (ingen)**.
   - Under **Välj medlem diskar**väljer **ORCL_FRA**.
   - Under **storlek på allokeringsenhet**väljer **4**.
   - Klicka på `ok` att skapa diskgruppen.
   - Klicka på `ok` att stänga bekräftelsefönstret.

   ![Skärmbild av dialogrutan Skapa diskgruppen](./media/oracle-asm/asm04.png)

6. Välj **avsluta** att stänga ASM Configuration Installationsassistenten.

   ![Skärmbild av Konfigurera ASM: Disk-grupper i dialogrutan Avsluta](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Skapa en databas

Oracle-databas som är installerad på Azure Marketplace-avbildning. Om du vill skapa en databas, gör du följande:

1. Växla användare till Oracle-superanvändare och sedan initiera lyssnaren för loggning:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```
   Databasen Configuration Assistant öppnas.

2. På den **Databasåtgärden** klickar du på `Create Database`.

3. På den **läget Skapa** sidan:

   - Ange ett namn för databasen.
   - För **lagringstyp**, se till att **automatisk hantering av lagring (ASM)** är markerad.
   - För **filer databasplatsen**, använder du standard ASM förslag på plats.
   - För **snabb återställning området**, använder du standard ASM förslag på plats.
   - Ange en **administratörslösenord** och **Bekräfta lösenord**.
   - Se till att `create as container database` är markerad.
   - Ange en `pluggable database name` värde.

4. På den **sammanfattning** sidan Granska de angivna inställningarna och klicka sedan på `Finish` att skapa databasen.

   ![Skärmbild av sidan Sammanfattning](./media/oracle-asm/createdb03.png)

5. Databasen har skapats. På den **Slutför** sida har du möjlighet att låsa upp ytterligare konton om du vill använda den här databasen och ändra lösenord. Om du vill göra det väljer **lösenordshantering** -Annars klickar du på `close`.

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Du har konfigurerat Oracle Automated lagringshantering på Oracle-DB-avbildning från Azure Marketplace.  Du kan använda följande kommando för att ta bort resursgruppen, virtuell dator och alla relaterade resurser när du inte längre behöver den här virtuella datorn:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudier: Konfigurera Oracle DataGuard](configure-oracle-dataguard.md)

[Självstudier: Konfigurera Oracle GoldenGate](Configure-oracle-golden-gate.md)

Granska [skapa en Oracle-databas](oracle-design.md)
