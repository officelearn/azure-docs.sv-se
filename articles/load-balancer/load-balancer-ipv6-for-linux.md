---
title: Konfigurera DHCPv6 för virtuella Linux-datorer | Microsoft Docs
description: Hur du konfigurerar DHCPv6 för virtuella Linux-datorer.
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: ''
keywords: IPv6, azure belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 6248ed2f55fb5bbcc2061af6ce1dedf2bd31ccad
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2018
ms.locfileid: "30261855"
---
# <a name="configure-dhcpv6-for-linux-vms"></a>Konfigurera DHCPv6 för virtuella Linux-datorer


Vissa bilder Linux virtuella datorer i Azure Marketplace har inte Dynamic Host Configuration Protocol version 6 (DHCPv6) konfigureras som standard. DHCPv6 måste konfigureras i Linux OS-distribution som du använder för att stödja IPv6. Olika Linux-distributioner konfigurera DHCPv6 i en mängd olika sätt, eftersom de använder olika paket.

> [!NOTE]
> Senaste SUSE Linux och virtuell CoreOS-avbildningar i Azure Marketplace har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar.

Det här dokumentet beskriver hur du aktiverar DHCPv6 så att den virtuella Linux-datorn hämtar en IPv6-adress.

> [!WARNING]
> Genom att redigera felaktigt network configuration-filer kan förlorar du åtkomst till nätverket till den virtuella datorn. Vi rekommenderar att du testar din konfigurationsändringar på datorer i icke-produktionsmiljö. Anvisningarna i den här artikeln har testats på de senaste versionerna av Linux-avbildningar i Azure Marketplace. Mer detaljerad information i dokumentationen för din egen version av Linux.

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

Senaste SUSE Linux Enterprise Server (SLES) och openSUSE avbildningar i Azure har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator som är baserad på en äldre eller anpassade SUSE bild kan du göra följande:

1. Installera den `dhcp-client` paketet, om det behövs:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Redigera den */etc/sysconfig/network/ifcfg-eth0* filen och Lägg till följande parameter:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 och openSUSE Leap

Senaste SLES och openSUSE avbildningar i Azure har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator som är baserad på en äldre eller anpassade SUSE bild kan du göra följande:

1. Redigera den */etc/sysconfig/network/ifcfg-eth0* filen och ersätter den `#BOOTPROTO='dhcp4'` parameter med följande värde:

        BOOTPROTO='dhcp'

2. Att den */etc/sysconfig/network/ifcfg-eth0* lägger du till följande parameter:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Senaste virtuell CoreOS-avbildningar i Azure har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa avbildningar. Om du har en virtuell dator baserat på en äldre eller anpassade virtuell CoreOS-avbildning, gör du följande:

1. Redigera den */etc/systemd/network/10_dhcp.network* fil:

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Förnya IPv6-adress:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
