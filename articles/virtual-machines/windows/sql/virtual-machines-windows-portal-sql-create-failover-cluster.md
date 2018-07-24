---
title: SQLServer FCI – Azure-datorer | Microsoft Docs
description: Den här artikeln beskriver hur du skapar Redundansklusterinstans för SQL Server på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: a4b63c9d184f58fe13c1271f9a425919a42fd897
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216529"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurera SQL serverinstansen för Failover-kluster på Azure Virtual Machines

Den här artikeln beskriver hur du skapar en SQL Server Failover Cluster instans (FCI) på Azure virtuella datorer i Resource Manager-modellen. Den här lösningen använder [Windows Server 2016 Datacenter edition Lagringsdirigering \(S2D\) ](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) som en programvarubaserad virtuellt SAN-nätverk som synkroniserar storage (datadiskar) mellan noder (Azure virtuella datorer) i en Windows-kluster. S2D är ny i Windows Server 2016.

Följande diagram visar den fullständiga lösningen på Azure virtual machines:

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Det föregående diagrammet visar:

- Två Azure-datorer i ett redundanskluster i Windows. När en virtuell dator är i ett redundanskluster det kallas även en *klusternod*, eller *noder*.
- Varje virtuell dator har två eller flera datadiskar.
- S2D synkroniserar data på datadisken och visar den synkroniserade storage som en lagringspool.
- Lagringspoolen anger en klusterdelad volym (CSV) i failover-kluster.
- Rollen FCI för SQL Server-klustret använder CSV-filen för dataenheterna.
- En Azure load balancer för IP-adressen för SQL Server-Redundansklusterinstanser.
- En Azure-tillgänglighetsuppsättning innehåller alla resurser.

   >[!NOTE]
   >Alla Azure-resurser finns i diagrammet finns i samma resursgrupp.

