---
title: SQL Server FCI med Premium File Share – Azure Virtual Machines
description: Den här artikeln beskriver hur du skapar en SQL Server redundanskluster med en Premium-filresurs på Azure Virtual Machines.
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
ms.openlocfilehash: 839faa4cf2455ee2b0de38046a464ce824f007cd
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301872"
---
# <a name="configure-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurera SQL Server kluster instans med Premium-filresurs på Azure Virtual Machines

Den här artikeln beskriver hur du skapar en SQL Server-FCI (failover Cluster instance) på virtuella Azure-datorer med en [Premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md). 

Premium-filresurser är SSD-omständigt-begränsade fil resurser med låg latens som fullständigt stöds för användning med kluster instans för växling vid fel i SQL Server 2012 och senare på Windows Server 2012 och senare. Premium-filresurser ger dig större flexibilitet, så att du kan ändra storlek på och skala fil resursen utan drift avbrott. 


## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och några saker du behöver innan du fortsätter.

Du bör ha en drifts förståelse för följande tekniker:

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instanser av kluster för växling vid fel](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).

En viktig skillnad är att i ett Azure IaaS VM-redundanskluster rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azures nätverk har fysisk redundans, vilket innebär att det inte behövs fler nätverkskort och undernät för gästkluster på virtuella Azure IaaS-datorer. Även om kluster verifierings rapporten utfärdar en varning om att noderna bara kan användas i ett enda nätverk, kan den här varningen ignoreras på ett säkert sätt i Azure IaaS VM-redundanskluster. 

Dessutom bör du ha en allmän förståelse för följande tekniker:

- [Azure Premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Resurs grupper i Azure](../../../azure-resource-manager/manage-resource-groups-portal.md)

