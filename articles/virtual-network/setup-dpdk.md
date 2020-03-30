---
title: DPDK i en virtuell Azure Linux-dator | Microsoft-dokument
description: Lär dig hur du konfigurerar DPDK i en virtuell Linux-dator.
services: virtual-network
documentationcenter: na
author: laxmanrb
manager: gedegrac
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/27/2018
ms.author: labattul
ms.openlocfilehash: c79c1fd687e329b97a854a3ff66a3cf95076b5d6
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80384236"
---
# <a name="set-up-dpdk-in-a-linux-virtual-machine"></a>Konfigurera DPDK i en virtuell Linux-dator

DPDK (Data Plane Development Kit) på Azure erbjuder ett snabbare ramverk för paketbearbetning av användarutrymme för prestandaintensiva program. Det här ramverket kringgår den virtuella datorns kernel-nätverksstack.

I typisk paketbearbetning som använder kernel-nätverksstacken avbryts processen. När nätverksgränssnittet tar emot inkommande paket finns det ett kernel-avbrott för att bearbeta paketet och en kontextväxel från kernel-utrymmet till användarutrymmet. DPDK eliminerar kontextväxling och den avbrottsdrivna metoden till förmån för en implementering av användarutrymme som använder drivrutiner för avsökningsläge för snabb paketbearbetning.

DPDK består av uppsättningar med bibliotek för användarutrymme som ger åtkomst till resurser på lägre nivå. Dessa resurser kan omfatta maskinvara, logiska kärnor, minneshantering och avsökningslägesdrivrutiner för nätverkskort.

DPDK kan köras på virtuella Azure-datorer som stöder flera operativsystemdistributioner. DPDK ger nyckelprestanda differentiering i körning nätverksfunktion virtualisering implementeringar. Dessa implementeringar kan ta formen av virtuella nätverksinstallationer (NVA), till exempel virtuella routrar, brandväggar, VPN, belastningsutjämnare, utvecklade paketkärnor och DDoS-program (Denial-of-Service).

## <a name="benefit"></a>Fördelar

**Högre paket per sekund (PPS):** Att kringgå kärnan och ta kontroll över paket i användarutrymmet minskar antalet cykler genom att eliminera kontextväxlar. Det förbättrar också hastigheten på paket som bearbetas per sekund i virtuella Azure Linux-datorer.


## <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande distributioner från Azure Marketplace stöds:

| Linux OS     | Kernelversion               | 
|--------------|---------------------------   |
| Ubuntu 16.04 | 4.15.0-1014-azure+           | 
| Ubuntu 18.04 | 4.15.0-1014-azure+           |
| SLES 15 SP1  | 4.12.14-8.27-azure+          | 
| RHEL 7.5     | 3.10.0-862.11.6.el7.x86_64+  | 
| CentOS 7.5   | 3.10.0-862.11.6.el7.x86_64+  | 

**Anpassat kernel-stöd**

