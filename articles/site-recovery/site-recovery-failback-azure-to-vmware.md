---
title: "Återställning av virtuella VMware-datorer från Azure till lokala | Microsoft Docs"
description: "Läs mer om misslyckas tillbaka till den lokala platsen efter redundans för virtuella VMware-datorer och fysiska servrar till Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 03/27/2017
ms.author: ruturajd
ms.openlocfilehash: dde0bb6b4f6bc10afdd7d40adc6689d42b37de81
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Växla tillbaka virtuella VMware-datorer och fysiska servrar till den lokala platsen


Den här artikeln beskriver hur du återställning av Azure virtuella datorer från Azure till den lokala platsen. Följ anvisningarna här när du är redo att återställas VMware-datorer eller Windows- eller Linux fysiska servrar när du har nytt skyddat dina datorer med detta [referens](site-recovery-how-to-reprotect.md).

>[!NOTE]
>Om du använder den klassiska Azure-portalen kan du läsa anvisningarna som nämns [här](site-recovery-failback-azure-to-vmware-classic.md) för förbättrad VMware till Azure-arkitektur och [här](site-recovery-failback-azure-to-vmware-classic-legacy.md) för äldre arkitektur

## <a name="overview"></a>Översikt
Diagrammen i det här avsnittet visar arkitekturen för återställning efter fel för det här scenariot.

Använd den här arkitekturen när Processervern är på plats och du använder en Azure ExpressRoute-anslutning:

