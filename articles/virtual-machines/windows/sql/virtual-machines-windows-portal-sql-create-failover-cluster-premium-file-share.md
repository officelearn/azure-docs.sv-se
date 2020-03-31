---
title: SQL Server FCI med premiumfilresurs – Virtuella Azure-datorer
description: I den här artikeln beskrivs hur du skapar en SQL Server-redundansklusterininstans med hjälp av en premiumfilresurs på virtuella Azure-datorer.
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
ms.openlocfilehash: 9595ee87801fa4ce187a50197fc58d6c448eac24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303230"
---
# <a name="configure-a-sql-server-failover-cluster-instance-with-premium-file-share-on-azure-virtual-machines"></a>Konfigurera en SQL Server-redundansklusterin med premiumfilresurs på virtuella Azure-datorer

I den här artikeln beskrivs hur du skapar en SQL Server-redundansklusterinstans (FCI) på virtuella Azure-datorer med hjälp av en [premiumfilresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md).

Premium-filresurser är SSD-stödda filresurser med låg latens som stöds fullt ut för användning med Redundansklusterinstanser för SQL Server 2012 eller senare på Windows Server 2012 eller senare. Premium-filresurser ger dig större flexibilitet, så att du kan ändra storlek på och skala en filresurs utan avbrott.


## <a name="before-you-begin"></a>Innan du börjar

Det finns några saker du behöver veta och har på plats innan du börjar.

Du bör ha en operativ förståelse för dessa tekniker:

