---
title: Hantera konfigurationsservern för haveriberedskap för lokala fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar Azure Site Recovery konfigurationsserver för fysisk server haveriberedskap till Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: b4a35cb853326aa3e54c7b261eaa72f15929a84c
ms.sourcegitcommit: 0dd053b447e171bc99f3bad89a75ca12cd748e9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2019
ms.locfileid: "58483972"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Hantera konfigurationsservern för fysisk server disaster recovery

Du ställer in en konfigurationsservern lokalt när du använder den [Azure Site Recovery](site-recovery-overview.md) för haveriberedskap för fysiska servrar till Azure. Konfigurationsservern samordnar kommunikationen mellan lokala datorer och Azure och hanterar datareplikering. Den här artikeln sammanfattas vanliga uppgifter för att hantera configuration server när det har distribuerats.

## <a name="prerequisites"></a>Förutsättningar

I tabell sammanfattas kraven för att distribuera den lokala configuration server-datorn.

| **Komponent** | **Krav** |
| --- |---|
| Processorkärnor| 8 |
| RAM | 16 GB|
| Antal diskar | 3, inklusive OS-disk, processerverns cachedisk och kvarhållningsenhet för återställning efter fel |
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB|
| Operativsystem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Nationella inställningar för operativsystem | English (US)|
| VMware vSphere PowerCLI-version | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Grupprinciper| Aktivera inte dessa grupp-principer: <br> -Förhindra åtkomst till kommandoprompten <br> -Förhindra åtkomst till registerredigeringsverktygen <br> -Förtroende för bifogade filer <br> -Aktivera körning av skript <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | – Ingen befintlig standardwebbplatsen <br> -Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Aktivera [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) inställningen  <br> – Ingen befintlig webbplats/program som lyssnar på port 443<br>|
| Typ av nätverkskort | VMXNET3 (när distribueras som en VMware VM) |
| IP-adresstyp | Statisk |
| Internet-åtkomst | Servern måste ha åtkomst till dessa webbadresser: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (krävs inte för skala ut Processervrar) <br> - time.nist.gov <br> - time.windows.com |
| Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)|

## <a name="download-the-latest-installation-file"></a>Ladda ned den senaste klientinstallationsfilen

