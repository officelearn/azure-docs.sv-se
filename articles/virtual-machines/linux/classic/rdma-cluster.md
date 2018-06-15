---
title: Ställa in ett Linux RDMA-kluster som kör MPI program | Microsoft Docs
description: Skapa ett Linux-kluster av storlek H16r H16mr, A8 eller A9 virtuella datorer för att köra MPI appar med Azure RDMA-nätverk
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 01834bad-c8e6-48a3-b066-7f1719047dd2
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/14/2017
ms.author: danlep
ms.openlocfilehash: d53305aae3b12c0de983dced85a9626cf98c6309
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210383"
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Konfigurera ett Linux RDMA-kluster för att köra MPI-program
Lär dig hur du ställer in ett Linux RDMA-kluster i Azure med [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att köra parallella Message Passing Interface (MPI) program. Den här artikeln innehåller steg för att förbereda en Linux HPC-avbildning för att köra Intel MPI på ett kluster. Efter förberedelse, kan du distribuera ett kluster för virtuella datorer med hjälp av den här avbildningen och en av RDMA-kompatibla Azure VM-storlekar (för närvarande H16r H16mr, A8 eller A9). Använd klustret för att köra MPI-program som effektiv kommunicerar över ett nätverk för låg latens, hög genomströmning baserat på remote direct memory access (RDMA)-teknik.

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager](../../../resource-manager-deployment-model.md) och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="cluster-deployment-options"></a>Distributionsalternativ för kluster
Följande är metoder som du kan använda för att skapa ett Linux RDMA-kluster med eller utan en jobbschemaläggaren.

