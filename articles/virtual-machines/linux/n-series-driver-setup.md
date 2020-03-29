---
title: Konfiguration av GPU-drivrutin i Azure N-serien för Linux
description: Konfigurera NVIDIA GPU-drivrutiner för virtuella datorer i N-serien som kör Linux i Azure
services: virtual-machines-linux
author: cynthn
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2019
ms.author: cynthn
ms.openlocfilehash: b424361f318504f96a57ee67722e725fbafc6561
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78944565"
---
# <a name="install-nvidia-gpu-drivers-on-n-series-vms-running-linux"></a>Installera NVIDIA GPU-drivrutiner på virtuella datorer i N-serien som kör Linux

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien som kör Linux måste NVIDIA GPU-drivrutiner installeras. [NVIDIA GPU Driver Extension](../extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA CUDA- eller GRID-drivrutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure-portalen eller verktyg som Azure CLI- eller Azure Resource Manager-mallarna. Se [dokumentationen](../extensions/hpccompute-gpu-linux.md) för NVIDIA GPU Driver Extension för distributioner och driftsättningssteg som stöds.

Om du väljer att installera GPU-drivrutiner manuellt innehåller den här artikeln distributioner, drivrutiner och installations- och verifieringssteg som stöds. Information om manuell drivrutinsinställning är också tillgänglig för [virtuella windows-datorer](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

För VM-specifikationer i N-serien, lagringskapacitet och diskinformation finns i [GPU Linux VM-storlekar](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). 

[!INCLUDE [virtual-machines-n-series-linux-support](../../../includes/virtual-machines-n-series-linux-support.md)]

## <a name="install-cuda-drivers-on-n-series-vms"></a>Installera CUDA-drivrutiner på virtuella datorer i N-serien

Här är steg för att installera CUDA-drivrutiner från NVIDIA CUDA Toolkit på virtuella datorer i N-serien. 


C- och C++-utvecklare kan eventuellt installera hela Verktygslådan för att skapa GPU-accelererade program. Mer information finns i [CUDA-installationsguiden](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html).

Om du vill installera CUDA-drivrutiner gör du en SSH-anslutning till varje virtuell dator. Om du vill kontrollera att systemet har en GPU med CUDA-kompatibel körning kör du följande kommando:

```bash
lspci | grep -i NVIDIA
```
Du kommer att se utdata som liknar följande exempel (visar ett NVIDIA Tesla K80-kort):

![lspci kommandoutdata](./media/n-series-driver-setup/lspci.png)

Kör sedan installationskommandon som är specifika för distributionen.

### <a name="ubuntu"></a>Ubuntu 

1. Ladda ner och installera CUDA-drivrutinerna från NVIDIA:s webbplats. Till exempel för Ubuntu 16.04 LTS:
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

2. Om du vill installera hela CUDA-verktygslådan kan du också installera den kompletta CUDA-verktygslådan:

   ```bash
   sudo apt-get install cuda
   ```

3. Starta om den virtuella datorn och fortsätt att verifiera installationen.

#### <a name="cuda-driver-updates"></a>Uppdateringar av CUDA-drivrutin

Vi rekommenderar att du regelbundet uppdaterar CUDA-drivrutiner efter distributionen.

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers

sudo reboot
```

### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux

1. Uppdatera kärnan (rekommenderas). Om du väljer att inte uppdatera kärnan, `kernel-devel` `dkms` se till att versionerna av och är lämpliga för din kärna.

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

4. Om du vill installera hela CUDA-verktygslådan kan du också installera den kompletta CUDA-verktygslådan:

   ```bash
   sudo yum install cuda
   ```

5. Starta om den virtuella datorn och fortsätt att verifiera installationen.

### <a name="verify-driver-installation"></a>Verifiera installationen av drivrutiner

Om du vill fråga gpu-enhetens tillstånd, SSH till den virtuella datorn och köra [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyget installerat med drivrutinen. 

Om drivrutinen är installerad visas utdata som liknar följande. Observera att **GPU-Util** visar 0% om du inte för närvarande kör en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversionen och GPU-informationen kan skilja sig från de som visas.

![STATUS FÖR NVIDIA-enheter](./media/n-series-driver-setup/smi.png)

## <a name="rdma-network-connectivity"></a>RDMA-nätverksanslutning

RDMA-nätverksanslutning kan aktiveras på virtuella datorer i N-serien RDMA, till exempel NC24r som distribueras i samma tillgänglighetsuppsättning eller i en enda placeringsgrupp i en vm-skalningsuppsättning. RDMA-nätverket stöder MPI-trafik (Message Passing Interface) för program som körs med Intel MPI 5.x eller en senare version. Ytterligare krav följer:

### <a name="distributions"></a>Distributioner

Distribuera virtuella datorer i NMA-serien från en av avbildningarna på Azure Marketplace som stöder RDMA-anslutning på virtuella datorer i N-serien:
  
* **Ubuntu 16.04 LTS** - Konfigurera RDMA-drivrutiner på den virtuella datorn och registrera dig hos Intel för att hämta Intel MPI:

  [!INCLUDE [virtual-machines-common-ubuntu-rdma](../../../includes/virtual-machines-common-ubuntu-rdma.md)]

* **CentOS-baserade 7,4 HPC** - RDMA-drivrutiner och Intel MPI 5.1 är installerade på den virtuella datorn.

## <a name="install-grid-drivers-on-nv-or-nvv3-series-vms"></a>Installera GRID-drivrutiner på virtuella NV- eller NVv3-serie

Om du vill installera NVIDIA GRID-drivrutiner på virtuella nv- eller NVv3-seriedämpare gör du en SSH-anslutning till varje virtuell dator och följer stegen för din Linux-distribution. 

### <a name="ubuntu"></a>Ubuntu 

1. Kör `lspci`-kommandot. Kontrollera att NVIDIA M60-kortet eller -korten är synliga som PCI-enheter.

2. Installera uppdateringar.

   ```bash
   sudo apt-get update

   sudo apt-get upgrade -y

   sudo apt-get dist-upgrade -y

   sudo apt-get install build-essential ubuntu-desktop -y
   
   sudo apt-get install linux-azure -y
   ```
3. Inaktivera Nouveau-kärndrivrutinen, som är inkompatibel med NVIDIA-drivrutinen. (Använd endast NVIDIA-drivrutinen på virtuella NV- eller NVv2-datorer.) Det gör du genom `/etc/modprobe.d` att `nouveau.conf` skapa en fil med namnet med följande innehåll:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```


4. Starta om den virtuella datorn och återanslut. Avsluta X-server:

   ```bash
   sudo systemctl stop lightdm.service
   ```

5. Ladda ned och installera GRID-drivrutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 

6. När du tillfrågas om du vill köra nvidia-xconfig-verktyget för att uppdatera x-konfigurationsfilen väljer du **Ja**.

7. Efter installationen är klar, kopiera /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats /etc/nvidia/

   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```

8. Lägg till `/etc/nvidia/gridd.conf`följande i:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE
   ```
   
9. Ta bort `/etc/nvidia/gridd.conf` följande från om det finns:
 
   ```
   FeatureType=0
   ```
10. Starta om den virtuella datorn och fortsätt att verifiera installationen.


### <a name="centos-or-red-hat-enterprise-linux"></a>CentOS eller Red Hat Enterprise Linux 

1. Uppdatera kärnan och DKMS (rekommenderas). Om du väljer att inte uppdatera kärnan, `kernel-devel` `dkms` se till att versionerna av och är lämpliga för din kärna.
 
   ```bash  
   sudo yum update
 
   sudo yum install kernel-devel
 
   sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm
 
   sudo yum install dkms
   
   sudo yum install hyperv-daemons
   ```

2. Inaktivera Nouveau-kärndrivrutinen, som är inkompatibel med NVIDIA-drivrutinen. (Använd endast NVIDIA-drivrutinen på virtuella NV- eller NV2-datorer.) Det gör du genom `/etc/modprobe.d` att `nouveau.conf` skapa en fil med namnet med följande innehåll:

   ```
   blacklist nouveau

   blacklist lbm-nouveau
   ```
 
3. Starta om den virtuella datorn, återanslut och installera de senaste [Linux-integrationstjänsterna för Hyper-V och Azure](https://www.microsoft.com/download/details.aspx?id=55106).
 
   ```bash
   wget https://aka.ms/lis

   tar xvzf lis

   cd LISISO

   sudo ./install.sh

   sudo reboot

   ```
 
4. Återanslut till den `lspci` virtuella datorn och kör kommandot. Kontrollera att NVIDIA M60-kortet eller -korten är synliga som PCI-enheter.
 
5. Ladda ned och installera GRID-drivrutinen:

   ```bash
   wget -O NVIDIA-Linux-x86_64-grid.run https://go.microsoft.com/fwlink/?linkid=874272  

   chmod +x NVIDIA-Linux-x86_64-grid.run

   sudo ./NVIDIA-Linux-x86_64-grid.run
   ``` 
6. När du tillfrågas om du vill köra nvidia-xconfig-verktyget för att uppdatera x-konfigurationsfilen väljer du **Ja**.

7. Efter installationen är klar, kopiera /etc/nvidia/gridd.conf.template till en ny fil gridd.conf på plats /etc/nvidia/
  
   ```bash
   sudo cp /etc/nvidia/gridd.conf.template /etc/nvidia/gridd.conf
   ```
  
8. Lägg till `/etc/nvidia/gridd.conf`följande i:
 
   ```
   IgnoreSP=FALSE
   EnableUI=FALSE 
   ```
9. Ta bort `/etc/nvidia/gridd.conf` följande från om det finns:
 
   ```
   FeatureType=0
   ```
10. Starta om den virtuella datorn och fortsätt att verifiera installationen.


### <a name="verify-driver-installation"></a>Verifiera installationen av drivrutiner


Om du vill fråga gpu-enhetens tillstånd, SSH till den virtuella datorn och köra [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) kommandoradsverktyget installerat med drivrutinen. 

Om drivrutinen är installerad visas utdata som liknar följande. Observera att **GPU-Util** visar 0% om du inte för närvarande kör en GPU-arbetsbelastning på den virtuella datorn. Drivrutinsversionen och GPU-informationen kan skilja sig från de som visas.

![STATUS FÖR NVIDIA-enheter](./media/n-series-driver-setup/smi-nv.png)
 

### <a name="x11-server"></a>X11-server
Om du behöver en X11-server för fjärranslutningar till en VIRTUELL NV eller NVV2 rekommenderas [x11vnc](http://www.karlrunge.com/x11vnc/) eftersom det tillåter maskinvaruacceleration av grafik. BusID-enheten för M60-enheten måste läggas till manuellt i X11-konfigurationsfilen (vanligtvis). `etc/X11/xorg.conf` Lägg `"Device"` till ett avsnitt som liknar följande:
 
```
Section "Device"
    Identifier     "Device0"
    Driver         "nvidia"
    VendorName     "NVIDIA Corporation"
    BoardName      "Tesla M60"
    BusID          "PCI:0@your-BusID:0:0"
EndSection
```
 
Uppdatera dessutom avsnittet `"Screen"` så att du använder den här enheten.
 
Decimalen BusID kan hittas genom att köra

```bash
nvidia-xconfig --query-gpu-info | awk '/PCI BusID/{print $4}'
```
 
BusID kan ändras när en virtuell dator omfördelas eller startas om. Därför kanske du vill skapa ett skript för att uppdatera BusID i X11-konfigurationen när en virtuell dator startas om. Skapa till exempel ett `busidupdate.sh` skript med namnet (eller ett annat namn som du väljer) med innehåll som liknar följande:

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

Skapa sedan en post för `/etc/rc.d/rc3.d` uppdateringsskriptet så att skriptet anropas som root vid uppstart.

## <a name="troubleshooting"></a>Felsökning

* Du kan ställa in `nvidia-smi` persistensläge med så att utdata för kommandot går snabbare när du behöver fråga kort. Om du vill ange `nvidia-smi -pm 1`persistensläge kör du . Observera att om den virtuella datorn startas om försvinner lägesinställningen. Du kan alltid skriptlägesinställningen för att köras vid start.
* Om du har uppdaterat NVIDIA CUDA-drivrutinerna till den senaste versionen och upptäcker att RDMA connectivcity inte längre fungerar [installerar du om RDMA-drivrutinerna](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup#rdma-network-connectivity) för att återupprätta den anslutningen. 

## <a name="next-steps"></a>Nästa steg

* Information om hur du tar en Linux VM-avbildning med dina installerade NVIDIA-drivrutiner finns i [Så här generaliserar och fångar du en virtuell Linux-dator](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
