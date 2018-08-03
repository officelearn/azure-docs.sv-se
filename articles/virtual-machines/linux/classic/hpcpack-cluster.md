---
title: Linux compute virtuella datorer i ett HPC Pack-kluster | Microsoft Docs
description: Lär dig hur du skapar och använder ett HPC Pack-kluster i Azure för Linux med höga prestanda (HPC) arbetsbelastningar
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
ms.openlocfilehash: 2d4091d8ad6a778405ee6bb916c399e0b144f21d
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39441535"
---
# <a name="get-started-with-linux-compute-nodes-in-an-hpc-pack-cluster-in-azure"></a>Kom igång med beräkningsnoder för Linux i ett HPC Pack-kluster i Azure
Konfigurera en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029.aspx) kluster i Azure som innehåller en huvudnod som kör Windows Server och flera beräkningsnoder som kör en Linux-distribution som stöds. Utforska alternativ för att flytta data mellan noder för Linux och Windows huvudnod i klustret. Lär dig hur du skickar in Linux HPC-jobb i klustret.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Följande diagram visar HPC Pack-kluster du skapar och arbetar med på en hög nivå.

![HPC Pack-kluster med Linux-noder][scenario]

Andra alternativ för att köra Linux HPC-arbetsbelastningar i Azure, se [tekniska resurser för batch och databehandling med höga prestanda](../../../batch/big-compute-resources.md).

## <a name="deploy-an-hpc-pack-cluster-with-linux-compute-nodes"></a>Distribuera ett HPC Pack-kluster med Linux-beräkningsnoder
Den här artikeln visar du två alternativ för att distribuera ett HPC Pack-kluster i Azure med Linux-beräkningsnoder. Båda metoderna använda en Marketplace-avbildning av Windows Server med HPC Pack för att skapa huvudnoden. 

* **Azure Resource Manager-mall** -använda en mall från Azure Marketplace eller en snabbstartsmall från gruppen, för att automatisera skapandet av klustret i Resource Manager-distributionsmodellen. Till exempel den [HPC Pack-kluster för Linux-arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) mallen i Azure Marketplace skapar en komplett infrastruktur för HPC Pack-kluster för Linux HPC arbetsbelastningar.
* **PowerShell-skript** – Använd den [Microsoft HPC Pack IaaS-distributionsskriptet](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) (**New-HpcIaaSCluster.ps1**) att automatisera distributionen av en hela klustret i den klassiska distributionsmodellen. Den här Azure PowerShell-skript använder ett HPC Pack VM-avbildning på Azure Marketplace för snabb distribution och ger en omfattande uppsättning konfigurationsparametrar för att distribuera Linux-beräkningsnoder.

