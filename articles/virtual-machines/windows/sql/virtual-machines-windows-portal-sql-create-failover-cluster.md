---
title: SQL Server FCI - Virtuella Azure-datorer | Microsoft-dokument
description: I den här artikeln beskrivs hur du skapar en SQL Server-redundansklusterininstans på virtuella Azure-datorer.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249807"
---
# <a name="configure-a-sql-server-failover-cluster-instance-on-azure-virtual-machines"></a>Konfigurera en SQL Server-redundansklusterininstans på virtuella Azure-datorer

I den här artikeln beskrivs hur du skapar en SQL Server-redundansklusterinstans (FCI) på virtuella Azure-datorer i Azure Resource Manager-modellen. Den här lösningen använder [Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview) som ett programvarubaserat virtuellt SAN som synkroniserar lagring (datadiskar) mellan noderna (Virtuella Azure-datorer) i ett Windows-kluster. Storage Spaces Direct var nytt i Windows Server 2016.

Följande diagram visar den kompletta lösningen på virtuella Azure-datorer:

![Den kompletta lösningen](./media/virtual-machines-windows-portal-sql-create-failover-cluster/00-sql-fci-s2d-complete-solution.png)

Det här diagrammet visar:

- Två virtuella Azure-datorer i ett Windows Server Redundanskluster. När en virtuell dator finns i ett redundanskluster kallas den också för en *klusternod* eller *nod*.
- Varje virtuell dator har två eller flera datadiskar.
- Storage Spaces Direct synkroniserar data på datadiskarna och presenterar den synkroniserade lagringen som en lagringspool.
- Lagringspoolen presenterar en csv -klustervolym (Cluster Shared Volume) för redundansklustret.
- Sql Server FCI-klusterrollen använder CSV för dataenheterna.
- En Azure-belastningsutjämnare som innehåller IP-adressen för SQL Server FCI.
- En Azure-tillgänglighetsuppsättning innehåller alla resurser.

>[!NOTE]
>Alla Azure-resurser i diagrammet finns i samma resursgrupp.

Mer information om Storage Spaces Direct finns i [Windows Server 2016 Datacenter edition Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview).

Storage Spaces Direct stöder två typer av arkitekturer: konvergerade och hyperkonvergerade. Arkitekturen i det här dokumentet är hyperkonvergerad. En hyperkonvergerad infrastruktur placerar lagringen på samma servrar som är värd för det klustrade programmet. I den här arkitekturen finns lagringen på varje SQL Server FCI-nod.

## <a name="licensing-and-pricing"></a>Licensiering och prissättning

På virtuella Azure-datorer kan du licensiera SQL Server med PAY-as-you-go (PAYG) eller byol-avbildningar (bring-your-own-license). Vilken typ av bild du väljer påverkar hur du debiteras.

