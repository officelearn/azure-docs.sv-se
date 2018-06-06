---
title: Hantera konfigurationsservern för VMware katastrofåterställning med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar en befintlig configuration server för VMware katastrofåterställning till Azure med Azure Site RecoveryS.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 06/04/2018
ms.author: raynew
ms.openlocfilehash: 64f5f2105a9048d649503b0790231676182a4c4f
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34737511"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Hantera konfigurationsservern för virtuella VMware-datorer

Du konfigurerar en lokal konfigurationsservern när du använder [Azure Site Recovery](site-recovery-overview.md) för katastrofåterställning av virtuella VMware-datorer och fysiska servrar till Azure. Konfigurationsservern samordnar kommunikationen mellan lokala VMware och Azure och hanterar datareplikering. Den här artikeln beskrivs vanliga uppgifter för att hantera konfigurationsservern när den har distribuerats.



## <a name="modify-vmware-settings"></a>Ändra inställningar för VMware

Du kan komma åt konfigurationsservern enligt följande:
    - Logga in på den virtuella datorn har distribuerats och starta Azure Site Recovery Configuration Manager från genvägen på skrivbordet.
    - Du kan också komma åt konfigurationsservern via en fjärranslutning från **https://*ConfigurationServerName*/:44315 /**. Logga in med administratörsbehörighet.
   
### <a name="modify-vmware-server-settings"></a>Ändra inställningarna för VMware

1. Om du vill associera en annan VMware-server med konfigurationsservern, när du loggar in, markera **Lägg till vCenter-Server/vSphere ESXi servern**.
2. Ange information och välj sedan **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Ändra autentiseringsuppgifter för automatisk identifiering

1. Om du vill uppdatera autentiseringsuppgifterna för att ansluta till VMware-server för automatisk identifiering av virtuella VMware-datorer, efter inloggning, Välj **redigera**.
2. Ange nya autentiseringsuppgifter och välj sedan **OK**.

    ![Ändra VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Ändra autentiseringsuppgifter för installation av Mobilitetstjänsten

Ändra de autentiseringsuppgifter som används för att automatiskt installera Mobilitetstjänsten på den virtuella VMware-datorer aktiveras för replikering.

1. Efter inloggning, Välj **hantera virtuella autentiseringsuppgifter**
2. Ange nya autentiseringsuppgifter och välj sedan **OK**.

    ![Ändra Mobilitetstjänsten autentiseringsuppgifter](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Ändra proxyinställningar för

Ändra proxyinställningar som används av configuration server-datorn för internet-åtkomst till Azure. Om du har en dator med processen server förutom standardserver för processen som körs på serverdatorn konfiguration kan du ändra inställningarna på båda datorerna.

1. Logga in på konfigurationsservern, markerar **hantera anslutningen**.
2. Uppdatera proxy-värden. Välj sedan **spara** att uppdatera inställningarna.

## <a name="add-a-network-adapter"></a>Lägga till ett nätverkskort

Öppna virtualisering (OVF) mallen distribuerar konfigurationsservern virtuell dator med ett enda nätverkskort.

- Du kan [lägga till ett ytterligare nätverkskort till den virtuella datorn)](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), men du måste lägga till den innan du registrerar konfigurationsservern i valvet.
- Om du vill lägga till ett kort när du har registrerat konfigurationsservern i valvet, lägger du till nätverkskortet i VM-egenskaper. Du måste registrera servern i valvet.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrera en konfigurationsserver i samma valvet

Du kan registrera om konfigurationsservern i samma valvet om du behöver. Om du har en ytterligare processer server-dator, förutom standardserver för processen som körs på serverdatorn konfiguration kan du registrera om båda datorerna.


  1. Öppna i valvet, **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
  2. I **servrar**väljer **Download registreringsnyckel** att hämta valvautentiseringsfilen.
  3. Logga in på serverdatorn konfiguration.
  4. I **%ProgramData%\ASR\home\svsystems\bin**öppnar **cspsconfigtool.exe**.
  5. På den **valvet registrering** väljer **Bläddra**, och leta upp valvautentiseringsfilen som du hämtat.
  6. Ange proxy-serverinformation om det behövs. Välj sedan **Registrera**.
  7. Öppna ett PowerShell-Kommandotolken som administratör och kör följande kommando:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="upgrade-the-configuration-server"></a>Uppgradera konfigurationsservern

Du kan köra samlade uppdateringar för att uppdatera konfigurationsservern. Uppdateringar kan användas för upp till N-4 versioner. Exempel:

- Om du kör 9.7 9.8, 9.9 eller 9.10, kan du uppgradera direkt till 9.11.
- Om du kör 9,6 eller tidigare och du vill uppgradera till 9.11, måste du först uppgradera till version 9.7. innan du 9.11.

Länkar till samlade uppdateringar för uppgradering av alla versioner av konfigurationsservern är tillgängliga i den [uppdateringar wiki-sida](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Uppgradera servern på följande sätt:

1. I valvet, går du till **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
2. Om en uppdatering är tillgänglig, visas en länk i den **Agentversion** > kolumn.

    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update2.png)

1. Hämta installationsfilen för uppdateringen till konfigurationsservern.

    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update1.png)

