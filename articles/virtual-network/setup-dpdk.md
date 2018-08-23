---
title: DPDK i en Azure Linux-dator | Microsoft Docs
description: Lär dig hur du ställer in DPDK i en Linux-dator.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: ''
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: 205a1e399eadd268ffaa390a7ebb4397fda9feff
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/22/2018
ms.locfileid: "42444661"
---
# <a name="setup-dpdk-in-a-linux-virtual-machine"></a>Konfigurera DPDK i en Linux-dator

Data plan Development Kit (DPDK) på Azure erbjuder en snabbare användaren utrymme paket ramverk för prestanda-intensiva program som kringgår nätverksstacken för den virtuella datorns kernel.

Vanliga paket bearbetning med kernel nätverksstacken är avbrott som drivs. Varje gång som ett nätverksgränssnitt tar emot inkommande paket är det en kernel-avbrott att bearbeta paketet och kontext bytet från kernel utrymme till användaren utrymme. DPDK eliminerar växla kontext och interrupt driven metoden inaktuell och ersatts med implementering användaren utrymme med drivrutiner för pull-läge för behandling av snabba paketet.

DPDK består av uppsättning användare utrymme bibliotek som ger åtkomst till resurser, till exempel maskinvara, logiska kärnor, minneshantering lägre nivå och söka lägesdrivrutiner för nätverkskort.

DPDK kan köra i Azure-datorer, stöd för flera operativsystem-distributioner. DPDK tillhandahåller en KPI-differentiering i utveckla nätverksfunktion virtualisering implementeringar, i form av virtuella nätverksinstallationer (NVA) som en virtuell router, brandvägg, VPN, belastningsutjämnare, utvecklade paket core och denial of service ( DDoS) program.

## <a name="benefit"></a>Fördelar

**Högre paket per sekund (PPS)**: kringgår kernel och få kontroll av paket i Användarutrymmet minskar antalet cykel genom att eliminera kontext växel och förbättrar paket som bearbetas per sekund i Azure Linux-datorer.


## <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande distributioner från Azure-galleriet stöds:

| Linux OS     | Kernel-Version        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-Azure     |
| Ubuntu 18.04 | 4.15.0-1015-Azure     |
| SLES 15      | 4.12.14-5.5-Azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Anpassade kernel-stöd**

