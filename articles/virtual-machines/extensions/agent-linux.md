---
title: Översikt över Azure Linux VM-agent
description: Lär dig hur du installerar och konfigurerar Linux Agent (waagent) för att hantera den virtuella datorns interaktion med Azure Fabric Controller.
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: e41de979-6d56-40b0-8916-895bf215ded6
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 10/17/2016
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 5f22fbd77069488e7aaf490f93f42cde747444a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74073856"
---
# <a name="understanding-and-using-the-azure-linux-agent"></a>Förstå och använda Azure Linux-agenten

Microsoft Azure Linux Agent (waagent) hanterar Linux-& FreeBSD-etablering och VM-interaktion med Azure Fabric Controller. Förutom att Linux-agenten tillhandahåller etableringsfunktioner, ger Azure också möjlighet att använda cloud-init för vissa Linux-OSes. Linux-agenten tillhandahåller följande funktioner för Linux- och FreeBSD IaaS-distributioner:

> [!NOTE]
> Mer information finns i [README](https://github.com/Azure/WALinuxAgent/blob/master/README.md).
> 
> 

* **Etablera bild**
  
  * Skapa ett användarkonto
  * Konfigurera SSH-autentiseringstyper
  * Distribution av SSH-offentliga nycklar och nyckelpar
  * Ange värdnamn
  * Publicera värdnamnet till plattformen DNS
  * Rapportering av SSH-värdnyckelfingeravtryck till plattformen
  * Hantering av resursdiskar
  * Formatera och montera resursdisken
  * Konfigurera växlingsutrymme
* **Nätverk**
  
  * Hanterar vägar för att förbättra kompatibiliteten med plattformens DHCP-servrar
  * Säkerställer nätverksgränssnittets stabilitet
* **Kernel**
  
  * Konfigurerar virtuella NUMA (inaktivera för `2.6.37`<)
  * Förbrukar Hyper-V-entropy för /dev/random
  * Konfigurerar SCSI-timeout för rotenheten (som kan vara fjärr)
* **Diagnostik**
  
  * Omdirigering av konsolen till den seriella porten
* **SCVMM-distributioner**
  
  * Identifierar och bootstraps VMM-agenten för Linux när du kör i en Virtual Machine Manager 2012 R2-miljö i System Center
* **Vm-tillägg**
  
  * Ingjuta komponent som skapats av Microsoft och Partners i Linux VM (IaaS) för att möjliggöra automatisering av programvara och konfiguration
  * Referensimplementering för VM-tillägg på[https://github.com/Azure/azure-linux-extensions](https://github.com/Azure/azure-linux-extensions)

## <a name="communication"></a>Kommunikation
Informationsflödet från plattformen till agenten sker via två kanaler:

* En starttidsansluten DVD för IaaS-distributioner. Den här DVD:n innehåller en OVF-kompatibel konfigurationsfil som innehåller all etableringsinformation förutom de faktiska SSH-nyckelparen.
* En TCP-slutpunkt som exponerar ett REST API som används för att hämta distributions- och topologikonfiguration.

## <a name="requirements"></a>Krav
Följande system har testats och är kända för att fungera med Azure Linux Agent:

> [!NOTE]
> Den här listan kan skilja sig från den officiella listan över system som stöds på Microsoft Azure-plattformen, enligt beskrivningen här:[https://support.microsoft.com/kb/2805216](https://support.microsoft.com/kb/2805216)
> 
> 

* CoreOS
* CentOS 6,3+
* Red Hat Enterprise Linux 6,7+
* Debian 7.0+
* Ubuntu 12,04+
* openSUSE 12.3+
* SLES 11 SP3+
* Oracle Linux 6,4+

Andra system som stöds:

* FreeBSD 10+ (Azure Linux Agent v2.0.10+)

Linux-agenten är beroende av vissa systempaket för att fungera korrekt:

* Python 2.6+
* OpenSSL 1.0+
* OpenSSH 5.3+
* Filsystem verktyg: sfdisk, fdisk, mkfs, skildes
* Lösenord verktyg: chpasswd, sudo
* Textbehandlingsverktyg: sed, grep
* Nätverksverktyg: ip-route
* Kernel-stöd för montering av UDF-filsystem.

## <a name="installation"></a>Installation
Installation med hjälp av ett RPM- eller DEB-paket från distributionens paketlagringsplats är den metod som föredras för att installera och uppgradera Azure Linux Agent. Alla [godkända distributionsleverantörer](../linux/endorsed-distros.md) integrerar Azure Linux-agentpaketet i sina avbildningar och databaser.

Se dokumentationen i [Azure Linux Agent repo på GitHub](https://github.com/Azure/WALinuxAgent) för avancerade installationsalternativ, till exempel installera från källa eller till anpassade platser eller prefix.

## <a name="command-line-options"></a>Kommandoradsalternativ
### <a name="flags"></a>Flaggor
* verbose: Öka verbositeten av det angivna kommandot
* kraft: Hoppa över interaktiv bekräftelse för vissa kommandon

### <a name="commands"></a>Kommandon
* hjälp: Visar en lista över kommandon och flaggor som stöds.
* avetablering: Försök att rengöra systemet och göra det lämpligt för ometablering. Följande åtgärd tar bort:
  
  * Alla SSH-värdnycklar (om Provisioning.RegenerateSshHostKeyPair är 'y' i konfigurationsfilen)
  * Namnserverkonfiguration i /etc/resolv.conf
  * Root lösenord från / etc / skugga (om Etablering.DeleteRootPassword är "y" i konfigurationsfilen)
  * Cachelagrade DHCP-klientlån
  * Återställer värdnamnet till localhost.localdomain

> [!WARNING]
> Avetablering garanterar inte att bilden rensas från all känslig information och lämpar sig för omfördelning.
> 
> 

* deetavision+user: Utför allt i -deetavision (ovan) och tar även bort det senast etablerade användarkontot (hämtat från /var/lib/waagent) och tillhörande data. Den här parametern är när de-etablering en avbildning som tidigare etablerats på Azure så att den kan fångas och återanvändas.
* version: Visar versionen av waagent
* serialconsole: Konfigurerar GRUB för att markera ttyS0 (den första seriella porten) som startkonsol. Detta säkerställer att kernel bootup loggar skickas till den seriella porten och görs tillgängliga för felsökning.
* demon: Kör waagent som en demon för att hantera interaktion med plattformen. Det här argumentet anges till waagent i skriptet för waagent init.
* start: Kör waagent som en bakgrundsprocess

## <a name="configuration"></a>Konfiguration
En konfigurationsfil (/etc/waagent.conf) styr waagentens åtgärder. Följande visar en exempelkonfigurationsfil:

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

Följande olika konfigurationsalternativ beskrivs. Konfigurationsalternativen är av tre typer. Boolesk, Sträng eller Heltal. De booleska konfigurationsalternativen kan anges som "y" eller "n". Det särskilda nyckelordet "Ingen" kan användas för vissa konfigurationsposter av strängtyp som följande information:

**Etablering.Aktiverad:**  
```
Type: Boolean  
Default: y
```
På så sätt kan användaren aktivera eller inaktivera etableringsfunktionen i agenten. Giltiga värden är "y" eller "n". Om etableringen är inaktiverad bevaras SSH-värd- och användarnycklar i avbildningen och alla konfigurationer som anges i Azure-etablerings-API:et ignoreras.

> [!NOTE]
> Parametern `Provisioning.Enabled` är standard "n" på Ubuntu Cloud Images som använder cloud-init för etablering.
> 
> 

**Provisioning.DeleteRootPassword:**  
```
Type: Boolean  
Default: n
```
Om den är inställd raderas rotlösenordet i -etc/shadow-filen under etableringsprocessen.

**Provisioning.regenerateSshHostKeyPair:**  
```
Type: Boolean  
Default: y
```
Om den är inställd tas alla SSH-värdnyckelpar (ecdsa, dsa och rsa) bort under etableringsprocessen från /etc/ssh/. Och ett enda nytt nyckelpar genereras.

Krypteringstypen för det nya nyckelparet kan konfigureras av posten Provisioning.SshHostKeyPairType. Vissa distributioner återskapaR SSH-nyckelpar för alla krypteringstyper som saknas när SSH-demonen startas om (till exempel vid en omstart).

**Etablering.SshHostKeyPairTyp:**  
```
Type: String  
Default: rsa
```
Detta kan ställas in på en krypteringsalgoritmtyp som stöds av SSH-demonen på den virtuella datorn. De värden som vanligtvis stöds är "rsa", "dsa" och "ecdsa". "putty.exe" i Windows stöder inte "ecdsa". Så, om du tänker använda putty.exe på Windows för att ansluta till en Linux-distribution, använd "rsa" eller "dsa".

**Provisioning.monitorHostName:**  
```
Type: Boolean  
Default: y
```
Om den är inställd övervakar waagent linux virtuella datorn för värdnamnsändringar (som returneras av kommandot "värdnamn") och uppdaterar automatiskt nätverkskonfigurationen i avbildningen för att återspegla ändringen. För att skicka namnändringen till DNS-servrarna startas nätverken om i den virtuella datorn. Detta resulterar i en kort förlust av Internet-anslutning.

**Provisioning.DecodeCustomData**  
```
Type: Boolean  
Default: n
```
Om den är inställd avkodar waagent CustomData från Base64.

**Etablering.ExecuteCustomData**  
```
Type: Boolean  
Default: n
```
Om den är inställd kör waagent CustomData efter etablering.

**Provisioning.AllowResetSysUser**
```
Type: Boolean
Default: n
```
Med det här alternativet kan lösenordet för den systemanvändare som ska återställas. standard är inaktiverad.

**Provisioning.PasswordCryptId**  
```
Type: String  
Default: 6
```
Algoritm som används av kryptan när du genererar lösenord hash.  
 1 - MD5  
 2a - Blåsfisk  
 5 - SHA-256  
 6 - SHA-512  

**Provisioning.PasswordCryptSaltLength**  
```
Type: String  
Default: 10
```
Längden på slumpmässigt salt som används vid generering av lösenordschh.

**ResourceDisk.Format:**  
```
Type: Boolean  
Default: y
```
Om den anges formateras resursdisken som tillhandahålls av plattformen och monteras av waagent om den filsystemtyp som användaren begär i "ResourceDisk.Filesystem" är något annat än "ntfs". En enda partition av typen Linux (83) görs tillgänglig på disken. Den här partitionen är inte formaterad om den kan monteras.

**ResourceDisk.Filesystem:**  
```
Type: String  
Default: ext4
```
Detta anger filsystemtypen för resursdisken. Värden som stöds varierar beroende på Linux-distribution. Om strängen är X, sedan mkfs. X bör finnas på Linux-avbildningen. SLES 11 bilder bör vanligtvis använda "ext3". FreeBSD bilder bör använda "ufs2" här.

**ResourceDisk.MountPoint:**  
```
Type: String  
Default: /mnt/resource 
```
Detta anger sökvägen där resursdisken är monterad. Resursdisken är en *tillfällig* disk och kan tömmas när den virtuella datorn avetableras.

**ResourceDisk.MountOptions**  
```
Type: String  
Default: None
```
Anger diskmonteringsalternativ som ska skickas till kommandot montera -o. Detta är en kommaavgränsad lista med värden, ex. "nodev,nosuid". Se montera(8) för mer information.

**ResourceDisk.EnableSwap:**  
```
Type: Boolean  
Default: n
```
Om den är inställd skapas en växlingsfil (/swapfile) på resursdisken och läggs till i systemväxlingsutrymmet.

**ResourceDisk.swapSizeMB:**  
```
Type: Integer  
Default: 0
```
Storleken på växlingsfilen i megabyte.

**Loggar.Utförlig:**  
```
Type: Boolean  
Default: n
```
Om den är inställd ökas logverositeten. Waagent loggar till /var/log/waagent.log och använder funktionen systemloggrotat för att rotera loggar.

**Os. EnableRDMA**  
```
Type: Boolean  
Default: n
```
Om den är inställd försöker agenten installera och läser sedan in en RDMA-kerneldrivrutin som matchar versionen av den inbyggda programvaran på den underliggande maskinvaran.

**Os. RootDeviceScsiTimeout:**  
```
Type: Integer  
Default: 300
```
Den här inställningen konfigurerar SCSI-timeouten på några sekunder på OS-disken och dataenheterna. Om den inte är inställd används systemets standardvärden.

**Os. ÖppnarslPath:**  
```
Type: String  
Default: None
```
Den här inställningen kan användas för att ange en alternativ sökväg för den öppnaslabinära som ska användas för kryptografiska åtgärder.

**HttpProxy.Host, HttpProxy.Port**  
```
Type: String  
Default: None
```
Om den är inställd använder agenten den här proxyservern för att ansluta till Internet. 

**AutoUpdate.Enabled**
```
Type: Boolean
Default: y
```
Aktivera eller inaktivera automatisk uppdatering för bearbetning av måltillstånd. standard är aktiverat.



## <a name="ubuntu-cloud-images"></a>Ubuntu molnbilder
Ubuntu Cloud Images använder [cloud-init](https://launchpad.net/ubuntu/+source/cloud-init) för att utföra många konfigurationsuppgifter som annars skulle hanteras av Azure Linux Agent. Följande skillnader gäller:

* **Provisioning.Enabled** defaults to "n" on Ubuntu Cloud Images that use cloud-init to perform provisioning tasks.
* Följande konfigurationsparametrar har ingen effekt på Ubuntu Cloud Images som använder cloud-init för att hantera resursdisken och byta utrymme:
  
  * **ResourceDisk.Format**
  * **ResourceDisk.Filsystem**
  * **ResourceDisk.MountPoint**
  * **ResourceDisk.EnableSwap**
  * **ResourceDisk.SwapSizeMB**

* Mer information finns i följande resurser för att konfigurera resursdiskens monteringspunkt och byta utrymme på Ubuntu Cloud Images under etableringen:
  
  * [Ubuntu Wiki: Konfigurera växlingspartitioner](https://go.microsoft.com/fwlink/?LinkID=532955&clcid=0x409)
  * [Injicera anpassade data i en virtuell Azure-dator](../windows/classic/inject-custom-data.md)

