---
title: Installera en huvudmålserver för återställning av virtuella Linux-datorer med Azure Site Recovery
description: Lär dig hur du konfigurerar en Målserver för Linux-huvudhanteraren för återställning av återställning av återställning av en lokal plats vid haveriberedskap av virtuella datorer med VMware till Azure med Hjälp av Azure Site Recovery.
author: mayurigupta13
services: site-recovery
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/06/2019
ms.author: mayg
ms.openlocfilehash: 5b4d625d28584bb601905e9439c112c845219e54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73954382"
---
# <a name="install-a-linux-master-target-server-for-failback"></a>Konfigurera en Linux-huvudmålserver för återställning efter fel
När du har växlat över dina virtuella datorer till Azure kan du återställa de virtuella datorerna till den lokala platsen. För att växla tillbaka måste du rotera den virtuella datorn från Azure till den lokala platsen. För den här processen behöver du en lokal huvudmålserver för att ta emot trafiken. 

Om din skyddade virtuella dator är en virtuell Windows-dator behöver du ett Windows-huvudmål. För en virtuell Linux-maskin behöver du ett Linux-huvudmål. Läs följande steg för att lära dig hur du skapar och installerar ett Linux-huvudmål.

> [!IMPORTANT]
> Från och med lanseringen av målservern 9.10.0 kan den senaste huvudmålservern endast installeras på en Ubuntu 16.04-server. Nya installationer är inte tillåtna på CentOS6.6-servrar. Du kan dock fortsätta att uppgradera dina gamla huvudmålservrar med hjälp av 9.10.0-versionen.
> Huvudmålserver på LVM stöds inte.

## <a name="overview"></a>Översikt
Den här artikeln innehåller instruktioner för hur du installerar ett Linux-huvudmål.