Referera till [korrigeringar för att skapa en anpassad Azure Linux-kernel](https://github.com/microsoft/azure-linux-kernel) för alla Linux kernel-version som inte visas, eller för ytterligare information, kontakta [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

## <a name="region-support"></a>Regionsstöd

Alla Azure-regioner stöder DPDK.

## <a name="prerequisites"></a>Förutsättningar

Accelererat nätverk måste aktiveras på en Linux-dator. Den virtuella datorn bör ha minst två nätverksgränssnitt, med ett gränssnitt för hantering. Lär dig hur du [skapa en Linux-dator med accelererat nätverk aktiverat](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Installera DPDK beroenden

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7.5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15"></a>SLES 15

**Azure kernel**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Standardkernel**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="setup-virtual-machine-environment-once"></a>Konfigurationsmiljö för virtuell dator (en gång)

1. [Ladda ned den senaste DPDK](https://core.dpdk.org/download). Version 18.02 eller senare krävs för Azure.
2. Skapa först standard-konfigurationen med `make config T=x86_64-native-linuxapp-gcc`.
3. Aktivera Mellanox PMDs in genererade konfigurationen med `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Kompilera med `make`.
5. Installera med `make install DESTDIR=<output folder>`.

# <a name="configure-runtime-environment"></a>Konfigurera körningsmiljö

Kör följande kommandon en gång, efter omstart:

1. Hugepages

   * Konfigurera hugepage genom att köra följande kommando en gång för alla numanodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Skapa en katalog för montering med `mkdir /mnt/huge`.
   *  Montera hugepages med `mount -t hugetlbfs nodev /mnt/huge`.
   *  Kontrollera hugepages reserveras med `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Det går att ändra grub-filen så att stora sidor är reserverade vid start genom att följa den [instruktioner](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) för DPDK. Instruktionen är längst ned på sidan. Vid körning i en virtuell Azure Linux-dator kan du ändra filer under /etc/config/grub.d i stället för att reservera hugepages mellan omstarter.

2. MAC och IP-adresser: Använd `ifconfig –a` att visa MAC och IP-adressen för nätverksgränssnitten. Den *VF* nätverksgränssnitt och *NETVSC* nätverksgränssnitt har samma MAC-adress, men endast den *NETVSC* nätverksgränssnittet har en IP-adress. Kör VF-gränssnitt som underordnad gränssnitt för NETVSC gränssnitt.

3. PCI-adresser

   * Ta reda på vilka PCI-adressen för *VF* med `ethtool -i <vf interface name>`.
   * Se till att testpmd inte råkar ta över VF pci-enhet för *eth0*om *eth0* har accelererat nätverk aktiverat. Om DPDK program av misstag har tagit över nätverket hanteringsgränssnitt och förlorar din SSH-anslutning, använder du seriekonsolen att avsluta DPDK program, eller för att stoppa och starta den virtuella datorn.

4. Läs in *ibuverbs* vid varje omstart med `modprobe -a ib_uverbs`. För SLES-15, även läsa in *mlx4_ib* med `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>Felsäker Kontrollpanelstillägget

DPDK program måste köra över felsäker Kontrollpanelstillägget som exponeras i Azure. Om programmet körs direkt via VF Kontrollpanelstillägget, kommer den inte får **alla** paket som är avsedd för den virtuella datorn, eftersom vissa paket visas över syntetisk gränssnittet. Kör över felsäker Kontrollpanelstillägget garanterar att programmet tar emot alla paket som är avsedd för det och även säkerställer att programmet fortsätter att köras i DPDK läge, även om VF återkallas när värden blir servad. Mer information om felsäker Kontrollpanelstillägget avser [felsäker avsökning läge drivrutinen biblioteket](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Kör testpmd

Använd `sudo` innan den *testpmd* kommando körs med rot.

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Basic: Förstånd kontroll, felsäker att initiera nätverkskortets

1. Kör följande kommandon för att starta en enskild port testpmd programmet:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Kör följande kommandon för att starta en dubbel port testpmd programmet:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Om kör med mer än 2 nätverkskort i `--vdev` argumentet följer detta mönster: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  När startats kör `show port info all` att kontrollera portinformation. Du bör se en eller två DPDK-portar som är net_failsafe (inte *net_mlx4*).
4.  Använd `start <port> /stop <port>` att starta trafik.

De tidigare kommandona start *testpmd* i interaktivt läge, vilket rekommenderas, du prova att använda vissa testpmd-kommandon.

### <a name="basic-single-sendersingle-receiver"></a>Basic: Enstaka avsändaren/enkel mottagare

Följande kommandon utskriftsserverns regelbundet paket per sekund-statistik:

1. Kör följande kommando på TX-sida:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
   ```

2. Kör följande kommando på RX-sida:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

När du kör kommandona på en virtuell dator kan du ändra *IP_SRC_ADDR* och *IP_DST_ADDR* i `app/test-pmd/txonly.c` att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. I annat fall ignoreras paket innan de når mottagaren.

### <a name="advanced-single-sendersingle-forwarder"></a>Avancerat: Enstaka avsändaren/enskild vidarebefordrare
Följande kommandon utskriftsserverns regelbundet paket per sekund-statistik:

1. Kör följande kommando på TX-sida:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device intended to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=txonly \
     --eth-peer=<port id>,<receiver peer MAC address> \
     --stats-period <display interval in seconds>
    ```

2. Kör följande kommando på FWD-sida:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address NIC1> \
     -w <pci address NIC2> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     --vdev="net_vdev_netvsc<2nd id>,iface=<2nd iface to attach to>" (you need as many --vdev arguments as the number of devices used by testpmd, in this case) \
     -- --nb-cores <number of cores to use for test pmd> \
     --forward-mode=io \
     --eth-peer=<recv port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
    ```

När du kör kommandona på en virtuell dator kan du ändra *IP_SRC_ADDR* och *IP_DST_ADDR* i `app/test-pmd/txonly.c` att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. I annat fall ignoreras paket innan de når vidarebefordraren. Du kan inte ha en tredje virtuell dator får vidarebefordrad trafik, eftersom den *testpmd* vidarebefordrare ändrar inte layer 3-adresser om du inte gör några ändringar i koden.

## <a name="references"></a>Referenser

* [EAL alternativ](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd kommandon](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
