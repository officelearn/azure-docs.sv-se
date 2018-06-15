---
title: Översikt över Azure virtuell Linux-dator | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar Linux-agenten (waagent) för att hantera den virtuella datorns interaktion med Azure-Infrastrukturkontrollanten.
services: virtual-machines-linux
documentationcenter: ''
author: danis
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 10/17/2016
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: d2fe93cba2c8b295925ce4cfa8c3017ee1373261
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
ms.locfileid: "33942769"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Förstå och använda Azure Linux-agenten

Microsoft Azure Linux-agenten (waagent) hanterar Linux och FreeBSD etablering och VM-interaktion med Azure-Infrastrukturkontrollanten. Azure tillhandahåller också möjlighet att använda molnet init för vissa Linux OSes förutom Linux-agenten är att tillhandahålla etablering funktioner. Linux-agenten finns följande funktioner för Linux och FreeBSD IaaS distributioner:

> [!NOTE]
> Mer information finns i [viktigt](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Bild etablering**
  
  * Skapa ett användarkonto
  * Konfigurera typer av SSH-autentisering
  * Distribution av offentliga SSH-nycklar och nyckelpar
  * Ange värdnamnet
  * Publicera värdnamnet för DNS-plattformen
  * Rapportering nyckel för SSH-värden för plattformen
  * Disk resurshantering
  * Formatering och monterar resurs-disk
  * Konfigurera växlingsutrymme
* **Nätverk**
  
  * Hanterar vägar för att förbättra kompatibiliteten med plattformen DHCP-servrar
  * Garanterar stabiliteten för nätverksgränssnittets namn
* **Kernel**
  
  * Konfigurerar virtuell NUMA (inaktivera för kernel <`2.6.37`)
  * Förbrukar Hyper-V entropi för /dev/random
  * Konfigurerar SCSI tidsgränser för rotenheten (som kan vara fjärråtkomst)
* **Diagnostik**
  
  * Omdirigering av konsol till seriell port
* **SCVMM-distributioner**
  
  * Identifierar och startar VMM-agenten för Linux vid körning i en miljö med System Center Virtual Machine Manager 2012 R2
* **Tillägg för virtuell dator**
  
  * Mata in komponenten som skapats av Microsoft och Partners i Linux VM (IaaS) till att programvaran och konfiguration automation
  * VM-tillägget referensimplementering på [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikation
Informationsflödet från plattform till agenten sker via två kanaler:

* En starten kopplade DVD för IaaS-distributioner. DVD-skivan innehåller ett OVF-kompatibel konfigurationsfil som innehåller alla Etableringsinformation än den faktiska SSH-keypairs.
* En TCP-slutpunkt som Exponerar en REST-API för att hämta distribution och konfiguration av topologi.

## <a name="requirements"></a>Krav
Följande datorer har testats och är känt att arbeta med Azure Linux-agenten:

> [!NOTE]
> Den här listan kan skilja sig från officiella listan över operativsystem som stöds på Microsoft Azure-plattformen som beskrivs här: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12.3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Andra operativsystem som stöds:

* FreeBSD 10 + (Azure Linux-agenten v2.0.10 +)

Linux-agenten beror på vissa Systempaket för att fungera korrekt:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Verktyg för filsystem: sfdisk datorn mkfs, åtskilda
* Lösenord verktyg: chpasswd sudo
* Text som bearbetar verktyg: sed grep
* Verktyg: IP-väg
* Kernel stöd för att montera UDF-filsystem.

## <a name="installation"></a>Installation
Installation med en RPM eller ett DEB-paket från din distribution paketdatabasen är den bästa metoden för installation och uppgradering av Azure Linux-agenten. Alla de [godkända distribution providers](../linux/endorsed-distros.md) integrera Azure Linux-agenten i sina avbildningar och databaser.

Dokumentationen i den [lagringsplatsen för Azure Linux-agenten på GitHub](https://github.com/Azure/WALinuxAgent) för avancerade installationsalternativ, till exempel installera från källan eller anpassade platser eller prefix.

## <a name="command-line-options"></a>Kommandoradsalternativ
### <a name="flags"></a>Flaggor
* utförlig: öka mängden information i angivna kommandot
* tvinga: hoppa över interaktiva bekräftelse för vissa kommandon

### <a name="commands"></a>Kommandon
* hjälp: Listar de kommandon som stöds och flaggor.
* avetablering: försök att rensa systemet och gör den lämplig för reprovisioning. I följande åtgärden tar bort:
  
  * Alla SSH-värdnycklar (om Provisioning.RegenerateSshHostKeyPair är ”y” i konfigurationsfilen)
  * NameServer konfigurationen i /etc/resolv.conf
  * Rotlösenordet från/etc/Shadow (om Provisioning.DeleteRootPassword är ”y” i konfigurationsfilen)
  * Cachelagrade DHCP-klientlån
  * Återställer värdnamnet till localhost.localdomain

> [!WARNING]
> Avetablering garanterar inte att avbildningen är avmarkerad av all känslig information och lämpar sig för omfördelning.
> 
> 

* avetablering + användare: utför allt i - deprovision (ovan) och även tar bort det senaste kontot för etablerad användare (hämtades från /var/lib/waagent) och tillhörande data. Den här parametern är att etablera en avbildning som tidigare etablering i Azure så att den kan avbildas och återanvänds.
* version: Visar vilken version av waagent
* serialconsole: konfigurerar GRUB om du vill markera ttyS0 (första seriell port) som start-konsol. Detta säkerställer att kernel Autostart loggar skickas till seriell port och tillgängliga för felsökning.
* Daemon: kört waagent som en daemon för att hantera interaktion med plattformen. Det här argumentet har angetts till waagent i skriptet waagent init.
* Starta: kört waagent i bakgrunden

## <a name="configuration"></a>Konfiguration
En konfigurationsfil (/ etc/waagent.conf) styr åtgärder för waagent. Nedan visas ett exempelkonfigurationsfilen:

    ```
    Provisioning.Enabled=y
    Provisioning.DeleteRootPassword=n
    Provisioning.RegenerateSshHostKeyPair=y
    Provisioning.SshHostKeyPairType=rsa
    Provisioning.MonitorHostName=y
    Provisioning.DecodeCustomData=n
    Provisioning.ExecuteCustomData=n
    Provisioning.AllowResetSysUser=n
    Provisioning.PasswordCryptId=6
    Provisioning.PasswordCryptSaltLength=10
    ResourceDisk.Format=y
    ResourceDisk.Filesystem=ext4
    ResourceDisk.MountPoint=/mnt/resource
    ResourceDisk.MountOptions=None
    ResourceDisk.EnableSwap=n
    ResourceDisk.SwapSizeMB=0
    LBProbeResponder=y
    Logs.Verbose=n
    OS.RootDeviceScsiTimeout=300
    OS.OpensslPath=None
    HttpProxy.Host=None
    HttpProxy.Port=None
    AutoUpdate.Enabled=y
    ```

Följande beskrivs olika konfigurationsalternativ. Konfigurationsalternativ som är av tre typer. Booleskt värde, en sträng eller heltal. Booleskt konfigurationsalternativen kan anges som ”y” eller ”n”. Särskilda nyckelordet ”None” kan användas för vissa sträng typen configuration poster som följande information:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Detta används att aktivera eller inaktivera funktionen etablering i agenten. Giltiga värden är ”y” eller ”n”. Om etablering inaktiveras SSH-värden och användaren nycklar i avbildningen bevaras och eventuella konfiguration som angetts i Azure API-etablering ignoreras.

> [!NOTE]
> Den `Provisioning.Enabled` ”n” på Ubuntu molnet bilder som använder molnet init för etablering som standard.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Om mängd rotlösenordet i shadow-filen/etc/tas bort under etableringen.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Om mängd alla SSH värden nyckelpar (ecdsa, dsa och rsa) tas bort under etableringen från/etc/ssh /. Och en enda ny nyckelpar genereras.

Krypteringstyp för ny nyckelparet kan konfigureras av Provisioning.SshHostKeyPairType posten. Vissa distributioner återskapa SSH-nyckelpar för valfri krypteringstyp som saknas när SSH-daemon startas (till exempel vid en omstart).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Detta kan ställas in att algoritmen krypteringstyp som stöds av SSH-daemon på den virtuella datorn. Värdena som vanligtvis stöds är ”rsa”, ”dsa” och ”ecdsa”. ”putty.exe” i Windows stöder inte ”ecdsa”. Så om du tänker använda putty.exe i Windows för att ansluta till en Linux-distributionen Använd ”rsa” eller ”dsa”.

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Om mängd waagent övervakar Linux-dator för hostname ändringar (som returneras av kommandot ”värdnamnet”) och automatiskt uppdatera nätverkskonfigurationen på bilden som ska ändras. För att push-namnbytet till DNS-servrar, startas nätverk på den virtuella datorn. Detta resulterar i korthet förlust av Internet-anslutning.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Om mängd waagent avkodar CustomData från Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Om mängd waagent kör CustomData efter etableringen.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Det här alternativet kan lösenordet för användaren sys återställs; Standardvärdet är inaktiverat.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritm som används av crypt vid generering av lösenords-hash.  
 1 - MD5  
 2a - Blowfish  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Längden på slumpmässiga salt som används vid generering av lösenords-hash.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Om resursen disketten plattformen är formaterad och monteras med waagent om typ av filsystem som begärts av användaren i ”ResourceDisk.Filesystem” är något annat än ”ntfs”. En partition av typen Linux (83) görs tillgänglig på disken. Den här partitionen är inte formaterad om den monteras har.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Anger typ av filsystem för resurs-disk. Värden som stöds beror på Linux-distribution. Om strängen är X, sedan mkfs. X ska finnas på Linux-avbildningen. SLES 11 bilder vanligtvis använda 'ext3'. FreeBSD bilder ska använda 'ufs2' här.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Detta anger den sökväg där resursen disken är monterad. Resurs-disken är en *tillfälliga* disk och kan tömmas när den virtuella datorn avetableras.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Anger disk monteringsalternativ som ska skickas till kommandot mount -o. Detta är en kommaavgränsad lista med värden, t.ex. 'nodev nosuid'. Se mount(8) mer information.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Om ange en växlingsfil (/ swapfile) skapas på disken för resursen och läggs till växlingsutrymme system.

**ResourceDisk.SwapSizeMB:**  
```
Type: Integer  
Default: 0
```
Storleken på växlingsfilen i megabyte.

**Logs.Verbose:**  
```
Type: Boolean  
Default: n
```
Om mängd loggen detaljnivå förstärks. Waagent loggar till /var/log/waagent.log och använder systemets logrotate funktion för att rotera loggar.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Om mängd agenten försöker installera och läsa in en RDMA kernel-drivrutinen matchar som versionen av den inbyggda programvaran i den underliggande maskinvaran.

**OS. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Den här inställningen konfigurerar SCSI-tidsgräns i sekunder för OS-disk- och enheter. Om den inte har angetts i systemet som standard.

**OS. OpensslPath:**  
```
Type: String  
Default: None
```
Den här inställningen kan användas för att ange en alternativ sökväg för openssl binära för kryptografiska åtgärder.

**HttpProxy.Host HttpProxy.Port**  
```
Type: String  
Default: None
```
Om mängd agenten använder den här proxyservern åtkomst till internet. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Aktivera eller inaktivera automatisk uppdatering för målet tillstånd bearbetning. Standardinställningen är aktiverad.



## <a name="ubuntu-cloud-images"></a>Ubuntu molnet bilder
Ubuntu molnet bilder använda [moln init](https://launchpad.net/ubuntu/+source/cloud-init) att utföra många konfigurationer som annars skulle hanteras av Azure Linux-agenten. Använd följande skillnader:

* **Provisioning.Enabled** ”n” på Ubuntu molnet bilder som använder molnet init etablering arbetsuppgifter som standard.
* Följande konfigurationsparametrar har ingen effekt på Ubuntu molnet bilder som använder molnet init att hantera resurs disken och växlingsutrymme:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Mer information finns i följande resurser för att konfigurera resursen disk monteringspunkten och växlingsutrymme på Ubuntu molnet bilder under etableringen:
  
  * [Ubuntu Wiki: Konfigurera växlingen partitioner](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Placera anpassade Data till en virtuell Azure-dator](../windows/classic/inject-custom-data.md)

