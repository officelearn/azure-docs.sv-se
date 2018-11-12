---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 2b92aba8b9a8d8f46ae2aeac3a7bfe60a4755f9b
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51019228"
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

|Parameter|Typ|Beskrivning|Möjliga värden|
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

|Parameter|Typ|Beskrivning|Möjliga värden|
|-|-|-|-|
|-i |Obligatorisk|IP-Adressen för konfigurationsservern|Vilken giltig IP-adress som helst|
|-P |Obligatorisk|Fullständig sökväg för där anslutningen lösenordsfras-filen sparas|En giltig mapp|
