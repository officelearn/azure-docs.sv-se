---
title: Linux compute virtuella datorer i ett kluster med HPC Pack | Microsoft Docs
description: Lär dig hur du skapar och använder ett HPC Pack-kluster i Azure för Linux högpresterande datorbearbetning (HPC) arbetsbelastningar
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 4d080fdd-5ffe-4f54-a78d-4c818f6eb3fb
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 10/12/2016
ms.author: danlep
ms.openlocfilehash: 57ad5d5d2e7e068f47d51408527f1f7553917279
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Kom igång med beräkningsnoder för Linux i ett HPC Pack-kluster i Azure
Konfigurera en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) kluster i Azure som innehåller en huvudnod som kör Windows Server och flera compute-noder som kör ett Linux-distribution som stöds. Utforska alternativ för att flytta data mellan noder för Linux och Windows huvudnod i klustret. Lär dig mer om att skicka Linux HPC-jobb till klustret.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Följande diagram visar HPC Pack kluster du skapar och arbetar med på en hög nivå.

![HPC Pack kluster med noder för Linux][scenario]

Andra alternativ att köra Linux HPC arbetsbelastningar i Azure finns [tekniska resurser för batch- och högpresterande datorbearbetning](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Distribuera ett HPC Pack kluster med beräkningsnoder för Linux
Den här artikeln lär du två alternativ för att distribuera ett HPC Pack kluster i Azure med Linux compute-noder. Båda metoderna kan du använda en Marketplace-avbildning av Windows Server med HPC Pack för att skapa huvudnoden. 

* **Azure Resource Manager-mall** -använda en mall från Azure Marketplace, eller en mall för Snabbstart från gemenskapen, för att automatisera skapandet av klustret i Resource Manager-distributionsmodellen. Till exempel den [HPC Pack kluster för Linux arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) mallen i Azure Marketplace skapar en komplett HPC Pack klustret infrastruktur för Linux HPC arbetsbelastningar.
* **PowerShell-skriptet** -användning i [Microsoft HPC Pack IaaS distributionsskriptet](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**ny HpcIaaSCluster.ps1**) att automatisera en distribution för hela klustret i den klassiska distributionsmodellen. Azure PowerShell-skript använder ett HPC Pack VM-avbildning i Azure Marketplace för snabb distribution och ger en omfattande uppsättning konfigurationsparametrar för att distribuera datornoderna Linux.

Mer information om HPC Pack distributionsalternativ för kluster i Azure finns [alternativ för att skapa och hantera högpresterande datorbearbetning (HPC) kluster i Azure with Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Krav
* **Azure-prenumeration** -du kan använda en prenumeration i Azure Global eller Azure Kina service. Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.
* **Kärnor kvoten** – du kan behöva öka kvoten för kärnor, särskilt om du väljer att distribuera flera klusternoder med flera kärnor VM-storlekar. Om du vill öka en kvot öppnar du en online-kund supportbegäran utan kostnad.
* **Linux-distributioner** -för närvarande HPC Pack stöder följande Linux-distributioner för compute-noder. Du kan använda dessa distributioner Marketplace-versioner där det är tillgängligt eller ange egna.
  
  * **CentOS-baserade**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12 SLES 12 (Premium) SLES 12 SP1, SLES 12 SP1 (Premium) SLES 12 för HPC SLES 12 för HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Ange en SUSE Linux Enterprise Server 12 HPC eller CentOS-baserade HPC-avbildning från Azure Marketplace för att använda RDMA-Azure-nätverk med en storlek på RDMA-kompatibla Virtuella datorer. Mer information finns i [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Ytterligare krav för att distribuera klustret med hjälp av distributionsskriptet HPC Pack IaaS:

* **Klientdatorn** -behöver du en Windows-baserad klient-dator för att köra skriptet för distribution av klustret.
* **Azure PowerShell** - [installera och konfigurera Azure PowerShell](/powershell/azure/overview) (version 0.8.10 eller senare) på din klientdator.
* **HPC Pack IaaS distributionsskriptet** – hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Du kan kontrollera vilken version av skriptet genom att köra `.\New-HPCIaaSCluster.ps1 –Version`. Den här artikeln är baserat på version 4.4.1 eller senare av skriptet.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Distributionsalternativ 1. Använd en Resource Manager-mall
1. Gå till den [HPC Pack kluster för Linux arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) mall i Azure Marketplace och klicka på **distribuera**.
2. Granska informationen i Azure-portalen och klicka sedan på **skapa**.
   
    ![Skapa Portal][portal]
3. På den **grunderna** bladet anger du ett namn för klustret, vilket även namnen huvudnod VM. Du kan välja en befintlig resursgrupp eller skapa en grupp för distribution på en plats som är tillgängliga för dig. Platsen påverkar tillgängligheten för vissa VM-storlekar och andra Azure-tjänster (se [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/)).
4. På den **Head noden inställningar** bladet för en första distribution, kan du oftast acceptera standardinställningarna. 
   
   > [!NOTE]
   > Den **efter konfigurationsskript URL** är en valfri inställning och anger ett offentligt tillgängliga Windows PowerShell-skript som du vill köra på huvudnoden VM när den körs. 
   > 
   > 
5. På den **Compute-nod inställningar** bladet Välj en namnkonflikt mönstret för noder, antal och storleken på noderna och Linux-distribution ska distribueras.
6. På den **infrastrukturinställningar** bladet ange namn för virtuellt nätverk och Active Directory domän, domän och VM-administratörsbehörighet och ett namngivningsmönster för storage-konton.
   
   > [!NOTE]
   > HPC Pack använder Active Directory-domänen för att autentisera användare för klustret. 
   > 
   > 
7. När verifieringstesterna köra och granska av användningsvillkoren, klickar du på **inköp**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Distributionsalternativ 2. Använd skript för IaaS-distribution
Följande är ytterligare krav för att distribuera klustret med hjälp av distributionsskriptet HPC Pack IaaS:

* **Klientdatorn** -behöver du en Windows-baserad klient-dator för att köra skriptet för distribution av klustret.
* **Azure PowerShell** - [installera och konfigurera Azure PowerShell](/powershell/azure/overview) (version 0.8.10 eller senare) på din klientdator.
* **HPC Pack IaaS distributionsskriptet** – hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Du kan kontrollera vilken version av skriptet genom att köra `.\New-HPCIaaSCluster.ps1 –Version`. Den här artikeln är baserat på version 4.4.1 eller senare av skriptet.

**XML-konfigurationsfilen**

HPC Pack IaaS-distributionsskriptet använder en XML-konfigurationsfil som indata för att beskriva HPC-kluster. Följande exempel konfigurationsfil anger ett mindre kluster som består av en huvudnod i HPC Pack och två storlek A7 CentOS 7.0 Linux compute-noder. 

Ändra den här filen behövs för din miljö och önskade klusterkonfigurationen och spara den med ett namn, till exempel HPCDemoConfig.xml. Till exempel måste ange namnet på din prenumeration och en unik lagringskontonamnet och molntjänster tjänstnamn. Dessutom kanske du vill välja en annan Linux avbildning som stöds för compute-noder. Mer information om elementen i konfigurationsfilen finns i filen Manual.rtf i mappen skript och [skapa ett HPC-kluster med HPC Pack IaaS-distributionsskriptet](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>allvhdsje</StorageAccount>
  </Subscription>
  <Location>Japan East</Location>  
  <VNet>
    <VNetName>centos7rdmavnetje</VNetName>
    <SubnetName>CentOS7RDMACluster</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>CentOS7RDMA-HN</VMName>
    <ServiceName>centos7rdma-je</ServiceName>
  <VMSize>ExtraLarge</VMSize>
  <EnableRESTAPI />
  <EnableWebPortal />
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>CentOS7RDMA-LN%1%</VMNamePattern>
    <ServiceName>centos7rdma-je</ServiceName>
    <VMSize>A7</VMSize>
    <NodeCount>2</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325</ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```

**Att köra skriptet HPC Pack IaaS-distribution**

1. Öppna Windows PowerShell på datorn som administratör.
2. Ändra katalogen till mappen där skriptet är installerat (E:\IaaSClusterScript i det här exemplet).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
3. Kör följande kommando för att distribuera HPC Pack-kluster. Det här exemplet förutsätter att konfigurationsfilen finns i E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Eftersom den **AdminPassword** har inte angetts i kommandot ovan uppmanas du att ange lösenord för användaren *MyAdminName*.
   
    b. Skriptet startar sedan att verifiera konfigurationsfilen. Det kan ta upp till flera minuter beroende på nätverksanslutningen.
   
    ![Verifiering][validate]
   
    c. När verifieringar skicka visar skriptet klusterresurserna som du vill skapa. Ange *Y* att fortsätta.
   
    ![Resurser][resources]
   
    d. Skriptet börjar distribuera HPC Pack klustret och slutför konfigurationen utan ytterligare manuella steg. Skriptet kan köras i flera minuter.
   
    ![Distribuera][deploy]
   
   > [!NOTE]
   > I det här exemplet skriptet genererar en loggfil automatiskt eftersom den **- LogFile** parameter har inte angetts. Loggarna inte är skriven i realtid men har samlats in i slutet av verifieringen och distribution. Vissa loggar går förlorade om PowerShell processen stoppas när skriptet körs.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Ansluta till huvudnoden
När du har distribuerat HPC Pack klustret i Azure, [ansluta med Remote Desktop](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) till huvudnod VM med domänen autentiseringsuppgifter du anges när du distribuerade klustret (till exempel *hpc\\clusteradmin*). Du kan hantera klustret från huvudnod.

Starta HPC Cluster Manager för att kontrollera status för HPC Pack klustret på noden head. Du kan hantera och övervaka Linux compute-noder på samma sätt som du arbetar med Windows compute-noder. Se exempelvis Linux-noder som anges i **resurshantering** (dessa noder distribueras med den **LinuxNode** mall).

![Hantering][management]

Du också se Linux-noder i den **termisk karta** vyn.

![Termisk karta][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Hur du flyttar data i ett kluster med noder som Linux
Du har flera alternativ för att flytta data mellan noder för Linux och Windows huvudnod i klustret. Här följer tre vanliga metoder som beskrivs i detalj i följande avsnitt:

* **Azure File** -visar den hantera SMB-filresursen som lagrar datafiler i Azure-lagring. Windows-noderna och Linux-noder kan montera en filresurs på Azure som en enhet eller mapp på samma gång, även om de har distribuerats i olika virtuella nätverk.
* **Huvudnod SMB dela** -monterar en standard Windows delad mapp för huvudnoden på Linux-noder.
* **HEAD nod NFS-servern** -är en lösning för fildelning för en blandad miljö för Windows och Linux.

### <a name="azure-file-storage"></a>Azure File storage
Den [Azure File](https://azure.microsoft.com/services/storage/files/) service exponerar filresurser med hjälp av SMB 2.1 standardprotokollet. Virtuella Azure-datorer och molntjänster kan dela fildata över programkomponenter via monterade resurser och lokala program kan komma åt fildata på en resurs via File storage API. 

Detaljerade anvisningar om hur du skapar en filresurs på Azure och montera på huvudnoden finns [Kom igång med Azure File storage i Windows](../../../storage/files/storage-how-to-use-files-windows.md). Montera filresursen Azure på Linux-noder, se [använda Azure File storage med Linux](../../../storage/files/storage-how-to-use-files-linux.md). Om du vill konfigurera spara anslutningar, se [Persisting anslutningar till Microsoft Azure-filer](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

I följande exempel skapar du en filresurs på Azure på ett lagringskonto. Om du vill ansluta till resursen på huvudnoden, öppna en kommandotolk och ange följande kommandon:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

I det här exemplet allvhdsje är namnet på ditt lagringskonto, storageaccountkey är din lagringskontonyckel och rdma är Azure File resursnamnet. Azure-filresursen är monterad som Z: på huvudnoden.

Montera filresursen Azure på Linux-noder, kör en **clusrun** på huvudnoden. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  är HPC Pack användbart att utföra administrativa uppgifter på flera noder. (Se även [Clusrun för Linux-noder](#Clusrun-for-Linux-nodes) i den här artikeln.)

Öppna Windows PowerShell-fönstret och ange följande kommandon:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Det första kommandot skapar en mapp med namnet /rdma på alla noder i gruppen LinuxNodes. Det andra kommandot monterar Azure File share allvhdsjw.file.core.windows.net/rdma till mappen /rdma med dir och filen bitar till 777. I det andra kommandot allvhdsje är namnet på ditt lagringskonto och storageaccountkey är din lagringskontonyckel.

> [!NOTE]
> Den ”\`” symbol i det andra kommandot är en symbolen för PowerShell. ”\`”, innebär att ””, (kommatecken tecken) är en del av kommandot.
> 
> 

### <a name="head-node-share"></a>Huvudnod resursen
Du kan också montera en delad mapp för huvudnoden på Linux-noder. En resurs som innehåller det enklaste sättet att dela filer, men huvudnoden och alla Linux-noder måste distribueras i samma virtuella nätverk. Här följer stegen.

1. Skapa en mapp på huvudnoden och dela den för alla med läs-/ skrivbehörighet. Till exempel dela D:\OpenFOAM på huvudnoden som \\CentOS7RDMA HN\OpenFOAM. Här är CentOS7RDMA HN värdnamnet för huvudnoden.
   
    ![Filresursbehörigheter][fileshareperms]
   
    ![Fildelning][filesharing]
2. Öppna Windows PowerShell-fönstret och kör följande kommandon:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Det första kommandot skapar en mapp med namnet /openfoam på alla noder i gruppen LinuxNodes. Det andra kommandot monterar delade mappen //CentOS7RDMA-HN/OpenFOAM till mappen med dir och filen bitar till 777. Användarnamnet och lösenordet i kommandot ska användarnamn och lösenord för en kluster-användare i huvudnod. (Se [Lägg till eller ta bort klustret användare](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> Den ”\`” symbol i det andra kommandot är en symbolen för PowerShell. ”\`”, innebär att ””, (kommatecken tecken) är en del av kommandot.
> 
> 

### <a name="nfs-server"></a>NFS-server
NFS-tjänsten kan du dela och flytta filer mellan datorer som kör operativsystemet Windows Server 2012 med SMB-protokollet och Linux-baserade datorer med hjälp av NFS-protokollet. NFS-servern och alla andra noder måste distribueras i samma virtuella nätverk. Det ger bättre kompatibilitet med Linux-noder jämfört med en SMB-resurs. Till exempel stöder filen länkar.

1. Om du vill installera och konfigurera NFS-servern, följer du stegen i [servern för System första nätverksfilresurs slutpunkt till slutpunkt](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Till exempel skapa en NFS-resurs med namnet nfs med följande egenskaper:
   
    ![NFS-auktorisering][nfsauth]
   
    ![NFS-resursbehörigheter][nfsshare]
   
    ![NFS-NTFS-behörigheter][nfsperm]
   
    ![Egenskaper för NFS][nfsmanage]
2. Öppna Windows PowerShell-fönstret och kör följande kommandon:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   Det första kommandot skapar en mapp med namnet /nfsshared på alla noder i gruppen LinuxNodes. Det andra kommandot monterar NFS-resurs CentOS7RDMA HN: / nfs till mappen. Här CentOS7RDMA HN: / nfs är fjärrsökväg NFS-resursen.

## <a name="how-to-submit-jobs"></a>Hur du skickar in jobb
Det finns flera sätt att skicka jobb till klustret HPC Pack:

* HPC Cluster Manager eller HPC Job Manager GUI
* HPC-webbportalens
* REST API

Skicka jobb till klustret i Azure via HPC Pack Gränssnittsbaserade verktyg och HPC-webbportalens är desamma som för Windows compute-noder. Se [HPC Pack Job Manager](https://technet.microsoft.com/library/ff919691.aspx) och [så att skicka jobb från en klientdator för lokala](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

För att skicka jobb via REST-API, referera till [skapa och skicka jobb med hjälp av REST-API i Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). För att skicka jobb från en Linux-klient, även gå till Python-exempel i den [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun för Linux-noder
HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) verktyget kan användas för att köra kommandon på Linux-noder antingen via en kommandotolk eller HPC Cluster Manager. Följande är några grundläggande exempel.

* Visa aktuella användarnamn på alla noder i klustret.
  
    ```command
    clusrun whoami
    ```
* Installera den **gdb** felsökningsverktyget med **yum** på alla noder i linuxnodes gruppen och starta sedan om noderna efter 10 minuter.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Skapa ett kommandoskript som visar varje tal 1 till 10 för en sekund på varje Linux-nod i klustret, kör den och visa utdata från noderna omedelbart.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Du kan behöva använda vissa escape-tecken i **clusrun** kommandon. I det här exemplet visas när du använder ^ i en kommandotolk för att undanta den ”>” symbol.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Prova att skala upp kluster till ett större antal noder eller körs en Linux-arbetsbelastning i klustret. Ett exempel finns [kör NAMD with Microsoft HPC Pack på Linux compute-noder i Azure](hpcpack-cluster-namd.md).
* Försök med ett kluster med [RDMA-kompatibla, beräkningsintensiva VMs](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) att köra MPI arbetsbelastningar. Ett exempel finns [kör OpenFOAM with Microsoft HPC Pack på en Linux RDMA-klustret i Azure](hpcpack-cluster-openfoam.md).
* Om du vill arbeta med Linux-noder i ett lokalt HPC Pack kluster finns i [TechNet vägledning](https://technet.microsoft.com/library/mt595803.aspx).

<!--Image references-->
[scenario]:media/hpcpack-cluster/scenario.png
[portal]:media/hpcpack-cluster/portal.png
[validate]:media/hpcpack-cluster/validate.png
[resources]:media/hpcpack-cluster/resources.png
[deploy]:media/hpcpack-cluster/deploy.png
[management]:media/hpcpack-cluster/management.png
[heatmap]:media/hpcpack-cluster/heatmap.png
[fileshareperms]:media/hpcpack-cluster/fileshare1.png
[filesharing]:media/hpcpack-cluster/fileshare2.png
[nfsauth]:media/hpcpack-cluster/nfsauth.png
[nfsshare]:media/hpcpack-cluster/nfsshare.png
[nfsperm]:media/hpcpack-cluster/nfsperm.png
[nfsmanage]:media/hpcpack-cluster/nfsmanage.png
