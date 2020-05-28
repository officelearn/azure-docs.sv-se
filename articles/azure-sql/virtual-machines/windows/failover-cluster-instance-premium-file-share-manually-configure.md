---
title: SQL Server FCI med Premium File Share – Azure Virtual Machines
description: Den här artikeln beskriver hur du skapar en SQL Server redundanskluster med hjälp av en Premium-filresurs på virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 9fc761b1-21ad-4d79-bebc-a2f094ec214d
ms.service: virtual-machines-sql
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/09/2019
ms.author: mathoma
ms.openlocfilehash: 60526dbeb3e221e6a2e4c6b900ff3a109d4cdf8f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84045965"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurera en SQL Server-redundanskluster med Premium-filresurs på virtuella Azure-datorer
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Den här artikeln beskriver hur du skapar en SQL Server-FCI (failover Cluster instance) på virtuella Azure-datorer med hjälp av en [Premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Premium-filresurser är SSD-backade, konsekventa fil resurser med låg latens som stöds fullt ut för användning med kluster instanser för växling vid fel för SQL Server 2012 eller senare på Windows Server 2012 eller senare. Premium-filresurser ger dig större flexibilitet, så att du kan ändra storlek på och skala en fil resurs utan drift avbrott.


## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och har på plats innan du börjar.

Du bör ha en operationell förståelse för dessa tekniker:

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

En sak att vara medveten om är att på ett Azure IaaS VM-redundanskluster rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans som gör att ytterligare nätverkskort och undernät inte behövs på ett gäst kluster för en Azure IaaS-dator. I kluster verifierings rapporten visas en varning om att noderna bara kan kommas åt i ett enda nätverk. Du kan ignorera den här varningen på virtuella Azure IaaS-redundanskluster.

Du bör också ha en allmän förståelse för dessa tekniker:

- [Azure Premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure-resursgrupper](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> För närvarande stöds SQL Server redundanskluster på Azure Virtual Machines endast med [läget för förenklad hantering](sql-vm-resource-provider-register.md#management-modes) i [SQL Server IaaS agent-tillägget](sql-server-iaas-agent-extension-automate-management.md). Om du vill ändra från fullständigt tillägg till Lightweight tar du bort den **virtuella SQL-datorns** resurs för motsvarande virtuella datorer och registrerar dem sedan med resurs leverantören för SQL-VM i Lightweight-läge. När du tar bort den **virtuella SQL-datorns** resurs med hjälp av Azure Portal **avmarkerar du kryss rutan bredvid rätt virtuell dator**. Det fullständiga tillägget har stöd för funktioner som automatisk säkerhets kopiering, uppdatering och avancerad Portal hantering. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har installerats om i läget för förenklad hantering.

Premium-filresurser ger IOPS och data flödes kapacitet som uppfyller behoven hos många arbets belastningar. För i/o-intensiva arbets belastningar kan du överväga att [SQL Server instanser av redundanskluster med Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md), baserat på hanterade Premium diskar eller Ultra disks.  

Kontrol lera din miljös IOPS-aktivitet och kontrol lera att Premium-filresurser kommer att ge den IOPS du behöver innan du påbörjar en distribution eller migrering. Använd disk räknare i Windows prestanda övervakaren för att övervaka total antalet IOPS (disk överföringar/sekund) och data flöde (Disk-byte/sekund) som krävs för att SQL Server data, logg och tillfälliga DB-filer.

Många arbets belastningar har burst i/o, så det är en bra idé att kontrol lera under tung användnings perioder och notera både högsta IOPS och den genomsnittliga IOPS. Premium-filresurser ger IOPS baserat på resursens storlek. Premium-filresurser ger också en kostnads fri burst-överföring som gör att du kan överföra ditt IO-värde till tre timmar.

Mer information om Premium-filresursens prestanda finns i [fil resurs prestanda nivåer](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licensiering och priser

På Azure Virtual Machines kan du licensiera SQL Server med hjälp av PAYG (betala per användning) eller BYOL-avbildningar (). Vilken typ av avbildning du väljer påverkar hur du debiteras.

Med betala per användning-licens debiteras du för alla noder i FCI, inklusive de passiva noderna, i en FCI (failover Cluster instance) för SQL Server på virtuella Azure-datorer. Mer information finns i [SQL Server Enterprise Virtual Machines prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Om du har Enterprise-avtal med Software Assurance kan du använda en kostnads fri passiv FCI-nod för varje aktiv nod. Om du vill dra nytta av den här förmånen i Azure använder du BYOL VM-avbildningar och använder samma licens på både aktiva och passiva noder i FCI. Mer information finns i [Enterprise-avtal](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Information om hur du jämför "betala per användning" och BYOL-licensiering för SQL Server på Azure Virtual Machines finns i [Kom igång med virtuella SQL](sql-server-on-azure-vm-iaas-what-is-overview.md#get-started-with-sql-vms)-datorer.

Fullständig information om licens SQL Server finns i [prissättning](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>FileStream

FILESTREAM stöds inte för ett redundanskluster med en Premium-filresurs. Om du vill använda FILESTREAM distribuerar du klustret med hjälp av [Lagringsdirigering](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

## <a name="prerequisites"></a>Förutsättningar

Innan du slutför stegen i den här artikeln bör du redan ha:

- En Microsoft Azure prenumeration.
- En Windows-domän på Azure Virtual Machines.
- Ett domän användar konto som har behörighet att skapa objekt både på virtuella Azure-datorer och i Active Directory.
- Ett domän användar konto för att köra SQL Server tjänsten och som du kan logga in på den virtuella datorn med när du monterar fil resursen.  
- Ett virtuellt Azure-nätverk och undernät med tillräckligt med IP-adressutrymme för dessa komponenter:
   - Två virtuella datorer.
   - IP-adressen för klustret för växling vid fel.
   - En IP-adress för varje FCI.
- DNS konfigurerat på Azure-nätverket och pekar på domän kontrol Lanterna.
- En [Premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md) som ska användas som den klustrade enheten, baserat på lagrings kvoten för din databas för dina datafiler.
- Om du använder Windows Server 2012 R2 och äldre behöver du en annan fil resurs som används som fil resurs vittne, eftersom det finns stöd för moln vittnen för Windows 2016 och nyare. Du kan använda en annan Azure-filresurs, eller så kan du använda en fil resurs på en separat virtuell dator. Om du ska använda en annan Azure-filresurs kan du montera den med samma process som för den Premium-filresurs som används för den klustrade enheten. 

Med dessa krav på plats kan du börja skapa ett redundanskluster. Det första steget är att skapa de virtuella datorerna.

## <a name="step-1-create-the-virtual-machines"></a>Steg 1: skapa de virtuella datorerna

1. Logga in på [Azure Portal](https://portal.azure.com) med din prenumeration.

1. [Skapa en tillgänglighets uppsättning i Azure](../../../virtual-machines/linux/tutorial-availability-sets.md).

   Tillgänglighets uppsättningen grupperar virtuella datorer över fel domäner och uppdaterings domäner. Det garanterar att ditt program inte påverkas av enskilda felpunkter, t. ex. nätverks växeln eller enheten för en rack Server.

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

   Etablera två SQL Server virtuella datorer i tillgänglighets uppsättningen för Azure. Instruktioner finns i [etablera en SQL Server virtuell dator i Azure Portal](create-sql-vm-portal.md).

   Placera båda virtuella datorerna:

   - I samma Azure-resurs grupp som din tillgänglighets uppsättning.
   - I samma nätverk som domänkontrollanten.
   - I ett undernät som har tillräckligt med IP-adressutrymme för både virtuella datorer och alla skyddas som du kan använda i klustret.
   - I Azures tillgänglighets uppsättning.

      >[!IMPORTANT]
      >Du kan inte ange eller ändra tillgänglighets uppsättningen när du har skapat en virtuell dator.

   Välj en avbildning från Azure Marketplace. Du kan använda en Azure Marketplace-avbildning som innehåller Windows Server och SQL Server, eller använda en som bara innehåller Windows Server. Mer information finns i [Översikt över SQL Server på virtuella Azure-datorer](sql-server-on-azure-vm-iaas-what-is-overview.md).

   De officiella SQL Server avbildningarna i Azure-galleriet innehåller en installerad SQL Server instans, SQL Server-installations program och nödvändig nyckel.

   >[!IMPORTANT]
   > När du har skapat den virtuella datorn tar du bort den förinstallerade fristående SQL Server-instansen. Du använder förinstallerade SQL Server media för att skapa SQL Server-FCI när du har ställt in klustret för växling vid fel och Premium-filresursen som lagrings plats.

   Du kan också använda Azure Marketplace-avbildningar som bara innehåller operativ systemet. Välj en **Windows Server 2016 Data Center** -avbildning och installera SQL Server FCI när du har ställt in klustret för växling vid fel och Premium-filresursen som lagrings plats. Den här avbildningen innehåller inte SQL Server installationsmedia. Placera installations mediet för SQL Server på en plats där du kan köra det för varje server.

1. När Azure har skapat de virtuella datorerna ansluter du till var och en med hjälp av RDP.

   När du först ansluter till en virtuell dator med hjälp av RDP frågar en fråga om du vill att datorn ska kunna identifieras i nätverket. Välj **Ja**.

1. Om du använder någon av de SQL Server-baserade avbildningarna av virtuella datorer tar du bort SQL Server-instansen.

   1. I **program och funktioner**högerklickar du på **Microsoft SQL Server 201_ (64-bitars)** och väljer **Avinstallera/ändra**.
   1. Välj **Ta bort**.
   1. Välj standard instansen.
   1. Ta bort alla funktioner under **databas motor tjänster**. Ta inte bort **Delade funktioner**. Du ser något som liknar följande skärm bild:

        ![Välj funktioner](./media/failover-cluster-instance-premium-file-share-manually-configure/03-remove-features.png)

   1. Välj **Nästa**och välj sedan **ta bort**.

1. <span id="ports"> </span> Öppna brand Väggs portarna.  

   Öppna dessa portar i Windows-brandväggen på varje virtuell dator:

   | Syfte | TCP-port | Anteckningar
   | ------ | ------ | ------
   | SQL Server | 1433 | Normal port för standard instanser av SQL Server. Om du använde en avbildning från galleriet öppnas porten automatiskt.
   | Hälsoavsökning | 59999 | Alla öppna TCP-portar. I ett senare steg konfigurerar du belastnings utjämningens [hälso avsökning](#probe) och klustret för att använda den här porten.
   | Filresurs | 445 | Port som används av fil resurss tjänsten.

1. [Lägg till de virtuella datorerna i din befintliga domän](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain).

När du har skapat och konfigurerat de virtuella datorerna kan du konfigurera Premium-filresursen.

## <a name="step-2-mount-the-premium-file-share"></a>Steg 2: montera Premium-filresursen

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till ditt lagrings konto.
1. Gå till **fil resurser** under **fil tjänst** och välj den Premium-filresurs som du vill använda för din SQL-lagring.
1. Välj **Anslut** för att ta fram anslutnings strängen för din fil resurs.
1. Välj den enhets beteckning som du vill använda i den nedrullningsbara listan och kopiera sedan båda kod blocken till anteckningar.


   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/premium-file-storage-commands.png" alt-text="Kopiera båda PowerShell-kommandona från fil resursen Connect-portalen":::

1. Använd RDP för att ansluta till SQL Server VM med det konto som din SQL Server-FCI kommer att använda för tjänst kontot.
1. Öppna en administrativ PowerShell-kommandorad.
1. Kör de kommandon som du sparade tidigare när du arbetade i portalen.
1. Gå till resursen med hjälp av antingen Utforskaren eller dialog rutan **Kör** (Windows-tangenten + r). Använd Nätverks Sök vägen `\\storageaccountname.file.core.windows.net\filesharename` . Till exempel, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Skapa minst en mapp på den nyligen anslutna fil resursen för att placera dina SQL-datafiler i.
1. Upprepa de här stegen på varje SQL Server VM som ska ingå i klustret.

  > [!IMPORTANT]
  > - Överväg att använda en separat fil resurs för säkerhets kopierings filer för att spara IOPS och utrymmes kapaciteten för den här resursen för data-och loggfiler. Du kan använda antingen en Premium-eller standard fil resurs för säkerhets kopierings filer.
  > - Om du använder Windows 2012 R2 och äldre följer du samma steg för att montera fil resursen som du kommer att använda som fil resurs vittne. 

## <a name="step-3-configure-the-failover-cluster"></a>Steg 3: Konfigurera redundansklustret

Nästa steg är att konfigurera redundansklustret. I det här steget ska du utföra följande under steg:

1. Lägg till funktionen kluster för växling vid fel i Windows Server.
1. Verifiera klustret.
1. Skapa klustret för växling vid fel.
1. Skapa moln vittnet (för Windows Server 2016 och nyare) eller fil resurs vittnet (för Windows Server 2012 R2 och äldre).


### <a name="add-windows-server-failover-clustering"></a>Lägg till kluster för växling vid fel i Windows Server

1. Anslut till den första virtuella datorn med RDP genom att använda ett domän konto som är medlem i den lokala administratören och som har behörighet att skapa objekt i Active Directory. Använd det här kontot för resten av konfigurationen.

1. [Lägg till kluster för växling vid fel på varje virtuell dator](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms).

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

### <a name="validate-the-cluster"></a>Verifiera klustret

Verifiera klustret i användar gränssnittet eller med hjälp av PowerShell.

Verifiera klustret med hjälp av användar gränssnittet genom att utföra följande steg på en av de virtuella datorerna:

1. Under **Serverhanteraren**väljer du **verktyg**och väljer sedan **Klusterhanteraren för växling vid fel**.
1. Under **Klusterhanteraren för växling vid fel**väljer du **åtgärd**och väljer sedan **Verifiera konfiguration**.
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster**anger du namnen på de båda virtuella datorerna.
1. Under **test alternativ**väljer **du kör endast test som jag väljer**. Välj **Nästa**.
1. Under **Val av test**väljer du alla tester förutom **lagring** och **Lagringsdirigering**, som du ser här:

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/cluster-validation.png" alt-text="Välj kluster verifierings test":::

1. Välj **Nästa**.
1. Under **bekräftelse**väljer du **Nästa**.

Verifierings testen körs i **guiden Verifiera en konfiguration** .

Om du vill verifiera klustret med hjälp av PowerShell kör du följande skript från en administratör PowerShell-session på en av de virtuella datorerna:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

När du har verifierat klustret skapar du klustret för växling vid fel.

### <a name="create-the-failover-cluster"></a>Skapa redundansklustret

Du behöver följande om du vill skapa ett kluster för växling vid fel:
- Namnen på de virtuella datorer som kommer att bli klusternoder.
- Ett namn för redundansklustret
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används på samma virtuella Azure-nätverk och undernät som klusternoderna.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 via Windows Server 2016

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2012 via Windows Server 2016. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2019. Mer information finns i [redundansklustret: kluster nätverks objekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Skapa ett moln vittne (Win 2016 +)

Om du använder Windows Server 2016 och senare måste du skapa ett moln vittne. Moln vittne är en ny typ av klusterkvorum som lagras i en Azure Storage-blob. Detta tar bort behovet av en separat virtuell dator som är värd för en vittnes resurs eller som använder en separat fil resurs.

1. [Skapa ett moln vittne för redundansklustret](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en BLOB-behållare.

1. Spara åtkomst nycklarna och behållar-URL: en.

### <a name="configure-quorum"></a>Konfigurera kvorum 

För Windows Server 2016 och senare konfigurerar du klustret så att det använder det moln vittne som du nyss skapade. Följ alla steg för att [Konfigurera kvorumdisken i användar gränssnittet](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

För Windows Server 2012 R2 och äldre följer du samma steg i [Konfigurera kvorumdisken i användar gränssnittet](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) , men på sidan **Välj kvorumdisk** väljer du alternativet **Konfigurera ett fil resurs vittne** . Ange den fil resurs som du allokerat som fil resurs vittnet, oavsett om det är ett konto som du har konfigurerat på en separat virtuell dator eller monterat från Azure. 


## <a name="step-4-test-cluster-failover"></a>Steg 4: testa redundanskluster

Testa redundansväxlingen av klustret. I **Klusterhanteraren för växling vid fel**högerklickar du på klustret och väljer **fler åtgärder**  >  **Flytta kärn kluster resurs**  >  **Välj nod**och välj sedan den andra noden i klustret. Flytta kärn kluster resursen till varje nod i klustret och flytta tillbaka den till den primära noden. Om du kan flytta klustret till varje nod är du redo att installera SQL Server.  

:::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/test-cluster-failover.png" alt-text="Testa redundanskluster genom att flytta kärn resursen till de andra noderna":::

## <a name="step-5-create-the-sql-server-fci"></a>Steg 5: skapa SQL Server FCI

När du har konfigurerat klustret för växling vid fel kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med hjälp av RDP.

1. I **Klusterhanteraren för växling vid fel**kontrollerar du att alla kärn kluster resurser finns på den första virtuella datorn. Om du behöver kan du flytta alla resurser till den virtuella datorn.

1. Leta upp installations mediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full` . Välj **installation**.

1. I **installations Center för SQL Server**väljer du **installation**.

1. Välj **ny SQL Server redundanskluster installationen**. Följ anvisningarna i guiden för att installera SQL Server FCI.

   FCI data kataloger måste finnas på Premium-filresursen. Ange den fullständiga sökvägen till resursen i det här formuläret: `\\storageaccountname.file.core.windows.net\filesharename\foldername` . En varning visas som talar om att du har angett en fil server som data katalog. Den här varningen förväntas. Se till att det användar konto som du använder RDP till den virtuella datorn när du sparade fil resursen är samma konto som den SQL Server tjänsten använder för att undvika eventuella problem.

   :::image type="content" source="media/manually-configure-failover-cluster-instance-premium-file-share/use-file-share-as-data-directories.png" alt-text="Använd fil resurs som SQL data-kataloger":::

1. När du har slutfört stegen i guiden kommer installations programmet att installera en SQL Server FCI på den första noden.

1. När installations programmet har installerat FCI på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **installations Center för SQL Server**. Välj **installation**.

1. Välj **Lägg till nod i ett SQL Server redundanskluster**. Följ anvisningarna i guiden för att installera SQL Server och lägga till servern i FCI.

   >[!NOTE]
   >Om du använde en Azure Marketplace-Galleri avbildning med SQL Server, inkluderades SQL Server verktyg tillsammans med avbildningen. Om du inte använde någon av dessa avbildningar installerar du SQL Server verktyg separat. Se [Ladda ned SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Steg 6: Skapa Azure Load Balancer

På Azure Virtual Machines använder kluster en belastningsutjämnare för att lagra en IP-adress som måste finnas på en klusternod i taget. I den här lösningen innehåller belastningsutjämnaren IP-adressen för SQL Server FCI.

Mer information finns i [skapa och konfigurera en Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure Portal

Så här skapar du belastningsutjämnaren:

1. I Azure Portal går du till resurs gruppen som innehåller de virtuella datorerna.

1. Välj **Lägg till**. Sök på Azure Marketplace efter **Load Balancer**. Välj **Load Balancer**.

1. Välj **Skapa**.

1. Konfigurera belastningsutjämnaren med hjälp av följande värden:

   - **Prenumeration**: din Azure-prenumeration.
   - **Resurs grupp**: den resurs grupp som innehåller de virtuella datorerna.
   - **Namn**: ett namn som identifierar belastningsutjämnaren.
   - **Region**: Azure-platsen som innehåller de virtuella datorerna.
   - **Typ**: antingen offentlig eller privat. Det går att få åtkomst till en privat belastningsutjämnare inifrån det virtuella nätverket. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via Internet använder du en offentlig belastningsutjämnare.
   - **SKU**: standard.
   - **Virtuellt nätverk**: samma nätverk som de virtuella datorerna.
   - **IP-adresstilldelning**: statisk. 
   - **Privat IP-adress**: den IP-adress som du tilldelade SQL Server FCI-kluster nätverks resurs.

   Följande bild visar användar gränssnittet för **belastnings utjämning** :

   ![Konfigurera belastningsutjämnaren](./media/failover-cluster-instance-premium-file-share-manually-configure/30-load-balancer-create.png)
   

### <a name="configure-the-load-balancer-backend-pool"></a>Konfigurera backend-poolen för belastnings utjämning

1. Gå tillbaka till den Azure-resurs grupp som innehåller de virtuella datorerna och leta upp den nya belastningsutjämnaren. Du kan behöva uppdatera vyn i resurs gruppen. Markera belastningsutjämnaren.

1. Välj **backend-pooler**och välj sedan **Lägg till**.

1. Koppla backend-poolen till den tillgänglighets uppsättning som innehåller de virtuella datorerna.

1. Under **mål nätverkets IP-konfigurationer**väljer du **virtuell dator** och väljer de virtuella datorer som ska ingå som klusternoder. Se till att ta med alla virtuella datorer som ska vara värdar för FCI.

1. Välj **OK** för att skapa backend-poolen.

### <a name="configure-a-load-balancer-health-probe"></a>Konfigurera en hälsoavsökning för lastbalanserare

1. På bladet belastnings utjämning väljer du **hälso avsökningar**.

1. Välj **Lägg till**.

1. <span id="probe"> </span> Ange följande parametrar för hälso avsökning på bladet **Lägg till hälso avsökning** .

   - **Namn**: ett namn för hälso avsökningen.
   - **Protokoll**: TCP.
   - **Port**: den port som du skapade i brand väggen för hälso avsökningen i [det här steget](#ports). I den här artikeln använder exemplet TCP-port `59999` .
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
   - **Backend-port**: använder samma port som **port** värde när du aktiverar **flytande IP (direkt Server retur)**.
   - **Backend-pool**: det namn på backend-poolen som du konfigurerade tidigare.
   - **Hälso avsökning**: hälso avsökningen som du konfigurerade tidigare.
   - **Persistence för session**: ingen.
   - **Tids gräns för inaktivitet (minuter)**: 4.
   - **Flytande IP (direkt Server retur)**: aktive rad.

1. Välj **OK**.

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Steg 7: Konfigurera klustret för avsökningen

Ange port parametern för kluster avsökningen i PowerShell.

Om du vill ange parametern för kluster avsöknings porten uppdaterar du variablerna i följande skript med värden från din miljö. Ta bort vinkelparenteser ( `<` och `>` ) från skriptet.

   ```powershell
   $ClusterNetworkName = "<Cluster Network Name>"
   $IPResourceName = "<SQL Server FCI IP Address Resource Name>" 
   $ILBIP = "<n.n.n.n>" 
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

I följande lista beskrivs de värden som du behöver uppdatera:

   - `<Cluster Network Name>`: Namnet på Windows Server-redundansklustret för nätverket. I **Klusterhanteraren för växling vid fel**  >  **nätverk**högerklickar du på nätverket och väljer **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** .

   - `<SQL Server FCI IP Address Resource Name>`: Den SQL Server FCI IP-adressresurs. I **Klusterhanteraren för växling vid fel**  >  **roller**, under rollen SQL Server FCI, under **Server namn**högerklickar du på IP-adressresursen och väljer **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** .

   - `<ILBIP>`: ILB IP-adress. Adressen är konfigurerad i Azure Portal som klient adress för ILB. Detta är också den SQL Server FCI-IP-adressen. Du hittar den i **Klusterhanteraren för växling vid fel** på samma egenskaps sida där du placerade `<SQL Server FCI IP Address Resource Name>` .  

   - `<nnnnn>`: Avsöknings porten som du konfigurerade i belastnings utjämningens hälso avsökning. En oanvänd TCP-port är giltig.

>[!IMPORTANT]
>Nät masken för kluster parametern måste vara TCP IP-broadcast-adressen: `255.255.255.255` .

När du har ställt in kluster avsökningen kan du se alla kluster parametrar i PowerShell. Kör det här skriptet:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter
  ```

## <a name="step-8-test-fci-failover"></a>Steg 8: testa FCI redundans

Testa redundansväxlingen av FCI för att verifiera kluster funktionen. Gör följande:

1. Anslut till en av SQL Server FCI-klusternoderna med hjälp av RDP.

1. Öppna **Klusterhanteraren för växling vid fel**. Välj **roller**. Lägg märke till vilken nod som äger rollen SQL Server FCI.

1. Högerklicka på rollen SQL Server FCI.

1. Välj **Flytta**och välj sedan **bästa möjliga nod**.

**Klusterhanteraren för växling vid fel** visar rollen och dess resurser går offline. Resurserna flyttar sedan och kommer tillbaka online på den andra noden.

### <a name="test-connectivity"></a>Testa anslutning

Om du vill testa anslutningen loggar du in på en annan virtuell dator i samma virtuella nätverk. Öppna **SQL Server Management Studio** och anslut till SQL Server FCI namn.

>[!NOTE]
>Om du behöver kan du [hämta SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="limitations"></a>Begränsningar

Azure Virtual Machines har stöd för Microsoft koordinator för distribuerad transaktion (MSDTC) på Windows Server 2019 med lagring på klusterdelade volymer (CSV) och en [standard belastnings utjämning](../../../load-balancer/load-balancer-standard-overview.md).

På Azure Virtual Machines stöds inte MSDTC på Windows Server 2016 eller tidigare eftersom:

- Det går inte att konfigurera den klustrade MSDTC-resursen att använda delad lagring. Om du skapar en MSDTC-resurs i Windows Server 2016 visas inga delade lagrings enheter som är tillgängliga för användning, även om lagring är tillgängligt. Det här problemet har åtgärd ATS i Windows Server 2019.
- Den grundläggande belastningsutjämnaren hanterar inte RPC-portar.

## <a name="see-also"></a>Se även

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instanser av redundanskluster](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
