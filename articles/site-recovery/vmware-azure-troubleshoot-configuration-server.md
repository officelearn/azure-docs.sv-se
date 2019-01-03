---
title: Felsöka problem med konfigurationsservern under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller felsökningsinformation för att distribuera konfigurationsservern för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 6c8f4fa1fdfdb18d57f001308a6b2105acf9a08d
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53788862"
---
# <a name="troubleshoot-configuration-server-issues"></a>Felsöka problem med konfigurationen av servern

Den här artikeln hjälper dig att felsöka problem när du distribuerar och hanterar den [Azure Site Recovery](site-recovery-overview.md) konfigurationsservern. Configuration server fungerar som en hanteringsserver och används för att konfigurera haveriberedskap för lokala virtuella VMware-datorer och fysiska servrar till Azure med Site Recovery. Följande avsnitt beskrivs de vanligaste felen visas när du lägger till en ny konfigurationsserver och hantering av en konfigurationsserver.

## <a name="registration-failures"></a>Registreringsfel

Källdatorn registreras med konfigurationsservern under mobility agentinstallationen. Fel under det här steget kan felsökas genom att följa riktlinjerna nedan:

1. Gå till C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. ProgramData kan vara en dold mapp. Om det inte gick att hitta, försöka ta fram mappen. Felen kan bero på flera problem.
2. Sök efter strängen ”ingen giltig IP-adress hittades”. Om strängen hittas
    - Kontrollera om begärda värd-id är samma som källdatorn.
    - Källdatorn bör ha minst en IP-adress för det fysiska nätverkskortet för registreringen med Konfigurationsservern ska lyckas.
    - Kör kommandot på källdatorn `> ipconfig /all` (för Windows-OS) och `# ifconfig -a` (för Linux-operativsystem) att hämta alla IP-adresser på källdatorn.
    - Observera att agentregistreringen kräver en giltig IP v4-adress tilldelad till det fysiska nätverkskortet.
3. Om strängen ovan inte hittas, Sök efter strängen ”orsak” = > ”NULL”. Om hittas
    - Det här felet uppstår när en tom värd används på källdatorn om för att registrera konfigurationsservern.
    - När du har löst problemen, följer du riktlinjerna [här](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) att göra registreringen manuellt.
