---
title: "Misslyckas tillbaka den virtuella VMware-datorer från Azure klassiska portal | Microsoft Docs"
description: "Läs mer om misslyckas tillbaka till den lokala platsen efter redundans för virtuella VMware-datorer och fysiska servrar till Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 7ca86e21-7a5d-45ab-8f4b-e42da90f199a
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.openlocfilehash: 82d5eb7fd13b1e9700a3e9bc2d30775e9c129749
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site-classic-portal"></a>Växla tillbaka virtuella VMware-datorer och fysiska servrar till den lokala platsen (klassiska portal)
> [!div class="op_single_selector"]
> * [Azure Portal](site-recovery-failback-azure-to-vmware.md)
> * [Klassisk Azure-portal](site-recovery-failback-azure-to-vmware-classic.md)
> * [Klassiska Azure-portalen (bakåtkompatibelt)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
>
>

Den här artikeln beskriver hur du växla tillbaka Azure virtuella datorer från Azure till den lokala platsen. Följ anvisningarna i den här artikeln när du är redo att misslyckas tillbaka din virtuella VMware-datorer eller Windows-/ Linux fysiska servrar när de har redundansväxlats från lokalt platsen till Azure med hjälp av det här [kursen](site-recovery-vmware-to-azure-classic.md).

## <a name="overview"></a>Översikt
Det här diagrammet visar arkitekturen för återställning efter fel för det här scenariot.

Använd den här arkitekturen när processervern är på plats och du använder en ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Använd den här arkitekturen när processervern är på Azure och du har en VPN-anslutning eller en ExpressRoute-anslutning.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Komplett lista över portar och återställningen architechture diagram finns i bilden nedan

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Här är hur återställningen fungerar:

* När du har redundansväxlats till Azure växla tillbaka till den lokala platsen i några steg:
  * **Fas 1**: du skyddar virtuella Azure-datorer så att de startar replikeras tillbaka till virtuella VMware-datorer körs i den lokala platsen. Aktivera återaktivera skydd flyttar den virtuella datorn i en skyddsgrupp för återställning efter fel som har skapats automatiskt när redundans skyddsgruppen skapades. Om du har lagt till skyddsgruppen redundans till en återställningsplan sedan skyddsgruppen återställning också automatiskt har lagts till i planen.  Under skyddar ange du hur du planerar att återställas.
  * **Fas 2**: när din virtuella Azure-datorer replikeras till den lokala platsen, kör du en misslyckas för att växla tillbaka från Azure.
  * **Steg 3**: efter dina data har misslyckats tillbaka, skyddar de lokala virtuella datorer som du inte tillbaka till, så att de startar replikera till Azure.


  [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failback/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Återställning till den ursprungliga eller en annan platsen
Om du redundansväxlade en VMware VM kan du växla tillbaka till samma VM-källa om det fortfarande finns lokalt. I det här scenariot kommer endast deltaändringar flyttas tillbaka. Tänk på följande:

* Om du redundansväxlade fysiska servrar sedan är återställning alltid till en ny VMware-VM.
  * Observera att innan tillbaka en fysisk dator
  * Fysisk dator som skyddas kommer tillbaka som en virtuell dator när redundansväxlats tillbaka från Azure till VMware
  * Se till att du upptäcker att minst en Huvudmålet Server tillsammans med de nödvändiga ESX/ESXi-värdar som du behöver återställning efter fel.
* Om du växlar tillbaka till den ursprungliga virtuella datorn krävs följande:

  * Om den virtuella datorn hanteras av en vCenter-server ska Huvudmålets ESX-värden ha åtkomst till datalagret för virtuella datorer.
  * Om den virtuella datorn finns på en ESX-värd men inte hanteras av vCenter måste hårddisken på den virtuella datorn vara i ett datalager som är tillgänglig på Huvudmålservern värden.
  * Om den virtuella datorn finns på en ESX-värd och inte använder vCenter bör du genomföra identifiering av ESX-värden för Huvudmålservern innan du skyddar. Detta gäller att om du växlar tillbaka fysiska servrar för.
  * Ett annat alternativ är (om den lokala virtuella datorn finns) att ta bort det innan du gör en återställning efter fel. Sedan skapar återställning sedan en ny virtuell dator på samma värddator som huvudmålservern ESX-värd.
* När återställning till en alternativ plats data kommer att återställas till samma datalager och samma ESX-värden som används av den lokala huvudmålservern.

## <a name="prerequisites"></a>Krav
* Du behöver en VMware-miljön för att växla tillbaka virtuella VMware-datorer och fysiska servrar. Misslyckas tillbaka till en fysisk server stöds inte.
* För att kunna återställas bör du har skapat ett Azure-nätverk när du först ställa in skydd. Återställning måste VPN eller ExpressRoute-anslutning från Azure-nätverket som virtuella Azure-datorer finns på den lokala platsen.
* Om de virtuella datorerna som du vill växla tillbaka till hanteras av en vCenter-server måste du kontrollera att har du behörigheterna som krävs för identifiering av virtuella datorer på vCenter-servrar. [Läs mer](site-recovery-vmware-to-azure-classic.md).
* Om det finns ögonblicksbilder på en virtuell dator misslyckas återaktivera skydd. Du kan ta bort ögonblicksbilderna eller diskarna.
* Innan du växlar tillbaka behöver du skapa ett antal komponenter:
  * **Skapa en processerver i Azure**. Det här är en Azure VM som du behöver skapa och fortsätta att köras under återställning efter fel. Du kan ta bort datorn när återställning har slutförts.
  * **Skapa en huvudmålserver**: huvudmålservern skickar och tar emot data för återställning efter fel. Hanteringsservern som du skapade lokala har en huvudmålserver som installeras som standard. Beroende på mängden misslyckade tillbaka trafik kan du dock behöver skapa en separat huvudmålserver för återställning efter fel.
  * Om du vill skapa en ytterligare huvudmålservern körs på Linux måste ställa in Linux VM innan du installerar huvudmålservern, som beskrivs nedan.
* Konfigurationsservern är lokala när du gör en återställning efter fel. Under återställning efter fel, måste den virtuella datorn finns i server-konfigurationsdatabasen, misslyckas vilka återställning inte genomföras. Se därför till att du vidtar regelbunden schemalagd säkerhetskopiering av servern. Vid katastrofåterställning måste du återställa den med samma IP-adress så att återställningen fungerar.

## <a name="set-up-the-process-server-in-azure"></a>Ställ in processerver i Azure
Du måste installera en processerver i Azure så att den virtuella Azure-datorer kan data skickas tillbaka till lokala huvudmålservern.

1. I Site Recovery-portalen > **Konfigurationsservrar** väljer att lägga till en ny processerver.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)
2. Ange namn på en server och ange ett namn och lösenord som du använder för att ansluta till den virtuella Azure-datorn som administratör. I **konfigurationsservern** Välj lokala management-servern och ange det Azure-nätverk där processervern ska distribueras. Detta bör vara nätverk som virtuella Azure-datorer finns. Ange en unik IP-adress från undernätet som du väljer och påbörjar distributionen.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

   Ett jobb för att distribuera processervern ska utlösas

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

   När processervern har distribuerats i Azure kan du logga in på den med hjälp av de autentiseringsuppgifter du angav. Första gången du loggar in dialogrutan processen server körs. Skriv i IP-adressen för den lokala management-servern och dess lösenfras. Lämna standardinställningen för port 443. Du kan också lämna standard 9443 port för datareplikering såvida inte den här inställningen ändras särskilt när du konfigurerar den lokala hanteringsservern.

   > [!NOTE]
   > Servern kommer inte att visas under **VM egenskaper**. Det är bara synliga under den **servrar** fliken i hanteringsservern som den har registrerats. Det kan ta om 10 – 15 minuter för processervern ska visas.
   >
   >

## <a name="set-up-the-master-target-server-on-premises"></a>Ställ in den huvudmålserver server lokalt
Huvudmålservern tar emot data för återställning efter fel. En huvudmålserver installeras automatiskt på den lokala hanteringsservern, men om du växlar tillbaka mycket data du behöva ställa in en ytterligare huvudmålservern. Gör på följande sätt:

> [!NOTE]
> Om du vill installera en huvudmålserver på Linux följer du anvisningarna i nästa procedur.
>
>

1. Om du installerar huvudmålservern i Windows, öppna sidan Snabbstart från den virtuella datorn där du installerar huvudmålservern och hämta installationsfilen för enhetlig installationsprogram för Azure Site Recovery-guiden.
2. Kör installationsprogrammet och i **innan du börjar** Välj **lägga till ytterligare servrar att skala upp distributionen**.
3. Slutför guiden på samma sätt som du gjorde när du [konfigurera hanteringsservern](site-recovery-vmware-to-azure-classic.md). På den **Server konfigurationsinformation** anger du IP-adressen för den här huvudmålservern och en lösenfras som ska få åtkomst till den virtuella datorn.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Konfigurera en Linux-VM som huvudmålservern
För att ställa in av hanteringsservern som kör huvudmålservern som en Linux VM måste du installera Cent) S 6.6 minimalt operativsystem, hämta SCSI-ID: N för varje SCSI-hårddisk, installera vissa ytterligare paket och tillämpa vissa egna ändringar.

