---
title: " Hantera en konfigurationsserver i Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du ställa in och hantera en Server Configuration."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 11/23/2017
ms.author: anoopkv
ms.openlocfilehash: 4f23750ff1ba261ea3cf782f7c85858e46632cfa
ms.sourcegitcommit: 5bced5b36f6172a3c20dbfdf311b1ad38de6176a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/27/2017
---
# <a name="manage-a-configuration-server"></a>Hantera en konfigurationsserver

Konfigurationsservern fungerar som en koordinator mellan Site Recovery services och lokal infrastruktur. Den här artikeln beskriver hur du kan skapa, konfigurera och hantera konfigurationsservern.

> [!NOTE]
> [Kapacitetsplanering](site-recovery-capacity-planner.md) är ett viktigt steg så att du distribuerar konfigurationsservern med en konfiguration som paket belastningen-krav. Läs mer om [storleksanpassa kraven för en konfigurationsserver](#sizing-requirements-for-a-configuration-server).


## <a name="prerequisites"></a>Krav
Följande är minimikraven på maskinvara, programvara och nätverkskonfiguration som krävs för att ställa in en konfigurationsserver.
> [!IMPORTANT]
> När du distribuerar en konfigurationsserver för att skydda virtuella VMware-datorer, rekommenderar vi att du distribuerar den som en **hög tillgänglighet (HA)** virtuella datorn.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

## <a name="downloading-the-configuration-server-software"></a>Hämta programvaran konfigurationsservern

1. Logga in på Azure-portalen och bläddra till Recovery Services-ventilen.
2. Bläddra till **Site Recovery-infrastruktur** > **Konfigurationsservrar** (under för VMware och fysiska datorer).

  ![Lägg till servrar på sidan](./media/site-recovery-vmware-to-azure-manage-configuration-server/AddServers.png)
3. Klicka på den **+ servrar** knappen.
4. På den **Lägg till Server** klickar du på knappen ladda ned för att ladda ned Registreringsnyckeln. Du behöver den här nyckeln under installationen av Configuration Server för att registrera den med Azure Site Recovery-tjänsten.
5. Klicka på den **ladda ned Microsoft Azure Site Recovery Unified installationsprogram** länk för att hämta den senaste versionen av konfigurationsservern.

  ![Hämtningssidan](./media/site-recovery-vmware-to-azure-manage-configuration-server/downloadcs.png)

  > [!TIP]
  Senaste versionen av konfigurationsservern kan hämtas direkt från [hämtningssidan för Microsoft Download Center](http://aka.ms/unifiedsetup)

## <a name="installing-and-registering-a-configuration-server-from-gui"></a>Installera och registrera en Server Configuration från GUI
[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

## <a name="installing-and-registering-a-configuration-server-using-command-line"></a>Installera och registrera en konfigurationsservern med hjälp av kommandoraden

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Exempel på användning
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="configuration-server-installer-command-line-arguments"></a>Konfiguration av servern installer kommandoradsargument.
[!INCLUDE [site-recovery-unified-setup-parameters](../../includes/site-recovery-unified-installer-command-parameters.md)]


### <a name="create-a-mysql-credentials-file"></a>Skapa en MySql-fil för autentiseringsuppgifter
Parametern MySQLCredsFilePath använder en fil som indata. Skapa filen med följande format och skickar den som indataparameter till MySQLCredsFilePath.
```
[MySQLCredentials]
MySQLRootPassword = "Password>"
MySQLUserPassword = "Password"
```
### <a name="create-a-proxy-settings-configuration-file"></a>Skapa en konfigurationsfil för proxy-inställningar
Parametern ProxySettingsFilePath använder en fil som indata. Skapa filen med följande format och skickar den som indataparameter till ProxySettingsFilePath.

```
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modifying-proxy-settings-for-configuration-server"></a>Ändra proxyinställningar för konfigurationsservern
1. Logga in på din av konfigurationsservern.
2. Starta cspsconfigtool.exe med genvägen på din.
3. Klicka på den **valvet registrering** fliken.
4. Hämtar en ny fil i valvet registrering från portalen och ange den som indata till verktyget.

  ![registrera konfigurationsservern](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
5. Ange ny proxyserver information och klicka på den **registrera** knappen.
6. Öppna ett kommandofönster Admin PowerShell.
7. Kör följande kommando
  ```
  $pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Om du har skalbar processen servrar som är kopplade till den här konfigurationen-servern, behöver du [åtgärda proxyinställningarna på alla servrar som skalbara processen](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#modifying-proxy-settings-for-scale-out-process-server) i distributionen.

## <a name="modify-user-accounts-and-passwords"></a>Ändra användarkonton och lösenord

CSPSConfigTool.exe används för att hantera användarkonton som används för **automatisk identifiering av virtuella VMware-datorer** och utföra ** Push-installation av Mobilitetstjänsten på skyddade datorer. 

1. Logga in på ditt konfigurationsservern.
2. Starta CSPSConfigtool.exe genom att klicka på genvägen på skrivbordet.
3. Klicka på den **hantera konton** fliken.
4. Välj det konto som lösenordet måste ändras och klicka på den **redigera** knappen.
5. Ange det nya lösenordet och klickar på **OK**


## <a name="re-register-a-configuration-server-with-the-same-recovery-services-vault"></a>Registrera en konfigurationsservern med samma Recovery Services-valvet
  1. Logga in på din av konfigurationsservern.
  2. Starta cspsconfigtool.exe använder genvägen på skrivbordet.
  3. Klicka på den **valvet registrering** fliken.
  4. Hämtar en ny registreringsfil från portalen och ange den som indata till verktyget.
        ![registrera konfigurationsservern](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
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
  Om du har skalbar processen servrar som är kopplade till den här konfigurationen-servern, behöver du [registrera alla servrar som skalbara processen](site-recovery-vmware-to-azure-manage-scaleout-process-server.md#re-registering-a-scale-out-process-server) i distributionen.

## <a name="registering-a-configuration-server-with-a-different-recovery-services-vault"></a>Registrerar en konfigurationsserver med en annan Recovery Services-valvet.

> [!WARNING]
> Följande steg tar bort associationen konfigurationen från det aktuella valvet och replikering av alla skyddade virtuella datorer under konfigurationsservern har stoppats.

1. Logga in på din av konfigurationsservern.
2. Kör kommando från en kommandotolk admin

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Starta cspsconfigtool.exe med genvägen på din.
4. Klicka på den **valvet registrering** fliken.
5. Hämtar en ny registreringsfil från portalen och ange den som indata till verktyget.

    ![registrera konfigurationsservern](./media/site-recovery-vmware-to-azure-manage-configuration-server/register-csonfiguration-server.png)
6. Ange en proxyserver information och klicka på den **registrera** knappen.  
7. Öppna ett kommandofönster Admin PowerShell.
8. Kör följande kommando
    ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrading-a-configuration-server"></a>Uppgradera en konfigurationsserver

> [!WARNING]
> Uppdateringar stöds bara upp till N-4th-version. Till exempel om den senaste versionen på marknaden 9.11 uppdatera sedan du från version 9.10 9.9, 9.8 eller 9.7 direkt till 9.11. Men om du är på en version som är mindre än eller lika med 9.6 och du måste uppdatera till minst 9.7 innan du kan använda de senaste uppdateringarna in Configuration-servern. Hämta länkar för tidigare version finns [uppdateringar av Azure Site Recovery-tjänsten](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)

1. Hämta uppdateringen på serverns konfiguration.
2. Starta installationsprogrammet genom att dubbelklicka på installationsprogrammet.
3. Installationsprogrammet identifierar versionen av Site Recovery-komponenter finns på datorn och fråga för en bekräftelse. 
4. Klicka på OK för att ange bekräftelsen och fortsätter med uppgraderingen.


## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurationsserver

> [!WARNING]
> Kontrollera följande innan du börjar inaktivering av serverns konfiguration.
> 1. [Inaktivera skyddet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under den här konfigurationen-servern.
> 2. [Avassociera](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) och [ta bort](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) alla replikeringsprinciper från konfigurationsservern.
> 3. [Ta bort](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) alla Vcenter-servrar/vSphere-värdar som är kopplade till konfigurationsservern.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Ta bort konfigurationsservern från Azure-portalen
1. Bläddra till i Azure-portalen **Site Recovery-infrastruktur** > **Konfigurationsservrar** på menyn valvet.
2. Klicka på konfigurationsservern som du vill inaktivera.
3. På den konfigurationsservern information klickar du på knappen Ta bort.

  ![ta bort konfigurationsservern](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.PNG)
4. Klicka på **Ja** att bekräfta borttagningen av servern.

### <a name="uninstall-the-configuration-server-software-and-its-dependencies"></a>Avinstallera Configuration Server-programvaran och dess beroenden
  > [!TIP]
  Om du tänker återanvända konfigurationsservern med Azure Site Recovery igen kan sedan du hoppa till steg 4 direkt

1. Logga in på av konfigurationsservern som administratör.
2. Öppna Kontrollpanelen > Program > avinstallera program
3. Avinstallera program i följande ordning:
  * Microsoft Azure Recovery Services-agent
  * Microsoft Azure Site Recovery Mobility tjänsten eller Huvudtjänsten målservern
  * Microsoft Azure Site Recovery-providern
  * Server-processen för Microsoft Azure Site Recovery konfigurationsservern
  * Microsoft Azure Site Recovery Configuration Server beroenden
  * MySQL-Server 5.5
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

## <a name="renew-configuration-server-secure-socket-layerssl-certificates"></a>Förnya Configuration servercertifikat Secure Socket Layer(SSL)
Konfigurationsservern har ett inbyggda webbserver som samordnar aktiviteter Mobilitetstjänsten, servrar och Huvudmålservern servrar som är anslutna till konfigurationsservern. Den konfigurationsservern webbserver använder ett SSL-certifikat för att autentisera klienter. Det här certifikatet har en utgången av tre år och kan förnyas när som helst på följande sätt:

> [!WARNING]
Certifikatet upphör att gälla kan bara utföras på version 9.4.XXXX. X eller senare. Uppgradera alla Azure Site Recovery-komponenter (konfigurationsservern, Processervern, Huvudmålserver, Mobilitetstjänsten) innan du startar arbetsflödet förnya certifikat.

1. På Azure-portalen går du till ditt valv > Site Recovery-infrastruktur > konfigurationsservern.
2. Klicka på konfigurationsservern för vilka du behöver att förnya certifikatet för SSL.
3. Du kan se utgångsdatum för SSL-certifikatet under Configuration Server-hälsa.
4. Förnya certifikatet genom att klicka på den **förnya certifikat** åtgärd som visas i följande bild:

  ![ta bort konfigurationsservern](./media/site-recovery-vmware-to-azure-manage-configuration-server/renew-cert-page.png)

### <a name="secure-socket-layer-certificate-expiry-warning"></a>Secure Socket Layer certifikatupphörandevarning

> [!NOTE]
SSL-certifikatets giltighetsperiod för alla installationer som inträffade före maj 2016 har angetts till ett år. du har startat ser certifikatet upphör att gälla meddelanden visas i Azure-portalen.

1. Om Konfigurationsserverns SSL-certifikatet upphör att gälla under de kommande två månaderna, startas tjänsten meddela användare via Azure-portalen & e-post (du måste prenumerera på Azure Site Recovery-meddelanden). Du ser en meddelandebanderoll på resurssidan på valvet.

  ![certifikat-meddelande](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-renew-notification.png)
2. Klicka på listen för att få mer information om certifikatet upphör att gälla.

  ![information för certifikat](./media/site-recovery-vmware-to-azure-manage-configuration-server/ssl-cert-expiry-details.png)

  >[!TIP]
  Om i stället för en **förnya nu** knapp som du ser en **uppgradera nu** knappen. Knappen uppgradera nu anger att det finns vissa komponenter i din miljö som ännu inte har uppgraderats till 9.4.xxxx.x eller senare versioner.

## <a name="revive-a-configuration-server-if-the-secure-socket-layer-ssl-certificate-expired"></a>Revive en konfigurationsserver om Secure Socket Layer (SSL)-certifikatet har upphört att gälla

1. Uppdatera din konfigurationsservern till den [senaste versionen](http://aka.ms/unifiedinstaller)
2. Om du har någon skalbar servrar uppdatera återställning Master målservrarna, återställning servrar dem till den senaste versionen
3. Uppdatera Mobilitetstjänsten på alla skyddade virtuella datorer till den senaste versionen.
4. Logga in på konfigurationsservern och öppna en kommandotolk med administratörsbehörighet.
5. Bläddra till mappen %ProgramData%\ASR\home\svsystems\bin
6. Kör RenewCerts.exe om du vill förnya SSL-certifikatet på konfigurationsservern.
7. Om processen lyckas, bör du se meddelandet ”certifikatförnyelse är lyckades”


## <a name="sizing-requirements-for-a-configuration-server"></a>Ändra storlek på kraven för en konfigurationsserver

| **CPU** | **Minne** | **Cachestorleken för disk** | **Dataändringshastighet** | **Skyddade datorer** |
| --- | --- | --- | --- | --- |
| 8 vCPUs (2 sockets * 4 kärnor @ 2,5 GHz) |16 GB |300 GB |500 GB eller mindre |Replikera färre än 100 datorer. |
| 12 vCPUs (2 sockets * @ 2,5 GHz-6 kärnor) |18 GB |600 GB |500 GB till 1 TB |Replikera mellan 100 150 datorer. |
| 16 vCPUs (2 sockets * 8 kärnor @ 2,5 GHz) |32 GB |1 TB |1 TB till 2 TB |Replikera mellan 150 200 datorer. |

  >[!TIP]
  Om din dagliga dataomsättningen överskrider 2 TB, eller om du planerar att replikera mer än 200 virtuella datorer, rekommenderas att distribuera ytterligare servrar för att belastningsutjämna replikeringstrafiken. Lär dig mer om hur du distribuerar skalbar Process-servrarna.


## <a name="common-issues"></a>Vanliga problem
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]
