---
title: Hantera konfigurationsservern för VMware-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar en befintlig konfigurationsservern för VMware-haveriberedskap till Azure med Azure Site RecoveryS.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: d7c2224e6529d1675cdad5b29de887f19135a2a6
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/09/2018
ms.locfileid: "37916918"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Hantera konfigurationsservern för VMware-datorer

Du ställer in en konfigurationsservern lokalt när du använder [Azure Site Recovery](site-recovery-overview.md) för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure. Konfigurationsservern samordnar kommunikationen mellan lokala VMware- och Azure och hanterar datareplikering. Den här artikeln sammanfattas vanliga uppgifter för att hantera configuration server när den har distribuerats.



## <a name="modify-vmware-settings"></a>Ändra inställningarna för VMware

Du kan komma åt konfigurationsservern på följande sätt:
    - Logga in på den virtuella datorn har distribuerats och starta Azure Site Recovery Configuration Manager från genvägen på skrivbordet.
    - Du kan också komma åt configuration server via en fjärranslutning från **https://*ConfigurationServerName*/:44315 /**. Logga in med administratörsbehörighet.
   
### <a name="modify-vmware-server-settings"></a>Ändra inställningarna för VMware-server

1. Om du vill associera en annan VMware-server med konfigurationsservern efter inloggning, Välj **Lägg till vCenter Server/vSphere ESXi-servern**.
2. Ange information och välj sedan **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Ändra autentiseringsuppgifter för automatisk identifiering

