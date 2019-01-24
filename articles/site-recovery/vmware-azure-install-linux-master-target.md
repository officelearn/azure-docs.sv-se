---
title: Installera en Linux-huvudmålserver för återställning efter fel på en lokal plats | Microsoft Docs
description: Lär dig hur du konfigurerar en Linux-huvudmålserver för återställning efter fel på en lokal plats under haveriberedskap för virtuella VMware-datorer till Azure med hjälp av Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 7ff73051abc5168b4dc3852a9008adc22d7d56c6
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54811867"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Installera en Linux-huvudmålserver för återställning efter fel
När du växlar över dina virtuella datorer till Azure kan du återställa dit de virtuella datorerna till den lokala platsen. För att återställa, måste du återaktivera skyddet för den virtuella datorn från Azure till den lokala platsen. För den här processen behöver du ett lokala huvudmålservern tar emot trafiken. 

Om den skyddade virtuella datorn är en Windows-dator, måste ett huvudmål för Windows. För en Linux-dator behöver du ett Linux-huvudmål. Läs följande om du vill veta hur du skapar och installerar ett Linux-huvudmål.

> [!IMPORTANT]
> Från och med lanseringen av 9.10.0 huvudmålservern, senaste huvudmålservern kan endast installeras på en Ubuntu 16.04-server. Nya installationer är inte tillåtna på CentOS6.6 servrar. Du kan dock fortsätta att uppgradera din gamla huvudnyckeln målservrar med hjälp av 9.10.0 version.
> Huvudmålservern på LVM stöds inte.

## <a name="overview"></a>Översikt
Den här artikeln innehåller anvisningar för hur du installerar en Linux-huvudmålserver.

Skicka kommentarer eller frågor i slutet av den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Förutsättningar

* Avgör om återställningen ska användas för att en befintlig lokal virtuell dator eller till en ny virtuell dator för att välja den värd som du kan distribuera Huvudmålet. 
    * Värden för Huvudmålet ska ha åtkomst till datalager för den virtuella datorn för en befintlig virtuell dator.
    * Om den lokala virtuella datorn inte finns (när det gäller alternativa Platsåterställningen), skapas den virtuella datorn som redundansväxlas på samma värddator som Huvudmålet. Du kan välja valfri ESXi-värd för att installera Huvudmålet.
* Huvudmålet ska vara i ett nätverk som kan kommunicera med processervern och konfigurationsservern.
* Versionen av Huvudmålet måste vara lika med eller tidigare än versionerna av processervern och konfigurationsservern. Till exempel om versionen av konfigurationsservern är 9.4, kan versionen av Huvudmålet vara 9.4 eller 9.3 men inte 9.5.
* Huvudmålet kan bara vara en virtuell VMware-dator och inte en fysisk server.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Ändra storlek på riktlinjer för att skapa huvudmålservern

Skapa Huvudmålet i enlighet med följande riktlinjer för storlek:
- **RAM**: 6 GB eller mer
- **Storlek på operativsystemdisk**: 100 GB eller mer (för att installera OS)
- **Ytterligare storleken för kvarhållningsenhet**: 1 TB
- **CPU-kärnor**: 4 kärnor eller mer

Följande stöds Ubuntu-kärnor stöds.


|Kernel-serien  |Stöd för upp till  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Distribuera huvudmålservern

### <a name="install-ubuntu-16042-minimal"></a>Installera Ubuntu 16.04.2 Minimal

Vidta följande steg för att installera Ubuntu 16.04.2 64-bitars operativsystem.

1.   Gå till den [nedladdningslänk](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso), och välj den närmaste spegling anddownload en Ubuntu 16.04.2 minimal 64-bitars ISO.
Håll en Ubuntu 16.04.2 minimal 64-bitars ISO i DVD-enheten och startas.

1.  Välj **engelska** som önskat språk och välj sedan **RETUR**.
    
    ![Välj ett språk](./media/vmware-azure-install-linux-master-target/image1.png)