4. Dubbelklicka för att köra installationsprogrammet.
2. Installationsprogrammet identifierar den aktuella versionen som körs på datorn. Klicka på **Ja** starta uppgraderingen. 
3. När uppgraderingen har slutförts verifierar serverkonfigurationen.

    ![Uppdatering](./media/vmware-azure-manage-configuration-server/update3.png)

4. Klicka på **Slutför** stängs installationsprogrammet.


## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurationsserver

1. [Inaktivera skyddet](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under konfigurationsservern.
2. [Avassociera](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) och [ta bort](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) alla replikeringsprinciper från konfigurationsservern.
3. [Ta bort](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) alla vCenter-servrar/vSphere-värdar som är kopplade till konfigurationsservern.
4. Öppna i valvet, **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
5. Välj den configuration-server som du vill ta bort. Klicka sedan på den **information** väljer **ta bort**.

    ![Ta bort konfigurationsservern](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Ta bort med PowerShell

Du kan eventuellt ta bort konfigurationsservern med hjälp av PowerShell.

1. [Installera](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell-modulen.
2. Logga in på ditt Azure-konto med hjälp av det här kommandot:
    
    `Connect-AzureRmAccount`
3. Välj prenumerationen för valvet.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Ange valvet kontext.
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Hämta konfigurationsservern.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurationsservern.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Du kan använda den **-Force** alternativ i Remove-AzureRmSiteRecoveryFabric för framtvingad borttagning av konfigurationsservern.
 


## <a name="renew-ssl-certificates"></a>Förnya SSL-certifikat

Konfigurationsservern har en webbserver som samordnar Mobilitetstjänsten, servrar och huvudmålservern servrar som är anslutna till den inbyggda. Webbservern använder ett SSL-certifikat för att autentisera klienter. Certifikatet upphör att gälla efter tre år och kan förnyas när som helst.

### <a name="check-expiry"></a>Kontrollera upphör att gälla

För configuration-serverdistribution före maj 2016 har certifikatet upphör att gälla angetts till ett år. Om du har ett certifikat som upphör att gälla, inträffar följande:

- När utgångsdatum är två månader eller mindre, tjänsten börjar skicka meddelanden i portalen och via e-post (om du prenumererar på Site Recovery-meddelanden).
- En meddelandebanderoll visas på sidan valvet resurs. Välj popup-meddelandet för mer information.
- Om du ser en **uppgradera nu** knappen, anger det att vissa komponenter i din miljö inte har uppgraderats till 9.4.xxxx.x eller senare versioner. Uppgradering av komponenterna innan du förnyar certifikatet. Du kan inte förnya på äldre versioner.

### <a name="renew-the-certificate"></a>Förnya certifikatet

1. Öppna i valvet, **Site Recovery-infrastruktur** > **konfigurationsservern**. Välj obligatoriska konfigurationsservern.
2. Utgångsdatum visas under **konfigurationsservern hälsa**.
3. Välj **förnya certifikat**. 


## <a name="next-steps"></a>Nästa steg

Granska självstudier för att ställa in katastrofåterställning av [virtuella VMware-datorer](vmware-azure-tutorial.md) till Azure.
