---
title: Installera Mobilitetstjänsten manuellt för haveriberedskap för virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Lär dig hur du installerar mobilitetstjänstagenten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: d0180a32f2a4258cb728869e2cb9f2abd57cc545
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52845968"
---
# <a name="install-the-mobility-service-manually-on-vmware-vms-and-physical-servers"></a>Installera mobilitetstjänsten manuellt på virtuella VMware-datorer och fysiska servrar

När du ställer in katastrofåterställning för virtuella VMware-datorer och fysiska servrar med [Azure Site Recovery](site-recovery-overview.md), du installerar den [Site Recovery-mobilitetstjänsten](vmware-physical-mobility-service-overview.md) på varje virtuell dator med en lokal VMware och fysiska servrar.  Mobilitetstjänsten samlar in skrivna data på datorn och vidarebefordrar dem till processervern för Site Recovery.

Den här artikeln beskriver hur du installerar mobilitetstjänsten manuellt på varje dator som du vill skydda.

## <a name="create-a-passphrase"></a>Skapa en lösenfras

Innan du installerar måste du skapa en lösenfras som ska användas under installationen.

1. Logga in på konfigurationsservern.
2. Öppna en kommandotolk som administratör.
3. Ändra katalogen till bin-mappen och sedan skapa en lösenfras-fil.

    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    genpassphrase.exe -v > MobSvc.passphrase
    ```
3. Store lösenfrasfilen på en säker plats. 


## <a name="install-the-service-from-the-ui"></a>Installera tjänsten i användargränssnittet

>[!IMPORTANT]
> Om du replikerar virtuella Azure IaaS-datorer från en Azure-region till en annan, Använd inte den här metoden. Använda kommandoradsbaserad Command-Line-baserad installation metoden insteadl.

1. Leta upp installer-version som du behöver för datorns operativsystem. Installationsprogram för finns i mappen %ProgramData%\ASR\home\svsystems\pushinstallsvc\repository. [Kontrollera](vmware-physical-mobility-service-overview.md#installer-files) vilket installationsprogram som du behöver.
2. Kopiera installationsfilen till datorn och köra den.
3. I **installationsalternativet**väljer **installation av mobilitetstjänsten**.
4. Välj installationsplats > **installera**.

    ![Installationssidan för alternativet på Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility1.png)

5. Övervaka installationen i **Installationsförlopp**. När installationen är klar, väljer **fortsätta konfigurationen** att registrera tjänsten med konfigurationsservern.

    ![Mobility Service-registreringssidan](./media/vmware-physical-mobility-service-install-manual/mobility3.png)

6.  i **information om konfigurationsservern**, ange IP-adressen och lösenfras som du har konfigurerat.  

    ![Mobility Service-registreringssidan](./media/vmware-physical-mobility-service-install-manual/mobility4.png)

7. Välj **registrera** att slutföra registreringen.

    ![Sista sidan Mobility Service](./media/vmware-physical-mobility-service-install-manual/mobility5.png)

## <a name="install-the-service-from-the-command-prompt"></a>Installera tjänsten från Kommandotolken

### <a name="on-a-windows-machine"></a>På en Windows-dator

1. Kopiera installationsprogrammet till en lokal mapp (exempelvis C:\Temp) på den server som du vill skydda. 

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Installera på följande sätt:

  ```
  UnifiedAgent.exe /Role "MS" /InstallLocation "C:\Program Files (x86)\Microsoft Azure Site Recovery" /Platform "VmWare" /Silent
  ```

3. Registrera agenten med konfigurationsservern.

  ```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```

#### <a name="installation-settings"></a>Inställningar för klientinstallationer
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgent.exe/role < MS|MT > /InstallLocation  <Install Location> /Platform ”VmWare” / silent
Installationsloggar | Under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentInstaller.log.
/ Role | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmål (MT) ska installeras.
/InstallLocation| Valfri parameter. Anger installationsplatsen för Mobility service (valfri mapp).
/ Plattform | Obligatorisk. Anger plattformen där Mobilitetstjänsten är installerad. **VMware** för Mware datorer/fysiska servrar. **Azure** för virtuella Azure-datorer. 
/ Tyst| Valfri. Anger om du vill köra installationsprogrammet i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgentConfigurator.exe /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Agenten konfigurationsloggar | Under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Platsen för lösenfrasen. Använd valfritt giltigt UNC eller lokal filsökväg.


### <a name="on-a-linux-machine"></a>På en Linux-dator

1. Kopiera installationsprogrammet till en lokal mapp (till exempel/tmp) på den server som du vill skydda. Kör följande kommandon i en terminal:
  ```
  cd /tmp ;

  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Installera på följande sätt:

  ```
  sudo ./install -d <Install Location> -r MS -v VmWare -q
  ```
3. När installationen är klar, måste Mobilitetstjänsten vara registrerad till konfigurationsservern. Kör följande kommando för att registrera Mobilitetstjänsten med konfigurationsservern:

  ```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```


#### <a name="installation-settings"></a>Inställningar för klientinstallationer
**Inställning** | **Detaljer**
--- | ---
Användning | . / install -d <Install Location> - r < MS|MT > - v VmWare - q
-r | Obligatorisk installationsparameter. Anger om mobilitetstjänsten (MS) eller huvudmål (MT) ska installeras.
-d | Valfri parameter. Anger installationsplatsen Mobility service: /usr/local/ASR.
-v | Obligatorisk. Anger plattformen där Mobilitetstjänsten är installerad. **VMware** för Mware datorer/fysiska servrar. **Azure** för virtuella Azure-datorer. 
-q | Valfri. Anger om du vill köra installationsprogrammet i tyst läge.

#### <a name="registration-settings"></a>Registreringsinställningar
**Inställning** | **Detaljer**
--- | ---
Användning | CD /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
-P |  Obligatorisk. Fullständig sökväg till filen där lösenfrasen sparas. Använda valfri giltig mapp

## <a name="next-steps"></a>Nästa steg
- [Konfigurera haveriberedskap för virtuella VMware-datorer](vmware-azure-tutorial.md)
- [Konfigurera haveriberedskap för fysiska servrar](physical-azure-disaster-recovery.md)
