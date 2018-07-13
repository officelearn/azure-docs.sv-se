---
title: Konfigurera ett Linux RDMA-kluster för att köra MPI-program | Microsoft Docs
description: Skapa en Linux-kluster med storleken H16r, H16mr, A8 eller A9-datorer för att köra MPI-appar med Azure RDMA-nätverk
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
ms.date: 07/11/2018
ms.author: danlep
ms.openlocfilehash: 471fd4095fe45e76f94df8c61a07eeb9bbc1c120
ms.sourcegitcommit: df50934d52b0b227d7d796e2522f1fd7c6393478
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2018
ms.locfileid: "38990735"
---
# <a name="set-up-a-linux-rdma-cluster-to-run-mpi-applications"></a>Konfigurera ett Linux RDMA-kluster för att köra MPI-program

Lär dig hur du ställer in ett Linux RDMA-kluster i Azure med [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) att köra program med parallella Message Passing Interface (MPI). Den här artikeln innehåller steg för att förbereda en Linux HPC-avbildningen som ska köra Intel MPI i ett kluster. Efter förberedelse, som du distribuerar ett kluster av virtuella datorer med den här avbildningen och en av de [RDMA-kompatibla Azure VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances). Du kan använda klustret för att köra MPI-program som kommunicerar effektivt med låg fördröjning och stora dataflöden nätverk baserat på teknik för direkt fjärråtkomst till minne (RDMA).

> [!IMPORTANT]
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Azure Resource Manager](../../../azure-resource-manager/resource-manager-deployment-model.md) och klassisk. Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

## <a name="cluster-deployment-options"></a>Distributionsalternativ för kluster
Följande är olika metoder som du kan använda för att skapa ett Linux RDMA-kluster med eller utan en jobbschemaläggare.

