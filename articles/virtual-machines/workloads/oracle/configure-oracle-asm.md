---
title: Konfigurera Oracle ASM på en virtuell Azure Linux-dator | Microsoft-dokument
description: Snabbt få Oracle ASM igång i din Azure-miljö.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: mimckitt
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: mimckitt
ms.openlocfilehash: 88fd740cad74b12860b6d367c501ba84635f9592
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263173"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Konfigurera Oracle ASM på en virtuell Azure Linux-dator  

Med virtuella Azure-datorer får du en fullständigt konfigurerbar och flexibel datormiljö. Den här självstudien täcker grundläggande Azure-distribution för virtuella datorer i kombination med installation och konfiguration av Oracle Automated Storage Management (ASM).  Lär dig att:

> [!div class="checklist"]
> * Skapa och ansluta till en virtuell oracle-databas
> * Installera och konfigurera Oracles hantering av automatiserad lagring
> * Installera och konfigurera Oracle Grid-infrastruktur
> * Initiera en Oracle ASM-installation
> * Skapa en Oracle DB som hanteras av ASM


Om du väljer att installera och använda CLI lokalt kräver de här självstudierna att du kör Azure CLI version 2.0.4 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli). 

## <a name="prepare-the-environment"></a>Förbereda miljön

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Du skapar en resursgrupp med kommandot [az group create](/cli/azure/group). En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. I det här exemplet har en resursgrupp med namnet *myResourceGroup* i *regionen eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>Skapa en virtuell dator

Om du vill skapa en virtuell dator baserad på Oracle-databasavbildningen och konfigurera den för att använda Oracle ASM använder du kommandot [az vm create.](/cli/azure/vm) 

I följande exempel skapas en virtuell dator med namnet myVM som är en Standard_DS2_v2 storlek med fyra anslutna datadiskar på 50 GB vardera. Om de inte redan finns på standardnyckelplatsen skapas även SSH-nycklar.  Om du vill använda en specifik uppsättning nycklar använder du alternativet `--ssh-key-value`.  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

När du har skapat den virtuella datorn visar Azure CLI information som liknar följande exempel. Notera värdet `publicIpAddress`för . Du använder den här adressen för att komma åt den virtuella datorn.

   ```output
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

Om du vill skapa en SSH-session med den virtuella datorn och konfigurera ytterligare inställningar använder du följande kommando. Ersätt IP-adressen `publicIpAddress` med värdet för den virtuella datorn.

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Installera Oracle ASM

Så här installerar du Oracle ASM: 

Mer information om hur du installerar Oracle ASM finns i [Oracle ASMLib Downloads for Oracle Linux 6](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html).  

1. Du måste logga in som root för att kunna fortsätta med ASM-installationen:

   ```bash
   sudo su -
   ```
   
2. Kör dessa ytterligare kommandon för att installera Oracle ASM-komponenter:

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Kontrollera att Oracle ASM är installerat:

   ```bash
   rpm -qa |grep oracleasm
   ```

    Utdata för det här kommandot bör innehålla följande komponenter:

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM kräver specifika användare och roller för att fungera korrekt. Följande kommandon skapar de nödvändiga användarkontona och grupperna: 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. Kontrollera att användare och grupper har skapats korrekt:

   ```bash
   id grid
   ```

    Utdata för det här kommandot bör lista följande användare och grupper:

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. Skapa en mapp för *användarrutnätet* och ändra ägaren:

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Konfigurera Oracle ASM

För den här självstudien är *standardanvändaren rutnät* och standardgruppen är *asmadmin*. Se till att *oracle-användaren* är en del av asmadmin-gruppen. Så här konfigurerar du Oracles ASM-installation:

1. Ställa in Oracle ASM-biblioteksdrivrutinen innebär att definiera standardanvändaren (rutnät) och standardgruppen (asmadmin) samt konfigurera enheten för att starta vid start (välj y) och söka efter diskar vid start (välj y). Du måste svara på uppmaningarna från följande kommando:

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   Utdata för det här kommandot bör se ut ungefär som följande, stoppa med uppmaningar som ska besvaras.

    ```output
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

   Utdata för det här kommandot bör se ut ungefär som följande lista över tillgängliga diskar

   ```output
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

3. Formatera disk */dev/sdc* genom att köra följande kommando och svara på uppmaningarna med:
   - *n* för ny partition
   - *p* för primär partition
   - *1* för att välja den första partitionen
   - tryck `enter` på för standard första cylindern
   - tryck `enter` på för standard sista cylindern
   - tryck på *w* för att skriva ändringarna i partitionstabellen  

   ```bash
   fdisk /dev/sdc
   ```
   
   Med hjälp av svaren ovan `fdisk` ska utdata för kommandot se ut så här:

   ```output
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

