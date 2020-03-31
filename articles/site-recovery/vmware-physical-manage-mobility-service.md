---
title: Hantera Mobilitetsagenten för VMware/fysiska servrar med Azure Site Recovery
description: Hantera Mobility Service-agent för haveriberedskap för virtuella datorer med VMware och fysiska servrar till Azure med hjälp av Azure Site Recovery-tjänsten.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 9be758c286e072b0fbefc5f8b20b7accc4e6741b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256970"
---
# <a name="manage-the-mobility-agent"></a>Hantera mobilitetsagenten 

Du konfigurerar mobilitetsagent på servern när du använder Azure Site Recovery för haveriberedskap av virtuella datorer och fysiska servrar till Azure. Mobilitetsagenten samordnar kommunikationen mellan den skyddade datorn, konfigurationsservern/skalningsprocessservern och hanterar datareplikering. Den här artikeln sammanfattar vanliga uppgifter för att hantera mobilitetsagenten när den har distribuerats.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-mobility-service-from-azure-portal"></a>Uppdatera mobilitetstjänsten från Azure-portalen

1. Innan du börjar se till att konfigurationsservern, utskalningsprocessservrarna och alla huvudmålservrar som ingår i distributionen uppdateras innan du uppdaterar mobilitetstjänsten på skyddade datorer.
2. Öppna valvet > **replikerade objekt**i portalen .
3. Om konfigurationsservern är den senaste versionen visas ett meddelande med texten "Uppdatering av replikeringsagent för ny plats återställning är tillgänglig. Klicka för att installera."

     ![Fönstret Replikerade objekt](./media/vmware-azure-install-mobility-service/replicated-item-notif.png)

4. Klicka på meddelandet och välj de datorer som du vill uppgradera mobilitetstjänsten på i **Agentuppdatering.** Klicka sedan på **OK**.

     ![VM-lista för replikerade objekt](./media/vmware-azure-install-mobility-service/update-okpng.png)

5. Jobbet Uppdatera mobilitetstjänsten startar för var och en av de valda datorerna.

## <a name="update-mobility-service-through-powershell-script-on-windows-server"></a>Uppdatera mobilitetstjänsten via powershell-skript på Windows-servern

Innan du börjar se till att konfigurationsservern, utskalningsprocessservrarna och alla huvudmålservrar som ingår i distributionen uppdateras innan du uppdaterar mobilitetstjänsten på skyddade datorer.

Använd följande skript för att uppgradera mobilitetstjänsten på en server via power shell cmdlet

```azurepowershell
Update-AzRecoveryServicesAsrMobilityService -ReplicationProtectedItem $rpi -Account $fabric.fabricSpecificDetails.RunAsAccounts[0]
```

## <a name="update-mobility-service-manually-on-each-protected-server"></a>Uppdatera mobilitetstjänsten manuellt på varje skyddad server

1. Innan du börjar se till att konfigurationsservern, utskalningsprocessservrarna och alla huvudmålservrar som ingår i distributionen uppdateras innan du uppdaterar mobilitetstjänsten på skyddade datorer.

2. [Leta reda på agentinstallationsprogrammet](vmware-physical-mobility-service-overview.md#locate-installer-files) baserat på serverns operativsystem.

>[!IMPORTANT]
> Om du replikerar Azure IaaS VM från en Azure-region till en annan ska du inte använda den här metoden. Se [vår vägledning](azure-to-azure-autoupdate.md) för information om alla tillgängliga alternativ.

3. Kopiera installationsfilen till den skyddade datorn och kör den för att uppdatera mobilitetsagenten.

## <a name="update-account-used-for-push-installation-of-mobility-service"></a>Update-konto som används för push-installation av mobilitetstjänsten

När du distribuerade Site Recovery, för att aktivera push-installation av mobilitetstjänsten, angav du ett konto som site recovery-processservern använder för att komma åt datorerna och installera tjänsten när replikering är aktiverad för datorn. Om du vill uppdatera autentiseringsuppgifterna för det här kontot följer du [dessa instruktioner](vmware-azure-manage-configuration-server.md#modify-credentials-for-mobility-service-installation).

## <a name="uninstall-mobility-service"></a>Avinstallera mobilitetstjänsten

### <a name="on-a-windows-machine"></a>På en Windows-dator

Avinstallera från användargränssnittet eller från en kommandotolk.

- **Välj Program i användargränssnittet**: I **Programs**datorns kontrollpanel . Välj**Avinstallera** **Microsoft Azure Site Recovery Mobility Service/Huvudmålserver** > .
- **Från en kommandotolk**: Öppna ett kommandotolksfönster som administratör på datorn. Kör följande kommando: 
    ```
    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1} /L+*V "C:\ProgramData\ASRSetupLogs\UnifiedAgentMSIUninstall.log"
    ```

### <a name="on-a-linux-machine"></a>På en Linux-maskin
1. Logga in som **en rotanvändare** på Linux-datorn.
2. I en terminal går du till /usr/local/ASR.
3. Kör följande kommando:
    ```
    uninstall.sh -Y
   ```
   
## <a name="install-site-recovery-vss-provider-on-source-machine"></a>Installera SITE Recovery VSS-provider på källdator

Azure Site Recovery VSS-provider krävs på källdatorn för att generera programkonsekvenspunkter. Om installationen av leverantören inte lyckades genom push-installation följer du nedanstående riktlinjer för att installera den manuellt.

1. Öppna cmd-fönstret för admin.
2. Navigera till installationsplatsen för mobilitetstjänster. (T.ex. C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent)
3. Kör skriptet InMageVSSProvider_Uninstall.cmd . Detta kommer att avinstallera tjänsten om den redan finns.
4. Kör skriptet InMageVSSProvider_Install.cmd för att installera VSS-providern manuellt.

## <a name="next-steps"></a>Nästa steg

- [Konfigurera haveriberedskap för virtuella datorer med VMware](vmware-azure-tutorial.md)
- [Konfigurera haveriberedskap för fysiska servrar](physical-azure-disaster-recovery.md)
