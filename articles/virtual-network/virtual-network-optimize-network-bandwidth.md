---
title: Optimera nätverkets dataflöde VM | Microsoft Docs
description: Lär dig mer om att optimera nätverkets dataflöde för Azure-dator.
services: virtual-network
documentationcenter: na
author: steveesp
manager: Gerald DeGrace
editor: ''
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/15/2017
ms.author: steveesp
ms.openlocfilehash: 50d7ca73e5e18f88f5d789e12fc7f26908e8b8f0
ms.sourcegitcommit: f6050791e910c22bd3c749c6d0f09b1ba8fccf0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50025556"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimera nätverkets dataflöde för Azure-datorer

Azure virtuella datorer (VM) har standardinställningarna för nätverk som kan optimeras ytterligare för dataflöde i nätverket. Den här artikeln beskriver hur du optimera nätverkets dataflöde för Microsoft Azure Windows och Linux virtuella datorer, inklusive större distributioner som Red Hat, Ubuntu och CentOS.

## <a name="windows-vm"></a>Virtuell Windows-dator

Om din virtuella Windows-dator stöder [Accelerated Networking](create-vm-accelerated-networking-powershell.md), aktivering av funktionen skulle vara den bästa konfigurationen för dataflöde. För alla Windows-datorer nå med hjälp av skalning på mottagarsidan (RSS) högre maximal genomströmning än en virtuell dator utan RSS. RSS inaktiveras som standard i en virtuell Windows-dator. För att avgöra om skalning på Mottagarsidan är aktiverat och aktiverar det om det är inaktiverat, gör du följande:

1. Om RSS är aktiverat för ett nätverkskort med den `Get-NetAdapterRss` PowerShell-kommando. I följande Exempelutdata returneras från den `Get-NetAdapterRss`, RSS är inte aktiverad.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Om du vill aktivera RSS, anger du följande kommando:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Föregående kommando har inte utdata. Kommandot Ändra inställningarna för nätverkskortet, orsakar tillfälliga anslutningsförlust för ungefär en minut. En dialogruta för Reconnecting visas under till anslutningsproblemet. Anslutningen återställs vanligtvis efter tredje försöket.
3. Bekräfta att RSS är aktiverat på den virtuella datorn genom att ange den `Get-NetAdapterRss` -kommandot på nytt. Om detta lyckas, returneras följande Exempelutdata:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Virtuell Linux-dator

RSS är alltid aktiverat som standard i en virtuell Linux-dator. Linux-kernel som getts ut sedan oktober 2017 innehåller nya optimeringar Nätverksalternativ som gör att en Linux VM att uppnå högre dataflöde i nätverket.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu för nya distributioner

Ubuntu Azure-kerneln innehåller bästa nätverksprestanda i Azure och är standardkernel sedan 21 September 2017. För att få den här kernel måste först installera den senaste versionen av 16.04 LTS, enligt följande:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

När datafabriken har skapats, anger du följande kommandon för att få de senaste uppdateringarna. De här stegen fungerar även för virtuella datorer som körs på Ubuntu Azure-kernel.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Följande valfria kommandouppsättning kan vara till hjälp för befintliga Ubuntu-distributioner som redan har Azure kernel men som inte har ytterligare uppdateringar med fel.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure kernel uppgraderingen för befintliga virtuella datorer

Betydande dataflödesprestanda kan uppnås genom att uppgradera till Azure Linux-kernel. Kontrollera vilken kernel-version för att kontrollera om du har den här kernel.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Om den virtuella datorn inte har Azure kernel börjar versionsnumret vanligtvis med ”4.4”. Om den virtuella datorn inte har Azure kerneln, kör du följande kommandon som rot:

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

Nya och befintliga virtuella datorer kan dra nytta av installera senaste Linux Integration Services (LIS). Genomflödesoptimeringsjobb genomgår LIS, med början från 4.2.2-2, även om senare versioner innehåller ytterligare förbättringar. Ange följande kommandon för att installera den senaste LIS:

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

Nya och befintliga virtuella datorer kan dra nytta av installera senaste Linux Integration Services (LIS). Genomflödesoptimeringsjobb genomgår LIS, med början från 4.2. Ange följande kommandon för att ladda ned och installera LIS:

```bash
mkdir lis4.2.3-5
cd lis4.2.3-5
wget https://download.microsoft.com/download/6/8/F/68FE11B8-FAA4-4F8D-8C7D-74DA7F2CFC8C/lis-rpms-4.2.3-5.tar.gz
tar xvzf lis-rpms-4.2.3-5.tar.gz
cd LISISO
install.sh #or upgrade.sh if prior LIS was previously installed
```

Läs mer om Linux Integration Services Version 4.2 för Hyper-V genom att visa den [hämtningssidan](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Nästa steg
* Visa optimerade resultatet med [bandbredd/dataflöde testa Azure VM](virtual-network-bandwidth-testing.md) för ditt scenario.
* Läs om hur [bandbredden som allokeras till virtuella datorer](virtual-machine-network-throughput.md)
* Lär dig mer med [Azure Virtual Network, vanliga frågor (och svar FAQ)](virtual-networks-faq.md)
