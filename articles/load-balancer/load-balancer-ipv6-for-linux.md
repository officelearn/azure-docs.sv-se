---
title: Konfigurera DHCPv6 för virtuella Linux-datorer | Microsoft Docs
description: Så här konfigurerar du DHCPv6 för virtuella Linux-datorer.
services: load-balancer
documentationcenter: na
author: KumudD
keywords: IPv6, azure-belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 7ef376c044bceb14614388a72c11942869dbde07
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741631"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurera DHCPv6 för virtuella Linux-datorer


Några av Linux-avbildningar i Azure Marketplace för virtuella datorer har inte Dynamic Host Configuration Protocol version 6 (DHCPv6) konfigureras som standard. För att stödja IPv6, måste DHCPv6 konfigureras i Linux OS-distribution som du använder. Olika Linux-distributioner konfigurera DHCPv6 i en mängd olika sätt, eftersom de använder olika paket.

> [!NOTE]
> Senaste SUSE Linux- och CoreOS-avbildningar på Azure Marketplace har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar.

Det här dokumentet beskriver hur du aktiverar DHCPv6 så att din Linux-dator hämtar en IPv6-adress.

> [!WARNING]
> Genom att redigera felaktigt nätverkskonfigurationen kan förlora du åtkomst till den virtuella datorn. Vi rekommenderar att du testar din konfigurationsändringar på icke-produktionssystem. Anvisningarna i den här artikeln har testats på de senaste versionerna av Linux-avbildningar i Azure Marketplace. Mer detaljerade anvisningar finns i dokumentationen för din egen version av Linux.

## <a name="ubuntu"></a>Ubuntu

1. Redigera den */etc/dhcp/dhclient6.conf* filen och Lägg till följande rad:

        timeout 10;

2. Redigera nätverkskonfigurationen för eth0-gränssnittet med följande konfiguration:

   * På **Ubuntu 12.04 och 14.04**, redigera den */etc/network/interfaces.d/eth0.cfg* fil. 
   * På **Ubuntu 16.04**, redigera den */etc/network/interfaces.d/50-cloud-init.cfg* fil.

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Redigera den */etc/dhcp/dhclient6.conf* filen och Lägg till följande rad:

        timeout 10;

2. Redigera den */etc/network/interfaces* filen och Lägg till följande konfiguration:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS och Oracle Linux

1. Redigera den */etc/sysconfig/network* filen och Lägg till följande parameter:

        NETWORKING_IPV6=yes

2. Redigera den */etc/sysconfig/network-scripts/ifcfg-eth0* filen och Lägg till följande två parametrar:

        IPV6INIT=yes
        DHCPV6C=yes

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 och openSUSE 13

Senaste SUSE Linux Enterprise Server (SLES) och openSUSE-avbildningar i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator som är baserad på en äldre eller anpassade SUSE-avbildning kan du göra följande:

1. Installera den `dhcp-client` Paketera om det behövs:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Redigera den */etc/sysconfig/network/ifcfg-eth0* filen och Lägg till följande parameter:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 och openSUSE steg

Senaste SLES och openSUSE-avbildningar i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator som är baserad på en äldre eller anpassade SUSE-avbildning kan du göra följande:

1. Redigera den */etc/sysconfig/network/ifcfg-eth0* , och Ersätt den `#BOOTPROTO='dhcp4'` parameter med följande värde:

        BOOTPROTO='dhcp'

2. Till den */etc/sysconfig/network/ifcfg-eth0* Lägg till följande parameter:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Senaste CoreOS-avbildningar i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator baserat på en äldre eller anpassade CoreOS-avbildning kan du göra följande:

1. Redigera den */etc/systemd/network/10_dhcp.network* fil:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Förnya IPv6-adress:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