Skicka kommentarer eller frågor i slutet av den här artikeln eller på [Azure Recovery Services Forum](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Krav

* Om du vill välja den värd där huvudmålet ska distribueras avgör du om återställningen av återställningen ska vara till en befintlig lokal virtuell dator eller till en ny virtuell dator. 
    * För en befintlig virtuell dator bör värden för huvudmålet ha åtkomst till datalager för den virtuella datorn.
    * Om den lokala virtuella datorn inte finns (vid återställning av alternativ plats) skapas den virtuella återställningsdatorn på samma värd som huvudmålet. Du kan välja vilken ESXi-värd som helst för att installera huvudmålet.
* Huvudmålet ska vara i ett nätverk som kan kommunicera med processservern och konfigurationsservern.
* Versionen av huvudmålet måste vara lika med eller tidigare än versionerna av processservern och konfigurationsservern. Om till exempel versionen av konfigurationsservern är 9.4 kan versionen av huvudmålet vara 9.4 eller 9.3 men inte 9.5.
* Huvudmålet kan bara vara en virtuell VMware-dator och inte en fysisk server.

## <a name="sizing-guidelines-for-creating-master-target-server"></a>Ändra riktlinjer för att skapa huvudmålserver

Skapa huvudmålet i enlighet med följande storleksriktlinjer:
- **RAM:** 6 GB eller mer
- **OS diskstorlek:** 100 GB eller mer (för att installera OS)
- **Ytterligare diskstorlek för kvarhållningsenhet:** 1 TB
- **CPU-kärnor:** 4 kärnor eller mer

Följande Ubuntu-kärnor stöds.


|Kernel-serien  |Stöd upp till  |
|---------|---------|
|4.4      |4.4.0-81-generisk         |
|4.8      |4.8.0-56-generisk         |
|4.10     |4.10.0-24-generisk        |


## <a name="deploy-the-master-target-server"></a>Distribuera huvudmålservern

### <a name="install-ubuntu-16042-minimal"></a>Installera Ubuntu 16.04.2 Minimal

Gör så här för att installera Ubuntu 16.04.2 64-bitars operativsystem.

1.   Gå till [nedladdningslänken,](http://old-releases.ubuntu.com/releases/16.04.2/ubuntu-16.04.2-server-amd64.iso)välj den närmaste spegeln och ladda ner en Ubuntu 16.04.2 minimal 64-bitars ISO.
Håll en Ubuntu 16.04.2 minimal 64-bitars ISO i DVD-enheten och starta systemet.

1.  Välj **engelska** som önskat språk och välj sedan **Retur**.
    
    ![Välj ett språk](./media/vmware-azure-install-linux-master-target/image1.png)
1. Välj **Installera Ubuntu Server**och välj sedan **Retur**.

    ![Välj Installera Ubuntu-server](./media/vmware-azure-install-linux-master-target/image2.png)

1.  Välj **engelska** som önskat språk och välj sedan **Retur**.

    ![Välj engelska som önskat språk](./media/vmware-azure-install-linux-master-target/image3.png)

1. Välj lämpligt alternativ i alternativlistan **För tidszon** och välj sedan **Retur**.

    ![Välj rätt tidszon](./media/vmware-azure-install-linux-master-target/image4.png)

1. Välj **Nej** (standardalternativet) och välj sedan **Retur**.

     ![Konfigurera tangentbordet](./media/vmware-azure-install-linux-master-target/image5.png)
1. Välj **engelska (USA)** som ursprungsland/ursprungsregion för tangentbordet och välj sedan **Retur**.

1. Välj **engelska (USA)** som tangentbordslayout och välj sedan **Retur**.

1. Ange värdnamnet för servern i rutan **Värdnamn** och välj sedan **Fortsätt**.

1. Om du vill skapa ett användarkonto anger du användarnamnet och väljer sedan **Fortsätt**.

      ![Skapa ett användarkonto](./media/vmware-azure-install-linux-master-target/image9.png)

1. Ange lösenordet för det nya användarkontot och välj sedan **Fortsätt**.

1.  Bekräfta lösenordet för den nya användaren och välj sedan **Fortsätt**.

    ![Bekräfta lösenorden](./media/vmware-azure-install-linux-master-target/image11.png)

1.  I nästa val för att kryptera din hemkatalog väljer du **Nej** (standardalternativet) och väljer sedan **Retur**.

1. Om tidszonen som visas är korrekt väljer du **Ja** (standardalternativet) och väljer sedan **Retur**. Om du vill konfigurera om tidszonen väljer du **Nej**.

1. Välj **Guidad**i alternativen för partitioneringsmetoden och välj sedan **Retur**.

     ![Välj alternativet partitioneringsmetod](./media/vmware-azure-install-linux-master-target/image14.png)

1.  Välj lämplig disk på **alternativen Välj disk till partition** och välj sedan **Retur**.

    ![Markera disken](./media/vmware-azure-install-linux-master-target/image15.png)

1.  Välj **Ja** om du vill skriva ändringarna till disken och välj sedan **Retur**.

    ![Välj standardalternativ](./media/vmware-azure-install-linux-master-target/image16-ubuntu.png)

1.  I konfigurationsproxyvalet väljer du standardalternativet, väljer **Fortsätt**och väljer sedan **Retur**.
     
     ![Välj hur du ska hantera uppgraderingar](./media/vmware-azure-install-linux-master-target/image17-ubuntu.png)

1.  Välj **Alternativet Inga automatiska uppdateringar** i valet för att hantera uppgraderingar på datorn och välj sedan **Retur**.

     ![Välj hur du ska hantera uppgraderingar](./media/vmware-azure-install-linux-master-target/image18-ubuntu.png)

    > [!WARNING]
    > Eftersom målservern för Azure Site Recovery kräver en mycket specifik version av Ubuntu måste du se till att kärnuppgraderingarna är inaktiverade för den virtuella datorn. Om de är aktiverade, orsakar alla regelbundna uppgraderingar huvudmålservern att fungera fel. Se till att du väljer alternativet **Inga automatiska uppdateringar.**

1.  Välj standardalternativ. Om du vill öppnaSSH för SSH-anslutning väljer du alternativet **OpenSSH-server** och väljer sedan **Fortsätt**.

    ![Välj programvara](./media/vmware-azure-install-linux-master-target/image19-ubuntu.png)

1. I valet för installation av GRUB-starthanteraren väljer du **Ja**och väljer sedan **Retur**.
     
    ![GRUB startinstallationsprogram](./media/vmware-azure-install-linux-master-target/image20.png)


1. Välj lämplig enhet för startlastarinstallationen (helst **/dev/sda)** och välj sedan **Retur**.
     
    ![Välj lämplig enhet](./media/vmware-azure-install-linux-master-target/image21.png)

1. Välj **Fortsätt**och välj sedan **Retur** för att slutföra installationen.

    ![Slutför installationen](./media/vmware-azure-install-linux-master-target/image22.png)

1. När installationen är klar loggar du in på den virtuella datorn med de nya användarautentiseringsuppgifterna. (Se **steg 10** för mer information.)

1. Använd stegen som beskrivs i följande skärmbild för att ange ROOT-användarlösenordet. Logga sedan in som ROOT-användare.

    ![Ange root-användarlösenord](./media/vmware-azure-install-linux-master-target/image23.png)


### <a name="configure-the-machine-as-a-master-target-server"></a>Konfigurera datorn som en huvudmålserver

För att få ID för varje SCSI-hårddisk i en virtuell Linux-dator, **disken. EnableUUID = TRUE-parametern** måste aktiveras. Så här aktiverar du den här parametern:

1. Stäng av den virtuella datorn.

2. Högerklicka på posten för den virtuella datorn i den vänstra rutan och välj sedan **Redigera inställningar**.

3. Välj fliken **Alternativ.**

4. I den vänstra rutan väljer du **Avancerat** > **allmänt**och väljer sedan knappen **Konfigurationsparametrar** längst ned till höger på skärmen.

    ![Öppna konfigurationsparametern](./media/vmware-azure-install-linux-master-target/image24-ubuntu.png) 

    Alternativet **Konfigurationsparametrar** är inte tillgängligt när datorn körs. Om du vill aktivera den här fliken stänger du av den virtuella datorn.

5. Se om en rad med **disk. EnableUUID** finns redan.

   - Om värdet finns och är inställt på **Falskt**ändrar du värdet till **Sant**. (Värdena är inte skiftlägeskänsliga.)

   - Om värdet finns och är inställt på **Sant**väljer du **Avbryt**.

   - Om värdet inte finns väljer du **Lägg till rad**.

   - Lägg till disk i **namnkolumnen. EnableUUID**och ange sedan värdet till **SANT**.

     ![Kontrollera om disk. EnableUUID finns redan](./media/vmware-azure-install-linux-master-target/image25.png)

#### <a name="disable-kernel-upgrades"></a>Inaktivera kärnuppgraderingar

Azure Site Recovery-huvudmålservern kräver en specifik version av Ubuntu, se till att kärnuppgraderingarna är inaktiverade för den virtuella datorn. Om kärnuppgraderingar är aktiverade kan det leda till att huvudmålservern inte fungerar.

#### <a name="download-and-install-additional-packages"></a>Ladda ned och installera ytterligare paket

> [!NOTE]
> Kontrollera att du har Internet-anslutning för att hämta och installera ytterligare paket. Om du inte har Internet-anslutning måste du manuellt hitta dessa Deb-paket och installera dem.

 `apt-get install -y multipath-tools lsscsi python-pyasn1 lvm2 kpartx`

### <a name="get-the-installer-for-setup"></a>Skaffa installationsprogrammet för installation

Om huvudmålet har Internet-anslutning kan du använda följande steg för att hämta installationsprogrammet. Annars kan du kopiera installationsprogrammet från processservern och sedan installera det.

#### <a name="download-the-master-target-installation-packages"></a>Hämta installationspaketen för huvudmål

[Ladda ner de senaste Linux-huvudmålinstallationsbitarna](https://aka.ms/latestlinuxmobsvc).

Om du vill hämta den med Linux skriver du:

`wget https://aka.ms/latestlinuxmobsvc -O latestlinuxmobsvc.tar.gz`

> [!WARNING]
> Se till att du hämtar och packar upp installationsprogrammet i din hemkatalog. Om du packa upp till **/usr/Local**misslyckas installationen.


#### <a name="access-the-installer-from-the-process-server"></a>Få tillgång till installationsprogrammet från processservern

1. Gå till **C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository**.

2. Kopiera den nödvändiga installationsfilen från processservern och spara den som **latestlinuxmobsvc.tar.gz** i din hemkatalog.


### <a name="apply-custom-configuration-changes"></a>Använda anpassade konfigurationsändringar

Om du vill använda anpassade konfigurationsändringar använder du följande steg som ROOT-användare:

1. Kör följande kommando för att ta bort binärfilen.

    `tar -xvf latestlinuxmobsvc.tar.gz`

    ![Skärmbild av kommandot som ska köras](./media/vmware-azure-install-linux-master-target/image16.png)

2. Kör följande kommando för att ge behörighet.

    `chmod 755 ./ApplyCustomChanges.sh`


3. Kör skriptet med hjälp av följande kommando.
    
    `./ApplyCustomChanges.sh`

> [!NOTE]
> Kör skriptet bara en gång på servern. Stäng sedan av servern. Starta om servern när du har lagt till en disk enligt beskrivningen i nästa avsnitt.

### <a name="add-a-retention-disk-to-the-linux-master-target-virtual-machine"></a>Lägga till en kvarhållningsdisk till virtuell virtuell dator för Linux-huvudmål

Så här skapar du en kvarhållningsdisk:

1. Koppla en ny 1 TB-disk till den virtuella datorn för Linux-huvudmål och starta sedan datorn.

2. Använd kommandot **multipath -ll** för att lära dig multipath-ID:et för kvarhållningsdisken: **multipath -ll**

    ![Multipath-ID](./media/vmware-azure-install-linux-master-target/image27.png)

3. Formatera enheten och skapa sedan ett filsystem på den nya enheten: **mkfs.ext4 /dev/mapper/\<Retention-diskens multipath-ID>**.
    
    ![Filsystem](./media/vmware-azure-install-linux-master-target/image23-centos.png)

4. När du har skapat filsystemet monterar du kvarhållningsdisken.

    ```
    mkdir /mnt/retention
    mount /dev/mapper/<Retention disk's multipath id> /mnt/retention
    ```

5. Skapa **fstab** posten för att montera kvarhållningsenheten varje gång systemet startar.
    
    `vi /etc/fstab`
    
    Välj **Infoga** om du vill börja redigera filen. Skapa en ny rad och infoga sedan följande text. Redigera diskmultett-ID:et baserat på det markerade multipath-ID:t från föregående kommando.

    **/dev/mapper/\<Retention diskar multipath id> /mnt/retention ext4 rw 0 0**

    Välj **Esc**och skriv sedan **:wq** (skriv och avsluta) för att stänga redigeringsfönstret.

### <a name="install-the-master-target"></a>Installera huvudmålet

> [!IMPORTANT]
> Versionen av huvudmålservern måste vara lika med eller tidigare än versionerna av processservern och konfigurationsservern. Om det här villkoret inte uppfylls lyckas reprotect, men replikeringen misslyckas.


> [!NOTE]
> Innan du installerar huvudmålservern kontrollerar du att filen **/etc/hosts** på den virtuella datorn innehåller poster som mappar det lokala värdnamnet till de IP-adresser som är associerade med alla nätverkskort.

1. Kopiera lösenfrasen från **C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase** på konfigurationsservern. Spara den sedan som **lösenfras.txt** i samma lokala katalog genom att köra följande kommando:

    `echo <passphrase> >passphrase.txt`

    Exempel: 

       `echo itUx70I47uxDuUVY >passphrase.txt`
    

2. Anteckna konfigurationsserverns IP-adress. Kör följande kommando för att installera huvudmålservern och registrera servern med konfigurationsservern.

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```

    Exempel: 
    
    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

Vänta tills skriptet är klart. Om huvudmålet registreras visas huvudmålet på sidan Infrastruktur för infrastruktur för **platsåterställning** i portalen.


#### <a name="install-the-master-target-by-using-interactive-installation"></a>Installera huvudmålet med hjälp av interaktiv installation

1. Kör följande kommando för att installera huvudmålet. För agentrollen väljer du **huvudmål**.

    ```
    ./install
    ```

2. Välj standardplatsen för installationen och välj sedan **Retur** för att fortsätta.

    ![Välja en standardplats för installation av huvudmål](./media/vmware-azure-install-linux-master-target/image17.png)

När installationen är klar registrerar du konfigurationsservern med hjälp av kommandoraden.

1. Observera konfigurationsserverns IP-adress. Du behöver det i nästa steg.

2. Kör följande kommando för att installera huvudmålservern och registrera servern med konfigurationsservern.

    ```
    ./install -q -d /usr/local/ASR -r MT -v VmWare
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <ConfigurationServer IP Address> -P passphrase.txt
    ```
    Exempel: 

    ```
    /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i 104.40.75.37 -P passphrase.txt
    ```

     Vänta tills skriptet är klart. Om huvudmålet har registrerats visas huvudmålet på sidan Infrastruktur för infrastruktur för **platsåterställning** i portalen.


### <a name="install-vmware-tools--open-vm-tools-on-the-master-target-server"></a>Installera VMware-verktyg / open-vm-verktyg på huvudmålservern

Du måste installera VMware-verktyg eller öppna vm-verktyg på huvudmålet så att de kan identifiera datalager. Om verktygen inte är installerade visas inte återrotectskärmen i datalagret. Efter installationen av VMware-verktygen måste du starta om.

### <a name="upgrade-the-master-target-server"></a>Uppgradera huvudmålservern

Kör installationsprogrammet. Det identifieras automatiskt att agenten är installerad på huvudmålet. Om du vill uppgradera väljer du **Y**.  När installationen har slutförts kontrollerar du versionen av huvudmålet som installerats med hjälp av följande kommando:

`cat /usr/local/.vx_version`


Du kommer att se att fältet **Version** anger versionsnumret för huvudmålet.

## <a name="common-issues"></a>Vanliga problem

* Kontrollera att du inte aktiverar Storage vMotion på några hanteringskomponenter, till exempel ett huvudmål. Om huvudmålet flyttas efter en lyckad reprotect kan inte virtuella datordiskar (VMDK: er) tas bort. I det här fallet misslyckas återställningen av återställningen.

* Huvudmålet bör inte ha några ögonblicksbilder på den virtuella datorn. Om det finns ögonblicksbilder misslyckas återställningen av återställningen.

* På grund av vissa anpassade nätverkskortskonfigurationer inaktiveras nätverksgränssnittet under start och huvudmålagenten kan inte initiera. Kontrollera att följande egenskaper är korrekt inställda. Kontrollera dessa egenskaper i Ethernet-kortfilens /etc/sysconfig/network-scripts/ifcfg-eth*.
    * BOOTPROTO=dhcp
    * ONBOOT =ja


## <a name="next-steps"></a>Nästa steg
När installationen och registreringen av huvudmålet har slutförts kan du se huvudmålet visas i **huvudmålavsnittet** i **Infrastruktur för platsåterställning**under översikten över konfigurationsservern.

Du kan nu fortsätta med [återskydd](vmware-azure-reprotect.md), följt av återställning efter fel.