1. Om du vill uppdatera de autentiseringsuppgifter som används för att ansluta till VMware-servern för automatisk identifiering av virtuella VMware-datorer, efter inloggning, Välj **redigera**.
2. Ange de nya autentiseringsuppgifterna och välj sedan **OK**.

    ![Ändra VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Ändra autentiseringsuppgifter för installation av Mobilitetstjänsten

Ändra autentiseringsuppgifterna som används för att automatiskt installera Mobilitetstjänsten på den virtuella VMware-datorer du aktivera för replikering.

1. Efter inloggningen, Välj **hantera autentiseringsuppgifter för virtuell dator**
2. Ange de nya autentiseringsuppgifterna och välj sedan **OK**.

    ![Ändra autentiseringsuppgifter för Mobilitetstjänsten](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Ändra proxyinställningar för

Ändra proxyinställningar som används av configuration server-datorn för internet-åtkomst till Azure. Om du har en process server-dator utöver standard-processerver som körs på configuration server-dator kan du ändra inställningarna på båda datorerna.

1. Efter inloggningen till konfigurationsservern, Välj **hantera anslutningar**.
2. Uppdatera proxy-värden. Välj sedan **spara** att uppdatera inställningarna.

## <a name="add-a-network-adapter"></a>Lägga till ett nätverkskort

Open Virtualization Format (OVF) mallen distribuerar configuration server VM med ett enda nätverkskort.

- Du kan [lägga till ytterligare en adapter till den virtuella datorn](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), men du måste lägga till den innan du registrerar konfigurationsservern i valvet.
- Lägg till ett kort när du registrerar konfigurationsservern i valvet genom att lägga till nätverkskortet i virtuella datorns egenskaper. Måste du registrera om servern i valvet.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrera en konfigurationsserver i samma valv

Du kan registrera om konfigurationsservern på samma valv om du behöver. Om du har en ytterligare process server-dator, utöver standard-processerver som körs på configuration server-dator kan du registrera om båda datorerna.


  1. Öppna i valvet, **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
  2. I **servrar**väljer **ladda ned Registreringsnyckeln** att hämta valvautentiseringsfilen.
  3. Logga in på configuration server-dator.
  4. I **%ProgramData%\ASR\home\svsystems\bin**öppnar **cspsconfigtool.exe**.
  5. På den **Vault registrering** fliken **Bläddra**, och leta upp filen med valvautentiseringsuppgifter som du laddade ned.
  6. Om det behövs kan du ange proxy serverinformation. Välj sedan **Registrera**.
  7. Öppna en PowerShell-kommandofönster som administratör och kör följande kommando:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurationsservern

Du kan köra samlade uppdateringar för att uppdatera konfigurationsservern. Uppdateringar kan användas för upp till N-4 versioner. Exempel:

- Om du kör 9.7, 9.8, 9.9 eller 9.10, kan du uppgradera direkt till 9.11.
- Om du kör 9,6 eller tidigare och du vill uppgradera till 9.11, måste du först uppgradera till version 9.7. innan du 9.11.

Länkar till samlade uppdateringar för att uppgradera till alla versioner av konfigurationsservern är tillgängliga i den [wiki-sida för uppdateringar](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uppgradera servern på följande sätt:

1. I valvet går du till **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
2. Om det finns en uppdatering, en länk som visas i den **Agentversion** > kolumn.
    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update2.png)
3. Ladda ned installationsfilen för uppdateringen till konfigurationsservern.

    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dubbelklicka för att köra installationsprogrammet.
5. Installationsprogrammet identifierar den aktuella versionen som körs på datorn. Klicka på **Ja** starta uppgraderingen.
6. När uppgraderingen har slutförts verifierar serverkonfigurationen.

    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Klicka på **Slutför** stängs installationsprogrammet.

## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller Avregistrerar en konfigurationsserver

1. [Inaktivera skyddet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under konfigurationsservern.
2. [Ta bort koppling till](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) och [ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alla replikeringsprinciper från konfigurationsservern.
3. [Ta bort](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alla vCenter-servrar/vSphere-värdar som är associerade med konfigurationsservern.
4. Öppna i valvet, **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
5. Välj configuration server som du vill ta bort. Klicka på den **information** väljer **ta bort**.

    ![Ta bort konfigurationsservern](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Ta bort med PowerShell

Du kan också ta bort konfigurationsservern med hjälp av PowerShell.

1. [Installera](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell-modulen.
2. Logga in på ditt Azure-konto med hjälp av det här kommandot:
    
    `Connect-AzureRmAccount`
3. Välj valvprenumerationen som.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Ange valvets sammanhang.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Hämta konfigurationsservern.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurationsservern.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Du kan använda den **-Force** alternativ i Remove-AzureRmSiteRecoveryFabric för Tvingad borttagning av konfigurationsservern.
 
## <a name="generate-configuration-server-passphrase"></a>Generera konfigurationsservern lösenfras

1. Logga in på konfigurationsservern och öppna ett kommandotolksfönster som administratör.
2. Om du vill ändra katalogen till bin-mappen, kör du kommandot **cd %ProgramData%\ASR\home\svsystems\bin**
3. Om du vill generera lösenfrasfilen köra **genpassphrase.exe - v > MobSvc.passphrase**.
4. Lösenfrasen kommer att lagras i filen i **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Förnya SSL-certifikat

Konfigurationsservern har en inbyggd webbserver som samordnar aktiviteter av den Mobilitetstjänsten och processervrar huvudmålservrar som är anslutna till den. Webbservern använder ett SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrollera förfallodatum

Certifikatets förfallodatum angavs för configuration server-distributioner innan maj 2016, till ett år. Om du har ett certifikat som upphör att gälla, inträffar följande:

- När utgångsdatum är två månader eller mindre, tjänsten börjar skicka meddelanden i portalen och via e-post (om du prenumererar på Site Recovery-meddelanden).
- En meddelandebanderoll visas på resurssidan för valvet. Välj banderollen för mer information.
- Om du ser en **uppgradera nu** knapp, betyder det att vissa komponenter i din miljö inte har uppgraderats till 9.4.xxxx.x eller senare versioner. Uppgradera komponenterna innan du förnyar certifikatet. Du kan inte förnya på äldre versioner.

### <a name="renew-the-certificate"></a>Förnya certifikatet

1. Öppna i valvet, **Site Recovery-infrastruktur** > **konfigurationsservern**. Välj den obligatoriska konfiguration-servern.
2. Utgångsdatum visas under **konfigurationsservern hälsotillstånd**.
3. Välj **förnya certifikat**. 


## <a name="next-steps"></a>Nästa steg

Gå igenom självstudierna för att konfigurera haveriberedskap för [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
