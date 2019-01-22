---
title: Felsöka problem med konfigurationsservern under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller felsökningsinformation för att distribuera konfigurationsservern för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 01/14/2019
ms.author: ramamill
ms.openlocfilehash: 0eebfd8b75f428d3b8f6024ed6ee71c18c1309f6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54435982"
---
# <a name="troubleshoot-configuration-server-issues"></a>Felsöka problem med konfigurationen av servern

Den här artikeln hjälper dig att felsöka problem när du distribuerar och hanterar den [Azure Site Recovery](site-recovery-overview.md) konfigurationsservern. Configuration server fungerar som en hanteringsserver. Använda configuration server för att konfigurera haveriberedskap för lokala virtuella VMware-datorer och fysiska servrar till Azure med Site Recovery. I följande avsnitt beskrivs de vanligaste felen som kan uppstå när du lägger till en ny konfigurationsserver och när du hanterar en konfigurationsserver.

## <a name="registration-failures"></a>Registreringsfel

Källdatorn registreras med konfigurationsservern när du installerar mobilitetsagenten. Du kan felsöka eventuella fel under det här steget genom att följa dessa riktlinjer:

1. Öppna filen C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (Mappen ProgramData kan vara en dold mapp. Om du inte ser mappen ProgramData i Utforskaren på den **visa** fliken den **Visa/dölj** väljer den **dolda objekt** markerar du kryssrutan.) Fel kan orsakas av flera problem.

2. Sök efter strängen **ingen giltig IP-adress hittades**. Om strängen hittas:
    1. Kontrollera att den begärda värd-ID är samma som värd-ID för källdatorn.
    2. Kontrollera att källdatorn har minst en IP-adress som tilldelats det fysiska nätverkskortet. För registreringen med konfigurationsservern ska lyckas, måste källdatorn ha minst en giltig IP v4-adress tilldelad till det fysiska nätverkskortet.
    3. Kör något av följande kommandon på källdatorn för att hämta alla IP-adresser på källdatorn:
      - För Windows: `> ipconfig /all`
      - För Linux: `# ifconfig -a`

3. Om strängen **ingen giltig IP-adress hittades** inte hittas, Sök efter strängen **orsak = > NULL**. Det här felet uppstår om källdatorn använder en tom värd för att registrera med konfigurationsservern. Om strängen hittas:
    - När du har löst problemen, följer du riktlinjerna i [registrera källdatorn med konfigurationsservern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) att göra registreringen manuellt.

4. Om strängen **orsak = > NULL** hittas på källdatorn, öppna filen C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log. (Mappen ProgramData kan vara en dold mapp. Om du inte ser mappen ProgramData i Utforskaren på den **visa** fliken den **Visa/dölj** väljer den **dolda objekt** markerar du kryssrutan.) Fel kan orsakas av flera problem. 

