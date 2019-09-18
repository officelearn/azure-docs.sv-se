---
title: SQL Server FCI – Azure Virtual Machines | Microsoft Docs
description: Den här artikeln förklarar hur du skapar SQL Server-redundanskluster på Azure Virtual Machines.
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
ms.openlocfilehash: 3e954a6c714e525e5bbefe8f62c798cf8ac9a517
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71036386"
---
# <a name="configure-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurera SQL Server-redundanskluster på Azure Virtual Machines

Den här artikeln beskriver hur du skapar en SQL Server-FCI (failover Cluster instance) på virtuella Azure-datorer i Resource Manager-modellen. Den här lösningen använder [Windows Server 2016 Data Center \(Edition\) Lagringsdirigering S2D](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) som ett programvarubaserad virtuellt SAN-nätverk som synkroniserar lagringen (data diskar) mellan noderna (virtuella Azure-datorer) i ett Windows-kluster. S2D är nytt i Windows Server 2016.

Följande diagram visar den kompletta lösningen på virtuella Azure-datorer:

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Föregående diagram visar:

- Två virtuella Azure-datorer i ett Windows-redundanskluster. När en virtuell dator finns i ett kluster för växling vid fel kallas den även för en *klusternod*eller *noder*.
- Varje virtuell dator har två eller flera data diskar.
- S2D synkroniserar data på data disken och presenterar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen presenterar en klusterdelad volym (CSV) för redundansklustret.
- Kluster rollen SQL Server FCI använder CSV-filen för data enheterna.
- En Azure Load Balancer för att lagra IP-adressen för SQL Server FCI.
- En tillgänglighets uppsättning i Azure innehåller alla resurser.

   >[!NOTE]
   >Alla Azure-resurser finns i diagrammet i samma resurs grupp.