För alla Linux-kärnversioner som inte finns med i listan finns [i Patchar för att skapa en Azure-trimmad Linux-kärna](https://github.com/microsoft/azure-linux-kernel). Om du vill ha [azuredpdk@microsoft.com](mailto:azuredpdk@microsoft.com)mer information kan du även kontakta . 

## <a name="region-support"></a>Stöd för regionen

Alla Azure-regioner stöder DPDK.

## <a name="prerequisites"></a>Krav

Accelererade nätverk måste aktiveras på en virtuell Linux-dator. Den virtuella datorn bör ha minst två nätverksgränssnitt, med ett gränssnitt för hantering. Lär dig hur du [skapar en virtuell Linux-dator med accelererade nätverksaktiverade](create-vm-accelerated-networking-cli.md).

## <a name="install-dpdk-dependencies"></a>Installera DPDK-beroenden

### <a name="ubuntu-1604"></a>Ubuntu 16.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="ubuntu-1804"></a>Ubuntu 18.04

```bash
sudo add-apt-repository ppa:canonical-server/dpdk-azure -y
sudo apt-get update
sudo apt-get install -y librdmacm-dev librdmacm1 build-essential libnuma-dev libmnl-dev
```

### <a name="rhel75centos-75"></a>RHEL7.5/CentOS 7,5

```bash
yum -y groupinstall "Infiniband Support"
sudo dracut --add-drivers "mlx4_en mlx4_ib mlx5_ib" -f
yum install -y gcc kernel-devel-`uname -r` numactl-devel.x86_64 librdmacm-devel libmnl-devel
```

### <a name="sles-15-sp1"></a>SLES 15 SP1

**Azure-kärna**

```bash
zypper  \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-azure kernel-devel-azure gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

**Standardkärna**

```bash
zypper \
  --no-gpg-checks \
  --non-interactive \
  --gpg-auto-import-keys install kernel-default-devel gcc make libnuma-devel numactl librdmacm1 rdma-core-devel
```

## <a name="set-up-the-virtual-machine-environment-once"></a>Konfigurera den virtuella datorns miljö (en gång)

1. [Ladda ner den senaste DPDK](https://core.dpdk.org/download). Version 18.11 LTS eller 19.11 LTS krävs för Azure.
2. Skapa standardkonfigurationen `make config T=x86_64-native-linuxapp-gcc`med .
3. Aktivera Mellanox PMD:er i `sed -ri 's,(MLX._PMD=)n,\1y,' build/.config`den genererade konfigurationen med .
4. Kompilera `make`med .
5. Installera `make install DESTDIR=<output folder>`med .

## <a name="configure-the-runtime-environment"></a>Konfigurera körningsmiljön

När du har startat om kör du följande kommandon en gång:

1. Hugepages (Stora sidor)

   * Konfigurera hugepage genom att köra följande kommando, en gång för varje nakennod:

     ```bash
     echo 1024 | sudo tee /sys/devices/system/node/node*/hugepages/hugepages-2048kB/nr_hugepages
     ```

   * Skapa en katalog `mkdir /mnt/huge`för montering med .
   * Montera hugepages `mount -t hugetlbfs nodev /mnt/huge`med .
   * Kontrollera att hugepages `grep Huge /proc/meminfo`är reserverade med .

     > - Jag vet inte vad du säger. Det finns ett sätt att ändra grub-filen så att hugepages är reserverade vid uppstart genom att följa [instruktionerna](https://dpdk.org/doc/guides/linux_gsg/sys_reqs.html#use-of-hugepages-in-the-linux-environment) för DPDK. Instruktionerna finns längst ner på sidan. När du använder en virtuell Azure Linux-dator ändrar du filer under **/etc/config/grub.d** istället för att reservera hugepages över omstarter.

2. MAC & IP-adresser: `ifconfig –a` Används för att visa MAC- och IP-adressen för nätverksgränssnitten. *VF-nätverksgränssnittet* och *NETVSC-nätverksgränssnittet* har samma MAC-adress, men bara *NETVSC-nätverksgränssnittet* har en IP-adress. *VF-gränssnitt* körs som underordnade gränssnitt för *NETVSC-gränssnitt.*

3. PCI-adresser

   * Används `ethtool -i <vf interface name>` för att ta reda på vilken PCI-adress som ska användas för *VF*.
   * Om *eth0* har accelererat nätverksaktiverat, se till att testpmd inte av misstag tar över *VF* PCI-enheten för *eth0*. Om DPDK-programmet av misstag tar över gränssnittet för hanteringsnätverket och gör att du förlorar din SSH-anslutning använder du seriekonsolen för att stoppa DPDK-programmet. Du kan också använda seriekonsolen för att stoppa eller starta den virtuella datorn.

4. Ladda *ibuverbs* på `modprobe -a ib_uverbs`varje omstart med . Endast för SLES 15, ladda `modprobe -a mlx4_ib`även *mlx4_ib* med .

## <a name="failsafe-pmd"></a>Felsäker PMD

DPDK-program måste köras över det felsäkra PMD som visas i Azure. Om programmet körs direkt över *VF* PMD, tar det inte emot **alla** paket som är avsedda för den virtuella datorn, eftersom vissa paket visas över det syntetiska gränssnittet. 

Om du kör ett DPDK-program över den felsäkra PMD:en garanteras det att programmet tar emot alla paket som är avsedda för det. Det ser också till att programmet fortsätter att köras i DPDK-läge, även om VF återkallas när värden servas. Mer information om felsäker PMD finns i [drivrutinsbiblioteket för felsäkert avsökningsläge](https://doc.dpdk.org/guides/nics/fail_safe.html).

## <a name="run-testpmd"></a>Kör testpmd

Om du vill köra testpmd i rotläge använder du `sudo` före *testpmd-kommandot.*

### <a name="basic-sanity-check-failsafe-adapter-initialization"></a>Grundläggande: Sanity check, felsäker adapter initiering

1. Kör följande kommandon för att starta ett testprogram med en port:

   ```bash
   testpmd -w <pci address from previous step> \
     --vdev="net_vdev_netvsc0,iface=eth1" \
     -- -i \
     --port-topology=chained
    ```

2. Kör följande kommandon för att starta ett testprogram med dubbla portar:

   ```bash
   testpmd -w <pci address nic1> \
   -w <pci address nic2> \
   --vdev="net_vdev_netvsc0,iface=eth1" \
   --vdev="net_vdev_netvsc1,iface=eth2" \
   -- -i
   ```

   Om du kör testpmd med fler än `--vdev` två nätverkskort följer `net_vdev_netvsc<id>,iface=<vf’s pairing eth>`argumentet det här mönstret: .

3.  När den har startats kör du `show port info all` för att kontrollera portinformation. Du bör se en eller två DPDK-portar som är net_failsafe *(inte net_mlx4).*
4.  Används `start <port> /stop <port>` för att starta trafiken.

De tidigare kommandona startar *testpmd* i interaktivt läge, vilket rekommenderas för att prova testpmd kommandon.

### <a name="basic-single-sendersingle-receiver"></a>Grundläggande: Enkel avsändare/enskild mottagare

Följande kommandon skriver regelbundet ut paket per sekund statistik:

1. Kör följande kommando på TX-sidan:

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

2. Kör följande kommando på RX-sidan:

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

När du kör föregående kommandon på en virtuell dator ändrar *du* IP_SRC_ADDR `app/test-pmd/txonly.c` och *IP_DST_ADDR* in för att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. Annars tas paketen bort innan de når mottagaren.

### <a name="advanced-single-sendersingle-forwarder"></a>Avancerat: Enkel avsändare/enskild vidarebefordrare
Följande kommandon skriver regelbundet ut paket per sekund statistik:

1. Kör följande kommando på TX-sidan:

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

2. Kör följande kommando på FWD-sidan:

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

När du kör föregående kommandon på en virtuell dator ändrar *du* IP_SRC_ADDR `app/test-pmd/txonly.c` och *IP_DST_ADDR* in för att matcha den faktiska IP-adressen för de virtuella datorerna innan du kompilerar. Annars tas paketen bort innan de når vidarebefordraren. Du kan inte låta en tredje dator ta emot vidarebefordrad trafik, eftersom *den testsvariga* vidarebefordraren inte ändrar layer-3-adresserna, såvida du inte gör vissa kodändringar.

## <a name="references"></a>Referenser

* [Alternativ för EAL](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#eal-command-line-options)
* [Kommandon för testpmd](https://dpdk.org/doc/guides/testpmd_app_ug/run_app.html#testpmd-command-line-options)
