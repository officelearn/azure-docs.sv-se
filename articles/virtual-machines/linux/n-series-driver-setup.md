---
title: N-serien GPU-drivrutinen konfigurationen av Azure för Linux | Microsoft Docs
description: Hur du ställer in NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Linux i Azure
services: virtual-machines-linux
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/24/2018
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2bd9f8508f67a3c4b87533fb514854b5f66a5f6b
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/02/2018
ms.locfileid: "48017271"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installera NVIDIA GPU-drivrutiner på N-serien virtuella datorer som kör Linux

Om du vill dra nytta av GPU-funktioner för Azure N-serien virtuella datorer som kör Linux, måste NVIDIA GPU-drivrutiner installeras. Den [NVIDIA GPU-drivrutinen tillägget](../extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA CUDA- eller NÄTVERKSBASERADE drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg som Azure CLI eller Azure Resource Manager-mallar. Se den [NVIDIA GPU-drivrutinen tillägget dokumentation](../extensions/hpccompute-gpu-linux.md) för distributioner som stöds och distributionssteg.

Om du väljer att installera GPU-drivrutiner manuellt, innehåller den här artikeln distributioner som stöds, drivrutiner och steg för installation och kontroll. Manuell installation av drivrutinsinformation är också tillgängligt för [Windows virtuella datorer](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

N-serien Virtuella specifikationer, lagringskapacitet och diskinformation, finns i [GPU Linux VM-storlekar](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Installera CUDA drivrutiner på virtuella datorer i N-serien

Här följer stegen för att installera CUDA drivrutiner från NVIDIA CUDA Toolkit på virtuella datorer i N-serien. 


C och C++-utvecklare kan också installera fullständig Toolkit för att bygga accelererat GPU program. Mer information finns i den [CUDA installationsguide](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Om du vill installera CUDA-drivrutiner, skapar du en SSH-anslutning till varje virtuell dator. Kontrollera att systemet har en CUDA-kompatibla GPU genom att köra följande kommando:

```bash
lspci | grep -i NVIDIA
```
Du ser utdata som liknar följande exempel (visar ett NVIDIA Tesla K80-kort):

![lspci kommandoutdata](./media/n-series-driver-setup/lspci.png)

Sedan kör installationskommandon som är specifika för din distribution.

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Hämta och installera CUDA-drivrutiner.
  ```bash
  CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

  wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

  sudo dpkg -i /tmp/${CUDA_REPO_PKG}

  sudo apt-key adv --fetch-keys http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo apt-get update

  sudo apt-get install cuda-drivers

  ```

  Installationen kan ta flera minuter.

2. För att du kan också installera komplett CUDA-verktyg, skriver du:

  ```bash
  sudo apt-get install cuda
  ```

3. Starta om den virtuella datorn och fortsätta med att verifiera installationen.

#### <a name="cuda-driver-updates"></a>CUDA drivrutinsuppdateringar

Vi rekommenderar att du regelbundet uppdaterar CUDA drivrutiner efter distributionen.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux-73-or-74"></a>CentOS eller Red Hat Enterprise Linux 7.3 eller 7.4

1. Uppdatera kernel (rekommenderas). Om du väljer att inte uppdatera kerneln, kontrollera att versionerna av `kernel-devel` och `dkms` är lämpliga för din kernel.

  ```
  sudo yum install kernel kernel-tools kernel-headers kernel-devel
  
  sudo reboot

2. Install the latest [Linux Integration Services for Hyper-V and Azure](https://www.microsoft.com/download/details.aspx?id=55106).

  ```bash
  wget https://aka.ms/lis
 
  tar xvzf lis
 
  cd LISISO
 
  sudo ./install.sh
 
  sudo reboot
  ```
 
3. Ansluta till den virtuella datorn igen och fortsätta installationen med följande kommandon:

  ```bash
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

  sudo yum install dkms

  CUDA_REPO_PKG=cuda-repo-rhel7-10.0.130-1.x86_64.rpm

  wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

  sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

  rm -f /tmp/${CUDA_REPO_PKG}

  sudo yum install cuda-drivers
  ```

  Installationen kan ta flera minuter. 

4. För att du kan också installera komplett CUDA-verktyg, skriver du:

  ```bash
  sudo yum install cuda
  ```

5. Starta om den virtuella datorn och fortsätta med att verifiera installationen.

### <a name="verify-driver-installation"></a>Kontrollera drivrutinsinstallation

Fråga enhetstillstånd GPU SSH till den virtuella datorn och kör den [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyg som installeras med drivrutinen. 

Om drivrutinen har installerats, visas utdata som liknar följande. Observera att **GPU-Util** visar 0% om du använder en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversion och GPU-detaljer för din kan skilja sig från de som visas.

![NVIDIA Enhetsstatus](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktiveras på RDMA-kompatibla virtuella datorer i N-serien som NC24r distribuerats i samma tillgänglighetsuppsättning eller i en enda placeringsgrupp i en VM-skalningsuppsättning. RDMA-nätverk stöder Message Passing Interface (MPI)-trafik för program som körs med Intel MPI 5.x eller en senare version. Ytterligare krav som följer:

### <a name="distributions"></a>Distributioner

Distribuera RDMA-kompatibla N-serien virtuella datorer från en avbildning i Azure Marketplace som stöder RDMA-anslutning på virtuella datorer i N-serien:
  
* **Ubuntu 16.04 LTS** – konfigurera RDMA drivrutiner på den virtuella datorn och registrera med Intel att ladda ned Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-baserade 7.4 HPC** -drivrutiner för RDMA och Intel MPI 5.1 är installerade på den virtuella datorn.

## <a name="install-grid-drivers-on-nv-or-nvv2-series-vms"></a>Installera GRID-drivrutiner på NV eller NVv2-seriens virtuella datorer

Om du vill installera NVIDIA GRID-drivrutiner på NV eller NVv2-seriens virtuella datorer, skapar du en SSH-anslutning till varje virtuell dator och följ anvisningarna för din Linux-distribution. 

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

1. Kör `lspci`-kommandot. Kontrollera att NVIDIA M60-kort eller kort visas som PCI-enheter.

2. Installera uppdateringar.

  ```bash
  sudo apt-get update

  sudo apt-get upgrade -y

  sudo apt-get dist-upgrade -y

  sudo apt-get install build-essential ubuntu-desktop -y
  ```
3. Inaktivera Nouveau kernel-drivrutin som inte är kompatibel med NVIDIA-drivrutin. (Endast använda NVIDIA-drivrutin på NV eller NVv2 virtuella datorer.) Gör detta genom att skapa en fil i `/etc/modprobe.d `med namnet `nouveau.conf` med följande innehåll:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```


4. Starta om den virtuella datorn och ansluta igen. Avsluta X-server:

  ```bash
  sudo systemctl stop lightdm.service
  ```

5. Hämta och installera drivrutinen GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 

6. När du blir tillfrågad om du vill köra verktyget nvidia xconfig Välj för att uppdatera konfigurationsfilen X **Ja**.

7. När installationen är klar kopierar du /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats/etc/nvidia /

  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```

8. Lägg till följande till `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=FALSE
  ```
9. Starta om den virtuella datorn och fortsätta med att verifiera installationen.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux 

1. Uppdatera kernel och DKMS (rekommenderas). Om du väljer att inte uppdatera kerneln, kontrollera att versionerna av `kernel-devel` och `dkms` är lämpliga för din kernel.
 
  ```bash  
  sudo yum update
 
  sudo yum install kernel-devel
 
  sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
  sudo yum install dkms
  ```

2. Inaktivera Nouveau kernel-drivrutin som inte är kompatibel med NVIDIA-drivrutin. (Endast använda NVIDIA-drivrutin på NV eller NV2 virtuella datorer.) Gör detta genom att skapa en fil i `/etc/modprobe.d `med namnet `nouveau.conf` med följande innehåll:

  ```
  blacklist nouveau

  blacklist lbm-nouveau
  ```
 
3. Starta om den virtuella datorn, återansluta och installera senast [Linux Integration-tjänster för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
  ```bash
  wget https://aka.ms/lis

  tar xvzf lis

  cd LISISO

  sudo ./install.sh

  sudo reboot

  ```
 
4. Återansluta till den virtuella datorn och kör den `lspci` kommando. Kontrollera att NVIDIA M60-kort eller kort visas som PCI-enheter.
 
5. Hämta och installera drivrutinen GRID:

  ```bash
  wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

  chmod +x NVIDIA-Linux-x86_64-grid.run

  sudo ./NVIDIA-Linux-x86_64-grid.run
  ``` 
6. När du blir tillfrågad om du vill köra verktyget nvidia xconfig Välj för att uppdatera konfigurationsfilen X **Ja**.

7. När installationen är klar kopierar du /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats/etc/nvidia /
  
  ```bash
  sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
  ```
  
8. Lägg till följande till `/etc/nvidia/gridd.conf`:
 
  ```
  IgnoreSP=FALSE
  ```
9. Starta om den virtuella datorn och fortsätta med att verifiera installationen.

### <a name="verify-driver-installation"></a>Kontrollera drivrutinsinstallation


Fråga enhetstillstånd GPU SSH till den virtuella datorn och kör den [nvidia smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyg som installeras med drivrutinen. 

Om drivrutinen har installerats, visas utdata som liknar följande. Observera att **GPU-Util** visar 0% om du använder en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversion och GPU-detaljer för din kan skilja sig från de som visas.

![NVIDIA Enhetsstatus](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11 server
Om du behöver en X11 server för fjärranslutningar till en NV eller NVv2 VM [x11vnc](http://www.karlrunge.com/x11vnc/) rekommenderas eftersom det tillåter maskinvaruacceleration av grafik. BusID M60-enhetens måste läggas till manuellt i X11 konfigurationsfilen (vanligtvis `etc/X11/xorg.conf`). Lägg till en `"Device"` avsnittet som liknar följande:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Dessutom uppdatera din `"Screen"` avsnitt för att använda den här enheten.
 
Decimaltecknet BusID kan hittas genom att köra

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID kan ändras när en virtuell dator hämtar tilldelas på nytt eller startas om. Därför kan du skapa ett skript för att uppdatera BusID i X11 konfigurationen när en virtuell dator startas om. Till exempel skapa ett skript som heter `busidupdate.sh` (eller ett annat namn som du väljer) med innehåll som liknar följande:

```bash 
#!/bin/bash
XCONFIG="/etc/X11/xorg.conf"
OLDBUSID=`awk '/BusID/{gsub(/"/, "", $2); print $2}' ${XCONFIG}`
NEWBUSID=`nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'`

if [[ "${OLDBUSID}" == "${NEWBUSID}" ]] ; then
        echo "NVIDIA BUSID not changed - nothing to do"
else
        echo "NVIDIA BUSID changed from \"${OLDBUSID}\" to \"${NEWBUSID}\": Updating ${XCONFIG}" 
        sed -e 's|BusID.*|BusID          '\"${NEWBUSID}\"'|' -i ${XCONFIG}
fi
```

Skapa sedan en post för uppdatering skriptet i `/etc/rc.d/rc3.d` så anropas skriptet som rot vid start.

## <a name="troubleshooting"></a>Felsökning

* Du kan ange persistence med hjälp av `nvidia-smi` så kommandots utdata är snabbare när du vill fråga kort. Om du vill ange persitenceläge köra `nvidia-smi -pm 1`. Observera att om den virtuella datorn har startats om inställningen för domänläge försvinner. Du kan alltid skript inställningen för domänläge till att köra vid starten.

## <a name="next-steps"></a>Nästa steg

* Om du vill samla in en Linux VM-avbildning med din installerade NVIDIA-drivrutiner, se [generalisera och avbilda en virtuell Linux-dator](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
