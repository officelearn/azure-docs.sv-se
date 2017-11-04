---
title: "Felsöka skydd fel VMware/fysiska till Azure | Microsoft Docs"
description: "Den här artikeln beskrivs vanliga replikeringsfel för VMware-dator och felsöka dem."
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/26/2017
ms.author: asgang
ms.openlocfilehash: 6ebec2e06566b1e2d6834fdd81c0d8b2801b80b9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-on-premises-vmwarephysical-server-replication-issues"></a>Felsöka replikeringsproblem i lokal VMware/fysisk server
Du kan få ett visst felmeddelande när du skyddar din virtuella VMware-datorer eller fysiska servrar med hjälp av Azure Site Recovery. Den här artikeln beskrivs några av de vanligaste felmeddelandena som uppstod, tillsammans med felsökning för att lösa dem.


## <a name="initial-replication-is-stuck-at-0"></a>Den inledande replikeringen har fastnat på % 0
De flesta inledande replikeringsfel som vi stöta på support är på grund av problem med nätverksanslutningen mellan serverprocessen till källservern eller processen server till Azure.
I de flesta fall kan du själv felsökning av dessa problem genom att följa stegen nedan.

###<a name="check-the-following-on-source-machine"></a>Kontrollera följande på KÄLLDATORN
* Använd Telnet pinga Processervern med https-porten (standard 9443) som du ser nedan för att se om det finns några problem med nätverksanslutningen eller brandväggen port blockerande problem från källservern datorn kommandoraden.
     
    `telnet <PS IP address> <port>`
> [!NOTE]
    > Använder Telnet kan inte använda PING för att testa anslutningen.  Om Telnet inte är installerad, följer du stegen listan [här](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx)

Om det inte går att ansluta, tillåter inkommande port 9443 på Processervern och kontrollera om problemet fortfarande avslutas. Det har uppstått tillfällen där processervern har bakom DMZ som orsakade det här problemet.

* Kontrollera status för tjänsten `InMage Scout VX Agent – Sentinel/OutpostStart` om den inte körs och kontrollera om problemet kvarstår.   
 
###<a name="check-the-following-on-process-server"></a>Kontrollera följande på PROCESSERVERN

* **Kontrollera om processervern aktivt skicka data till Azure** 

Öppna Aktivitetshanteraren (tryck på Ctrl-Skift-Esc) från Processervern datorn. Gå till fliken prestanda och klicka på öppna Resursövervakaren länk. Från hanteraren för filserverresurser, gå till fliken nätverk. Kontrollera om cbengine.exe i ”processer med nätverksaktivitet” aktivt skickar stora mängder (i MB) data.

![Aktivera replikering](./media/site-recovery-protection-common-errors/cbengine.png)

Om inte följer du stegen nedan:

* **Kontrollera om processervern inte kan ansluta Azure Blob**: Välj och kontrollera cbengine.exe om du vill visa den 'TCP-anslutningar, för att se om det finns en anslutning från processervern till Azure Storage blob-URL.

![Aktivera replikering](./media/site-recovery-protection-common-errors/rmonitor.png)

Om det inte gå till Kontrollpanelen > tjänster, kontrollera att följande tjänster är igång:

     * cxprocessserver
     * InMage Scout VX Agent – Sentinel/Outpost
     * Microsoft Azure Recovery Services Agent
     * Microsoft Azure Site Recovery Service
     * tmansvc
     * 
(Re) Starta alla tjänster som inte körs och kontrollera om problemet kvarstår.

* **Kontrollera om processervern inte kan ansluta till Azure offentliga IP-adress via port 443**

Öppna senaste CBEngineCurr.errlog från `%programfiles%\Microsoft Azure Recovery Services Agent\Temp` och Sök efter: 443 och anslutningen misslyckades.

![Aktivera replikering](./media/site-recovery-protection-common-errors/logdetails1.png)

Om det uppstår problem kan från Processervern kommandorad Använd telnet pinga din Azure offentlig IP-adress (maskeras i ovanstående bild) hittades i CBEngineCurr.currLog via port 443.

      telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443
Kontrollera om problemet beror på brandvägg eller proxyserver som beskrivs i nästa steg om det inte går att ansluta.


* **Kontrollera om IP-adressbaserade brandväggen på processervern inte blockerar åtkomst**: Om du använder en IP-adressbaserade brandväggsregler på servern sedan hämta den fullständiga listan över Microsoft Azure Datacenter IP-intervall från [här](https://www.microsoft.com/download/details.aspx?id=41653) och lägga till dem i brandväggskonfigurationen för att säkerställa att de tillåter kommunikation till Azure (och HTTPS (443)-port).  Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för USA, västra (används för åtkomstkontroll och identitetshantering).

* **Kontrollera om URL-baserade processervern-brandväggen inte blockerar åtkomsten**: Om du använder en brandväggsregler för URL-Adressen baserat på servern, kan du kontrollera att följande webbadresser läggs till brandväggskonfigurationen. 
     
  `*.accesscontrol.windows.net:` Används för Access Control och identitetshantering

  `*.backup.windowsazure.com:` Används för överföring av replikeringsdata och dirigering

  `*.blob.core.windows.net:`Används för åtkomst till lagringskontot som lagrar replikerade data

  `*.hypervrecoverymanager.windowsazure.com:` Används för åtgärder för replikeringshantering och dirigering

  `time.nist.gov`och `time.windows.com`: används för att kontrollera tidssynkronisering mellan system och global tid.

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

![Aktivera replikering](./media/site-recovery-protection-common-errors/mab.png)

Öppna den och klicka på åtgärden > ändra egenskaper. Du bör se proxyadress som ska vara densamma som visas av registerinställningarna under fliken proxykonfiguration. Om du inte ändra det till samma adress.

![Aktivera replikering](./media/site-recovery-protection-common-errors/mabproxy.png)

* **Kontrollera om begränsning av bandbredd inte är begränsad på processervern**: öka bandbredden och kontrollera om problemet kvarstår.

##<a name="next-steps"></a>Nästa steg
Om du behöver mer hjälp för att sedan skicka frågan [ASR forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv community och en av våra tekniker kommer att kunna hjälpa dig.