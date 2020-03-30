---
title: Hantera konfigurationsservern för fysiska servrar i Azure Site Recovery
description: I den här artikeln beskrivs hur du hanterar konfigurationsservern för Azure Site Recovery för fysisk serverkatastrofåterställning till Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: 25be48e9caed446be3a86a11143ce3040808065a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294301"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Hantera konfigurationsservern för fysisk återställning av serverkatastrofer

Du konfigurerar en lokal konfigurationsserver när du använder [Azure Site Recovery-tjänsten](site-recovery-overview.md) för haveriberedskap av fysiska servrar till Azure. Konfigurationsservern samordnar kommunikationen mellan lokala datorer och Azure och hanterar datareplikering. Den här artikeln sammanfattar vanliga uppgifter för att hantera konfigurationsservern när den har distribuerats.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Krav

Tabellen sammanfattar förutsättningarna för att distribuera den lokala konfigurationsserverdatorn.

| **Komponent** | **Krav** |
| --- |---|
| Processorkärnor| 8 |
| RAM | 16 GB|
| Antal diskar | 3, inklusive OS-disken, processservercachedisken och kvarhållningsenheten för återställning av fel |
| Ledigt diskutrymme (processerverns cacheminne) | 600 GB
| Ledigt diskutrymme (kvarhållningsdisken) | 600 GB|
| Operativsystem  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Nationella inställningar för operativsystem | Engelska (USA)|
| VMware vSphere PowerCLI-version | Krävs inte|
| Windows Server-roller | Aktivera inte dessa roller: <br> - Active Directory Domain Services <br>- Internet Information Services <br> - Hyper-V |
| Grupprinciper| Aktivera inte dessa grupprinciper: <br> - Förhindra åtkomst till kommandotolken <br> - Förhindra åtkomst till verktyg för registerredigering <br> - Förtroendelogik för bifogade filer <br> - Aktivera skriptkörning <br> [Läs mer](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Ingen befintlig standardwebbplats <br> - Aktivera [anonym autentisering](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Aktivera [FastCGI-inställning](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Ingen befintlig webbplats /ansökan lyssna på port 443<br>|
| Typ av nätverkskort | VMXNET3 (när det distribueras som en virtuell virtuell VMware-dator) |
| IP-adresstyp | Statisk |
| Internetåtkomst | Servern behöver åtkomst till dessa webbadresser: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi(krävs inte för utskalningsprocessservrar) <br> - time.nist.gov <br> - time.windows.com |
| Portar | 443 (kontrolkanalsorchestration)<br>9443 (dataöverföring)|

## <a name="download-the-latest-installation-file"></a>Hämta den senaste installationsfilen

Den senaste versionen av installationsfilen för konfigurationsservern finns i portalen för webbplatsåterställning. Dessutom kan den hämtas direkt från [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Logga in på Azure-portalen och bläddra till ditt Recovery Services Vault.
2. Bläddra till > **konfigurationsservrar** för infrastruktur för **webbplatsåterställning**(under För VMware & fysiska datorer).
3. Klicka på knappen **+Servrar.**
4. Klicka på knappen Hämta på sidan Lägg till **server** för att hämta registreringsnyckeln. Du behöver den här nyckeln under configuration server-installationen för att registrera den med Azure Site Recovery-tjänsten.
5. Klicka på länken Hämta installationsprogrammet för **Microsoft Azure Site Recovery Unified för** att hämta den senaste versionen av konfigurationsservern.

   ![Ladda ner sida](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Installera och registrera servern

1. Kör det enhetliga installationsprogrammet.
2. Välj **Installera konfigurationsservern och processservern**i **Innan du börjar**.

    ![Innan du börjar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. I **Third Party Software License** (Licens för programvara från tredje part) klickar du på **I Accept** (Jag accepterar) för att ladda ned och installera MySQL.
4. I **Internet-inställningar**anger du hur providern som körs på konfigurationsservern ansluter till Azure Site Recovery via Internet. Kontrollera att du har tillåtit de webbadresser som krävs.

    - Om du vill ansluta till proxyn som för närvarande är konfigurerad på datorn väljer du **Anslut till Azure Site Recovery med hjälp av en proxyserver**.
    - Om du vill att leverantören ska ansluta direkt väljer du **Anslut direkt till Azure Site Recovery utan proxyserver**.
    - Om den befintliga proxyn kräver autentisering, eller om du vill använda en anpassad proxy för Provider-anslutningen, väljer du **Anslut med anpassade proxyinställningar**och anger adress, port och autentiseringsuppgifter.
     ![Brandvägg](./media/physical-manage-configuration-server/combined-wiz4.png)
6. I **Kravkontroll** körs en kontroll för att se till att installationen kan köras. Om det visas en varning om **synkroniseringskontrollen för global tid** kontrollerar du att systemklockans tid (inställningarna för **datum och tid**) är samma som tidszonen.

    ![Krav](./media/physical-manage-configuration-server/combined-wiz5.png)
7. I **MySQL Configuration** (MySQL-konfiguration) skapar du autentiseringsuppgifter för att logga in på den MySQL-serverinstans som är installerad.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. I **Miljöinformation** väljer du om du ska replikera virtuella VMwares-datorer. Om du är det kontrollerar installationsprogrammet att PowerCLI 6.0 är installerat.
9. I **Installationsplats** väljer du om du vill installera binärfilerna och lagra cachen. Enheten du väljer måste ha minst 5 GB tillgängligt utrymme, men vi rekommenderar en cacheenhet med 600 GB eller mer ledigt utrymme.

    ![Installationsplats](./media/physical-manage-configuration-server/combined-wiz8.png)
10. I **Nätverksval**väljer du först det nätverkskort som den inbyggda processservern använder för identifiering och push-installation av mobilitetstjänsten på källdatorer och välj sedan det nätverkskort som Configuration Server använder för anslutning till Azure. Port 9443 är standardporten som används för att skicka och ta emot replikeringstrafik, men du kan ändra portnumret så att det passar din miljö. Förutom port 9443 öppnar vi också port 443, som används av en webbserver för att dirigera replikeringsåtgärder. Använd inte port 443 för att skicka eller ta emot replikeringstrafik.

    ![Val av nätverk](./media/physical-manage-configuration-server/combined-wiz9.png)


11. I **Sammanfattning** granskar du informationen och klickar på **Installera**. När installationen är klar skapas en lösenfras. Du behöver den när du aktiverar replikering. Kopiera lösenfrasen och förvara den på en säker plats.


När registreringen är klar visas servern på bladet **Inställningar** > **servrar** i valvet.


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
MySQLRootPassword = "Password"
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
## <a name="modify-proxy-settings"></a>Ändra proxyinställningar

Du kan ändra proxyinställningarna för konfigurationsservermaskinen på följande sätt:

1. Logga in på konfigurationsservern.
2. Starta cspsconfigtool.exe med genvägen på skrivbordet.
3. Klicka på fliken **Arkivregistrering.**
4. Hämta en ny arkivregistreringsfil från portalen och ange den som indata till verktyget.

   ![register-konfiguration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Ange de nya proxyinformationerna och klicka på **knappen Registrera.**
6. Öppna ett kommandofönster för Admin PowerShell.
7. Kör följande kommando:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Om du har ytterligare processservrar anslutna till konfigurationsservern måste du [åtgärda proxyinställningarna på alla skalningsprocessservrar](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) i distributionen.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Registrera om en konfigurationsserver med samma valv
1. Logga in på konfigurationsservern.
2. Starta cspsconfigtool.exe med genvägen på skrivbordet.
3. Klicka på fliken **Arkivregistrering.**
4. Hämta en ny registreringsfil från portalen och ange den som indata till verktyget.
      ![register-konfiguration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Ange information om proxyservern och klicka på knappen **Registrera.**  
6. Öppna ett kommandofönster för Admin PowerShell.
7. Kör följande kommando

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Om du har flera processserver måste du [registrera om dem](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrera en konfigurationsserver med ett annat valv

> [!WARNING]
> Följande steg tar bort tilldelningen av konfigurationsservern från det aktuella valvet och replikeringen av alla skyddade virtuella datorer under konfigurationsservern stoppas.

1. Logga in på konfigurationsservern
2. från en admin kommandotolk kör kommandot:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Starta cspsconfigtool.exe med genvägen på skrivbordet.
4. Klicka på fliken **Arkivregistrering.**
5. Hämta en ny registreringsfil från portalen och ange den som indata till verktyget.
6. Ange information om proxyservern och klicka på knappen **Registrera.**  
7. Öppna ett kommandofönster för Admin PowerShell.
8. Kör följande kommando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Uppgradera en konfigurationsserver

Du kör samlade uppdateringar för att uppdatera konfigurationsservern. Uppdateringar kan tillämpas för upp till N-4 versioner. Ett exempel:

- Om du kör 9,7, 9,8, 9,9 eller 9,10 - du kan uppgradera direkt till 9,11.
- Om du kör 9.6 eller tidigare och vill uppgradera till 9.11 måste du först uppgradera till version 9.7. före 9.11.

Länkar till samlade uppdateringar för uppgradering till alla versioner av konfigurationsservern finns på [sidan wiki-uppdateringar](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uppgradera servern enligt följande:

1. Hämta installationsfilen för uppdatering till konfigurationsservern.
2. Dubbelklicka för att köra installationsprogrammet.
3. Installationsprogrammet identifierar den aktuella versionen som körs på datorn.
4. Klicka på **OK** för att bekräfta och kör uppgraderingen. 


## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurationsserver

> [!WARNING]
> Kontrollera följande innan du börjar inaktivera konfigurationsservern.
> 1. [Inaktivera skydd](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under den här konfigurationsservern.
> 2. Ta bort alla replikeringsprinciper från [konfigurationsservern.](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) [Delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy)
> 3. [Ta bort](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alla vCenters-servrar/vSphere-värdar som är associerade till konfigurationsservern.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Ta bort konfigurationsservern från Azure-portalen
1. I Azure-portalen bläddrar du till**konfigurationsservrar** för infrastruktur för **webbplatsåterställning** > på Arkiv-menyn.
2. Klicka på den konfigurationsserver som du vill inaktivera.
3. Klicka på knappen **Ta bort** på informationssidan för Konfigurationsservern.
4. Klicka på **Ja** för att bekräfta borttagningen av servern.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Avinstallera konfigurationsservern och dess beroenden
> [!TIP]
>   Om du planerar att återanvända konfigurationsservern med Azure Site Recovery igen kan du hoppa till steg 4 direkt

1. Logga in på konfigurationsservern som administratör.
2. Öppna kontrollpanelen > program > avinstallera program
3. Avinstallera programmen i följande ordning:
   * Agent för Microsoft Azure-återställningstjänster
   * Microsoft Azure Site Recovery Mobility Service/Master Target-server
   * Microsoft Azure-webbplatsåterställningsprovider
   * Konfigurationsserver/processserver för konfiguration av Microsoft Azure-platsåterställning
   * Konfigurationsserver beroenden för konfigurationsserver för Microsoft Azure-platsåterställning
   * MySQL Server 5.5
4. Kör följande kommando från och admin kommandotolken.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Ta bort eller avregistrera en konfigurationsserver (PowerShell)

1. [Installera](https://docs.microsoft.com/powershell/azure/install-Az-ps) Azure PowerShell-modul
2. Logga in på ditt Azure-konto med kommandot
    
    `Connect-AzAccount`
3. Välj den prenumeration som valvet finns under

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Ställ nu in ditt valvkontext
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Hämta välj konfigurationsserver

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurationsservern

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> **Alternativet -Force** i alternativet Remove-AzSiteRecoveryFabric kan användas för att tvinga borttagning/borttagning av konfigurationsservern.

## <a name="renew-ssl-certificates"></a>Förnya SSL-certifikat
Konfigurationsservern har en inbyggd webbserver som dirigerar aktiviteter för mobilitetstjänsten, processservrar och huvudmålservrar som är anslutna till den. Webbservern använder ett SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrollera utgångsdatum

För konfigurationsserverdistributioner före maj 2016 angavs certifikatets utgång till ett år. Om du har ett certifikat kommer att upphöra att gälla inträffar följande:

- När utgångsdatumet är två månader eller mindre börjar tjänsten skicka meddelanden i portalen och via e-post (om du prenumererar på Azure Site Recovery-meddelanden).
- En meddelandebanderoll visas på arkivresurssidan. Klicka på bannern för mer information.
- Om knappen **Uppgradera nu** visas betyder det att det finns vissa komponenter i din miljö som inte har uppgraderats till 9.4.xxxx.x eller högre versioner. Uppgradera komponenter innan du förnyar certifikatet. Du kan inte förnya på äldre versioner.

### <a name="renew-the-certificate"></a>Förnya certifikatet

1. Öppna**Configuration Server**för infrastruktur för **platsåterställning** > i valvet och klicka på den konfigurationsserver som krävs.
2. Utgångsdatumet visas under **hälsotillstånd för Konfigurationsserver**
3. Klicka på **Förnya certifikat**. 




## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Nästa steg

Granska självstudierna för att konfigurera haveriberedskap av [fysiska servrar](tutorial-physical-to-azure.md) till Azure.