4. Upprepa föregående `fdisk` kommando `/dev/sdd`för `/dev/sde`, `/dev/sdf`och .

5. Kontrollera diskkonfigurationen:

   ```bash
   cat /proc/partitions
   ```

   Utdata för kommandot ska se ut så här:

   ```output
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

6. Kontrollera Oracles ASM-tjänststatus och starta Oracle ASM-tjänsten:

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   Utdata för kommandot ska se ut så här:

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Skapa Oracle ASM-diskar:

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   Utdata för kommandot ska se ut så här:

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Lista Oracle ASM-diskar:

   ```bash
   service oracleasm listdisks
   ```

   Utdata för kommandot bör lista bort följande Oracle ASM-diskar:

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. Ändra lösenord för rot-, oracle- och rutnätsanvändare. **Anteckna dessa nya lösenord** när du använder dem senare under installationen.

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. Ändra mappbehörigheten:

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

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Ladda ner och förbereda Oracle Grid-infrastruktur

Så här hämtar och förbereder du programvaran för Oracle Grid-infrastruktur:

1. Ladda ner Oracle Grid Infrastruktur från [Oracle ASM nedladdningssida](https://www.oracle.com/technetwork/database/enterprise-edition/downloads/database12c-linux-download-2240591.html). 

   Under nedladdningen med titeln **Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) för Linux x86-64**, ladda ner de två .zip-filerna.

2. När du har hämtat ZIP-filerna till klientdatorn kan du använda SCP (Secure Copy Protocol) för att kopiera filerna till den virtuella datorn:

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. SSH tillbaka till din Virtuella Oracle-dator i Azure för att flytta ZIP-filerna till mappen /opt. Ändra sedan filägaren:

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. Packa upp filerna. (Installera Verktyget För uppstallning av Linux om det inte redan är installerat.)

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. Ändra behörighet:

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. Uppdatera konfigurerat växlingsutrymme. Oracle Grid-komponenter behöver minst 6,8 GB växlingsutrymme för att installera Grid. Standardswapfilstorleken för Oracle Linux-avbildningar i Azure är bara 2048 MB. Du måste `ResourceDisk.SwapSizeMB` öka `/etc/waagent.conf` i filen och starta om TJÄNSTEN WALinuxAgent för att de uppdaterade inställningarna ska börja gälla. Eftersom det är en skrivskyddad fil måste du ändra filbehörigheter för att aktivera skrivåtkomst.

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   Sök `ResourceDisk.SwapSizeMB` efter och ändra värdet till **8192**. Du måste trycka `insert` för att gå in i infogningsläge, `esc` skriva in värdet **8192** och sedan trycka för att återgå till kommandoläge. Om du vill skriva ändringarna `:wq` och `enter`avsluta filen skriver du och trycker på .
   
   > [!NOTE]
   > Vi rekommenderar starkt att `WALinuxAgent` du alltid använder för att konfigurera växlingsutrymme så att det alltid skapas på den lokala efemära disken (tillfällig disk) för bästa prestanda. Mer information om finns i [Så här lägger du till en växlingsfil i virtuella Linux Azure-datorer](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines).

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>Förbereda din lokala klient och virtuella datorer för att köra x11
Konfigurera Oracle ASM kräver ett grafiskt gränssnitt för att slutföra installationen och konfigurationen. Vi använder x11-protokollet för att underlätta denna installation. Om du använder ett klientsystem (Mac eller Linux) som redan har X11-funktioner aktiverade och konfigurerade - kan du hoppa över den här konfigurationen och installationen exklusivt för Windows-datorer. 

1. [Ladda ner PuTTY](https://www.putty.org/) och [ladda ner Xming](https://xming.en.softonic.com/) till din Windows-dator. Du måste slutföra installationen av båda dessa program med standardvärdena innan du fortsätter.

2. När du har installerat PuTTY öppnar du en kommandotolk, byter till PuTTY-mappen (till `puttygen.exe` exempel C:\Program Files\PuTTY) och kör för att generera en nyckel.

3. I PuTTY nyckel generera:
   
   1. Generera en nyckel genom `Generate` att välja knappen.
   2. Kopiera innehållet på nyckeln (Ctrl+C).
   3. Välj knappen `Save private key`.
   4. Ignorera varningen om att skydda nyckeln med en `OK`lösenfras och välj sedan .

   ![Skärmbild av PuTTY Key Generator](./media/oracle-asm/puttykeygen.png)

4. Kör dessa kommandon i den virtuella datorn:

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. Skapa en fil som heter `authorized_keys`. Klistra in innehållet i nyckeln i den här filen och spara sedan filen.

   > [!NOTE]
   > Nyckeln måste innehålla `ssh-rsa`strängen . Dessutom måste innehållet i nyckeln vara en enda textrad.
   >  

6. Starta PuTTY på ditt klientsystem. Gå till **Anslutning** > **SSH** > **Auth**i **kategorifönstret** . Bläddra till nyckeln som du genererade tidigare i rutan **Privat nyckelfil för autentisering.**

   ![Skärmbild av SSH-autentiseringsalternativen](./media/oracle-asm/setprivatekey.png)

7. Gå till **Anslutning** > **SSH** > **X11**i **kategorifönstret** . Markera kryssrutan **Aktivera X11-vidarebefordran.**

   ![Skärmbild av SSH X11-vidarekopplingsalternativen](./media/oracle-asm/enablex11.png)

8. Gå till **Session**i **kategorifönstret** . Ange din Virtuella `<publicIPaddress>` Oracle ASM-dator i dialogrutan `Saved Session` Värdnamn, fyll `Save`i ett nytt namn och klicka sedan på .  När du har `open` sparat, klicka på för att ansluta till din Oracle ASM virtuell dator.  Första gången du ansluter varnas fjärrsystemet inte cachelagras i registret. Klicka `yes` på för att lägga till den och fortsätt.

   ![Skärmbild av PuTTY-sessionsalternativen](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Installera Oracle Grid-infrastruktur

Så här installerar du Oracle Grid Infrastructure:

1. Logga in som **rutnät**. (Du bör kunna logga in utan att bli tillfrågad om ett lösenord.) 

   > [!NOTE]
   > Om du kör Windows kontrollerar du att du har startat Xming innan du påbörjar installationen.

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid Infrastructure 12c Release 1 Installer öppnas. (Det kan ta några minuter innan installationsprogrammet startar.)

2. På sidan **Välj installationsalternativ** väljer du **Installera och Konfigurera Oracle Grid-infrastruktur för en fristående server**.

   ![Skärmbild av installationsans välj installationsalternativ](./media/oracle-asm/install01.png)

3. På sidan **Välj produktspråk** kontrollerar du att **engelska** eller det språk du vill använda är valt.  Klicka på `next`.

4. På sidan **Skapa ASM-diskgrupp:**
   - Ange ett namn på diskgruppen.
   - Under **Redundans**väljer du **Extern**.
   - Under **Allokeringsenhetsstorlek**väljer du **4**.
   - Under **Lägg till diskar**väljer du **ORCLASMSP**.
   - Klicka på `next`.

5. På sidan **Ange ASM-lösenord** väljer du alternativet **Använd samma lösenord för dessa konton** och anger ett lösenord.

   ![Skärmbild av installationsskärmens sida Ange ASM-lösenord](./media/oracle-asm/install04.png)

6. På sidan **Ange hanteringsalternativ** har du möjlighet att konfigurera EM Cloud Control. Vi hoppar över det `next` här alternativet - klicka för att fortsätta. 

7. Använd standardinställningarna på sidan **Privilegierade operativsystemgrupper.** Klicka `next` här om du vill fortsätta.

8. Använd standardinställningarna på sidan **Ange installationsplats.** Klicka `next` här om du vill fortsätta.

9. På sidan **Skapa lager** ändrar `/u01/app/grid/oraInventory`du lagerkatalogen till . Klicka `next` här om du vill fortsätta.

   ![Skärmbild av installationssidan Skapa lager](./media/oracle-asm/install08.png)

10. Markera kryssrutan **Kör konfigurationsskript automatiskt** på sidan Konfigurationsskript för **rotskript.** Välj sedan alternativet **Använd "root"-användarautentiseringsuppgifter** och ange rotanvändarlösenordet.

    ![Skärmbild av konfigurationssidan för installationsprogrammets konfiguration av root-skript](./media/oracle-asm/install09.png)

11. På sidan **Utför nödvändiga kontroller** misslyckas den aktuella inställningen med fel. Detta är ett förväntat beteende. Välj `Fix & Check Again`.

12. Klicka på `OK`i dialogrutan **Fixup Script** .

13. Granska **Summary** de markerade inställningarna på sidan `Install`Sammanfattning och klicka sedan på .

    ![Skärmbild av installationskontots sammanfattningssida](./media/oracle-asm/install12.png)

14. En varningsdialogruta visas som informerar dig om att konfigurationsskript måste köras som en privilegierad användare. Klicka `Yes` här om du vill fortsätta.

15. Klicka på slutför `Close` installationen på sidan **Slutför.**

## <a name="set-up-your-oracle-asm-installation"></a>Konfigurera din Oracle ASM-installation

Så här konfigurerar du Oracles ASM-installation:

1. Se till att du fortfarande är inloggad som **rutnät**från din X11-session. Du kan behöva `enter` slå för att återuppliva terminalen. Starta sedan konfigurationsassistenten för Oracles konfiguration för automatiserad lagring:

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracles ASM-konfigurationsassistent öppnas.

2. Klicka på knappen i `Create` dialogrutan **Konfigurera ASM: Diskgrupper** och klicka sedan på `Show Advanced Options`.

3. I dialogrutan **Skapa diskgrupp:**

   - Ange diskgruppsnamnet **DATA**.
   - Under **Välj medlemsdiskar**väljer du **ORCL_DATA** och **ORCL_DATA1**.
   - Under **Allokeringsenhetsstorlek**väljer du **4**.
   - Klicka `ok` här om du vill skapa diskgruppen.
   - Klicka `ok` här om du vill stänga bekräftelsefönstret.

   ![Skärmbild av dialogrutan Skapa diskgrupp](./media/oracle-asm/asm02.png)

4. Klicka på knappen i `Create` dialogrutan **Konfigurera ASM: Diskgrupper** och klicka sedan på `Show Advanced Options`.

5. I dialogrutan **Skapa diskgrupp:**

   - Ange namnet på diskgruppen **FRA**.
   - Under **Redundans**väljer du **Extern (ingen)**.
   - Under **Välj Medlemsdiskar**väljer du **ORCL_FRA**.
   - Under **Allokeringsenhetsstorlek**väljer du **4**.
   - Klicka `ok` här om du vill skapa diskgruppen.
   - Klicka `ok` här om du vill stänga bekräftelsefönstret.

   ![Skärmbild av dialogrutan Skapa diskgrupp](./media/oracle-asm/asm04.png)

6. Välj **Avsluta** för att stänga ASM-konfigurationsassistenten.

   ![Skärmbild av dialogrutan Konfigurera ASM: Diskgrupper med knappen Avsluta](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>Skapa databasen

Oracle-databasprogramvaran är redan installerad på Azure Marketplace-avbildningen. Så här skapar du en databas:

1. Växla användare till Oracle-superanvändaren och initiera sedan lyssnaren för loggning:

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Konfigurationsassistenten för databasen öppnas.

2. Klicka på `Create Database`på på sidan **Databasåtgärd.**

3. På sidan **Skapande läge:**

   - Ange ett namn på databasen.
   - För **lagringstyp**kontrollerar du att **ASM (Automatic Storage Management)** har valts.
   - För **plats för databasfiler**använder du den föreslagna standardplatsen för ASM.
   - För **snabbt återställningsområde**använder du den föreslagna standardplatsen för ASM.
   - skriva in ett **administrativt lösenord** och **bekräfta lösenord**.
   - se `create as container database` till att den är vald.
   - skriva in `pluggable database name` ett värde.

4. På sidan **Sammanfattning** granskar du de markerade `Finish` inställningarna och klickar sedan på för att skapa databasen.

   ![Skärmbild av sammanfattningssidan](./media/oracle-asm/createdb03.png)

5. Databasen har skapats. På sidan **Slutför** kan du välja att låsa upp ytterligare konton för att använda databasen och ändra lösenorden. Om du vill göra det väljer du `close` **Lösenordshantering** - annars klickar du på .

## <a name="delete-the-vm"></a>Ta bort den virtuella datorn

Du har konfigurerat Oracle Automated Storage Management på Oracle DB-avbildningen från Azure Marketplace.  När du inte längre behöver den här virtuella datorn kan du använda följande kommando för att ta bort resursgruppen, den virtuella datorn och alla relaterade resurser:

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

[Självstudiekurs: Konfigurera Oracle DataGuard](configure-oracle-dataguard.md)

[Självstudiekurs: Konfigurera Oracle GoldenGate](Configure-oracle-golden-gate.md)

Granska [Arkitekt en Oracle DB](oracle-design.md)
