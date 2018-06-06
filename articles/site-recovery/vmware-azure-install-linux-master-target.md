---
title: Installera en Linux-huvudmålserver för redundans från Azure till lokala | Microsoft Docs
description: Innan du skydda en Linux-dator, måste en Linux-huvudmålserver. Lär dig hur du installerar en.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: gauravd
ms.service: site-recovery
ms.topic: article
ms.date: 05/08/2018
ms.author: nisoneji
ms.openlocfilehash: 4d28a9a840b25ea415021da149ab145836cbed28
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802518"
---
# <a name="install-a-linux-master-target-server"></a>Installera en Linux-huvudmålsserver
När du växlar över dina virtuella datorer till Azure kan du växla tillbaka de virtuella datorerna till den lokala platsen. För att växla tillbaka måste att skydda den virtuella datorn från Azure till den lokala platsen. För den här processen behöver du en lokal huvudmålservern för att ta emot trafiken. 

Om den skydda virtuella datorn är en Windows-dator, måste en Windows-huvudmålserver. För en virtuell Linux-dator behöver du en Linux-huvudmålserverns. Läs följande information om hur du skapar och installerar en Linux-huvudmålserverns.

> [!IMPORTANT]
> Från och med lanseringen av 9.10.0 huvudmålservern, senaste huvudmålservern kan endast installeras på en 16.04 Ubuntu server. Nya installationer är inte tillåtna på CentOS6.6 servrar. Du kan dock fortsätta att uppgradera din gamla huvudnyckeln målservrar med hjälp av 9.10.0 version.

## <a name="overview"></a>Översikt
Den här artikeln innehåller anvisningar för hur du installerar en Linux-huvudmålserverns.