4. Om strängen ovan inte hittas, gå till källdatorn och Kontrollera loggen C:\ProgramData\ASRSetupLogs\UploadedLogs\* ASRUnifiedAgentInstaller.log ProgramData kan vara en dold mapp. Om det inte gick att hitta, försöka ta fram mappen. Felen kan bero på flera problem. Sök efter strängen ”publicera begäran: (7) - Det gick inte att ansluta till servern ”. Om hittas
    - Lös nätverksproblem mellan datorn och configuration källservern. Kontrollera att konfigurationsservern kan nås från källdatorn med hjälp av verktyg för nätverk som ping, traceroute, webbläsare osv, se till att källdatorn är kan nå konfigurationsservern via port 443.
    - Kontrollera om det finns brandväggsregler på källdatorn blockerar anslutningen mellan källservern för datorn och konfiguration. Arbeta med i dina nätverk-administratörer att avblockera problem med anslutningen.
    - Se till att de angivna mapparna [här](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) undantas från antivirusprogrammet.
    - När du har löst problemen nätverk och försök registreringen följande riktlinjerna [här](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
5. Om det inte finns i samma log-Sök efter strängen ”begäran: (60) - peer-certifikat som inte kan autentiseras med angivna CA-certifikat ”. Om hittas 
    - Det här felet kan bero på att konfigurationen servercertifikatet har upphört att gälla eller om källdatorn har inte stöd för TLS 1.0 och ovan SSL-protokoll eller det finns en brandvägg som blockerar SSL-kommunikation mellan källservern för datorn och konfiguration.
    - Lös genom att ansluta till konfiguration av serverns IP-adress med hjälp av en webbläsare på källdatorn med hjälp av URI https://<CSIPADDRESS>: 443 /. Se till att källdatorn är kan nå konfigurationsservern via port 443.
    - Kontrollera om det finns brandväggsregler på källdatorn som ska läggas till/tas bort att kommunicera med CS källdatorn. Eftersom det kan finnas många olika brandväggsprogram, går det inte att lista ned på konfigurationerna som krävs, kan du prata med kundadministratörer för nätverket.
    - Se till att de angivna mapparna [här](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) undantas från antivirusprogrammet.  
    - När du har löst problemen och försök registreringen följande riktlinjerna [här](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).
6. I Linux, om värdet för plattform från < INSTALLATION_DIR > /etc/drscout.conf är skadad, sedan registreringen misslyckas. För att identifiera, går du till loggen /var/log/ua_install.log. Du hittar strängen ”avbryts konfiguration eftersom VM_PLATFORM värde är null eller så är inte VmWare/Azure”. Plattformen ska anges till ”VmWare” eller ”Azure”. Eftersom filen drscout.conf är skadad, rekommenderar vi att du [avinstallera](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) mobilitetsagenten och installera igen. Om un-installationen misslyckas kan du följa den stegen nedan:
    - Öppna filen Installation_Directory/uninstall.sh och kommentera ut anropet till funktionen *StopServices*
    - Öppna filen Installation_Directory/Vx/bin/avinstallation och kommentera ut anropet till funktionen `stop_services`
    - Öppna filen Installation_Directory/Fx/avinstallation och kommentera ut fullständig avsnittet som försöker att stoppa tjänsten Fx.
    - Prova nu att [avinstallera](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) mobilitetsagenten. Starta om datorn efter un-installationen och försök att installera agenten igen.

## <a name="installation-failure---failed-to-load-accounts"></a>Fel vid installation - det gick inte att läsa in konton

Det här felet uppstår när tjänsten kan inte läsa data från transportanslutningen när du installerar mobilitetsagenten och registrera med konfigurationsservern. För att lösa, se till att TLS 1.0 är aktivera på källdatorn.

## <a name="change-ip-address-of-configuration-server"></a>Ändra IP-adressen för konfigurationsservern

Det rekommenderas starkt att inte ändra IP-adressen för en konfigurationsserver. Se till att alla IP-adresser tilldelade till konfigurationsservern är statiska IP-adresser och inte DHCP IP-adresser.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-token är ogiltig

Se till att tiden på systemklockan inte är mer än 15 minuter jämfört med den lokala tiden för att undvika det här felet. Kör installationsprogrammet igen för att slutföra registreringen.

## <a name="failed-to-create-certificate"></a>Det gick inte att skapa certifikat

Det går inte att skapa ett certifikat som krävs för att autentisera Site Recovery. Kör installationsprogrammet igen när du har säkerställt att du kör installationen som lokal administratör.

## <a name="register-source-machine-with-configuration-server"></a>Registrera källdatorn med konfigurationsservern

### <a name="if-source-machine-has-windows-os"></a>Om källdatorn har Windows OS

Kör följande kommando på källdatorn

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
  ```
**Inställning** | **Detaljer**
--- | ---
Användning | UnifiedAgentConfigurator.exe /CSEndPoint <CSIP> /PassphraseFilePath <PassphraseFilePath>
Agenten konfigurationsloggar | Under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Platsen för lösenfrasen. Använd valfritt giltigt UNC eller lokal filsökväg.

### <a name="if-source-machine-has-linux-os"></a>Om källdatorn har Linux-operativsystem

Kör följande kommando på källdatorn

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <CSIP> -P /var/passphrase.txt
  ```
**Inställning** | **Detaljer**
--- | ---
Användning | CD /usr/local/ASR/Vx/bin<br/><br/> UnifiedAgentConfigurator.sh -i <CSIP> - P <PassphraseFilePath>
-i | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
-P |  Obligatorisk. Fullständig sökväg till filen där lösenfrasen sparas. Använda valfri giltig mapp