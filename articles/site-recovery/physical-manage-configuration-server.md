---
title: " Hantera konfigurationsservern för katastrofåterställning för fysisk server med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du hanterar en befintlig server konfiguration för fysisk server katastrofåterställning till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 02/18/2018
ms.author: anoopkv
ms.openlocfilehash: 7fe68f072ef438e21f3e6d3d52aee9e86e537687
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2018
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Hantera konfigurationsservern för fysisk server katastrofåterställning

Du konfigurerar en lokal konfigurationsservern när du använder den [Azure Site Recovery](site-recovery-overview.md) tjänsten för katastrofåterställning av fysiska servrar till Azure. Konfigurationsservern samordnar kommunikationen mellan lokala datorer och Azure och hanterar datareplikering. Den här artikeln beskrivs vanliga uppgifter för att hantera konfigurationsservern när de har distribuerats.

## <a name="prerequisites"></a>Förutsättningar

I tabell sammanfattas de nödvändiga förutsättningar för distribution av den lokala configuration server-datorn.

| **Komponent** | **Krav** |
| --- |---|
| Processorkärnor| 8 |
| RAM | 12 GB|
| Antal diskar | 3, inklusive OS-disk, disk för processen server cache och kvarhållningsenhetens för återställning efter fel |
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB|
| Operativsystem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Nationella inställningar för operativsystem | English (US)|
| VMware vSphere PowerCLI-version | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Grupprinciper| Aktivera inte dessa grupp-principer: <br> -Förhindra åtkomst till via Kommandotolken <br> -Förhindra åtkomst till verktyg för redigering av registret <br> -Förtroende för bifogade filer <br> -Aktivera körning av skript <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Ingen befintlig standardwebbplatsen <br> -Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställning  <br> -Ingen befintlig webbplats/program som lyssnar på port 443<br>|
| NIC-typ | VMXNET3 (när distribueras som en VM VMware) |
| IP-adresstyp | Statisk |
| Internet-åtkomst | Servern behöver åtkomst till dessa webbadresser: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi (krävs inte för skalbara processervrar) <br> - time.nist.gov <br> - time.windows.com |
| Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)|

## <a name="download-the-latest-installation-file"></a>Hämta installationsfilen för senaste