Den senaste versionen av installationsfilen för configuration server är tillgänglig i Site Recovery-portalen. Dessutom kan den kan hämtas direkt från den [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Logga in på Azure portal och bläddra till ditt Recovery Services-valv.
2. Bläddra till **Site Recovery-infrastruktur** > **Konfigurationsservrar** (under för VMware och fysiska datorer).
3. Klicka på den **+ servrar** knappen.
4. På den **Lägg till Server** klickar du på knappen ladda ned för att ladda ned Registreringsnyckeln. Du behöver den här nyckeln under installationen av Configuration Server för att registrera den med Azure Site Recovery-tjänsten.
5. Klicka på den **ladda ned Microsoft Azure Site Recovery enhetliga installationsprogrammet** länken för att hämta den senaste versionen av konfigurationsservern.

   ![Hämtningssidan](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Installera och registrera servern

1. Kör det enhetliga installationsprogrammet.
2. I **innan du börjar**väljer **installera konfigurationsservern och processervern**.

    ![Innan du börjar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. I **Third Party Software License** (Licens för programvara från tredje part) klickar du på **I Accept** (Jag accepterar) för att ladda ned och installera MySQL.
4. I **Internet Settings** (Internetinställningar) anger du hur providern som körs på konfigurationsservern ska ansluta till Azure Site Recovery via internet. Kontrollera att du har tillåtit URL: er som krävs.

    - Om du vill ansluta till proxyservern som för närvarande har ställts in på datorn väljer **Anslut till Azure Site Recovery med proxyserver**.
    - Om du vill att providern ska ansluta direkt väljer **Anslut direkt till Azure Site Recovery utan proxyserver**.
    - Om den befintliga proxyservern kräver autentisering, eller om du vill använda en anpassad proxyserver för provideranslutningen, väljer **Anslut med anpassade proxyinställningar**, och ange adressen, porten och autentiseringsuppgifterna.
     ![Brandvägg](./media/physical-manage-configuration-server/combined-wiz4.png)
6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

    ![Förutsättningar](./media/physical-manage-configuration-server/combined-wiz5.png)
7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. Om du kan sedan kontrollerar installationen att PowerCLI 6.0 är installerat.
9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

    ![Installationsplats](./media/physical-manage-configuration-server/combined-wiz8.png)
10. I **val av nätverk**, väljer du det nätverkskort som inbyggd processervern som används för identifiering och push-installation av mobilitetstjänsten på källdatorer först och sedan väljer du det nätverkskort som konfigurationsservern använder för anslutning med Azure. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

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
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parametrar

|Parameternamn| Type | Beskrivning| Värden|
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



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Skapa fil som indata för MYSQLCredsFilePath

Parametern MySQLCredsFilePath använder en fil som indata. Skapa filen med följande format och skickar den som indataparameter till MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Skapa fil som indata för ProxySettingsFilePath
ProxySettingsFilePath parametern använder en fil som indata. Skapa filen med följande format och skickar den som indataparameter till ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Ändra proxyinställningar för

Du kan ändra proxyinställningarna för configuration server-dator på följande sätt:

1. Logga in på konfigurationsservern.
2. Starta cspsconfigtool.exe hjälp genvägen på skrivbordet.
3. Klicka på den **Vault registrering** fliken.
4. Hämta en ny fil i valvet registrering från portalen och ange den som indata för verktyget.

   ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Ange nya proxyinformationen och klicka på den **registrera** knappen.
6. Öppna en Admin PowerShell-kommandofönster.
7. Kör följande kommando:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Om du har ytterligare processervrar som är anslutna till konfigurationsservern kan du behöva [åtgärda proxyinställningarna på alla processervrar som skalbara](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) i distributionen.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Registrera en konfigurationsserver med samma valv
1. Logga in på konfigurationsservern.
2. Starta cspsconfigtool.exe hjälp genvägen på skrivbordet.
3. Klicka på den **Vault registrering** fliken.
4. Hämta en ny registreringsfil från portalen och ange den som indata för verktyget.
      ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Ange proxyservern information och klicka på den **registrera** knappen.  
6. Öppna en Admin PowerShell-kommandofönster.
7. Kör följande kommando

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Om du har flera processervern kan du behöva [registrera om dem](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrera en konfigurationsserver med ett annat valv

> [!WARNING]
> Följande steg tas konfigurationsservern från aktuella valvet och replikering av alla skyddade virtuella datorer under configuration server har stoppats.

1. Logga in på konfigurationsservern
2. från Kommandotolken som administratör, kör du kommandot:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Starta cspsconfigtool.exe hjälp genvägen på skrivbordet.
4. Klicka på den **Vault registrering** fliken.
5. Hämta en ny registreringsfil från portalen och ange den som indata för verktyget.
6. Ange proxyservern information och klicka på den **registrera** knappen.  
7. Öppna en Admin PowerShell-kommandofönster.
8. Kör följande kommando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uppgradera en konfigurationsserver

Du kan köra samlade uppdateringar för att uppdatera konfigurationsservern. Uppdateringar kan användas för upp till N-4 versioner. Exempel:

- Om du kör 9.7, 9.8, 9.9 eller 9.10 – kan du uppgradera direkt till 9.11.
- Om du kör 9,6 eller tidigare och du vill uppgradera till 9.11, måste du först uppgradera till version 9.7. before  9.11.

Länkar till samlade uppdateringar för att uppgradera till alla versioner av konfigurationsservern är tillgängliga i den [wiki-sida för uppdateringar](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uppgradera servern på följande sätt:

1. Ladda ned installationsfilen för uppdateringen till konfigurationsservern.
2. Dubbelklicka för att köra installationsprogrammet.
3. Installationsprogrammet identifierar den aktuella versionen som körs på datorn.
4. Klicka på **OK** att bekräfta och köra uppgraderingen. 


## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller Avregistrerar en konfigurationsserver

> [!WARNING]
> Kontrollera följande innan du börjar ställa av konfigurationsservern.
> 1. [Inaktivera skyddet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under den här konfigurationsservern.
> 2. [Ta bort koppling till](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) och [ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alla replikeringsprinciper från konfigurationsservern.
> 3. [Ta bort](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alla vCenters servrar/vSphere-värdar som är kopplade till konfigurationsservern.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Ta bort konfigurationsservern från Azure-portalen
1. I Azure-portalen bläddrar du till **Site Recovery-infrastruktur** > **Konfigurationsservrar** valvmenyn.
2. Klicka på configuration server som du vill inaktivera.
3. Konfigurationsserverns informationssidan, klicka på den **ta bort** knappen.
4. Klicka på **Ja** att bekräfta borttagningen av servern.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Avinstallera configuration server och dess beroenden
> [!TIP]
>   Om du planerar att återanvända konfigurationsservern med Azure Site Recovery igen kan hoppa du till steg 4 direkt

1. Logga in på konfigurationsservern som en administratör.
2. Öppna Kontrollpanelen > Program > avinstallera program
3. Avinstallera program i följande ordning:
   * Microsoft Azure Recovery Services-agent
   * Microsoft Azure Site Recovery Mobility Service/huvudmålservern
   * Microsoft Azure Site Recovery Provider
   * Microsoft Azure Site Recovery Configuration Server/Processerver
   * Microsoft Azure Site Recovery Configuration Serverberoenden
   * MySQL-Server 5.5
4. Kör följande kommando från och kommandotolk för administratör.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Ta bort eller Avregistrerar en konfigurationsserver (PowerShell)

1. [Installera](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps) Azure PowerShell-modulen
2. Logga in på till ditt Azure-konto med hjälp av kommandot
    
    `Connect-AzureRmAccount`
3. Välj den prenumeration som valvet finns

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Nu ställa in valvets sammanhang
    
    ```powershell
    $Vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Hämta väljer konfigurationsservern

    `$Fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurationsservern

    `Remove-AzureRmSiteRecoveryFabric -Fabric $Fabric [-Force] `

> [!NOTE]
> Den **-tvinga** alternativ i Remove-AzureRmSiteRecoveryFabric kan användas för att Framtvinga borttagning av/borttagning av konfigurationsservern.

## <a name="renew-ssl-certificates"></a>Förnya SSL-certifikat
Konfigurationsservern har en inbyggd webbserver som samordnar aktiviteter av Mobility service, processervrar och huvudmålservrar som är anslutna till den. Webbservern använder ett SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrollera förfallodatum

Certifikatets förfallodatum angavs för configuration server-distributioner innan maj 2016, till ett år. Om du har upphör ett certifikat att gälla, inträffar följande:

- När utgångsdatum är två månader eller mindre, tjänsten börjar skicka meddelanden i portalen och via e-post (om du prenumererar på Azure Site Recovery-meddelanden).
- En meddelandebanderoll visas på resurssidan för valvet. Klicka på banderollen för mer information.
- Om du ser en **uppgradera nu** knappen Detta indikerar att det finns vissa komponenter i din miljö som inte har uppgraderats till 9.4.xxxx.x eller senare versioner. Uppgradera komponenterna innan du förnyar certifikatet. Du kan inte förnya på äldre versioner.

### <a name="renew-the-certificate"></a>Förnya certifikatet

1. Öppna i valvet, **Site Recovery-infrastruktur** > **konfigurationsservern**, och klicka på den obligatoriska konfiguration-servern.
2. Utgångsdatum visas under **konfigurationsservern hälsotillstånd**
3. Klicka på **förnya certifikat**. 




## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för att konfigurera haveriberedskap för [fysiska servrar](tutorial-physical-to-azure.md) till Azure.

