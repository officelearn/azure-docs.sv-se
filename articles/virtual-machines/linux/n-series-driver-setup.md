---
title: Installation av GPU-drivrutin för Azure N-serien för Linux | Microsoft Docs
description: Konfigurera NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Linux i Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3abc221295a90dfbf7e46e3bd5bff1c8c0937162
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035017"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installera NVIDIA GPU-drivrutiner på virtuella datorer i N-serien som kör Linux

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien som kör Linux måste NVIDIA GPU-drivrutiner vara installerade. [NVidia GPU-drivrutinen](../extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure CLI eller Azure Resource Manager mallar. Se [dokumentationen för NVIDIA GPU-drivrutin](../extensions/hpccompute-gpu-linux.md) för distributioner som stöds och distributions steg.

Om du väljer att installera GPU-drivrutiner manuellt tillhandahåller den här artikeln distributioner, driv rutiner och installations-och verifierings steg som stöds. Det finns även information om manuell driv rutins installation för [virtuella Windows-datorer](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

För specifikationer för virtuella datorer i N-serien, lagrings kapacitet och disk information, se [storlekar för virtuella GPU Linux-datorer](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Installera CUDA-drivrutiner för virtuella datorer i N-serien

Här följer steg för att installera CUDA-drivrutiner från NVIDIA CUDA Toolkit på virtuella datorer i N-serien. 


C och C++ utvecklare kan också installera hela verktygs uppsättningen för att bygga GPU-accelererade program. Mer information finns i [installations guiden för CUDA](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Om du vill installera CUDA-drivrutiner gör du en SSH-anslutning till varje virtuell dator. Verifiera att systemet har en CUDA-kompatibel GPU genom att köra följande kommando:

```bash
lspci | grep -i NVIDIA
```
Du ser utdata som liknar följande exempel (visar ett NVIDIA Tesla K80-kort):

![utdata för lspci-kommandot](./media/n-series-driver-setup/lspci.png)

Kör sedan installations kommandon som är speciella för distributionen.

### <a name="ubuntu"></a>Ubuntu 

1. Hämta och installera CUDA-drivrutinerna från NVIDIA-webbplatsen. Till exempel för Ubuntu 16,04 LTS:
   ```bash
   CUDA_REPO_PKG=cuda-repo-ubuntu1604_10.0.130-1_amd64.deb

   wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

   sudo dpkg -i /tmp/${CUDA_REPO_PKG}

   sudo apt-key adv --fetch-keys https://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/7fa2af80.pub 

   rm -f /tmp/${CUDA_REPO_PKG}

   sudo apt-get update

   sudo apt-get install cuda-drivers

   ```

   Installationen kan ta flera minuter.

2. Om du vill installera hela CUDA Toolkit skriver du:

   ```bash
   sudo apt-get install cuda
   ```

3. Starta om den virtuella datorn och fortsätt att verifiera installationen.

#### <a name="cuda-driver-updates"></a>CUDA driv rutins uppdateringar

Vi rekommenderar att du regelbundet uppdaterar CUDA-drivrutiner efter distributionen.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux

1. Uppdatera kärnan (rekommenderas). Om du väljer att inte uppdatera kärnan kontrollerar du att versionerna av `kernel-devel` och `dkms` är lämpliga för din kernel.

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
 
3. Återanslut till den virtuella datorn och fortsätt installationen med följande kommandon:

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

4. Om du vill installera hela CUDA Toolkit skriver du:

   ```bash
   sudo yum install cuda
   ```

5. Starta om den virtuella datorn och fortsätt att verifiera installationen.

### <a name="verify-driver-installation"></a>Verifiera installation av driv rutin

För att fråga GPU-enhetens tillstånd, SSH till den virtuella datorn och köra kommando rads verktyget [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) installerat med driv rutinen. 

Om driv rutinen är installerad visas utdata som liknar följande. Observera att **GPU-util** visar 0% om du för närvarande kör en GPU-arbetsbelastning på den virtuella datorn. Driv rutins versionen och GPU-informationen kan skilja sig från de som visas.

![Status för NVIDIA-enhet](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktive ras på virtuella datorer med RDMA-kapacitet, till exempel NC24r som distribuerats i samma tillgänglighets uppsättning eller i en enda placerings grupp i en skalnings uppsättning för virtuella datorer. RDMA-nätverket stöder MPI-trafik (Message Passing Interface) för program som körs med Intel MPI 5. x eller en senare version. Ytterligare krav följer:

### <a name="distributions"></a>Distributioner

Distribuera RDMA-kompatibla virtuella datorer i N-serien från en av avbildningarna på Azure Marketplace som har stöd för RDMA-anslutning på virtuella datorer i N-serien:
  
* **Ubuntu 16,04 LTS** – konfigurera RDMA-drivrutiner på den virtuella datorn och registrera dig för Intel för att ladda ned Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-baserade 7,4 HPC** -drivrutiner och Intel MPI 5,1 är installerade på den virtuella datorn.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Installera RUTNÄTs driv rutiner på virtuella datorer med NV eller NVv3-serien

Om du vill installera NVIDIA GRID-drivrutiner på virtuella datorer med NV eller NVv3-serien gör du en SSH-anslutning till varje virtuell dator och följer stegen för din Linux-distribution. 

### <a name="ubuntu"></a>Ubuntu 

1. Kör `lspci`-kommandot. Kontrol lera att kortet eller korten i NVIDIA M60 visas som PCI-enheter.

2. Installera uppdateringar.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Inaktivera Nouveau kernel-drivrutinen, som inte är kompatibel med NVIDIA-drivrutinen. (Använd bara NVIDIA-drivrutinen på NV-eller NVv2 virtuella datorer.) Det gör du genom att skapa en fil i `/etc/modprobe.d` med namnet `nouveau.conf` med följande innehåll:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Starta om den virtuella datorn och återanslut. Avsluta X-Server:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Hämta och installera RUTNÄTs driv rutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. När du tillfrågas om du vill köra nvidia-xconfig-verktyget för att uppdatera X-konfigurationsfilen väljer du **Ja**.

7. När installationen är klar kopierar du/etc/NVIDIA/gridd.conf.template till en ny fil med rutnätet. conf på plats/etc/NVIDIA/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Lägg till följande i `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Ta bort följande från `/etc/nvidia/gridd.conf` om det finns:
 
   ```
   FeatureType=0
   ```
10. Starta om den virtuella datorn och fortsätt att verifiera installationen.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux 

1. Uppdatera kernel-och DKMS (rekommenderas). Om du väljer att inte uppdatera kärnan kontrollerar du att versionerna av `kernel-devel` och `dkms` är lämpliga för din kernel.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Inaktivera Nouveau kernel-drivrutinen, som inte är kompatibel med NVIDIA-drivrutinen. (Använd bara NVIDIA-drivrutinen på NV-eller NV2 virtuella datorer.) Det gör du genom att skapa en fil i `/etc/modprobe.d` med namnet `nouveau.conf` med följande innehåll:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Starta om den virtuella datorn, återanslut och installera de senaste [Linux-integrerings tjänsterna för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Återanslut till den virtuella datorn och kör kommandot `lspci`. Kontrol lera att kortet eller korten i NVIDIA M60 visas som PCI-enheter.
 
5. Hämta och installera RUTNÄTs driv rutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. När du tillfrågas om du vill köra nvidia-xconfig-verktyget för att uppdatera X-konfigurationsfilen väljer du **Ja**.

7. När installationen är klar kopierar du/etc/NVIDIA/gridd.conf.template till en ny fil med rutnätet. conf på plats/etc/NVIDIA/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Lägg till följande i `/etc/nvidia/gridd.conf`:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Ta bort följande från `/etc/nvidia/gridd.conf` om det finns:
 
   ```
   FeatureType=0
   ```
10. Starta om den virtuella datorn och fortsätt att verifiera installationen.


### <a name="verify-driver-installation"></a>Verifiera installation av driv rutin


För att fråga GPU-enhetens tillstånd, SSH till den virtuella datorn och köra kommando rads verktyget [NVIDIA-SMI](https://developer.nvidia.com/nvidia-system-management-interface) installerat med driv rutinen. 

Om driv rutinen är installerad visas utdata som liknar följande. Observera att **GPU-util** visar 0% om du för närvarande kör en GPU-arbetsbelastning på den virtuella datorn. Driv rutins versionen och GPU-informationen kan skilja sig från de som visas.

![Status för NVIDIA-enhet](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>Begäran om x11-Server
Om du behöver en begäran om x11-Server för fjärr anslutningar till en NV-eller NVv2-VM rekommenderas [x11vnc](http://www.karlrunge.com/x11vnc/) eftersom den tillåter grafikens maskin varu acceleration. BusID för den M60-enheten måste läggas till manuellt i begäran om x11-konfigurations filen (vanligt vis `etc/X11/xorg.conf`). Lägg till ett `"Device"`-avsnitt som liknar följande:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Uppdatera dessutom `"Screen"`-avsnittet för att använda den här enheten.
 
Du kan hitta decimal BusID genom att köra

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID kan ändras när en virtuell dator tilldelas eller startas om. Därför kanske du vill skapa ett skript för att uppdatera BusID i begäran om x11-konfigurationen när en virtuell dator startas om. Du kan till exempel skapa ett skript med namnet `busidupdate.sh` (eller ett annat namn som du väljer) med innehåll som liknar följande:

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

Skapa sedan en post för ditt uppdaterings skript i `/etc/rc.d/rc3.d` så att skriptet anropas som rot vid start.

## <a name="troubleshooting"></a>Felsökning

* Du kan ställa in persistence-läge med `nvidia-smi` så att kommandots utdata går snabbare när du behöver fråga kort. Kör `nvidia-smi -pm 1` om du vill ställa in persistence-läge. Observera att om den virtuella datorn startas om så går läges inställningen bort. Du kan alltid skripta läges inställningen så att den körs vid start.
* Om du har uppdaterat NVIDIA CUDA-drivrutinerna till den senaste versionen och hittar RDMA-connectivcity fungerar inte längre, [installerar du om RDMA-drivrutinerna](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) för att reistablish anslutningen. 

## <a name="next-steps"></a>Nästa steg

* Om du vill avbilda en virtuell Linux-avbildning med dina installerade NVIDIA-drivrutiner, se [hur man generaliserar och fångar in en virtuell Linux-dator](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
