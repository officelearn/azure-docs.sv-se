---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: 9fe3b66de83ebc2cd0bf3a56a45456668c069191
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60301164"
---
1. Kopiera installationsprogrammet till en lokal mapp (exempelvis C:\Temp) på den server som du vill skydda. Kör följande kommandon som en administratör i en kommandotolk:

   ```
   cd C:\Temp
   ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
   MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
   cd C:\Temp\Extracted.
   ```
2. För att installera Mobilitetstjänsten, kör du följande kommando:

   ```
   UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
   ```
3. Agenten måste nu registreras med konfigurationsservern.

   ```
   cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
   UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
   ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Mobility Service installer kommandoradsargument

```
Usage :
UnifiedAgent.exe /Role <MS|MT> /InstallLocation <Install Location> /Platform “VmWare” /Silent
```

| Parameter|Typ|Beskrivning|Möjliga värden|
|-|-|-|-|
|/ Role|Obligatorisk|Anger om Mobility Service (MS) som ska installeras eller MasterTarget (MT) ska installeras.|MS </br> MT|
|/InstallLocation|Valfri|Plats där Mobilitetstjänsten är installerad.|Vilken mapp på datorn som helst|
|/ Plattform|Obligatorisk|Anger plattformen där Mobilitetstjänsten är installerad. </br> </br>- **VMware**: Använd det här värdet om du installerar Mobilitetstjänsten på en virtuell dator som körs på *VMware vSphere ESXi-värdar*, *Hyper-V-värdar*, och *fysiska servrar*. </br> - **Azure**: Använd det här värdet om du installerar en agent på en Azure IaaS-VM. | VMware </br> Azure|
|/ Tyst|Valfri|Anger om du vill köra installationsprogrammet i tyst läge.| Gäller inte|

>[!TIP]
> Installationsloggarna finns under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.

#### <a name="mobility-service-registration-command-line-arguments"></a>Mobility Service registrering kommandoradsargument

```
Usage :
UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
```

  | Parameter|Typ|Beskrivning|Möjliga värden|
  |-|-|-|-|
  |/CSEndPoint |Obligatorisk|IP-adressen för konfigurationsservern| En giltig IP-adress|
  |/PassphraseFilePath|Obligatorisk|Platsen för lösenordet |Alla giltiga UNC eller lokal filsökväg|


>[!TIP]
> Agentkonfiguration-loggarna finns under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
