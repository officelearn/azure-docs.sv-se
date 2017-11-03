---
title: "Konfigurera DHCPv6 för virtuella Linux-datorer | Microsoft Docs"
description: "Hur du konfigurerar DHCPv6 för virtuella Linux-datorer."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
keywords: "IPv6, azure belastningsutjämnare, dual stack, offentlig IP-adress, inbyggd ipv6, mobil, iot"
ms.assetid: b32719b6-00e8-4cd0-ba7f-e60e8146084b
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 84558cb6e3a5524969f590eb0272a64ad8839ab5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configuring-dhcpv6-for-linux-vms"></a>Konfigurera DHCPv6 för virtuella Linux-datorer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Vissa av Linux virtuella avbildningar i Azure Marketplace har inte DHCPv6 konfigureras som standard. För att stödja IPv6 konfigureras DHCPv6 i inom den Linux OS-distribution som du använder. Olika Linux-distributioner har olika sätt att konfigurera DHCPv6 eftersom de använder olika paket.

> [!NOTE]
> Senaste SUSE Linux och virtuell CoreOS-avbildningar i Azure Marketplace har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder.

Det här dokumentet beskriver hur du aktiverar DHCPv6 så att den virtuella Linux-datorn hämtar en IPv6-adress.

> [!WARNING]
> Felaktigt redigera konfigurationsfiler för nätverket kan medföra att du förlorar åtkomst till nätverket till den virtuella datorn. Vi rekommenderar att du testar din konfigurationsändringar på datorer i icke-produktionsmiljö. Anvisningarna i den här artikeln har testats på de senaste versionerna av Linux-avbildningar i Azure Marketplace. I dokumentationen för din specifika version av Linux mer detaljerad information.

## <a name="ubuntu"></a>Ubuntu

1. Redigera filen `/etc/dhcp/dhclient6.conf` och Lägg till följande rad:

        timeout 10;

2. Redigera nätverkskonfigurationen för eth0-gränssnittet med följande konfiguration:

   * På **Ubuntu 12.04 och 14.04**, redigerar du filen`/etc/network/interfaces.d/eth0.cfg`
   * På **Ubuntu 16.04**, redigerar du filen`/etc/network/interfaces.d/50-cloud-init.cfg`

         iface eth0 inet6 auto
             up sleep 5
             up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="debian"></a>Debian

1. Redigera filen `/etc/dhcp/dhclient6.conf` och Lägg till följande rad:

        timeout 10;

2. Redigera filen `/etc/network/interfaces` och Lägg till följande konfiguration:

        iface eth0 inet6 auto
            up sleep 5
            up dhclient -1 -6 -cf /etc/dhcp/dhclient6.conf -lf /var/lib/dhcp/dhclient6.eth0.leases -v eth0 || true

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="rhel--centos--oracle-linux"></a>RHEL / CentOS / Oracle Linux

1. Redigera filen `/etc/sysconfig/network` och Lägg till följande parameter:

        NETWORKING_IPV6=yes

2. Redigera filen `/etc/sysconfig/network-scripts/ifcfg-eth0` och Lägg till följande två parametrar:

        IPV6INIT=yes
        DHCPV6C=yes

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-11--opensuse-13"></a>SLES 11 & openSUSE 13

Senaste SLES och openSUSE avbildningar i Azure har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder. Om du har en virtuell dator baserat på en äldre eller anpassade SUSE avbildning, använder du följande steg:

1. Installera den `dhcp-client` paketet, om det behövs:

    ```bash
    sudo zypper install dhcp-client
    ```

2. Redigera filen `/etc/sysconfig/network/ifcfg-eth0` och Lägg till följande parameter:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="sles-12-and-opensuse-leap"></a>SLES 12 och openSUSE Leap

Senaste SLES och openSUSE avbildningar i Azure har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder. Om du har en virtuell dator baserat på en äldre eller anpassade SUSE avbildning, använder du följande steg:

1. Redigera filen `/etc/sysconfig/network/ifcfg-eth0` och ersätter den här parametern

        #BOOTPROTO='dhcp4'

    med följande värde:

        BOOTPROTO='dhcp'

2. Lägg till följande parameter till `/etc/sysconfig/network/ifcfg-eth0`:

        DHCLIENT6_MODE='managed'

3. Förnya IPv6-adress:

    ```bash
    sudo ifdown eth0 && sudo ifup eth0
    ```

## <a name="coreos"></a>CoreOS

Senaste virtuell CoreOS-avbildningar i Azure har redan konfigurerats med DHCPv6. Inga ytterligare ändringar krävs när du använder dessa bilder. Om du har en virtuell dator baserat på en äldre eller anpassade virtuell CoreOS-avbildning, använder du följande steg:

1. Redigera filen`/etc/systemd/network/10_dhcp.network`

        [Match]
        eth0

        [Network]
        DHCP=ipv6

2. Förnya IPv6-adress:

    ```bash
    sudo systemctl restart systemd-networkd
    ```
