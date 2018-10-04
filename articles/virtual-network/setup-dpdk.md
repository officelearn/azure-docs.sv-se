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
ms.openlocfilehash: 34647c218bd5fd2eec775599a4d2f10373dbd2fd
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268284"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Ställ in DPDK i en Linux-dator

Data plan Development Kit (DPDK) på Azure erbjuder ett ramverk för behandling av pakethantering av snabbare Användarutrymmet för prestandakrävande program. Det här ramverket kringgår nätverksstacken för den virtuella datorns kernel.

I vanliga behandling av pakethantering som använder nätverksstacken kernel, är processen interrupt-drivna. När nätverksgränssnittet tar emot inkommande paket, finns det en kernel-avbrott att bearbeta paketet och en kontext växla från kernel-utrymme till området användare. DPDK eliminerar växla kontext och metoden interrupt-drivna inaktuell och ersatts med en Användarutrymmet-implementering som använder avsöka lägesdrivrutiner för snabb paket.

DPDK består av uppsättningar Användarutrymmet bibliotek som ger åtkomst till resurser på lägre nivå. De här resurserna kan innehålla maskinvara, logiska kärnor, minneshantering och avsökning lägesdrivrutiner för nätverkskort.

DPDK kan köras på Azure virtuella datorer som stöder flera operativsystem-distributioner. DPDK innehåller KPI differentiering föra nätverk funktionen virtualisering implementeringar. Dessa implementeringar kan utgöras av virtuella nätverksinstallationer (Nva), till exempel virtuella routrar, brandväggar, VPN, belastningsutjämnare, utvecklade paket kärnor och denial of service (DDoS) program.

## <a name="benefit"></a>Fördelar

**Högre paket per sekund (PPS)**: kringgår kernel och få kontroll av paket i Användarutrymmet minskar antalet cykel genom att eliminera kontext växlar. Det förbättrar också paket som bearbetas per sekund i Azure Linux-datorer.


## <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande distributioner från Azure-galleriet stöds:

| Linux OS     | Kernel-version        |
|--------------|----------------       |
| Ubuntu 16.04 | 4.15.0-1015-Azure     |
| Ubuntu 18.04 | 4.15.0-1015-Azure     |
| SLES 15      | 4.12.14-5.5-Azure     |
| RHEL 7.5     | 3.10.0-862.9.1.el7    |
| CentOS 7.5   | 3.10.0-862.3.3.el7    |

**Anpassade kernel-stöd**

