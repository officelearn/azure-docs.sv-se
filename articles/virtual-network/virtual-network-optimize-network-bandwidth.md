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
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 2f7a65d32f662d7e265e58c5fe7d9dea81a4e63c
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimera dataflödet i nätverket för virtuella Azure-datorer

Virtuella Azure-datorer (VM) har standardinställningar för nätverket som kan optimeras ytterligare för dataflödet i nätverket. Den här artikeln beskriver hur du optimerar dataflödet i nätverket för Microsoft Azure Windows och Linux virtuella datorer, inklusive större distributioner, till exempel Ubuntu och CentOS Red Hat.

## <a name="windows-vm"></a>Windows VM

Om din Windows-VM stöds med [snabbare nätverk](virtual-network-create-vm-accelerated-networking.md), aktivera funktionen är den bästa konfigurationen för genomströmning. För alla andra virtuella Windows-datorer, skalning på mottagarsidan (RSS) kan du nå ut högre maximal genomströmning än en virtuell dator utan RSS. RSS kan inaktiveras som standard i en virtuell Windows-dator. Utför följande steg för att fastställa om RSS är aktiverat och aktivera den om den är inaktiverad.

1. Ange den `Get-NetAdapterRss` PowerShell-kommandot för att se om RSS är aktiverat för ett nätverkskort. I följande exempel utdata returneras från den `Get-NetAdapterRss`, RSS har inte aktiverats.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Ange följande kommando för att aktivera RSS:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Föregående kommando saknar utdata. Kommandot Ändra inställningarna för nätverkskortet, orsaka förlust av tillfälliga anslutning för ungefär en minut. En Reconnecting dialogruta visas under den anslutningen inte bryts. Anslutningen återställs vanligtvis efter det tredje försöket.
3. Bekräfta att RSS är aktiverat på den virtuella datorn genom att ange den `Get-NetAdapterRss` kommandot igen. Om det lyckas, returneras i följande exempel på utdata:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled              : True
    ```

## <a name="linux-vm"></a>Linux VM

RSS är alltid aktiverat som standard i en Azure Linux-dator. Linux-kernel som getts ut sedan oktober 2017 inkluderar nya optimeringar Nätverksalternativ som möjliggör en Linux VM att uppnå högre dataflödet i nätverket.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu för nya distributioner

Kernel Ubuntu Azure ger bästa nätverksprestanda på Azure och är standardkernel sedan 21 September 2017. För att få den här kernel måste först installera senaste version som stöds av 16.04 LTS, som beskrivs nedan:
```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```
Ange följande kommandon för att hämta de senaste uppdateringarna när de har skapats. De här stegen fungerar även för virtuella datorer körs Ubuntu Azure-kernel.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Följande valfria kommandouppsättning kan vara användbar för befintliga Ubuntu-distributioner som redan har Azure kerneln, men som inte har ytterligare uppdateringar med fel.

```bash
#optional steps may be helpful in existing deployments with the Azure kernel
#run as root or preface with sudo
apt-get -f install
apt-get --fix-missing install
apt-get clean
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel uppgradering för befintliga virtuella datorer

Betydande kapaciteten kan uppnås genom att uppgradera till Azure Linux-kärnan. Kontrollera om du har den här kernel, kontrollera kernel-version.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.11.0-1014-azure
```

Om den virtuella datorn inte har Azure kernel versionsnumret vanligtvis att börja med ”4.4”. I sådana fall kan du köra följande kommandon som rot.
```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

För att få de senaste Optimeringarna, är det bäst att skapa en virtuell dator med den senaste versionen genom att ange följande parametrar:
```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```
Nya och befintliga virtuella datorer kan utnyttja installera senaste Linux Integration Services (LIS).
Genomströmning optimering har LIS, från 4.2.2-2, även om senare versioner innehåller ytterligare förbättringar. Ange följande kommandon för att installera den senaste LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

För att få Optimeringarna, är det bäst att skapa en virtuell dator med den senaste versionen genom att ange följande parametrar:
```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```
Nya och befintliga virtuella datorer kan utnyttja installera senaste Linux Integration Services (LIS).
Genomströmning optimering har LIS, från 4.2. Ange följande kommandon för att hämta och installera LIS:

```bash
mkdir lis4.2.3-1
cd lis4.2.3-1
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-1.tar.gz
tar xvzf lis-rpms-4.2.3-1.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Mer information om Linux Integration Services Version 4.2 för Hyper-V genom att visa den [hämtningssidan](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Nästa steg
* Nu när den virtuella datorn är optimerad ser resultatet med [bandbredd/dataflöde testa Azure VM](virtual-network-bandwidth-testing.md) för ditt scenario.
* Lär dig mer i [Azure Virtual Network vanliga frågor (FAQ)](virtual-networks-faq.md)
