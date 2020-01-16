---
title: SQL Server FCI – Azure Virtual Machines | Microsoft Docs
description: Den här artikeln beskriver hur du skapar en SQL Server-redundanskluster på virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/11/2018
ms.author: mikeray
ms.openlocfilehash: 20c231e4f3052797eac79a3c97a3d8148690b8c5
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75965422"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurera en SQL Server-redundanskluster på virtuella Azure-datorer

Den här artikeln beskriver hur du skapar en SQL Server-FCI (failover Cluster instance) på virtuella Azure-datorer i Azure Resource Manager-modellen. Den här lösningen använder [Windows Server 2016 Data Center edition Lagringsdirigering](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) som ett programvarubaserad virtuellt SAN-nätverk som synkroniserar lagringen (data diskar) mellan noderna (virtuella Azure-datorer) i ett Windows-kluster. Lagringsdirigering var nytt i Windows Server 2016.

Följande diagram visar den kompletta lösningen på virtuella Azure-datorer:

![Den kompletta lösningen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Det här diagrammet visar:

- Två virtuella Azure-datorer i ett Windows Server-redundanskluster. När en virtuell dator finns i ett redundanskluster kallas den även för en *klusternod* eller *nod*.
- Varje virtuell dator har två eller flera data diskar.
- Lagringsdirigering synkroniserar data på data diskarna och presenterar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen visar en klusterdelad volym (CSV) för redundansklustret.
- Kluster rollen SQL Server FCI använder CSV-filen för data enheterna.
- En Azure Load Balancer för att lagra IP-adressen för SQL Server FCI.
- En tillgänglighets uppsättning i Azure innehåller alla resurser.

>[!NOTE]
>Alla Azure-resurser i diagrammet finns i samma resurs grupp.

Mer information om Lagringsdirigering finns i [Windows Server 2016 Data Center edition Lagringsdirigering](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Lagringsdirigering stöder två typer av arkitekturer: konvergerade och Hyper-konvergerade. Arkitekturen i det här dokumentet är Hyper-konvergerad. En Hyper-konvergerad infrastruktur placerar lagringen på samma servrar som är värdar för det klustrade programmet. I den här arkitekturen finns lagringen på varje SQL Server FCI-nod.

## <a name="licensing-and-pricing"></a>Licensiering och priser

På Azure Virtual Machines kan du licensiera SQL Server med hjälp av PAYG (betala per användning) eller BYOL-avbildningar (betala per användning). Vilken typ av avbildning du väljer påverkar hur du debiteras.

Med betala per användning-licens debiteras du för alla noder i FCI, inklusive de passiva noderna, i en FCI (failover Cluster instance) för SQL Server på virtuella Azure-datorer. Mer information finns i [SQL Server Enterprise Virtual Machines prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Om du har Enterprise-avtal med Software Assurance kan du använda en kostnads fri passiv FCI-nod för varje aktiv nod. Om du vill dra nytta av den här förmånen i Azure använder du BYOL VM-avbildningar och använder samma licens på både aktiva och passiva noder i FCI. Mer information finns i [Enterprise-avtal](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Information om hur du jämför "betala per användning" och BYOL-licensiering för SQL Server på Azure Virtual Machines finns i [Kom igång med virtuella SQL](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms)-datorer.

Fullständig information om licens SQL Server finns i [prissättning](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exempel på Azure-mall

Du kan skapa hela lösningen i Azure från en mall. Ett exempel på en mall finns i GitHub för [Azure snabb start](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Det här exemplet är inte utformat eller testat för någon speciell arbets belastning. Du kan köra mallen för att skapa en SQL Server-FCI med Lagringsdirigering lagring som är ansluten till din domän. Du kan utvärdera mallen och ändra den så att den passar dina behov.

## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och har på plats innan du börjar.

### <a name="what-to-know"></a>Vad du behöver veta
Du bör ha en operationell förståelse för dessa tekniker:

- [Windows kluster tekniker](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instanser av redundanskluster](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

En sak att vara medveten om är att på ett Azure IaaS-redundanskluster för virtuella datorer i Azure, rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans, vilket gör att ytterligare nätverkskort och undernät inte behövs på ett gäst kluster för en Azure IaaS-dator. I kluster verifierings rapporten visas en varning om att noderna bara kan kommas åt i ett enda nätverk. Du kan ignorera den här varningen på virtuella Azure IaaS-kluster för gästa gäster.

Du bör också ha en allmän förståelse för dessa tekniker:

- [Hyper-konvergerade lösningar som använder Lagringsdirigering i Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Resurs grupper i Azure](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> För närvarande stöds SQL Server redundanskluster på Azure Virtual Machines endast med [läget för förenklad hantering](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) i [SQL Server IaaS agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md). Om du vill ändra från fullständigt tillägg till Lightweight tar du bort den **virtuella SQL-datorns** resurs för motsvarande virtuella datorer och registrerar dem sedan med resurs leverantören för SQL-VM i Lightweight-läge. När du tar bort den **virtuella SQL-datorns** resurs med hjälp av Azure Portal **avmarkerar du kryss rutan bredvid rätt virtuell dator**. Det fullständiga tillägget har stöd för funktioner som automatisk säkerhets kopiering, uppdatering och avancerad Portal hantering. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har installerats om i läget för förenklad hantering.

### <a name="what-to-have"></a>Vad ska jag ha

Innan du slutför stegen i den här artikeln bör du redan ha:

- En Microsoft Azure prenumeration.
- En Windows-domän på Azure Virtual Machines.
- Ett konto som har behörighet att skapa objekt både på virtuella Azure-datorer och i Active Directory.
- Ett virtuellt Azure-nätverk och undernät med tillräckligt med IP-adressutrymme för dessa komponenter:
   - Båda virtuella datorerna.
   - IP-adressen för klustret för växling vid fel.
   - En IP-adress för varje FCI.
- DNS konfigurerat på Azure-nätverket och pekar på domän kontrol Lanterna.

Med dessa krav på plats kan du börja skapa ett redundanskluster. Det första steget är att skapa de virtuella datorerna.

## <a name="step-1-create-the-virtual-machines"></a>Steg 1: skapa de virtuella datorerna

1. Logga in på [Azure Portal](https://portal.azure.com) med din prenumeration.

1. [Skapa en tillgänglighets uppsättning i Azure](../tutorial-availability-sets.md).

   Tillgänglighets uppsättningen grupperar virtuella datorer över fel domäner och uppdaterings domäner. Det säkerställer att programmet inte påverkas av enskilda felpunkter, t. ex. nätverks växeln eller strömförsörjnings enheten för en rack Server.

   Om du inte har skapat resurs gruppen för dina virtuella datorer gör du det när du skapar en Azures tillgänglighets uppsättning. Gör så här om du använder Azure Portal för att skapa tillgänglighets uppsättningen:

   1. I Azure Portal väljer du **skapa en resurs** för att öppna Azure Marketplace. Sök efter **tillgänglighets uppsättning**.
   1. Välj **tillgänglighets uppsättning**.
   1. Välj **Skapa**.
   1. Ange följande värden under **skapa tillgänglighets uppsättning**:
      - **Namn**: ett namn på tillgänglighets uppsättningen.
      - **Prenumeration**: din Azure-prenumeration.
      - **Resurs grupp**: om du vill använda en befintlig grupp klickar du på **Välj befintlig** och väljer sedan gruppen i listan. Annars väljer du **Skapa nytt** och anger ett namn för gruppen.
      - **Plats**: Ange den plats där du planerar att skapa virtuella datorer.
      - **Fel domäner**: Använd standardvärdet (**3**).
      - **Uppdaterings domäner**: Använd standardvärdet (**5**).
   1. Skapa tillgänglighets uppsättningen genom att välja **skapa** .

1. Skapa de virtuella datorerna i tillgänglighets uppsättningen.

   Etablera två SQL Server virtuella datorer i tillgänglighets uppsättningen för Azure. Instruktioner finns i [etablera en SQL Server virtuell dator i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Placera båda virtuella datorerna:

   - I samma Azure-resurs grupp som din tillgänglighets uppsättning.
   - I samma nätverk som domänkontrollanten.
   - I ett undernät som har tillräckligt med IP-adressutrymme för både virtuella datorer och alla skyddas som du kan använda i klustret.
   - I Azures tillgänglighets uppsättning.

      >[!IMPORTANT]
      >Du kan inte ange eller ändra tillgänglighets uppsättningen när du har skapat en virtuell dator.

   Välj en avbildning från Azure Marketplace. Du kan använda en Azure Marketplace-avbildning som innehåller Windows Server och SQL Server, eller använda en som bara innehåller Windows Server. Mer information finns i [Översikt över SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).

   De officiella SQL Server avbildningarna i Azure-galleriet innehåller en installerad SQL Server instans, SQL Server-installations program och nödvändig nyckel.

   Välj rätt bild, baserat på hur du vill betala för SQL Server-licensen:

   - **Betala per användning-licensiering**. Kostnaden per sekund för de här avbildningarna omfattar SQL Server-licensen:
      - **SQL Server 2016 Enterprise på Windows Server 2016 Data Center**
      - **SQL Server 2016 standard på Windows Server 2016 Data Center**
      - **SQL Server 2016-utvecklare på Windows Server 2016 Data Center**

   - **Hämta din egen licens (BYOL)**

      - **BYOL SQL Server 2016 Enterprise på Windows Server 2016 Data Center**
      - **BYOL SQL Server 2016 standard på Windows Server 2016 Data Center**

   >[!IMPORTANT]
   >När du har skapat den virtuella datorn tar du bort den förinstallerade fristående SQL Server-instansen. Du använder förinstallerade SQL Server media för att skapa SQL Server-FCI när du har ställt in klustret för växling vid fel och Lagringsdirigering.

   Du kan också använda Azure Marketplace-avbildningar som bara innehåller operativ systemet. Välj en **Windows Server 2016 Data Center** -avbildning och installera SQL Server FCI när du har ställt in klustret för växling vid fel och Lagringsdirigering. Den här avbildningen innehåller inte SQL Server installationsmedia. Placera installations mediet för SQL Server på en plats där du kan köra det för varje server.

1. När Azure har skapat de virtuella datorerna ansluter du till var och en med hjälp av RDP.

   När du först ansluter till en virtuell dator med hjälp av RDP frågar en fråga om du vill att datorn ska kunna identifieras i nätverket. Välj **Ja**.

1. Om du använder någon av de SQL Server-baserade avbildningarna av virtuella datorer tar du bort SQL Server-instansen.

   1. I **program och funktioner**högerklickar du på **Microsoft SQL Server 2016 (64-bitars)** och väljer **Avinstallera/ändra**.
   1. Välj **Ta bort**.
   1. Välj standard instansen.
   1. Ta bort alla funktioner under **databas motor tjänster**. Ta inte bort **Delade funktioner**. Du ser något som liknar följande skärm bild:

      ![Välj funktioner](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Välj **Nästa**och välj sedan **ta bort**.

1. <a name="ports"></a>Öppna brand Väggs portarna.

   Öppna dessa portar i Windows-brandväggen på varje virtuell dator:

   | Syfte | TCP-port | Anteckningar
   | ------ | ------ | ------
   | SQL Server | 1433 | Normal port för standard instanser av SQL Server. Om du använde en avbildning från galleriet öppnas porten automatiskt.
   | Hälsoavsökning | 59999 | Alla öppna TCP-portar. I ett senare steg konfigurerar du belastnings utjämningens [hälso avsökning](#probe) och klustret för att använda den här porten.  

1. Lägg till lagring på den virtuella datorn. Detaljerad information finns i [Lägg till lagring](../disks-types.md).

   Båda de virtuella datorerna behöver minst två data diskar.

   Bifoga RAW-diskar, inte NTFS-formaterade diskar.
      >[!NOTE]
      >Om du kopplar NTFS-formaterade diskar kan du bara aktivera Lagringsdirigering utan en disk kontroll.  

   Koppla minst två Premium-SSD till varje virtuell dator. Vi rekommenderar minst P30-diskar (1 TB).

   Ange cachelagring av värden till **skrivskyddad**.

   Vilken lagrings kapacitet du använder i produktions miljöer beror på din arbets belastning. Värdena som beskrivs i den här artikeln är för demonstration och testning.

1. [Lägg till de virtuella datorerna i din befintliga domän](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

När du har skapat och konfigurerat de virtuella datorerna kan du konfigurera klustret för växling vid fel.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Steg 2: Konfigurera Windows Server-redundansklustret med Lagringsdirigering

Nästa steg är att konfigurera redundansklustret med Lagringsdirigering. I det här steget ska du slutföra dessa del steg:

1. Lägg till funktionen kluster för växling vid fel i Windows Server.
1. Verifiera klustret.
1. Skapa klustret för växling vid fel.
1. Skapa moln vittnet.
1. Lägg till lagring.

### <a name="add-windows-server-failover-clustering"></a>Lägg till kluster för växling vid fel i Windows Server

1. Anslut till den första virtuella datorn med RDP genom att använda ett domän konto som är medlem i den lokala administratören och som har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägg till kluster för växling vid fel på varje virtuell dator](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Om du vill installera kluster för växling vid fel från användar gränssnittet utför du följande steg på båda de virtuella datorerna:
   1. I **Serverhanteraren**väljer du **Hantera**och väljer sedan **Lägg till roller och funktioner**.
   1. I **guiden Lägg till roller och funktioner**väljer du **Nästa** tills du kommer igång med att **välja funktioner**.
   1. I **Välj funktioner**väljer du **kluster för växling vid fel**. Ta med alla nödvändiga funktioner och hanterings verktyg. Välj **Lägg till funktioner**.
   1. Välj **Nästa**och välj sedan **Slutför** för att installera funktionerna.

   Om du vill installera kluster för växling vid fel med hjälp av PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Mer information om nästa steg finns i anvisningarna under steg 3 i [Hyper-konvergerad lösning med hjälp av Lagringsdirigering i Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Verifiera klustret

Verifiera klustret i användar gränssnittet eller med hjälp av PowerShell.

Verifiera klustret med hjälp av användar gränssnittet genom att utföra följande steg på en av de virtuella datorerna:

1. Under **Serverhanteraren**väljer du **verktyg**och väljer sedan **Klusterhanteraren för växling vid fel**.
1. Under **Klusterhanteraren för växling vid fel**väljer du **åtgärd**och väljer sedan **Verifiera konfiguration**.
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster**anger du namnen på de båda virtuella datorerna.
1. Under **test alternativ**väljer **du kör endast test som jag väljer**. Välj **Nästa**.
1. Under **Val av test**väljer du alla tester förutom **lagring**, som du ser här:

   ![Välj kluster verifierings test](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Välj **Nästa**.
1. Under **bekräftelse**väljer du **Nästa**.

Verifierings testen körs i guiden Verifiera en konfiguration.

Om du vill verifiera klustret med hjälp av PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

När du har verifierat klustret skapar du klustret för växling vid fel.

### <a name="create-the-failover-cluster"></a>Skapa redundansklustret

Du behöver följande om du vill skapa ett kluster för växling vid fel:
- Namnen på de virtuella datorer som kommer att bli klusternoder.
- Ett namn för redundansklustret
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används på samma virtuella Azure-nätverk och undernät som klusternoderna.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 via Windows Server 2016

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2008 via Windows Server 2016. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2019. Mer information finns i [redundansklustret: kluster nätverks objekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Skapa ett moln vittne

Moln vittne är en ny typ av klusterkvorum som lagras i en Azure Storage-blob. Detta tar bort behovet av en separat virtuell dator som är värd för en vittnes resurs.

1. [Skapa ett moln vittne för redundansklustret](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en BLOB-behållare.

1. Spara åtkomst nycklarna och behållar-URL: en.

1. Konfigurera ett kvorumlogg för redundanskluster. Se [Konfigurera kvorumdisken i användar gränssnittet](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Lägg till lagring

Diskarna för Lagringsdirigering måste vara tomma. De får inte innehålla partitioner eller andra data. Följ [stegen i den här guiden](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives)om du vill rensa diskarna.

1. [Aktivera lagrings](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct)dirigering.

   Följande PowerShell-skript aktiverar Lagringsdirigering:  

   ```powershell
   Enable-ClusterS2D
   ```

   I **Klusterhanteraren för växling vid fel**kan du nu se lagringspoolen.

1. [Skapa en volym](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Lagringsdirigering skapar automatiskt en lagringspool när du aktiverar den. Nu är du redo att skapa en volym. PowerShell-cmdleten `New-Volume` automatisera processen för att skapa volymer. Den här processen inkluderar formatering, tillägg av volymen i klustret och skapande av en klusterdelad volym (CSV). I det här exemplet skapas en 800-gigabyte (GB) CSV:

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   När det här kommandot har slutförts monteras en 800 GB-volym som en kluster resurs. Volymen är `C:\ClusterStorage\Volume1\`.

   Den här skärm bilden visar en klusterdelad volym med Lagringsdirigering:

   ![Klusterdelad volym](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Steg 3: testa redundans för kluster

I **Klusterhanteraren för växling vid fel**kontrollerar du att du kan flytta lagrings resursen till den andra klusternoden. Om du kan ansluta till redundansklustret med hjälp av **Klusterhanteraren för växling vid fel** och flytta lagringen från en nod till en annan, är du redo att konfigurera FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Steg 4: skapa SQL Server FCI

När du har konfigurerat redundansklustret och alla kluster komponenter, inklusive lagring, kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med hjälp av RDP.

1. I **Klusterhanteraren för växling vid fel**kontrollerar du att alla kärn kluster resurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den virtuella datorn.

1. Leta upp installations mediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full`. Välj **installation**.

1. I **SQL Server installations Center**väljer du **installation**.

1. Välj **ny SQL Server redundanskluster installationen**. Följ anvisningarna i guiden för att installera SQL Server FCI.

   FCI data kataloger måste finnas i klustrad lagring. Med Lagringsdirigering är det inte en delad disk, men en monterings punkt på en volym på varje server. Lagringsdirigering synkroniserar volymen mellan båda noderna. Volymen visas för klustret som en klusterdelad volym. Använd CSV-monterings punkten för data katalogerna.

   ![Data kataloger](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. När du har slutfört anvisningarna i guiden kommer installations programmet att installera en SQL Server FCI på den första noden.

1. När installations programmet har installerat FCI på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **installations Center för SQL Server**. Välj **installation**.

1. Välj **Lägg till nod i ett SQL Server redundanskluster**. Följ anvisningarna i guiden för att installera SQL Server och lägga till servern i FCI.

   >[!NOTE]
   >Om du använde en Azure Marketplace-Galleri avbildning som innehåller SQL Server, inkluderades SQL Server verktyg med avbildningen. Om du inte använde någon av dessa avbildningar installerar du SQL Server verktyg separat. Se [Ladda ned SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Steg 5: Skapa Azure Load Balancer

På Azure Virtual Machines använder kluster en belastningsutjämnare för att lagra en IP-adress som måste finnas på en klusternod i taget. I den här lösningen innehåller belastningsutjämnaren IP-adressen för SQL Server FCI.

Mer information finns i [skapa och konfigurera en Azure Load Balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure Portal

Så här skapar du belastningsutjämnaren:

1. I Azure Portal går du till resurs gruppen som innehåller de virtuella datorerna.

1. Välj **Lägg till**. Sök på Azure Marketplace efter **Load Balancer**. Välj **Load Balancer**.

1. Välj **Skapa**.

1. Konfigurera belastningsutjämnaren med:

   - **Prenumeration**: din Azure-prenumeration.
   - **Resurs grupp**: den resurs grupp som innehåller de virtuella datorerna.
   - **Namn**: ett namn som identifierar belastningsutjämnaren.
   - **Region**: Azure-platsen som innehåller de virtuella datorerna.
   - **Typ**: antingen offentlig eller privat. Det går att få åtkomst till en privat belastningsutjämnare inifrån det virtuella nätverket. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via Internet använder du en offentlig belastningsutjämnare.
   - **SKU**: standard.
   - **Virtuellt nätverk**: samma nätverk som de virtuella datorerna.
   - **IP-adresstilldelning**: statisk. 
   - **Privat IP-adress**: den IP-adress som du tilldelade SQL Server FCI-kluster nätverks resurs.

 Följande skärm bild visar användar gränssnittet för **belastnings utjämning** :

   ![Konfigurera belastningsutjämnaren](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurera backend-poolen för belastnings utjämning

1. Gå tillbaka till den Azure-resurs grupp som innehåller de virtuella datorerna och leta upp den nya belastningsutjämnaren. Du kan behöva uppdatera vyn i resurs gruppen. Markera belastningsutjämnaren.

1. Välj **backend-pooler**och välj sedan **Lägg till**.

1. Koppla backend-poolen till den tillgänglighets uppsättning som innehåller de virtuella datorerna.

1. Under **mål nätverkets IP-konfigurationer**väljer du **virtuell dator** och väljer de virtuella datorer som ska ingå som klusternoder. Se till att ta med alla virtuella datorer som ska vara värdar för FCI.

1. Välj **OK** för att skapa backend-poolen.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurera en hälsoavsökning för lastbalanserare

1. På bladet belastnings utjämning väljer du **hälso avsökningar**.

1. Välj **Lägg till**.

1. På bladet **Lägg till hälso avsökning** <a name="probe"> </a>anger du parametrarna för hälso avsökningen.

   - **Namn**: ett namn för hälso avsökningen.
   - **Protokoll**: TCP.
   - **Port**: Ange till den port som du skapade i brand väggen för hälso avsökningen i [det här steget](#ports). I den här artikeln använder exemplet TCP-port `59999`.
   - **Intervall**: 5 sekunder.
   - **Tröskelvärde**för fel: 2 efterföljande fel.

1. Välj **OK**.

### <a name="set-load-balancing-rules"></a>Ange regler för belastnings utjämning

1. Välj **belastnings Utjämnings regler**på bladet Load Balancer (belastnings utjämning).

1. Välj **Lägg till**.

1. Ange parametrar för belastnings Utjämnings regeln:

   - **Namn**: ett namn för belastnings Utjämnings reglerna.
   - **IP-adress för klient**del: IP-adressen för SQL Server FCI-klustrets nätverks resurs.
   - **Port**: SQL Server FCI TCP-port. Standard instans porten är 1433.
   - **Backend-port**: använder samma port som **port** värde när du aktiverar **flytande IP (direkt Server retur)** .
   - **Backend-pool**: det namn på backend-poolen som du konfigurerade tidigare.
   - **Hälso avsökning**: hälso avsökningen som du konfigurerade tidigare.
   - **Persistence för session**: ingen.
   - **Tids gräns för inaktivitet (minuter)** : 4.
   - **Flytande IP (direkt Server retur)** : aktive rad.

1. Välj **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Steg 6: Konfigurera klustret för avsökningen

Ange port parametern för kluster avsökningen i PowerShell.

Om du vill ange parametern för kluster avsöknings porten uppdaterar du variablerna i följande skript med värden från din miljö. Ta bort vinkelparenteser (`<` och `>`) från skriptet.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

I följande lista beskrivs de värden som du behöver uppdatera:

   - `<Cluster Network Name>`: namnet på Windows Server-redundansklustret för nätverket. I **Klusterhanteraren för växling vid fel** > **nätverk**högerklickar du på nätverket och väljer **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** .

   - `<SQL Server FCI IP Address Resource Name>`: resurs namnet för IP-FCI SQL Server-. I **Klusterhanteraren för växling vid fel** > **roller**går du till SQL Server FCI-rollen, under **Server namn**, högerklickar på IP-adressresursen och väljer **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** . 

   - `<ILBIP>`: IP-adressen för ILB. Adressen är konfigurerad i Azure Portal som klient adress för ILB. Detta är också den SQL Server FCI-IP-adressen. Du hittar den i **Klusterhanteraren för växling vid fel** på samma egenskaps sida där du placerade `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: avsöknings porten som du konfigurerade i belastnings utjämningens hälso avsökning. En oanvänd TCP-port är giltig.

>[!IMPORTANT]
>Nät masken för kluster parametern måste vara TCP IP-broadcast-adressen: `255.255.255.255`.

När du har ställt in kluster avsökningen kan du se alla kluster parametrar i PowerShell. Kör det här skriptet:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Steg 7: testa FCI redundans

Testa redundansväxlingen av FCI för att verifiera kluster funktionen. Utför följande steg:

1. Anslut till en av SQL Server FCI-klusternoderna med hjälp av RDP.

1. Öppna **Klusterhanteraren för växling vid fel**. Välj **roller**. Lägg märke till vilken nod som äger rollen SQL Server FCI.

1. Högerklicka på rollen SQL Server FCI.

1. Välj **Flytta**och välj sedan **bästa möjliga nod**.

**Klusterhanteraren för växling vid fel** visar rollen och dess resurser går offline. Resurserna flyttar sedan och är online på den andra noden.

### <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen loggar du in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och anslut till SQL Server FCI namn.

>[!NOTE]
>Om du behöver kan du [hämta SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Begränsningar

Azure Virtual Machines har stöd för Microsoft koordinator för distribuerad transaktion (MSDTC) på Windows Server 2019 med lagring på klusterdelade volymer (CSV) och en [standard belastnings utjämning](../../../load-balancer/load-balancer-standard-overview.md).

På Azure Virtual Machines stöds inte MSDTC på Windows Server 2016 eller tidigare eftersom:

- Det går inte att konfigurera den klustrade MSDTC-resursen att använda delad lagring. Om du skapar en MSDTC-resurs i Windows Server 2016 visas inga delade lagrings enheter som är tillgängliga för användning, även om lagring är tillgängligt. Det här problemet har åtgärd ATS i Windows Server 2019.
- Den grundläggande belastningsutjämnaren hanterar inte RPC-portar.

## <a name="see-also"></a>Se också

[Konfigurera Lagringsdirigering med fjärr skrivbord (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyper-konvergerad lösning med Lagringsdirigering](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Översikt över Lagringsdirigering](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server stöd för Lagringsdirigering](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