> [!IMPORTANT]
> För närvarande stöds SQL Server redundanskluster på Azure Virtual Machines endast med läget för [förenklad](virtual-machines-windows-sql-register-with-resource-provider.md#register-with-sql-vm-resource-provider) hantering i [SQL Server IaaS agent-tillägget](virtual-machines-windows-sql-server-agent-extension.md). Avinstallera det fullständiga tillägget från de virtuella datorer som ingår i redundansklustret och registrera dem sedan med resurs leverantören för SQL-VM i `lightweight`-läge. Det fullständiga tillägget har stöd för funktioner som automatisk säkerhets kopiering, uppdatering och avancerad Portal hantering. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har installerats om i läget för förenklad hantering.

### <a name="workload-consideration"></a>Arbets belastnings överväganden

Premium-filresurser ger IOPS och alla kapaciteter som uppfyller behoven hos många arbets belastningar. För i/o-intensiva arbets belastningar kan du dock överväga [SQL Server FCI med Lagringsdirigering](virtual-machines-windows-portal-sql-create-failover-cluster.md) baserat på hanterade Premium diskar eller Ultra disks.  

Kontrol lera IOPS-aktiviteten i din aktuella miljö och kontrol lera att Premium-filerna ger de IOPS du behöver innan du påbörjar en distribution eller migrering. Använd Windows prestanda övervakaren disk räknare och övervaka total IOPS (disk överföringar/s) och data flöde (Disk-byte/s) som krävs för att SQL Server data, logg och tillfälliga DB-filer. Många arbets belastningar har burst i/o, så det är en bra idé att kontrol lera under tung användnings perioder och notera Max IOPS samt genomsnittlig IOPS. Premium Files-resurser tillhandahåller IOPS baserat på resursens storlek. Premium-filer ger också en kostnads fri burst-överföring där du kan överföra ditt IO-värde till tredubbla bas linjen i upp till en timme. 

### <a name="licensing-and-pricing"></a>Licensiering och priser

På Azure Virtual Machines kan du licens SQL Server använda betala per användning (PAYG) eller ta med egna licens-avbildningar (BYOL). Vilken typ av avbildning du väljer påverkar hur du debiteras.

Med PAYG-licensiering debiteras en FCI (failover Cluster instance) av SQL Server på Azure Virtual Machines avgifter för alla noder i FCI, inklusive de passiva noderna. Mer information finns i [SQL Server Enterprise Virtual Machines prissättning](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/). 

Kunder med Enterprise-avtal med Software Assurance har rätt att använda en kostnads fri passiv FCI-nod för varje aktiv nod. Om du vill dra nytta av den här förmånen i Azure använder du BYOL VM-avbildningar och använder sedan samma licens på både aktiva och passiva noder i FCI. Mer information finns i [Enterprise-avtal](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Om du vill jämföra PAYG-och BYOL-licensieringen för SQL Server på Azure Virtual Machines se [Kom igång med virtuella SQL-datorer](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Fullständig information om licens SQL Server finns i [prissättning](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="limitations"></a>Begränsningar

- FILESTREAM stöds inte för ett redundanskluster med en Premium-filresurs. Om du vill använda FILESTREAM distribuerar du klustret med hjälp av [Lagringsdirigering](virtual-machines-windows-portal-sql-create-failover-cluster.md). 

## <a name="prerequisites"></a>Krav 

Innan du följer anvisningarna i den här artikeln bör du redan ha:

- En Microsoft Azure prenumeration.
- En Windows-domän på Azure Virtual Machines.
- Ett konto med behörighet att skapa objekt på den virtuella Azure-datorn.
- Ett virtuellt Azure-nätverk och undernät med tillräckligt med IP-adressutrymme för följande komponenter:
   - Båda virtuella datorerna.
   - IP-adressen för klustret för växling vid fel.
   - En IP-adress för varje FCI.
- DNS konfigurerat på Azure-nätverket och pekar på domän kontrol Lanterna.
- En [Premium fil resurs](../../../storage/files/storage-how-to-create-premium-fileshare.md) baserat på lagrings kvoten för din databas för dina datafiler. 
- En fil resurs för säkerhets kopieringar som skiljer sig från Premium-filresursen som används för dina datafiler. Fil resursen kan antingen vara standard eller Premium. 

Med dessa krav på plats kan du fortsätta med att skapa ett redundanskluster. Det första steget är att skapa de virtuella datorerna.

## <a name="step-1-create-virtual-machines"></a>Steg 1: skapa virtuella datorer

1. Logga in på [Azure Portal](https://portal.azure.com) med din prenumeration.

1. [Skapa en tillgänglighets uppsättning i Azure](../tutorial-availability-sets.md).

   Tillgänglighets uppsättningen grupperar virtuella datorer över fel domäner och uppdaterings domäner. Tillgänglighets uppsättningen säkerställer att programmet inte påverkas av enskilda felpunkter, t. ex. nätverks växeln eller enhets enheten för en rack Server.

   Om du inte har skapat resurs gruppen för dina virtuella datorer gör du det när du skapar en Azures tillgänglighets uppsättning. Gör så här om du använder Azure Portal för att skapa tillgänglighets uppsättningen:

   - Öppna Azure Marketplace genom att klicka på **+** i Azure Portal. Sök efter **tillgänglighets uppsättning**.
   - Klicka på **tillgänglighets uppsättning**.
   - Klicka på **Skapa**.
   - Ange följande värden på bladet **skapa tillgänglighets uppsättning** :
      - **Namn**: ett namn på tillgänglighets uppsättningen.
      - **Prenumeration**: din Azure-prenumeration.
      - **Resurs grupp**: om du vill använda en befintlig grupp klickar du på **Använd befintlig** och väljer gruppen i den nedrullningsbara listan. Annars väljer du **Skapa ny** och skriver ett namn för gruppen.
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

   >[!IMPORTANT]
   > När du har skapat den virtuella datorn tar du bort den förinstallerade fristående SQL Server-instansen. Du kommer att använda förinstallerade SQL Server media för att skapa SQL Server-FCI när du har konfigurerat redundansklustret och Premium-fildelningen som lagring. 

   Du kan också använda Azure Marketplace-avbildningar med bara operativ systemet. Välj en **Windows Server 2016 Data Center** -avbildning och installera SQL Server FCI när du har konfigurerat klustret för växling vid fel och Premium-filresursen som lagring. Den här avbildningen innehåller inte SQL Server installationsmedia. Placera installations mediet på en plats där du kan köra SQL Server-installationen för varje server. 

1. När Azure har skapat de virtuella datorerna ansluter du till varje virtuell dator med RDP.

   När du först ansluter till en virtuell dator med RDP frågar datorn om du vill att den här datorn ska kunna identifieras i nätverket. Klicka på **Ja**.

1. Om du använder någon av de SQL Server-baserade avbildningarna av virtuella datorer tar du bort SQL Server-instansen.

   - I **program och funktioner**högerklickar du på **Microsoft SQL Server 201_ (64-bitars)** och klickar på **Avinstallera/ändra**.
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
   | Filresurs | 445 | Port som används av fil resurss tjänsten. 

1. [Lägg till de virtuella datorerna i din befintliga domän](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

När de virtuella datorerna har skapats och kon figurer ATS kan du konfigurera Premium-filresursen.

## <a name="step-2-mount-premium-file-share"></a>Steg 2: montera Premium-filresurs

1. Logga in på [Azure Portal](https://portal.azure.com) och gå till ditt lagrings konto.
1. Gå till **fil resurser** under **fil tjänst** och välj den Premium-filresurs som du vill använda för din SQL-lagring. 
1. Välj **Anslut** för att ta fram anslutnings strängen för din fil resurs. 
1. Välj den enhets beteckning som du vill använda från List rutan och kopiera sedan de två PowerShell-kommandona från de två kommando blocken för PowerShell.  Klistra in dem i en text redigerare, till exempel Anteckningar. 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/premium-file-storage-commands.png" alt-text="Kopiera båda PowerShell-kommandona från fil resursen Connect-portalen":::

1. RDP i SQL Server VM med det konto som din SQL Server-FCI kommer att använda för tjänst kontot. 
1. Starta en administrativ PowerShell-kommandorad. 
1. Kör kommandot `Test-NetConnection` för att testa anslutningen till lagrings kontot. Kör inte kommandot `cmdkey` från det första kod blocket. 

   ```console
   example: Test-NetConnection -ComputerName  sqlvmstorageaccount.file.core.windows.net -Port 445
   ```

1. Kör kommandot `cmdkey` från det *andra* kod blocket för att montera fil resursen som en enhet och spara den. 

   ```console
   example: cmdkey /add:sqlvmstorageaccount.file.core.windows.net /user:Azure\sqlvmstorageaccount /pass:+Kal01QAPK79I7fY/E2Umw==
   net use M: \\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare /persistent:Yes
   ```

1. Öppna **Utforskaren** och gå till **den här datorn**. Fil resursen visas under nätverks platser: 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/file-share-as-storage.png" alt-text="Fil resursen visas som lagring i Utforskaren":::

1. Öppna den nyss mappade enheten och skapa minst en mapp här för att placera dina SQL-datafiler i. 
1. Upprepa de här stegen på varje SQL Server VM som ska ingå i klustret. 

  > [!IMPORTANT]
  > Använd inte samma fil resurs för både datafiler och säkerhets kopior. Använd samma steg för att konfigurera en sekundär fil resurs för säkerhets kopior om du vill säkerhetskopiera databaserna till en fil resurs. 

## <a name="step-3-configure-failover-cluster-with-file-share"></a>Steg 3: Konfigurera redundanskluster med fil resurs 

Nästa steg är att konfigurera redundansklustret. I det här steget ska du göra följande under steg:

1. Lägg till funktion för redundanskluster i Windows
1. Verifiera klustret
1. Skapa redundansklustret
1. Skapa moln vittnet


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

### <a name="validate-the-cluster"></a>Verifiera klustret

Den här guiden refererar till instruktioner under [Verifiera kluster](https://technet.microsoft.com/windows-server-docs/storage/storage-spaces/hyper-converged-solution-using-storage-spaces-direct#step-31-run-cluster-validation).

Verifiera klustret i användar gränssnittet eller med PowerShell.

Verifiera klustret med användar gränssnittet genom att utföra följande steg från en av de virtuella datorerna.

1. Klicka på **verktyg**i **Serverhanteraren**och klicka sedan på **Klusterhanteraren för växling vid fel**.
1. I **Klusterhanteraren för växling vid fel**klickar du på **åtgärd**och sedan på **Verifiera konfiguration...** .
1. Klicka på **Next**.
1. På **Välj servrar eller ett kluster**skriver du namnet på båda de virtuella datorerna.
1. På **test alternativ**väljer **du kör endast test som jag väljer**. Klicka på **Next**.
1. Ta med alla tester förutom **lagring** och **Lagringsdirigering**vid **Val av test**. Se följande bild:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/cluster-validation.png" alt-text="Kluster verifierings test":::

1. Klicka på **Next**.
1. Klicka på **Nästa**vid **bekräftelse**.

Verifierings testen körs i **guiden Verifiera en konfiguration** .

Verifiera klustret med PowerShell genom att köra följande skript från en administratörs PowerShell-session på en av de virtuella datorerna.

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

När du har verifierat klustret skapar du klustret för växling vid fel.

### <a name="create-the-failover-cluster"></a>Skapa redundansklustret


Du behöver följande om du vill skapa ett kluster för växling vid fel:
- Namnen på de virtuella datorer som blir klusternoderna.
- Ett namn för redundansklustret
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används på samma virtuella Azure-nätverk och undernät som klusternoderna.

#### <a name="windows-server-2012-2016"></a>Windows Server 2012-2016

Följande PowerShell skapar ett redundanskluster för **Windows Server 2012-2016**. Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Följande PowerShell skapar ett redundanskluster för Windows Server 2019.  Mer information finns i bloggens [redundanskluster: kluster nätverks objekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).  Uppdatera skriptet med namnen på noderna (namnen på de virtuella datorerna) och en tillgänglig IP-adress från det virtuella Azure-nätverket:

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness"></a>Skapa ett moln vittne

Moln vittne är en ny typ av klusterkvorum som lagras i en Azure Storage Blob. Detta tar bort behovet av en separat virtuell dator som är värd för en vittnes resurs.

1. [Skapa ett moln vittne för redundansklustret](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en BLOB-behållare.

1. Spara åtkomst nycklarna och behållar-URL: en.

1. Konfigurera ett kvorumlogg för redundanskluster. Se [Konfigurera kvorumdisken i användar gränssnittet i användar gränssnittet](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) .


## <a name="step-4-test-cluster-failover"></a>Steg 4: testa redundanskluster

Testa redundansväxlingen av klustret. I Klusterhanteraren för växling vid fel högerklickar du på klustret > **fler åtgärder** > **Flytta kärn kluster resurs** > **Välj nod** och välj den andra noden i klustret. Flytta kärn kluster resursen till varje nod i klustret och flytta tillbaka den till den primära noden. Om du har möjlighet att flytta klustret till varje nod är du redo att installera SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/test-cluster-failover.png" alt-text="Testa redundanskluster genom att flytta kärn resursen till de andra noderna":::

## <a name="step-5-create-sql-server-fci"></a>Steg 5: skapa SQL Server FCI

När du har konfigurerat redundansklustret kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med RDP.

1. I **Klusterhanteraren för växling vid fel**kontrollerar du att alla kluster kärn resurser finns på den första virtuella datorn. Om det behövs flyttar du alla resurser till den virtuella datorn.

1. Leta upp installations mediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full`. Klicka på **Konfigurera**.

1. Klicka på **installation**i **SQL Server installations Center**.

1. Klicka på **ny SQL Server redundanskluster installationen**. Följ anvisningarna i guiden för att installera SQL Server FCI.

   FCI data kataloger måste finnas på Premium-filresursen. Ange den fullständiga sökvägen till resursen, i form av `\\storageaccountname.file.core.windows.net\filesharename\foldername`. En varning visas, och du får ett meddelande om att du har angett en fil server som data katalog. Detta är normalt. Se till att samma konto som du sparade fil resursen med är samma konto som den SQL Server tjänsten använder för att undvika eventuella problem. 

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-storage/use-file-share-as-data-directories.png" alt-text="Använd fil resurs som SQL data-kataloger":::

1. När du har slutfört guiden kommer installations programmet att installera en SQL Server FCI på den första noden.

1. När installations programmet har installerat FCI på den första noden ansluter du till den andra noden med RDP.

1. Öppna **installations Center för SQL Server**. Klicka på **installation**.

1. Klicka på **Lägg till nod i ett SQL Server redundanskluster**. Följ anvisningarna i guiden för att installera SQL Server och lägga till den här servern i FCI.

   >[!NOTE]
   >Om du använde en Azure Marketplace-Galleri avbildning med SQL Server, inkluderades SQL Server verktyg tillsammans med avbildningen. Om du inte använde avbildningen installerar du SQL Server verktyg separat. Se [Ladda ned SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-azure-load-balancer"></a>Steg 6: Skapa Azure Load Balancer

På Azure Virtual Machines använder kluster en belastningsutjämnare för att lagra en IP-adress som måste finnas på en klusternod i taget. I den här lösningen innehåller belastningsutjämnaren IP-adressen för SQL Server FCI.

[Skapa och konfigurera en Azure Load Balancer](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure Portal

Så här skapar du belastningsutjämnaren:

1. I Azure Portal går du till resurs gruppen med de virtuella datorerna.

1. Klicka på **+ Lägg till**. Sök på Marketplace efter **Load Balancer**. Klicka på **Load Balancer**.

1. Klicka på **Skapa**.

1. Konfigurera belastningsutjämnaren med:

   - **Prenumeration**: din Azure-prenumeration.
   - **Resurs grupp**: Använd samma resurs grupp som dina virtuella datorer.
   - **Namn**: ett namn som identifierar belastningsutjämnaren.
   - **Region**: Använd samma Azure-plats som dina virtuella datorer.
   - **Typ**: belastningsutjämnaren kan vara antingen offentlig eller privat. Det går att få åtkomst till en privat belastningsutjämnare i samma VNET. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via Internet använder du en offentlig belastningsutjämnare.
   - **SKU**: SKU för din belastningsutjämnare bör vara standard. 
   - **Virtual Network**: samma nätverk som de virtuella datorerna.
   - **Tilldelning av IP-adress**: IP-adresstilldelning ska vara statisk. 
   - **Privat IP-adress**: samma IP-adress som du tilldelade SQL Server FCI-kluster nätverks resurs.
   Se följande bild:

   ![CreateLoadBalancer](./media/virtual-machines-windows-portal-sql-create-failover-cluster/30-load-balancer-create.png)

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

   - **Namn**: ett namn för hälso avsökningen.
   - **Protokoll**: TCP.
   - **Port**: Ange till den port som du skapade i brand väggen för hälso avsökningen i [det här steget](#ports). I den här artikeln använder exemplet TCP-port `59999`.
   - **Intervall**: 5 sekunder.
   - **Tröskelvärde**för fel: 2 efterföljande fel.

1. Klicka på OK.

### <a name="set-load-balancing-rules"></a>Ange regler för belastnings utjämning

1. Klicka på **belastnings Utjämnings regler**på bladet Load Balancer (belastnings utjämning).

1. Klicka på **+ Lägg till**.

1. Ange parametrar för belastnings Utjämnings regler:

   - **Namn**: ett namn för reglerna för belastnings utjämning.
   - **IP-adress för klient**del: Använd IP-adressen för SQL Server FCI-kluster nätverks resurs.
   - **Port**: anges för SQL Server FCI TCP-port. Standard instans porten är 1433.
   - **Backend-port**: det här värdet använder samma port som **port** svärdet när du aktiverar **flytande IP (direkt Server retur)** .
   - **Backend-pool**: Använd det Server dels namn som du konfigurerade tidigare.
   - **Hälso avsökning**: Använd den hälso avsökning som du konfigurerade tidigare.
   - **Persistence för session**: ingen.
   - **Tids gräns för inaktivitet (minuter)** : 4.
   - **Flytande IP (direkt Server retur)** : aktive rad

1. Klicka på **OK**

## <a name="step-7-configure-cluster-for-probe"></a>Steg 7: Konfigurera kluster för avsökning

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

   - `<Cluster Network Name>`: kluster namn för Windows Server-redundanskluster för nätverket. I **Klusterhanteraren för växling vid fel** > **nätverk**högerklickar du på nätverket och klickar på **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** . 

   - `<SQL Server FCI IP Address Resource Name>`: SQL Server FCI IP-adressresurs. I **Klusterhanteraren för växling vid fel** > **roller**går du till SQL Server FCI-rollen, under **Server namn**, högerklickar på IP-adressresursen och klickar på **Egenskaper**. Det korrekta värdet finns under **namn** på fliken **Allmänt** . 

   - `<ILBIP>`: ILB IP-adress. Adressen är konfigurerad i Azure Portal som klient adress för ILB. Detta är också den SQL Server FCI-IP-adressen. Du hittar den i **Klusterhanteraren för växling vid fel** på samma egenskaps sida där du hittade `<SQL Server FCI IP Address Resource Name>`.  

   - `<nnnnn>`: är avsöknings porten som du konfigurerade i belastnings utjämningens hälso avsökning. En oanvänd TCP-port är giltig. 

>[!IMPORTANT]
>Nät masken för kluster parametern måste vara TCP IP-broadcast-adressen: `255.255.255.255`.

När du har ställt in kluster avsökningen kan du se alla kluster parametrar i PowerShell. Kör följande skript:

   ```powershell
   Get-ClusterResource $IPResourceName | Get-ClusterParameter 
  ```

## <a name="step-8-test-fci-failover"></a>Steg 8: testa FCI redundans

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

- [Windows kluster tekniker](/windows-server/failover-clustering/failover-clustering-overview)
- [SQL Server instanser av kluster för växling vid fel](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server).