* **Azure CLI-skript**: enligt nedan använder den [Azure-kommandoradsgränssnittet](../../../cli-install-nodejs.md) (CLI) skript för distribution av ett kluster med RDMA-kompatibla virtuella datorer. CLI i Service Management-läge skapar noderna i följd i den klassiska distributionsmodellen så distribuerar många compute-noder kan ta flera minuter. Distribuera virtuella datorer i samma molntjänst för att aktivera RDMA-nätverksanslutning när du använder den klassiska distributionsmodellen.
* **Azure Resource Manager-mallar**: du kan också använda distributionsmodell Resource Manager för att distribuera ett kluster med RDMA-kompatibla virtuella datorer som ansluter till nätverket RDMA. Du kan [skapa en egen mall](../../../resource-group-authoring-templates.md), eller kontrollera den [Azure quickstart mallar](https://azure.microsoft.com/documentation/templates/) för mallar som har bidragit med Microsoft eller gruppen för att distribuera lösningen som du söker. Resource Manager-mallar kan ge en snabb och tillförlitlig sätt att distribuera ett Linux-kluster. Om du vill aktivera RDMA-nätverksanslutning när du använder Resource Manager-distributionsmodellen, distribuera de virtuella datorerna i samma tillgänglighetsuppsättning.
* **HPC Pack**: skapa ett Microsoft HPC Pack-kluster i Azure och lägga till RDMA-kompatibla compute-noder som kör en Linux-distribution som stöds för att komma åt nätverkets RDMA. Mer information finns i [komma igång med Linux compute-noder i ett HPC Pack kluster i Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Exempeldistribution stegen i den klassiska modellen
Följande steg visar hur du använder Azure CLI för att distribuera en SUSE Linux Enterprise Server (SLES) 12 SP1 HPC virtuell dator från Azure Marketplace, anpassa den och skapa en anpassad VM-avbildning. Du kan sedan använda avbildningen skript för distribution av ett kluster med RDMA-kompatibla virtuella datorer.

> [!TIP]
> Använd liknande steg för att distribuera ett kluster med RDMA-kompatibla virtuella datorer baserat på CentOS-baserade HPC-avbildningar i Azure Marketplace. Vissa av stegen variera något som anges. 
>
>

### <a name="prerequisites"></a>Förutsättningar
* **Klientdatorn**: du behöver en Mac, Linux eller Windows-klientdator att kommunicera med Azure. De här stegen förutsätter att du använder en Linux-klient.
* **Azure-prenumeration**: Om du inte har en prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter. Överväg att en prenumeration med användningsbaserad betalning eller andra köpalternativ för större kluster.
* **Tillgänglighet för VM-storlek**: följande instans storleken är RDMA-kompatibla: H16r, H16mr, A8 och A9. Kontrollera [produkter som är tillgängliga efter region](https://azure.microsoft.com/regions/services/) för tillgänglighet i Azure-regioner.
* **Kärnor kvoten**: du kan behöva öka kvoten kärnor att distribuera ett kluster med beräkningsintensiva virtuella datorer. Du måste till exempel minst 128 kärnor, om du vill distribuera 8 A9 VM som visas i den här artikeln. Din prenumeration kan också begränsa antal kärnor som du kan distribuera i vissa VM storlek familjer, inklusive H-serien. Att begära en ökad kvot [öppna en supportbegäran online customer](../../../azure-supportability/how-to-create-azure-support-request.md) utan kostnad.
* **Azure CLI**: [installera](../../../cli-install-nodejs.md) Azure CLI och [ansluta till din Azure-prenumeration](/cli/azure/authenticate-azure-cli) från klientdatorn.

### <a name="provision-an-sles-12-sp1-hpc-vm"></a>Etablera en SLES 12 SP1 HPC-dator
När du loggar in på Azure med Azure CLI, kör du `azure config list` att bekräfta att utdata visar Service Management-läge. Om det inte ange läget genom att köra det här kommandot:

    azure config mode asm


Skriv följande om du vill visa en lista över alla prenumerationer som du har behörighet att använda:

    azure account list

Den aktuella aktiva prenumerationen identifieras med `Current` inställd på `true`. Om den här prenumerationen är inte det som du vill använda för att skapa klustret, anger du lämplig prenumerations-ID som aktiv prenumeration:

    azure account set <subscription-Id>

Om du vill se offentligt tillgängliga SLES 12 SP1 HPC-avbildningar i Azure, köra ett kommando som liknar följande, förutsatt att din miljö har stöd för shell **grep**:

    azure vm image list | grep "suse.*hpc"

Etablera en RDMA-kompatibla virtuell dator med en SLES 12 SP1 HPC-avbildning genom att köra ett kommando som liknar följande:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824

Där:

* Storlek (A9 i det här exemplet) är en av RDMA-kompatibla VM-storlekar.
* Externa SSH-portnumret (22 i det här exemplet, vilket är standard SSH) är ett giltigt portnummer. Den interna SSH-portnummer har angetts till 22.
* En ny molntjänst skapas i Azure-region som anges av platsen. Ange en plats där VM-storlek som du väljer är tillgänglig.
* SUSE prioritet support (som debiteras ytterligare) Avbildningsnamnet SLES 12 SP1 för närvarande kan vara något av följande två alternativ: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-v20160824`

  `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp1-hpc-priority-v20160824`


### <a name="customize-the-vm"></a>Anpassa den virtuella datorn
När den virtuella datorn är klar etablerar SSH till den virtuella datorn med hjälp av den virtuella datorn extern IP-adress (eller DNS-namn) och den externa porten nummer du har konfigurerat och anpassa den. Anslutningsinformation finns [så att logga in på en virtuell dator som kör Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Utför kommandon som användaren du konfigurerat på den virtuella datorn, om inte rotåtkomst krävs för att slutföra ett steg.

> [!IMPORTANT]
> Microsoft Azure tillhandahåller inte rotåtkomst till Linux virtuella datorer. Kör kommandon för att få administrativ åtkomst när du är ansluten som en användare till den virtuella datorn, med hjälp av `sudo`.
>
>

* **Uppdateringar**: Installera uppdateringar med hjälp av zypper. Du kanske också vill installera verktyg för NFS.

  > [!IMPORTANT]
  > I en SLES 12 SP1 HPC VM rekommenderar vi att du inte installerar kernel-uppdateringar, vilket kan orsaka problem med Linux-RDMA drivrutiner.
  >
  >
* **Intel MPI**: Slutför installationen av Intel MPI på SLES 12 SP1 HPC VM genom att köra följande kommando:

        sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
* **Låsa minne**: för MPI koder för att låsa det tillgängliga minnet för RDMA, lägga till eller ändra följande inställningar i filen /etc/security/limits.conf. Du måste rotåtkomst att redigera filen.

    ```
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > I testsyfte kan ange du också memlock till obegränsad. Till exempel: `<User or group name>    hard    memlock unlimited`. Mer information finns i [kända bästa metoder för inställningen låst minnesstorleken](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **SSH-nycklar för virtuella datorer SLES**: Generera SSH-nycklar som ska upprätta förtroende för ditt användarkonto bland compute-noderna i SLES kluster när du kör MPI-jobb. Om du har distribuerat en CentOS-baserade HPC VM inte följer du detta steg. Se instruktionerna senare i den här artikeln för att ställa in passwordless SSH-förtroende mellan klusternoderna när du tillverkar avbildningen och distribuera klustret.

    Kör följande kommando för att skapa SSH-nycklar. När du uppmanas att ange indata väljer **RETUR** att generera nycklar på standardplatsen utan att ange ett lösenord.

        ssh-keygen

    Lägg till den offentliga nyckeln till filen authorized_keys för kända offentliga nycklar.

        cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys

    I katalogen ~/.ssh, redigera eller skapa konfigurationsfilen. Ange IP-adressintervall för det privata nätverk som du planerar att använda i Azure (10.32.0.0/16 i det här exemplet):

        host 10.32.0.*
        StrictHostKeyChecking no

    Du kan också visa privat nätverk IP-adressen för varje virtuell dator i klustret:

    ```
    host 10.32.0.1
     StrictHostKeyChecking no
    host 10.32.0.2
     StrictHostKeyChecking no
    host 10.32.0.3
     StrictHostKeyChecking no
    ```

  > [!NOTE]
  > Konfigurera `StrictHostKeyChecking no` kan skapa en säkerhetsrisk om en specifik IP-adress eller intervall har angetts.
  >
  >
* **Program**: installera alla program som du behöver eller utföra andra anpassningar innan du tillverkar avbildningen.

### <a name="capture-the-image"></a>Fånga avbildningen
Om du vill avbilda, kör följande kommando på Linux-VM. Det här kommandot deprovisions den virtuella datorn men underhåller användarkonton och SSH-nycklar som du skapat.

```
sudo waagent -deprovision
```

Kör följande Azure CLI-kommandon för att avbilda från klientdatorn. Mer information finns i [så här skapar du en klassisk virtuell Linux-dator som en bild](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

När du har kört dessa kommandon VM-avbildningen har gjorts för din användning och den virtuella datorn tas bort. Nu har du den anpassade avbildningen som är redo att distribuera ett kluster.

### <a name="deploy-a-cluster-with-the-image"></a>Distribuera ett kluster med avbildningen
Ändra följande Bash-skript med lämpliga värden för din miljö och köra den från din klientdator. Eftersom Azure distribuerar de virtuella datorerna följd i den klassiska distributionsmodellen, tar det några minuter att distribuera åtta A9 virtuella datorer i det här skriptet.

```
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a VNet in Azure
# Create a custom private network in Azure
# Replace 10.32.0.0 with your virtual network address space
# Replace <network-name> with your network identifier
# Replace "West US" with an Azure region where the VM size is available
# See Azure Pricing pages for prices and availability of compute-intensive VMs

azure network vnet create -l "West US" -e 10.32.0.0 -i 16 <network-name>

# Create a cloud service. All the compute-intensive instances need to be in the same cloud service for Linux RDMA to work across InfiniBand.
# Note: The current maximum number of VMs in a cloud service is 50. If you need to provision more than 50 VMs in the same cloud service in your cluster, contact Azure Support.

azure service create <cloud-service-name> --location "West US" –s <subscription-ID>

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Define a prefix for external port numbers. If you want to turn off external ports and use only internal ports to communicate between compute nodes via port 22, don’t use this option. Since port numbers up to 10000 are reserved, use numbers after 10000. Leave external port on for rank 0 and head node.

portnumber=101

# In this cluster there will be 8 size A9 nodes, named cluster11 to cluster18. Specify your captured image in <image-name>. Specify the username and password you used when creating the SSH keys.

for (( i=11; i<19; i++ )); do
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Överväganden för ett CentOS HPC-kluster
Om du vill konfigurera ett baserat på en av CentOS-baserade HPC-avbildningar i Azure Marketplace i stället för SLES 12 för HPC-kluster, följer du de allmänna stegen i föregående avsnitt. Observera följande skillnader när du etablera och konfigurera den virtuella datorn:

- Intel MPI är redan installerad på en virtuell dator etablerats från en CentOS-baserade HPC-avbildning.
- Lås minnesinställningarna redan lagts till i den virtuella datorn /etc/security/limits.conf-filen.
- Generera inte SSH-nycklar på den virtuella datorn du etablera för avbildning. Vi rekommenderar i stället konfigurera användare för autentisering när du har distribuerat klustret. Mer information finns i följande avsnitt.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Ställ in passwordless SSH-förtroende på klustret
Det finns två metoder för att upprätta förtroende mellan compute-noderna på ett CentOS-baserade HPC-kluster: värd-baserad autentisering och användaren-baserad autentisering. Värd-baserad autentisering ligger utanför omfånget för den här artikeln och vanligen måste göras via ett skript med filnamnstillägget under distributionen. Användare-baserad autentisering är praktiskt för att upprätta förtroende efter distributionen och kräver generering och delning av SSH-nycklar mellan compute-noder i klustret. Den här metoden kallas passwordless SSH-inloggning och krävs när du kör MPI-jobb.

Ett exempelskript som bidrog från gemenskapen är tillgängligt på [GitHub](https://github.com/tanewill/utils/blob/master/user_authentication.sh) för att aktivera enkel användarautentisering på ett CentOS-baserade HPC-kluster. Hämta och använda det här skriptet med hjälp av följande steg. Du kan också ändra det här skriptet eller använda någon annan metod för att upprätta passwordless SSH-autentisering mellan klusternoderna för beräkning.

    wget https://raw.githubusercontent.com/tanewill/utils/master/user_authentication.sh

Om du vill köra skriptet som du behöver veta prefix för IP-adresser för undernät. Hämta prefixet genom att köra följande kommando på en av klusternoderna. Din utdata ska se ut ungefär 10.1.3.5 och att prefixet är 10.1.3 del.

    ifconfig eth0 | grep -w inet | awk '{print $2}'

Nu köra skriptet med tre parametrar: vanliga användarnamn på datornoderna, gemensamt lösenord för användaren på datornoder och undernätets prefix som returnerades från det föregående kommandot.

    ./user_authentication.sh <myusername> <mypassword> 10.1.3

Skriptet gör följande:

* Skapar en katalog på värdnoden med namnet .ssh, vilket krävs för passwordless inloggningen.
* Skapar en fil i katalogen .ssh som instruerar passwordless inloggning så att inloggning från en nod i klustret.
* Skapa filer som innehåller nod-namn och IP-adresser i noden för alla noder i klustret. De här filerna finns kvar när skriptet har körts för senare referens.
* Skapar en privata och offentliga nyckel för varje nod i klustret (inklusive värdnoden) och skapar poster i filen authorized_keys.

> [!WARNING]
> Kör skriptet kan du skapa en säkerhetsrisk. Se till att offentlig nyckelinformation i ~/.ssh inte distribueras.
>
>

## <a name="configure-intel-mpi"></a>Konfigurera Intel MPI
Om du vill köra MPI program på Azure Linux RDMA, måste du konfigurera vissa miljövariabler som är specifika för Intel MPI. Här är ett Bash exempelskript för att konfigurera de variabler som behövs för att köra ett program. Ändra sökvägen till mpivars.sh som behövs för installationen av Intel MPI.

```
#!/bin/bash -x

# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh

export I_MPI_FABRICS=shm:dapl

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB
# Setting the variable to shm:dapl gives best performance for some applications
# If your application doesn’t take advantage of shared memory and MPI together, then set only dapl

export I_MPI_DAPL_PROVIDER=ofa-v2-ib0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

export I_MPI_DYNAMIC_CONNECTION=0

# THIS IS A MANDATORY ENVIRONMENT VARIABLE AND MUST BE SET BEFORE RUNNING ANY JOB

# Command line to run the job

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Formatet på värdfilen är som följer. Lägg till en rad för varje nod i klustret. Ange den privata IP-adresser från det virtuella nätverket definierade tidigare, inte DNS-namn. Till exempel för två värdar med IP-adresser 10.32.0.1 och 10.32.0.2 innehåller filen följande:

```
10.32.0.1:16
10.32.0.2:16
```

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Kör MPI på en grundläggande kluster med två noder
Om du inte redan har gjort först ställa in miljön för Intel MPI.

```
# For a SLES 12 SP1 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Kör MPI-kommando
Kör MPI-kommando på en av compute-noder för att visa att MPI är korrekt installerad och att den kan kommunicera mellan minst två compute-noder. Följande **mpirun** kommando körs i **värdnamn** på två noder.

```
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Din utdata ska visa en lista med namnen på alla noder som du har överfört som indata för `-hosts`. Till exempel en **mpirun** kommando med två noder returnerar utdata som liknar följande:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Kör en MPI prestandamått
Kommandot Intel MPI körs en pingpong prestandamått för att verifiera klusterkonfigurationen och anslutning till RDMA-nätverk.

```
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Du bör se utdata som liknar följande i ett fungerande kluster med två noder. Förvänta dig fördröjning vid eller under 3 mikrosekunder för meddelande storlekar upp till 512 byte i Azure RDMA-nätverket.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 17 23:16:46 2015
# Machine               : x86_64
# System                : Linux
# Release               : 3.12.39-44-default
# Version               : #5 SMP Thu Jun 25 22:45:24 UTC 2015
# MPI Version           : 3.0
# MPI Thread Environment:
# New default behavior from Version 3.2 on:
# the number of iterations per message size is cut down
# dynamically when a certain run time (per message size sample)
# is expected to be exceeded. Time limit is defined by variable
# "SECS_PER_SAMPLE" (=> IMB_settings.h)
# or through the flag => -time

# Calling sequence was:
# /opt/intel/impi_latest/bin64/IMB-MPI1 pingpong
# Minimum message length in bytes:   0
# Maximum message length in bytes:   4194304
#
# MPI_Datatype                   :   MPI_BYTE
# MPI_Datatype for reductions    :   MPI_FLOAT
# MPI_Op                         :   MPI_SUM
#
#
# List of Benchmarks to run:
# PingPong
#---------------------------------------------------
# Benchmarking PingPong
# #processes = 2
#---------------------------------------------------
       #bytes #repetitions      t[usec]   Mbytes/sec
            0         1000         2.23         0.00
            1         1000         2.26         0.42
            2         1000         2.26         0.85
            4         1000         2.26         1.69
            8         1000         2.26         3.38
           16         1000         2.36         6.45
           32         1000         2.57        11.89
           64         1000         2.36        25.81
          128         1000         2.64        46.19
          256         1000         2.73        89.30
          512         1000         3.09       157.99
         1024         1000         3.60       271.53
         2048         1000         4.46       437.57
         4096         1000         6.11       639.23
         8192         1000         7.49      1043.47
        16384         1000         9.76      1600.76
        32768         1000        14.98      2085.77
        65536          640        25.99      2405.08
       131072          320        50.68      2466.64
       262144          160        80.62      3101.01
       524288           80       145.86      3427.91
      1048576           40       279.06      3583.42
      2097152           20       543.37      3680.71
      4194304           10      1082.94      3693.63

# All processes entering MPI_Finalize

```



## <a name="next-steps"></a>Nästa steg
* Distribuera och köra din Linux MPI program på Linux-kluster.
* Finns det [Intel MPI biblioteket dokumentationen](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) vägledning om Intel MPI.
* Försök en [quickstart mallen](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) att skapa ett Intel Lyster kluster med hjälp av en CentOS-baserade HPC-avbildning. Mer information finns i [distribuera Intel molnet Edition för Lyster på Microsoft Azure](https://blogs.msdn.microsoft.com/arsen/2015/10/29/deploying-intel-cloud-edition-for-lustre-on-microsoft-azure/).
