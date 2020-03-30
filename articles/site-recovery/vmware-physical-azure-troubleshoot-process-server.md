---
title: Felsöka processservern för Azure Site Recovery
description: I den hÃ¤r artikeln beskrivs felsÃ¶kning av problem med processservern för Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 09/09/2019
ms.author: raynew
ms.openlocfilehash: 812cd0293f9627b7438e9870d8985e71dae1d147
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256866"
---
# <a name="troubleshoot-the-process-server"></a>Felsöka processservern

[Site Recovery](site-recovery-overview.md) process server används när du ställer in haveriberedskap till Azure för lokala virtuella datorer och fysiska servrar. I den hÃ¤r artikeln beskrivs felsÃ¶kning av problem med processservern, inklusive replikerings- och anslutningsproblem.

[Läs mer](vmware-physical-azure-config-process-server-overview.md) om processservern.

## <a name="before-you-start"></a>Innan du börjar

Innan du börjar felsöka:

1. Se till att du förstår hur du [övervakar processservrar](vmware-physical-azure-monitor-process-server.md).
2. Gå igenom de bästa metoderna nedan.
3. Se till att du följer [kapacitetsöverväganden](site-recovery-plan-capacity-vmware.md#capacity-considerations)och använder storleksguider för [konfigurationsservern](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) eller [fristående processservrar](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Metodtips för processserverdistribution

För optimal prestanda för processservrar har vi sammanfattat ett antal allmänna metodtips.

**Bästa praxis** | **Detaljer**
--- |---
**Användning** | Kontrollera att konfigurationsservern/den fristående processservern endast används för det avsedda syftet. Kör inget annat på maskinen.
**IP-adress** | Kontrollera att processservern har en statisk IPv4-adress och inte har NAT konfigurerat.
**Styra minnes-/CPU-användning** |Håll CPU- och minnesanvändning under 70 %.
**Se till att det finns ledigt utrymme** | Ledigt utrymme refererar till cachediskutrymmet på processservern. Replikeringsdata lagras i cacheminnet innan de överförs till Azure.<br/><br/> Håll ledigt utrymme över 25%. Om den går under 20 % begränsas replikeringen för replikerade datorer som är associerade med processservern.

## <a name="check-process-server-health"></a>Kontrollera processserverns hälsa

Det första steget i felsökning är att kontrollera hälsotillståndet och statusen för processservern. Det gör du genom att granska alla aviseringar, kontrollera att nödvändiga tjänster körs och kontrollera att det finns ett pulsslag från processservern. De här stegen sammanfattas i följande bild, följt av procedurer som hjälper dig att utföra stegen.

![Felsöka processserverhälsa](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Steg 1: Felsöka hälsovarningar för processserver

Processservern genererar ett antal hälsoaviseringar. Dessa aviseringar och rekommenderade åtgärder sammanfattas i följande tabell.

**Aviseringstyp** | **Fel** | **Felsöka**
--- | --- | --- 
![Felfri][green] | Inget  | Processservern är ansluten och felfri.
![Varning][yellow] | Angivna tjänster körs inte. | 1. Kontrollera att tjänsterna körs.<br/> 2. Om tjänsterna körs som förväntat följer du anvisningarna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).
![Varning][yellow]  | CPU-användning > 80% under de senaste 15 minuterna. | 1. Lägg inte till nya maskiner.<br/>2. Kontrollera att antalet virtuella datorer som använder processservern justerar till [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations)och överväg att konfigurera ytterligare en [processserver](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ instruktionerna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).
![Kritisk][red] |  CPU-användning > 95% för de senaste 15 minuterna. | 1. Lägg inte till nya maskiner.<br/>2. Kontrollera att antalet virtuella datorer som använder processservern justerar till [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations)och överväg att konfigurera ytterligare en [processserver](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ instruktionerna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).<br/> 4. Om problemet kvarstår kör du [distributionsplaneraren](https://aka.ms/asr-v2a-deployment-planner) för VMware/fysisk serverreplikering.
![Varning][yellow] | Minnesanvändning > 80% under de senaste 15 minuterna. |  1. Lägg inte till nya maskiner.<br/>2. Kontrollera att antalet virtuella datorer som använder processservern justerar till [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations)och överväg att konfigurera ytterligare en [processserver](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ anvisningarna i samband med varningen.<br/> 4. Om problemet kvarstår följer du anvisningarna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).
![Kritisk][red] | Minnesanvändning > 95% under de senaste 15 minuterna. | 1. Lägg inte till nya datorer och överväger att konfigurera ytterligare en [processserver](vmware-azure-set-up-process-server-scale.md).<br/> 2. Följ eventuella instruktioner i samband med varningen.<br/> 3. 4. Om problemet kvarstår följer du anvisningarna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).<br/> 4. Om problemet kvarstår kör du [distributionsplaneraren](https://aka.ms/asr-v2a-deployment-planner) för VMware/fysiska problem med serverreplikering.
![Varning][yellow] | Cachemappen ledigt utrymme < 30% under de senaste 15 minuterna. | 1. Lägg inte till nya datorer och överväg att konfigurera ytterligare en [processserver](vmware-azure-set-up-process-server-scale.md).<br/>2. Kontrollera att antalet virtuella datorer som använder processservern stämmer överens med [riktlinjerna](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Följ instruktionerna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).
![Kritisk][red] |  Fritt utrymme < 25% för de senaste 15 minuterna | 1. Följ instruktionerna i samband med varningen för det här problemet.<br/> 2. 3. Följ instruktionerna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).<br/> 3. Om problemet kvarstår kör du [distributionsplaneraren](https://aka.ms/asr-v2a-deployment-planner) för VMware/fysisk serverreplikering.
![Kritisk][red] | Inga pulsslag från processservern på 15 minuter eller mer. Tmansvs-tjänsten kommunicerar inte med konfigurationsservern. | 1) Kontrollera att processservern är igång.<br/> 2. Kontrollera att tmassvc körs på processservern.<br/> 3. Följ instruktionerna nedan för att [felsöka anslutnings- och replikeringsproblem](#check-connectivity-and-replication).


![Tabellnyckel](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Steg 2: Kontrollera processservertjänster

Tjänster som ska köras på processservern sammanfattas i följande tabell. Det finns små skillnader i tjänster, beroende på hur processservern distribueras. 

För alla tjänster utom Microsoft Azure Recovery Services Agent (obengine) kontrollerar du att StartType är inställt på **Automatisk** eller **Automatisk (Fördröjd start).**
 
**Distribution** | **Köra tjänster**
--- | ---
**Processserver på konfigurationsservern** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Loggöverföringstjänst (LogUpload); Programtjänst för Inmage Scout; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Utpost (svagents); tmansvc; World Wide Web Publishing Service (W3SVC); MySQL; Microsoft Azure Site Recovery Service (dra)
**Bearbeta server som körs som en fristående server** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Loggöverföringstjänst (LogUpload); Programtjänst för Inmage Scout; Microsoft Azure Recovery Services Agent (obengine); InMage Scout VX Agent-Sentinel/Utpost (svagents); tmansvc.
**Bearbeta server som distribueras i Azure för återställning efter fel** | ProcessServer; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Tjänsten Logga uppladdning (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Steg 3: Kontrollera processserverns pulsslag

Om det inte finns några pulsslag från processservern (felkod 806) gör du följande:

1. Kontrollera att den virtuella datorn för processservern är igång.
2. Kontrollera dessa loggar för fel.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Kontrollera anslutning och replikering

 Inledande och pågående replikeringsfel orsakas ofta av anslutningsproblem mellan källdatorer och processservern, eller mellan processservern och Azure. De här stegen sammanfattas i följande bild, följt av procedurer som hjälper dig att utföra stegen.

![Felsöka anslutning och replikering](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Steg 4: Verifiera tidssynkronisering på källmaskinen

Kontrollera att systemdatum/tid för den replikerade datorn är synkroniserad. [Läs mer](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Steg 5: Kontrollera antivirusprogram på källmaskinen

Kontrollera att inget antivirusprogram på den replikerade datorn blockerar Site Recovery. Om du behöver utesluta webbplatsåterställning från antivirusprogram läser du [den här artikeln](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Steg 6: Kontrollera anslutningen från källmaskinen


1. Installera [Telnet-klienten](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) på källmaskinen om du behöver. Använd inte Ping.
2. Från källdatorn pingar du processservern på HTTPS-porten med Telnet. Som standard är 9443 HTTPS-porten för replikeringstrafik.

    `telnet <process server IP address> <port>`

3. Kontrollera om anslutningen lyckas.


**Anslutning** | **Detaljer** | **Åtgärd**
--- | --- | ---
**Framgångsrika** | Telnet visar en tom skärm och processservern kan nås. | Inga ytterligare åtgärder krävs.
**Misslyckade** | Du kan inte ansluta | Kontrollera att inkommande port 9443 är tillåten på processservern. Om du till exempel har ett perimeternätverk eller ett skärmat undernät. Kontrollera anslutningen igen.
**Delvis lyckad** | Du kan ansluta, men källdatorn rapporterar att processservern inte kan nås. | Fortsätt med nästa felsökningsprocedur.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Steg 7: Felsöka en processserver som inte kan nås

Om processservern inte kan nås från källdatorn visas fel 78186. Om det inte åtgärdas, kommer det här problemet att leda till att både appkonsekventa och kraschkonsekventa återställningspunkter inte genereras som förväntat.

Felsöka genom att kontrollera om källdatorn kan nå processserverns IP-adress och köra cxpsclient-verktyget på källdatorn för att kontrollera slutpunkt till slutpunkt.Troubleshoot by check whether the source machine can reach the IP address of the process server, and run the cxpsclient tool on the source machine, to check the end-to-end connection.


### <a name="check-the-ip-connection-on-the-process-server"></a>Kontrollera IP-anslutningen på processservern

Om telnet lyckas men källdatorn rapporterar att processservern inte kan nås kontrollerar du om du kan nå processserverns IP-adress.

1. I en webbläsare försöker du nå IP-adressen https://<PS_IP>:<PS_Data_Port>/.
2. Om den här kontrollen visar ett HTTPS-certifikatfel är det normalt. Om du ignorerar felet bör du se en 400 - Felaktig begäran. Det innebär att servern inte kan hantera webbläsarbegäran och att standard-HTTPS-anslutningen är bra.
3. Om den här kontrollen inte fungerar bör du tänka på felmeddelandet i webbläsaren. Ett 407-fel indikerar till exempel ett problem med proxyautentisering.

### <a name="check-the-connection-with-cxpsclient"></a>Kontrollera anslutningen med cxpsclient

Dessutom kan du köra cxpsclient-verktyget för att kontrollera slutpunkt till slutpunkt-anslutning.

1. Kör verktyget på följande sätt:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Kontrollera de genererade loggarna i de här mapparna på processservern:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Kontrollera käll-VM-loggar för uppladdningsfel (fel 78028)

Problem med dataöverföringar som blockerats från källdatorer till processtjänsten kan resultera i att både kraschkonsekventa och appkonsekventa återställningspunkter inte genereras. 

1. Om du vill felsöka nätverksuppladdningsfel kan du söka efter fel i den här loggen:

    C:\Program-filer (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

2. Använd resten av procedurerna i den här artikeln kan hjälpa dig att lösa problem med dataöverföring.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Steg 8: Kontrollera om processservern driver data

Kontrollera om processservern aktivt driver data till Azure.

  1. Öppna Aktivitetshanteraren på processservern (tryck på Ctrl+Skift+Esc).
  2. Välj fliken **Prestanda** > **Övervakare för öppen resurs**.
  3. Välj fliken Nätverk på sidan **Resursövervakare.** **Network** Kontrollera om cbengine.exe aktivt skickar en stor mängd data under **Processer med nätverksaktivitet.**

       ![Volymer under processer med nätverksaktivitet](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Om cbengine.exe inte skickar en stor mängd data utför du stegen i följande avsnitt.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Steg 9: Kontrollera processserveranslutningen till Azure blob storage

1. Välj **cbengine.exe**i Resursövervakaren .
2. Under **TCP-anslutningar**kontrollerar du om det finns anslutning från processservern till Azure-lagring.

  ![Anslutning mellan cbengine.exe och Azure Blob-lagrings-URL:en](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Kontrollera tjänster

Om det inte finns någon anslutning från processservern till Azure blob storage URL kontrollerar du att tjänsterna körs.

1. Välj **Tjänster**på Kontrollpanelen .
2. Kontrollera att följande tjänster körs:

    - cxprocessserver (cxprocessserver)
    - Inmage Scout VX Agent - Sentinel / Utpost
    - Agent för Microsoft Azure-återställningstjänster
    - Microsoft Azure-tjänsten för återställning av webbplatser
    - tmansvc (tmansvc)

3. Starta eller starta om en tjänst som inte körs.
4. Kontrollera att processservern är ansluten och kan nås. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Steg 10: kontrollera processserveranslutningen till Azures offentliga IP-adress

1. Öppna den senaste FILEN CBEngineCurr.errlog i **%programfiler%\Microsoft Azure Recovery Services Agent\Temp**på processservern.
2. Sök efter **443**i filen eller efter att **stränganslutningsförsöket misslyckades**.

  ![Felloggar i mappen Temp](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Om du ser problem finns din offentliga Azure-IP-adress i filen CBEngineCurr.currLog med port 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. På kommandoraden på processservern använder du Telnet för att pinga din offentliga Azure-IP-adress.
6. Om du inte kan ansluta följer du nästa procedur.

## <a name="step-11-check-process-server-firewall-settings"></a>Steg 11: Kontrollera inställningarna för processserverbrandväggen. 

Kontrollera om den IP-adressbaserade brandväggen på processservern blockerar åtkomsten.

1. För IP-adressbaserade brandväggsregler:

    a) Hämta hela listan över [IP-intervall för Microsoft Azure-datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

    b) Lägg till IP-adressintervallen i brandväggskonfigurationen för att säkerställa att brandväggen tillåter kommunikation till Azure (och till standard-HTTPS-porten, 443).

    c) Tillåt IP-adressintervall för Azure-regionen för din prenumeration och för regionen Azure West US (används för åtkomstkontroll och identitetshantering).

2. För URL-baserade brandväggar lägger du till webbadresserna i följande tabell i brandväggskonfigurationen.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Steg 12: Verifiera proxyinställningar för processserver 

1. Om du använder en proxyserver kontrollerar du att proxyservernamnet matchas av DNS-servern. Kontrollera värdet som du angav när du konfigurerade konfigurationsservern i registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Site Recovery\ProxySettings**.
2. Kontrollera att samma inställningar används av Azure Site Recovery agent för att skicka data.

    a) Sök efter **Microsoft Azure Backup**.

    b) Öppna **Microsoft Azure Backup**och välj Egenskaper för **åtgärdsändring** > **Change Properties**.

    c) På fliken **Proxykonfiguration** ska proxyadressen vara samma som proxyadressen som visas i registerinställningarna. Om inte, ändra den till samma adress.

## <a name="step-13-check-bandwidth"></a>Steg 13: Kontrollera bandbredd

Öka bandbredden mellan processservern och Azure och kontrollera sedan om problemet kvarstår.


## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du ställa din fråga i [Azure Site Recovery-forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png
