---
title: Felsöka problem med replikering för akutåterställning av virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller information om att felsöka vanliga replikeringsproblem under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 12/17/2018
ms.author: ramamill
ms.openlocfilehash: 1c37b764b47856d3a369228d3f224f2a464029bb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790664"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsöka problem med replikering för virtuella VMware-datorer och fysiska servrar

Du får ett specifikt felmeddelande när du skyddar dina virtuella VMware-datorer eller fysiska servrar med Azure Site Recovery. Den här artikeln beskriver några vanliga problem som kan uppstå när replikera lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).


## <a name="initial-replication-issues"></a>Problem med inledande replikering

I många fall är den inledande replikeringsfel som vi påträffar på support på grund av problem med nätverksanslutningen mellan serverprocessen till källservern eller process server till Azure. Du kan felsöka de här problemen genom att följa stegen nedan för de flesta fall.

### <a name="verify-the-source-machine"></a>Kontrollera källdatorn
* Använd Telnet pinga Processervern med https-porten (standard 9443) som visas nedan för att se om det finns problem med nätverksanslutningen eller brandväggen port blockerande problem från källservern datorn från kommandoraden.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Använda Telnet kan inte använda PING för att testa anslutningen.  Om Telnet inte är installerad, följer du stegen listan [här](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Om det inte går att ansluta, Tillåt inkommande port 9443 på Processervern och kontrollera om problemet fortfarande avslutas. Det har varit tillfällen där processervern har bakom DMZ som orsakade problemet.

* Kontrollera status för tjänsten `InMage Scout VX Agent – Sentinel/OutpostStart` om den inte körs och kontrollera om problemet kvarstår.   

### <a name="verify-the-process-server"></a>Kontrollera processervern

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


* **Kontrollera om IP-adressbaserade brandväggsregler på processervern blockerar åtkomst**: Om du använder en IP-adressbaserade brandväggsregler på servern, ladda ned den fullständiga listan med Microsoft Azure Datacenter IP-intervall från [här](https://www.microsoft.com/download/details.aspx?id=41653) och lägga till dem i brandväggskonfigurationen av för att säkerställa att de tillåter kommunikation till Azure (och HTTPS-port (443)).  Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).

* **Kontrollera om URL-baserad brandvägg på processervern blockerar åtkomst**:  Om du använder en URL-baserad brandväggsregler på servern, se till att följande webbadresser läggs till brandväggskonfigurationen.

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

* **Kontrollera om inte proxyinställningar på processervern blockerar åtkomst**.  Om du använder en proxyserver, se till att namnet på proxyservern matchar DNS-servern.
Om du vill kontrollera vad har du angett vid tidpunkten för installationen av konfigurationsservern. Gå till registernyckeln

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Se nu till att samma inställningar som används av Azure Site Recovery-agenten för att skicka data.
Sök Microsoft Azure Backup

Öppna den och klicka på åtgärden > ändra egenskaper. Du bör se proxyadress som ska vara samma som visas av registerinställningarna under fliken proxykonfiguration. Annars kan du ändra det till samma adress.


* **Kontrollera om begränsa bandbredden inte är begränsad på processervern**:  Öka bandbredden och kontrollera om problemet kvarstår.

## <a name="source-machine-to-be-protected-through-site-recovery-is-not-listed-on-azure-portal"></a>Källdatorn som ska skyddas genom Site Recovery visas inte på Azure-portalen

När du försöker väljer källdatorn för att aktivera replikering via Azure Site Recovery kanske datorn inte tillgänglig för dig att fortsätta på grund av följande orsaker

* Om det finns två virtuella datorer under vCenter med samma instans UUID, sedan den första virtuella datorn som identifierats av konfigurationsservern visas på portalen. Lös genom att kontrollera att inga två virtuella datorer har samma instans UUID.
* Se till att du har lagt till korrekt vCenter-autentiseringsuppgifter under uppsättningen med hjälp av OVF-mall eller enhetlig uppsättning. För att kontrollera de autentiseringsuppgifter som har lagts till, referera till de riktlinjer som delas [här](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* Om de behörigheter som finns att få åtkomst till vCenter inte har tillräckliga privilegier, kan det leda till fel vid identifiering av virtuella datorer. Se till att de behörigheter som finns [här](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) läggs till vCenter-användarkonto.
* Om den virtuella datorn är redan skyddade via Site Recovery kan sedan kan den inte skyddas. Se till att den virtuella datorn som du söker på portalen redan inte skyddas av någon annan användare eller under andra prenumerationer.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Skyddade virtuella datorer är gråmarkerat ut i portalen

Virtuella datorer som replikeras under Site Recovery är nedtonad om det dubbla poster i systemet. Referera till riktlinjerna [här](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) att ta bort inaktuella poster och lösa problemet.

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp kan sedan publicera din fråga till [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv community och en av våra tekniker kan hjälpa dig.