Alla Linux kernel-version som inte visas, se [korrigeringar för att skapa en anpassad Azure Linux-kernel](https://github.com/microsoft/azure-linux-kernel). Mer information kan du även kontakta [ azuredpdk@microsoft.com ](mailto:azuredpdk@microsoft.com). 

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

## <a name="set-up-the-virtual-machine-environment-once"></a>Konfigurera VM-miljö (en gång)

1. [Ladda ned den senaste DPDK](https://core.dpdk.org/download). Version 18.02 eller senare krävs för Azure.
2. Skapa standard-konfigurationen med `make config T=x86_64-native-linuxapp-gcc`.
3. Aktivera Mellanox PMDs in genererade konfigurationen med `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`.
4. Kompilera med `make`.
5. Installera med `make install DESTDIR=<output folder>`.

## <a name="configure-the-runtime-environment"></a>Konfigurera runtime-miljö

Kör följande kommandon en gång efter omstart:

1. Hugepages

   * Konfigurera hugepage genom att köra följande kommando en gång för alla numanodes:

     ```bash
     echo 1024 | sudo tee
     /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   *  Skapa en katalog för montering med `mkdir /mnt/huge`.
   *  Montera hugepages med `mount -t hugetlbfs nodev /mnt/huge`.
   *  Kontrollera att hugepages är reserverade med `grep Huge /proc/meminfo`.

     > [!NOTE]
     > Det går att ändra grub-filen så att hugepages är reserverade vid start genom att följa den [instruktioner](http://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) för DPDK. Anvisningarna är längst ned på sidan. När du använder en virtuell Azure Linux-dator, ändra filer under **/etc/config/grub.d** i stället att reservera hugepages mellan omstarter.

2. MAC och IP-adresser: Använd `ifconfig –a` att visa MAC och IP-adressen för nätverksgränssnitten. Den *VF* nätverksgränssnitt och *NETVSC* nätverksgränssnitt har samma MAC-adress, men endast den *NETVSC* nätverksgränssnittet har en IP-adress. Kör VF-gränssnitt som underordnade gränssnitt för NETVSC gränssnitt.

3. PCI-adresser

   * Använd `ethtool -i <vf interface name>` att ta reda på vilka PCI-adressen för *VF*.
   * Om *eth0* har accelererat nätverk aktiverat, kontrollera att den testpmd inte råkar ta över VF pci-enhet för *eth0*. Om programmet DPDK av misstag tar över hanteringsgränssnitt för nätverket och du blir din SSH-anslutning, kan du använda seriekonsolen för att stoppa programmet DPDK. Du kan också använda seriekonsolen för att stoppa eller starta den virtuella datorn.

4. Läs in *ibuverbs* vid varje omstart med `modprobe -a ib_uverbs`. För SLES-15, även läsa in *mlx4_ib* med `modprobe -a mlx4_ib`.

## <a name="failsafe-pmd"></a>Felsäker Kontrollpanelstillägget

DPDK program måste köra över felsäker Kontrollpanelstillägget som exponeras i Azure. Om programmet körs direkt via VF Kontrollpanelstillägget kan den inte har tagit emot **alla** paket som är avsedd för den virtuella datorn, eftersom vissa paket som visas över syntetisk gränssnittet. 

Om du kör ett DPDK program över felsäker Kontrollpanelstillägget garanterar det att programmet tar emot alla paket som ska den. Gör det också se till att programmet köras i DPDK läge, även om VF återkallas när värden blir servad. Läs mer om felsäker Kontrollpanelstillägget [felsäker avsökning läge drivrutinen biblioteket](http://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Kör testpmd

Om du vill köra testpmd i rot-läge, Använd `sudo` innan den *testpmd* kommando.

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

   Om du kör testpmd med fler än två nätverkskort på `--vdev` argumentet följer detta mönster: `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`.

3.  När den startas kör `show port info all` att kontrollera portinformation. Du bör se en eller två DPDK-portar som är net_failsafe (inte *net_mlx4*).
4.  Använd `start <port> /stop <port>` att starta trafik.

De tidigare kommandona start *testpmd* i interaktivt läge som rekommenderas för att prova testpmd kommandon.

### <a name="basic-single-sendersingle-receiver"></a>Basic: Enstaka avsändaren/enkel mottagare

Följande kommandon utskriftsserverns regelbundet paket per sekund-statistik:

1. Kör följande kommando på TX-sida:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
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
     -w <pci address of the device you plan to use> \
     --vdev="net_vdev_netvsc<id>,iface=<the iface to attach to>" \
     -- --port-topology=chained \
     --nb-cores <number of cores to use for test pmd> \
     --forward-mode=rxonly \
     --eth-peer=<port id>,<sender peer MAC address> \
     --stats-period <display interval in seconds>
   ```

När du kör kommandona på en virtuell dator, ändra *IP_SRC_ADDR* och *IP_DST_ADDR* i `app/test-pmd/txonly.c` att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. I annat fall ignoreras paket innan de når mottagaren.

### <a name="advanced-single-sendersingle-forwarder"></a>Avancerat: Enstaka avsändaren/enskild vidarebefordrare
Följande kommandon utskriftsserverns regelbundet paket per sekund-statistik:

1. Kör följande kommando på TX-sida:

   ```bash
   testpmd \
     -l <core-list> \
     -n <num of mem channels> \
     -w <pci address of the device you plan to use> \
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

När du kör kommandona på en virtuell dator, ändra *IP_SRC_ADDR* och *IP_DST_ADDR* i `app/test-pmd/txonly.c` att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. I annat fall ignoreras paket innan de når vidarebefordraren. Du kan inte ha en tredje virtuell dator får vidarebefordrad trafik, eftersom den *testpmd* vidarebefordrare ändrar inte layer 3-adresser om du inte gör några ändringar i koden.

## <a name="references"></a>Referenser

* [EAL alternativ](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Testpmd kommandon](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
