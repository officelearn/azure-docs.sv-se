---
title: Felsöka problem med konfigurationsservern under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller felsökningsinformation för att distribuera konfigurationsservern för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: a81bcf26234f3b7840a75e7134596cb777071e8f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432798"
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

Lös vCenter Identifieringsfel genom att lägga till vCenter-servern att kringgå listan proxy-inställningar. 

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

## <a name="failure-to-activate-windows-licence-from-server-standard-evaluation-to-server-standard"></a>Det gick inte att aktivera Windows-licens från utvärdering av Standard-Server till Server Standard

1. Som en del av distribution av konfigurationsserver via OVF används en utvärderingslicens, vilket är giltig i 180 dagar. Du måste aktivera den här licensen innan det upphör. Annars kan detta leda till frekventa avstängning av konfigurationsservern och därmed orsaka hinderance replikering aktiviteter.
2. Om det inte går att aktivera Windows-licens kan nå ut till [Windows supportteam](https://aka.ms/Windows_Support) att lösa problemet.

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

## <a name="unable-to-configure-the-configuration-server"></a>Det går inte att konfigurera konfigurationsservern

Om du installerar andra program än konfigurationsservern på den virtuella datorn måste kanske du inte att konfigurera Huvudmålet. 

Konfigurationsservern måste vara ingen enskild server och använder den som en delad server inte stöds. 

Mer information finns i konfigurationen vanliga frågor och svar i [distribuera en konfigurationsserver](vmware-azure-deploy-configuration-server.md#faq). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Ta bort inaktuella poster för skyddade objekt från configuration server-databas 

Använd följande steg för att ta bort inaktuella skyddad dator på konfigurationsservern. 
 
1. Att fastställa källdatorn och IP-adressen för inaktuell post: 

    1. Öppna MYSQL-kommandorad i administratörsläge. 
    2. Kör följande kommandon. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Det här returnerar listan över registrerade datorer och deras IP-adresser och senaste pulsslag. Hitta den värd som har inaktuella replikering par.

2. Öppna en upphöjd kommandotolk och navigera till C:\ProgramData\ASR\home\svsystems\bin. 
4. Ta bort registrerade värdar information och inaktuella informationen från konfigurationsservern genom att köra följande kommando med hjälp av källdatorn och IP-adressen för inaktuell post. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Om du har en källa server inmatning av ”OnPrem-VM01” med en IP-adress för adressen 10.0.0.4 sedan använda följande kommando i stället.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Starta om följande tjänster på källdatorn för att registrera om med konfigurationsservern. 
 
    - InMage Scout Application Service
    - InMage Scout VX Agent - Sentinel/Outpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Uppgraderingen misslyckas när tjänsterna inte att stoppa

Det går inte att uppgraderingen av configuration server vid vissa tjänster inte stoppar. 

Du kan identifiera problemet genom att gå till C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile på konfigurationsservern. Om du hittar följande fel kan du använda stegen nedan för att lösa problemet: 

    2018-06-28 14:28:12.943   Successfully copied php.ini to C:\Temp from C:\thirdparty\php5nts
    2018-06-28 14:28:12.943   svagents service status - SERVICE_RUNNING
    2018-06-28 14:28:12.944   Stopping svagents service.
    2018-06-28 14:31:32.949   Unable to stop svagents service.
    2018-06-28 14:31:32.949   Stopping svagents service.
    2018-06-28 14:34:52.960   Unable to stop svagents service.
    2018-06-28 14:34:52.960   Stopping svagents service.
    2018-06-28 14:38:12.971   Unable to stop svagents service.
    2018-06-28 14:38:12.971   Rolling back the install changes.
    2018-06-28 14:38:12.971   Upgrade has failed.

Att lösa problemet:

Stoppa följande tjänster:

- cxprocessserver
- InMage Scout VX Agent – Sentinel/Outpost, 
- Microsoft Azure Recovery Services Agent, 
- Microsoft Azure Site Recovery Service, 
- tmansvc
  
Om du vill uppdatera configuration server kör den [enhetligt installationsprogram](service-updates-how-to.md#links-to-currently-supported-update-rollups) igen.

## <a name="azure-active-directory-application-creation-failure"></a>Azure Active Directory application fel vid skapande

Du har inte behörighet att skapa ett program i Azure Active Directory (AAD) med hjälp av den [Open Virtualization program (OVA)](vmware-azure-deploy-configuration-server.md#deployment-of-configuration-server-through-ova-template
) mall.

Lös problemet genom att logga in till Azure-portalen och gör något av följande:

- Begära rollen programutvecklare i AAD. Läs mer på rollen programutvecklare [behörigheter för administratör i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Kontrollera att den **användare kan skapa program** flagga har angetts till *SANT* i AAD. Mer information finns i [Gör så här: Använd portalen för att skapa ett Azure AD-program och huvudnamn för tjänsten som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Process server/Master Target kan inte kommunicera med konfigurationsservern 

Processervern (PS) och huvudmål (MT) moduler kan inte kommunicera med konfigurationsservern (CS) och deras status visas som inte är ansluten på Azure-portalen.

Detta är vanligtvis på grund av ett fel med port 443. Använd följande steg för att avblockera porten och aktivera kommunikation med Konfigurationsservern igen.

**Kontrollera att MARS-agenten som anropas av Master Target-agent**

Kontrollera att Master Target-agenten kan skapa en TCP-session för Konfigurationsserverns IP-adress, leta efter en spårning som liknar följande i Master Target-agentloggar:

TCP <Replace IP with CS IP here>:52739 <Replace IP with CS IP here>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT / / Ersätt IP-Adressen med CS IP-adress här

Om du hittar spårningar liknar följande i MT-agentloggarna, rapporterar MT-agenten fel på port 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Det här felet kan uppstå när andra program också använder port 443 eller på grund av en brandväggsinställning som blockerar porten.

Att lösa problemet:

- Kontrollera att port 443 inte blockeras av brandväggen.
- Om porten inte kan nås på grund av ett annat program via den porten, stoppa och avinstallera appen.
  - Konfigurera en ny ren CS om stoppar appen inte är möjligt.
- Starta om konfigurationsservern.
- Starta om IIS-tjänsten.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurationsservern är inte ansluten på grund av felaktigt UUID-poster

Det här felet kan inträffa när det finns flera server (CS) instans UUID konfigurationsposter i databasen. Problemet uppstår ofta när du klonar konfigurationsservern VM.

Att lösa problemet:

1. Ta bort föråldrade/gamla CS VM från vCenter. Mer information finns i [ta bort servrar och inaktivera skydd](site-recovery-manage-registration-and-protection.md).
2. Logga in på konfigurationsservern VM och ansluta till MySQL svsdb1-databasen. 
3. Kör följande fråga:

    > [!IMPORTANT]
    >
    > Kontrollera att du anger vilka UUID klonade konfigurationsservern eller inaktuell post för konfigurationsservern som inte längre används för att skydda virtuella datorer. Ange ett felaktigt UUID resulterar i att förlora informationen för alla befintliga skyddade objekt.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Uppdatera portalsidan.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>En oändlig inloggning loop inträffar när du anger dina inloggningsuppgifter

När du har angett rätt användarnamn och lösenord på konfigurationsservern OVF, Azure inloggning fortsätter att fråga efter rätt autentiseringsuppgifter.

Det här problemet kan inträffa när systemklockan är felaktig.

Att lösa problemet:

Ange rätt tid på datorn och försök logga in. 
 