Mer information om distributionsalternativen för HPC Pack-kluster i Azure finns i [alternativ för att skapa och hantera en databehandling med höga prestanda (HPC) kluster i Azure med Microsoft HPC Pack](../hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

### <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration** – du kan använda en prenumeration i tjänsten Azure Global eller Azure Kina. Om du inte har ett konto kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/pricing/free-trial/) på bara några minuter.
* **Kärnkvot** – du kan behöva öka kärnkvoten kärnor, särskilt om du väljer att distribuera flera klusternoder med flera kärnor VM-storlekar. Öppna en kundsupportärende utan kostnad för att öka en kvot.
* **Linux-distributioner** – för närvarande HPC Pack stöder följande Linux-distributioner för beräkningsnoder. Du kan använda Marketplace versioner av dessa distributioner där det är tillgängligt eller ange dina egna.
  
  * **CentOS-baserade**: 6.5, 6.6, 6.7, 7.0, 7.1, 7.2, 6.5 HPC, 7.1 HPC
  * **Red Hat Enterprise Linux**: 6.7, 6.8, 7.2
  * **SUSE Linux Enterprise Server**: SLES 12, SLES 12 (Premium), SLES 12 SP1, SLES 12 SP1 (Premium), SLES 12 för HPC, SLES 12 för HPC (Premium)
  * **Ubuntu Server**: 14.04 LTS, 16.04 LTS
    
    > [!TIP]
    > Ange en SUSE Linux Enterprise Server 12 HPC eller CentOS-baserade HPC-avbildning från Azure Marketplace för att använda Azure RDMA-nätverk med en av storlekarna som RDMA-kompatibla. Mer information finns i [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

Ytterligare krav för att distribuera klustret med hjälp av skriptet för HPC Pack IaaS-distribution:

* **Klientdatorn** -behöver du en Windows-baserad klient-dator för att köra skriptet för kluster-distribution.
* **Azure PowerShell** - [installera och konfigurera Azure PowerShell](/powershell/azure/overview) (version 0.8.10 eller senare) på din klientdator.
* **HPC Pack IaaS-distributionsskriptet** – hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Du kan kontrollera vilken version av skriptet genom att köra `.\New-HPCIaaSCluster.ps1 –Version`. Den här artikeln är baserad på version 4.4.1 eller senare av skriptet.

### <a name="deployment-option-1-use-a-resource-manager-template"></a>Alternativ för distribution 1. Använd en Resource Manager-mall
1. Gå till den [HPC Pack-kluster för Linux-arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/) mall i Azure Marketplace och klicka på **distribuera**.
1. Granska informationen i Azure-portalen och klicka sedan på **skapa**.
   
    ![Skapa Portal][portal]
1. På den **grunderna** bladet anger du ett namn för klustret, som också huvudnoden VM-namn. Du kan välja en befintlig resursgrupp eller skapa en grupp för distributionen på en plats som är tillgängliga för dig. Platsen påverkar tillgängligheten för vissa storlekar för Virtuella datorer och andra Azure-tjänster (se [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/)).
1. På den **gå noden inställningar** bladet för första distributionen, kan du oftast acceptera standardinställningarna. 
   
   > [!NOTE]
   > Den **skriptkommandon för efterkonfiguration URL** är en valfri inställning och anger ett offentligt tillgängliga Windows PowerShell-skript som ska köras på huvudnoden virtuell dator när den körs. 
   > 
   > 
1. På den **Compute noden inställningar** bladet och välja en namnkonflikt mönstret för noderna, antalet och storleken på noderna och Linux-distribution att distribuera.
1. På den **infrastrukturinställningar** bladet ange namn för det virtuella nätverket och Active Directory-domän, domän och administratörsautentiseringsuppgifter för VM och ett namnvigningsmönster för storage-konton.
   
   > [!NOTE]
   > HPC Pack använder Active Directory-domänen för att autentisera användare för klustret. 
   > 
   > 
1. När verifieringstesterna körs och du granska användningsvillkoren, klickar du på **köp**.

### <a name="deployment-option-2-use-the-iaas-deployment-script"></a>Alternativ för distribution 2. Använd skriptet för IaaS-distribution
Följande är ytterligare förutsättningar för att distribuera klustret med hjälp av skriptet för HPC Pack IaaS-distribution:

* **Klientdatorn** -behöver du en Windows-baserad klient-dator för att köra skriptet för kluster-distribution.
* **Azure PowerShell** - [installera och konfigurera Azure PowerShell](/powershell/azure/overview) (version 0.8.10 eller senare) på din klientdator.
* **HPC Pack IaaS-distributionsskriptet** – hämta och packa upp den senaste versionen av skriptet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=44949). Du kan kontrollera vilken version av skriptet genom att köra `.\New-HPCIaaSCluster.ps1 –Version`. Den här artikeln är baserad på version 4.4.1 eller senare av skriptet.

**XML-konfigurationsfil**

Skriptet för HPC Pack IaaS-distribution använder en XML-konfigurationsfil som indata för att beskriva HPC-kluster. Följande exempel konfigurationsfil anger ett litet kluster som består av ett HPC Pack-huvudnod och två storlek A7 CentOS 7.0 Linux-beräkningsnoder. 

Ändra filen som behövs för din miljö och önskad klusterkonfigurationen och spara den med ett namn, till exempel HPCDemoConfig.xml. Du behöver till exempel att ange ditt prenumerationsnamn och ett unikt namn på lagringskontot och tjänstnamn. Dessutom kan du välja en annan stöds Linux-avbildning för compute-noder. Mer information om elementen i konfigurationsfilen finns i filen Manual.rtf i mappen skript och [skapa ett HPC-kluster med skriptet för HPC Pack IaaS-distribution](../../windows/classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

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

**Att köra skriptet för HPC Pack IaaS-distribution**

1. Öppna Windows PowerShell på datorn som administratör.
1. Ändra katalog till den mapp där skriptet är installerat (E:\IaaSClusterScript i det här exemplet).
   
    ```powershell
    cd E:\IaaSClusterScript
    ```
1. Kör följande kommando för att distribuera HPC Pack-kluster. Det här exemplet förutsätts att konfigurationsfilen finns i E:\HPCDemoConfig.xml
   
    ```powershell
    .\New-HpcIaaSCluster.ps1 –ConfigFile E:\HPCDemoConfig.xml –AdminUserName MyAdminName
    ```
   
    a. Eftersom den **AdminPassword** har inte angetts i det föregående kommandot uppmanas du att ange lösenordet för användaren *MyAdminName*.
   
    b. Skriptet börjar sedan att verifiera konfigurationsfilen. Det kan ta upp till flera minuter beroende på nätverksanslutningen.
   
    ![Validering][validate]
   
    c. När verifieringar skicka visar skriptet klusterresurserna som du skapar. Ange *Y* att fortsätta.
   
    ![Resurser][resources]
   
    d. Skriptet börjar distribuera HPC Pack-kluster och slutför konfigurationen utan ytterligare manuella steg. Det kan köras i flera minuter.
   
    ![Distribuera][deploy]
   
   > [!NOTE]
   > I det här exemplet skriptet genererar en loggfil automatiskt eftersom den **- LogFile** parametern har inte angetts. Loggarna skrevs inte i realtid, men har samlats in i slutet av verifieringen och distribution. Om PowerShell-processen stoppas när skriptet körs, försvinner vissa loggar.
   > 
   > 

## <a name="connect-to-the-head-node"></a>Ansluta till huvudnoden
När du har distribuerat HPC Pack-kluster i Azure, [ansluta med Remote Desktop](../../windows/connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) till huvudnoden VM med hjälp av domänen autentiseringsuppgifter som du anges när du distribuerade klustret (till exempel *hpc\\clusteradmin*). Du hanterar klustret från klustrets huvudnod.

Starta HPC Cluster Manager för att kontrollera status för HPC Pack-kluster på klustrets huvudnod. Du kan hantera och övervaka Linux compute-noder på samma sätt som du arbetar med Windows-beräkningsnoder. Se exempelvis Linux-noder som anges i **resurshantering** (dessa noder distribueras med den **LinuxNode** mall).

![Hantering av][management]

Du också se Linux-noder i den **termisk karta** vy.

![Termisk karta][heatmap]

## <a name="how-to-move-data-in-a-cluster-with-linux-nodes"></a>Flytta data i ett kluster med Linux-noder
Har du flera alternativ för att flytta data mellan noder för Linux och Windows huvudnoden för klustret. Här är tre vanliga metoder som beskrivs i detalj i följande avsnitt:

* **Azure File** -Exponerar en hanterad SMB-filresurs för att lagra filer i Azure storage. Noder för Windows och Linux-noder kan montera en Azure-filresurs som en enhet eller mapp på samma gång, även om de har distribuerats i olika virtuella nätverk.
* **Huvudnoden SMB dela** -monterar en standard Windows delad mapp för huvudnoden på Linux-noder.
* **HEAD node NFS-server** -är en lösning för fildelning för en blandad miljö för Windows och Linux.

### <a name="azure-file-storage"></a>Azure-fillagring
Den [Azure File](https://azure.microsoft.com/services/storage/files/) tjänsten exponerar filresurser som använder standard SMB 2.1-protokollet. Azure virtuella datorer och molntjänster kan dela fildata i programkomponenter via monterade resurser och lokala program kan komma åt fildata i en resurs via API: et för File storage. 

Detaljerade anvisningar om hur du skapar en Azure-filresurs och montera den på huvudnoden finns i [Kom igång med Azure File storage i Windows](../../../storage/files/storage-how-to-use-files-windows.md). Om du vill montera Azure-filresursen på Linux-noder, se [hur du använder Azure File storage med Linux](../../../storage/files/storage-how-to-use-files-linux.md). Om du vill konfigurera spara anslutningar, se [Persisting anslutningar till Microsoft Azure Files](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/27/persisting-connections-to-microsoft-azure-files.aspx).

I följande exempel skapar du en Azure-filresurs på ett lagringskonto. Öppna en kommandotolk för att montera filresursen på huvudnoden, och ange följande kommandon:

```command
cmdkey /add:allvhdsje.file.core.windows.net /user:allvhdsje /pass:<storageaccountkey>

net use Z: \\allvhdje.file.core.windows.net\rdma /persistent:yes
```

I det här exemplet allvhdsje är namnet på ditt lagringskonto, storageaccountkey är din lagringskontonyckel och rdma är Azure File-resursnamnet. Azure-filresursen monteras som Z: huvudnoden.

Om du vill montera Azure-filresursen på Linux-noder kör en **clusrun** på huvudnoden. **[Clusrun](https://technet.microsoft.com/library/cc947685.aspx)**  är en användbar HPC Pack-verktyg för att utföra administrativa åtgärder på flera noder. (Se även [Clusrun Linux-noder](#Clusrun-for-Linux-nodes) i den här artikeln.)

Öppna ett Windows PowerShell-fönster och ange följande kommandon:

```powershell
clusrun /nodegroup:LinuxNodes mkdir -p /rdma

clusrun /nodegroup:LinuxNodes mount -t cifs //allvhdsje.file.core.windows.net/rdma /rdma -o vers=2.1`,username=allvhdsje`,password=<storageaccountkey>'`,dir_mode=0777`,file_mode=0777
```

Det första kommandot skapar en mapp med namnet /rdma på alla noder i gruppen LinuxNodes. Det andra kommandot monterar Azure File-resursen allvhdsjw.file.core.windows.net/rdma till mappen /rdma med dir och filen bitar ange 777. I det andra kommandot allvhdsje är namnet på ditt lagringskonto och storageaccountkey är din lagringskontonyckel.

> [!NOTE]
> Den ”\`” symbolen i det andra kommandot är en symbolen för PowerShell. ”\`”, innebär det att ””, (kommatecken) är en del av kommandot.
> 
> 

### <a name="head-node-share"></a>Huvudnoden resurs
Du kan också ansluta till en delad mapp för huvudnoden på Linux-noder. En resurs som innehåller det enklaste sättet att dela filer, men huvudnoden och alla Linux-noder måste distribueras i samma virtuella nätverk. Här följer stegen.

1. Skapa en mapp på huvudnoden och dela den till alla med läs-/ skrivbehörighet. Till exempel dela D:\OpenFOAM på huvudnoden som \\CentOS7RDMA HN\OpenFOAM. Här är CentOS7RDMA HN värdnamnet för huvudnoden.
   
    ![Filresursbehörigheter][fileshareperms]
   
    ![Fildelning][filesharing]
1. Öppna ett Windows PowerShell-fönster och kör följande kommandon:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /openfoam
   
    clusrun /nodegroup:LinuxNodes mount -t cifs //CentOS7RDMA-HN/OpenFOAM /openfoam -o vers=2.1`,username=<username>`,password='<password>'`,dir_mode=0777`,file_mode=0777
    ```

Det första kommandot skapar en mapp med namnet /openfoam på alla noder i gruppen LinuxNodes. Det andra kommandot monterar den delade mappen //CentOS7RDMA-HN/OpenFOAM till mappen med dir och filen bitar ange 777. Användarnamnet och lösenordet i kommandot ska vara användarnamnet och lösenordet för en kluster-användare på huvudnoden. (Se [Lägg till eller ta bort klustret användare](https://technet.microsoft.com/library/ff919330.aspx).)

> [!NOTE]
> Den ”\`” symbolen i det andra kommandot är en symbolen för PowerShell. ”\`”, innebär det att ””, (kommatecken) är en del av kommandot.
> 
> 

### <a name="nfs-server"></a>NFS-server
NFS-tjänsten kan du dela och migrera filer mellan datorer som kör operativsystemet Windows Server 2012 med SMB-protokollet och Linux-baserade datorer med hjälp av NFS-protokollet. NFS-servern och alla andra noder måste distribueras i samma virtuella nätverk. Det ger bättre kompatibilitet med Linux-noder jämfört med en SMB-resurs. Den stöder till exempel filen länkar.

1. För att installera och konfigurera en NFS-server, följer du stegen i [Server för System första nätverksfilresurs slutpunkt till slutpunkt](http://blogs.technet.com/b/filecab/archive/2012/10/08/server-for-network-file-system-first-share-end-to-end.aspx).
   
    Till exempel skapa en NFS-resurs med namnet nfs med följande egenskaper:
   
    ![NFS-auktorisering][nfsauth]
   
    ![NFS-resursbehörigheter][nfsshare]
   
    ![NFS-NTFS-behörigheter][nfsperm]
   
    ![Egenskaper för NFS][nfsmanage]
1. Öppna ett Windows PowerShell-fönster och kör följande kommandon:
   
    ```powershell
    clusrun /nodegroup:LinuxNodes mkdir -p /nfsshare
   
    clusrun /nodegroup:LinuxNodes mount CentOS7RDMA-HN:/nfs /nfsshared
    ```
   
   Det första kommandot skapar en mapp med namnet /nfsshared på alla noder i gruppen LinuxNodes. Det andra kommandot monterar NFS-resurs CentOS7RDMA HN: / nfs till mappen. Här CentOS7RDMA HN: / nfs är fjärrsökväg NFS-resursen.

## <a name="how-to-submit-jobs"></a>Hur du skickar in jobb
Det finns flera sätt att skicka jobb till HPC Pack-kluster:

* HPC Cluster Manager eller HPC Job Manager-Gränssnittet
* HPC-webbportalens
* REST-API

Jobböverföring till kluster i Azure via HPC Pack GUI-verktyg och HPC-webbportalens är desamma som för Windows-beräkningsnoder. Se [HPC Pack Job Manager](https://technet.microsoft.com/library/ff919691.aspx) och [hur du skickar in jobb från en klientdator för lokala](../../windows/hpcpack-cluster-submit-jobs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

För att skicka jobb via REST API, som avser [skapa och skicka jobb med hjälp av REST-API i Microsoft HPC Pack](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx). För att skicka jobb från en klient för Linux, även gå till Python-exemplet i den [HPC Pack SDK](https://www.microsoft.com/download/details.aspx?id=47756).

## <a name="clusrun-for-linux-nodes"></a>Clusrun för Linux-noder
HPC Pack [clusrun](https://technet.microsoft.com/library/cc947685.aspx) verktyget kan användas för att köra kommandon på Linux-noder antingen via en kommandotolk eller HPC Cluster Manager. Här följer några grundläggande exempel.

* Visa aktuella användarnamn på alla noder i klustret.
  
    ```command
    clusrun whoami
    ```
* Installera den **gdb** felsökningsverktyget med **yum** på alla noder i linuxnodes gruppen och sedan starta om noderna efter 10 minuter.
  
    ```command
    clusrun /nodegroup:linuxnodes yum install gdb –y; shutdown –r 10
    ```
* Skapa ett kommandoskript som visar varje tal 1 till 10 för en sekund på varje Linux-nod i klustret, köra den och visa utdata från noderna direkt.
  
    ```command
    clusrun /interleaved /nodegroup:linuxnodes echo \"for i in {1..10}; do echo \\\"\$i\\\"; sleep 1; done\" ^> script.sh; chmod +x script.sh; ./script.sh
    ```

> [!NOTE]
> Du kan behöva använda vissa escape-tecken i **clusrun** kommandon. I det här exemplet visas använda ^ i en kommandotolk för att undvika den ”>” symbol.
> 
> 

## <a name="next-steps"></a>Nästa steg
* Prova att skala kluster till ett större antal noder eller försök att köra en Linux-arbetsbelastning i klustret. Ett exempel finns i [kör NAMD med Microsoft HPC Pack på Linux-beräkningsnoder i Azure](hpcpack-cluster-namd.md).
* Testa ett kluster med [RDMA-kompatibla, beräkningsintensiva virtuella datorer](../../windows/sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) att köra MPI-arbetsbelastningar. Ett exempel finns i [kör OpenFOAM med Microsoft HPC Pack på ett Linux RDMA-kluster i Azure](hpcpack-cluster-openfoam.md).
* Om du är intresserad av att arbeta med Linux-noder i ett lokalt HPC Pack-kluster finns i den [TechNet vägledning](https://technet.microsoft.com/library/mt595803.aspx).

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