1. Välj **installerar Ubuntu Server**, och välj sedan **RETUR**.

    ![Välj installera Ubuntu-Server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Välj **engelska** som önskat språk och välj sedan **RETUR**.

    ![Välj engelska som språk du föredrar](./media/vmware-azure-install-linux-master-target/image3.png)

1. Välj lämpligt alternativ från den **tidszon** listan över distributionsalternativ och välj sedan **RETUR**.

    ![Välj rätt tidszon](./media/vmware-azure-install-linux-master-target/image4.png)

1. Välj **nr** (standardalternativet) och välj sedan **RETUR**.

     ![Konfigurera tangentbordet](./media/vmware-azure-install-linux-master-target/image5.png)
1. Välj **engelska (US)** som ursprungslandet tangentbord och välj sedan **RETUR**.

1. Välj **engelska (US)** som tangentbordslayout och välj sedan **RETUR**.

1. Ange värdnamnet för din server i den **värdnamn** och väljer sedan **Fortsätt**.

1. Ange användarnamnet för att skapa ett användarkonto, och välj sedan **Fortsätt**.

      ![Skapa ett användarkonto](./media/vmware-azure-install-linux-master-target/image9.png)

1. Ange lösenordet för det nya användarkontot och välj sedan **Fortsätt**.

1.  Bekräfta lösenordet för den nya användaren och väljer sedan **Fortsätt**.

    ![Bekräfta lösenord](./media/vmware-azure-install-linux-master-target/image11.png)

1.  I nästa valet för att kryptera din hemkatalog väljer **nr** (standardalternativet) och välj sedan **RETUR**.

1. Om den tidszon som visas är korrekt, Välj **Ja** (standardalternativet) och välj sedan **RETUR**. Om du vill konfigurera om din tidszon, Välj **nr**.

1. Alternativen partitionering metod väljer **guidad - använda hela disken**, och välj sedan **RETUR**.

     ![Välj metodalternativet partitionering](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Välj lämplig disk från den **väljer disk till partition** alternativ och välj sedan **RETUR**.

    ![Välj disken](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Välj **Ja** att skriva ändringar till hårddisken och välj sedan **RETUR**.

    ![Välj standardalternativet](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  I Konfigurera proxy markeringen, väljer du alternativet **Fortsätt**, och välj sedan **RETUR**.
     
     ![Välj hur du hanterar uppgraderingar](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Välj **inga automatiska uppdateringar** i markeringen för att hantera uppgraderingar på datorn och välj sedan **RETUR**.

     ![Välj hur du hanterar uppgraderingar](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Eftersom Azure Site Recovery huvudmålservern kräver en mycket specifik version av Ubuntu, måste du se till att kernel uppgraderingar har inaktiverats för den virtuella datorn. Om de är aktiverade kan orsaka några vanliga uppgraderingar huvudmålservern inte fungerar korrekt. Kontrollera att du väljer den **inga automatiska uppdateringar** alternativet.

1.  Välj standardalternativen. Om du vill openSSH för SSH, Välj den **OpenSSH-server** och välj sedan **Fortsätt**.

    ![Välj program](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Välj i markering för att installera startprogrammet GRUB **Ja**, och välj sedan **RETUR**.
     
    ![GRUB-Start installer](./media/vmware-azure-install-linux-master-target/image20.png)


1. Välj enheten som är lämpliga för boot loader installation (helst **/dev/sda**), och välj sedan **RETUR**.
     
    ![Välj lämplig enhet](./media/vmware-azure-install-linux-master-target/image21.png)

1. Välj **Fortsätt**, och välj sedan **RETUR** att slutföra installationen.

    ![Slut installationen](./media/vmware-azure-install-linux-master-target/image22.png)

1. När installationen är klar kan du logga in till den virtuella datorn med de nya autentiseringsuppgifterna. (Se **steg 10** för mer information.)

1. Använd stegen som beskrivs i följande skärmbild och ange ROTEN användarlösenord. Logga sedan in som rotanvändare.

    ![Ange ROTEN användarlösenord](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurera datorn som en huvudmålserver

Att hämta ID för varje SCSI-hårddisk i en Linux-dator, den **disk. EnableUUID = TRUE** parametern måste vara aktiverat. Om du vill aktivera den här parametern gör du följande:

1. Stäng av den virtuella datorn.

2. Högerklicka på posten för den virtuella datorn i den vänstra rutan och välj sedan **redigera inställningar för**.

3. Välj den **alternativ** fliken.

4. I den vänstra rutan väljer **Avancerat** > **Allmänt**, och välj sedan den **konfigurationsparametrar** knappen i den nedre högra delen av skärmen.

    ![Öppna konfigurationsparameter](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Den **konfigurationsparametrar** alternativet är inte tillgängligt när datorn körs. Stäng av den virtuella datorn för att aktivera den här fliken.

5. Se om en rad med **disk. EnableUUID** finns redan.

    - Om värdet finns och är inställd på **FALSKT**, ändra värdet till **SANT**. (Värdena är inte skiftlägeskänsliga.)

    - Om värdet finns och är inställd på **SANT**väljer **Avbryt**.

    - Om värdet inte finns, väljer **Lägg till rad**.

    - Lägg till i namnkolumnen **disk. EnableUUID**, och sedan ange värdet till **SANT**.

    ![Kontrollerar om disken. EnableUUID finns redan](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Inaktivera kernel-uppgraderingar

Azure Site Recovery-huvudmålservern kräver en viss version av Ubuntu, se till att kernel-uppgraderingar är inaktiverat för den virtuella datorn. Om kernel-uppgraderingar är aktiverade, kan det orsaka huvudmålservern inte fungerar korrekt.

#### <a name="download-and-install-additional-packages"></a>Ladda ned och installera ytterligare paket

> [!NOTE]
> Se till att du har Internetanslutning för att ladda ned och installera ytterligare paket. Om du inte har Internetanslutning kan behöva du hitta dessa Deb-paket och installera dem manuellt.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Hämta installationsprogrammet för installation

Om din huvudmålservern är ansluten till Internet, kan du kan använda följande steg för att hämta installationsprogrammet. Annars kan du kopiera installationsprogrammet från processervern och sedan installera den.

#### <a name="download-the-master-target-installation-packages"></a>Ladda ned Installationspaketen huvudmålservern

[Ladda ned de senaste nyheterna för Linux huvudmålservern installation](https://aka.ms/latestlinuxmobsvc).

För att hämta den med hjälp av Linux, skriver du:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Se till att du hämta och packa upp installationsprogrammet i din arbetskatalog. Om du zippa upp till **/usr/Local**, misslyckas installationen.


#### <a name="access-the-installer-from-the-process-server"></a>Få åtkomst till installationsprogrammet från processervern

1. På processervern, går du till **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Kopiera den nödvändiga installer-fil från processervern och spara den som **latestlinuxmobsvc.tar.gz** i din arbetskatalog.


### <a name="apply-custom-configuration-changes"></a>Tillämpa ändringar för anpassad konfiguration

Om du vill använda anpassade konfigurationsändringar, använder du följande steg:


1. Kör följande kommando för att untar binärfilen.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Skärmbild av kommandot ska köras](./media/vmware-azure-install-linux-master-target/image16.png)

2. Kör följande kommando för att ge behörighet.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Kör följande kommando för att köra skriptet.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Kör skriptet bara en gång på servern. Sedan stänga av servern. Starta om servern när du lägger till en disk, enligt beskrivningen i nästa avsnitt.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Lägg till en disk för kvarhållning Linux master target-datorn

Använd följande steg för att skapa en kvarhållningsdisken:

1. Koppla en ny 1 TB disk till Linux master target-datorn och sedan starta datorn.

2. Använd den **multipath -lla** kommando för att lära dig multipath-ID: T kvarhållningsdisken: **multipath -lla**

    ![Flera sökvägar ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formatera enheten och sedan skapa ett filsystem på den nya enheten: **mkfs.ext4 /dev/mapper/< multipath kvarhållning disk-id >**.
    
    ![Filsystem](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. När du har skapat filsystemet montera kvarhållningsdisken.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Skapa den **fstab** posten för att montera kvarhållningsenhetens varje gång systemet startar.
    
    `vi /etc/fstab`
    
    Välj **infoga** att börja redigera filen. Skapa en ny rad och Lägg till följande text. Redigera diskens multipath ID baserat på det markerade multipath ID från föregående kommando.

     **/dev/mapper/ <Retention disks multipath id> /mnt/kvarhållning ext4 rw 0 0**

    Välj **Esc**, och skriv sedan **: wq** (skriva och avsluta) att Stäng editor-fönstret.

### <a name="install-the-master-target"></a>Installera Huvudmålet

> [!IMPORTANT]
> Versionen av huvudmålservern måste vara lika med eller tidigare än versionerna av processervern och konfigurationsservern. Om inte det här villkoret är uppfyllt, återaktivering av skydd lyckas, men kan slutföras inte.


> [!NOTE]
> Innan du installerar huvudmålservern kontrollerar du att den **/etc/hosts** filen på den virtuella datorn innehåller poster som mappar det lokala värdnamnet till IP-adresser som är associerade med alla nätverkskort.

1. Kopiera lösenfrasen från **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** på konfigurationsservern. Spara den som **passphrase.txt** i samma lokala katalog genom att köra följande kommando:

    `echo <passphrase> >passphrase.txt`

    Exempel: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Anteckna konfigurationsserverns IP-adress. Kör följande kommando för att installera huvudmålservern och registrera servern med konfigurationsservern.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exempel: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Vänta tills skriptet är färdigt. Om du registrerar Huvudmålet, Huvudmålet finns på den **Site Recovery-infrastruktur** i portalen.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Installera Huvudmålet genom att använda interaktiv installation

1. Kör följande kommando för att installera huvudmålservern. Agent-rollen, välja **huvudmålservern**.

    ```
    ./install
    ```

2. Standardplatsen för installation och sedan väljer **RETUR** att fortsätta.

    ![Välja en standardplatsen för installation av huvudmålservern](./media/vmware-azure-install-linux-master-target/image17.png)

När installationen är klar kan du registrera konfigurationsservern med hjälp av kommandoraden.

1. Observera IP-adressen för konfigurationsservern. Du behöver det i nästa steg.

2. Kör följande kommando för att installera huvudmålservern och registrera servern med konfigurationsservern.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exempel: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Vänta tills skriptet är färdigt. Om huvudmålservern har registrerats ser Huvudmålet finns på den **Site Recovery-infrastruktur** i portalen.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Installera VMware-verktyg / öppen-vm-tools på huvudmålservern

Du måste installera VMware-verktyg eller verktyg för öppen virtuella datorer på Huvudmålet så att den kan identifiera datalagren. Om verktygen inte är installerade visas återaktivering av skydd skärmen inte i datalager. Du måste starta om efter installationen av VMware-verktyg.

### <a name="upgrade-the-master-target-server"></a>Uppgradera huvudmålservern

Kör installationsprogrammet. Den identifierar automatiskt att agenten är installerad på Huvudmålet. Om du vill uppgradera, Välj **Y**.  När installationen har slutförts kan du kontrollera vilken version av Huvudmålet installeras med hjälp av följande kommando:

`cat /usr/local/.vx_version`


Du kan nu se att den **Version** fält har det lägre versionsnumret för Huvudmålet.

## <a name="common-issues"></a>Vanliga problem

* Kontrollera att du inte aktiverar lagrings-vMotion på alla komponenterna, till exempel ett huvudmål. Om Huvudmålet flyttar efter en lyckad återaktivering av skydd, kan du inte kopplas från virtuella diskar (vmdk: er). I det här fallet misslyckas återställning efter fel.

* Huvudmålet ska inte ha några ögonblicksbilder på den virtuella datorn. Om det finns ögonblicksbilder, misslyckas återställning efter fel.

* På grund av vissa anpassade konfigurationer för NIC, nätverksgränssnittet är inaktiverat under starten och huvudmålservern agenten kan inte initiera. Kontrollera att följande egenskaper är korrekt inställda. Kontrollera de här egenskaperna i Ethernet kortet filens /etc/sysconfig/network-scripts/ifcfg-eth *.
    * BOOTPROTO=dhcp
    * ONBOOT = Ja


## <a name="next-steps"></a>Nästa steg
När installationen och registreringen av Huvudmålet är klar kan du se Huvudmålet visas på den **huvudmålservern** i avsnittet **Site Recovery-infrastruktur**, under konfigurationen Översikt över Server.

Du kan gå vidare med [återaktiveringen av skyddet](vmware-azure-reprotect.md), följt av återställning efter fel.

