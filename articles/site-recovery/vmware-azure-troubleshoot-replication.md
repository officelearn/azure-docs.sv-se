---
title: Felsöka problem med replikering för VMware VM och fysisk serverreplikering till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller felsökning av vanliga replikeringsproblem när du replikerar virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery.
services: site-recovery
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: ramamill
ms.openlocfilehash: f305f552d576f58914bc33351331f1da3c68bc23
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2018
ms.locfileid: "37951656"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsöka problem med replikering för virtuella VMware-datorer och fysiska servrar

Du får ett specifikt felmeddelande när du skyddar dina virtuella VMware-datorer eller fysiska servrar med Azure Site Recovery. Den här artikeln beskriver några vanliga problem som kan uppstå när replikera lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Inledande replikeringsproblem.

I många fall är den inledande replikeringsfel som vi påträffar på support på grund av problem med nätverksanslutningen mellan serverprocessen till källservern eller process server till Azure. Du kan felsöka de här problemen genom att följa stegen nedan för de flesta fall.

### <a name="verify-the-source-machine"></a>Kontrollera källdatorn
* Använd Telnet pinga Processervern med https-porten (standard 9443) som visas nedan för att se om det finns problem med nätverksanslutningen eller brandväggen port blockerande problem från källservern datorn från kommandoraden.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Använda Telnet kan inte använda PING för att testa anslutningen.  Om Telnet inte är installerad, följer du stegen listan [här](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Om det inte går att ansluta, Tillåt inkommande port 9443 på Processervern och kontrollera om problemet fortfarande avslutas. Det har varit tillfällen där processervern har bakom DMZ som orsakade problemet.

* Kontrollera status för tjänsten `InMage Scout VX Agent – Sentinel/OutpostStart` om den inte körs och kontrollera om problemet kvarstår.   

## <a name="verify-the-process-server"></a>Kontrollera processervern

* **Kontrollera om processervern aktivt skicka data till Azure**

Process Server-dator, öppna Aktivitetshanteraren (tryck på Ctrl-Shift-Esc). Gå till fliken prestanda och klicka på öppna Resursövervakaren länken. Från Resource Manager går du till fliken nätverk. Kontrollera om cbengine.exe i ”processer med nätverksaktivitet” aktivt skickar stora mängder (i MB) data.

![Aktivera replikering](./media/vmware-azure-troubleshoot-replication/cbengine.png)

Om så inte är fallet, Följ stegen nedan:

* **Kontrollera om processervern är att ansluta Azure Blob**: Välj och kontrollera cbengine.exe om du vill visa den ”TCP-anslutningar” för att se om det finns en anslutning från processervern till Azure Storage blob URL.

![Aktivera replikering](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Om det inte gå till Kontrollpanelen > tjänster, kontrollera att följande tjänster är igång:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Åter) Starta tjänsten som inte körs och kontrollera om problemet kvarstår.

* **Kontrollera om processervern kan ansluta till Azures offentliga IP-adress via port 443**

Öppna senaste CBEngineCurr.errlog från `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` och Sök efter: 443 och anslutningen anslutningsförsöket misslyckades.

![Aktivera replikering](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Om det finns problem, sedan från Processervern kommandorad använda telnet pinga din Azures offentliga IP-adressen som (maskeras i ovan bild) finns i CBEngineCurr.currLog via port 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Om det inte går att ansluta kan du kontrollera om Åtkomstproblemet beror på att brandväggen eller proxyservern som beskrivs i nästa steg.


* **Kontrollera om IP-adressbaserade brandväggsregler på processervern blockerar åtkomst**: Om du använder en IP-adressbaserade brandväggsregler på servern, ladda ned den fullständiga listan med Microsoft Azure Datacenter IP-intervall från [här](https://www.microsoft.com/download/details.aspx?id=41653) och lägga till dem i brandväggskonfigurationen till att de tillåter kommunikation till Azure (och HTTPS-port (443)).  Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).

* **Kontrollera om URL-baserad brandvägg på processervern blockerar åtkomst**: Om du använder en URL-baserad brandväggsregler på servern, se till att följande webbadresser läggs till brandväggskonfigurationen.

  `*.accesscontrol.windows.net:` Används för Access Control och identitetshantering

  `*.backup.windowsazure.com:` Används för överföring av replikeringsdata och dirigering

  `*.blob.core.windows.net:` Används för åtkomst till lagringskontot som lagrar replikerade data

  `*.hypervrecoverymanager.windowsazure.com:` Används för åtgärder för replikeringshantering och dirigering

  `time.nist.gov` och `time.windows.com`: används för att kontrollera tidssynkronisering mellan system och global tid.

URL: er för **Azure Government-molnet**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Kontrollera om inte proxyinställningar på processervern blockerar åtkomst**.  Om du använder en proxyserver, se till att namnet på proxyservern matchar DNS-servern.
Om du vill kontrollera vad har du angett vid tidpunkten för installationen av konfigurationsservern. Gå till registernyckeln

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Se nu till att samma inställningar som används av Azure Site Recovery-agenten för att skicka data.
Sök Microsoft Azure Backup

Öppna den och klicka på åtgärden > ändra egenskaper. Du bör se proxyadress som ska vara samma som visas av registerinställningarna under fliken proxykonfiguration. Annars kan du ändra det till samma adress.


* **Kontrollera om begränsa bandbredden inte är begränsad på processervern**: öka bandbredden och kontrollera om problemet kvarstår.

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan sedan publicera din fråga till [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv community och en av våra tekniker kan hjälpa dig.
