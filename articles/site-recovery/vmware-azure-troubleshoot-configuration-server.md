---
title: Felsöka problem med konfigurationsservern vid haveriberedskap av virtuella datorer med VMware och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft-dokument
description: Den här artikeln innehåller felsökningsinformation för distribution av konfigurationsservern för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 02/13/2019
ms.author: ramamill
ms.openlocfilehash: 0383a512dfb7c2bb1ae2422b9ade1e3c7387a70c
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478301"
---
# <a name="troubleshoot-configuration-server-issues"></a>Felsöka konfigurationsserverfel

Den här artikeln hjälper dig att felsöka problem när du distribuerar och hanterar konfigurationsservern [för Azure Site Recovery.](site-recovery-overview.md) Konfigurationsservern fungerar som en hanteringsserver. Använd konfigurationsservern för att ställa in haveriberedskap för lokala virtuella datorer och fysiska servrar till Azure med hjälp av Site Recovery. I följande avsnitt beskrivs de vanligaste felen som kan uppstå när du lägger till en ny konfigurationsserver och när du hanterar en konfigurationsserver.

## <a name="registration-failures"></a>Registreringsfel

Källmaskinen registrerar sig med konfigurationsservern när du installerar mobilitetsagenten. Du kan felsöka eventuella fel under det här steget genom att följa dessa riktlinjer:

1. Öppna filen C:\ProgramData\ASR\home\svsystems\var\configurator_register_host_static_info.log. (Mappen ProgramData kan vara en dold mapp. Om mappen ProgramData inte visas markerar du kryssrutan **Dölj objekt** i avsnittet **Visa/dölj** i Utforskaren på fliken **Visa.)** Fel kan orsakas av flera problem.

2. Sök efter strängen **Ingen giltig IP-adress hittades**. Om strängen hittas:
   1. Kontrollera att det begärda värd-ID:t är samma som källdatorns värd-ID.
   2. Kontrollera att källdatorn har minst en IP-adress tilldelad det fysiska nätverkskortet. För att agentregistrering med konfigurationsservern ska lyckas måste källdatorn ha minst en giltig IP v4-adress tilldelad det fysiska nätverkskortet.
   3. Kör ett av följande kommandon på källdatorn för att hämta alla IP-adresser till källdatorn:
      - För Windows:`> ipconfig /all`
      - För Linux:`# ifconfig -a`

3. Om strängen **Ingen giltig IP-adress hittades** söker du efter strängen **Reason=>NULL**. Det här felet uppstår om källdatorn använder en tom värd för att registrera sig hos konfigurationsservern. Om strängen hittas:
    - När du har löst problemen följer du riktlinjerna i [Registrera källdatorn med konfigurationsservern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server) för att försöka igen registreringen manuellt.

4. Om strängen **Reason=>NULL** inte hittas öppnar du filen C:\ProgramData\ASRSetupLogs\UploadedLogs\ASRUnifiedAgentInstaller.log på källdatorn. (Mappen ProgramData kan vara en dold mapp. Om mappen ProgramData inte visas markerar du kryssrutan **Dölj objekt** i avsnittet **Visa/dölj** i Utforskaren på fliken **Visa.)** Fel kan orsakas av flera problem. 

