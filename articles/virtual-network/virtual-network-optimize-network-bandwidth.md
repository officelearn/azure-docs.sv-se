---
title: "Optimera genomflödet för VM-nätverket | Microsoft Docs"
description: "Lär dig att optimera virtuella Azure-datorn dataflödet i nätverket."
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: 
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/24/2017
ms.author: steveesp
ms.openlocfilehash: 914747983d4d974810836be66d6c6af343f58b60
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimera dataflödet i nätverket för virtuella Azure-datorer

Virtuella Azure-datorer (VM) har standardinställningar för nätverket som kan optimeras ytterligare för dataflödet i nätverket. Den här artikeln beskriver hur du optimerar dataflödet i nätverket för Microsoft Azure Windows och Linux virtuella datorer, inklusive större distributioner, till exempel Ubuntu och CentOS Red Hat.

## <a name="windows-vm"></a>Windows VM

Om din Windows-VM stöds med [snabbare nätverk](virtual-network-create-vm-accelerated-networking.md), aktivera funktionen är den bästa konfigurationen för genomströmning. För alla andra virtuella Windows-datorer, skalning på mottagarsidan (RSS) kan du nå ut högre maximal genomströmning än en virtuell dator utan RSS. RSS kan inaktiveras som standard i en virtuell Windows-dator. Utför följande steg för att fastställa om RSS är aktiverat och aktivera den om den är inaktiverad.

1. Ange den `Get-NetAdapterRss` PowerShell-kommandot för att se om RSS är aktiverat för ett nätverkskort. I följande exempel utdata returneras från den `Get-NetAdapterRss`, RSS har inte aktiverats.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : False
    ```
2. Ange följande kommando för att aktivera RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Föregående kommando saknar utdata. Kommandot Ändra inställningarna för nätverkskortet, orsaka förlust av tillfälliga anslutning för ungefär en minut. En Reconnecting dialogruta visas under den anslutningen inte bryts. Anslutningen återställs vanligtvis efter det tredje försöket.
3. Bekräfta att RSS är aktiverat på den virtuella datorn genom att ange den `Get-NetAdapterRss` kommandot igen. Om det lyckas, returneras i följande exempel på utdata:

    ```powershell
    Name                    :Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Linux VM

RSS är alltid aktiverat som standard i en Azure Linux-dator. Linux-kernel som getts ut sedan januari 2017 inkluderar nya optimering Nätverksalternativ som möjliggör en Linux VM att uppnå högre dataflödet i nätverket.

### <a name="ubuntu"></a>Ubuntu

För att få optimeringen först uppdatera till den senaste versionen, från och med juni 2017, vilket är:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
När uppdateringen är klar kan du ange följande kommandon för att hämta den senaste kerneln:

```bash
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
```

Valfritt kommando:

`apt-get -y dist-upgrade`
#### <a name="ubuntu-azure-preview-kernel"></a>Ubuntu Azure Preview kernel
> [!WARNING]
> Den här förhandsversionen av Azure Linux-kernel kanske inte har samma nivå av tillgänglighet och tillförlitlighet som Marketplace-bilder och kernlar som är i allmänhet tillgänglighet utgåva. Funktionen stöds inte, kan ha begränsad kapacitet och får inte vara lika tillförlitlig som standardkernel. Använd inte den här kernel för produktionsarbetsbelastningar.

Betydande kapaciteten kan uppnås genom att installera den föreslagna Azure Linux-kerneln. Om du vill testa den här kernel, Lägg till följande rad /etc/apt/sources.list

```bash
#add this to the end of /etc/apt/sources.list (requires elevation)
deb http://archive.ubuntu.com/ubuntu/ xenial-proposed restricted main multiverse universe
```

Kör sedan följande kommandon som rot.
```bash
apt-get update
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

För att få optimeringen först uppdatera till den senaste versionen, från och med juli 2017, vilket är:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.3",
"Version": "latest"
```
När uppdateringen är klar kan du installera senaste Linux Integration Services (LIS).
Genomströmning optimering har LIS, från 4.2.2-2. Ange följande kommandon för att installera LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

För att få optimeringen först uppdatera till den senaste versionen, från och med juli 2017, vilket är:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7.3"
"Version": "7.3.2017071923"
```
När uppdateringen är klar kan du installera senaste Linux Integration Services (LIS).
Genomströmning optimering har LIS, från 4.2. Ange följande kommandon för att hämta och installera LIS:

```bash
mkdir lis4.2.2-2
cd lis4.2.2-2
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.2-2.tar.gz
tar xvzf lis-rpms-4.2.2-2.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Mer information om Linux Integration Services Version 4.2 för Hyper-V genom att visa den [hämtningssidan](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Nästa steg
* Nu när den virtuella datorn är optimerad ser resultatet med [bandbredd/dataflöde testa Azure VM](virtual-network-bandwidth-testing.md) för ditt scenario.
* Lär dig mer i [Azure Virtual Network vanliga frågor (FAQ)](virtual-networks-faq.md)