Mer information om S2D finns [Windows Server 2016 Datacenter edition Lagringsdirigering \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D stöder två typer av arkitekturer - Konvergerad och hyperkonvergerade. Arkitekturen i det här dokumentet är hyperkonvergerade. En hyper-Konvergerad infrastruktur placerar lagringen på samma servrar som är värdar för klustrade programmet. I den här arkitekturen är lagringen på varje SQL Server FCI-nod.

## <a name="licensing-and-pricing"></a>Licensiering och prissättning

På Azure virtuella datorer som du kan licensiera SQL Server med användningsbaserad betalning (PAYG) eller använda din egen licens (BYOL) VM-avbildningar. Typ av bild som du väljer påverkar hur du debiteras.

En redundansklusterinstans (FCI) av SQL Server på Azure Virtual Machines tillkommer avgifter för alla noder i FCI, inklusive de passiva noderna med PAYG licensiering. Mer information finns i [priser för SQL Server Enterprise virtuella datorer](http://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Kunder med Enterprise-avtal med Software Assurance har rätt att använda en kostnadsfri passiv FCI nod för varje aktiv nod. Om du vill dra nytta av den här förmånen i Azure, använda BYOL VM-avbildningar och sedan använda samma licens på både aktiva och passiva noder i Redundansklusterinstanser. Mer information finns i [Företagsavtal](http://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Jämföra PAYG och BYOL licens för SQL Server på Azure Virtual Machines finns i [Kom igång med virtuella datorer med SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Fullständig information om SQL-licensieringsservern finns [priser](http://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exemplet Azure-mall

Du kan skapa hela lösningen i Azure från en mall. Ett exempel på en mall finns i GitHub [Azure-Snabbstartsmallar](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Det här exemplet har inte utformats eller testats för specifika arbetsbelastningar. Du kan köra mallen för att skapa en SQL Server FCI med S2D-lagring som är ansluten till din domän. Du kan utvärdera mallen och ändra det efter dina egna behov.

## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och några saker som du behöver på plats innan du fortsätter.

### <a name="what-to-know"></a>Vad du behöver veta
Du bör ha en operativ tolkning av följande tekniker:

- [Windows-klusterteknik](http://technet.microsoft.com/library/hh831579.aspx)
- [Redundanskluster för SQL Server-instanser](http://msdn.microsoft.com/library/ms189134.aspx).

Du bör också ha en förståelse av följande tekniker:

- [Hyperkonvergerad lösning med Lagringsdirigering i Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure-resursgrupper](../../../azure-resource-manager/resource-group-portal.md)

> [!IMPORTANT]
> För närvarande den [SQL Server IaaS Agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md) stöds inte för FCI för SQL Server på Azure. Vi rekommenderar att du avinstallerar tillägget från virtuella datorer som deltar i Redundansklusterinstanser. Det här tillägget har stöd för funktioner, till exempel automatisk säkerhetskopiering och uppdatering och vissa portalen funktioner för SQL. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har avinstallerats.

### <a name="what-to-have"></a>Vad du har

Innan du följer anvisningarna i den här artikeln bör du redan ha:

- En Microsoft Azure-prenumeration.
- En Windows-domän i Azure-datorer.
- Ett konto med behörighet att skapa objekt i Azure-dator.
- Ett Azure-nätverk och undernät med tillräckligt med IP-adressutrymme för följande komponenter:
   - Båda de virtuella datorerna.
   - Redundans klustrets IP-adress.
   - En IP-adress för varje FCI.
- DNS är konfigurerat på Azure-nätverk, som pekar på domänkontrollanterna.

Dessa krav är uppfyllda, kan du gå vidare med att skapa redundansklustret. Det första steget är att skapa de virtuella datorerna.

## <a name="step-1-create-virtual-machines"></a>Steg 1: Skapa virtuella datorer

1. Logga in på den [Azure-portalen](http://portal.azure.com) med din prenumeration.

1. [Skapa en Azure-tillgänglighetsuppsättning](../tutorial-availability-sets.md).

   Tillgängligheten in grupper virtuella datorer över feldomäner och uppdateringsdomäner. Tillgänglighetsuppsättningen ser till att ditt program inte påverkas av felkritiska systemdelar som nätverksväxeln eller strömenheten för rackmonterade servrar.

   Om du inte har skapat resursgruppen för dina virtuella datorer kan du göra det när du skapar en Azure-tillgänglighetsuppsättning. Om du använder Azure-portalen för att skapa tillgänglighetsuppsättningen, gör du följande steg:

   - I Azure-portalen klickar du på **+** att öppna Azure Marketplace. Sök efter **tillgänglighetsuppsättning**.
   - Klicka på **tillgänglighetsuppsättning**.
   - Klicka på **Skapa**.
   - På den **skapa tillgänglighetsuppsättning** , anger du följande värden:
      - **Namn på**: ett namn för tillgänglighetsuppsättningen.
      - **Prenumeration**: din Azure-prenumeration.
      - **Resursgrupp**: Om du vill använda en befintlig grupp klickar du på **Använd befintlig** och väljer du gruppen från den nedrullningsbara listan. Annars väljer **Skapa ny** och Skriv ett namn för gruppen.
      - **Plats**: Ange platsen där du tänker skapa virtuella datorer.
      - **Feldomäner**: Använd förvalet (3).
      - **Uppdateringsdomäner**: Använd förvalet (5).
   - Klicka på **skapa** att skapa tillgängligheten.

1. Skapa de virtuella datorerna i tillgänglighetsuppsättningen.

   Etablera två SQL Server-datorer i Azure-tillgänglighetsuppsättning. Anvisningar finns i [etablera en virtuell dator med SQL Server i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).

   Placera båda de virtuella datorerna:

   - Samma är resursgrupp som din tillgänglighetsuppsättning i.
   - I samma nätverk som din domänkontrollant.
   - I ett undernät med tillräckligt med IP-adressutrymmet för både virtuella datorer och alla FCIs som du slutligen kan använda i det här klustret.
   - I Azure-tillgänglighetsuppsättning.   

      >[!IMPORTANT]
      >Du kan inte ange eller ändra tillgänglighetsuppsättningen när du har skapat en virtuell dator.

   Välj en avbildning från Azure Marketplace. Du kan använda en Marketplace-avbildning med som innehåller Windows Server och SQL Server eller bara Windows-Server. Mer information finns i [översikt över SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)

   De officiella SQL Server-avbildningarna i Azure-galleriet är en installerade SQL Server-instans, plus installationsprogramvaran för SQL Server och nyckeln krävs.

   Välj vilken avbildning enligt hur vill du betala för SQL Server-licens:

   - **Betala per användning licensiering**: kostnaden per sekund för dessa avbildningar innehåller SQL Server-licensiering:
      - **SQL Server 2016 Enterprise på Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard på Windows Server Datacenter 2016**
      - **SQL Server 2016 Developer på Windows Server Datacenter 2016**

   - **Bring-your-own-license (BYOL)**

      - **{BYOL} SQL Server 2016 Enterprise på Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard på Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >Ta bort förinstallerade fristående SQL Server-instansen när du har skapat den virtuella datorn. Du använder det förinstallerade mediet för SQL Server för att skapa SQL Server FCI när du har konfigurerat redundansklustret och S2D.

   Du kan också använda Azure Marketplace-avbildningar med operativsystemet. Välj en **Windows Server 2016 Datacenter** bild och installera SQL Server FCI när du har konfigurerat redundansklustret och S2D. Den här avbildningen innehåller inte SQL Server-installationsmediet. Placera installationsmediet på en plats där du kan köra SQL Server-installationen för varje server.

1. När Azure skapar dina virtuella datorer kan ansluta till varje virtuell dator med RDP.

   När du först ansluter till en virtuell dator med RDP på datorn som frågar om du vill tillåta den här datorn att identifiera i nätverket. Klicka på **Ja**.

1. Om du använder en SQL Server-baserade virtuella datoravbildningar kan du ta bort SQL Server-instansen.

   - I **program och funktioner**, högerklicka på **Microsoft SQL Server 2016 (64-bitars)** och klicka på **Avinstallera/ändra**.
   - Klicka på **ta bort**.
   - Välj standardinstansen.
   - Ta bort alla funktioner under **Databasmotortjänster**. Ta inte bort **Delade funktioner**. Se följande bild:

      ![Ta bort funktioner](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klicka på **nästa**, och klicka sedan på **ta bort**.

1. <a name="ports"></a>Öppna portar i brandväggen.

   Öppna följande portar i Windows-brandväggen på varje virtuell dator.

   | Syfte | TCP-Port | Anteckningar
   | ------ | ------ | ------
   | SQL Server | 1433 | Normal port för standardinstanser av SQL Server. Om du har använt en avbildning från galleriet, öppnas automatiskt den här porten.
   | Hälsoavsökning | 59999 | Eventuella öppna TCP-port. Konfigurera i ett senare steg, belastningsutjämnaren [hälsoavsökning](#probe) och klustret ska använda den här porten.  

1. Lägga till lagring till den virtuella datorn. Detaljerad information finns i [lägga till lagringsenheter](../premium-storage.md).

   Båda de virtuella datorerna måste ha minst två datadiskar.

   Bifoga rådiskar - inte NTFS-formaterade diskar.
      >[!NOTE]
      >Om du kopplar NTFS-formaterade diskar, kan du bara aktivera S2D med ingen kontroll för disk-behörighet.  

   Koppla minst två Premium Storage (SSD-diskar) till varje virtuell dator. Vi rekommenderar minst P30 (1 TB) diskar.

   Ange värdcachelagring till **skrivskyddad**.

   Den lagringskapacitet som du använder i produktionsmiljöer beror på din arbetsbelastning. Värdena i den här artikeln är för demonstration och testning.

1. [Lägg till de virtuella datorerna i din befintliga domän](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

När de virtuella datorerna skapas och konfigureras, kan du konfigurera failover-kluster.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Steg 2: Konfigurera Windows Failover-kluster med S2D

Nästa steg är att konfigurera failover-kluster med S2D. I det här steget ska du göra följande stegen:

1. Lägg till funktionen redundanskluster i Windows
1. Verifiera klustret
1. Skapa redundansklustret
1. Skapa molnvittnet
1. Lägg till lagringsutrymme

### <a name="add-windows-failover-clustering-feature"></a>Lägg till funktionen redundanskluster i Windows

1. Börja genom att ansluta till den första virtuella datorn med RDP med ett domänkonto som är medlem i lokala administratörer och har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägga till redundansklusterfunktionen till varje virtuell dator](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Om du vill installera funktionen för redundanskluster i användargränssnittet, gör du följande på båda de virtuella datorerna.
   - I **Serverhanteraren**, klickar du på **hantera**, och klicka sedan på **Lägg till roller och funktioner**.
   - I **guiden Lägg till roller och funktioner**, klickar du på **nästa** tills du kommer till **Välj funktioner**.
   - I **Välj funktioner**, klickar du på **Redundansklustring**. Inkludera alla nödvändiga funktioner och hanteringsverktyg. Klicka på **lägga till funktioner**.
   - Klicka på **nästa** och klicka sedan på **Slutför** för installation av funktioner.

   Om du vill installera funktionen Redundanskluster med PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Referens är nästa steg följer instruktionerna under steg3 i [en hyperkonvergerad lösning med Lagringsdirigering i Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Verifiera klustret

Den här handboken refererar till instruktioner under [verifiera kluster](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Verifiera kluster i Användargränssnittet eller med PowerShell.

Utför följande steg för att verifiera klustret med Användargränssnittet från någon av de virtuella datorerna.

1. I **Serverhanteraren**, klickar du på **verktyg**, klicka sedan på **Klusterhanteraren**.
1. I **Klusterhanteraren**, klickar du på **åtgärd**, klicka sedan på **verifiera konfiguration...** .
1. Klicka på **Nästa**.
1. På **Välj servrar eller ett kluster**, skriver du namnet på båda de virtuella datorerna.
1. På **Testningsalternativ**, Välj **kör bara valda test**. Klicka på **Nästa**.
1. På **testa val av**, omfattar alla test med undantag för **Storage**. Se följande bild:

   ![Verifiera test](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Klicka på **Nästa**.
1. På **bekräftelse**, klickar du på **nästa**.

Den **guiden Verifiera en konfiguration** körs verifieringstesterna.

För att verifiera klustret med PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

När du har validerat klustret skapa redundansklustret.

### <a name="create-the-failover-cluster"></a>Skapa redundansklustret

Den här handboken refererar till [skapar redundansklustret](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Om du vill skapa redundansklustret, behöver du:
- Namnen på de virtuella datorerna som blir noder i klustret.
- Ett namn för failover-kluster
- En IP-adress för failover-kluster. Du kan använda en IP-adress som inte används på samma Azure-nätverk och undernät som klusternoderna.

Följande PowerShell skapar ett redundanskluster. Uppdatera skriptet med namnen på noderna (VM-namn) och en tillgänglig IP-adress från det virtuella Azure-nätverket:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Skapa ett molnvittne

Molnvittne är en ny typ av kvorumvittne för kluster som lagras i en Azure Storage Blob. Detta eliminerar behovet av en separat virtuell dator som är värd för en resurs som vittne.

1. [Skapa ett molnvittne för redundansklustret](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en blobbehållare.

1. Spara åtkomstnycklarna och behållarens Webbadress.

1. Konfigurera kvorumvittne för failover-kluster. Se, [konfigurera kvorumvittnet i användargränssnittet](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) i Användargränssnittet.

### <a name="add-storage"></a>Lägg till lagringsutrymme

Diskarna för S2D måste vara tomma och utan partitioner eller andra data. För att rensa diskar följer [stegen i den här guiden](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Aktivera Store Lagringsdirigering \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Följande PowerShell aktiverar lagringsdirigering.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   I **Klusterhanteraren**, kan du nu se lagringspoolen.

1. [Skapa en volym](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   En av funktionerna i S2D är att det automatiskt skapas en lagringspool när du aktiverar den. Du är nu redo att skapa en volym. PowerShell-kommandot `New-Volume` automatiserar skapandeprocessen volym, inklusive formatering, lägga till i klustret och skapa en klusterdelad volym (CSV). I följande exempel skapas en 800 gigabyte (GB) CSV.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   När det här kommandot har slutförts, monteras en 800 GB-volym som en klusterresurs. Volymen är på `C:\ClusterStorage\Volume1\`.

   Följande diagram visar en delad klustervolym med S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Steg 3: Testa redundans vid fel

I hanteraren för redundanskluster, kontrollera att du kan flytta till lagringsresursen till den andra noden i klustret. Om du kan ansluta till klustret med **Klusterhanteraren** och flytta lagringen från en nod till den andra är du redo att konfigurera Redundansklusterinstanser.

## <a name="step-4-create-sql-server-fci"></a>Steg 4: Skapa SQL-Server FCI

När du har konfigurerat failover-kluster och alla komponenter i serverkluster inklusive lagring, kan du skapa FCI för SQL Server.

1. Ansluta till den första virtuella datorn med RDP.

1. I **Klusterhanteraren**, se till att alla klustrets kärnresurser är på den första virtuella datorn. Flytta alla resurser till den virtuella datorn.

1. Leta upp installationsmediet. Om den virtuella datorn använder något av Azure Marketplace-avbildningar, mediet finns på `C:\SQLServer_<version number>_Full`. Klicka på **installationsprogrammet**.

1. I den **SQL Server Installationscenter**, klickar du på **Installation**.

1. Klicka på **nya SQL Server failover-kluster-installation**. Följ anvisningarna i guiden för att installera SQL Server-Redundansklusterinstanser.

   Datakataloger FCI måste finnas i klustrad lagring. Med S2D är det inte en delad disk, men en monteringspunkt till en volym på varje server. S2D synkroniserar volymen mellan båda noderna. Volymen visas som en klusterdelad volym i klustret. Använda monteringspunkten CSV för datakataloger.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. När du har slutfört guiden installeras ett FCI för SQL Server på den första noden.

1. När installationsprogrammet har installerats Redundansklusterinstanser på den första noden kan du ansluta till den andra noden med RDP.

1. Öppna den **SQL Server Installationscenter**. Klicka på **Installation**.

1. Klicka på **Lägg till nod till ett SQL Server-redundanskluster**. Följ instruktionerna i guiden för att installera SQLServer och lägga till den här servern i Redundansklusterinstanser.

   >[!NOTE]
   >Om du använde ett galleri Azure Marketplace-avbildning med SQL Server, ingick SQL Server-verktyg i avbildningen. Om du inte använde den här bilden, installera SQL Server tools separat. Se [ladda ned SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Steg 5: Skapa en Azure load balancer

På Azure-datorer använder kluster en belastningsutjämnare för en IP-adress som måste finnas på en klusternod åt gången. I den här lösningen innehåller belastningsutjämnaren IP-adressen för SQL Server-Redundansklusterinstanser.

[Skapa och konfigurera en Azure load balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure portal

Skapa belastningsutjämnaren:

1. Gå till resursgruppen med de virtuella datorerna i Azure-portalen.

1. Klicka på **+ Lägg till**. Sök på Marketplace efter **belastningsutjämnare**. Klicka på **belastningsutjämnare**.

1. Klicka på **Skapa**.

1. Konfigurera belastningsutjämnaren med:

   - **Namn på**: ett namn som identifierar belastningsutjämnaren.
   - **Typ**: belastningsutjämnaren kan vara antingen offentliga eller privata. En privat belastningsutjämnare kan nås från inom samma virtuella nätverk. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via Internet, använder du en offentlig belastningsutjämnare.
   - **Virtuellt nätverk**: samma nätverk som de virtuella datorerna.
   - **Undernät**: samma undernät som de virtuella datorerna.
   - **Privat IP-adress**: samma IP-adress som tilldelats nätverksresurs för FCI för SQL Server-kluster.
   - **prenumeration**: din Azure-prenumeration.
   - **Resursgrupp**: Använd samma resursgrupp som dina virtuella datorer.
   - **Plats**: Använd samma Azure-plats som dina virtuella datorer.
   Se följande bild:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurera belastningsutjämnarens serverdelspool

1. Gå tillbaka till Azure-resursgrupp med de virtuella datorerna och leta upp den nya belastningsutjämnaren. Du kan behöva uppdatera vyn på resursgruppen. Klicka på belastningsutjämnaren.

1. Klicka på **serverdelspooler** och klicka på **+ Lägg till** att lägga till en serverdelspool.

1. Koppla serverdelspoolen tillgänglighetsuppsättningen som innehåller de virtuella datorerna.

1. Under **rikta IP-konfigurationer**, kontrollera **VM** och välj de virtuella datorer som deltar som klusternoder. Var noga med att inkludera alla virtuella datorer som är värd för Redundansklusterinstanser. 

1. Klicka på **OK** skapa backend-poolen.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurera en hälsoavsökning för belastningsutjämnaren

1. Load balancer-bladet och klicka på **hälsoavsökningar**.

1. Klicka på **+ Lägg till**.

1. På den **Lägg till hälsoavsökning** bladet <a name="probe"> </a>ställer in hälsotillståndet avsökningen parametrarna:

   - **Namn på**: ett namn för hälsoavsökningen.
   - **Protokollet**: TCP.
   - **Port**: inställt på en tillgänglig TCP-port. Den här porten kräver en öppen brandväggsport. Använd den [samma port](#ports) du angett för hälsoavsökningen i brandväggen.
   - **Intervall**: 5 sekunder.
   - **Tröskelvärde för ej felfri**: 2 upprepade fel.

1. Klicka på OK.

### <a name="set-load-balancing-rules"></a>Ange regler för belastningsutjämning

1. Load balancer-bladet och klicka på **belastningsutjämningsregler**.

1. Klicka på **+ Lägg till**.

1. Ange parametrar för regler för belastningsutjämning:

   - **Namn på**: ett namn för regler för belastningsutjämning.
   - **Frontend-IP-adress**: använda IP-adressen för SQL Server FCI-klusterresursen för nätverket.
   - **Port**: för SQL Server FCI TCP-port. Instans-standardporten är 1433.
   - **Serverdelsport**: det här värdet använder samma port som den **Port** värdet när du aktiverar **flytande IP (direkt serverreturnering)**.
   - **Serverdelspool**: Använd det namn på serverdelspool som du konfigurerade tidigare.
   - **Hälsoavsökning**: använda hälsoavsökning som du konfigurerade tidigare.
   - **Sessionspermanens**: Ingen.
   - **Inaktivitetstid (minuter)**: 4.
   - **Flytande IP (direkt serverreturnering)**: aktiverat

1. Klicka på **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Steg 6: Konfigurera kluster för avsökning

Ange parametern kluster avsökning i PowerShell.

Uppdatera variabler i skriptet nedan för att ange parametern kluster avsökning med värden från din miljö. Ta bort hakparenteser `<>` från skriptet. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Ange värden för din miljö i föregående skript. I följande lista beskrivs värdena:

   - `<Cluster Network Name>`: Windows Server Failover Cluster namnet för det virtuella nätverket. I **Klusterhanteraren** > **nätverk**högerklickar du på nätverket och på **egenskaper**. Det korrekta värdet är under **namn** på den **Allmänt** fliken. 

   - `<SQL Server FCI IP Address Resource Name>`: Resursnamnet för SQL Server FCI-IP-adress. I **Klusterhanteraren** > **roller**, under rollen FCI för SQL Server under **servernamn**, högerklicka på IP-adressresursen och på **Egenskaper**. Det korrekta värdet är under **namn** på den **Allmänt** fliken. 

   - `<ILBIP>`: ILB IP-adressen. Den här adressen konfigureras i Azure-portalen som frontend ILB-adressen. Detta är också SQL Server FCI IP-adress. Du hittar den i **Klusterhanteraren** på samma egenskapssidan där du letade upp i `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Är avsökningsporten som du konfigurerade i hälsoavsökningen för belastningsutjämnaren. Alla TCP-port som inte används är giltig. 

>[!IMPORTANT]
>Nätmask för Klusterparametern måste vara broadcast TCP IP-adressen: `255.255.255.255`.

När du ställer in avsökningen kluster kan du se alla Klusterparametrar i PowerShell. Kör följande skript:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Steg 7: Testa redundans för FCI

Testa redundans för FCI att validera klusterfunktionaliteten. Utför följande steg:

1. Ansluta till en av noderna i SQL Server FCI med RDP.

1. Öppna **Klusterhanteraren**. Klicka på **roller**. Observera vilken nod som äger FCI för SQL Server-rollen.

1. Högerklicka på SQL Server FCI-rollen.

1. Klicka på **flytta** och klicka på **bästa möjliga nod**.

**Hanteraren för redundanskluster** visas rollen och dess resurser frånkopplas. Resurserna flytta och är online på den andra noden.

### <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen för att logga in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och Anslut till SQL Server FCI-namn.

>[!NOTE]
>Om nödvändigt, kan du [ladda ned SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Begränsningar
På Azure-datorer stöds inte Microsoft Distributed Transaction Coordinator (DTC) på FCIs eftersom RPC-porten inte stöds av belastningsutjämnaren.

## <a name="see-also"></a>Se även

[Konfigurera S2D med fjärrskrivbord (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyperkonvergerad lösning med storage spaces direct](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Direct översikt över](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server-stöd för S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