Mer information om S2D finns i [Windows Server 2016 Data Center Edition \(Lagringsdirigering\)S2D](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

S2D stöder två typer av arkitekturer – konvergerade och Hyper-konvergerade. Arkitekturen i det här dokumentet är Hyper-konvergerad. En Hyper-konvergerad infrastruktur placerar lagringen på samma servrar som är värdar för det klustrade programmet. I den här arkitekturen finns lagringen på varje SQL Server FCI-nod.

## <a name="licensing-and-pricing"></a>Licensiering och priser

På Azure Virtual Machines du licens SQL Server använda betala per användning (PAYG) eller ta med egna licens (BYOL) VM-avbildningar. Vilken typ av avbildning du väljer påverkar hur du debiteras.

Med PAYG-licensiering debiteras en FCI (failover Cluster instance) av SQL Server på Azure Virtual Machines avgifter för alla noder i FCI, inklusive de passiva noderna. Mer information finns i [SQL Server Enterprise Virtual Machines prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Kunder med Enterprise-avtal med Software Assurance har rätt att använda en kostnads fri passiv FCI-nod för varje aktiv nod. Om du vill dra nytta av den här förmånen i Azure använder du BYOL VM-avbildningar och använder sedan samma licens på både aktiva och passiva noder i FCI. Mer information finns i [Enterprise-avtal](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Om du vill jämföra PAYG-och BYOL-licensieringen för SQL Server på Azure Virtual Machines se [Kom igång med virtuella SQL-datorer](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Fullständig information om licens SQL Server finns i [prissättning](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exempel på Azure-mall

Du kan skapa hela lösningen i Azure från en mall. Ett exempel på en mall finns i GitHub för [Azure snabb start](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Det här exemplet är inte utformat eller testat för någon speciell arbets belastning. Du kan köra mallen för att skapa en SQL Server-FCI med S2D-lagring som är ansluten till din domän. Du kan utvärdera mallen och ändra den så att den passar dina behov.

## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och några saker du behöver innan du fortsätter.

### <a name="what-to-know"></a>Vad du behöver veta
Du bör ha en drifts förståelse för följande tekniker:

- [Windows kluster tekniker](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instanser av kluster för växling vid fel](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

En viktig skillnad är att på ett Azure IaaS-redundanskluster för virtuella datorer rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azures nätverk har fysisk redundans, vilket innebär att det inte behövs fler nätverkskort och undernät för gästkluster på virtuella Azure IaaS-datorer. Även om klustrets verifieringsrapport utfärdar en varning om att noderna endast kan nås i ett enda nätverk, kan varningen ignoreras för redundanskluster på virtuella Azure IaaS-gästdatorer. 

Dessutom bör du ha en allmän förståelse för följande tekniker:

- [Hyper-konvergerad lösning med hjälp av Lagringsdirigering i Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)
- [Resurs grupper i Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> För närvarande stöds inte [SQL Server IaaS agent Extension](virtual-machines-windows-sql-server-agent-extension.md) för SQL Server FCI på Azure. Vi rekommenderar att du avinstallerar tillägget från virtuella datorer som ingår i FCI. Det här tillägget har stöd för funktioner, till exempel automatisk säkerhets kopiering och uppdatering och vissa Portal funktioner för SQL. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har avinstallerats.

### <a name="what-to-have"></a>Vad ska jag ha

Innan du följer anvisningarna i den här artikeln bör du redan ha:

- En Microsoft Azure prenumeration.
- En Windows-domän på Azure Virtual Machines.
- Ett konto med behörighet att skapa objekt på den virtuella Azure-datorn.
- Ett virtuellt Azure-nätverk och undernät med tillräckligt med IP-adressutrymme för följande komponenter:
   - Båda virtuella datorerna.
   - IP-adressen för klustret för växling vid fel.
   - En IP-adress för varje FCI.
- DNS konfigurerat på Azure-nätverket och pekar på domän kontrol Lanterna.

Med dessa krav på plats kan du fortsätta med att skapa ett redundanskluster. Det första steget är att skapa de virtuella datorerna.

## <a name="step-1-create-virtual-machines"></a>Steg 1: Skapa virtuella datorer

1. Logga in på [Azure Portal](https://portal.azure.com) med din prenumeration.

1. [Skapa en tillgänglighets uppsättning i Azure](../tutorial-availability-sets.md).

   Tillgänglighets uppsättningen grupperar virtuella datorer över fel domäner och uppdaterings domäner. Tillgänglighets uppsättningen säkerställer att programmet inte påverkas av enskilda felpunkter, t. ex. nätverks växeln eller enhets enheten för en rack Server.

   Om du inte har skapat resurs gruppen för dina virtuella datorer gör du det när du skapar en Azures tillgänglighets uppsättning. Gör så här om du använder Azure Portal för att skapa tillgänglighets uppsättningen:

   - Öppna Azure Marketplace genom att **+** Klicka på Azure Portal. Sök efter **tillgänglighets uppsättning**.
   - Klicka på **tillgänglighets uppsättning**.
   - Klicka på **Skapa**.
   - Ange följande värden på bladet **skapa tillgänglighets uppsättning** :
      - **Namn på**: Ett namn på tillgänglighets uppsättningen.
      - **Prenumeration**: Din Azure-prenumeration.
      - **Resursgrupp**: Om du vill använda en befintlig grupp klickar du på **Använd befintlig** och väljer gruppen i den nedrullningsbara listan. Annars väljer du **Skapa ny** och skriver ett namn för gruppen.
      - **Plats**: Ange den plats där du planerar att skapa virtuella datorer.
      - **Fel domäner**: Använd standardvärdet (3).
      - **Uppdaterings domäner**: Använd standardvärdet (5).
   - Skapa tillgänglighets uppsättningen genom att klicka på **skapa** .

1. Skapa de virtuella datorerna i tillgänglighets uppsättningen.

   Etablera två SQL Server virtuella datorer i tillgänglighets uppsättningen för Azure. Instruktioner finns i [etablera en SQL Server virtuell dator i Azure Portal](virtual-machines-windows-portal-sql-server-provision.md).

   Placera båda virtuella datorerna:

   - I samma Azure-resurs grupp som din tillgänglighets uppsättning är i.
   - I samma nätverk som domänkontrollanten.
   - På ett undernät med tillräckligt med IP-adressutrymme för både virtuella datorer och alla skyddas som du kan använda i det här klustret.
   - I Azures tillgänglighets uppsättning.   

      >[!IMPORTANT]
      >Du kan inte ange eller ändra tillgänglighets uppsättning när en virtuell dator har skapats.

   Välj en avbildning från Azure Marketplace. Du kan använda en Marketplace-avbildning med som inkluderar Windows Server och SQL Server eller bara Windows Server. Mer information finns i [Översikt över SQL Server på Azure Virtual Machines](virtual-machines-windows-sql-server-iaas-overview.md)

   De officiella SQL Server-avbildningarna i Azure-galleriet innehåller en installerad SQL Server instans, plus SQL Server-installations program och nödvändig nyckel.

   Välj rätt bild enligt hur du vill betala för SQL Server-licensen:

   - **Betala per användnings licensiering**: Kostnaden per sekund för de här avbildningarna omfattar SQL Server-licensen:
      - **SQL Server 2016 Enterprise på Windows Server Data Center 2016**
      - **SQL Server 2016 standard på Windows Server Data Center 2016**
      - **SQL Server 2016-utvecklare på Windows Server Data Center 2016**

   - **Hämta din egen licens (BYOL)**

      - **BYOL SQL Server 2016 Enterprise på Windows Server Data Center 2016**
      - **BYOL SQL Server 2016 standard på Windows Server Data Center 2016**

   >[!IMPORTANT]
   >När du har skapat den virtuella datorn tar du bort den förinstallerade fristående SQL Server-instansen. Du kommer att använda förinstallerade SQL Server media för att skapa SQL Server-FCI när du har konfigurerat redundansklustret och S2D.

   Du kan också använda Azure Marketplace-avbildningar med bara operativ systemet. Välj en **Windows Server 2016 Data Center** -avbildning och installera SQL Server FCI när du har konfigurerat REDUNDANSKLUSTRET och S2D. Den här avbildningen innehåller inte SQL Server installationsmedia. Placera installations mediet på en plats där du kan köra SQL Server-installationen för varje server.

1. När Azure har skapat de virtuella datorerna ansluter du till varje virtuell dator med RDP.

   När du först ansluter till en virtuell dator med RDP frågar datorn om du vill att den här datorn ska kunna identifieras i nätverket. Klicka på **Ja**.

1. Om du använder någon av de SQL Server-baserade avbildningarna av virtuella datorer tar du bort SQL Server-instansen.

   - I **program och funktioner**högerklickar du på **Microsoft SQL Server 2016 (64-bitars)** och klickar på **Avinstallera/ändra**.
   - Klicka på **Ta bort**.
   - Välj standard instansen.
   - Ta bort alla funktioner under **databas motor tjänster**. Ta inte bort **Delade funktioner**. Se följande bild:

      ![Ta bort funktioner](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   - Klicka på **Nästa**och sedan på **ta bort**.

1. <a name="ports"></a>Öppna brand Väggs portarna.

   På varje virtuell dator öppnar du följande portar i Windows-brandväggen.

   | Syfte | TCP-port | Anteckningar
   | ------ | ------ | ------
   | SQL Server | 1433 | Normal port för standard instanser av SQL Server. Om du använde en avbildning från galleriet öppnas porten automatiskt.
   | Hälsoavsökning | 59999 | Alla öppna TCP-portar. I ett senare steg konfigurerar du belastnings utjämningens [hälso avsökning](#probe) och klustret för att använda den här porten.  

1. Lägg till lagring på den virtuella datorn. Detaljerad information finns i [Lägg till lagring](../disks-types.md).

   Båda de virtuella datorerna behöver minst två data diskar.

   Bifoga RAW-diskar – inte NTFS-formaterade diskar.
      >[!NOTE]
      >Om du kopplar NTFS-formaterade diskar kan du bara aktivera S2D utan disk kontroll.  

   Koppla minst två Premium-SSD till varje virtuell dator. Vi rekommenderar minst P30-diskar (1 TB).

   Ange cachelagring av värden till **skrivskyddad**.

   Vilken lagrings kapacitet du använder i produktions miljöer beror på din arbets belastning. Värdena som beskrivs i den här artikeln är för demonstration och testning.

1. [Lägg till de virtuella datorerna i din befintliga domän](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

När de virtuella datorerna har skapats och kon figurer ATS kan du konfigurera redundansklustret.

## <a name="step-2-configure-the-windows-failover-cluster-with-s2d"></a>Steg 2: Konfigurera Windows-redundansklustret med S2D

Nästa steg är att konfigurera redundansklustret med S2D. I det här steget ska du göra följande under steg:

1. Lägg till funktion för redundanskluster i Windows
1. Verifiera klustret
1. Skapa redundansklustret
1. Skapa moln vittnet
1. Lägg till lagringsutrymme

### <a name="add-windows-failover-clustering-feature"></a>Lägg till funktion för redundanskluster i Windows

1. Börja genom att ansluta till den första virtuella datorn med RDP med ett domän konto som är medlem i lokala administratörer och som har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägg till funktionen kluster för växling vid fel på varje virtuell dator](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Om du vill installera funktionen kluster för växling vid fel från användar gränssnittet utför du följande steg på båda de virtuella datorerna.
   - I **Serverhanteraren**klickar du på **Hantera**och sedan på **Lägg till roller och funktioner**.
   - I **guiden Lägg till roller och funktioner**klickar du på **Nästa** tills du kommer igång med att **välja funktioner**.
   - I **Välj funktioner**klickar du på **redundanskluster**. Ta med alla nödvändiga funktioner och hanterings verktyg. Klicka på **Lägg till funktioner**.
   - Klicka på **Nästa** och sedan på **Slutför** för att installera funktionerna.

   Om du vill installera funktionen kluster för växling vid fel med PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna.

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

För referens följer du anvisningarna under steg 3 i [Hyper-konvergerad lösning med hjälp av Lagringsdirigering i Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Verifiera klustret

Den här guiden refererar till instruktioner under [Verifiera kluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Verifiera klustret i användar gränssnittet eller med PowerShell.

Verifiera klustret med användar gränssnittet genom att utföra följande steg från en av de virtuella datorerna.

1. Klicka på **verktyg**i **Serverhanteraren**och klicka sedan på **Klusterhanteraren för växling vid fel**.
1. I **Klusterhanteraren för växling vid fel**klickar du på **åtgärd**och sedan på **Verifiera konfiguration...** .
1. Klicka på **Nästa**.
1. På **Välj servrar eller ett kluster**skriver du namnet på båda de virtuella datorerna.
1. På **test alternativ**väljer **du kör endast test som jag väljer**. Klicka på **Nästa**.
1. Ta med alla tester utom **lagring**vid **Val av test**. Se följande bild:

   ![Validera tester](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Klicka på **Nästa**.
1. Klicka på **Nästa**vid **bekräftelse**.

Verifierings testen körs i **guiden Verifiera en konfiguration** .

Verifiera klustret med PowerShell genom att köra följande skript från en administratörs PowerShell-session på en av de virtuella datorerna.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

När du har verifierat klustret skapar du klustret för växling vid fel.

### <a name="create-the-failover-cluster"></a>Skapa redundansklustret

Den här guiden refererar till att [skapa redundansklustret](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-32-create-a-cluster).

Du behöver följande om du vill skapa ett kluster för växling vid fel:
- Namnen på de virtuella datorer som blir klusternoderna.
- Ett namn för redundansklustret
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används på samma virtuella Azure-nätverk och undernät som klusternoderna.

#### <a name="windows-server-2008-2016"></a>Windows Server 2008-2016

Följande PowerShell skapar ett redundanskluster för **Windows Server 2008-2016**. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Följande PowerShell skapar ett redundanskluster för Windows Server 2019.  Mer information finns i bloggens [kluster för växling vid fel: Kluster nätverks objekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Skapa ett moln vittne

Moln vittne är en ny typ av klusterkvorum som lagras i en Azure Storage Blob. Detta tar bort behovet av en separat virtuell dator som är värd för en vittnes resurs.

1. [Skapa ett moln vittne för redundansklustret](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en BLOB-behållare.

1. Spara åtkomst nycklarna och behållar-URL: en.

1. Konfigurera ett kvorumlogg för redundanskluster. Se [Konfigurera kvorumdisken i användar gränssnittet i användar gränssnittet](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) .

### <a name="add-storage"></a>Lägg till lagringsutrymme

Diskarna för S2D måste vara tomma och utan partitioner eller andra data. Om du vill rensa diskar följer [du stegen i den här hand boken](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-34-clean-disks).

1. [Aktivera lagrings dirigering \(med\)S2D](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Följande PowerShell aktiverar lagrings dirigering.  

   ```powershell
   Enable-ClusterS2D
   ```

   I **Klusterhanteraren för växling vid fel**kan du nu se lagringspoolen.

1. [Skapa en volym](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   En av funktionerna i S2D är att den automatiskt skapar en lagringspool när du aktiverar den. Nu är du redo att skapa en volym. PowerShell-kommandot `New-Volume` automatiserar skapande processen för volymer, inklusive formatering, tillägg till klustret och skapande av en klusterdelad volym (CSV). I följande exempel skapas en 800 gigabyte (GB) CSV.

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   När det här kommandot har slutförts monteras en 800 GB-volym som en kluster resurs. Volymen är på `C:\ClusterStorage\Volume1\`.

   Följande diagram visar en klusterdelad volym med S2D:

   ![ClusterSharedVolume](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Steg 3: Testa redundans för kluster

I Klusterhanteraren för växling vid fel kontrollerar du att du kan flytta lagrings resursen till den andra klusternoden. Om du kan ansluta till redundansklustret med **Klusterhanteraren för växling vid fel** och flytta lagringen från en nod till en annan, är du redo att konfigurera FCI.

## <a name="step-4-create-sql-server-fci"></a>Steg 4: Skapa SQL Server FCI

När du har konfigurerat redundansklustret och alla kluster komponenter, inklusive lagring, kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med RDP.

1. I **Klusterhanteraren för växling vid fel**kontrollerar du att alla kluster kärn resurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den virtuella datorn.

1. Leta upp installations mediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full`. Klicka på **Konfigurera**.

1. Klicka på **installation**i **SQL Server installations Center**.

1. Klicka på **ny SQL Server redundanskluster installationen**. Följ anvisningarna i guiden för att installera SQL Server FCI.

   FCI data kataloger måste finnas i klustrad lagring. Med S2D är det ingen delad disk, men en monterings punkt på en volym på varje server. S2D synkroniserar volymen mellan båda noderna. Volymen visas för klustret som en klusterdelad volym. Använd CSV-monterings punkten för data katalogerna.

   ![DataDirectories](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. När du har slutfört guiden kommer installations programmet att installera en SQL Server FCI på den första noden.

1. När installations programmet har installerat FCI på den första noden ansluter du till den andra noden med RDP.

1. Öppna **installations Center för SQL Server**. Klicka på **installation**.

1. Klicka på **Lägg till nod i ett SQL Server redundanskluster**. Följ anvisningarna i guiden för att installera SQL Server och lägga till den här servern i FCI.

   >[!NOTE]
   >Om du använde en Azure Marketplace-Galleri avbildning med SQL Server, inkluderades SQL Server verktyg tillsammans med avbildningen. Om du inte använde avbildningen installerar du SQL Server verktyg separat. Se [Ladda ned SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-azure-load-balancer"></a>Steg 5: Skapa en Azure Load Balancer

På Azure Virtual Machines använder kluster en belastningsutjämnare för att lagra en IP-adress som måste finnas på en klusternod i taget. I den här lösningen innehåller belastningsutjämnaren IP-adressen för SQL Server FCI.

[Skapa och konfigurera en Azure Load Balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure Portal

Så här skapar du belastningsutjämnaren:

1. I Azure Portal går du till resurs gruppen med de virtuella datorerna.

1. Klicka på **+ Lägg till**. Sök på Marketplace efter **Load Balancer**. Klicka på **Load Balancer**.

1. Klicka på **Skapa**.

1. Konfigurera belastningsutjämnaren med:

   - **Namn på**: Ett namn som identifierar belastningsutjämnaren.
   - **Typ**: Belastningsutjämnaren kan vara antingen offentlig eller privat. Det går att få åtkomst till en privat belastningsutjämnare i samma VNET. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via Internet använder du en offentlig belastningsutjämnare.
   - **Virtual Network**: Samma nätverk som de virtuella datorerna.
   - **Undernät**: Samma undernät som de virtuella datorerna.
   - **Privat IP-adress**: Samma IP-adress som du tilldelade SQL Server FCI-kluster nätverks resurs.
   - **prenumeration**: Din Azure-prenumeration.
   - **Resursgrupp**: Använd samma resurs grupp som dina virtuella datorer.
   - **Plats**: Använd samma Azure-plats som dina virtuella datorer.
   Se följande bild:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurera backend-poolen för belastnings utjämning

1. Gå tillbaka till Azure-resurs gruppen med de virtuella datorerna och leta upp den nya belastningsutjämnaren. Du kan behöva uppdatera vyn i resurs gruppen. Klicka på belastningsutjämnaren.

1. Klicka på **backend-pooler** och klicka på **+ Lägg** till för att lägga till en backend-pool.

1. Koppla backend-poolen till den tillgänglighets uppsättning som innehåller de virtuella datorerna.

1. Under **mål nätverkets IP-konfigurationer**kontrollerar du den **virtuella datorn** och väljer de virtuella datorer som ska ingå i klusternoder. Se till att ta med alla virtuella datorer som ska vara värdar för FCI. 

1. Klicka på **OK** för att skapa backend-poolen.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurera en belastnings utjämning hälso avsökning

1. Klicka på **hälso avsökningar**på bladet belastnings utjämning.

1. Klicka på **+ Lägg till**.

1. På bladet **Lägg till hälso avsökning** <a name="probe"> </a>anger du parametrar för hälso avsökning:

   - **Namn på**: Ett namn på hälso avsökningen.
   - **Protokoll**: TCP.
   - **Port**: Ange till den port som du skapade i brand väggen för hälso avsökningen i [det här steget](#ports). I den här artikeln använder exemplet TCP-port `59999`.
   - **Intervall**: 5 sekunder.
   - **Tröskelvärde för Ej felfri**: 2 efterföljande försök.

1. Klicka på OK.

### <a name="set-load-balancing-rules"></a>Ange regler för belastnings utjämning

1. Klicka på **belastnings Utjämnings regler**på bladet Load Balancer (belastnings utjämning).

1. Klicka på **+ Lägg till**.

1. Ange parametrar för belastnings Utjämnings regler:

   - **Namn på**: Ett namn på belastnings Utjämnings reglerna.
   - **IP-adress för klient**del: Använd IP-adressen för SQL Server FCI-kluster nätverks resurs.
   - **Port**: Anges för TCP-porten för SQL Server FCI. Standard instans porten är 1433.
   - **Serverdelsport**: Värdet använder samma port som **port** värdet när du aktiverar **flytande IP (direkt Server retur)** .
   - **Serverdelspool**: Använd det namn på backend-poolen som du konfigurerade tidigare.
   - **Hälsoavsökning**: Använd den hälso avsökning som du konfigurerade tidigare.
   - **Persistence för session**: Ingen.
   - **Tids gräns för inaktivitet (minuter)** : 4.
   - **Flytande IP (direkt Server retur)** : Aktiverad

1. Klicka på **OK**.

## <a name="step-6-configure-cluster-for-probe"></a>Steg 6: Konfigurera kluster för avsökning

Ange port parametern för kluster avsökningen i PowerShell.

Om du vill ange parametern för kluster avsöknings porten uppdaterar du variabler i följande skript med värden från din miljö. Ta bort vinkelparenteser `<>` från skriptet. 

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

Ange värdena för din miljö i föregående skript. I följande lista beskrivs värdena:

   - `<Cluster Network Name>`: Kluster namn för Windows Server-redundanskluster för nätverket. I **Klusterhanteraren för växling vid fel** > **nätverk**högerklickar du på nätverket och klickar på **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** . 

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI IP-adressens resurs namn. I **Klusterhanteraren för växling vid fel** > **roller**, under rollen SQL Server FCI, högerklickar du på IP-adressresursen under **Server namn**och klickar på **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** . 

   - `<ILBIP>`: ILB IP-adress. Adressen är konfigurerad i Azure Portal som klient adress för ILB. Detta är också den SQL Server FCI-IP-adressen. Du hittar den i **Klusterhanteraren för växling vid fel** på samma egenskaps sida där du placerade `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: Är avsöknings porten som du konfigurerade i belastnings utjämningens hälso avsökning. En oanvänd TCP-port är giltig. 

>[!IMPORTANT]
>Nät masken för kluster parametern måste vara TCP IP-broadcast-adressen: `255.255.255.255`.

När du har ställt in kluster avsökningen kan du se alla kluster parametrar i PowerShell. Kör följande skript:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Steg 7: Testa FCI redundans

Testa redundansväxlingen av FCI för att verifiera kluster funktionen. Gör så här:

1. Anslut till en av de SQL Server FCI-klusternoderna med RDP.

1. Öppna **Klusterhanteraren för växling vid fel**. Klicka på **roller**. Lägg märke till vilken nod som äger rollen SQL Server FCI.

1. Högerklicka på rollen SQL Server FCI.

1. Klicka på **Flytta** och klicka på **bästa möjliga nod**.

**Klusterhanteraren för växling vid fel** visar rollen och dess resurser går offline. Resurserna flyttar sedan och är online på den andra noden.

### <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen loggar du in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och anslut till SQL Server FCI namn.

>[!NOTE]
>Om det behövs kan du [Ladda ned SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Begränsningar

Azure Virtual Machines stöder Microsoft koordinator för distribuerad transaktion (MSDTC) på Windows Server 2019 med lagring på klusterdelade volymer (CSV) och en [standard belastningsutjämnare](../../../load-balancer/load-balancer-standard-overview.md).

På Azure Virtual Machines stöds inte MSDTC på Windows Server 2016 och tidigare eftersom:

- Det går inte att konfigurera den klustrade MSDTC-resursen att använda delad lagring. Med Windows Server 2016 om du skapar en MSDTC-resurs kommer den inte att visa någon delad lagring som är tillgänglig för användning, även om lagrings platsen finns där. Det här problemet har åtgärd ATS i Windows Server 2019.
- Den grundläggande belastningsutjämnaren hanterar inte RPC-portar.

## <a name="see-also"></a>Se även

[Konfigurera S2D med fjärr skrivbord (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

En [Hyper-konvergerad lösning med lagrings dirigering](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct).

[Översikt över lagrings utrymmes dirigering](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server stöd för S2D](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
