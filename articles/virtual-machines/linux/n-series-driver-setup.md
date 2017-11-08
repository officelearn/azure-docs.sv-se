---
title: "Azure N-serien drivrutinsinstallation för Linux | Microsoft Docs"
description: "Hur du ställer in NVIDIA GPU drivrutiner för N-serien virtuella datorer som kör Linux i Azure"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 11/06/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 96e429ae0e9462e6046a4aaabc5ab9281f2e67ce
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2017
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installera drivrutiner för NVIDIA GPU på N-serien virtuella datorer som kör Linux

För att dra nytta av funktionerna i Azure N-serien virtuella datorer som kör Linux GPU installera stöd för grafik drivrutinerna. Den här artikeln innehåller drivrutinen konfigurationsstegen när du distribuerar en virtuell dator i N-serien. Inställningsinformation för drivrutinen är också tillgängligt för [virtuella Windows-datorer](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


N-serien VM specifikationerna, lagringskapacitet, och diskinformation finns [GPU Linux VM-storlekar](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 



[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-grid-drivers-for-nv-vms"></a>Installera drivrutiner för rutnät för NV virtuella datorer

Att installera drivrutiner för NVIDIA RUTNÄTET på NV VMs, göra en SSH-anslutning på varje virtuell dator och följer du stegen för Linux-distribution. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Kör den `lspci` kommando. Kontrollera att NVIDIA M60 kort eller kort visas som PCI-enheter.

2. Installera uppdateringar.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Inaktivera kernel-drivrutinen Nouveau som inte är kompatibel med NVIDIA-drivrutinen. (Endast använda NVIDIA drivrutinen på NV virtuella datorer.) Det gör du genom att skapa en fil i `/etc/modprobe.d `med namnet `nouveau.conf` med följande innehåll:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Starta om den virtuella datorn och återansluta. Avsluta X-server:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Hämta och installera drivrutinen RUTNÄTET:

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 

6. När du tillfrågas om du vill köra verktyget nvidia xconfig Välj om du vill uppdatera konfigurationsfilen X **Ja**.

7. När installationen är klar, kopiera /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats/etc/nvidia /

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Lägg till följande `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Starta om den virtuella datorn och fortsätta med att verifiera installationen.


### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>CentOS-baserade 7.3 eller Red Hat Enterprise Linux 7.3

> [!IMPORTANT]
> Kör inte `sudo yum update` att uppdatera versionen på CentOS 7.3 eller Red Hat Enterprise Linux 7.3 kernel. För närvarande fungerar installation av drivrutiner och uppdateringar inte om kernel uppdateras.
>

1. Uppdatera kernel och DKMS.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Inaktivera kernel-drivrutinen Nouveau som inte är kompatibel med NVIDIA-drivrutinen. (Endast använda NVIDIA drivrutinen på NV virtuella datorer.) Det gör du genom att skapa en fil i `/etc/modprobe.d `med namnet `nouveau.conf` med följande innehåll:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Starta om den virtuella datorn, återansluta och installera de senaste Linux integreringstjänsterna för Hyper-V:
 
  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3.tar.gz

  tar xvzf lis-rpms-4.2.3.tar.gz

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Återansluta till den virtuella datorn och kör den `lspci` kommando. Kontrollera att NVIDIA M60 kort eller kort visas som PCI-enheter.
 
5. Hämta och installera drivrutinen RUTNÄTET:

  ```bash
  wget -O NVIDIA-Linux-x86_64-367.106-grid.run https://go.microsoft.com/fwlink/?linkid=849941  

  chmod +x NVIDIA-Linux-x86_64-367.106-grid.run

  sudo ./NVIDIA-Linux-x86_64-367.106-grid.run
  ``` 
6. När du tillfrågas om du vill köra verktyget nvidia xconfig Välj om du vill uppdatera konfigurationsfilen X **Ja**.

7. När installationen är klar, kopiera /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats/etc/nvidia /
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Lägg till följande `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=TRUE
  ```
9. Starta om den virtuella datorn och fortsätta med att verifiera installationen.

### <a name="verify-driver-installation"></a>Verifiera installation av drivrutiner


Fråga Enhetsstatus GPU SSH till den virtuella datorn och kör den [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyg som installeras med drivrutinen. 

Det visas utdata som liknar följande:

![NVIDIA Enhetsstatus](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 server
Om du behöver en X11 server för fjärranslutningar till en NV VM [x11vnc](http://www.karlrunge.com/x11vnc/) rekommenderas eftersom det tillåter maskinvaruacceleration för grafik. BusID för M60 enheten måste läggas till manuellt i filen xconfig (`etc/X11/xorg.conf` på Ubuntu 16.04 LTS `/etc/X11/XF86config` på CentOS 7.3 eller Red Hat Enterprise Server 7.3). Lägg till en `"Device"` avsnitt som liknar följande:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "your-BusID:0:0:0"
EndSection
```
 
Dessutom uppdatera din `"Screen"` avsnittet om du vill använda den här enheten.
 
BusID hittar du genom att köra

```bash
/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1
```
 
BusID kan ändras när en virtuell dator hämtar omfördelats startas om. Därför kan du kanske vill använda ett skript för att uppdatera BusID i X11 konfiguration när en virtuell dator startas. Exempel:

```bash 
#!/bin/bash
BUSID=$((16#`/usr/bin/nvidia-smi --query-gpu=pci.bus_id --format=csv | tail -1 | cut -d ':' -f 1`))

if grep -Fxq "${BUSID}" /etc/X11/XF86Config; then     echo "BUSID is matching"; else   echo "BUSID changed to ${BUSID}" && sed -i '/BusID/c\    BusID          \"PCI:0@'${BUSID}':0:0:0\"' /etc/X11/XF86Config; fi
```

Den här filen kan anropas som rot på Start genom att skapa en post för den i `/etc/rc.d/rc3.d`.


## <a name="install-cuda-drivers-for-nc-vms"></a>Installera CUDA drivrutiner för NC virtuella datorer

Här följer stegen för att installera drivrutinerna på Linux NC virtuella datorer från NVIDIA CUDA Toolkit. 

C och C++ utvecklare kan välja att installera fullständig Toolkit för att skapa GPU-snabbare program. Mer information finns i [CUDA installationsguiden](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).


> [!NOTE]
> CUDA drivrutinen länkar som anges här aktuella vid tiden för publikationen. De senaste drivrutinerna för CUDA, finns det [NVIDIA](https://developer.nvidia.com/cuda-zone) webbplats.
>

Om du vill installera CUDA Toolkit gör du en SSH-anslutning på varje virtuell dator. Kontrollera att systemet har en CUDA-kompatibla GPU genom att köra följande kommando:

```bash
lspci | grep -i NVIDIA
```
Du kommer se utdata som liknar följande exempel (visar ett NVIDIA Tesla K80-kort):

![lspci kommandoutdata](./media/n-series-driver-setup/lspci.png)

Sedan kör installationskommandon som är specifika för din distribution.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Hämta och installera drivrutiner för CUDA.
  ```bash
  CUDA_REPO_PKG=cuda-9-0_9.0.176-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  Installationen kan ta flera minuter.

2. Vill du installera komplett CUDA verktyg skriver du:

  ```bash
  sudo apt-get install cuda
  ```

3. Starta om den virtuella datorn och fortsätta med att verifiera installationen.

#### <a name="cuda-driver-updates"></a>CUDA drivrutinsuppdateringar

Vi rekommenderar att du regelbundet uppdatera CUDA drivrutiner efter distributionen.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-based-73-or-red-hat-enterprise-linux-73"></a>CentOS-baserade 7.3 eller Red Hat Enterprise Linux 7.3

1. Installera de senaste Linux integreringstjänsterna för Hyper-V.

  > [!IMPORTANT]
  > Om du har installerat en CentOS-baserade HPC-avbildning på en virtuell dator NC24r vidare till steg3. Eftersom Azure RDMA drivrutiner och Linux-integreringstjänster är förinstallerade i HPC-avbildningen LIS uppgraderas inte och kernel-uppdateringar är inaktiverad som standard.
  >

  ```bash
  wget http://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
 
  tar xvzf lis-rpms-4.2.3-1.tar.gz
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Anslut till den virtuella datorn och fortsätta installationen med följande kommandon:

  ```bash
  sudo yum install kernel-devel

  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-9.0.176-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  Installationen kan ta flera minuter. 

4. Vill du installera komplett CUDA verktyg skriver du:

  ```bash
  sudo yum install cuda
  ```

5. Starta om den virtuella datorn och fortsätta med att verifiera installationen.


### <a name="verify-driver-installation"></a>Verifiera installation av drivrutiner


Fråga Enhetsstatus GPU SSH till den virtuella datorn och kör den [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyg som installeras med drivrutinen. 

Det visas utdata som liknar följande:

![NVIDIA Enhetsstatus](./media/n-series-driver-setup/smi.png)



## <a name="rdma-network-for-nc24r-vms"></a>RDMA-nätverk för virtuella datorer NC24r

RDMA-nätverksanslutning kan aktiveras på NC24r virtuella datorer som distribueras i samma tillgänglighetsuppsättning. RDMA-nätverket har stöd för Message Passing Interface (MPI) trafik för program som körs med Intel MPI 5.x eller en senare version. Ytterligare krav som följer:

### <a name="distributions"></a>Distributioner

Distribuera NC24r virtuella datorer från en av följande avbildningar i Azure Marketplace som stöder RDMA-anslutningar:
  
* **Ubuntu** -Ubuntu Server 16.04 LTS. Konfigurera RDMA drivrutiner på den virtuella datorn och registrera med Intel hämta Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-baserade HPC** -CentOS-baserade 7.3 HPC. RDMA-drivrutiner och Intel MPI 5.1 är installerade på den virtuella datorn. 


## <a name="troubleshooting"></a>Felsökning

* Det finns ett känt problem med CUDA drivrutiner på Azure N-serien virtuella datorer som kör Linux-kärnan 4.4.0-75 på Ubuntu 16.04 LTS. Om du uppgraderar från en tidigare version av kernel kan uppgradera till minst 4.4.0-77 för kernel-version. 



## <a name="next-steps"></a>Nästa steg

* Mer information om NVIDIA GPU-kort på virtuella datorer N-serien finns i:
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html) (för Azure NC virtuella datorer)
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html) (för Azure NV virtuella datorer)

* För att avbilda en Linux-VM-avbildning med din installerade drivrutinerna finns [så att generalisera och avbilda en virtuell Linux-dator](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
