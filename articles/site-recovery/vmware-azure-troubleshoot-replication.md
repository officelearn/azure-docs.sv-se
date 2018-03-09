---
title: "Felsökning av problem med replikering för VMware VM och fysisk serverreplikering till Azure med Azure Site Recovery | Microsoft Docs"
description: "Den här artikeln innehåller felsökning av vanliga replikeringsproblem med vid replikering av virtuella VMware-datorer och fysiska servrar till Azure med Azure Site Recovery."
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: asgang
ms.openlocfilehash: 9291840428c9a8d7ba5d65bc94ce5964728316f3
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/08/2018
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsökning av problem med replikering för virtuella VMware-datorer och fysiska servrar

organisationsenhet kan få ett visst felmeddelande när du skyddar din virtuella VMware-datorer eller fysiska servrar med hjälp av Azure Site Recovery. Den här artikeln beskriver några vanliga problem som kan uppstå när du replikera lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="initial-replication-issues"></a>Problem med inledande replikering.

I många fall är inledande replikeringsfel som vi stöta på support på grund av problem med nätverksanslutningen mellan serverprocessen till källservern eller processen server till Azure. I de flesta fall kan du felsöker dessa problem genom att följa stegen nedan.

### <a name="verify-the-source-machine"></a>Kontrollera källdatorn
* Använd Telnet pinga Processervern med https-porten (standard 9443) som du ser nedan för att se om det finns några problem med nätverksanslutningen eller brandväggen port blockerande problem från källservern datorn kommandoraden.

    `telnet <PS IP address> <port>`
> [!NOTE]
    > Använder Telnet kan inte använda PING för att testa anslutningen.  Om Telnet inte är installerad, följer du stegen listan [här](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Om det inte går att ansluta, tillåter inkommande port 9443 på Processervern och kontrollera om problemet fortfarande avslutas. Det har uppstått tillfällen där processervern har bakom DMZ som orsakade det här problemet.

* Kontrollera status för tjänsten `InMage Scout VX Agent – Sentinel/OutpostStart` om den inte körs och kontrollera om problemet kvarstår.   

## <a name="verify-the-process-server"></a>Kontrollera processervern

* **Kontrollera om processervern aktivt skicka data till Azure**

Öppna Aktivitetshanteraren (tryck på Ctrl-Skift-Esc) från Processervern datorn. Gå till fliken prestanda och klicka på öppna Resursövervakaren länk. Från hanteraren för filserverresurser, gå till fliken nätverk. Kontrollera om cbengine.exe i ”processer med nätverksaktivitet” aktivt skickar stora mängder (i MB) data.

![Aktivera replikering](./media/vmware-azure-troubleshoot-replication/cbengine.png)

Om inte, Följ stegen nedan:

* **Kontrollera om processervern inte kan ansluta Azure Blob**: Välj och kontrollera cbengine.exe om du vill visa den 'TCP-anslutningar, för att se om det finns en anslutning från processervern till Azure Storage blob-URL.

![Aktivera replikering](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

Om det inte gå till Kontrollpanelen > tjänster, kontrollera att följande tjänster är igång:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     *
(Re) Starta alla-tjänsten som inte körs och kontrollera om problemet kvarstår.

* **Kontrollera om processervern inte kan ansluta till Azure offentliga IP-adress via port 443**

Öppna senaste CBEngineCurr.errlog från `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` och Sök efter: 443 och anslutningen misslyckades.

![Aktivera replikering](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

Om det uppstår problem kan från Processervern kommandorad Använd telnet pinga din Azure offentlig IP-adress (maskeras i ovanstående bild) hittades i CBEngineCurr.currLog via port 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Kontrollera om problemet beror på brandvägg eller proxyserver som beskrivs i nästa steg om det inte går att ansluta.


* **Kontrollera om IP-adressbaserade brandväggen på processervern inte blockerar åtkomsten**: Om du använder en IP-adressbaserade brandväggsregler på servern sedan hämta den fullständiga listan över Microsoft Azure Datacenter IP-intervall från [här](https://www.microsoft.com/download/details.aspx?id=41653) och lägga till dem i brandväggskonfigurationen för att säkerställa att de tillåter kommunikation till Azure (och HTTPS (443)-port).  Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).

* **Kontrollera om URL-baserade processervern-brandväggen inte blockerar åtkomsten**: Om du använder en URL-baserade brandväggsregler på servern, kan du kontrollera att följande webbadresser läggs till brandväggskonfigurationen.

  `*.accesscontrol.windows.net:` Används för Access Control och identitetshantering

  `*.backup.windowsazure.com:` Används för överföring av replikeringsdata och dirigering

  `*.blob.core.windows.net:` Används för åtkomst till lagringskontot som lagrar replikerade data

  `*.hypervrecoverymanager.windowsazure.com:` Används för åtgärder för replikeringshantering och dirigering

  `time.nist.gov` och `time.windows.com`: används för att kontrollera tidssynkronisering mellan system och global tid.

URL: er för **Azure Government molnet**:

`* .ugv.hypervrecoverymanager.windowsazure.us`

`* .ugv.backup.windowsazure.us`

`* .ugi.hypervrecoverymanager.windowsazure.us`

`* .ugi.backup.windowsazure.us`

* **Kontrollera om proxyinställningarna på processervern inte blockerar åtkomst**.  Om du använder en proxyserver, se till att namnet på proxyservern matchar DNS-servern.
För att kontrollera att du har angett vid tidpunkten för installationsprogrammet för konfigurationsservern. Gå till registernyckeln

    `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings`

Se nu till att samma inställningar som används av Azure Site Recovery-agenten för att skicka data.
Sök Microsoft Azure Backup

Öppna den och klicka på åtgärden > ändra egenskaper. Du bör se proxyadress som ska vara densamma som visas av registerinställningarna under fliken proxykonfiguration. Om du inte ändra det till samma adress.


* **Kontrollera om begränsning av bandbredd inte är begränsad på processervern**: öka bandbredden och kontrollera om problemet kvarstår.

## <a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp för att sedan skicka frågan [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv community och en av våra tekniker kommer att kunna hjälpa dig.