Med användningsbaserad licensiering medför en REDUNDAnsklusterinstan (FCI) av SQL Server på virtuella Azure-datorer avgifter för alla noder i FCI, inklusive de passiva noderna. Mer information finns i [SQL Server Enterprise Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Om du har Enterprise Agreement med Software Assurance kan du använda en gratis passiv FCI-nod för varje aktiv nod. Om du vill dra nytta av den här förmånen i Azure använder du BYOL VM-avbildningar och använder samma licens på både aktiva och passiva noder i FCI. Mer information finns i [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Om du vill jämföra användningsbaserad användning och BYOL-licensiering för SQL Server på virtuella Azure-datorer läser [du Komma igång med virtuella SQL-datorer](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Fullständig information om licensiering av SQL Server finns i [Prissättning](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="example-azure-template"></a>Exempel på Azure-mall

Du kan skapa hela den här lösningen i Azure från en mall. Ett exempel på en mall är tillgängligt i GitHub [Azure Quickstart Templates](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad). Det här exemplet är inte utformat eller testat för någon specifik arbetsbelastning. Du kan köra mallen för att skapa en SQL Server FCI med Storage Spaces Direct-lagring ansluten till domänen. Du kan utvärdera mallen och ändra den för dina syften.

## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och har på plats innan du börjar.

### <a name="what-to-know"></a>Att veta
Du bör ha en operativ förståelse för dessa tekniker:

- [Windows-klustertekniker](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)
- [Klusterinstanser för SQL Server-redundans](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

En sak att vara medveten om är att på en Azure IaaS VM gäst redundans kluster, rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans, vilket gör ytterligare nätverkskort och undernät onödiga på ett Azure IaaS VM-gästkluster. Klusterverifieringsrapporten varnar dig om att noderna endast kan nås i ett enda nätverk. Du kan ignorera den här varningen på Azure IaaS VM-gästväxlingskluster.

Du bör också ha en allmän förståelse för dessa tekniker:

- [Hyperkonvergerade lösningar som använder Storage Spaces Direct i Windows Server 2016](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Azure-resursgrupper](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> För närvarande stöds SQL Server redundansklusterinstanser på virtuella Azure-datorer med [lightweight management-läget](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) i [SQL Server IaaS-agenttillägget](virtual-machines-windows-sql-server-agent-extension.md). Om du vill ändra från fullständigt tilläggsläge till lättvikt tar du bort **SQL Virtual Machine-resursen** för motsvarande virtuella datorer och registrerar dem sedan med SQL VM-resursprovidern i lättviktsläge. När du tar bort **RESURSEN FÖR virtuell SQL-dator** med Azure-portalen **avmarkerar du kryssrutan bredvid rätt virtuell dator**. Det fullständiga tillägget stöder funktioner som automatisk säkerhetskopiering, korrigering och avancerad portalhantering. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har installerats om i lightweight management-läge.

### <a name="what-to-have"></a>Vad man ska ha

Innan du slutför stegen i den här artikeln bör du redan ha:

- En Microsoft Azure-prenumeration.
- En Windows-domän på virtuella Azure-datorer.
- Ett konto som har behörighet att skapa objekt på både virtuella Azure-datorer och i Active Directory.
- Ett virtuellt Azure-nätverk och -undernät med tillräckligt med IP-adressutrymme för dessa komponenter:
   - Båda virtuella datorer.
   - IP-adressen för redundansklustret.
   - En IP-adress för varje FCI.
- DNS konfigurerad i Azure-nätverket och pekar på domänkontrollanterna.

Med dessa förutsättningar på plats kan du börja bygga ditt redundanskluster. Det första steget är att skapa de virtuella datorerna.

## <a name="step-1-create-the-virtual-machines"></a>Steg 1: Skapa de virtuella datorerna

1. Logga in på [Azure-portalen](https://portal.azure.com) med din prenumeration.

1. [Skapa en Azure-tillgänglighetsuppsättning](../tutorial-availability-sets.md).

   Tillgänglighetsuppsättningen grupperar virtuella datorer över feldomäner och uppdaterar domäner. Det säkerställer att ditt program inte påverkas av enskilda felpunkter, som nätverksväxeln eller kraftenheten på ett rack med servrar.

   Om du inte har skapat resursgruppen för dina virtuella datorer gör du det när du skapar en Azure-tillgänglighetsuppsättning. Om du använder Azure-portalen för att skapa tillgänglighetsuppsättningen gör du så här:

   1. I Azure-portalen väljer du **Skapa en resurs** för att öppna Azure Marketplace. Sök efter **tillgänglighetsuppsättning**.
   1. Välj **Tillgänglighetsuppsättning**.
   1. Välj **Skapa**.
   1. Under **Skapa tillgänglighetsuppsättning**anger du följande värden:
      - **Namn**: Ett namn för tillgänglighetsuppsättningen.
      - **Prenumeration**: Din Azure-prenumeration.
      - **Resursgrupp**: Om du vill använda en befintlig grupp klickar du på **Markera befintlig** och väljer sedan gruppen i listan. Annars väljer du **Skapa nytt** och anger ett namn för gruppen.
      - **Plats**: Ange den plats där du planerar att skapa dina virtuella datorer.
      - **Feldomäner:** Använd standard **(3**).
      - **Uppdatera domäner**: Använd standard **(5**).
   1. Välj **Skapa** om du vill skapa tillgänglighetsuppsättningen.

1. Skapa de virtuella datorerna i tillgänglighetsuppsättningen.

   Etablera två virtuella SQL Server-datorer i Azure-tillgänglighetsuppsättningen. Instruktioner finns [i Etablera en virtuell SQL Server-dator i Azure-portalen](virtual-machines-windows-portal-sql-server-provision.md).

   Placera båda virtuella datorer:

   - I samma Azure-resursgrupp som din tillgänglighetsuppsättning.
   - I samma nätverk som domänkontrollanten.
   - På ett undernät som har tillräckligt med IP-adressutrymme för både virtuella datorer och alla FC:er som du så småningom kan använda i klustret.
   - I azure-tillgänglighetsuppsättningen.

      >[!IMPORTANT]
      >Du kan inte ange eller ändra tillgänglighetsuppsättningen när du har skapat en virtuell dator.

   Välj en avbildning från Azure Marketplace. Du kan använda en Azure Marketplace-avbildning som innehåller Windows Server och SQL Server, eller använda en som bara innehåller Windows Server. Mer information finns i [Översikt över SQL Server på virtuella Azure-datorer](virtual-machines-windows-sql-server-iaas-overview.md).

   De officiella SQL Server-avbildningarna i Azure Gallery innehåller en installerad SQL Server-instans, SQL Server-installationsprogrammet och den nödvändiga nyckeln.

   Välj rätt avbildning baserat på hur du vill betala för SQL Server-licensen:

   - **Licensiering av betalning per användning**. Kostnaden per sekund för dessa avbildningar inkluderar SQL Server-licensieringen:
      - **SQL Server 2016 Enterprise på Windows Server 2016 Datacenter**
      - **SQL Server 2016 Standard på Windows Server 2016 Datacenter**
      - **SQL Server 2016-utvecklare på Windows Server 2016 Datacenter**

   - **Bring-your-own-licens (BYOL)**

      - **-Jag är inte så bra som jag. SQL Server 2016 Enterprise på Windows Server 2016 Datacenter**
      - **-Jag är inte så bra som jag. SQL Server 2016 Standard på Windows Server 2016 Datacenter**

   >[!IMPORTANT]
   >När du har skapat den virtuella datorn tar du bort den förinstallerade fristående SQL Server-instansen. Du ska använda det förinstallerade SQL Server-mediet för att skapa SQL Server FCI när du har konfigurerat redundansklustret och Storage Spaces Direct.

   Du kan också använda Azure Marketplace-avbildningar som bara innehåller operativsystemet. Välj en **datacenteravbildning för Windows Server 2016** och installera SQL Server FCI när du har konfigurerat redundansklustret och Storage Spaces Direct. Den här avbildningen innehåller inte SQL Server-installationsmedia. Placera SQL Server-installationsmediet på en plats där du kan köra det för varje server.

1. När Azure har skapat dina virtuella datorer ansluter du till var och en med hjälp av RDP.

   När du först ansluter till en virtuell dator med RDP frågar en fråga om du vill att datorn ska kunna identifieras i nätverket. Välj **Ja**.

1. Om du använder någon av de SQL Server-baserade avbildningarna för virtuella datorer tar du bort SQL Server-instansen.

   1. Högerklicka på Microsoft SQL **Server 2016 (64-bitars) i** **Program och funktioner**och välj **Avinstallera/ändra**.
   1. Välj **Ta bort**.
   1. Markera standardinstansen.
   1. Ta bort alla funktioner under **Database Engine Services**. Ta inte bort **delade funktioner**. Du ser något i stil med följande skärmdump:

      ![Välj funktioner](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Välj **Nästa**och välj sedan **Ta bort**.

1. <a name="ports"></a>Öppna brandväggsportarna.

   Öppna dessa portar i Windows-brandväggen på varje virtuell dator:

   | Syfte | TCP-port | Anteckningar
   | ------ | ------ | ------
   | SQL Server | 1433 | Normal port för standardinstanser av SQL Server. Om du har använt en bild från galleriet öppnas den här porten automatiskt.
   | Hälsoavsökning | 59999 | Alla öppna TCP-portar. I ett senare steg konfigurerar du [hälsoavsökningen](#probe) för belastningsutjämnaren och klustret så att den här porten används.  

1. Lägg till lagringsutrymme på den virtuella datorn. Detaljerad information finns i [Lägga till lagring](../disks-types.md).

   Båda virtuella datorer behöver minst två datadiskar.

   Bifoga rådiskar, inte NTFS-formaterade diskar.
      >[!NOTE]
      >Om du ansluter NTFS-formaterade diskar kan du endast aktivera Storage Spaces Direct utan en diskberättigande kontroll.  

   Koppla minst två premium-SSD:er till varje virtuell dator. Vi rekommenderar minst P30 -diskar (1 TB).

   Ange värdcachelagring till **Skrivskyddad**.

   Vilken lagringskapacitet du använder i produktionsmiljöer beror på din arbetsbelastning. De värden som beskrivs i den här artikeln är för demonstration och testning.

1. [Lägg till de virtuella datorerna i din befintliga domän](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

När du har skapat och konfigurerat de virtuella datorerna kan du ställa in redundansklustret.

## <a name="step-2-configure-the-windows-server-failover-cluster-with-storage-spaces-direct"></a>Steg 2: Konfigurera Redundansklustret för Windows Server med Direct för lagringsutrymmen

Nästa steg är att konfigurera redundansklustret med Storage Spaces Direct. I det här steget ska du slutföra dessa understeg:

1. Lägg till funktionen Kluster för Windows Server-redundans.
1. Verifiera klustret.
1. Skapa redundansklustret.
1. Skapa molnvittnet.
1. Lägg till lagringsutrymme.

### <a name="add-windows-server-failover-clustering"></a>Lägga till Windows Server Redundanskluster

1. Anslut till den första virtuella datorn med RDP med hjälp av ett domänkonto som är medlem i de lokala administratörerna och som har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägg till redundanskluster till varje virtuell dator](virtual-machines-windows-portal-sql-availability-group-prereq.md#add-failover-clustering-features-to-both-sql-server-vms).

   Så här installerar du Redunda-kluster från användargränssnittet:
   1. Välj **Hantera**i **Serverhanteraren**och välj sedan **Lägg till roller och funktioner**.
   1. I **guiden Lägg till roller och funktioner**väljer du **Nästa** tills du kommer till **Välj funktioner**.
   1. I **Välj funktioner**väljer du **Redundanskluster.** Inkludera alla nödvändiga funktioner och hanteringsverktyg. Välj **Lägg till funktioner**.
   1. Välj **Nästa**och välj sedan **Slutför** för att installera funktionerna.

   Om du vill installera Redunda-kluster med PowerShell kör du följande skript från en powershell-administratörssession på en av de virtuella datorerna:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Mer information om nästa steg finns i instruktionerna under Steg 3 i [Hyper-konvergerad lösning med Storage Spaces Direct i Windows Server 2016](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-3-configure-storage-spaces-direct).

### <a name="validate-the-cluster"></a>Verifiera klustret

Validera klustret i användargränssnittet eller med PowerShell.

Så här validerar du klustret med hjälp av användargränssnittet:

1. Under **Serverhanteraren**väljer du **Verktyg**och väljer sedan **Redundansklusterhanteraren**.
1. Under **Redundansklusterhanterare**väljer du **Åtgärd**och väljer sedan **Validera konfiguration**.
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster**anger du namnen på båda virtuella datorerna.
1. Under **Testalternativ**väljer du **Kör endast tester som jag väljer**. Välj **Nästa**.
1. Under **Testval**väljer du alla tester utom **Lagring**, som visas här:

   ![Välj klustervalideringstester](./media/virtual-machines-windows-portal-sql-create-failover-cluster/10-validate-cluster-test.png)

1. Välj **Nästa**.
1. Under **Bekräftelse**väljer du **Nästa**.

Guiden Validera en konfiguration kör valideringstesterna.

Om du vill validera klustret med PowerShell kör du följande skript från en powershell-administratörssession på en av de virtuella datorerna:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```

När du har validerat klustret skapar du redundansklustret.

### <a name="create-the-failover-cluster"></a>Skapa redundansklustret

Om du vill skapa redundansklustret behöver du:
- Namnen på de virtuella datorer som blir klusternoder.
- Ett namn på redundansklustret
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används i samma virtuella Azure-nätverk och undernät som klusternoder.

#### <a name="windows-server-2008-through-windows-server-2016"></a>Windows Server 2008 via Windows Server 2016

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2008 via Windows Server 2016. Uppdatera skriptet med namnen på noderna (namn på den virtuella datorn) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2019. Mer information finns i [Redundanskluster: Klusternätverksobjekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Uppdatera skriptet med namnen på noderna (namn på den virtuella datorn) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Skapa ett molnvittne

Cloud Witness är en ny typ av klusterkvorumvittne som lagras i en Azure storage-blob. Detta tar bort behovet av en separat virtuell dator som är värd för en vittnesresurs.

1. [Skapa ett molnvittne för redundansklustret](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en blob-behållare.

1. Spara åtkomstnycklarna och behållar-URL:en.

1. Konfigurera kvorumvittnet för redundanskluster. Se [Konfigurera kvorumvittnet i användargränssnittet](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

### <a name="add-storage"></a>Lägg till lagringsutrymme

Diskarna för Storage Spaces Direct måste vara tomma. De kan inte innehålla partitioner eller andra data. Om du vill rensa diskarna följer du [stegen i den här guiden](https://docs.microsoft.com/windows-server/storage/storage-spaces/deploy-storage-spaces-direct?redirectedfrom=MSDN#step-31-clean-drives).

1. [Aktivera Store Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   Följande PowerShell-skript aktiverar Storage Spaces Direct:  

   ```powershell
   Enable-ClusterS2D
   ```

   I **Redundansklusterhanteraren**kan du nu se lagringspoolen.

1. [Skapa en volym](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-36-create-volumes).

   Storage Spaces Direct skapar automatiskt en lagringspool när du aktiverar den. Du är nu redo att skapa en volym. PowerShell-cmdlet `New-Volume` automatiserar processen för att skapa volymen. Den här processen omfattar formatering, tillägg av volymen i klustret och skapandet av en klusterdelad volym (CSV). I det här exemplet skapas en CSV på 800 GB(GIGABYTE):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   När det här kommandot har slutförts monteras en volym på 800 GB som en klusterresurs. Volymen är `C:\ClusterStorage\Volume1\`på .

   Den här skärmbilden visar en klusterdelad volym med Storage Spaces Direct:

   ![Klusterdelad volym](./media/virtual-machines-windows-portal-sql-create-failover-cluster/15-cluster-shared-volume.png)

## <a name="step-3-test-failover-cluster-failover"></a>Steg 3: Redundanskluster för testkluster

Kontrollera att du kan flytta lagringsresursen till den andra klusternoden i **Redundansklusterhanteraren.** Om du kan ansluta till redundansklustret med **redundansklusterhanteraren** och flytta lagringen från en nod till den andra, är du redo att konfigurera FCI.

## <a name="step-4-create-the-sql-server-fci"></a>Steg 4: Skapa SQL Server FCI

När du har konfigurerat redundansklustret och alla klusterkomponenter, inklusive lagring, kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med hjälp av RDP.

1. I **Redundansklusterhanteraren**kontrollerar du att alla kärnklusterresurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den virtuella datorn.

1. Leta reda på installationsmediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full`. Välj **Installation .**

1. Välj **Installation**i **SQL Server Installation Center**.

1. Välj **Ny sql server-redundansklusterinstallation**. Installera SQL Server FCI genom att följa instruktionerna i guiden.

   FCI-datakatalogerna måste finnas på klusterlagring. Med Storage Spaces Direct är det inte en delad disk, utan en fästpunkt till en volym på varje server. Storage Spaces Direct synkroniserar volymen mellan båda noderna. Volymen presenteras för klustret som en klusterdelad volym. Använd CSV-monteringspunkten för datakatalogerna.

   ![Datakataloger](./media/virtual-machines-windows-portal-sql-create-failover-cluster/20-data-dicrectories.png)

1. När du har slutfört instruktionerna i guiden installeras en SQL Server FCI på den första noden.

1. När installationen av FCI:en har installerats på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **SQL Server-installationscentret**. Välj **Installation**.

1. Välj **Lägg till nod i ett SQL Server-redundanskluster**. Installera SQL Server och lägga till servern i FCI.Follow the instructions in the wizard to install SQL Server and add the server to the FCI.

   >[!NOTE]
   >Om du använde en Avbildning i Azure Marketplace-galleriet som innehåller SQL Server inkluderades SQL Server-verktygen i avbildningen. Om du inte använde någon av dessa avbildningar installerar du SQL Server-verktygen separat. Se [Hämta SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-5-create-the-azure-load-balancer"></a>Steg 5: Skapa Azure-belastningsutjämnaren

På virtuella Azure-datorer använder kluster en belastningsutjämnare för att hålla en IP-adress som måste finnas på en klusternod i taget. I den här lösningen innehåller belastningsutjämnaren IP-adressen för SQL Server FCI.

Mer information finns i [Skapa och konfigurera en Azure-belastningsutjämnare](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure-portalen

Så här skapar du belastningsutjämnaren:

1. Gå till resursgruppen som innehåller de virtuella datorerna i Azure-portalen.

1. Välj **Lägg till**. Sök på Azure Marketplace efter **belastningsutjämnare**. Välj **Belastningsutjämnare**.

1. Välj **Skapa**.

1. Konfigurera belastningsutjämnaren med:

   - **Prenumeration**: Din Azure-prenumeration.
   - **Resursgrupp**: Resursgruppen som innehåller dina virtuella datorer.
   - **Namn**: Ett namn som identifierar belastningsutjämnaren.
   - **Region**: Den Azure-plats som innehåller dina virtuella datorer.
   - **Typ**: Antingen offentliga eller privata. En privat belastningsutjämnare kan nås inifrån det virtuella nätverket. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via internet använder du en offentlig belastningsutjämnare.
   - **SKU**: Standard.
   - **Virtuellt nätverk:** Samma nätverk som de virtuella datorerna.
   - **IP-adresstilldelning**: Statisk. 
   - **Privat IP-adress:** DEN IP-adress som du har tilldelat till SQL Server FCI-klusternätverksresursen.

 Följande skärmbild visar användargränssnittet **Skapa belastningsutjämnare:**

   ![Ställ in belastningsutjämnaren](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurera serverda poolen för belastningsutjämning

1. Återgå till Azure-resursgruppen som innehåller de virtuella datorerna och hitta den nya belastningsutjämnaren. Du kan behöva uppdatera vyn i resursgruppen. Välj belastningsutjämnaren.

1. Välj **Backend-pooler**och välj sedan **Lägg till**.

1. Associera backend-poolen med tillgänglighetsuppsättningen som innehåller de virtuella datorerna.

1. Under **Ip-konfigurationer**för målnätverk väljer du **VIRTUELL DATOR** och väljer de virtuella datorer som ska delta som klusternoder. Var noga med att inkludera alla virtuella datorer som kommer att vara värd för FCI.

1. Välj **OK** om du vill skapa backend-poolen.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurera en hälsoavsökning för lastbalanserare

1. På belastningsutjämnarbladet väljer du **Hälsoavsökningar**.

1. Välj **Lägg till**.

1. På **Bladet Lägg till hälsoavsökning** <a name="probe"> </a>ställer du in parametrarna för hälsoavsökningen.

   - **Namn**: Ett namn på hälsoavsökningen.
   - **Protokoll**: TCP.
   - **Port**: Ställ in på porten som du skapade i brandväggen för hälsoavsökningen i [det här steget](#ports). I den här artikeln använder exemplet `59999`TCP-port .
   - **Intervall**: 5 sekunder.
   - **Fel tröskel:** 2 på varandra följande fel.

1. Välj **OK**.

### <a name="set-load-balancing-rules"></a>Ange regler för belastningsutjämning

1. På belastningsutjämnbladet väljer du **Belastningsutjämningsregler**.

1. Välj **Lägg till**.

1. Ange parametrar för belastningsutjämningsregeln:

   - **Namn**: Ett namn på belastningsutjämningsreglerna.
   - **Klientåtkomst-IP-adress:** IP-adressen för SQL Server FCI-klusternätverksresursen.
   - **Port**: SQL Server FCI TCP-port. Standardinstansporten är 1433.
   - **Serveringsport:** Använder samma port som **portvärdet** när du aktiverar **Flytande IP (direkt serverretur)**.
   - **Serverdapool:** Serverdapoolens namn som du konfigurerade tidigare.
   - **Hälsoavsökning:** Hälsoavsökningen som du konfigurerade tidigare.
   - **Sessionsensenyrs**: Ingen.
   - **Tidsgränsen för inaktiv utskrift (minuter):** 4.
   - **Flytande IP (direkt serverretur)**: Aktiverad.

1. Välj **OK**.

## <a name="step-6-configure-the-cluster-for-the-probe"></a>Steg 6: Konfigurera klustret för avsökningen

Ange parametern klusteravsökningsport i PowerShell.

Om du vill ange parametern klusteravsökningsport uppdaterar du variablerna i följande skript med värden från din miljö. Ta bort vinkelparenteserna (`<` och `>`) från skriptet.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

I följande lista beskrivs de värden som du behöver uppdatera:

   - `<Cluster Network Name>`: Windows Server Redundansklusternamn för nätverket. Högerklicka på nätverket i Klusterhanteraren för **redundansnätverk** > **Networks**och välj **Egenskaper**. Det korrekta värdet finns under **Namn** på fliken **Allmänt.**

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI IP-adressresursnamn.  > **Roles**Högerklicka **Failover Cluster Manager**på IP-adressresursen under rollen SQL Server FCI under **Servernamn**och välj **Egenskaper**. Det korrekta värdet finns under **Namn** på fliken **Allmänt.** 

   - `<ILBIP>`: ILB:s IP-adress. Den här adressen har konfigurerats i Azure-portalen som ILB-klientadressen. Detta är också SQL Server FCI IP-adress. Du hittar den i **Redundansklusterhanteraren** på samma `<SQL Server FCI IP Address Resource Name>`egenskapssida där du hittade .  

   - `<nnnnn>`: Avsökningsporten som du konfigurerade i hälsoavsökningen för belastningsutjämnare. Oanvänd TCP-port är giltig.

>[!IMPORTANT]
>Undernätsmasken för klusterparametern måste vara TCP IP-broadcast-adressen: `255.255.255.255`.

När du har angett klusteravsökningen kan du se alla klusterparametrar i PowerShell. Kör det här skriptet:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-7-test-fci-failover"></a>Steg 7: Testa FCI-redundans

Testa redundans för FCI för att validera klusterfunktioner. Utför följande steg:

1. Anslut till en av SQL Server FCI-klusternoderna med hjälp av RDP.

1. Öppna **Redundansklusterhanteraren**. Välj **Roller**. Observera vilken nod som äger SQL Server FCI-rollen.

1. Högerklicka på SQL Server FCI-rollen.

1. Välj **Flytta**och välj sedan **Bästa möjliga nod**.

**Redundansklusterhanteraren** visar rollen och dess resurser kopplas från. Resurserna flyttar sedan och kommer online på den andra noden.

### <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen loggar du in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och anslut till SQL Server FCI-namnet.

>[!NOTE]
>Om du behöver kan du [hämta SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Begränsningar

Virtuella Azure-datorer stöder Microsoft Distributed Transaction Coordinator (MSDTC) på Windows Server 2019 med lagring på klustrade delade volymer (CSV) och en [standardbelastningsutjämnare](../../../load-balancer/load-balancer-standard-overview.md).

På virtuella Azure-datorer stöds INTE MSDTC på Windows Server 2016 eller tidigare på grund av:

- Den klustrade MSDTC-resursen kan inte konfigureras för att använda delad lagring. Om du skapar en MSDTC-resurs i Windows Server 2016 visas ingen delad lagring som är tillgänglig för användning, även om det finns tillgängligt lagringsutrymme. Det här problemet har åtgärdats i Windows Server 2019.
- Den grundläggande belastningsutjämnaren hanterar inte RPC-portar.

## <a name="see-also"></a>Se även

[Konfigurera Lagringsutrymmen direkt med fjärrskrivbord (Azure)](https://technet.microsoft.com/windows-server-docs/compute/remote-desktop-services/rds-storage-spaces-direct-deployment)

[Hyperkonvergerad lösning med Storage Spaces Direct](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct)

[Översikt över lagringsdirigering](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/storage-spaces-direct-overview)

[SQL Server-stöd för Storage Spaces Direct](https://blogs.technet.microsoft.com/dataplatforminsider/2016/09/27/sql-server-2016-now-supports-windows-server-2016-storage-spaces-direct/)