- [Windows-klustertekniker](/windows-server/failover-clustering/failover-clustering-overview)
- [Klusterinstanser för SQL Server-redundans](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

En sak att vara medveten om är att på en Azure IaaS VM redundans kluster, rekommenderar vi ett enda nätverkskort per server (klusternod) och ett enda undernät. Azure-nätverk har fysisk redundans som gör ytterligare nätverkskort och undernät onödiga på ett Azure IaaS VM-gästkluster. Klusterverifieringsrapporten varnar dig om att noderna endast kan nås i ett enda nätverk. Du kan ignorera den här varningen på Azure IaaS VM-redundanskluster.

Du bör också ha en allmän förståelse för dessa tekniker:

- [Azure premium-filresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md)
- [Azure-resursgrupper](../../../azure-resource-manager/management/manage-resource-groups-portal.md)

> [!IMPORTANT]
> För närvarande stöds SQL Server redundansklusterinstanser på virtuella Azure-datorer med [lightweight management-läget](virtual-machines-windows-sql-register-with-resource-provider.md#management-modes) i [SQL Server IaaS-agenttillägget](virtual-machines-windows-sql-server-agent-extension.md). Om du vill ändra från fullständigt tilläggsläge till lättvikt tar du bort **SQL Virtual Machine-resursen** för motsvarande virtuella datorer och registrerar dem sedan med SQL VM-resursprovidern i lättviktsläge. När du tar bort **RESURSEN FÖR virtuell SQL-dator** med Azure-portalen **avmarkerar du kryssrutan bredvid rätt virtuell dator**. Det fullständiga tillägget stöder funktioner som automatisk säkerhetskopiering, korrigering och avancerad portalhantering. Dessa funktioner fungerar inte för virtuella SQL-datorer när agenten har installerats om i lightweight management-läge.

Premium-filresurser tillhandahåller IOPS och kapaciteter som uppfyller behoven hos många arbetsbelastningar. För IO-intensiva arbetsbelastningar bör du överväga [SQL Server Redundansklusterinstanser med Storage Spaces Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md), baserat på hanterade premiumdiskar eller ultradiskar.  

Kontrollera IOPS-aktiviteten i din miljö och kontrollera att premiumfilresurser tillhandahåller de IOPS du behöver innan du startar en distribution eller migrering. Använd Diskräknare för Windows Performance Monitor för att övervaka den totala IOPS-disköverföringar/sekund och dataflöde (diskbyte/sekund) som krävs för SQL Server Data-, Log- och Temp DB-filer.

Många arbetsbelastningar har spruckna IO, så det är en bra idé att kontrollera under tunga användningsperioder och notera både den maximala IOPS och den genomsnittliga IOPS. Premium-filaktier tillhandahåller IOPS baserat på aktiens storlek. Premium-filresurser ger också gratis sprängning som gör att du kan spränga din IO för att tredubbla baslinjebeloppet i upp till en timme.

Mer information om prestanda för premiumfildelning finns i [Prestandanivåer för fildelning](https://docs.microsoft.com/azure/storage/files/storage-files-planning#storage-tiers).

### <a name="licensing-and-pricing"></a>Licensiering och prissättning

På virtuella Azure-datorer kan du licensiera SQL Server med pay-as-you-go (PAYG) eller byol-avbildningar (bring-your-own-license). Vilken typ av bild du väljer påverkar hur du debiteras.

Med användningsbaserad licensiering medför en REDUNDAnsklusterinstan (FCI) av SQL Server på virtuella Azure-datorer avgifter för alla noder i FCI, inklusive de passiva noderna. Mer information finns i [SQL Server Enterprise Virtual Machines Pricing](https://azure.microsoft.com/pricing/details/virtual-machines/sql-server-enterprise/).

Om du har Enterprise Agreement med Software Assurance kan du använda en gratis passiv FCI-nod för varje aktiv nod. Om du vill dra nytta av den här förmånen i Azure använder du BYOL VM-avbildningar och använder samma licens på både aktiva och passiva noder i FCI. Mer information finns i [Enterprise Agreement](https://www.microsoft.com/Licensing/licensing-programs/enterprise.aspx).

Om du vill jämföra användningsbaserad användning och BYOL-licensiering för SQL Server på virtuella Azure-datorer läser [du Komma igång med virtuella SQL-datorer](virtual-machines-windows-sql-server-iaas-overview.md#get-started-with-sql-vms).

Fullständig information om licensiering av SQL Server finns i [Prissättning](https://www.microsoft.com/sql-server/sql-server-2017-pricing).

### <a name="filestream"></a>FileStream

Filestream stöds inte för ett redundanskluster med en premiumfilresurs. Om du vill använda filestream distribuerar du klustret med hjälp av [Storage Spaces Direct](virtual-machines-windows-portal-sql-create-failover-cluster.md).

## <a name="prerequisites"></a>Krav

Innan du slutför stegen i den här artikeln bör du redan ha:

- En Microsoft Azure-prenumeration.
- En Windows-domän på virtuella Azure-datorer.
- Ett domänanvändarkonto som har behörighet att skapa objekt på både virtuella Azure-datorer och i Active Directory.
- Ett domänanvändarkonto som ska köra SQL Server-tjänsten och som du kan logga in på den virtuella datorn när du monterar filresursen.  
- Ett virtuellt Azure-nätverk och -undernät med tillräckligt med IP-adressutrymme för dessa komponenter:
   - Två virtuella datorer.
   - IP-adressen för redundansklustret.
   - En IP-adress för varje FCI.
- DNS konfigurerad i Azure-nätverket och pekar på domänkontrollanterna.
- En [premiumfilresurs](../../../storage/files/storage-how-to-create-premium-fileshare.md) som ska användas som klustrade enhet, baserat på databasens lagringskvot för dina datafiler.
- Om du använder Windows Server 2012 R2 och äldre behöver du en annan filresurs som ska användas som filresursvittne, eftersom molnvittnen stöds för Windows 2016 och nyare. Du kan använda en annan Azure-filresurs eller använda en filresurs på en separat virtuell dator. Om du ska använda en annan Azure-filresurs kan du montera den med samma process som för premiumfilresursen som används för din klustrade enhet. 

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

   >[!IMPORTANT]
   > När du har skapat den virtuella datorn tar du bort den förinstallerade fristående SQL Server-instansen. Du använder det förinstallerade SQL Server-mediet för att skapa SQL Server FCI när du har konfigurerat redundansklustret och premiumfilresursen som lagring.

   Du kan också använda Azure Marketplace-avbildningar som bara innehåller operativsystemet. Välj en **Datacenteravbildning för Windows Server 2016** och installera SQL Server FCI när du har konfigurerat redundansklustret och premiumfilresursen som lagring. Den här avbildningen innehåller inte SQL Server-installationsmedia. Placera SQL Server-installationsmediet på en plats där du kan köra det för varje server.

1. När Azure har skapat dina virtuella datorer ansluter du till var och en med hjälp av RDP.

   När du först ansluter till en virtuell dator med RDP frågar en fråga om du vill att datorn ska kunna identifieras i nätverket. Välj **Ja**.

1. Om du använder någon av de SQL Server-baserade avbildningarna för virtuella datorer tar du bort SQL Server-instansen.

   1. Högerklicka på Microsoft SQL **Server 201_ (64-bitars) i** **Program och funktioner**och välj **Avinstallera/ändra**.
   1. Välj **Ta bort**.
   1. Markera standardinstansen.
   1. Ta bort alla funktioner under **Database Engine Services**. Ta inte bort **delade funktioner**. Du ser något i stil med följande skärmdump:

        ![Välj funktioner](./media/virtual-machines-windows-portal-sql-create-failover-cluster/03-remove-features.png)

   1. Välj **Nästa**och välj sedan **Ta bort**.

1. <span id="ports"> </span> Öppna brandväggsportarna.  

   Öppna dessa portar i Windows-brandväggen på varje virtuell dator:

   | Syfte | TCP-port | Anteckningar
   | ------ | ------ | ------
   | SQL Server | 1433 | Normal port för standardinstanser av SQL Server. Om du har använt en bild från galleriet öppnas den här porten automatiskt.
   | Hälsoavsökning | 59999 | Alla öppna TCP-portar. I ett senare steg konfigurerar du [hälsoavsökningen](#probe) för belastningsutjämnaren och klustret så att den här porten används.
   | Filresurs | 445 | Port som används av fildelningstjänsten.

1. [Lägg till de virtuella datorerna i din befintliga domän](virtual-machines-windows-portal-sql-availability-group-prereq.md#joinDomain).

När du har skapat och konfigurerat de virtuella datorerna kan du konfigurera premiumfilresursen.

## <a name="step-2-mount-the-premium-file-share"></a>Steg 2: Montera premiumfilresursen

1. Logga in på [Azure-portalen](https://portal.azure.com) och gå till ditt lagringskonto.
1. Gå till **Filresurser** under **Filtjänst** och välj den premiumfilresurs som du vill använda för SQL-lagringen.
1. Välj **Anslut** om du vill visa anslutningssträngen för filresursen.
1. Markera den enhetsbeteckning som du vill använda i listrutan och kopiera sedan båda kodblocken till Anteckningar.


   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/premium-file-storage-commands.png" alt-text="Kopiera båda PowerShell-kommandon från filresursanslutningsportalen":::

1. Använd RDP för att ansluta till DEN VIRTUELLA SQL Server-datorn med det konto som DIN SQL Server FCI kommer att använda för tjänstkontot.
1. Öppna en administrativ PowerShell-kommandokonsol.
1. Kör de kommandon som du sparade tidigare när du arbetade i portalen.
1. Gå till resursen med hjälp av utforskaren eller dialogrutan **Kör** (Windows-tangenten + r). Använd nätverkssökvägen `\\storageaccountname.file.core.windows.net\filesharename`. Till exempel, `\\sqlvmstorageaccount.file.core.windows.net\sqlpremiumfileshare`

1. Skapa minst en mapp på den nyligen anslutna filresursen för att placera dina SQL Data-filer i.
1. Upprepa dessa steg på varje VIRTUELL SQL Server som ska delta i klustret.

  > [!IMPORTANT]
  > - Överväg att använda en separat filresurs för säkerhetskopieringsfiler för att spara IOPS och utrymmeskapaciteten för den här resursen för data- och loggfiler. Du kan använda antingen en premium- eller standardfilresurs för säkerhetskopieringsfiler.
  > - Om du använder Windows 2012 R2 och äldre följer du samma steg för att montera filresursen som du ska använda som filresursvittne. 

## <a name="step-3-configure-the-failover-cluster"></a>Steg 3: Konfigurera redundansklustret

Nästa steg är att konfigurera redundansklustret. I det här steget ska du slutföra följande understeg:

1. Lägg till funktionen Kluster för Windows Server-redundans.
1. Verifiera klustret.
1. Skapa redundansklustret.
1. Skapa molnvittnet (för Windows Server 2016 och nyare) eller filresursvittnet (för Windows Server 2012 R2 och äldre).


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

### <a name="validate-the-cluster"></a>Verifiera klustret

Validera klustret i användargränssnittet eller med PowerShell.

Så här validerar du klustret med hjälp av användargränssnittet:

1. Under **Serverhanteraren**väljer du **Verktyg**och väljer sedan **Redundansklusterhanteraren**.
1. Under **Redundansklusterhanterare**väljer du **Åtgärd**och väljer sedan **Validera konfiguration**.
1. Välj **Nästa**.
1. Under **Välj servrar eller ett kluster**anger du namnen på båda virtuella datorerna.
1. Under **Testalternativ**väljer du **Kör endast tester som jag väljer**. Välj **Nästa**.
1. Under **Testmarkering**väljer du alla tester utom **Lagring** och **Lagringsutrymme Direkt**, som visas här:

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/cluster-validation.png" alt-text="Välj klustervalideringstester":::

1. Välj **Nästa**.
1. Under **Bekräftelse**väljer du **Nästa**.

**Guiden Validera en konfiguration** kör valideringstesterna.

Om du vill validera klustret med PowerShell kör du följande skript från en powershell-administratörssession på en av de virtuella datorerna:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
   ```

När du har validerat klustret skapar du redundansklustret.

### <a name="create-the-failover-cluster"></a>Skapa redundansklustret

Om du vill skapa redundansklustret behöver du:
- Namnen på de virtuella datorer som blir klusternoder.
- Ett namn på redundansklustret
- En IP-adress för redundansklustret. Du kan använda en IP-adress som inte används i samma virtuella Azure-nätverk och undernät som klusternoder.

#### <a name="windows-server-2012-through-windows-server-2016"></a>Windows Server 2012 via Windows Server 2016

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2012 via Windows Server 2016. Uppdatera skriptet med namnen på noderna (namn på den virtuella datorn) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

#### <a name="windows-server-2019"></a>Windows Server 2019

Följande PowerShell-skript skapar ett redundanskluster för Windows Server 2019. Mer information finns i [Redundanskluster: Klusternätverksobjekt](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97). Uppdatera skriptet med namnen på noderna (namn på den virtuella datorn) och en tillgänglig IP-adress från det virtuella Azure-nätverket.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```


### <a name="create-a-cloud-witness-win-2016-"></a>Skapa ett molnvittne (Vinn 2016 +)

Om du använder Windows Server 2016 och senare måste du skapa ett molnvittne. Cloud Witness är en ny typ av klusterkvorumvittne som lagras i en Azure storage-blob. Detta tar bort behovet av en separat virtuell dator som är värd för en vittnesresurs eller med hjälp av en separat filresurs.

1. [Skapa ett molnvittne för redundansklustret](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness).

1. Skapa en blob-behållare.

1. Spara åtkomstnycklarna och behållar-URL:en.

### <a name="configure-quorum"></a>Konfigurera kvorum 

För Windows Server 2016 och senare konfigurerar du klustret så att det använder det molnvittne som du just skapade. Följ alla steg [Konfigurera kvorumvittnet i användargränssnittet](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness).

För Windows Server 2012 R2 och äldre följer du samma steg i [Konfigurera kvorumvittnet i användargränssnittet,](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness#to-configure-cloud-witness-as-a-quorum-witness) men välj alternativet **Konfigurera ett filresursvittne** på sidan **Välj kvorumvittnet.** Ange den filresurs som du har allokerat för att vara filresursvittnet, oavsett om det är en som du har konfigurerat på en separat virtuell dator eller monterad från Azure. 


## <a name="step-4-test-cluster-failover"></a>Steg 4: Testkluster redundans

Testa redundans för klustret. Högerklicka på **klustret i Redundansklusterhanteraren**och välj **Fler åtgärder** > Flytta**noden****För kärnklusterresurs** > och välj sedan den andra noden i klustret. Flytta kärnklusterresursen till varje nod i klustret och flytta sedan tillbaka den till den primära noden. Om du kan flytta klustret till varje nod är du redo att installera SQL Server.  

:::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/test-cluster-failover.png" alt-text="Testa klusterväxling genom att flytta kärnresursen till de andra noderna":::

## <a name="step-5-create-the-sql-server-fci"></a>Steg 5: Skapa SQL Server FCI

När du har konfigurerat redundansklustret kan du skapa SQL Server FCI.

1. Anslut till den första virtuella datorn med hjälp av RDP.

1. I **Redundansklusterhanteraren**kontrollerar du att alla kärnklusterresurser finns på den första virtuella datorn. Om du behöver flyttar du alla resurser till den här virtuella datorn.

1. Leta reda på installationsmediet. Om den virtuella datorn använder en av Azure Marketplace-avbildningarna finns mediet på `C:\SQLServer_<version number>_Full`. Välj **Installation .**

1. Välj **Installation**i **SQL Server-installationscenter**.

1. Välj **Ny sql server-redundansklusterinstallation**. Installera SQL Server FCI genom att följa instruktionerna i guiden.

   FCI-datakatalogerna måste finnas på premiumfilresursen. Ange hela sökvägen för resursen `\\storageaccountname.file.core.windows.net\filesharename\foldername`i det här formuläret: . En varning visas som talar om att du har angett en filserver som datakatalog. Denna varning är väntad. Kontrollera att användarkontot som du rdp'd till den virtuella datorn med när du sparade filresursen är samma konto som SQL Server-tjänsten använder för att undvika eventuella fel.

   :::image type="content" source="media/virtual-machines-windows-portal-sql-create-failover-cluster-premium-file-share/use-file-share-as-data-directories.png" alt-text="Använda filresurs som SQL-datakataloger":::

1. När du har slutfört stegen i guiden installeras en SQL Server FCI på den första noden.

1. När installationen av FCI:en har installerats på den första noden ansluter du till den andra noden med hjälp av RDP.

1. Öppna **SQL Server-installationscentret**. Välj **Installation**.

1. Välj **Lägg till nod i ett SQL Server-redundanskluster**. Installera SQL Server och lägga till servern i FCI.Follow the instructions in the wizard to install SQL Server and add the server to the FCI.

   >[!NOTE]
   >Om du använde en Avbildning i Azure Marketplace-galleriet med SQL Server inkluderades SQL Server-verktygen i avbildningen. Om du inte använde någon av dessa avbildningar installerar du SQL Server-verktygen separat. Se [Hämta SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="step-6-create-the-azure-load-balancer"></a>Steg 6: Skapa Azure-belastningsutjämnaren

På virtuella Azure-datorer använder kluster en belastningsutjämnare för att hålla en IP-adress som måste finnas på en klusternod i taget. I den här lösningen innehåller belastningsutjämnaren IP-adressen för SQL Server FCI.

Mer information finns i [Skapa och konfigurera en Azure-belastningsutjämnare](virtual-machines-windows-portal-sql-availability-group-tutorial.md#configure-internal-load-balancer).

### <a name="create-the-load-balancer-in-the-azure-portal"></a>Skapa belastningsutjämnaren i Azure-portalen

Så här skapar du belastningsutjämnaren:

1. Gå till resursgruppen som innehåller de virtuella datorerna i Azure-portalen.

1. Välj **Lägg till**. Sök på Azure Marketplace efter **belastningsutjämnare**. Välj **Belastningsutjämnare**.

1. Välj **Skapa**.

1. Ställ in belastningsutjämnaren med hjälp av följande värden:

   - **Prenumeration**: Din Azure-prenumeration.
   - **Resursgrupp**: Resursgruppen som innehåller dina virtuella datorer.
   - **Namn**: Ett namn som identifierar belastningsutjämnaren.
   - **Region**: Den Azure-plats som innehåller dina virtuella datorer.
   - **Typ**: Antingen offentliga eller privata. En privat belastningsutjämnare kan nås inifrån det virtuella nätverket. De flesta Azure-program kan använda en privat belastningsutjämnare. Om ditt program behöver åtkomst till SQL Server direkt via internet använder du en offentlig belastningsutjämnare.
   - **SKU**: Standard.
   - **Virtuellt nätverk:** Samma nätverk som de virtuella datorerna.
   - **IP-adresstilldelning**: Statisk. 
   - **Privat IP-adress:** DEN IP-adress som du har tilldelat till SQL Server FCI-klusternätverksresursen.

   Följande bild visar användargränssnittet **Skapa belastningsutjämnare:**

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

1. På **Bladet Lägg till hälsoavsökning** <span id="probe"> </span> ställer du in följande parametrar för hälsoavsökning.

   - **Namn**: Ett namn på hälsoavsökningen.
   - **Protokoll**: TCP.
   - **Port**: Porten som du skapade i brandväggen för hälsoavsökningen i [det här steget](#ports). I den här artikeln använder exemplet `59999`TCP-port .
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

## <a name="step-7-configure-the-cluster-for-the-probe"></a>Steg 7: Konfigurera klustret för sonden

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

## <a name="step-8-test-fci-failover"></a>Steg 8: Testa FCI-redundans

Testa redundans för FCI för att validera klusterfunktioner. Utför följande steg:

1. Anslut till en av SQL Server FCI-klusternoderna med hjälp av RDP.

1. Öppna **Redundansklusterhanteraren**. Välj **Roller**. Observera vilken nod som äger SQL Server FCI-rollen.

1. Högerklicka på SQL Server FCI-rollen.

1. Välj **Flytta**och välj sedan **Bästa möjliga nod**.

**Redundansklusterhanteraren** visar rollen och dess resurser kopplas från. Resurserna flyttas sedan och kommer tillbaka online i den andra noden.

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

- [Windows-klustertekniker](/windows-server/failover-clustering/failover-clustering-overview)
- [Klusterinstanser för SQL Server-redundans](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