* **Azure CLI-skript**: använda senare i den här artikeln visas den [kommandoradsgränssnittet](../../../cli-install-nodejs.md) (CLI) för att skriva distributionen av ett kluster med RDMA-kompatibla virtuella datorer. CLI i Service Management-läge skapar klusternoderna seriellt i den klassiska distributionsmodellen så distribuera många compute-noder kan ta flera minuter. Aktivera RDMA-nätverksanslutning när du använder den klassiska distributionsmodellen genom att distribuera de virtuella datorerna i samma molntjänst.
* **Azure Resource Manager-mallar**: du kan också använda distributionsmodellen Resource Manager för att distribuera ett kluster med RDMA-kompatibla virtuella datorer som ansluter till RDMA-nätverk. Du kan [skapa en egen mall](../../../resource-group-authoring-templates.md), eller kontrollera den [Azures snabbstartsmallar](https://azure.microsoft.com/documentation/templates/) för mallar som tillförts av Microsoft eller communityn för att distribuera lösningen som du söker. Resource Manager-mallar kan ge ett snabbt och tillförlitligt sätt att distribuera ett Linux-kluster. Aktivera RDMA-nätverksanslutning när du använder Resource Manager-distributionsmodellen genom att distribuera de virtuella datorerna i samma tillgänglighetsuppsättning eller skalningsuppsättning för virtuell dator.
* **HPC Pack**: skapa ett Microsoft HPC Pack-kluster i Azure och lägga till RDMA-kompatibla compute-noder som kör en Linux-distribution som stöds för att komma åt RDMA-nätverk. Mer information finns i [Kom igång med Linux-beräkningsnoder i ett HPC Pack-kluster i Azure](hpcpack-cluster.md).

## <a name="sample-deployment-steps-in-the-classic-model"></a>Exemplet distributionsstegen i den klassiska modellen
Följande steg visar hur du använder Azure CLI för att distribuera en SUSE Linux Enterprise Server (SLES) 12 HPC-VM på Azure Marketplace, anpassa den och skapa en anpassad virtuell datoravbildning. Du kan sedan använda avbildningen skript distributionen av ett kluster med RDMA-kompatibla virtuella datorer.

> [!TIP]
> Du kan använda liknande steg för att distribuera ett kluster med RDMA-kompatibla virtuella datorer baserat på CentOS-baserade HPC-avbildningar på Azure Marketplace. Vissa steg skilja sig åt, enligt vad som anges.
>

### <a name="prerequisites"></a>Förutsättningar
* **Klientdatorn**: du behöver en Mac, Linux eller Windows-klientdator att kommunicera med Azure. De här stegen förutsätter att du använder en Linux-klient.
* **Azure-prenumeration**: Om du inte har en prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) på bara några minuter. Överväg att en användningsbaserad prenumeration eller andra alternativ för större kluster.
* **VM-storlek tillgänglighet**: Kontrollera [produkttillgänglighet per region](https://azure.microsoft.com/regions/services/) för tillgänglighet i H-serien eller andra RDMA-kompatibla VM-storlekar i Azure-regioner.
* **Kärnkvot**: du kan behöva öka kärnkvoten kärnor för att distribuera ett kluster av beräkningsintensiva virtuella datorer. Till exempel behöver du minst 128 kärnor om du vill distribuera 8 A9 VM som visas i den här artikeln. Din prenumeration kan också begränsa hur många kärnor du kan distribuera i vissa storleksfamiljer VM, inklusive H-serien. En kvot, [öppna en supportbegäran online-kund](../../../azure-supportability/how-to-create-azure-support-request.md) utan kostnad.
* **Azure CLI**: [installera](../../../cli-install-nodejs.md) Azure CLI och [ansluta till din Azure-prenumeration](/cli/azure/authenticate-azure-cli) från klientdatorn.

Granska även överväganden vid distribution för den [RDMA-kompatibla Azure VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json#rdma-capable-instances).

### <a name="provision-an-sles-12-hpc-vm"></a>Etablera en virtuell dator HPC SLES 12
När du loggar in i Azure med Azure CLI, kör `azure config list` att bekräfta att utdata visar Service Management-läge. Om det inte ange läget genom att köra det här kommandot:

    azure config mode asm


Skriv följande om du vill visa alla prenumerationer som du har behörighet att använda:

    azure account list

Den aktuella aktiva prenumerationen identifieras med `Current` inställd `true`. Om den här prenumerationen inte är det du vill använda för att skapa klustret, anger du rätt prenumerations-ID som aktiv prenumeration:

    azure account set <subscription-Id>

Köra ett kommando som liknar följande offentligt tillgängliga SLES 12 HPC-avbildningar i Azure, förutsatt att din miljö har stöd för shell **grep**:

    azure vm image list | grep "suse.*hpc"

Etablera en RDMA-kompatibla virtuell dator med en SLES 12 SP3 HPC-avbildning genom att köra ett kommando som liknar följande:

    azure vm create -g <username> -p <password> -c <cloud-service-name> -l <location> -z A9 -n <vm-name> -e 22 b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-v20170913

Där:

* Storleken (A9 i det här exemplet) är en av storlekarna som RDMA-kompatibla.
* Externa SSH-portnumret (22 i det här exemplet, vilket är standard för SSH) är ett giltigt portnummer. Intern SSH-portnumret är inställd på 22.
* En ny molntjänst skapas i Azure-region anges av platsen. Ange en plats där virtuella datorstorlek som du väljer är tillgängliga, till exempel ”West US”.
* För SUSE prioriterad support (vilket medför ytterligare avgifter), SLES 12 avbildningens namn för närvarande kan vara något av de alternativ som har `priority` i namnet, till exempel: 

 `b4590d9e3ed742e4a1d46e5424aa335e__suse-sles-12-sp3-hpc-priority-v20170913`

### <a name="customize-the-vm"></a>Anpassa den virtuella datorn
När den virtuella datorn är klar etablering, SSH till den virtuella datorn med hjälp av Virtuellt datorns extern IP-adress (eller DNS-namn) och den externa porten nummer du har konfigurerat och sedan anpassa den. Anslutningsinformationen, se [hur du ansluter till en virtuell dator som kör Linux](../mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Utför kommandon som användaren du konfigurerat på den virtuella datorn, såvida inte rotåtkomst krävs för att slutföra ett steg.

> [!IMPORTANT]
> Microsoft Azure ger inte rotåtkomst till virtuella Linux-datorer. Kör kommandon för att få administrativ åtkomst när du är ansluten som en användare till den virtuella datorn med hjälp av `sudo`.
>
>

* **Uppdateringar**: Installera uppdateringar med zypper. Du kanske också vill installera NFS-verktyg.

  > [!IMPORTANT]
  > I en SLES 12 HPC virtuell dator rekommenderar vi att du inte använda kernel-uppdateringar, vilket kan orsaka problem med Linux RDMA drivrutiner.
  >
  >
* **Intel MPI**: Slutför installationen av Intel MPI på den virtuella datorn genom att köra följande kommando:

    ```bash
    sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm
    ```

* **Låsa minne**: för MPI-koder för att låsa det tillgängliga minnet för RDMA, lägga till eller ändra följande inställningar i filen /etc/security/limits.conf. Du behöver rotåtkomst att redigera den här filen.

    ```bash
    <User or group name> hard    memlock <memory required for your application in KB>

    <User or group name> soft    memlock <memory required for your application in KB>
    ```

  > [!NOTE]
  > I testsyfte kan du också ange memlock till obegränsad. Till exempel: `* hard memlock unlimited`. Mer information finns i [mest känd metoder för att inställningen låst minnesstorlek](https://software.intel.com/en-us/blogs/2014/12/16/best-known-methods-for-setting-locked-memory-size).
  >
  >
* **SSH-nycklar för virtuella SLES-datorer**: Generera SSH-nycklar upprätta förtroende för ditt konto bland compute-noder i SLES klustret när du kör MPI-jobb. Om du har distribuerat en CentOS-baserade HPC VM inte följer du detta steg. Se anvisningarna senare i den här artikeln för att upprätta lösenordslös SSH-förtroende mellan noderna i klustret när du tillverkar avbildningen och distribuera klustret.

  Du skapar SSH-nycklar måste köra den `ssh-keygen` kommando. När du uppmanas att ange indata väljer **RETUR** att generera nycklar på standardplatsen för utan att ange ett lösenord.

  Lägg till den offentliga nyckeln till filen authorized_keys för kända offentliga nycklar.

  ```bash
  cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
  ```

  I katalogen ~/.ssh, redigera eller skapa den `config` filen. Ange IP-adressintervallet för det privata nätverket som du planerar att använda i Azure (10.32.0.0/16 i det här exemplet):

  ```bash
  host 10.32.0.*
  StrictHostKeyChecking no
  ```

  Du kan också lista privata nätverk IP-adressen för varje virtuell dator i klustret enligt följande:

  ```bash
  host 10.32.0.1
  StrictHostKeyChecking no
  host 10.32.0.2
  StrictHostKeyChecking no
  host 10.32.0.3
  StrictHostKeyChecking no
  ...
  ```

  > [!NOTE]
  > Konfigurera `StrictHostKeyChecking no` kan skapa en potentiell säkerhetsrisk när en specifik IP-adress eller ett intervall inte anges.
  >
  >
* **Program**: installera alla program som du behöver eller utföra andra anpassningar innan du tillverkar avbildningen.

### <a name="capture-the-image"></a>Samla in en avbildning
Om du vill samla in en avbildning, kör följande kommando på Linux-VM. Det här kommandot deprovisions den virtuella datorn men underhåller användarkonton och SSH-nycklar som du har konfigurerat.

```bash
sudo waagent -deprovision
```

Kör följande Azure CLI-kommandon för att samla in en avbildning från klientdatorn. Mer information finns i [så här avbildar du en klassisk Linux-dator som en bild](capture-image-classic.md).  

```
azure vm shutdown <vm-name>

azure vm capture -t <vm-name> <image-name>

```

När du har kört dessa kommandon VM-avbildningen har gjorts för din användning och den virtuella datorn tas bort. Nu har du en anpassad avbildning som är redo att distribuera ett kluster.

### <a name="deploy-a-cluster-with-the-image"></a>Distribuera ett kluster med avbildningen
Ändra följande Bash-skript med lämpliga värden för din miljö och köra den från klientdatorn. Eftersom Azure har distribuerat de virtuella datorerna seriellt i den klassiska distributionsmodellen, tar det några minuter att distribuera de åtta A9 virtuella datorerna som föreslås i det här skriptet.

```bash
#!/bin/bash -x
# Script to create a compute cluster without a scheduler in a classic VNet in Azure
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
        azure vm create -g <username> -p <password> -c <cloud-service-name> -z A9 -n $vmname$i -e $portnumber$i -w <network-name> -b Subnet-1 <image-name>;
done

# Save this script with a name like makecluster.sh and run it in your shell environment to provision your cluster
```

## <a name="considerations-for-a-centos-hpc-cluster"></a>Överväganden för en CentOS HPC-kluster
Om du vill konfigurera ett kluster baserat på en av avbildningarna för CentOS-baserade HPC på Azure Marketplace i stället för SLES 12 för HPC, följer du de allmänna stegen i föregående avsnitt. Observera följande skillnader när du etablera och konfigurera den virtuella datorn:

- Intel MPI är redan installerad på en virtuell dator som etablerats från en avbildning av en CentOS-baserade HPC.
- Lås minnesinställningarna har redan lagts till i den Virtuella datorns /etc/security/limits.conf-filen.
- Generera inte SSH-nycklar på den virtuella datorn som du etablerar för avbildning. I stället rekommenderar vi att konfigurera användarbaserad autentisering när du har distribuerat i klustret. Mer information finns i avsnittet nedan.  

### <a name="set-up-passwordless-ssh-trust-on-the-cluster"></a>Upprätta förtroende för konfiguration av lösenordsfri SSH på klustret
Det finns två metoder för att upprätta förtroende mellan compute-noderna på en CentOS-baserade HPC-kluster: värd-baserad autentisering och användarbaserad autentisering. Värd-baserad autentisering utanför omfånget för den här artikeln och allmänt måste göras via ett skript för tillägget under distributionen. Användarbaserad autentisering är praktiskt för att upprätta förtroende efter distributionen och kräver generation och delning av SSH-nycklar mellan compute-noder i klustret. Den här metoden kallas lösenordslös SSH-inloggning och krävs när du kör MPI-jobb.

Ett exempelskript `user_authentication.sh` för att aktivera användarbaserad autentisering på en CentOS-baserade HPC kluster följer:

```bash
#!/bin/bash
# For CentOS user must first install epel-release, sshpass, and nmap (sshpass and nmap are available from epel-release for CentOS)

# usage ./user_authentication.sh [username] [password] [internalIP prefix]
# ./user_authentication.sh azureuser Azure@123 10.32.0
USER=$1
PASS=$2
IPPRE=$3
HEADNODE=`hostname`

mkdir -p .ssh
echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''

echo 'Host *' >> .ssh/config
echo 'StrictHostKeyChecking no' >> .ssh/config
chmod 400 .ssh/config
chown azureuser:azureuser /home/azureuser/.ssh/config

nmap -sn $IPPRE.* | grep $IPPRE. | awk '{print $5}' > nodeips.txt
for NAME in `cat nodeips.txt`; do sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'hostname' >> nodenames.txt;done

NAMES=`cat nodenames.txt` #names from names.txt file
for NAME in $NAMES; do
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 /home/$USER/nodenames.txt $USER@$NAME:/home/$USER/
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "mkdir .ssh && chmod 700 .ssh"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME "echo -e  'y\n' | ssh-keygen -f .ssh/id_rsa -t rsa -N ''"
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'touch /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo "Host *" >  /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'echo StrictHostKeyChecking no >> /home/'$USER'/.ssh/config'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 400 /home/'$USER'/.ssh/config'
    cat .ssh/id_rsa.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'cat >> .ssh/authorized_keys'
    sshpass -p $PASS scp -o "StrictHostKeyChecking no" -o ConnectTimeout=2 $USER@$NAME:/home/$USER/.ssh/id_rsa.pub .ssh/sub_node.pub

    for SUBNODE in `cat nodeips.txt`; do
         sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'mkdir -p .ssh'
         cat .ssh/sub_node.pub | sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$SUBNODE 'cat >> .ssh/authorized_keys'
    done
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 700 .ssh/'
    sshpass -p $PASS ssh -o ConnectTimeout=2 $USER@$NAME 'chmod 640 .ssh/authorized_keys'
done
```

Kör skriptet med hjälp av följande steg. Du kan också ändra det här skriptet eller använda en annan metod för att upprätta lösenordslös SSH-autentisering mellan noderna i beräkning.

Om du vill köra skriptet som du behöver veta prefixet för din IP-adresser i undernätet. Hämta prefixet genom att köra följande kommando på en av noderna i klustret. Dina utdata bör se ut ungefär så 10.1.3.5 och prefixet är 10.1.3 delen.

```bash
ifconfig eth0 | grep -w inet | awk '{print $2}'
```

Nu köra skriptet med tre parametrar: vanliga användarnamnet på datornoder, vanliga lösenordet för användaren på datornoder och undernätsprefixet som returnerades från föregående kommando.

```bash
./user_authentication.sh <myusername> <mypassword> 10.1.3
```

Skriptet gör följande:

* Skapar en katalog på värdnoden med namnet .ssh, vilket krävs för konfiguration av lösenordsfri inloggning.
* Skapar en konfigurationsfil i katalogen .ssh som instruerar lösenordslös inloggning så att inloggning från en nod i klustret.
* Skapar filer som innehåller noden namn och nod-IP-adresser för alla noder i klustret. De här filerna är kvar när skriptet har körts för senare referens.
* Skapar en privat och offentlig nyckel för varje nod i klustret (inklusive värdnoden) och skapar poster i filen authorized_keys.

> [!WARNING]
> Kör skriptet kan du skapa en säkerhetsrisk. Se till att informationen om offentliga nyckeln i ~/.ssh inte distribuerats.
>

## <a name="run-mpi-on-a-basic-two-node-cluster"></a>Kör MPI på en grundläggande tvånods-kluster
Om du inte redan har gjort det först ställa in miljön för Intel MPI.

```bash
# For a SLES 12 HPC cluster

source /opt/intel/impi/5.0.3.048/bin64/mpivars.sh

# For a CentOS-based HPC cluster

# source /opt/intel/impi/5.1.3.181/bin64/mpivars.sh
```

### <a name="run-an-mpi-command"></a>Kör en MPI-kommando
Kör en MPI-kommando på en av beräkningsnoder för att visa att MPI är korrekt installerad och att den kan kommunicera mellan minst två beräkningsnoder. Följande **mpirun** kommandot körs den **värdnamn** på två noder. För den `-hosts` parameter, skicka IP-adresserna för två noder i Azure virtuella nätverk (till exempel 10.32.0.4,10.32.0.5).

```bash
mpirun -ppn 1 -n 2 -hosts <host1>,<host2> -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 hostname
```
Ditt resultatet bör innehålla namnen på alla noder som du angav som indata för `-hosts`. Till exempel en **mpirun** kommando med två noder returnerar utdata som liknar följande:

```
cluster11
cluster12
```

### <a name="run-an-mpi-benchmark"></a>Kör en MPI-benchmark
Kommandot Intel MPI körs pingpong prestandamått för att verifiera klusterkonfigurationen och anslutning till RDMA-nätverk.

```bash
mpirun -hosts <host1>,<host2> -ppn 1 -n 2 -env I_MPI_FABRICS=shm:dapl -env I_MPI_DAPL_PROVIDER=ofa-v2-ib0 -env I_MPI_DYNAMIC_CONNECTION=0 IMB-MPI1 pingpong
```

Du bör se utdata som liknar följande i ett fungerande kluster med två noder. Förvänta dig fördröjning vid eller under 3 mikrosekunder för meddelande storlekar upp till 512 byte i Azure RDMA-nätverk.

```
#------------------------------------------------------------
#    Intel (R) MPI Benchmarks 4.0 Update 1, MPI-1 part
#------------------------------------------------------------
# Date                  : Fri Jul 6 17:16:46 2018
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
### <a name="sample-mpi-run-script"></a>Exemplet MPI kör skript
Här är ett Bash-exempelskript för att konfigurera de variabler som behövs för att köra ett MPI-program. Ändra sökvägen till `mpivars.sh` som behövs för installationen av Intel MPI.

```bash
#!/bin/bash -x

# For a SLES 12 HPC cluster

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

# Command line to run the MPI job. Substitute with values appropriate for your application.

mpirun -n <number-of-cores> -ppn <core-per-node> -hostfile <hostfilename>  /path <path to the application exe> <arguments specific to the application>

#end
```

Formatet på värdfilen är som följer. Lägg till en rad för varje nod i klustret. Ange den privata IP-adresser från det virtuella nätverket definierade tidigare, inte DNS-namn. Till exempel för två värdar med IP-adresser 10.32.0.4 och 10.32.0.4 innehåller filen följande:

```bash
10.32.0.4:16
10.32.0.5:16
```


## <a name="next-steps"></a>Nästa steg
* Distribuera och kör dina Linux MPI-program på Linux-kluster.
* Se den [Intel MPI biblioteksdokumentation](https://software.intel.com/en-us/articles/intel-mpi-library-documentation/) vägledning för Intel MPI.
* Prova en [snabbstartsmall](https://github.com/Azure/azure-quickstart-templates/tree/master/intel-lustre-clients-on-centos) att skapa ett Intel Lustre-kluster med hjälp av en CentOS-baserade HPC-avbildning. 