5. Sök efter strängen **post begäran: (7) - Kunde inte ansluta till servern**. Om strängen hittas:
    1. Lös nätverksproblemen mellan källdatorn och konfigurationsservern. Kontrollera att konfigurationsservern kan nås från källmaskinen med hjälp av nätverksverktyg som ping, traceroute eller en webbläsare. Se till att källmaskinen kan nå konfigurationsservern via port 443.
    2. Kontrollera om några brandväggsregler på källdatorn blockerar anslutningen mellan källdatorn och konfigurationsservern. Arbeta med nätverksadministratörerna för att häva blockeringen av anslutningsproblem.
    3. Kontrollera att mapparna i [mappen Site Recovery-undantag från antivirusprogram](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) inte ingår i antivirusprogrammet.
    4. När nätverksproblem har lösts försöker du igen registreringen genom att följa riktlinjerna i [Registrera källdatorn med konfigurationsservern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

6. Om **begäran om stränginlägg: (7) - Det gick inte att ansluta till servern** hittades, leta efter strängbegäran i samma loggfil: **(60) - Peer-certifikatet kan inte autentiseras med givna certifikatutfärdare**. Det här felet kan uppstå eftersom konfigurationsservercertifikatet har upphört att gälla eller att källdatorn inte stöder TLS 1.0 eller senare protokoll. Det kan också inträffa om en brandvägg blockerar TLS-kommunikation mellan källdatorn och konfigurationsservern. Om strängen hittas: 
    1. Lös problemet genom att ansluta till konfigurationsserverns IP-adress med hjälp av en webbläsare på källdatorn. Använd URI https:\/ \/<konfigurationsserverNS\>IP-adress :443/. Se till att källmaskinen kan nå konfigurationsservern via port 443.
    2. Kontrollera om några brandväggsregler på källdatorn behöver läggas till eller tas bort för att källdatorn ska kunna prata med konfigurationsservern. På grund av de olika brandväggsprogram som kan användas kan vi inte lista alla nödvändiga brandväggskonfigurationer. Arbeta med nätverksadministratörerna för att häva blockeringen av anslutningsproblem.
    3. Kontrollera att mapparna i [mappen Site Recovery-undantag från antivirusprogram](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) inte ingår i antivirusprogrammet.  
    4. NÃ¤r du har löst problemen ã¶nsÃ¤lla registreringen igen genom att följa riktlinjer i [Registrera källdatorn med konfigurationsservern](vmware-azure-troubleshoot-configuration-server.md#register-source-machine-with-configuration-server).

7. På Linux, om värdet på plattformen i <\>INSTALLATION_DIR /etc/drscout.conf är skadad, misslyckas registreringen. Om du vill identifiera problemet öppnar du filen /var/log/ua_install.log. Sök efter strängen **Abortera konfigurationen som VM_PLATFORM värdet är antingen null eller är det inte VmWare/Azure**. Plattformen ska ställas in på **vmware** eller **Azure**. Om filen drscout.conf är skadad rekommenderar vi att du [avinstallerar mobilitetsagenten](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) och sedan installerar om mobilitetsagenten. Om avinstallationen misslyckas slutför du följande steg: a. Öppna filen Installation_Directory/uninstall.sh och kommentera anropet till **funktionen StopServices.**
    b. Öppna filen Installation_Directory/Vx/bin/uninstall.sh och kommentera anropet till **funktionen stop_services.**
    c. Öppna Installation_Directory / Fx / uninstall.sh fil och kommentera hela avsnittet som försöker stoppa Fx tjänsten.
    d. [Avinstallera](vmware-physical-manage-mobility-service.md#uninstall-mobility-service) mobilitetsagenten. Efter en lyckad avinstallation startar du om systemet och försöker sedan installera om mobilitetsagenten.

## <a name="installation-failure-failed-to-load-accounts"></a>Installationsfel: Det gick inte att läsa in konton

Det här felet uppstår när tjänsten inte kan läsa data från transportanslutningen när den installerar mobilitetsagenten och registrerar sig hos konfigurationsservern. LÃ¶s problemet genom att kontrollera att TLS 1.0 är aktiverat på källdatorn.

## <a name="vcenter-discovery-failures"></a>vCenter-identifieringsfel

Lös fel i vCenter-identifieringen genom att lägga till vCenter-servern i proxyinställningarna för byPass-listan. 

- Ladda ner PsExec verktyg [härifrån](https://aka.ms/PsExec) för att komma åt System användarinnehåll.
- Öppna Internet Explorer i systemanvändarinnehåll genom att köra följande kommandorad psexec -s -i %programfiler%\Internet Explorer\iexplore.exe"
- Lägg till proxyinställningar i IE och starta om tmanssvc-tjänsten.
- Om du vill konfigurera DRA-proxyinställningar kör du cd C:\Program\Microsoft Azure Site Recovery Provider
- Kör sedan DRCONFIGURATOR. EXE /configure /AddBypassUrls [add IP Address/FQDN of vCenter Server provided during **Configure vCenter Server/vSphere ESXi server** step of Configuration Server deployment ] EXE /configure /addBypassUrls [add IP Address/FQDN of vCenter Server provided during Configure vCenter Server/vSphere ESXi server step of Configuration Server deployment ] EXE /configure /addBypassUrls [add IP Address/FQDN of vCenter Server provided during Configure vCenter Server/vSphere ESXi server step of Configuration Server [deployment](vmware-azure-deploy-configuration-server.md#configure-settings)] EXE /

## <a name="change-the-ip-address-of-the-configuration-server"></a>Ändra konfigurationsserverns IP-adress

Vi rekommenderar starkt att du inte ändrar IP-adressen för en konfigurationsserver. Kontrollera att alla IP-adresser som har tilldelats konfigurationsservern är statiska IP-adresser. Använd inte DHCP IP-adresser.

## <a name="acs50008-saml-token-is-invalid"></a>ACS50008: SAML-token är ogiltig

Undvik det här felet genom att se till att tiden på systemklockan inte skiljer sig från den lokala tiden med mer än 15 minuter. Kör installationsprogrammet igen för att slutföra registreringen.

## <a name="failed-to-create-a-certificate"></a>Det gick inte att skapa ett certifikat

Det går inte att skapa ett certifikat som krävs för att autentisera webbplatsåterställning. Kör installationsprogrammet igen när du har er till dig som kontrollerar att du kör installationen som lokal administratör.

## <a name="failure-to-activate-windows-license-from-server-standard-evaluation-to-server-standard"></a>Det gick inte att aktivera Windows-licensen från serverstandardutvärdering till serverstandard

1. Som en del av konfigurationsserverdistribution via OVF används en utvärderingslicens som är giltig i 180 dagar. Du måste aktivera den här licensen innan den upphör att gälla. Annars kan detta resultera i frekvent avstängning av konfigurationsservern och därmed orsaka hinder för replikeringsaktiviteter.
2. Om du inte kan aktivera Windows-licensen kan du kontakta [Windows supportteam](https://aka.ms/Windows_Support) för att lösa problemet.

## <a name="register-source-machine-with-configuration-server"></a>Registrera källdator med konfigurationsserver

### <a name="if-the-source-machine-runs-windows"></a>Om källmaskinen kör Windows

Kör följande kommando på källdatorn:

```
  cd C:\Program Files (x86)\Microsoft Azure Site Recovery\agent
  UnifiedAgentConfigurator.exe  /CSEndPoint <configuration server IP address> /PassphraseFilePath <passphrase file path>
```

Inställning | Information
--- | ---
Användning | UnifiedAgentConfigurator.exe /CSEndPoint <konfigurationsserverNS\> IP-adress /LösphraseFilePath <lösenfrassökvägen för lösenfras\>
Agentkonfigurationsloggar | Finns under %ProgramData%\ASRSetupLogs\ASRUnifiedAgentConfigurator.log.
/CSEndPoint | Obligatorisk parameter. Anger konfigurationsserverns IP-adress. Använd en giltig IP-adress.
/PassphraseFilePath |  Obligatorisk. Platsen för lösenfrasen. Använd en giltig UNC- eller lokal filsökväg.

### <a name="if-the-source-machine-runs-linux"></a>Om källmaskinen kör Linux

Kör följande kommando på källdatorn:

```
  /usr/local/ASR/Vx/bin/UnifiedAgentConfigurator.sh -i <configuration server IP address> -P /var/passphrase.txt
  ```

Inställning | Information
--- | ---
Användning | cd /usr/lokal/ASR/Vx/bin<br /><br /> UnifiedAgentConfigurator.sh -i <konfigurationsserverNS IP-adress\> -P <filsökvägen för lösenfras\>
-i | Obligatorisk parameter. Anger konfigurationsserverns IP-adress. Använd en giltig IP-adress.
-P. |  Obligatorisk. Den fullständiga filsökvägen för filen där lösenfrasen sparas. Använd vilken giltig mapp som helst.

## <a name="unable-to-configure-the-configuration-server"></a>Det gick inte att konfigurera konfigurationsservern

Om du installerar andra program än konfigurationsservern på den virtuella datorn kanske du inte kan konfigurera huvudmålet. 

Konfigurationsservern måste vara en enda syftesserver och det som en delad server inte stöds. 

Mer information finns i vanliga frågor och svar om konfigurationen i [Distribuera en konfigurationsserver](vmware-azure-deploy-configuration-server.md#faqs). 

## <a name="remove-the-stale-entries-for-protected-items-from-the-configuration-server-database"></a>Ta bort de inaktuella posterna för skyddade objekt från konfigurationsserverdatabasen 

Om du vill ta bort den inaktuella skyddade datorn på konfigurationsservern följer du följande steg. 
 
1. Så här fastställer du källmaskinen och IP-adressen för den inaktuella posten: 

    1. Öppna MYSQL-cmdline i administratörsläge. 
    2. Kör följande kommandon. 
   
        ```
        mysql> use svsdb1;
        mysql> select id as hostid, name, ipaddress, ostype as operatingsystem, from_unixtime(lasthostupdatetime) as heartbeat from hosts where name!='InMageProfiler'\G;
        ```

        Detta returnerar listan över registrerade maskiner tillsammans med deras IP-adresser och sista hjärtslag. Hitta värden som har inaktuella replikeringspar.

2. Öppna en upphöjd kommandotolk och navigera till C:\ProgramData\ASR\home\svsystems\bin. 
4. Om du vill ta bort information om registrerade värdar och den inaktuella inmatningsinformationen från konfigurationsservern kör du följande kommando med källdatorn och IP-adressen för den inaktuella posten. 
   
    `Syntax: Unregister-ASRComponent.pl -IPAddress <IP_ADDRESS_OF_MACHINE_TO_UNREGISTER> -Component <Source/ PS / MT>`
 
    Om du har en källserverpost i "OnPrem-VM01" med en IP-adress på 10.0.0.4 använder du följande kommando i stället.
 
    `perl Unregister-ASRComponent.pl -IPAddress 10.0.0.4 -Component Source`
 
5. Starta om följande tjänster på källdator för att registrera om med konfigurationsservern. 
 
    - InMage Scout Application Service
    - Inmage Scout VX Agent - Sentinel/Utpost

## <a name="upgrade-fails-when-the-services-fail-to-stop"></a>Uppgraderingen misslyckas när tjänsterna inte stoppas

Uppgraderingen av konfigurationsservern misslyckas när vissa tjänster inte stoppas. 

Om du vill identifiera problemet navigerar du till C:\ProgramData\ASRSetupLogs\CX_TP_InstallLogFile på konfigurationsservern. Om du hittar följande fel kan du följa stegen nedan för att lösa problemet: 

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

Så här löser du problemet:

Stoppa följande tjänster manuellt:

- cxprocessserver (cxprocessserver)
- Inmage Scout VX Agent - Sentinel / Utpost, 
- Microsoft Azure Recovery Services Agent, 
- Microsoft Azure-tjänsten för återställning av webbplatser, 
- tmansvc (tmansvc)
  
Om du vill uppdatera [konfigurationsservern](service-updates-how-to.md#links-to-currently-supported-update-rollups) kör du den enhetliga installationen igen.

## <a name="azure-active-directory-application-creation-failure"></a>Fel i att Skapa Azure Active Directory-program

Du har inte tillräcklig behörighet för att skapa ett program i Azure Active Directory (AAD) med hjälp av [OVA-mallen (Open Virtualization Application).](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template
)

Lös problemet genom att logga in på Azure-portalen och göra något av följande:

- Begär rollen Programutvecklare i AAD. Mer information om rollen Programutvecklare finns [i Administratörsrollbehörigheter i Azure Active Directory](../active-directory/users-groups-roles/directory-assign-admin-roles.md).
- Kontrollera att **användarflaggan kan skapa** programflaggan är inställd på *true* i AAD. Mer information finns i Så här använder du [portalen för att skapa ett Azure AD-program och tjänsthuvudnamn som kan komma åt resurser](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions).

## <a name="process-servermaster-target-are-unable-to-communicate-with-the-configuration-server"></a>Processserver/huvudmål kan inte kommunicera med konfigurationsservern 

Processservermodulerna (PS) och Master Target (MT) kan inte kommunicera med konfigurationsservern (CS) och deras status visas som inte ansluten på Azure-portalen.

Vanligtvis beror detta på ett fel med port 443. Följ följande steg för att häva blockeringen av porten och återaktivera kommunikationen med CS.

**Kontrollera att MARS-agenten anropas av huvudmålagenten**

Om du vill kontrollera att huvudmålagenten kan skapa en TCP-session för konfigurationsserver-IP:n letar du efter en spårning som liknar följande i huvudmålagentloggarna:

TCP \<Ersätt IP med CS IP här>:52739 \<Ersätt IP med CS IP här>:443 SYN_SENT 

TCP 192.168.1.40:52739 192.168.1.40:443 SYN_SENT // Ersätt IP med CS IP här

Om du hittar spår som liknar följande i MT-agentloggarna, rapporterar MT Agent fel på port 443:

    #~> (11-20-2018 20:31:51):   ERROR  2508 8408 313 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
    #~> (11-20-2018 20:31:54):   ERROR  2508 8408 314 FAILED : PostToSVServer with error [at curlwrapper.cpp:CurlWrapper::processCurlResponse:212]   failed to post request: (7) - Couldn't connect to server
 
Det här felet kan uppstå när andra program också använder port 443 eller på grund av en brandväggsinställning som blockerar porten.

Så här löser du problemet:

- Kontrollera att port 443 inte blockeras av brandväggen.
- Om porten inte kan nås på grund av ett annat program som använder den porten stoppar och avinstallerar du appen.
  - Om det inte är möjligt att stoppa appen konfigurerar du en ny ren CS.
- Starta om konfigurationsservern.
- Starta om IIS-tjänsten.

### <a name="configuration-server-is-not-connected-due-to-incorrect-uuid-entries"></a>Konfigurationsservern är inte ansluten på grund av felaktiga UUID-poster

Det här felet kan uppstå när det finns UUID-poster (Multien konfigurationsserver) i databasen. Problemet uppstår ofta när du klonar den virtuella datorn för konfigurationsservern.

Så här löser du problemet:

1. Ta bort inaktuella/gamla CS-virtuella datorer från vCenter. Mer information finns i [Ta bort servrar och inaktivera skydd](site-recovery-manage-registration-and-protection.md).
2. Logga in på den virtuella konfigurationsservern och anslut till MySQL svsdb1-databasen. 
3. Kör följande fråga:

    > [!IMPORTANT]
    >
    > Kontrollera att du anger UUID-information om den klonade konfigurationsservern eller den inaktuella posten för konfigurationsservern som inte längre används för att skydda virtuella datorer. Om du anger en felaktig UUID förlorar du informationen för alla befintliga skyddade objekt.
   
    ```
        MySQL> use svsdb1;
        MySQL> delete from infrastructurevms where infrastructurevmid='<Stale CS VM UUID>';
        MySQL> commit; 
    ```
4. Uppdatera portalsidan.

## <a name="an-infinite-sign-in-loop-occurs-when-entering-your-credentials"></a>En oändlig inloggningsloop inträffar när du anger dina autentiseringsuppgifter

När du har angett rätt användarnamn och lösenord på konfigurationsservern OVF fortsätter Azure-inloggningen att fråga efter rätt autentiseringsuppgifter.

Det här problemet kan uppstå när systemtiden är felaktig.

Så här löser du problemet:

Ställ in rätt tid på datorn och försök logga in igen. 
 