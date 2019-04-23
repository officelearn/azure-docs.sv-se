---
title: Hantera mobilitetsagenten på servrar på haveriberedskap av virtuella VMware-datorer och fysiska servrar med Azure Site Recovery | Microsoft Docs
description: Hantera mobilitetstjänstagenten för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 69b8e1c533747d1bade69949911ea43f299f49e9
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59794243"
---
# <a name="manage-mobility-agent-on-protected-machines"></a>Hantera mobilitetsagenten på skyddade datorer

Du konfigurerar mobilitetsagenten på servern när du använder Azure Site Recovery för haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure. Mobilitetsagenten samordnar kommunikationen mellan den skyddade datorn configuration server-/ skalbar processerver och hanterar datareplikering. Den här artikeln sammanfattas vanliga uppgifter för att hantera mobilitetsagenten när den har distribuerats.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Uppdateringen av mobilitetstjänsten från Azure-portalen

1. Innan du börjar måste du kontrollera att konfigurationsservern och skala ut processervrar någon huvudmålservern-server som är en del av distributionen uppdateras innan du uppdaterar Mobilitetstjänsten på skyddade datorer.
2. Öppna i portalen valvet > **replikerade objekt**.
3. Om konfigurationsservern är den senaste versionen kan se du ett meddelande som läser ”ny Site recovery replikering agentuppdatering är tillgänglig. Klicka för att installera ”.

     ![Replikerade objekt fönster](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klicka på meddelandet och i **agentuppdatering**, Välj de datorer som du vill uppgradera mobilitetstjänsten. Klicka sedan på **OK**.

     ![Replikerade objekt VM-lista](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Uppdateringen av Mobilitetstjänsten jobbet startar för var och en av de valda datorerna.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Uppdateringen av mobilitetstjänsten via powershell-skript på Windows server

Använd följande skript för att uppgradera mobilitetstjänsten på en server via power shell-cmdlet

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Uppdatera kontot som används för push-installation av mobilitetstjänsten

När du har distribuerat Site Recovery för att aktivera push-installation av mobilitetstjänsten, du har angett ett konto som processervern för Site Recovery använder för att få åtkomst till datorerna och installera tjänsten när replikering har aktiverats för datorn. Om du vill uppdatera autentiseringsuppgifterna för det här kontot kan du följa [instruktionerna](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Avinstallera mobilitetstjänsten

### <a name="on-a-windows-machine"></a>På en Windows-dator

Avinstallera i användargränssnittet eller från en kommandotolk.

- **I användargränssnittet**: I Kontrollpanelen på datorn, väljer **program**. Välj **Microsoft Azure Site Recovery Mobility Service/huvudmålservern** > **avinstallera**.
- **Från en kommandotolk**: Öppna ett kommandotolksfönster som administratör på datorn. Kör följande kommando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>På en Linux-dator
1. Logga in på Linux-dator som en **rot** användare.
2. Gå till /user/local/ASR i en terminal.
3. Kör följande kommando:
    ```
    uninstall.sh -Y

## Install Site Recovery VSS provider on source machine

Azure Site Recovery VSS provider is required on the source machine to generate application consistency points. If the installation of the provider didn't succeed through push installation, follow the below given guidelines to install it manually.

1. Open admin cmd window.
2. Navigate to the mobility service installation location. (Eg - C:\Program Files (x86)\Microsoft Azure Site Recovery\agent)
3. Run the script InMageVSSProvider_Uninstall.cmd . This will uninstall the service if it already exists.
4. Run the script InMageVSSProvider_Install.cmd to install the VSS provider manually.

## Next steps

- [Set up disaster recovery for VMware VMs](vmware-azure-tutorial.md)
- [Set up disaster recovery for physical servers](physical-azure-disaster-recovery.md)