#### <a name="install-centos-66"></a>Installera CentOS 6.6
1. Installera CentOS 6.6 minimalt operativsystem på VM-hanteringsservern. Behåll ISO i DVD-enheten och starta systemet. Hoppa över media testning, Välj amerikansk engelska på språk, Välj **grundläggande lagringsenheter**, kontrollera att hårddisken inte är viktiga data och klicka på **Ja**, ta bort alla data. Ange värdnamnet för hanteringsservern och Välj server nätverkskort.  I den **redigera System** dialogrutan Välj ** Anslut automatiskt ** och lägga till en statisk IP-adress, nätverk och DNS-inställningarna. Ange en tidszon och en rotlösenord för åtkomst till hanteringsservern.
2. När du uppmanas att installationstyp du vill markera **Skapa anpassad Layout** som partition. När du klickar på **nästa** Välj **lediga** och klicka på Skapa. Skapa  **/** , **/var/krascher** och **/home partitioner** med **FS typ:** **ext4**. Skapa växlingen partion som **FS typ: växlingen**.
3. Om befintliga enheter är en varning visas. Klicka på **Format** formatera enheten med partitionsinställningar. Klicka på **skriva ändringar till disken** ändringarna partition.
4. Välj **installera startprogram** > **nästa** att installera startprogrammet på rotpartition.
5. När installationen är klar klickar du på **omstart**.

