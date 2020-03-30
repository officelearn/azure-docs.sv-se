---
title: Konfigurera DHCPv6 för virtuella Linux-datorer
titleSuffix: Azure Load Balancer
description: I den här artikeln kan du lära dig hur du konfigurerar DHCPv6 för virtuella Linux-datorer.
services: load-balancer
documentationcenter: na
author: asudbring
keywords: ipv6, azure load balancer, dual stack, public ip, native ipv6, mobile, iot
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/22/2019
ms.author: allensu
ms.openlocfilehash: 6ea215b6aa826231e940f88c3687bb65591303f2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74225321"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurera DHCPv6 för virtuella Linux-datorer


Vissa av Linux-avbildningarna för virtuella datorer på Azure Marketplace har inte dynamic host configuration protocol version 6 (DHCPv6) konfigurerad som standard. För att stödja IPv6 måste DHCPv6 konfigureras i Linux OS-distributionen som du använder. De olika Linux-distributionerna konfigurerar DHCPv6 på en mängd olika sätt eftersom de använder olika paket.

> [!NOTE]
> De senaste SUSE Linux- och CoreOS-avbildningarna på Azure Marketplace har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder.

I det här dokumentet beskrivs hur du aktiverar DHCPv6 så att din virtuella Linux-dator får en IPv6-adress.

> [!WARNING]
> Genom att felaktigt redigera nätverkskonfigurationsfiler kan du förlora nätverksåtkomst till den virtuella datorn. Vi rekommenderar att du testar dina konfigurationsändringar på icke-produktionssystem. Instruktionerna i den här artikeln har testats på de senaste versionerna av Linux-avbildningarna på Azure Marketplace. Mer detaljerade instruktioner finns i dokumentationen för din egen version av Linux.

## <a name="ubuntu"></a>Ubuntu

1. Redigera filen */etc/dhcp/dhclient6.conf* och lägg till följande rad:

        timeout 10;

2. Redigera nätverkskonfigurationen för eth0-gränssnittet med följande konfiguration:

   * På **Ubuntu 12.04 och 14.04**redigerar du filen */etc/network/interfaces.d/eth0.cfg.* 
   * På **Ubuntu 16.04**redigerar du filen */etc/network/interfaces.d/50-cloud-init.cfg.*

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```
Från och med Ubuntu 17.10 är standardkonfigurationsmekanismen för nätverkskonfiguration [NETPLAN]( https://netplan.io).  Vid installation/instansieringstid läser NETPLAN nätverkskonfiguration från YAML-konfigurationsfiler på den här platsen: /{lib,etc,run}/netplan/*.yaml.

Inkludera en *dhcp6:true-sats* för varje Ethernet-gränssnitt i konfigurationen.  Ett exempel:
  
        network:
          version: 2
          ethernets:
            eno1:
              dhcp6: true

Under tidig start skriver netplan "network renderer" konfiguration till /run to hand off control of devices to the https://netplan.io/referenceanspecified networking daemon For reference information about NETPLAN, see .
 
## <a name="debian"></a>Debian

1. Redigera filen */etc/dhcp/dhclient6.conf* och lägg till följande rad:

        timeout 10;

2. Redigera filen */etc/network/interfaces* och lägg till följande konfiguration:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel-centos-and-oracle-linux"></a>RHEL, CentOS och Oracle Linux

1. Redigera nätverksfilen */etc/sysconfig/och* lägg till följande parameter:

        NETWORKING_IPV6=yes

2. Redigera filen */etc/sysconfig/network-scripts/ifcfg-eth0* och lägg till följande två parametrar:

        IPV6INIT=yes
        DHCPV6C=yes

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11-and-opensuse-13"></a>SLES 11 och openSUSE 13

De senaste SUSE Linux Enterprise Server (SLES) och openSUSE-avbildningarna i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder. Om du har en virtuell dator som baseras på en äldre eller anpassad SUSE-avbildning gör du följande:

1. Installera `dhcp-client` paketet om det behövs:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Redigera filen */etc/sysconfig/network/ifcfg-eth0* och lägg till följande parameter:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 och openSUSE Leap

De senaste SLES- och openSUSE-avbildningarna i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder. Om du har en virtuell dator som baseras på en äldre eller anpassad SUSE-avbildning gör du följande:

1. Redigera filen */etc/sysconfig/network/ifcfg-eth0* och `#BOOTPROTO='dhcp4'` ersätt parametern med följande värde:

        BOOTPROTO='dhcp'

2. Lägg till följande parameter i filen */etc/sysconfig/network/ifcfg-eth0:*

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adressen:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

De senaste CoreOS-avbildningarna i Azure har förkonfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder. Om du har en virtuell dator baserad på en äldre eller anpassad CoreOS-avbildning gör du följande:

1. Redigera filen */etc/systemd/network/10_dhcp.network:*

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Förnya IPv6-adressen:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