Den senaste versionen av filen configuration server-installation finns i Site Recovery-portalen. Dessutom kan hämta direkt från den [Microsoft Download Center](http://aka.ms/unifiedsetup).

1. Logga in på Azure-portalen och bläddra till Recovery Services-ventilen.
2. Bläddra till **Site Recovery-infrastruktur** > **Konfigurationsservrar** (under för VMware och fysiska datorer).
3. Klicka på den **+ servrar** knappen.
4. På den **Lägg till Server** klickar du på knappen ladda ned för att ladda ned Registreringsnyckeln. Du behöver den här nyckeln under installationen av Configuration Server för att registrera den med Azure Site Recovery-tjänsten.
5. Klicka på den **ladda ned Microsoft Azure Site Recovery Unified installationsprogram** länk för att hämta den senaste versionen av konfigurationsservern.

  ![Hämtningssidan](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Installera och registrera servern

1. Kör det enhetliga installationsprogrammet.
2. I **innan du börjar**väljer **installera konfigurationsservern och processervern**.

    ![Innan du börjar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. I **Third Party Software License** (Licens för programvara från tredje part) klickar du på **I Accept** (Jag accepterar) för att ladda ned och installera MySQL.
4. I **Internet Settings** (Internetinställningar) anger du hur providern som körs på konfigurationsservern ska ansluta till Azure Site Recovery via internet. Kontrollera att du har tillåtit URL: er som krävs.

    - Om du vill ansluta till proxyservern som för närvarande har ställts in på datorn, väljer **Anslut till Azure Site Recovery via en proxyserver**.
    - Om du vill att providern ska ansluta direkt väljer **Anslut direkt till Azure Site Recovery utan en proxyserver**.
    - Om den befintliga proxyservern kräver autentisering, eller om du vill använda en anpassad proxyserver för Provider-anslutning väljer **Anslut med anpassade proxyinställningar**, och ange adress, port och autentiseringsuppgifter.
     ![Brandvägg](./media/physical-manage-configuration-server/combined-wiz4.png)
6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

    ![Förutsättningar](./media/physical-manage-configuration-server/combined-wiz5.png)
7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. Om du kan sedan kontrolleras att PowerCLI 6.0 är installerad.
9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

    ![Installationsplats](./media/physical-manage-configuration-server/combined-wiz8.png)
10. I **Val av nätverk** anger du lyssnare (nätverkskort och SSL-port) där konfigurationsservern skickar och tar emot replikeringsdata. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

    ![Val av nätverk](./media/physical-manage-configuration-server/combined-wiz9.png)


11. I **Sammanfattning** granskar du informationen och klickar på **Installera**. När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats.


När registreringen är klar visas servern på bladet **Inställningar** > **Servrar** i valvet.


## <a name="install-from-the-command-line"></a>Installera från kommandoraden

Kör installationsfilen på följande sätt:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Exempel på användning
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
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



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Skapa filen indata för MYSQLCredsFilePath

Parametern MySQLCredsFilePath använder en fil som indata. Skapa filen med följande format och skickar den som indataparameter till MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Skapa filen indata för ProxySettingsFilePath
Parametern ProxySettingsFilePath använder en fil som indata. Skapa filen med följande format och skickar den som indataparameter till ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Ändra proxyinställningar för

Du kan ändra proxyinställningar för den configuration server-datorn på följande sätt:

1. Logga in på konfigurationsservern.
2. Starta cspsconfigtool.exe med genvägen på din.
3. Klicka på den **valvet registrering** fliken.
4. Hämtar en ny fil i valvet registrering från portalen och ange den som indata till verktyget.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Ange nya proxyinformationen och klicka på den **registrera** knappen.
6. Öppna ett kommandofönster Admin PowerShell.
7. Kör följande kommando:
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Om du har ytterligare processer servrar som är kopplade till konfigurationsservern måste du [åtgärda proxyinställningarna på alla servrar som skalbara processen](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) i distributionen.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Registrera en konfigurationsserver med samma valvet
  1. Logga in på din av konfigurationsservern.
  2. Starta cspsconfigtool.exe använder genvägen på skrivbordet.
  3. Klicka på den **valvet registrering** fliken.
  4. Hämtar en ny registreringsfil från portalen och ange den som indata till verktyget.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Ange en proxyserver information och klicka på den **registrera** knappen.  
  6. Öppna ett kommandofönster Admin PowerShell.
  7. Kör följande kommando

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Om du har flera processervern, behöver du [registrera dem](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrera en server configuration med ett annat valv

> [!WARNING]
> Följande steg tar bort associationen konfigurationsservern från det aktuella valvet och replikering av alla skyddade virtuella datorer under konfigurationsservern har stoppats.

1. Logga in på konfigurationsservern
2. Kör kommandot från en administratör i Kommandotolken:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Starta cspsconfigtool.exe använder genvägen på skrivbordet.
4. Klicka på den **valvet registrering** fliken.
5. Hämtar en ny registreringsfil från portalen och ange den som indata till verktyget.
6. Ange en proxyserver information och klicka på den **registrera** knappen.  
7. Öppna ett kommandofönster Admin PowerShell.
8. Kör följande kommando
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uppgradera en konfigurationsserver

Du kan köra samlade uppdateringar för att uppdatera konfigurationsservern. Uppdateringar kan användas för upp till N-4 versioner. Exempel:

- Om du kör 9.7 9.8, 9.9 eller 9.10 - kan du uppgradera direkt till 9.11.
- Om du kör 9,6 eller tidigare och du vill uppgradera till 9.11, måste du först uppgradera till version 9.7. innan du 9.11.

Länkar till samlade uppdateringar för uppgradering av alla versioner av konfigurationsservern är tillgängliga i den [uppdateringar wiki-sida](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uppgradera servern på följande sätt:

1. Hämta installationsfilen för uppdateringen till konfigurationsservern.
2. Dubbelklicka för att köra installationsprogrammet.
3. Installationsprogrammet identifierar den aktuella versionen som körs på datorn.
4. Klicka på **OK** att bekräfta och köra uppgraderingen. 


## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurationsserver

> [!WARNING]
> Kontrollera följande innan du börjar inaktivering av serverns konfiguration.
> 1. [Inaktivera skyddet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under den här konfigurationen-servern.
> 2. [Avassociera](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) och [ta bort](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) alla replikeringsprinciper från konfigurationsservern.
> 3. [Ta bort](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) alla Vcenter-servrar/vSphere-värdar som är kopplade till konfigurationsservern.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Ta bort konfigurationsservern från Azure-portalen
1. Bläddra till i Azure-portalen **Site Recovery-infrastruktur** > **Konfigurationsservrar** på menyn valvet.
2. Klicka på konfigurationsservern som du vill inaktivera.
3. På den konfigurationsservern information klickar du på den **ta bort** knappen.
4. Klicka på **Ja** att bekräfta borttagningen av servern.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Avinstallera konfigurationsservern och dess beroenden
  > [!TIP]
  Om du tänker återanvända konfigurationsservern med Azure Site Recovery igen kan sedan du hoppa till steg 4 direkt

1. Logga in på av konfigurationsservern som administratör.
2. Öppna Kontrollpanelen > Program > avinstallera program
3. Avinstallera program i följande ordning:
  * Microsoft Azure Recovery Services-agent
  * Microsoft Azure Site Recovery Mobility tjänsten eller Huvudtjänsten målservern
  * Microsoft Azure Site Recovery Provider
  * Server-processen för Microsoft Azure Site Recovery konfigurationsservern
  * Microsoft Azure Site Recovery Configuration Server beroenden
  * MySQL Server 5.5
4. Kör följande kommando från och admin-kommandotolk.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Ta bort eller avregistrera konfigurationsservern (PowerShell)

1. [Installera](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell-modulen
2. Logga in till ditt Azure-konto med hjälp av kommandot
    
    `Login-AzureRmAccount`
3. Välj den prenumeration som valvet finns

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Nu konfigurerat valvet-kontexten
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Hämta väljer din konfigurationsservern

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurationsservern

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Den **-tvinga** alternativ i Remove-AzureRmSiteRecoveryFabric kan användas för att Framtvinga borttagning/borttagning av konfigurationsservern.

## <a name="renew-ssl-certificates"></a>Förnya SSL-certifikat
Konfigurationsservern har en webbserver som samordnar aktiviteter för tjänsten Mobility, servrar och huvudmålservern servrar som är anslutna till den inbyggda. Webbservern använder ett SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrollera upphör att gälla

För configuration-serverdistribution före maj 2016 har certifikatet upphör att gälla angetts till ett år. Om du har upphör ett certifikat att gälla, inträffar följande:

- När utgångsdatum är två månader eller mindre, tjänsten börjar skicka meddelanden i portalen och via e-post (om du prenumererar på Azure Site Recovery-meddelanden).
- En meddelandebanderoll visas på sidan valvet resurs. Klicka på listen för mer information.
- Om du ser en **uppgradera nu** knappen, anger detta att det finns vissa komponenter i din miljö som inte har uppgraderats till 9.4.xxxx.x eller senare versioner. Uppgradera komponenterna innan du förnya certifikatet. Du kan inte förnya på äldre versioner.

### <a name="renew-the-certificate"></a>Förnya certifikatet

1. Öppna i valvet, **Site Recovery-infrastruktur** > **konfigurationsservern**, och klicka på krävs konfigurationsservern.
2. Utgångsdatum visas under **konfigurationsservern hälsa**
3. Klicka på **förnya certifikat**. 




## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Nästa steg

Granska självstudier för att ställa in katastrofåterställning av [fysiska servrar](tutorial-physical-to-azure.md) till Azure.

