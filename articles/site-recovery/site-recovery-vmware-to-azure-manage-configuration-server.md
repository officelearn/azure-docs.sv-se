---
title: " Hantera konfigurationsservern för VMware katastrofåterställning med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln beskriver hur du hanterar en befintlig configuration server för VMware katastrofåterställning till Azure med Azure Site Recovery-tjänsten."
services: site-recovery
author: AnoopVasudavan
ms.service: site-recovery
ms.topic: article
ms.date: 01/15/2017
ms.author: anoopkv
ms.openlocfilehash: e9e4bfc86df2cae1facac62472c915d91fb8c84c
ms.sourcegitcommit: 7edfa9fbed0f9e274209cec6456bf4a689a4c1a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2018
---
# <a name="manage-the-configuration-server"></a>Hantera konfigurationsservern

Du konfigurerar en lokal konfigurationsservern när du använder den [Azure Site Recovery](site-recovery-overview.md) tjänsten för katastrofåterställning av virtuella VMware-datorer och fysiska servrar till Azure. Konfigurationsservern samordnar kommunikationen mellan lokala VMware och Azure och hanterar datareplikering. Den här artikeln beskrivs vanliga uppgifter för att hantera konfigurationsservern när de har distribuerats.

## <a name="modify-vmware-settings"></a>Ändra inställningar för VMware

Ändra inställningarna för VMware-server som konfigurationsservern ansluter.

1. Logga in på datorn som kör konfigurationsservern.
2. Starta Azure Site Recovery Configuration Manager från genvägen på skrivbordet. Eller öppna **https://configuration-server-name/IP:44315**.
3. Klicka på **hantera vCenter Server/vSPhere ESXi-servern**:
    - Du associerar en annan VMware-server med konfigurationsservern genom att klicka på **Lägg till vCenter-Server/vSphere ESXi servern**, och ange serverinformation.
    - Om du vill uppdatera autentiseringsuppgifterna för att ansluta till VMware-servern för automatisk identifiering av virtuella VMware-datorer, klickar du på **redigera**. Ange nya autentiseringsuppgifter och klicka sedan på **OK**.

        ![Ändra VMware](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-vmware-server.png)

## <a name="modify-credentials-for-mobility-service-installation"></a>Ändra autentiseringsuppgifter för installationen av Mobility

Ändra de autentiseringsuppgifter som används för att automatiskt installera mobilitetstjänsten på virtuella VMware-datorer aktiveras för replikering.

1. Logga in på datorn som kör konfigurationsservern.
2. Starta Azure Site Recovery Configuration Manager från genvägen på skrivbordet. Eller öppna **https://configuration-server-name/IP:44315**.
3. Klicka på **hantera virtuella autentiseringsuppgifter**, och ange nya autentiseringsuppgifter. Klicka på **OK** att uppdatera inställningarna.

    ![Ändra Mobility Tjänstereferenser](./media/site-recovery-vmware-to-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Ändra proxyinställningar för

Ändra proxyinställningar som används av configuration server-datorn för internet-åtkomst till Azure. Om du har en ytterligare processer server-dator, förutom standardserver för processen som körs på serverdatorn konfiguration kan du ändra inställningarna på båda datorerna.

1. Logga in på datorn som kör konfigurationsservern.
2. Starta Azure Site Recovery Configuration Manager från genvägen på skrivbordet. Eller öppna **https://configuration-server-name/IP:44315**.
3. Klicka på **hantera anslutningen**, och uppdatera proxy-värden. Klicka på **spara** att uppdatera inställningarna.

## <a name="add-a-network-adapter"></a>Lägga till ett nätverkskort

Mallen OVF distribuerar konfigurationsservern virtuell dator med ett enda nätverkskort. Du kan [lägga till ett ytterligare nätverkskort till den virtuella datorn)](how-to-deploy-configuration-server.md#add-an-additional-adapter), men du måste göra detta innan du registrerar konfigurationsservern i valvet.

Om du behöver lägga till ett kort när du har registrerat konfigurationsservern i valvet måste du lägga till nätverkskortet i VM-egenskaper och sedan registrera servern i valvet.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrera en konfigurationsserver i samma valvet

Du kan registrera om konfigurationsservern i samma valvet om du behöver. du har en ytterligare processer server-dator, förutom standardserver för processen som körs på serverdatorn configuration f registrera om båda datorerna.

  1. Öppna i valvet, **hantera** > **Site Recovery-infrastruktur** > **Konfigurationsservrar**.
  2. I **servrar**, klickar du på **Download registreringsnyckel**. Här kan du hämta valvautentiseringsfilen.
  3. Logga in på serverdatorn konfiguration.
  4. In **%ProgramData%\ASR\home\svagent\bin**, open **cspsconfigtool.exe**.
  5. På den **valvet registrering** fliken, klicka på Bläddra och finns valvautentiseringsfilen som du hämtat.
  6. Ange proxy-serverinformation om det behövs. Klicka på **registrera**.
  7. Öppna en Admin PowerShell-Kommandotolken och kör följande kommando:

      ```
      $pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
      net stop obengine
      net start obengine
      ```

## <a name="delete-or-unregister-a-configuration-server"></a>Ta bort eller avregistrera en konfigurationsserver

1. Inaktivera [inaktivera skydd](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) för alla virtuella datorer under konfigurationsservern.
2. [Avassociera](site-recovery-setup-replication-settings-vmware.md#dissociate-a-configuration-server-from-a-replication-policy) och [ta bort](site-recovery-setup-replication-settings-vmware.md#delete-a-replication-policy) alla replikeringsprinciper från konfigurationsservern.
3. [Ta bort](site-recovery-vmware-to-azure-manage-vCenter.md#delete-a-vcenter-in-azure-site-recovery) alla Vcenter-servrar/vSphere-värdar som är kopplade till konfigurationsservern.
4. Öppna i valvet, **Site Recovery-infrastruktur** > **Configuration-servrar**
5. Klicka på konfigurationsservern som du vill ta bort. Klicka sedan på den **information** klickar du på **ta bort**.

    ![Ta bort konfigurationsservern](./media/site-recovery-vmware-to-azure-manage-configuration-server/delete-configuration-server.png)
   

### <a name="delete-with-powershell"></a>Ta bort med PowerShell

Du kan eventuellt ta bort konfigurationsservern med hjälp av PowerShell:

1. [Installera](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) Azure PowerShell-modulen
2. Logga in på ditt Azure-konto med hjälp av kommandot:
    
    `Login-AzureRmAccount`
3. Välj prenumerationen som valvet:

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Ange valvet kontext:
    
    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Hämta konfigurationsservern:

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Ta bort konfigurationsservern:

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Du kan använda den **-Force** alternativ i Remove-AzureRmSiteRecoveryFabric för framtvingad borttagning av konfigurationsservern.
 


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


## <a name="next-steps"></a>Nästa steg

Granska självstudier för att ställa in katastrofåterställning av [virtuella VMware-datorer](tutorial-vmware-to-azure.md) och fysiska servers(tutorial-physical-to-azure.md) till Azure.