![Arkitekturdiagram för ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Använd den här arkitekturen när Processervern är på Azure och du har en VPN-anslutning eller en ExpressRoute-anslutning:

![Arkitekturdiagram för VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

En fullständig lista över portar och Arkitekturdiagram för återställning efter fel finns i följande bild:

![Återställning av failover lista över alla portar](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

När du har redundansväxlats till Azure växla tillbaka till den lokala platsen i tre steg:

* **Fas 1**: du skyddar virtuella Azure-datorer så att de startar replikeras tillbaka till den virtuella VMware-datorer som körs på den lokala platsen.
* **Fas 2**: när din virtuella Azure-datorer replikeras till den lokala platsen du köra en växling till växla tillbaka från Azure.
* **Steg 3**: efter dina data har misslyckats tillbaka, skyddar de lokala virtuella datorer som du inte tillbaka till, så att de startar replikera till Azure.

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>Växla tillbaka till den ursprungliga platsen eller en alternativ plats
När du redundansväxlar en VMware VM, kan du växla tillbaka till samma VM-källa om det fortfarande finns lokalt. I det här scenariot misslyckades endast går tillbaka.

Om du redundansväxlade fysiska servrar är alltid återställning till en ny VMware-VM. Observera att innan tillbaka en fysisk dator:
* En skyddad fysisk dator kommer tillbaka som en virtuell dator när den har redundansväxlats tillbaka från Azure till VMware. En fysisk dator för Windows Server 2008 R2 SP1, kan inte om den är skyddad och växlas över till Azure, flyttas tillbaka. En dator med Windows Server 2008 R2 SP1 som startats som en virtuell dator på lokala kommer att kunna återställas.
* Se till att du upptäcker att minst en huvudmålservern tillsammans med nödvändiga ESX/ESXi-värdar som du behöver återställas till.

Om du växlar tillbaka till den ursprungliga virtuella datorn på krävs följande:

* Om den virtuella datorn hanteras av en vCenter-server ska på huvudmålservern ESX-värden ha åtkomst till datalagret för virtuella datorer.
* Om den virtuella datorn finns på en ESX-värd men inte hanteras av vCenter, måste hårddisken på den virtuella datorn vara i ett datalager som är tillgänglig på Huvudmålservern värden.
* Om den virtuella datorn finns på en ESX-värd och inte använder vCenter, bör du genomföra identifiering av ESX-värden för Huvudmålservern innan du skyddar. Detta gäller att om du växlar tillbaka fysiska servrar för.
* Ett annat alternativ är (om den lokala virtuella datorn finns) att ta bort det innan du gör en återställning efter fel. Återställning efter fel skapar sedan en ny virtuell dator på samma värddator som huvudmålservern ESX-värd.

Data återställs till samma datalager och samma ESX-värden som används av den lokala huvudmålservern när du växlar tillbaka till en alternativ plats.

## <a name="prerequisites"></a>Krav
* Misslyckas säkerhetskopiera virtuella VMware-datorer och fysiska servrar behöver du en VMware-miljö. Misslyckas tillbaka till en fysisk server stöds inte.
* För att återställa, måste du har skapat ett Azure-nätverk när du först ställa in skydd. Återställning måste VPN eller ExpressRoute-anslutning från Azure-nätverket som virtuella Azure-datorer finns på den lokala platsen.
* Om de virtuella datorer som du vill växla tillbaka till hanteras av vCenter-servern kontrollerar du att har du behörigheterna som krävs för identifiering av virtuella datorer på vCenter-servrar. Mer information finns i [replikera VMware-datorer och fysiska servrar till Azure med Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
* Om det finns ögonblicksbilder på en virtuell dator, misslyckas återaktivera skydd. Du kan ta bort ögonblicksbilderna eller diskarna.
* Innan du växlar tillbaka skapa dessa komponenter:
  * **Skapa en Processerver i Azure**. Den här komponenten är en Azure VM som du skapar och fortsätta att köras under återställning efter fel. Du kan ta bort den virtuella datorn när återställning har slutförts.
  * **Skapa en huvudmålserver**: huvudmålservern skickar och tar emot data för återställning efter fel. Hanteringsservern som du skapade lokala har en huvudmålserver som installeras som standard. Beroende på mängden misslyckades tillbaka trafik, kan du dock behöver skapa en separat huvudmålserver för återställning efter fel.
  * Om du vill skapa en ytterligare huvudmålservern som körs på Linux, konfigurera Linux VM innan du installerar huvudmålservern, såsom beskrivs senare.
* En konfigurationsservern är lokala när du gör en återställning efter fel. Under återställning efter fel, måste den virtuella datorn finns i server-konfigurationsdatabasen. Om configuration server-databasen innehåller inga VM, kan inte återställning lyckas. Därför ska du kontrollera att du gör regelbundna schemalagda säkerhetskopieringar av servern. Du måste återställa med samma IP-adress så att återställningen fungerar i en katastrof.
* Ange inställningen disk.enableUUID=true **konfigurationsparametrar** av originalet mål VM i VMware. Om den här raden inte finns, kan du lägga till den. Den här inställningen krävs för att ge en konsekvent universellt Unik identifierare (UUID) till den virtuella dator (VMDK) fil så att den är monterad på rätt sätt.
* Tänk på att en ”Master målservern får inte vara lagring vMotioned” tillstånd, vilket kan orsaka återställning misslyckas. Den virtuella datorn kan inte startas eftersom diskarna inte ska få tillgång till den.
* Lägg till en enhet som kallas en kvarhållningsenhetens på huvudmålservern. Lägg till en disk och formatera hårddisken.

## <a name="failback-policy"></a>Princip för återställning efter fel
Om du vill replikera tillbaka till lokala behöver du en princip för återställning efter fel. Principen skapas automatiskt när du skapar en princip för riktning framåt och det associeras automatiskt med konfigurationsservern. Den kan inte redigeras. Principen har följande replikeringsinställningarna:

* Återställningspunktmålet tröskelvärde = 15 minuter
* Kvarhållningstid för återställningspunkten = 24 timmar
* Frekvens för appkonsekvent ögonblicksbild = 60 minuter

 ![Replikeringsinställningarna för principen för återställning](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>Konfigurera en Processerver i Azure
Installera en Processerver i Azure så att den virtuella Azure-datorer kan data skickas tillbaka till den lokala huvudmålservern.

Om du har skyddat de virtuella datorerna som klassiska resurser (det vill säga den virtuella datorn återställs i Azure är en virtuell dator som har skapats med hjälp av den klassiska distributionsmodellen), behöver du en Processerver i Azure. Om du har återställt de virtuella datorerna med Azure Resource Manager som distributionstypen ha Processervern Resource Manager som distributionstypen. Distributionstypen är markerad som virtuella Azure-nätverket som du distribuerar till Processervern.

1. I **valvet** > **inställningar** > **Site Recovery-infrastruktur** (under **hantera**) > **Konfigurationsservrar** (under **för VMware och fysiska datorer**), Välj konfigurationsservern.
2. Klicka på **bearbeta Server**.

  ![Bearbeta knappen Server](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. Välja att distribuera Processervern som **distribuera en återställning efter fel Processerver i Azure**.
4. Välj den prenumeration som du har återställt de virtuella datorerna till.
5. Välj den Azure-nätverk som du har återställt de virtuella datorerna till. Processervern måste vara i samma nätverk så att de återställda virtuella datorerna och Processervern kan kommunicera.
6. Om du har valt en *klassiska distributionsmodellen* nätverk, skapa en virtuell dator via Azure Marketplace och installera sedan Processervern i den.

 ![Fönstret ”Lägg till Processerver”](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 När du skapar Processervern, ska du vara uppmärksam på följande:
 * Namnet på avbildningen är *Microsoft Azure Site Recovery Process Server V2*. Välj **klassiska** som distributionsmodell.

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * Installera Processervern enligt anvisningarna i [replikera VMware-datorer och fysiska servrar till Azure med Azure Site Recovery](site-recovery-vmware-to-azure-classic.md).
7. Om du väljer den *Resource Manager* Azure nätverk, distribuera Processervern genom att ange följande information:

  * Namnet på den resursgrupp som du vill distribuera till servern
  * Namnet på servern
  * Ett användarnamn och lösenord för att logga in på servern
  * Storage-konto som du vill distribuera till servern
  * Undernätet och nätverksgränssnittet som du vill ansluta till den
   >[!NOTE]
   >Du måste skapa en egen [nätverksgränssnittet](../virtual-network/virtual-networks-multiple-nics.md) (NIC) och markera den när du distribuerar Processervern.

    ![Ange information i dialogrutan ”Lägg till Processerver”](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. Klicka på **OK**. Den här åtgärden startar ett jobb som skapar en Resource Manager distribution typen virtuell dator under installationen av processen. Om du vill registrera servern till konfigurationsservern kör du installationsprogrammet inuti den virtuella datorn genom att följa instruktionerna i [replikera VMware-datorer och fysiska servrar till Azure med Azure Site Recovery](site-recovery-vmware-to-azure-classic.md). Ett jobb för att distribuera Processervern utlöses också.

  Processervern visas på den **konfigurationsservrar** > **associerade servrar** > **servrar** fliken.

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > Processervern inte visas under **VM egenskaper**. Visas endast på den **servrar** fliken i hanteringsservern som den är registrerad. Det kan ta 10 – 15 minuter för Processervern ska visas.


## <a name="set-up-the-master-target-server-on-premises"></a>Ställ in den huvudmålserver server lokalt
Huvudmålservern tar emot data för återställning efter fel. Servern installeras automatiskt på den lokala hanteringsservern, men om du växlar tillbaka för mycket data, kan du behöva konfigurera ytterligare huvudmålservern. Om du vill konfigurera en huvudmålserver lokalt, gör du följande:

> [!NOTE]
> Om du vill konfigurera en huvudmålserver på Linux, går du vidare till nästa procedur. Använd endast CentOS 6.6 minimalt operativsystem som huvudmålservern OS.

1. Om du ställer in huvudmålservern i Windows, öppna sidan Snabbstart, starta från den virtuella datorn som du installerar på huvudmålservern.
2. Hämta installationsfilen för enhetlig installationsprogram för Azure Site Recovery-guiden.
3. Kör installationsprogrammet och i **innan du börjar**väljer **lägga till ytterligare servrar för processen att skala upp distributionen**.
4. Slutför guiden på samma sätt som du gjorde när du [konfigurera hanteringsservern](site-recovery-vmware-to-azure-classic.md). På den **Server konfigurationsinformation** sidan, ange IP-adressen till huvudmålservern och ange en lösenfras för att komma åt den virtuella datorn.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Konfigurera en Linux-VM som huvudmålservern
Installera CentOS 6.6 minimalt operativsystem för att ställa in av hanteringsservern som kör huvudmålservern som en Linux VM. Hämta SCSI-ID: N för varje SCSI-hårddisk, installerar vissa ytterligare paket och tillämpa vissa egna ändringar.

#### <a name="install-centos-66"></a>Installera CentOS 6.6

1. Installera CentOS 6.6 minimalt operativsystem på VM-hanteringsservern. Håll ISO på en DVD-enhet och starta systemet. Hoppa över media testning. Välj **amerikansk engelska** som språk, Välj **grundläggande lagringsenheter**, kontrollera att hårddisken inte har viktiga data, klickar du på **Ja**, och alla data. Ange värdnamnet för hanteringsservern och välj nätverkskortet.  I den **redigera System** dialogrutan **Anslut automatiskt**, och sedan lägga till en statisk IP-adress, nätverk och DNS-inställningarna. Ange en tidszon. Ange rotlösenordet för att komma åt management-servern.
2. När du tillfrågas om vilken typ av installation som du vill använda, Välj **Skapa anpassad Layout** som partition. Klicka på **Nästa**. Välj **lediga**, och klicka sedan på **skapa**. Skapa  **/** , **/var/krascher**, och **/home partitioner** med **FS typ:** **ext4**. Skapa växlingen partitionen som **FS typ: växlingen**.
3. Om befintliga enheter hittas, visas ett varningsmeddelande. Klicka på **Format** formatera enheten med partitionsinställningar. Klicka på **skriva ändringar till disken** ändringarna partition.
4. Välj **installera startprogram** > **nästa** att installera startprogrammet på rotpartition.
5. När installationen är klar klickar du på **omstart**.

#### <a name="retrieve-the-scsi-ids"></a>Hämta SCSI-ID.

1. Hämta SCSI-ID: N för varje SCSI-hårddisk på den virtuella datorn efter installationen. Det gör du genom att stänga av hanteringsservern VM. Högerklicka på VM-posten i VM-egenskaper i VMware > **redigera inställningar för** > **alternativ**.
2. Välj **Avancerat** > **allmän artikel**, och klicka sedan på **konfigurationsparametrar**. Det här alternativet är inte tillgänglig när datorn körs. Datorn måste stängas av för alternativet ska vara tillgängliga.
3. Gör något av följande:
 * Om raden **disk. EnableUUID** finns, kontrollera att värdet har angetts till **SANT** (skiftlägeskänsligt). Om värdet redan har angetts till True, kan du avbryta och testa SCSI-kommando i ett gästoperativsystem när den har startats.
 * Om raden **disk. EnableUUID** inte finns, klickar du på **Lägg till rad**, och Lägg sedan till den med den **SANT** värde. Använd inte dubbla citattecken.

#### <a name="install-additional-packages"></a>Installera ytterligare paket
Hämta och installera ytterligare paket.

1. Kontrollera att huvudmålservern är ansluten till Internet.
2. Hämta och installera 15 paket från CentOS databasen genom att köra det här kommandot: `# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools`.
3. Om källdatorer du skyddar kör Linux med en Reiser eller XFS filsystem enhetens rot- eller ladda ned och installera ytterligare paket på följande sätt:

   * \#CD /usr/local
   * \#wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \#wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \#rpm – ivh kmod-reiserfs-0,0-1.el6.elrepo.x86_64.rpm reiserfs-verktyg för webbplatsuppgradering-3.6.21-1.el6.elrepo.x86_64.rpm
   * \#wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \#rpm – ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \#YUM installera enheten-mapper-multipath (krävs för att aktivera multipath paket på huvudmålservern)

#### <a name="apply-custom-changes"></a>Använd anpassade ändringar
När du har slutfört stegen efter installationen och installerat paketen, tillämpa egna ändringar genom att göra följande:

1. Kopiera RHEL 6-64 Unified Agent binära till den virtuella datorn. Kör det här kommandot för att untar binärfilen: `tar –zxvf <file name>`.
2. Om du vill ge behörighet att köra det här kommandot: `# chmod 755 ./ApplyCustomChanges.sh`.
3. Kör följande skript: `# ./ApplyCustomChanges.sh`. Kör bara en gång. Starta om servern när den har slutförts.

## <a name="run-the-failback"></a>Kör den återställning efter fel
### <a name="reprotect-the-azure-vms"></a>Skyddar Azure virtuella datorer
1. I **valvet**i **replikerade objekt**, högerklicka på den virtuella datorn som misslyckades under och välj sedan **skydda igen**.
2. På bladet kan du se att riktningen för skydd **Azure till lokala** har redan valts.
3. I **Huvudmålserver** och **Processervern**, Välj den lokala huvudmålservern och Processervern Azure VM.
4. Markera databasen som du vill återställa den diskar lokalt till. Använd det här alternativet när den lokala virtuella datorn tas bort och du behöver skapa nya diskar. Ignorera alternativet om diskarna finns redan, men du måste ange ett värde.
5. Använd en kvarhållningsenhetens för att stoppa punkterna i när den virtuella datorn replikeras tillbaka till lokal tid. Vissa kriterier i en kvarhållningsenhetens visas här. Utan dessa villkor visas inte enheten för huvudmålservern.

  * Volymen får inte vara används för andra ändamål (mål för replikering och så vidare).
  * Volymen bör inte vara i låsläge.
  * Volymen bör inte vara en cachevolym. (Huvudmålservern installationen får inte finnas på den volymen. Processervern plus master mål anpassad installation volymen är inte berättigad till kvarhållningsvolymen. Här är installerade Processervern plus master mål volymen är cachevolymen på Huvudmålet.)
  * Typ av filsystem på volymen får inte vara FAT och FAT32.
  * Volymens kapacitet ska inte vara noll.
  * Standard-kvarhållningsvolymen för Windows är R-volym.
  * Standard-kvarhållningsvolymen för Linux är /mnt/retention.

6. Principen för återställning väljs automatiskt.
7. När du klickar på **OK** påbörja återaktivera skyddet börjar ett jobb att replikera den virtuella datorn från Azure till den lokala platsen. Du kan följa förloppet på den **jobb** fliken.

Om du vill återställa till en alternativ plats, Välj kvarhållningsenhetens och datalager som är konfigurerade för huvudmålservern. När du växlar tillbaka till den lokala platsen virtuella VMware-datorer i skyddsplan återställning efter fel att använda samma datalager som huvudmålservern. Om du vill återställa repliken Azure VM till samma lokala VM bör den lokala virtuella datorn redan är i samma datalager som huvudmålservern. Om det inte finns någon VM lokal, skapas en ny under återaktivera skydd.

![Välj ”Azure till lokala” i den nedrullningsbara menyn](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Du kan även skydda igen på en återställning plan nivå. Om du har en replikeringsgrupp kan du skydda den igen med en återställningsplan. När du skyddar med hjälp av en återställningsplan Använd tidigare värden för varje skyddad dator.

> [!NOTE]
> Replikeringsgrupper bör skyddas igen med samma huvudmålservern. En gemensam tidpunkt kan inte fastställas för dem om de är skyddade igen med en annan huvudmålserver servrar.

### <a name="run-a-failover-to-the-on-premises-site"></a>Kör en redundansväxling till den lokala platsen
När du skyddar den virtuella datorn kan du påbörja en växling från Azure till lokala.

1. Högerklicka på den virtuella datorn på sidan replikerade objekt och välj sedan **oplanerad växling**.
2. I **bekräfta redundans**kontrollerar redundansriktning (från Azure) och välj den återställningspunkt som du vill använda för redundans (senast eller den senaste programkonsekventa återställningspunkten). En programkonsekventa återställningspunkt sker innan den senaste punkten i tid och den kommer att orsaka dataförluster.
3. Under växling vid fel stängs Site Recovery virtuella Azure-datorer. När du har kontrollerat att återställningen har slutförts som förväntat, kan du kontrollera för att säkerställa att den virtuella Azure-datorer har stängts av som förväntat.

### <a name="reprotect-the-on-premises-site"></a>Skyddar den lokala platsen
När återställning har slutförts, bekräfta att den virtuella datorn för att säkerställa att de virtuella datorerna återställas i Azure tas bort. Gör du genom att högerklicka på det skyddade objektet och klicka sedan på **genomför**. Den här åtgärden startar ett jobb som tar bort tidigare återställda virtuella datorer i Azure.

När överföringen är klar dina data ska vara tillbaka på den lokala platsen, men det kommer inte att skydda. Om du vill starta replikera till Azure igen, gör du följande:

1. I **valvet**i **inställningen** > **replikerade objekt**, Välj de virtuella datorer som har misslyckats tillbaka och klicka sedan på **skydda igen**.
2. Ge värdet som ska användas för att skicka data till Azure.
3. Klicka på **OK**.

När det återaktivera skyddet har slutförts, den virtuella datorn replikeras tillbaka till Azure och du kan göra en växling vid fel.

### <a name="resolve-common-failback-issues"></a>Lösa vanliga problem med återställning efter fel
* Om du utför en skrivskyddade användaren vCenter identifiering och skydda virtuella datorer det lyckas och redundans fungerar. Under återaktivera skydd misslyckas redundans eftersom datastores inte kan identifieras. Som ett symtom visas inte datastores som anges under återaktivera skydd. Lös problemet, kan du uppdatera vCenter-autentiseringsuppgifter med ett lämpligt konto som har behörighet och försök igen. Mer information finns i [replikera VMware-datorer och fysiska servrar till Azure med Azure Site Recovery](site-recovery-vmware-to-azure-classic.md)
* När du återställas på en Linux VM och köra den lokalt, ser du att paketet Network Manager har avinstallerats från datorn. Den här avinstallationen beror Nätverkshanteraren paketet tas bort när den virtuella datorn återställs i Azure.
* När en virtuell dator konfigureras med en statisk IP-adress och växlas över till Azure, förvärvas IP-adress via DHCP. När du redundansväxlar tillbaka till lokala fortsätter den virtuella datorn att använda DHCP för att få IP-adress. Logga in på datorn och ange IP-adressen till en statisk adress om det behövs manuellt.
* Om du använder ESXi 5.5 gratisversion eller vSphere 6 Hypervisor gratisversion redundans lyckades, men återställning efter fel skulle misslyckas. Uppgradera till utvärderingslicens för båda programmen om du vill aktivera återställning efter fel.
* Om du inte kan nå konfigurationsservern från Processervern, kontrollera anslutningen till konfigurationsservern för av Telnet att configuration server-dator på port 443. Du kan även försöka att pinga konfigurationsservern från Processervern-datorn. En Process-Server bör också ha ett pulsslag när den är ansluten till konfigurationsservern.
* Om du försöker att återställas till en alternativ vCenter, se till att din nya vCenter identifieras och att huvudmålservern identifieras också. Ett typiskt symtom är att datastores inte är tillgänglig eller visas i den **skyddar** dialogrutan.
* En WS2008R2SP1-dator som skyddas som en fysisk lokal dator inte kan misslyckad tillbaka från Azure till lokala.

## <a name="fail-back-with-expressroute"></a>Växla tillbaka med ExpressRoute
Du kan växla tillbaka via en VPN-anslutning eller en ExpressRoute-anslutning. Tänk på följande om du vill använda en ExpressRoute-anslutning:

* ExpressRoute-anslutningen ska ställas in på virtuella Azure-nätverket som källdatorer växla över till och där virtuella Azure-datorer finns efter att växlingen sker.
* Data replikeras till Azure storage-konto på en offentlig slutpunkt. Använd en ExpressRoute-anslutning genom att konfigurera offentlig peering i ExpressRoute med Måldatacenter för Site Recovery replikering.
