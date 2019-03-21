---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: aa2e6d80620f0a4cf5063919a6de53e4de20f706
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58077011"
---
1. Kopiera installationsprogrammet till en lokal mapp (till exempel/tmp) på den server som du vill skydda. Kör följande kommandon i en terminal:
   ```
   cd /tmp ;

   tar -xvzf Microsoft-ASR_UA*release.tar.gz
   ```
2. För att installera Mobilitetstjänsten, kör du följande kommando:

   ```
   sudo ./install -d <Install Location> -r MS -v VmWare -q
   ```
3. När installationen är klar, måste Mobilitetstjänsten vara registrerad till konfigurationsservern. Kör följande kommando för att registrera Mobilitetstjänsten med konfigurationsservern:

   ```
   /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
   ```

#### <a name="mobility-service-installer-command-line"></a>Mobility Service installer-kommandoraden

```
Usage:
./install -d <Install Location> -r <MS|MT> -v VmWare -q
```

|Parameter|Type|Beskrivning|Möjliga värden|
|-|-|-|-|
|-r |Obligatorisk|Anger om Mobility Service (MS) som ska installeras eller MasterTarget (MT) ska installeras.|MS </br> MT|
|-d |Valfri|Plats där Mobilitetstjänsten är installerad.|/usr/local/ASR|
|-v|Obligatorisk|Anger plattformen där Mobilitetstjänsten är installerad. </br> </br>- **VMware**: Använd det här värdet om du installerar Mobilitetstjänsten på en virtuell dator som körs på *VMware vSphere ESXi-värdar*, *Hyper-V-värdar*, och *fysiska servrar*. </br> - **Azure**: Använd det här värdet om du installerar en agent på en Azure IaaS-VM.| VMware </br> Azure|
|-q|Valfri|Anger om du vill köra installationsprogrammet i tyst läge.| Gäller inte|


#### <a name="mobility-service-configuration-command-line"></a>Mobility Service configuration från kommandoraden

```
Usage:
cd /usr/local/ASR/Vx/bin
UnifiedAgentConfigurator.sh -i <CSIP> -P <PassphraseFilePath>
```

|Parameter|Type|Beskrivning|Möjliga värden|
|-|-|-|-|
|-i |Obligatorisk|IP-Adressen för konfigurationsservern|Vilken giltig IP-adress som helst|
|-P |Obligatorisk|Fullständig sökväg för där anslutningen lösenordsfras-filen sparas|En giltig mapp|
