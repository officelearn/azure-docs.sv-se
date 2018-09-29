---
title: Översikt över Azure Linux-VM-agenten | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar Linux-agenten (waagent) för att hantera den virtuella datorns interaktion med Azure-Infrastrukturkontrollanten.
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
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
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 73e5e54e40223f7cd972e2bd2752add9ca82ff2b
ms.sourcegitcommit: f31bfb398430ed7d66a85c7ca1f1cc9943656678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/28/2018
ms.locfileid: "47452351"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Förstå och använda Azure Linux Agent

Microsoft Azure Linux-agenten (waagent) hanterar Linux och FreeBSD etablering och VM-interaktioner med Azure-Infrastrukturkontrollanten. Azure tillhandahåller även alternativet att använda cloud-init för vissa Linux-OSes förutom Linux-agenten är att tillhandahålla etablering funktioner. Linux-agenten tillhandahåller följande funktioner för Linux och FreeBSD IaaS distributioner:

> [!NOTE]
> Mer information finns i den [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Etablering av avbildningar**
  
  * Skapa ett användarkonto
  * Konfigurera SSH-autentiseringstyper
  * Distribution av offentliga SSH-nycklar och nyckelpar
  * Ange värdnamnet
  * Publicera värdnamnet för DNS-plattformen
  * Rapporterar viktiga fingeravtryck för SSH-värden till plattformen
  * Resurshantering för Disk
  * Formatera och montera disken resurs
  * Konfigurera växlingsutrymme
* **Nätverk**
  
  * Hanterar vägar för att förbättra kompatibiliteten med plattformen DHCP-servrar
  * Garanterar stabiliteten i namnet på nätverksgränssnittet
* **Kernel**
  
  * Konfigurerar du virtuellt NUMA (inaktivera för kernel <`2.6.37`)
  * Förbrukar Hyper-V entropi för /dev/random
  * Konfigurerar SCSI tidsgränser för rotenheten (som kan vara fjärransluten)
* **Diagnostik**
  
  * Omdirigering av konsol till den seriella porten
* **SCVMM-distributioner**
  
  * Identifierar och startar VMM-agenten för Linux när de körs i en miljö med System Center Virtual Machine Manager 2012 R2
* **VM-tillägg**
  
  * Mata in komponenten som skapats av Microsoft och partner i Linux VM (IaaS) till att programvaran och konfigurationsautomatisering
  * Referensimplementering för VM-tillägget på [https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikation
Informationsflödet från plattformen till agenten sker via två kanaler:

* En uppstart kopplade DVD för IaaS-distributioner. DVD-skivan innehåller en OVF-kompatibla konfigurationsfil som innehåller alla etableringsinformationen än den faktiska SSH-keypairs.
* En TCP-slutpunkt som exponerar ett REST-API som används för att hämta distributions- och topologi.

## <a name="requirements"></a>Krav
Följande datorer har testats och är kända för att arbeta med Azure Linux Agent:

> [!NOTE]
> Den här listan kan skilja sig från listan över officiella av operativsystem som stöds på Microsoft Azure-plattformen, enligt nedan: [http://support.microsoft.com/kb/2805216](http://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6.3 +
* Red Hat Enterprise Linux 6.7 +
* Debian 7.0 +
* Ubuntu 12.04 +
* openSUSE 12,3 +
* SLES 11 SP3 +
* Oracle Linux 6.4 +

Andra system som stöds:

* FreeBSD 10 + (Azure Linux-agenten v2.0.10 +)

Linux-agenten beror på vissa Systempaket för att fungera korrekt:

* Python 2.6 +
* OpenSSL 1.0 +
* OpenSSH 5.3 +
* Filsystem-verktyg: sfdisk fdisk, mkfs, åtskilda
* Verktyg för lösenord: chpasswd sudo
* Verktyg för textbearbetning: sed grep
* Verktyg: IP-väg
* Kernel stöd för att montera UDF-filsystem.

## <a name="installation"></a>Installation
Installationen med hjälp av en RPM- eller DEB-paketet från paketdatabasen för din distribution är den bästa metoden för att installera och uppgradera Azure Linux Agent. Alla de [godkända distribution providers](../linux/endorsed-distros.md) integrera Azure Linux-agenten i sina avbildningar och databaser.

Finns i dokumentationen i den [Azure Linux Agent lagringsplatsen på GitHub](https://github.com/Azure/WALinuxAgent) avancerade installationsalternativ, till exempel installera från källan eller till anpassade platser eller prefix.

## <a name="command-line-options"></a>Kommandoradsalternativ
### <a name="flags"></a>Flaggor
* utförlig: ökar loggfilernas detaljnivå angivna kommandot
* tvinga: hoppa över interaktiva bekräftelse för vissa kommandon

### <a name="commands"></a>Kommandon
* hjälpa: Visar en lista över kommandon som stöds och flaggor.
* avetablering: försök att rensa systemet och gör den lämplig för reprovisioning. I följande åtgärden tar bort:
  
  * Alla SSH-värdnycklar (om Provisioning.RegenerateSshHostKeyPair är ”y” i konfigurationsfilen)
  * Namnserverkonfigurationen i /etc/resolv.conf
  * Rotlösenordet från/etc/Shadow (om Provisioning.DeleteRootPassword är ”y” i konfigurationsfilen)
  * Cachelagrade lån för DHCP-klient
  * Återställer värdnamnet till localhost.localdomain

> [!WARNING]
> Avetablering garanterar inte att avbildningen är avmarkerad av all känslig information och lämpliga för Vidaredistribution.
> 
> 

* avetablering + användare: utför allt i - avetablering (ovan) och även tar bort det senast etablerade användarkontot (hämtades från /var/lib/waagent) och tillhörande data. Den här parametern är när avetableringsförbättringar en avbildning som tidigare etablering i Azure så att den kan hämtas och återanvändas.
* version: Visar vilken version av waagent
* serialconsole: konfigurerar GRUB om du vill markera ttyS0 (den första seriella porten) som start-konsol. Detta säkerställer att kernel starttillstånd loggarna skickas till den seriella porten och görs tillgängliga för felsökning.
* Daemon: kör waagent som en daemon för att hantera interaktion med plattformen. Det här argumentet anges till waagent i skriptet waagent init.
* Starta: kört waagent bakgrunden

## <a name="configuration"></a>Konfiguration
En konfigurationsfil (/ etc/waagent.conf) styr åtgärderna för waagent. Nedan visas ett exempel på konfigurationsfil:

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

Följande beskrivs olika konfigurationsalternativ. Inställningarna finns i tre typer. Booleskt, sträng eller heltal. Booleska konfigurationsalternativen kan anges som ”y” eller ”n”. Särskilda nyckelordet ”None” kan användas för vissa konfigurationsposter för sträng-typ som följande information:

**Provisioning.Enabled:**  
```
Type: Boolean  
Default: y
```
Detta gör att användaren kan aktivera eller inaktivera funktionen etablering i agenten. Giltiga värden är ”y” eller ”n”. Om etablering har inaktiverats kan SSH-värden och användaren nycklar i bild bevaras och alla konfigurationer som angetts i Azure etablering API ignoreras.

> [!NOTE]
> Den `Provisioning.Enabled` ”n” på Ubuntu molnet avbildningar som använder cloud-init för att etablera som standard.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Om uppsättningen, rotlösenordet i/etc/shadow filen tas bort under etableringen.

**Provisioning.RegenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Om uppsättningen, alla SSH värd nyckelpar (ecdsa, dsa och rsa) tas bort under etableringen från/etc/ssh /. Och en enda ny nyckelpar genereras.

Krypteringstyp för nytt nyckelpar kan konfigureras av Provisioning.SshHostKeyPairType-post. Vissa distributioner återskapa SSH-nyckelpar för valfri krypteringstyp som saknas när SSH-daemon startas (till exempel vid en omstart).

**Provisioning.SshHostKeyPairType:**  
```
Type: String  
Default: rsa
```
Detta kan ställas in till en typ av algoritm som stöds av SSH-daemon på den virtuella datorn. Vanligtvis godkända värden är ”rsa”, ”dsa” och ”ecdsa”. ”putty.exe” på Windows stöder inte ”ecdsa”. Så om du tänker använda putty.exe på Windows för att ansluta till en Linux-distribution måste använda ”rsa” eller ”dsa”.

**Provisioning.MonitorHostName:**  
```
Type: Boolean  
Default: y
```
Om uppsättningen, waagent övervakar Linux-dator för hostname-ändringar (som returneras av kommandot ”värdnamnet”) och automatiskt uppdatera nätverkskonfigurationen i bilden för att avspegla ändringen. För att kunna skicka namnbytet till DNS-servrar, startas nätverk på den virtuella datorn. Detta resulterar i korthet förlust av Internet-anslutning.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Om uppsättningen, waagent avkodar CustomData från Base64.

**Provisioning.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Om uppsättningen, waagent kör CustomData efter etableringen.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Det här alternativet kan lösenordet för användaren sys ska återställas; Standardvärdet är inaktiverad.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritm som används av crypt vid generering av lösenords-hash.  
 1 - MD5  
 2a - Blowfish  
 5 – SHA-256  
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
Om resursdisk som tillhandahålls av plattformen är formaterad och monteras med waagent om typen av filsystem som begärts av användaren i ”ResourceDisk.Filesystem” är något annat än ”ntfs”. En partition av typen Linux (83) görs tillgänglig på disken. Den här partitionen är inte formaterad om den har monteras.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Detta anger typen av filsystem för resurs-disken. Värden som stöds beror på Linux-distribution. Om strängen är X, sedan mkfs. X bör finnas på Linux-avbildning. SLES 11 avbildningar bör normalt använda 'ext3'. FreeBSD avbildningar bör använda 'ufs2' här.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Detta anger sökvägen där resursen disken är monterad. Resurs-disken är en *tillfälliga* disk och kan tömmas när Virtuellt datorn avetableras.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Anger alternativ för montering av disk som ska skickas till kommandot mount -o. Det här är en kommaavgränsad lista med värden, t.ex. 'nodev nosuid'. Se mount(8) mer information.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Om har angetts en växlingsfil (/ swapfile) skapas på resurs-disken och läggs till växlingsutrymme system.

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
Om uppsättningen, log detaljnivå förstärks. Waagent loggar till /var/log/waagent.log och använder systemets logrotate funktion för att rotera loggar.

**OS.EnableRDMA**  
```
Type: Boolean  
Default: n
```
Om uppsättningen, agenten försöker installera och sedan läsa in en RDMA-kerneldrivrutinen matchar som versionen av den inbyggda programvaran på den underliggande maskinvaran.

**OS. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Den här inställningen konfigurerar SCSI-tidsgräns i sekunder för OS-disk- och enheter. Om det inte har angetts i systemet som standard.

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
Om uppsättningen, agenten använder den här proxyservern för att få åtkomst till internet. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Aktivera eller inaktivera automatisk uppdatering för målstatusen bearbetning; standard är aktiverat.



## <a name="ubuntu-cloud-images"></a>Ubuntu Molnbilder
Ubuntu Molnbilder använda [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) att utföra flera konfigurationsåtgärder som annars skulle hanteras av Azure Linux Agent. Det gäller följande skillnader:

* **Provisioning.Enabled** standardvärdet är ”n” på Ubuntu molnet avbildningar som använder cloud-init för att utföra uppgifter för etablering.
* Följande konfigurationsparametrar har ingen effekt på Ubuntu Cloud-avbildningar som använder cloud-init för att hantera resource disken och växlingsutrymme:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filesystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Mer information finns i följande resurser för att konfigurera monteringspunkten resource disk och växlingsutrymme på Ubuntu Molnbilder under etableringen:
  
  * [Ubuntu Wiki: Konfigurera växling](http://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Infoga anpassade Data i en Azure virtuell dator](../windows/classic/inject-custom-data.md)

