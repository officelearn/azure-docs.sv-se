---
title: Kör STAR-CCM + med HPC Pack på virtuella Linux-datorer | Microsoft Docs
description: Distribuera ett kluster för Microsoft HPC Pack på Azure och köra en stjärna-CCM + jobb på flera Linux compute-noder i ett nätverk med RDMA.
services: virtual-machines-linux
documentationcenter: ''
author: xpillons
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager,hpc-pack
ms.assetid: 75523406-d268-4623-ac3e-811c7b74de4b
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: big-compute
ms.date: 09/13/2016
ms.author: xpillons
ms.openlocfilehash: 8689d7abfd5ab45277df3b5672a1f6e7e874d88e
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="run-star-ccm-with-microsoft-hpc-pack-on-a-linux-rdma-cluster-in-azure"></a>Kör STAR-CCM + med Microsoft HPC Pack på en Linux RDMA kluster i Azure
Den här artikeln visar hur du distribuerar ett Microsoft HPC Pack kluster på Azure och kör en [CD-simuleringar STAR-CCM +](http://www.cd-adapco.com/products/star-ccm%C2%AE) jobb på flera Linux compute-noder som är sammankopplade med InfiniBand.

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

Microsoft HPC Pack tillhandahåller funktioner för att köra en mängd stora HPC och parallella program, inklusive MPI program på kluster av Microsoft Azure-datorer. HPC Pack stöder också Linux HPC-program som körs på Linux-beräkningsnod virtuella datorer som distribueras i ett HPC Pack-kluster. En introduktion till Linux compute-noder med HPC Pack, se [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster.md).

## <a name="set-up-an-hpc-pack-cluster"></a>Konfigurera ett HPC Pack-kluster
Hämta HPC Pack IaaS distribution skript från den [Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=44949) och extrahera dem lokalt.

Azure PowerShell är en förutsättning. Läs artikeln om PowerShell inte har konfigurerats på den lokala datorn [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

När detta skrivs är Linux-avbildningar från Azure Marketplace (som innehåller InfiniBand-drivrutiner för Azure) för SLES 12, CentOS 6.5 och CentOS 7.1. Den här artikeln baseras på användningen av SLES 12. Du kan köra följande PowerShell-kommando för att hämta namnet på alla Linux-bilder som stöder HPC i Marketplace:

```
    get-azurevmimage | ?{$_.ImageName.Contains("hpc") -and $_.OS -eq "Linux" }
```

Utdata visar den plats där dessa avbildningar är tillgängliga och Avbildningsnamnet (**avbildning**) som ska användas i distributionsmallen för senare.

Innan du distribuerar klustret som du behöver skapa en mallfil för distribution av HPC Pack. Eftersom vi riktad ett litet kluster huvudnoden domänkontrollant och vara värd för en lokal SQL-databas.

Följande mall kommer att distribuera en huvudnod, skapa en XML-fil med namnet **MyCluster.xml**, och Ersätt värdena för **SubscriptionId**, **StorageAccount**,  **Plats**, **VMName**, och **ServiceName** med dina.

    <?xml version="1.0" encoding="utf-8" ?>
    <IaaSClusterConfig>
      <Subscription>
        <SubscriptionId>99999999-9999-9999-9999-999999999999</SubscriptionId>
        <StorageAccount>mystorageaccount</StorageAccount>
      </Subscription>
      <Location>North Europe</Location>
      <VNet>
        <VNetName>hpcvnetne</VNetName>
        <SubnetName>subnet-hpc</SubnetName>
      </VNet>
      <Domain>
        <DCOption>HeadNodeAsDC</DCOption>
        <DomainFQDN>hpc.local</DomainFQDN>
      </Domain>
      <Database>
        <DBOption>LocalDB</DBOption>
      </Database>
      <HeadNode>
        <VMName>myhpchn</VMName>
        <ServiceName>myhpchn</ServiceName>
        <VMSize>Standard_D4</VMSize>
      </HeadNode>
      <LinuxComputeNodes>
        <VMNamePattern>lnxcn-%0001%</VMNamePattern>
        <ServiceNamePattern>mylnxcn%01%</ServiceNamePattern>
        <MaxNodeCountPerService>20</MaxNodeCountPerService>
        <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
        <VMSize>A9</VMSize>
        <NodeCount>0</NodeCount>
        <ImageName>b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-hpc-v20150708</ImageName>
      </LinuxComputeNodes>
    </IaaSClusterConfig>

Börja skapa huvudnod genom att köra PowerShell-kommando i en upphöjd kommandotolk:

```
    .\New-HPCIaaSCluster.ps1 -ConfigFile MyCluster.xml
```

Efter 20 till 30 minuter vara huvudnoden redo. Du kan ansluta till den från Azure portal genom att klicka på den **Anslut** ikonen för den virtuella datorn.

Du kanske så småningom att åtgärda DNS-vidarebefordrare. Gör du genom att starta DNS-hanteraren.

1. Högerklicka på servernamnet i DNS-hanteraren, Välj **egenskaper**, och klicka sedan på den **vidarebefordrare** fliken.
2. Klicka på den **redigera** knappen för att ta bort alla vidarebefordrare och klicka sedan på **OK**.
3. Se till att den **använda rottips om det finns inga vidarebefordrare** kryssrutan är markerad och klicka sedan på **OK**.

## <a name="set-up-linux-compute-nodes"></a>Ställ in Linux compute-noder
Du kan distribuera datornoderna Linux med hjälp av samma distributionsmallen som du använde för att skapa huvudnoden.

Kopiera filen **MyCluster.xml** från din lokala dator till huvudnod och uppdatera den **NodeCount** tagg med antalet noder som du vill distribuera (< = 20). Var noga med att ha tillräckligt med tillgängliga kärnor i din Azure kvot eftersom varje A9-instansen kommer att använda 16 kärnor i prenumerationen. Du kan använda A8 instanser (8 kärnor) i stället för A9 om du vill använda flera virtuella datorer i samma budget.

Kopiera HPC Pack IaaS distribution skript på huvudnoden.

Kör följande Azure PowerShell-kommandon i en upphöjd kommandotolk:

1. Kör **Add-AzureAccount** att ansluta till din Azure-prenumeration.
2. Om du har flera prenumerationer kör **Get-AzureSubscription** att lista dem.
3. Ange en standard-prenumeration genom att köra den **Välj AzureSubscription - SubscriptionName xxxx-standard** kommando.
4. Kör **.\New-HPCIaaSCluster.ps1 - ConfigFile MyCluster.xml** för att börja distribuera Linux compute-noder.
   
   ![Huvudnod distribution i åtgärd][hndeploy]

Öppna verktyget HPC Pack Cluster Manager. Efter några minuter visas regelbundet Linux compute-noder i listan över compute-noder i klustret. Klassisk distribution-läget skapas IaaS-VM sekventiellt. Så om antalet noder som är viktigt, kan hämtar alla distribuerade ta lång tid.

![Linux-noder i HPC Pack Cluster Manager][clustermanager]

Nu när alla noder är igång och körs i klustret, finns det ytterligare infrastruktur för att se.

## <a name="set-up-an-azure-file-share-for-windows-and-linux-nodes"></a>Konfigurera en Azure-filresurs för Windows och Linux-noder
Du kan använda tjänsten Azure för att lagra skript och programpaket datafiler. Azure-filen innehåller CIFS ovanpå Azure Blob storage som beständiga arkivet. Tänk på att detta är inte den bästa lösningen, men det är den enklaste och behöver inte dedikerade virtuella datorer.

Skapa en filresurs i Azure genom att följa anvisningarna i artikeln [Kom igång med Azure File storage i Windows](../../../storage/files/storage-dotnet-how-to-use-files.md).

Behålla namnet på ditt lagringskonto som **saname**, filresursnamn som **sharename**, och lagringskontonyckel som **sakey**.

### <a name="mount-the-azure-file-share-on-the-head-node"></a>Montera filresursen Azure på huvudnoden
Öppna en upphöjd kommandotolk och kör följande kommando för att lagra autentiseringsuppgifter i det lokala datorn valvet:

```
    cmdkey /add:<saname>.file.core.windows.net /user:<saname> /pass:<sakey>
```

Kör sedan montera filresursen för Azure:

```
    net use Z: \\<saname>.file.core.windows.net\<sharename> /persistent:yes
```

### <a name="mount-the-azure-file-share-on-linux-compute-nodes"></a>Montera filresursen Azure på Linux compute-noder
Ett bra verktyg som medföljer HPC Pack är verktyget clusrun. Du kan använda kommandoradsverktyget för att köra samma kommando samtidigt på en uppsättning compute-noder. I vårt fall används att montera filresursen för Azure och spara det ska finnas kvar vid omstarter.
Kör följande kommandon i en upphöjd kommandotolk på huvudnoden.

För att skapa monteringskatalogen:

```
    clusrun /nodegroup:LinuxNodes mkdir -p /hpcdata
```

Montera filresursen för Azure:

```
    clusrun /nodegroup:LinuxNodes mount -t cifs //<saname>.file.core.windows.net/<sharename> /hpcdata -o vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777
```

Att bevara montera filresursen:

```
    clusrun /nodegroup:LinuxNodes "echo //<saname>.file.core.windows.net/<sharename> /hpcdata cifs vers=2.1,username=<saname>,password='<sakey>',dir_mode=0777,file_mode=0777 >> /etc/fstab"
```

## <a name="install-star-ccm"></a>Installera STAR-CCM +
Azure VM-instanser A8- och A9 tillhandahåller InfiniBand-stöd och RDMA-funktioner. Kernel-drivrutiner som aktiverar dessa funktioner är tillgängliga för Windows Server 2012 R2, SUSE 12, CentOS 6.5 och CentOS 7.1 avbildningar i Azure Marketplace. Microsoft MPI och Intel MPI (utgåva 5.x) är de två MPI-bibliotek som har stöd för de drivrutinerna i Azure.

CD-simuleringar STAR-CCM + släpper 11.x och senare paketeras med Intel MPI version 5.x, så ingår InfiniBand-stöd för Azure.

Hämta Linux64 STAR-CCM + paket från den [CD-simuleringar portal](https://steve.cd-adapco.com). I det här fallet används det version 11.02.010 i blandade precision.

I huvudnoden i den **/hpcdata** Azure File delar, skapa ett kommandoskript som heter **setupstarccm.sh** med följande innehåll. Det här skriptet ska köras på varje compute-nod för att ställa in STAR-CCM + lokalt.

#### <a name="sample-setupstarcmsh-script"></a>Exempelskript för setupstarcm.sh
```
    #!/bin/bash
    # setupstarcm.sh to set up STAR-CCM+ locally

    # Create the CD-adapco main directory
    mkdir -p /opt/CD-adapco

    # Copy the STAR-CCM package from the file share to the local directory
    cp /hpcdata/StarCCM/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz /opt/CD-adapco/

    # Extract the package
    tar -xzf /opt/CD-adapco/STAR-CCM+11.02.010_01_linux-x86_64.tar.gz -C /opt/CD-adapco/

    # Start a silent installation of STAR-CCM without the FLEXlm component
    /opt/CD-adapco/starccm+_11.02.010/STAR-CCM+11.02.010_01_linux-x86_64-2.5_gnu4.8.bin -i silent -DCOMPUTE_NODE=true -DNODOC=true -DINSTALLFLEX=false

    # Update memory limits
    echo "*               hard    memlock         unlimited" >> /etc/security/limits.conf
    echo "*               soft    memlock         unlimited" >> /etc/security/limits.conf
```
Nu ställa in STAR-CCM + på alla Linux compute-noder, öppna en upphöjd kommandotolk och kör följande kommando:

```
    clusrun /nodegroup:LinuxNodes bash /hpcdata/setupstarccm.sh
```

Du kan övervaka CPU-användningen med hjälp av termisk karta av Klusterhanterare när kommandot körs. Efter några minuter måste alla noder vara korrekt inställt.

## <a name="run-star-ccm-jobs"></a>Kör STAR-CCM + jobb
HPC Pack används för dess jobbet scheduler-funktioner för att kunna köra STAR-CCM + jobb. Om du vill göra det, vi behöver stöd för några skript som används för att starta jobbet och kör STAR-CCM +. Inkommande data sparas på Azure-filresursen första för enkelhetens skull.

Följande PowerShell-skript används för att Köa en stjärna-CCM + jobb. Det tar tre argument:

* Modellnamnet
* Antalet noder som ska användas
* Antal kärnor på varje nod som ska användas

Eftersom STAR-CCM + kan fylla bandbredden minne är det oftast bättre att använda färre kärnor per datornoder och lägga till nya noder. Det exakta antalet kärnor per nod beror på processorfamiljen och sammankoppling hastighet.

Noderna tilldelas endast för jobbet och kan inte delas med andra jobb. Jobbet startas inte som ett MPI-jobb direkt. Den **runstarccm.sh** shell-skript ska starta MPI starta.

Inkommande modellen och **runstarccm.sh** skriptet lagras i den **/hpcdata** resurs som tidigare har monterats.

Loggfilerna namnges med jobb-ID och lagras i den **/hpcdata resursen**, tillsammans med STJÄRNAN-CCM + utgående filer.

#### <a name="sample-submitstarccmjobps1-script"></a>Sample SubmitStarccmJob.ps1 script
```
    Add-PSSnapin Microsoft.HPC -ErrorAction silentlycontinue
    $scheduler="headnodename"
    $modelName=$args[0]
    $nbCoresPerNode=$args[2]
    $nbNodes=$args[1]

    #---------------------------------------------------------------------------------------------------------
    # Create a new job; this will give us the job ID that's used to identify the name of the uploaded package in Azure
    #
    $job = New-HpcJob -Name "$modelName $nbNodes $nbCoresPerNode" -Scheduler $scheduler -NumNodes $nbNodes -NodeGroups "LinuxNodes" -FailOnTaskFailure $true -Exclusive $true
    $jobId = [String]$job.Id

    #---------------------------------------------------------------------------------------------------------
    # Submit the job     
    $workdir =  "/hpcdata"
    $execName = "$nbCoresPerNode runner.java $modelName.sim"

    $job | Add-HpcTask -Scheduler $scheduler -Name "Compute" -stdout "$jobId.log" -stderr "$jobId.err" -Rerunnable $false -NumNodes $nbNodes -Command "runstarccm.sh $execName" -WorkDir "$workdir"


    Submit-HpcJob -Job $job -Scheduler $scheduler
```
Ersätt **runner.java** med din önskade stjärna-CCM + Java modellen programstart och loggning kod.

#### <a name="sample-runstarccmsh-script"></a>Exempelskript för runstarccm.sh
```
    #!/bin/bash
    echo "start"
    # The path of this script
    SCRIPT_PATH="$( dirname "${BASH_SOURCE[0]}" )"
    echo ${SCRIPT_PATH}
    # Set the mpirun runtime environment
    export CDLMD_LICENSE_FILE=1999@flex.cd-adapco.com

    # mpirun command
    STARCCM=/opt/CD-adapco/STAR-CCM+11.02.010/star/bin/starccm+

    # Get node information from ENVs
    NODESCORES=(${CCP_NODES_CORES})
    COUNT=${#NODESCORES[@]}
    NBCORESPERNODE=$1

    # Create the hostfile file
    NODELIST_PATH=${SCRIPT_PATH}/hostfile_$$
    echo ${NODELIST_PATH}

    # Get every node name and write into the hostfile file
    I=1
    NBNODES=0
    while [ ${I} -lt ${COUNT} ]
    do
        echo "${NODESCORES[${I}]}" >> ${NODELIST_PATH}
        let "I=${I}+2"
        let "NBNODES=${NBNODES}+1"
    done
    let "NBCORES=${NBNODES}*${NBCORESPERNODE}"

    # Run STAR-CCM with the hostfile argument
    #  
    ${STARCCM} -np ${NBCORES} -machinefile ${NODELIST_PATH} \
        -power -podkey "<yourkey>" -rsh ssh \
        -mpi intel -fabric UDAPL -cpubind bandwidth,v \
        -mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0" \
        -batch $2 $3
    RTNSTS=$?
    rm -f ${NODELIST_PATH}

    exit ${RTNSTS}
```

I vår test använde vi en licens Power på begäran-token. För att denna token måste du ange den **$CDLMD_LICENSE_FILE** miljövariabeln **1999@flex.cd-adapco.com** och nyckeln i den **- podkey** alternativ på kommandoraden.

Efter vissa initiering extraherar skriptet--från den **$CCP_NODES_CORES** miljövariabler som HPC Pack ange--listan över noder för att skapa en hostfile MPI programstart använder. Den här hostfile innehåller listan över beräkning nod-namn som används för jobbet, ett namn per rad.

Formatet på **$CCP_NODES_CORES** följer detta mönster:

```
<Number of nodes> <Name of node1> <Cores of node1> <Name of node2> <Cores of node2>...`
```

Där:

* `<Number of nodes>` är antalet noder som allokerats till det här jobbet.
* `<Name of node_n_...>` är namnet på varje nod som allokerats till det här jobbet.
* `<Cores of node_n_...>` är antalet kärnor på den nod som allokerats till det här jobbet.

Antal kärnor (**$NBCORES**) beräknas baserat på antalet noder (**$NBNODES**) och antalet kärnor per nod (som anges som parameter **$NBCORESPERNODE**).

För MPI-alternativ är de som används med Intel MPI på Azure

* `-mpi intel` Ange Intel MPI.
* `-fabric UDAPL` att använda Azure InfiniBand verb.
* `-cpubind bandwidth,v` att optimera bandbredd för MPI med stjärna-CCM +.
* `-mppflags "-ppn $NBCORESPERNODE -genv I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -genv I_MPI_DAPL_UD=0 -genv I_MPI_DYNAMIC_CONNECTION=0"` Om du vill se Intel MPI arbeta med Azure InfiniBand och ange antalet kärnor per nod som krävs.
* `-batch` Starta STAR-CCM + i batchläge med något användargränssnitt.

Slutligen, om du vill starta ett jobb, kontrollera att noderna är igång och körs och är online i hanteraren för. Från en PowerShell-Kommandotolken kör detta:

```
    .\ SubmitStarccmJob.ps1 <model> <nbNodes> <nbCoresPerNode>
```

## <a name="stop-nodes"></a>Stoppa noder
Vid ett senare tillfälle när du är klar med dina tester kan du använda följande HPC Pack PowerShell-kommandon stoppa och starta noder:

```
    Stop-HPCIaaSNode.ps1 -Name <prefix>-00*
    Start-HPCIaaSNode.ps1 -Name <prefix>-00*
```

## <a name="next-steps"></a>Nästa steg
Försök att köra andra Linux-arbetsbelastningar. Se exempelvis:

* [Kör NAMD med Microsoft HPC Pack på Linux compute-noder i Azure](hpcpack-cluster-namd.md)
* [Köra OpenFOAM with Microsoft HPC Pack på en Linux RDMA-kluster i Azure](hpcpack-cluster-openfoam.md)

<!--Image references-->
[hndeploy]:media/hpcpack-cluster-starccm/hndeploy.png
[clustermanager]:media/hpcpack-cluster-starccm/ClusterManager.png
