---
title: SQLServer FCI - virtuella Azure-datorer | Microsoft Docs
description: Den här artikeln förklaras hur du skapar SQL Server Redundansklusterinstans på Azure Virtual Machines.
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
ms.date: 13/22/2018
ms.author: mikeray
ms.openlocfilehash: 425310f50cebc920a71090d2017dca2a6c135991
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2018
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurera SQL Server-instans för redundanskluster på virtuella Azure-datorer

Den här artikeln förklarar hur du skapar en SQL Server Failover Cluster instansen (FCI) på virtuella Azure-datorer i Resource Manager-modellen. Den här lösningen använder [Windows Server 2016 Datacenter edition Storage Spaces Direct \(S2D\) ](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) som en programvarubaserad virtuellt SAN-nätverk som synkroniserar lagring (datadiskar) mellan noder (Azure virtuella datorer) i en Windows-kluster. S2D är ny i Windows Server 2016.

Följande diagram visar den kompletta lösningen på virtuella Azure-datorer:

![Tillgänglighetsgruppen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

I föregående diagram visas:

- Två virtuella Azure-datorer i ett redundanskluster i Windows. När en virtuell dator är i ett redundanskluster som också kallas en *klusternod*, eller *noder*.
- Varje virtuell dator har två eller flera datadiskar.
- S2D synkroniserar data på datadisken för och visar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen presenterar en klusterdelad volym (CSV) till redundansklustret.
- SQL Server-FCI-klusterrollen använder CSV: N för dataenheter.
- En Azure belastningsutjämnare för IP-adressen för SQL Server FCI.
- Tillgänglighetsuppsättningen Azure innehåller alla resurser.

   >[!NOTE]
   >Alla Azure-resurser finns i diagrammet finns i samma resursgrupp.

Mer information om S2D finns [Windows Server 2016 Datacenter edition Storage Spaces Direct \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D stöder två typer av arkitekturer - konvergerade och hyperkonvergerad. Arkitekturen i det här dokumentet är hyperkonvergerad. En infrastruktur för hyperkonvergerade placeras lagring på samma servrar som är värdar för klustrade programmet. I den här arkitekturen är lagring på varje SQL Server FCI-nod.

## <a name="licensing-and-pricing"></a>Licensierings- och prissättning

På Azure Virtual Machines du licens SQL Server med betala per användning (PAYG) eller använda din egen licens (BYOL) VM-avbildningar. Typ av bild som du väljer påverkar hur du debiteras.

Med PAYG licensiering, debiteras en redundansklusterinstans (FCI) av SQL Server på Azure Virtual Machines för alla noder i FCI, inklusive de passiva noderna. Mer information finns i [priser för SQL Server Enterprise virtuella datorer](http://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Kunder med Enterprise-avtal med Software Assurance har rätt att använda en kostnadsfri passiva FCI nod för varje aktiv nod. Om du vill dra nytta av den här funktionen i Azure, använda BYOL VM-avbildningar och använder sedan samma licens på aktiva och passiva noder i Redundansklusterinstanser. Mer information finns i [Enterprise-avtal](http://www.microsoft.com/en-us/Licensing/licensing-programs/enterprise.aspx).

Om du vill jämföra PAYG och BYOL licensiering för SQL Server på Azure Virtual Machines finns [Kom igång med SQL virtuella datorer](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Fullständig information om licensiering SQL Server finns [priser](http://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exemplet Azure-mall

Du kan skapa hela lösningen från en mall i Azure. Ett exempel på en mall finns i GitHub [Azure Quickstart mallar](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Det här exemplet är inte utformad eller testas för en viss arbetsbelastning. Du kan köra mallen för att skapa en SQL Server-FCI med S2D lagring ansluten till din domän. Du kan utvärdera mallen och ändra den efter dina egna behov.

## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och några saker som du behöver på plats innan du fortsätter.

### <a name="what-to-know"></a>Vad du behöver veta
Du bör ha en operativa förståelse av följande tekniker:

- [Tekniker för Windows-kluster](http://technet.microsoft.com/library/hh831579.aspx)
-  [Instanser av SQL Server-redundanskluster](http://msdn.microsoft.com/library/ms189134.aspx).

Du bör också ha en förståelse av följande tekniker:

- [Hyper-Konvergerad lösning med lagringsutrymmen i Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Azure-resursgrupper](../../../azure-resource-manager/resource-group-portal.md)

### <a name="what-to-have"></a>Vad du har

Innan du följer anvisningarna i den här artikeln bör du redan har:

- En Microsoft Azure-prenumeration.
- En Windows-domän på virtuella Azure-datorer.
- Ett konto med behörighet att skapa objekt i den virtuella Azure-datorn.
- Ett virtuellt Azure-nätverk och undernät med tillräckliga IP-adressutrymmet för följande komponenter:
   - Både virtuella datorer.
   - Failover-kluster IP-adressen.
   - En IP-adress för varje FCI.
- DNS har konfigurerats i Azure-nätverket, pekar på domänkontrollanterna.

Med dessa krav är på plats, kan du fortsätta med att skapa klustret för växling vid fel. Det första steget är att skapa de virtuella datorerna.

## <a name="step-1-create-virtual-machines"></a>Steg 1: Skapa virtuella datorer

1. Logga in på den [Azure-portalen](http://portal.azure.com) med din prenumeration.

1. [Skapa en Azure tillgänglighetsuppsättning](../tutorial-availability-sets.md).

   Tillgängligheten in grupper virtuella datorer över feldomäner och uppdatera domäner. Tillgänglighetsuppsättningen ser till att programmet inte påverkas av enskilda felpunkter, t.ex. nätverks-switch eller en samling servrar power enheten.

   Om du inte har skapat resursgruppen för dina virtuella datorer kan du göra det när du skapar en Azure tillgänglighetsuppsättning. Om du använder Azure-portalen för att skapa tillgänglighetsuppsättningen gör du följande steg:

   - I Azure-portalen klickar du på **+** att öppna Azure Marketplace. Sök efter **tillgänglighetsuppsättning**.
   - Klicka på **tillgänglighetsuppsättning**.
   - Klicka på **Skapa**.
   - På den **skapa tillgänglighetsuppsättning** bladet anger du följande värden:
      - **Namnet**: ett namn för tillgänglighetsuppsättningen.
      - **Prenumerationen**: din Azure-prenumeration.
      - **Resursgruppen**: Om du vill använda en befintlig grupp klickar du på **använda befintliga** och välj gruppen från den nedrullningsbara listan. Annars väljer **Skapa nytt** och ange ett namn för gruppen.
      - **Plats**: Ange den plats där du planerar att skapa virtuella datorer.
      - **Fault domäner**: Använd standard (3).
      - **Uppdatera domäner**: Använd standard (5).
   - Klicka på **skapa** att skapa tillgängligheten.

1. Skapa virtuella datorer i tillgänglighetsuppsättningen.

   Etablera två virtuella SQL Server-datorer i Azure tillgänglighetsuppsättning. Instruktioner finns i [etablera en virtuell dator med SQL Server i Azure portal](virtual-machines-windows-portal-sql-server-provision.md).

   Placera både virtuella datorer:

   - I samma Azure används resursgrupp som din tillgänglighetsuppsättning.
   - På samma nätverk som en domänkontrollant.
   - I ett undernät med tillräckligt utrymme för IP-adresser för både virtuella datorer och alla FCIs som du kan använda förr eller senare i det här klustret.
   - I Azure tillgänglighetsuppsättning.   

      >[!IMPORTANT]
      >Du kan inte ange eller ändra tillgänglighetsuppsättning när en virtuell dator har skapats.

   Välj en bild från Azure Marketplace. Du kan använda en marknadsplats avbildningen med som omfattar Windows Server och SQL Server eller bara Windows-Server. Mer information finns i [översikt över SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)

   De officiella SQL Server-avbildningarna i Azure-galleriet är en installerade SQL Server-instans, plus installationsprogrammet för SQL Server och nödvändig nyckel.

   Välj den högra bilden enligt hur vill du betala för SQL Server-licens:

   - **Betala per användning licensiering**: kostnaden per sekund för dessa avbildningar innehåller SQL Server-licensiering:
      - **SQL Server 2016 Enterprise på Windows Server Datacenter 2016**
      - **SQL Server 2016 Standard på Windows Server Datacenter 2016**
      - **SQL Server 2016 utvecklare i Windows Server Datacenter 2016**

   - **Bring-your-own-license (BYOL)**

      - **{BYOL} SQL Server 2016 Enterprise på Windows Server Datacenter 2016**
      - **{BYOL} SQL Server 2016 Standard på Windows Server Datacenter 2016**

   >[!IMPORTANT]
   >När du har skapat den virtuella datorn tar du bort förinstallerade fristående SQL Server-instansen. Du använder det förinstallerade mediet för SQL Server för att skapa SQL Server-FCI när du har konfigurerat failover-kluster och S2D.

   Du kan också använda Azure Marketplace-bilder med operativsystemet. Välj en **Windows Server 2016 Datacenter** avbildningen och installera SQL Server FCI när du har konfigurerat failover-kluster och S2D. Den här avbildningen innehåller inte SQL Server-installationsmedia. Placera installationsmediet på en plats där du kan köra SQL Server-installationen för varje server.

1. När Azure skapar virtuella datorer kan ansluta till varje virtuell dator med RDP.

   När du först ansluta till en virtuell dator med RDP frågar datorn om du vill tillåta den här datorn att identifiera i nätverket. Klicka på **Ja**.

1. Om du använder en SQL Server-baserade virtuella avbildningar kan du ta bort SQL Server-instansen.

   - I **program och funktioner**, högerklicka på **Microsoft SQL Server 2016 (64-bitars)** och på **Avinstallera/ändra**.
   - Klicka på **ta bort**.
   - Välj standardinstansen.
   - Ta bort alla funktioner under **Databasmotortjänster**. Ta inte bort **Delade funktioner i**. Se följande bild:

      ![Ta bort funktioner](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klicka på **nästa**, och klicka sedan på **ta bort**.

1. <a name="ports"></a>Öppna portar i brandväggen.

   Öppna följande portar i Windows-brandväggen på varje virtuell dator.

   | Syfte | TCP-Port | Anteckningar
   | ------ | ------ | ------
   | SQL Server | 1433 | Normal port för standardinstanser av SQL Server. Om du har använt en avbildning från galleriet öppnas automatiskt den här porten.
   | Hälsoavsökningen | 59999 | Alla öppna TCP-port. I ett senare steg, konfigurera belastningsutjämnaren [hälsoavsökningen](#probe) och klustret använder den här porten.  

1. Lägga till lagringsenheter till den virtuella datorn. Detaljerad information finns i [lägga till lagringsenheter](../premium-storage.md).

   Både virtuella datorer måste ha minst två hårddiskar.

   Koppla rådata diskar - inte NTFS-formaterade diskar.
      >[!NOTE]
      >Du kan bara aktivera S2D med ingen kontroll av behörighet om du kopplar NTFS-formaterade diskar.  

   Koppla minst två Premium-lagring (SSD-diskar) på varje virtuell dator. Vi rekommenderar minst P30 diskar (1 TB).

   Ange värdcachelagring till **skrivskyddad**.

   Lagringskapacitet som du använder i produktionsmiljöer beror på din arbetsbelastning. De värden som beskrivs i den här artikeln är för demonstration och testning.

1. [Lägga till de virtuella datorerna i din befintliga domän](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

När virtuella datorer skapas och konfigureras, kan du konfigurera failover-kluster.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Steg 2: Konfigurera Windows-redundanskluster med S2D

Nästa steg är att du konfigurerar ett redundanskluster med S2D. I det här steget ska du göra följande stegen:

1. Lägger till funktionen för redundanskluster i Windows
1. Validera att klustret
1. Skapa failover-kluster
1. Skapa moln vittne
1. Lägga till lagringsenheter

### <a name="add-windows-failover-clustering-feature"></a>Lägger till funktionen för redundanskluster i Windows

1. Börja genom att ansluta till den första virtuella datorn med RDP med ett domänkonto som är medlem i lokala administratörer och har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägga till funktionen kluster för växling vid fel i varje virtuell dator](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Utför följande steg på både virtuella datorer om du vill installera funktionen kluster för växling vid fel i användargränssnittet.
   - I **Serverhanteraren**, klickar du på **hantera**, och klicka sedan på **Lägg till roller och funktioner**.
   - I **guiden Lägg till roller och funktioner**, klickar du på **nästa** tills du kommer till **Välj funktioner**.
   - I **Välj funktioner**, klickar du på **redundanskluster**. Inkludera alla nödvändiga funktioner och hanteringsverktyg. Klicka på **lägga till funktioner**.
   - Klicka på **nästa** och klicka sedan på **Slutför** att installera funktioner.

   Om du vill installera funktionen kluster för växling vid fel med PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna.

   ```PowerShell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

För referens anger nästa steg följer du anvisningarna under steg3 av [Hyper-Konvergerad lösning med lagringsutrymmen i Windows Server 2016](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Validera att klustret

Den här guiden refererar till anvisningarna under [verifiera kluster](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Validera att klustret i Användargränssnittet eller med PowerShell.

Utför följande steg för att verifiera klustret med Användargränssnittet från någon av de virtuella datorerna.

1. I **Serverhanteraren**, klickar du på **verktyg**, klicka på **Klusterhanteraren**.
1. I **Klusterhanteraren**, klickar du på **åtgärd**, klicka på **verifiera konfiguration...** .
1. Klicka på **Nästa**.
1. På **Välj servrar eller ett kluster**, skriver du namnet på både virtuella datorer.
1. På **test,**, Välj **kör bara valda test**. Klicka på **Nästa**.
1. På **testa markeringen**, inkluderar alla test med undantag **lagring**. Se följande bild:

   ![Verifiera test](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Klicka på **Nästa**.
1. På **bekräftelse**, klickar du på **nästa**.

Den **guiden Verifiera en konfiguration** körs verifieringstesterna.

Om du vill validera att klustret med PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna.

   ```PowerShell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

När du validera att klustret kan du skapa klustret.

### <a name="create-the-failover-cluster"></a>Skapa failover-kluster

Den här guiden refererar till [skapar redundansklustret](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Om du vill skapa redundansklustret, behöver du:
- Namnen på de virtuella datorerna som blivit klusternoderna.
- Ett namn för failover-kluster
- En IP-adress för failover-kluster. Du kan använda en IP-adress som inte används på samma virtuella Azure-nätverk och undernät som klusternoder.

Följande PowerShell skapar ett redundanskluster. Uppdatera skriptet med namnen på noder (virtuella namn) och en tillgänglig IP-adress från Azure VNET:

```PowerShell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

### <a name="create-a-cloud-witness"></a>Skapa ett moln vittne

Molnet vittne är en ny typ av kluster kvorumvittne lagras i en Azure Storage Blob. Detta eliminerar behovet av en separat virtuell dator som värd för en resurs som vittne.

1. [Skapa ett moln vittne för failover-kluster](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en blob-behållare.

1. Spara snabbtangenterna och webbadressen för behållaren.

1. Konfigurera kvorumvittne för failover-kluster klustret. Visa, [konfigurera kvorumvittnet i användargränssnittet]. (http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) i Användargränssnittet.

### <a name="add-storage"></a>Lägga till lagringsenheter

Diskar för S2D måste vara tomt och utan partitioner eller andra data. Rensa diskar följer [stegen i den här guiden](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Aktivera Store lagringsutrymmen Direct \(S2D\)](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Följande PowerShell kan lagringsutrymmen direkt.  

   ```PowerShell
   Enable-ClusterS2D
   ```

   I **Klusterhanteraren**, du kan nu se lagringspoolen.

1. [Skapa en volym](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   En av funktionerna i S2D är att det automatiskt skapas en lagringspool när du aktiverar den. Du är nu redo att skapa en volym. PowerShell-kommandot `New-Volume` automatiserar processen volymen, inklusive formatering, lägger till i klustret och skapa en klusterdelad volym (CSV). I följande exempel skapas en 800 GB (Gigabyte) CSV.

   ```PowerShell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   När det här kommandot har slutförts, är en 800 GB-volym monterad som en klusterresurs. Volymen är på `C:\ClusterStorage\Volume1\`.

   Följande diagram visar en delad klustervolym med S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Steg 3: Testa redundans-kluster

I hanteraren för redundanskluster, kontrollera att du kan flytta storage-resursen till den andra noden i klustret. Om du kan ansluta till klustret med **Klusterhanteraren** och flytta lagringen från en nod till den andra är du redo att konfigurera Redundansklusterinstanser.

## <a name="step-4-create-sql-server-fci"></a>Steg 4: Skapa SQLServer FCI

När du har konfigurerat failover-kluster och alla komponenter i serverkluster inklusive lagring, kan du skapa SQL Server FCI.

1. Ansluta till den första virtuella datorn med RDP.

1. I **Klusterhanteraren**, se till att alla klustrets kärnresurser är på den första virtuella datorn. Flytta alla resurser till den virtuella datorn.

1. Leta reda på installationsmediet. Om den virtuella datorn använder något av Azure Marketplace-bilder, mediet finns på `C:\SQLServer_<version number>_Full`. Klicka på **installationsprogrammet**.

1. I den **SQL Server Installationscenter**, klickar du på **Installation**.

1. Klicka på **nya SQL-servern failover cluster installation**. Följ instruktionerna i guiden för att installera SQL Server FCI.

   Datakataloger FCI måste finnas i klustrad lagring. Med S2D är det inte en delad disk utan en monteringspunkt till en volym på varje server. S2D synkroniserar volymen i båda noderna. Volymen visas som en klusterdelad volym i klustret. Använda monteringspunkten CSV för datakataloger.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. När du har slutfört guiden installeras ett SQL Server FCI på den första noden.

1. När Redundansklusterinstanser har installeras på den första noden, ansluta till den andra noden med RDP.

1. Öppna den **SQL Server Installationscenter**. Klicka på **Installation**.

1. Klicka på **Lägg till nod i ett redundanskluster för SQL Server**. Följ instruktionerna i guiden för att installera SQLServer och lägga till den här servern i Redundansklusterinstanser.

   >[!NOTE]
   >Om du använde en galleriet Azure Marketplace-avbildning med SQL Server, ingick SQL Server-verktyg i avbildningen. Om du inte använde den här avbildningen, installera SQL Server-verktyg separat. Se [Hämta SQL Server Management Studio (SSMS)](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Steg 5: Skapa Azure belastningsutjämnare

På Azure virtual machines använder kluster en belastningsutjämnare för en IP-adress som måste finnas på en klusternod åt gången. I den här lösningen innehåller IP-adress i belastningsutjämnaren för SQL Server FCI.

[Skapa och konfigurera en Azure belastningsutjämnare](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure-portalen

Skapa belastningsutjämnaren:

1. Gå till resursgruppen med virtuella datorer i Azure-portalen.

1. Klicka på **+ Lägg till**. Sök Marketplace för **belastningsutjämnare**. Klicka på **belastningsutjämnare**.

1. Klicka på **Skapa**.

1. Konfigurera belastningsutjämnaren med:

   - **Namnet**: ett namn som identifierar belastningsutjämnaren.
   - **Typen**: belastningsutjämnaren kan vara offentligt eller privat. En privat belastningsutjämnare kan nås från inom samma virtuella nätverk. De flesta Azure-program kan använda en privat belastningsutjämnare. Om programmet behöver åtkomst till SQL Server direkt via Internet, kan du använda en offentlig belastningsutjämnare.
   - **Virtuellt nätverk**: I samma nätverk som de virtuella datorerna.
   - **Undernät**: samma undernät som de virtuella datorerna.
   - **Privata IP-adressen**: samma IP-adress som du tilldelade till SQL Server FCI-klusterresursen för nätverket.
   - **Prenumerationen**: din Azure-prenumeration.
   - **Resursgruppen**: använda samma resursgrupp som de virtuella datorerna.
   - **Plats**: använda samma Azure-plats som de virtuella datorerna.
   Se följande bild:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurera belastningsutjämnarens serverdelspool

1. Gå tillbaka till Azure-resursgrupp med de virtuella datorerna och leta upp den nya belastningsutjämnaren. Du kan behöva uppdatera vyn i resursgruppen. Klicka på belastningsutjämnaren.

1. Klicka på **serverdelspooler** och på **+ Lägg till** att lägga till en serverdelspool.

1. Associera serverdelspoolen tillgänglighetsuppsättning som innehåller de virtuella datorerna.

1. Under **mål IP nätverkskonfigurationer**, kontrollera **VIRTUELLA** och välj de virtuella datorerna som deltar som klusternoder. Se till att inkludera alla virtuella datorer som är värd för Redundansklusterinstanser. 

1. Klicka på **OK** att skapa serverdelspoolen.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurera en belastningsutjämnaren, hälsoavsökningen

1. Klicka på bladet belastningen belastningsutjämnaren **hälsoavsökning**.

1. Klicka på **+ Lägg till**.

1. På den **Lägg till hälsoavsökningen** bladet <a name="probe"> </a>ange hälsotillståndet avsökningen parametrar:

   - **Namnet**: ett namn för hälsoavsökningen.
   - **Protokollet**: TCP.
   - **Port**: inställd på en tillgänglig TCP-port. Den här porten kräver en öppen brandväggsport. Använd den [samma port](#ports) du anger för hälsoavsökningen i brandväggen.
   - **Intervallet**: 5 sekunder.
   - **Tröskelvärde för ohälsosamt värde**: 2 upprepade fel.

1. Klicka på OK.

### <a name="set-load-balancing-rules"></a>Ange regler för belastningsutjämning

1. Klicka på bladet belastningen belastningsutjämnaren **belastningsutjämningsregler**.

1. Klicka på **+ Lägg till**.

1. Ange parametrar för regler för belastningsutjämning:

   - **Namnet**: ett namn för regler för belastningsutjämning.
   - **Frontend-IP-adressen**: använda IP-adressen för SQL Server FCI-klusterresursen för nätverket.
   - **Port**: för SQL Server FCI TCP-port. Instans-standardporten är 1433.
   - **Serverdelsport**: det här värdet använder samma port som den **Port** värdet när du aktiverar **flytande IP (direkt serverreturnering)**.
   - **Serverdelspool**: Använd namn på backend-pool som du tidigare har konfigurerat.
   - **Hälsoavsökningen**: Använd hälsoavsökningen som du tidigare har konfigurerat.
   - **Sessionspersistence**: Ingen.
   - **Inaktivitetstid (minuter)**: 4.
   - **Flytande IP (direkt serverreturnering)**: aktiverat

1. Klicka på **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Steg 6: Konfigurera kluster för avsökningen

Ange parametern klustret avsökningen port i PowerShell.

Uppdatera variabler i skriptet nedan för att ange parametern klustret avsökningen port, med värden från din miljö. Ta bort hakparenteser `<>` från skriptet. 

   ```PowerShell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

I det här skriptet, ange värden för din miljö. I följande lista beskrivs värdena:

   - `<Cluster Network Name>`: Windows Server Failover Cluster namn för nätverket. I **Klusterhanteraren** > **nätverk**, högerklicka på nätverket och på **egenskaper**. Det korrekta värdet är under **namn** på den **allmänna** fliken. 

   - `<SQL Server FCI IP Address Resource Name>`: Resursnamnet för FCI SQL Server-IP-adress. I **Klusterhanteraren** > **roller**, under rollen SQL Server FCI under **servernamn**, högerklicka på IP-adressresurs och på **Egenskaper**. Det korrekta värdet är under **namn** på den **allmänna** fliken. 

   - `<ILBIP>`: ILB IP-adressen. Den här adressen har konfigurerats i Azure-portalen som ILB frontend-adress. Detta är även SQL Server FCI IP-adress. Du hittar den i **Klusterhanteraren för växling** på samma egenskapssidan där du letade upp i `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Är avsökningsport du konfigurerade i den belastningsutjämnaren, hälsoavsökningen. Alla oanvända TCP-port är giltig. 

>[!IMPORTANT]
>Nätmask för Klusterparametern måste vara broadcast TCP-IP-adressen: `255.255.255.255`.

När du ställer in kluster-avsökning kan du se alla Klusterparametrar i PowerShell. Kör följande skript:

   ```PowerShell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Steg 7: Testa redundans för FCI

Testa redundans för FCI att validera klusterfunktionaliteten. Utför följande steg:

1. Ansluta till en av noderna i SQL Server FCI med RDP.

1. Öppna **Klusterhanteraren**. Klicka på **roller**. Observera vilken nod som äger den SQL Server FCI-rollen.

1. Högerklicka på SQL Server FCI-rollen.

1. Klicka på **flytta** och på **bästa möjliga nod**.

**Hanteraren för redundanskluster** visas rollen och dess resurser i offlineläge. Resurserna flytta sedan och anslutas på den andra noden.

### <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen för att logga in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och Anslut till SQL Server FCI-namn.

>[!NOTE]
>Om nödvändigt, kan du [Hämta SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Begränsningar
På Azure virtual machines stöds inte Microsoft Distributed Transaction Coordinator (DTC) på FCIs eftersom RPC-porten inte stöds av belastningsutjämnaren.

## <a name="see-also"></a>Se även

[Konfigurera S2D med fjärrskrivbord (Azure)](http://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyper-Konvergerad lösning med lagringsutrymmen direkt](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Direkt översikt över lagringsutrymmen i](http://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server-stöd för S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
