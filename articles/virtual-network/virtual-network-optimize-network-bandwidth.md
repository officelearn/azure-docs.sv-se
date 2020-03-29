---
title: Optimera vm-nätverksdataflöde | Microsoft-dokument
description: Lär dig hur du optimerar Azure-nätverksdataflödet för virtuella datorer.
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
ms.openlocfilehash: be5f38bdeaf51dbe23006ecf30b4deb66aa7402a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75690889"
---
# <a name="optimize-network-throughput-for-azure-virtual-machines"></a>Optimera nätverksdataflödet för virtuella Azure-datorer

Virtuella Azure-datorer (VM) har standardnätverksinställningar som kan optimeras ytterligare för nätverksdataflöde. I den här artikeln beskrivs hur du optimerar nätverksdataflödet för virtuella Microsoft Azure Windows- och Linux-datorer, inklusive större distributioner som Ubuntu, CentOS och Red Hat.

## <a name="windows-vm"></a>Virtuell Windows-dator

Om din virtuella Windows-dator stöder [accelererat nätverk](create-vm-accelerated-networking-powershell.md)är det optimal konfiguration för dataflöde att aktivera den funktionen. För alla andra virtuella Windows-datorer kan rss (Receive Side Scaling) nå högre maximalt dataflöde än en virtuell dator utan RSS. RSS may be disabled by default in a Windows VM. Så här tar du reda på om RSS är aktiverat och aktiverar den om den är inaktiverad:

1. Se om RSS är aktiverat för `Get-NetAdapterRss` ett nätverkskort med PowerShell-kommandot. I följande exempelutdata som `Get-NetAdapterRss`returneras från , är RSS inte aktiverat.

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                 : False
    ```
2. Om du vill aktivera RSS anger du följande kommando:

    ```powershell
    Get-NetAdapter | % {Enable-NetAdapterRss -Name $_.Name}
    ```
    Det föregående kommandot har ingen utdata. Kommandot ändrade nätverkskortsinställningarna, vilket orsakade tillfällig anslutningsförlust i ungefär en minut. Dialogrutan Återanslut visas under anslutningsförlusten. Anslutningen återställs vanligtvis efter det tredje försöket.
3. Bekräfta att RSS är aktiverat i den `Get-NetAdapterRss` virtuella datorn genom att ange kommandot igen. Om det lyckas returneras följande exempelutdata:

    ```powershell
    Name                    : Ethernet
    InterfaceDescription    : Microsoft Hyper-V Network Adapter
    Enabled                  : True
    ```

## <a name="linux-vm"></a>Virtuell Linux-dator

RSS is always enabled by default in an Azure Linux VM. Linux-kärnor som släppts sedan oktober 2017 innehåller nya alternativ för nätverksoptimeringar som gör det möjligt för en Virtuell Linux-dator att uppnå högre nätverksdataflöde.

### <a name="ubuntu-for-new-deployments"></a>Ubuntu för nya driftsättningar

Ubuntu Azure-kärnan ger den bästa nätverksprestandan på Azure och har varit standardkärnan sedan den 21 september 2017. För att få den här kärnan installerar du först den senaste versionen av 16.04-LTS som stöds enligt följande:

```json
"Publisher": "Canonical",
"Offer": "UbuntuServer",
"Sku": "16.04-LTS",
"Version": "latest"
```

När skapandet är klart anger du följande kommandon för att hämta de senaste uppdateringarna. De här stegen fungerar även för virtuella datorer som för närvarande kör Ubuntu Azure-kärnan.

```bash
#run as root or preface with sudo
apt-get -y update
apt-get -y upgrade
apt-get -y dist-upgrade
```

Följande valfria kommandouppsättning kan vara till hjälp för befintliga Ubuntu-distributioner som redan har Azure-kärnan men som inte har lyckats uppdatera ytterligare med fel.

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

#### <a name="ubuntu-azure-kernel-upgrade-for-existing-vms"></a>Ubuntu Azure-kärnuppgradering för befintliga virtuella datorer

Betydande dataflödesprestanda kan uppnås genom att uppgradera till Azure Linux-kärnan. Kontrollera din kärnversion för att kontrollera om du har den här kärnan.

```bash
#Azure kernel name ends with "-azure"
uname -r

#sample output on Azure kernel:
#4.13.0-1007-azure
```

Om den virtuella datorn inte har Azure-kärnan börjar versionsnumret vanligtvis med "4.4". Om den virtuella datorn inte har Azure-kärnan kör du följande kommandon som rot:

```bash
#run as root or preface with sudo
apt-get update
apt-get upgrade -y
apt-get dist-upgrade -y
apt-get install "linux-azure"
reboot
```

### <a name="centos"></a>CentOS

För att få de senaste optimeringarna är det bäst att skapa en virtuell dator med den senaste versionen som stöds genom att ange följande parametrar:

```json
"Publisher": "OpenLogic",
"Offer": "CentOS",
"Sku": "7.4",
"Version": "latest"
```

Nya och befintliga virtuella datorer kan dra nytta av att installera de senaste Linux Integration Services (LIS). Dataflödesoptimeringen finns i LIS, från 4.2.2-2, även om senare versioner innehåller ytterligare förbättringar. Ange följande kommandon för att installera den senaste LIS:

```bash
sudo yum update
sudo reboot
sudo yum install microsoft-hyper-v
```

### <a name="red-hat"></a>Red Hat

För att få optimeringar är det bäst att skapa en virtuell dator med den senaste versionen som stöds genom att ange följande parametrar:

```json
"Publisher": "RedHat"
"Offer": "RHEL"
"Sku": "7-RAW"
"Version": "latest"
```

Nya och befintliga virtuella datorer kan dra nytta av att installera de senaste Linux Integration Services (LIS). Dataflödesoptimeringen finns i LIS från 4.2. Ange följande kommandon för att hämta och installera LIS:

```bash
wget https://aka.ms/lis
tar xvf lis
cd LISISO
sudo ./install.sh #or upgrade.sh if prior LIS was previously installed
```

Läs mer om Linux Integration Services Version 4.2 för Hyper-V genom att visa [nedladdningssidan](https://www.microsoft.com/download/details.aspx?id=55106).

## <a name="next-steps"></a>Nästa steg
* Se det optimerade resultatet med [bandbredd/dataflöde som testar Azure VM](virtual-network-bandwidth-testing.md) för ditt scenario.
* Läs om hur [bandbredd allokeras till virtuella datorer](virtual-machine-network-throughput.md)
* Läs mer med [Vanliga frågor och svar om Azure Virtual Network (Faq)](virtual-networks-faq.md)
