---
title: Hantera konfigurationsservern för VMware-haveriberedskap med Azure Site Recovery | Microsoft Docs
description: Den här artikeln beskriver hur du hanterar en befintlig konfigurationsservern för VMware-haveriberedskap till Azure med Azure Site RecoveryS.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346128"
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
   ```

      >[!NOTE] 
      >För att **Hämta senaste certifikaten** från konfigurationsservern till skalbar processerver kör du kommandot *”< Installation Drive\Microsoft Azure plats Recovery\agent\cdpcli.exe >” – registermt*

  8. Slutligen startar du om obengine genom att köra följande kommando.
  ```
          net stop obengine
          net start obengine

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = get-AzureRmRecoveryServicesVault-namnet <name of your vault> Set-AzureRmSiteRecoveryVaultSettings - ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