#### <a name="retrieve-the-scsi-ids"></a>Hämta SCSI-ID.
1. Hämta SCSI-ID: N för varje SCSI-hårddisk på den virtuella datorn efter installationen. Högerklicka på posten VM i Egenskaper för virtuell dator i VMware för att göra den här stängs av hanteringsservern VM > **redigera inställningar för** > **alternativ**.
2. Välj **Avancerat** > **allmän artikel** och på **konfigurationsparametrar**. Det här alternativet kommer att de-active när datorn körs. Om du vill aktivera den måste datorn att stängas.
3. Om raden **disk. EnableUUID** finns kontrollerar du att värdet anges **SANT** (skiftlägeskänsligt). Om det redan finns kan du avbryta och testa SCSI-kommando i ett gästoperativsystem när den har startats.
4. Om raden inte befintliga Klicka **Lägg till rad** – och Lägg till den med den **SANT** värde. Använd inte dubbla citattecken.

#### <a name="install-additional-packages"></a>Installera ytterligare paket
Du måste hämta och installera vissa ytterligare paket.

1. Kontrollera att huvudmålservern är ansluten till internet.
2. Kör detta kommando för att hämta och installera 15 paket från databasen CentOS: **# yum installera – y xfsprogs perl lsscsi rsync wget kexec-tools**.
3. Om källdatorer du skyddar kör Linux med Reiser eller XFS filsystem för rot- eller -enhet, och sedan hämta och installera ytterligare paket på följande sätt:

   * # <a name="cd-usrlocal"></a>CD /usr/local
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm – ivh kmod-reiserfs-0,0-1.el6.elrepo.x86_64.rpm reiserfs-verktyg för webbplatsuppgradering-3.6.21-1.el6.elrepo.x86_64.rpm
   * # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm – ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Använd anpassade ändringar
Gör följande för att tillämpa ändringar efter att du har instruktionerna efter installationen och installerade paket:

1. Kopiera RHEL 6-64 Unified Agent binära till den virtuella datorn. Kör kommandot ska untar binärfilen: **tar – zxvf<file name>**
2. Kör detta kommando för att ge behörighet: **# chmod 755./ApplyCustomChanges.sh**
3. Kör skriptet: **#./ApplyCustomChanges.sh**. Du bör bara köra skriptet en gång. Starta om servern när skriptet har körts.