Skicka kommentarer eller frågor i slutet av den här artikeln eller på den [Azure Recovery Services-forumet](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Förutsättningar

* Avgör om återställningen kommer att vara till en befintlig lokal virtuell dator eller till en ny virtuell dator för att välja den värd som du vill distribuera huvudmålservern. 
    * För en befintlig virtuell dator ska värden på Huvudmålet ha åtkomst till datalager på den virtuella datorn.
    * Om den lokala virtuella datorn inte finns (vid återställning till alternativ plats), skapas den virtuella datorn för återställning efter fel på samma värddator som huvudmålservern. Du kan välja alla ESXi-värd för att installera huvudmålservern.
* Huvudmålservern ska vara i ett nätverk som kan kommunicera med processervern och konfigurationsservern.
* Versionen av huvudmålservern måste vara lika med eller tidigare versioner av processervern och konfigurationsservern. Till exempel om versionen av konfigurationsservern är 9.4, kan versionen av huvudmålservern vara 9.4 eller 9.3 men inte 9.5.
* Huvudmålservern kan bara finnas en virtuell VMware-dator och inte en fysisk server.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Ändra storlek på riktlinjer för att skapa huvudmålservern

Skapa huvudmålservern i enlighet med riktlinjerna nedan storlek:
- **RAM-minne**: 6 GB eller mer
- **OS-diskstorlek**: 100 GB eller mer (för att installera OS)
- **Ytterligare diskstorleken för kvarhållningsenhetens**: 1 TB
- **CPU-kärnor**: 4 kärnor eller mer

Följande stöds Ubuntu kärnor stöds.


|Kernel-serien  |Stöd för upp till  |
|---------|---------|
|4.4      |4.4.0-81-Generic         |
|4.8      |4.8.0-56-Generic         |
|4.10     |4.10.0-24-Generic        |


## <a name="deploy-the-master-target-server"></a>Distribuera huvudmålservern

### <a name="install-ubuntu-16042-minimal"></a>Installera Ubuntu 16.04.2 Minimal

Vidta följande steg för att installera Ubuntu 16.04.2 64-bitars operativsystem.

1.   Gå till den [Hämta länk](https://www.ubuntu.com/download/server/thank-you?version=16.04.2&architecture=amd64), välja den närmaste spegling anddownload en Ubuntu 16.04.2 minimal 64-bitars ISO.
Behåll en Ubuntu 16.04.2 minimal 64-bitars ISO i DVD-enheten och starta systemet.

1.  Välj **engelska** som önskat språk och välj sedan **RETUR**.
    
    ![Välj ett språk](./media/vmware-azure-install-linux-master-target/image1.png)
1. Välj **installerar Ubuntu Server**, och välj sedan **RETUR**.

    ![Välj installerar Ubuntu Server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Välj **engelska** som önskat språk och välj sedan **RETUR**.

    ![Välj engelska som ditt språk](./media/vmware-azure-install-linux-master-target/image3.png)

1. Välj lämpligt alternativ från den **tidszon** alternativ, och välj **RETUR**.

    ![Välj rätt tidszon](./media/vmware-azure-install-linux-master-target/image4.png)

1. Välj **nr** (standardalternativet) och välj sedan **RETUR**.

     ![Konfigurera tangentbordet](./media/vmware-azure-install-linux-master-target/image5.png)
1. Välj **engelska (USA)** som land för tangentbord och välj sedan **RETUR**.

1. Välj **engelska (USA)** som tangentbordslayout och välj sedan **RETUR**.

1. Ange värdnamnet för servern i den **värdnamn** och välj sedan **Fortsätt**.

1. Ange användarnamnet för att skapa ett användarkonto, och välj sedan **Fortsätt**.

      ![Skapa ett användarkonto](./media/vmware-azure-install-linux-master-target/image9.png)

1. Ange lösenordet för det nya användarkontot och välj sedan **Fortsätt**.

1.  Bekräfta lösenordet för den nya användaren och välj sedan **Fortsätt**.

    ![Bekräfta lösenord](./media/vmware-azure-install-linux-master-target/image11.png)

1.  I nästa markeringen för att kryptera din hemkatalog, Välj **nr** (standardalternativet) och välj sedan **RETUR**.

1. Om den tidszon som visas är korrekt, Välj **Ja** (standardalternativet) och välj sedan **RETUR**. Om du vill konfigurera om tidszonen, Välj **nr**.

1. Välj alternativen partitionering metoden **interaktiv - använder hela disken**, och välj sedan **RETUR**.

     ![Välj metodalternativet partitionering](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Välj lämplig disk från den **väljer disk till partition** alternativ och välj sedan **RETUR**.

    ![Välj disken](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Välj **Ja** att skriva ändringar till disk och välj sedan **RETUR**.

    ![Välj alternativet](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  Konfigurera proxy markering, väljer du alternativet, väljer **Fortsätt**, och välj sedan **RETUR**.
     
     ![Välj hur du hanterar uppgraderingar](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Välj **inga automatiska uppdateringar** i markeringen för att hantera uppgraderingar på datorn och välj sedan **RETUR**.

     ![Välj hur du hanterar uppgraderingar](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Eftersom Azure Site Recovery huvudmålservern kräver en särskild version av Ubuntu, måste du se till att kernel uppgraderingar har inaktiverats för den virtuella datorn. Om de är aktiverade kan orsaka reguljära uppgraderingar huvudmålservern inte fungerar korrekt. Kontrollera att du väljer den **inga automatiska uppdateringar** alternativet.

1.  Välj standardalternativen. Om du vill openSSH för SSH ansluta, Välj den **OpenSSH server** alternativet och välj sedan **Fortsätt**.

    ![Välj program](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. Markera i markering för att installera startprogram GRUB **Ja**, och välj sedan **RETUR**.
     
    ![GRUB Start installer](./media/vmware-azure-install-linux-master-target/image20.png)


1. Välj enheten som är lämpliga för start inläsaren installation (helst **/dev/sda**), och välj sedan **RETUR**.
     
    ![Välj lämplig enhet](./media/vmware-azure-install-linux-master-target/image21.png)

1. Välj **Fortsätt**, och välj sedan **RETUR** att slutföra installationen.

    ![Slut installationen](./media/vmware-azure-install-linux-master-target/image22.png)

1. När installationen är klar kan du logga in på den virtuella datorn med de nya användarautentiseringsuppgifterna för. (Se **steg 10** för mer information.)

1. Använd de steg som beskrivs i följande skärmbild och ange ROTEN användarens lösenord. Logga in som rotanvändare.

    ![Ange ROTEN användarlösenord](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurera datorn som en huvudmålserver

Att hämta ID för varje SCSI-hårddisk på en Linux-dator i **disk. EnableUUID = TRUE** parameter måste vara aktiverat. Om du vill aktivera den här parametern, gör du följande:

1. Stäng av den virtuella datorn.

2. Högerklicka på posten för den virtuella datorn i den vänstra rutan och välj sedan **redigera inställningar för**.

3. Välj den **alternativ** fliken.

4. I den vänstra rutan, Välj **Avancerat** > **allmänna**, och välj sedan den **konfigurationsparametrar** -knappen i den nedre högra delen av skärmen.

    ![Öppna konfigurationsparameter](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Den **konfigurationsparametrar** alternativet är inte tillgängligt när datorn körs. Stäng av den virtuella datorn om du vill aktivera den här fliken.

5. Se om en rad med **disk. EnableUUID** finns redan.

    - Om värdet finns och är inställd på **FALSKT**, ändra värdet till **SANT**. (Värdena är inte skiftlägeskänsligt.)

    - Om värdet finns och är inställd på **SANT**väljer **Avbryt**.

    - Om värdet inte finns, väljer **Lägg till rad**.

    - Lägg till i namnkolumnen **disk. EnableUUID**, och ange värdet **SANT**.

    ![Kontrollerar om disken. EnableUUID finns redan](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Inaktivera kernel-uppgraderingar

Azure Site Recovery-huvudmålservern kräver en viss version av Ubuntu, se till att kernel-uppgraderingar är inaktiverad för den virtuella datorn. Kernel uppgraderingar är aktiverade, kan orsaka huvudmålservern inte fungerar korrekt.

#### <a name="download-and-install-additional-packages"></a>Hämta och installera ytterligare paket

> [!NOTE]
> Kontrollera att du har Internetanslutning att hämta och installera ytterligare paket. Om du inte har Internetanslutning, måste du söka efter dessa RPM-paket och installera dem manuellt.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Hämta installationsprogrammet för installation

Om din huvudmålservern har Internetanslutning, kan du använda följande steg för att hämta installationsprogrammet. Annars kan du kopiera installationsprogrammet från processervern och installera den.

#### <a name="download-the-master-target-installation-packages"></a>Hämta huvudmålservern-paket

[Hämta den senaste Linux huvudmålserver installation bits](https://aka.ms/latestlinuxmobsvc).

Om du vill hämta den med hjälp av Linux, skriver du:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Kontrollera att du hämtar och packa installationsprogrammet i arbetskatalogen. Om du packa upp till **/usr/lokal**, misslyckas installationen.


#### <a name="access-the-installer-from-the-process-server"></a>Åtkomst till installationsprogrammet från processervern

1. På processervern, gå till **C:\Program Files (x86) \Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Kopiera den nödvändiga installer-fil från processervern och spara den som **latestlinuxmobsvc.tar.gz** i arbetskatalogen.


### <a name="apply-custom-configuration-changes"></a>Använd anpassad konfigurationsändringar

Om du vill använda anpassade konfigurationsändringar, använder du följande steg:


1. Kör följande kommando för att untar binärfilen.

    `tar -zxvf latestlinuxmobsvc.tar.gz`

    ![Skärmbild av kommandot ska köras](./media/vmware-azure-install-linux-master-target/image16.png)

2. Kör följande kommando för att ge behörighet.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Kör följande kommando för att köra skriptet.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Kör skriptet en gång på servern. Stäng sedan servern. Starta om servern när du lägger till en disk som beskrivs i nästa avsnitt.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Lägg till en disk för kvarhållning Linux huvudmålservern virtuell dator

Använd följande steg för att skapa en kvarhållningsdisken:

1. Koppla en ny disk 1 TB till Linux huvudmålservern virtuell dator och sedan starta datorn.

2. Använd den **multipath -lla** kommandot Läs multipath ID kvarhållningsdisken: **multipath -lla**

    ![MPIO-ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formatera hårddisken och sedan skapa ett filsystem i den nya enheten: **mkfs.ext4 /dev/mapper/< multipath kvarhållning disk-id >**.
    
    ![Filsystem](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. När du har skapat filsystemet montera kvarhållningsdisken.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Skapa den **fstab** post att montera kvarhållningsenhetens varje gång systemet startar.
    
    `vi /etc/fstab`
    
    Välj **infoga** att börja redigera filen. Skapa en ny rad och Lägg till följande text. Redigera disk multipath-ID baserat på den markerade multipath ID från det föregående kommandot.

    **/dev/mapper/ <Retention disks multipath id> /mnt/kvarhållning ext4 rw 0 0**

    Välj **Esc**, och skriv sedan **: wq** (skriva och avsluta) att stänga fönstret redigeraren.

### <a name="install-the-master-target"></a>Installera huvudmålservern

> [!IMPORTANT]
> Versionen av huvudmålservern måste vara lika med eller tidigare versioner av processervern och konfigurationsservern. Om detta inte är uppfyllt, skyddar lyckas, men kan slutföras inte.


> [!NOTE]
> Innan du installerar huvudmålservern måste du kontrollera att den **/etc/hosts** filen på den virtuella datorn innehåller poster som motsvarar IP-adresser som är associerade med alla nätverkskort för det lokala värdnamnet.

1. Kopiera lösenfras från **C:\ProgramData\Microsoft Azure plats Recovery\private\connection.passphrase** på konfigurationsservern. Spara den som **passphrase.txt** i samma lokala katalog genom att köra följande kommando:

    `echo <passphrase> >passphrase.txt`

    Exempel: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Notera configuration-serverns IP-adress. Kör följande kommando för att installera huvudmålservern och registrera servern med konfigurationsservern.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exempel: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Vänta tills skriptet har slutförts. Om du registrerar huvudmålservern, huvudmålservern finns på den **Site Recovery-infrastruktur** i portalen.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Installera huvudmålservern genom att använda interaktiv installation

1. Kör följande kommando för att installera huvudmålservern. Rollen agent väljer **huvudmålservern**.

    ```
    ./install
    ```

2. Välj standardplatsen för installation och välj sedan **RETUR** att fortsätta.

    ![Om du väljer en standardplatsen för installation av huvudmålservern](./media/vmware-azure-install-linux-master-target/image17.png)

När installationen är klar kan du registrera konfigurationsservern med hjälp av kommandoraden.

1. Observera IP-adress för konfigurationsservern. Du behöver den i nästa steg.

2. Kör följande kommando för att installera huvudmålservern och registrera servern med konfigurationsservern.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exempel: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Vänta tills skriptet har slutförts. Om huvudmålservern har registrerats, visas huvudmålservern på den **Site Recovery-infrastruktur** i portalen.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Installera VMware-verktyg / öppna vm verktyg på huvudmålservern

Du måste installera VMware-verktyg eller öppna-vm-verktyg på huvudmålservern så att den kan identifiera datalager. Om inte verktygen är installerade, visas skyddar skärmen inte i datalager. Du måste starta om efter installationen av VMware-verktyg.

### <a name="upgrade-the-master-target-server"></a>Uppgradera huvudmålservern

Kör installationsprogrammet. Den upptäcker automatiskt att agenten är installerad på huvudmålservern. Om du vill uppgradera, Välj **Y**.  När installationen har slutförts, kontrollerar du vilken version av huvudmålservern installeras med hjälp av följande kommando:

`cat /usr/local/.vx_version`


Du ser den **Version** fältet visar versionsnumret på Huvudmålet.

## <a name="common-issues"></a>Vanliga problem

* Kontrollera att du inte aktiverar Storage vMotion på alla management-komponenter, till exempel en huvudmålserver. Om huvudmålservern flyttas efter en lyckad skyddar, kan virtuella diskar (VMDKs) inte frånkopplas. I det här fallet misslyckas återställning efter fel.

* Huvudmålservern ska inte ha några ögonblicksbilder på den virtuella datorn. Om det finns ögonblicksbilder, misslyckas återställning efter fel.

* På grund av vissa anpassade konfigurationer för NIC, nätverksgränssnittet inaktiveras under start och huvudmålservern-agenten gick inte att initiera. Kontrollera att följande egenskaper är korrekt inställda. Kontrollera de här egenskaperna i Ethernet-kort filens /etc/sysconfig/network-scripts/ifcfg-eth *.
    * BOOTPROTO = dhcp
    * ONBOOT = Ja


## <a name="next-steps"></a>Nästa steg
Efter installation och registrering av huvudmålservern har slutförts visas huvudmålservern visas på den **huvudmålservern** i avsnittet **Site Recovery-infrastruktur**, enligt konfigurationen Översikt över Server.

Du kan nu fortsätta med [återaktivera skydd](vmware-azure-reprotect.md), följt av återställning efter fel.