5. Sök efter strängen **publicera begäran: (7) - Det gick inte att ansluta till servern**. Om strängen hittas:
    1. Lös nätverksproblem mellan källdatorn och konfigurationsservern. Kontrollera att konfigurationsservern kan nås från källdatorn med hjälp av verktyg för nätverk som ping, traceroute eller en webbläsare. Se till att källdatorn kan nå konfigurationsservern via port 443.
    2. Kontrollera om alla brandväggsregler på källan datorn blockera anslutningen mellan källdatorn och konfigurationsservern. Arbeta med dina nätverk-administratörer att avblockera alla problem med anslutningen.
    3. Kontrollera att mapparna i [Site Recovery mappar som ska undantas från antivirusprogram](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) undantas från antivirusprogrammet.
    4. När nätverksproblem har åtgärdats, gör om registreringen genom att följa riktlinjerna i [registrera källdatorn med konfigurationsservern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Om strängen **publicera begäran: (7) - Det gick inte att ansluta till servern** inte hittas i samma loggfil, titta efter strängen **begäran: (60) - peer-certifikat som inte kan autentiseras med angivna CA-certifikat**. Det här felet kan uppstå configuration-servercertifikatet har upphört att gälla eller källdatorn inte stöd för TLS 1.0 eller senare SSL-protokoll. Det kan också inträffa om en brandvägg blockerar SSL-kommunikation mellan källdatorn och konfigurationsservern. Om strängen hittas: 
    1. Om du vill lösa kan ansluta till konfigurationsservern IP-adress med hjälp av en webbläsare på källdatorn. Använda https för URI:\/\/< IP-adress för configuration server\>: 443 /. Se till att källdatorn kan nå konfigurationsservern via port 443.
    2. Kontrollera om brandväggsregler på källdatorn måste läggas till eller tas bort för källdatorn för att kommunicera med konfigurationsservern. Vi kan inte visa alla nödvändiga brandväggskonfigurationer på grund av variation av brandväggsprogram som kanske används. Arbeta med dina nätverk-administratörer att avblockera alla problem med anslutningen.
    3. Kontrollera att mapparna i [Site Recovery mappar som ska undantas från antivirusprogram](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) undantas från antivirusprogrammet.  
    4. När du har löst problemen och försök registreringen följande riktlinjer i [registrera källdatorn med konfigurationsservern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. På Linux, om värdet för plattformen i < INSTALLATION_DIR\>/etc/drscout.conf är skadad, misslyckas registreringen. Öppna filen /var/log/ua_install.log för att identifiera problemet. Sök efter strängen **avbryts konfiguration eftersom VM_PLATFORM värde är null eller så är inte VmWare/Azure**. Plattformen ska vara inställd på antingen **VmWare** eller **Azure**. Om filen drscout.conf är skadad, rekommenderar vi att du [avinstallera mobilitetsagenten](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) och sedan installera om mobilitetsagenten. Om avinstallationen misslyckas gör du följande:
    1. Öppna filen Installation_Directory/uninstall.sh och kommentera ut anropet till den **StopServices** funktion.
    2. Öppna filen Installation_Directory/Vx/bin/uninstall.sh och kommentera ut anropet till den **stop_services** funktion.
    3. Öppna Installation_Directory/Fx/uninstall.sh filen och kommentera ut hela avsnittet som försöker att stoppa tjänsten Fx.
    4. [Avinstallera](vmware-physical-mobility-service-overview.md#uninstall-the-mobility-service) mobilitetsagenten. Starta om systemet efter lyckad avinstallation och försök sedan installera om mobilitetsagenten.

## <a name="installation-failure-failed-to-load-accounts"></a>Installationsfel: Det gick inte att läsa in konton

Det här felet uppstår när tjänsten inte kan läsa data från transportanslutningen när det installera mobilitetsagenten och registrera med konfigurationsservern. Lös problemet genom att se till att TLS 1.0 är aktiverad på källdatorn.

## <a name="vcenter-discovery-failures"></a>vCenter-identifiering av fel

Se till att den vCenter-servern har lagts till proxyinställningar för kringgå listan för att lösa vCenter Identifieringsfel. Att utföra den här aktiviteten

- Ladda ned PsExec-verktyg från [här](https://aka.ms/PsExec) åtkomst till innehåll för användare av systemet.
- Öppna Internet Explorer i systemet användarinnehåll genom att köra följande kommandorad psexec -s -i ”%programfiles%\Internet Explorer\iexplore.exe”
- Lägg till proxyinställningarna i Internet Explorer och starta om tmanssvc-tjänsten.
- Om du vill konfigurera proxyinställningar för DRA kör cd C:\Program Files\Microsoft Azure Site Recovery-providern
- Kör därefter DRCONFIGURATOR. EXE / konfigurera /AddBypassUrls [Lägg till IP-adressen/FQDN av vCenter Server som angavs under **konfigurera vCenter Server/vSphere ESXi-server** steg i [konfigurationsservern distribution](vmware-azure-deploy-configuration-server.md#configure-settings)]

## <a name="change-the-ip-address-of-the-configuration-server"></a>Ändra IP-adressen för konfigurationsservern

Vi rekommenderar starkt att du inte ändrar IP-adressen för en konfigurationsserver. Kontrollera att alla IP-adresser som är kopplade till konfigurationsservern är statiska IP-adresser. Använd inte DHCP IP-adresser.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-token är ogiltig

Om du vill undvika det här felet, se till att det tiden på systemklockan inte är detsamma som den lokala tiden med mer än 15 minuter. Kör installationsprogrammet igen för att slutföra registreringen.

## <a name="failed-to-create-a-certificate"></a>Det gick inte att skapa ett certifikat

Det går inte att skapa ett certifikat som krävs för att autentisera Site Recovery. Kör installationsprogrammet igen när du har kontrollerat att du kör installationen som lokal administratör.

## <a name="register-source-machine-with-configuration-server"></a>Registrera källdatorn med konfigurationsservern

### <a name="if-the-source-machine-runs-windows"></a>Om källdatorn kör Windows

Kör följande kommando på källdatorn:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
  ```

Inställning | Information
--- | ---
Användning | UnifiedAgentConfigurator.exe /CSEndPoint < IP-adress för configuration server\> /PassphraseFilePath < filsökväg för lösenfras\>
Agenten konfigurationsloggar | Finns under % ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Platsen för lösenfrasen. Använd valfritt giltigt UNC eller lokal filsökväg.

### <a name="if-the-source-machine-runs-linux"></a>Om källdatorn kör Linux

Kör följande kommando på källdatorn:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Inställning | Information
--- | ---
Användning | CD /usr/local/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i < IP-adress för configuration server\> - P < filsökväg för lösenfras\>
-i | Obligatorisk parameter. Anger IP-adressen för konfigurationsservern. Använd en giltig IP-adress.
-P |  Obligatorisk. Den fullständiga sökvägen för filen där lösenfrasen har sparats. Använd en befintlig mapp.