## <a name="run-the-failback"></a>Kör den återställning efter fel
### <a name="reprotect-the-azure-vms"></a>Skyddar Azure virtuella datorer
1. I Site Recovery-portalen > **datorer** fliken markerar du den virtuella datorn som har växlas över och klicka på **skydda igen**.
2. I **Huvudmålserver** och **Processervern** väljer du den lokala huvudmålservern och processervern Azure VM.
3. Välj det konto som du har konfigurerat för att ansluta till den virtuella datorn.
4. Välj återställning efter fel version av skyddsgruppen. För exempel om den virtuella datorn är skyddad i SG1 och du måste markera PG1_Failback.
5. Om du vill återställa till en alternativ plats, Välj kvarhållningsenhetens och datalagret som konfigurerats för huvudmålservern. När du växlar tillbaka till den lokala platsen virtuella VMware-datorer i skyddsplan återställning använder samma datalager som huvudmålservern. Om du vill återställa repliken Azure VM till samma lokala VM sedan den lokala virtuella datorn bör redan vara i samma datalager som huvudmålservern. Om det inte finns någon VM lokal skapas en ny under återaktivera skydd.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)
6. När du klickar på **OK** ska börja återaktivera skydd ett jobb börjar att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på den **jobb** fliken.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Kör en redundansväxling till den lokala platsen
Den virtuella datorn flyttas till återställning efter fel version av skyddsgruppen efter återaktivera skydd och läggs automatiskt till i återställningsplanen som du använde för redundans till Azure om det finns en.

1. I den **Återställningsplaner** Välj återställningsplan som innehåller gruppen återställning efter fel och klickar på **redundans** > **oplanerad växling**.
2. I **bekräfta redundans** Kontrollera redundansriktning (för att Azure) och välj den återställningspunkt som du vill använda för redundans (senaste). Om du har aktiverat **Multi-VM** när du konfigurerade replikeringsegenskaper kan du återställa till den senaste app eller kraschkonsekvent återställningspunkt. Klicka på kryssmarkeringen för att starta redundansväxlingen.
3. Under växling vid fel stängs Site Recovery virtuella Azure-datorer. När du har kontrollerat att återställningen har slutförts som förväntat du kan du kontrollera att den virtuella Azure-datorer har stängts av som förväntat.

### <a name="reprotect-the--on-premises-site"></a>Skyddar den lokala platsen
När återställning har slutförts blir tillbaka på den lokala platsen dina data, men skyddas inte. Så här startar du replikerar till Azure igen gör du följande:

1. I Site Recovery-portalen > **datorer** fliken väljer de virtuella datorerna som har misslyckats tillbaka och klicka på **skydda igen**.
2. När du har kontrollerat att replikeringen till fungerar Azure som förväntat i Azure kan du ta bort den virtuella Azure-datorer (för närvarande inte körs) som har misslyckats igen.

### <a name="common-issues-in-failback"></a>Vanliga problem i återställning efter fel
1. Om du utför skrivskyddade användaridentifieringen vCenter och skydda virtuella datorer det lyckas och redundans fungerar. När du skyddar, kommer inte eftersom datastores inte kan identifieras. Som ett symtom visas inte datastores som visas när skydda igen. För att lösa problemet kan du uppdatera vCenter-autentiseringsuppgifter med rätt konto som har behörighet och försök igen. [Läs mer](site-recovery-vmware-to-azure-classic.md)
2. När återställning en Linux VM och köra den lokalt, ser du att paketet Network Manager avinstalleras från datorn. Det beror på att när den virtuella datorn återställs i Azure, Network Manager paketet tas bort.
3. När en virtuell dator har konfigurerats med statiska IP-adress och växlas över till Azure, förvärvas IP-adress via DHCP. När du redundansväxlar tillbaka till lokala fortsätter den virtuella datorn att använda DHCP för att få IP-adress. Behöver du manuellt inloggningen till datorn och ange IP-adressen tillbaka till statisk adress om det behövs.
4. Om du använder ESXi 5.5 gratisversion eller vSphere 6 Hypervisor gratisversion redundans lyckades, men lyckas inte återställning efter fel. Du kommer ned om du vill uppgradera till antingen Utvärderingslicens att aktivera återställning efter fel.

## <a name="failing-back-with-expressroute"></a>Tillbaka med ExpressRoute
Du kan växla tillbaka via en VPN-anslutning eller Azure ExpressRoute. Om du vill använda ExpressRoute Tänk på följande:

* ExpressRoute ska ställas in på virtuella Azure-nätverket till vilken källa datorer misslyckas över och i vilka Azure virtuella datorer är placerade efter att växlingen sker.
* Data replikeras till Azure storage-konto på en offentlig slutpunkt. Du bör konfigurera offentlig peering i ExpressRoute med mål-Datacenter för Site Recovery replikering ska använda ExpressRoute.
