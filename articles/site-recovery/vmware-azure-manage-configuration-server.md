---
title: Hantera konfigurationsservern för haveriberedskap med Azure Site Recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 56c53b9e2388cc0594076a5ef35b072216aec20d
ms.sourcegitcommit: b129186667a696134d3b93363f8f92d175d51475
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80672744"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Hantera konfigurationsservern för VMware VM/fysisk serverkatastrofåterställning

Du konfigurerar en lokal konfigurationsserver när du använder [Azure Site Recovery](site-recovery-overview.md) för haveriberedskap av virtuella datorer och fysiska servrar till Azure. Konfigurationsservern samordnar kommunikationen mellan lokala VMware och Azure och hanterar datareplikering. Den här artikeln sammanfattar vanliga uppgifter för att hantera konfigurationsservern när den har distribuerats.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Uppdatera Windows-licens

Licensen som medföljer OVF-mallen är en utvärderingslicens som är giltig i 180 dagar. För oavbruten användning måste du aktivera Windows med en upphandlad licens. Licensuppdatering kan göras antingen via en fristående nyckel eller KMS-standardnyckel. Vägledning finns på [DISM Windows kommandorad för att köra OS](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Information om hur du hämtar nycklar finns [i KMS-klienten.](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys)

## <a name="access-configuration-server"></a>Konfigurationsserver för Åtkomst

Du kan komma åt konfigurationsservern på följande sätt:

* Logga in på den virtuella datorn där den distribueras och Starta **Azure Site Recovery Configuration Manager** från genvägen till skrivbordet.
* Du kan också fjärransluta till konfigurationsservern från https://*ConfigurationServerName*/:44315/. Logga in med administratörsautentiseringsuppgifter.

## <a name="modify-vmware-server-settings"></a>Ändra serverinställningar för VMware

1. Om du vill associera en annan VMware-server med konfigurationsservern väljer du **Lägg till vCenter Server/vSphere ESXi-server**efter [inloggning](#access-configuration-server).
2. Ange informationen och välj sedan **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Ändra autentiseringsuppgifter för automatisk identifiering

1. Om du vill uppdatera de autentiseringsuppgifter som används för att ansluta till VMware-servern för automatisk identifiering av virtuella datorer med VMware väljer du kontot efter [inloggningen](#access-configuration-server)och klickar på **Redigera**.
2. Ange de nya autentiseringsuppgifterna och välj sedan **OK**.

    ![Ändra VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Du kan också ändra autentiseringsuppgifterna via CSPSConfigtool.exe.

1. Logga in på konfigurationsservern och starta CSPSConfigtool.exe
2. Välj det konto som du vill ändra och klicka på **Redigera**.
3. Ange ändrade autentiseringsuppgifter och klicka på **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Ändra autentiseringsuppgifter för installation av Mobilitetstjänster

Ändra de autentiseringsuppgifter som används för att automatiskt installera mobilitetstjänsten på de virtuella datorer med VMware som du aktiverar för replikering.

1. Efter [inloggning](#access-configuration-server)väljer du **Hantera autentiseringsuppgifter för virtuella datorer**
2. Välj det konto du vill ändra och klicka på **Redigera**
3. Ange de nya autentiseringsuppgifterna och välj sedan **OK**.

    ![Ändra autentiseringsuppgifter för mobilitetstjänsten](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Du kan också ändra autentiseringsuppgifter via CSPSConfigtool.exe.

1. Logga in på konfigurationsservern och starta CSPSConfigtool.exe
2. Välj det konto du vill ändra och klicka på **Redigera**
3. Ange de nya autentiseringsuppgifterna och klicka på **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Lägga till autentiseringsuppgifter för installation av mobilitetstjänster

Om du missade att lägga till autentiseringsuppgifter under OVF-distributionen av konfigurationsservern

1. När [du har loggat in](#access-configuration-server)väljer du Hantera **autentiseringsuppgifter för virtuella datorer**.
2. Klicka på **Lägg till autentiseringsuppgifter för virtuella datorer**.
    ![tilläggsmobilitet-autentiseringsuppgifter](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Ange de nya autentiseringsuppgifterna och klicka på **Lägg till**.

Du kan också lägga till autentiseringsuppgifter via CSPSConfigtool.exe.

1. Logga in på konfigurationsservern och starta CSPSConfigtool.exe
2. Klicka på **Lägg till,** ange de nya autentiseringsuppgifterna och klicka på **Ok**.

## <a name="modify-proxy-settings"></a>Ändra proxyinställningar

Ändra proxyinställningarna som används av konfigurationsserverdatorn för internetåtkomst till Azure. Om du har en processserverdator utöver standardprocessservern som körs på konfigurationsserverdatorn ändrar du inställningarna på båda datorerna.

1. När [du har loggat in](#access-configuration-server) på konfigurationsservern väljer du Hantera **anslutning**.
2. Uppdatera proxyvärdena. Välj sedan **Spara** för att uppdatera inställningarna.

## <a name="add-a-network-adapter"></a>Lägga till ett nätverkskort

OVF-mallen Open Virtualization Format (OVF) distribuerar den virtuella konfigurationsservern med ett enda nätverkskort.

- Du kan [lägga till ytterligare ett kort i den virtuella datorn,](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)men du måste lägga till det innan du registrerar konfigurationsservern i valvet.
- Om du vill lägga till ett kort när du har registrerat konfigurationsservern i valvet lägger du till kortet i egenskaperna för den virtuella datorn. Då måste du [registrera](#reregister-a-configuration-server-in-the-same-vault) servern i valvet igen.

## <a name="how-to-renew-ssl-certificates"></a>Så här förnyar du SSL-certifikat

Konfigurationsservern har en inbyggd webbserver som dirigerar aktiviteter för mobilitetsagenter på alla skyddade datorer, inbyggda/utskalningsprocessservrar och huvudmålservrar som är anslutna till den. Webbservern använder ett SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrollera utgångsdatum

Utgångsdatumet visas under **hälsotillståndet för Konfigurationsserver**. För konfigurationsserverdistributioner före maj 2016 angavs certifikatets utgång till ett år. Om du har ett certifikat som ska upphöra att gälla inträffar följande:

- När utgångsdatumet är två månader eller mindre börjar tjänsten skicka meddelanden i portalen och via e-post (om du prenumererar på meddelanden om webbplatsåterställning).
- En meddelandebanderoll visas på arkivresurssidan. Om du vill ha mer information väljer du banderollen.
- Om knappen **Uppgradera nu** visas att vissa komponenter i din miljö inte har uppgraderats till 9.4.xxxx.x eller högre versioner. Uppgradera komponenterna innan du förnyar certifikatet. Du kan inte förnya på äldre versioner.

### <a name="if-certificates-are-yet-to-expire"></a>Om certifikaten ännu inte har upphört att gälla

1. Öppna**Konfigurationsserver**för infrastruktur för infrastruktur för **platsåterställning** > i valvet . Välj den konfigurationsserver som krävs.
2. Se till att alla komponenter skala ut processservrar, master målservrar och rörlighet agenter på alla skyddade datorer är på senaste versioner och är i ansluten tillstånd.
3. Välj nu **Förnya certifikat**.
4. Följ noga instruktionerna på den här sidan och klicka på okej för att förnya certifikat på vald konfigurationsserver och dess associerade komponenter.

### <a name="if-certificates-have-already-expired"></a>Om certifikaten redan har upphört att gälla

1. Efter utgångsdatum kan certifikat **inte förnyas från Azure Portal**. Innan du fortsätter, se till att alla komponenter skala ut processservrar, huvudmålservrar och rörlighet agenter på alla skyddade datorer är på senaste versioner och är i ansluten tillstånd.
2. **Följ den här proceduren endast om certifikaten redan har upphört att gälla.** Logga in på konfigurationsserver, navigera till C-enhet > programdata > Site Recovery > hem > svsystems > bin och kör "RenewCerts" executor verktyg som administratör.
3. Ett PowerShell-körningsfönster visas och utlöser förnyelse av certifikat. Det här kan ta upp till 15 minuter. Stäng inte fönstret förrän förnyelsen är klar.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="Förnya certifikat":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrera om en konfigurationsserver i samma valv

Du kan registrera om konfigurationsservern i samma valv om du behöver. Om du har ytterligare en processserverdator registrerar du båda datorerna, förutom standardprocessservern som körs på konfigurationsserverdatorn.


1. Öppna **Konfigurationsservrar för hantering av** > **platsåterställningsinfrastruktur** > **Configuration Servers**i valvet .
2. Välj Hämta **registreringsnyckel** för att hämta autentiseringsuppgifterna i Valve i **Servrar.**
3. Logga in på konfigurationsservermaskinen.
4. Öppna **cspsconfigtool.exe**i **%ProgramData%\ASR\home\svsystems\bin**.
5. På fliken **Arkivregistrering** väljer du **Bläddra**och letar reda på autentiseringsfilen för valvet som du hämtade.
6. Om det behövs anger du information om proxyservern. Välj sedan **Registrera**.
7. Öppna ett kommandofönster för administratörer i PowerShell och kör följande kommando:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >För att **hämta de senaste certifikaten från konfigurationsservern** till utskalningsprocessservern kör kommandot " *\<Installationsenhet\Microsoft Azure Site Recovery\agent\cdpcli.exe>"--registermt*

8. Starta slutligen om obenginen genom att köra följande kommando.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrera en konfigurationsserver med ett annat valv

> [!WARNING]
> Följande steg tar bort tilldelningen av konfigurationsservern från det aktuella valvet och replikeringen av alla skyddade virtuella datorer under konfigurationsservern stoppas.

1. Logga in på konfigurationsservern.
2. Öppna ett kommandofönster för administratörer i PowerShell och kör följande kommando:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Starta webbläsarportalen för konfigurationsserverinstallation med hjälp av genvägen på skrivbordet.
4. Utför registreringsstegen som liknar en ny registrering av [konfigurationsserver](vmware-azure-tutorial.md#register-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurationsservern

Du kör samlade uppdateringar för att uppdatera konfigurationsservern. Uppdateringar kan tillämpas för upp till N-4 versioner. Ett exempel:

- Om du kör 9,7, 9,8, 9,9 eller 9,10 kan du uppgradera direkt till 9,11.
- Om du kör 9.6 eller tidigare och vill uppgradera till 9.11 måste du först uppgradera till version 9.7. före 9.11.

Mer information om supportpolicyn för Azure Site Recovery-komponenter finns [här](https://aka.ms/asr_support_statement).
Länkar till samlade uppdateringar för uppgradering till alla versioner av konfigurationsservern finns [här](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> Med varje ny version "N" av en Azure Site Recovery-komponent som släpps, anses alla versioner under "N-4" vara ur stöd. Det är alltid lämpligt att uppgradera till de senaste versionerna tillgängliga.</br>
> Mer information om supportpolicyn för Azure Site Recovery-komponenter finns [här](https://aka.ms/asr_support_statement).

Uppgradera servern enligt följande:

1. Gå till Hantera **konfigurationsservrar** > för infrastruktur för**platsåterställning** > **Configuration Servers**i valvet .
2. Om en uppdatering är tillgänglig visas en länk i kolumnen **Agentversion** >.
    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update2.png)
3. Hämta installationsfilen för uppdatering till konfigurationsservern.

    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dubbelklicka för att köra installationsprogrammet.
5. Installationsprogrammet identifierar den aktuella versionen som körs på datorn. Klicka på **Ja** för att starta uppgraderingen.
6. När uppgraderingen är klar valideras serverkonfigurationen.

    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update3.png)

7. Stäng installationsprogrammet genom att klicka på **Slutför.**
8. Om du vill uppgradera resten av komponenterna för återställning av webbplatser läser du vår [uppgraderingsvägledning](https://aka.ms/asr_vmware_upgrades).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Uppgradera konfigurationsservern/processservern från kommandoraden

Kör installationsfilen på följande sätt:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Exempel på användning
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametrar

|Parameternamn| Typ | Beskrivning| Värden|
|-|-|-|-|
| /ServerMode|Krävs|Anger om både konfigurations- och processervrar eller endast processervern ska installeras|CS<br>PS|
|/InstallLocation|Krävs|Den mapp där komponenterna installeras| Vilken mapp på datorn som helst|
|/MySQLCredsFilePath|Krävs|Filsökvägen till platsen där autentiseringsuppgifterna för MySQL-servern lagras|Filen ska vara i det format som anges nedan|
|/VaultCredsFilePath|Krävs|Sökvägen för valvautentiseringsfilen|Giltig sökväg|
|/EnvType|Krävs|Typ av miljö som du vill skydda |VMware<br>NonVMware|
|/PSIP|Krävs|Nätverkskortets IP-adress används för överföring av replikeringsdata| Vilken giltig IP-adress som helst|
|/CSIP|Krävs|Nätverkskortets IP-adress som konfigurationsservern lyssnar på| Vilken giltig IP-adress som helst|
|/PassphraseFilePath|Krävs|Den fullständiga sökvägen till platsen för lösenfrasfilen|Giltig sökväg|
|/BypassProxy|Valfri|Anger att konfigurationsservern ansluter till Azure utan en proxyserver|För att få det här värdet från Venu|
|/ProxySettingsFilePath|Valfri|Proxy-inställningar (standardproxy kräver autentisering, eller en anpassad proxy)|Filen ska vara i det format som anges nedan|
|DataTransferSecurePort|Valfri|Portnumret på PSIP ska användas för replikeringsdata| Giltigt portnummer (standardvärdet är 9433)|
|/SkipSpaceCheck|Valfri|Hoppa över utrymmeskontroll för cachedisk| |
|/AcceptThirdpartyEULA|Krävs|När du flaggar innebär det att du godkänner licensavtalet från tredje part| |
|/ShowThirdpartyEULA|Valfri|Visar licensavtalet (EULA) från tredje part. Om detta anges som indata ignoreras alla andra parametrar| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Skapa filindata för MYSQLCredsFilePath

Parametern MySQLCredsFilePath tar en fil som indata. Skapa filen med följande format och skicka den som indata MySQLCredsFilePath-parameter.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Skapa filindata för ProxySettingsFilePath
Parametern ProxySettingsFilePath tar en fil som indata. Skapa filen med följande format och skicka den som indata ProxySettingsFilePath-parameter.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```

## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurationsserver

1. [Inaktivera skydd](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under konfigurationsservern.
2. [Ta bort och](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) [ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alla replikeringsprinciper från konfigurationsservern.
3. [Ta bort](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alla vCenter-servrar/vSphere-värdar som är associerade med konfigurationsservern.
4. Öppna**konfigurationsservrar**för infrastruktur för **platsåterställning** > i valvet .
5. Välj den konfigurationsserver som du vill ta bort. Välj sedan **Ta bort**på sidan **Information** .

    ![Ta bort konfigurationsserver](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Ta bort med PowerShell

Du kan också ta bort konfigurationsservern med PowerShell.

1. [Installera](https://docs.microsoft.com/powershell/azure/install-Az-ps) Azure PowerShell-modulen.
2. Logga in på ditt Azure-konto med det här kommandot:

    `Connect-AzAccount`
3. Välj valvprenumerationen.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Ange valvkontexten.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Hämta konfigurationsservern.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurationsservern.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Du kan använda alternativet **-Force** i Remove-AzSiteRecoveryFabric för påtvingad borttagning av konfigurationsservern.

## <a name="generate-configuration-server-passphrase"></a>Generera lösenord för konfigurationsserver

1. Logga in på konfigurationsservern och öppna sedan ett kommandotolksfönster som administratör.
2. Om du vill ändra katalogen till lagerplatsmappen kör du **kommandoskivan %ProgramData%\ASR\home\svsystems\bin**
3. Om du vill generera lösphrase-filen kör du **genpassphrase.exe -v > MobSvc.lösphrase**.
4. Lösenfrasen lagras i filen som finns på **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="refresh-configuration-server"></a>Uppdatera konfigurationsserver

1. I Azure-portalen navigerar du till **Recovery Services Vault** > **Hantera** > infrastruktur > för**platsåterställning****för VMware & konfigurationsservrar för fysiska datorer** > **Configuration Servers**
2. Klicka på den konfigurationsserver som du vill uppdatera.
3. Klicka på **Mer** > **uppdateringsserver**på bladet med information om vald konfigurationsserver .
4. Övervaka förloppet för jobbet under **Återställningstjänster Arkiv** > **Övervakning** > **Site Recovery jobb**.

## <a name="failback-requirements"></a>Krav för återställning efter fel

Under återrotect och återställning av återställning måste den lokala konfigurationsservern köras och vara ansluten. För att återställa återställningen av återställningen efter fel måste den virtuella datorn som återställs finnas i konfigurationsserverdatabasen.

Se till att du tar regelbundna schemalagda säkerhetskopior av konfigurationsservern. Om en katastrof inträffar och konfigurationsservern går förlorad måste du först återställa konfigurationsservern från en säkerhetskopia och se till att den återställda konfigurationsservern har samma IP-adress som den registrerades i valvet. Återställning av återställning av återställning fungerar inte om en annan IP-adress används för den återställda konfigurationsservern.

## <a name="next-steps"></a>Nästa steg

Granska självstudierna för att konfigurera haveriberedskap för virtuella virtuella datorer med [VMware](vmware-azure-tutorial.md) till Azure.
