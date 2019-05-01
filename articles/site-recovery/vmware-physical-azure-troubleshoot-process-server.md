---
title: Felsöka Azure Site Recovery processervern
description: Den här artikeln beskriver hur du felsöker problem med Azure Site Recovery processervern
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 04/29/2019
ms.author: rayne
ms.openlocfilehash: 926e5b685369f8660daf6221f818734f6f12d2b5
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928414"
---
# <a name="troubleshoot-the-process-server"></a>Felsöka processervern

Den [Site Recovery](site-recovery-overview.md) processervern som används när du konfigurerar haveriberedskap till Azure för lokala virtuella VMware-datorer och fysiska servrar. Den här artikeln beskriver hur du felsöker problem med processervern, inklusive problem med replikering och anslutning.

[Läs mer](vmware-physical-azure-config-process-server-overview.md) om processervern.

## <a name="before-you-start"></a>Innan du börjar

Innan du startar Felsökning:

1. Kontrollera att du förstår hur du [övervaka processervrar](vmware-physical-azure-monitor-process-server.md).
2. Granska de bästa metoderna nedan.
3. Se till att du följer [kapacitetsöverväganden](site-recovery-plan-capacity-vmware.md#capacity-considerations), och använda storlek vägledning för den [konfigurationsservern](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server) eller [fristående processervrar](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server).

## <a name="best-practices-for-process-server-deployment"></a>Metodtips för distribution av processerver

För optimala prestanda av processervrar, vi har en översikt över ett antal allmänna metodtips.

**Bästa praxis** | **Detaljer**
--- |---
**Användning** | Kontrollera att processervern configuration server/fristående används bara för det avsedda syftet. Kör inte något annat på datorn.
**IP-adress** | Se till att processervern har en statisk IPv4-adress och har inte konfigurerats NAT.
**Kontrollen minne/CPU-användning** |Behåll CPU och minnesanvändning under 70%.
**Se till att ledigt utrymme** | Ledigt utrymme refererar till cache diskutrymme på processervern. Replikeringsdata lagras i cachen innan det överförs till Azure.<br/><br/> Behåll ledigt utrymme över 25%. Om Kölängden är under 20%, begränsas replikering för replikerade datorer som är kopplade till processervern.

## <a name="check-process-server-health"></a>Kontrollera hälsa för processen server

Felsökning av det första steget är att kontrollera hälsotillstånd och status för processervern. Gör detta genom att granska alla aviseringar, kontrollera att de nödvändiga tjänsterna körs och kontrollera att det finns inget pulsslag från processervern. De här stegen sammanfattas i följande bild, följt av förfaranden för att utföra stegen.

![Felsöka server hälsotillstånd](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-process-server-health.png)

## <a name="step-1-troubleshoot-process-server-health-alerts"></a>Steg 1: Felsöka process server health-aviseringar

Processervern genererar ett antal health-aviseringar. Dessa aviseringar och rekommenderade åtgärder sammanfattas i tabellen nedan.

**Typ av avisering** | **Fel** | **Felsöka**
--- | --- | --- 
![Felfri][green] | Ingen  | Processervern är ansluten och felfri.
![Varning][yellow] | Tjänster som anges inte körs. | 1. Kontrollera att tjänsterna är igång.<br/> 2. Om tjänsterna körs som förväntat, följer du anvisningarna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).
![Varning][yellow]  | CPU användning > 80% för de senaste 15 minuterna. | 1. Lägg inte till nya datorer.<br/>2. Kontrollera att antalet virtuella datorer som använder processervern stämmer överens med [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations), och du bör konfigurera en [kompletterande processervern](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ anvisningarna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).
![Kritisk][red] |  Processorförbrukning > 95 i % för de senaste 15 minuterna. | 1. Lägg inte till nya datorer.<br/>2. Kontrollera att antalet virtuella datorer som använder processervern stämmer överens med [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations), och du bör konfigurera en [kompletterande processervern](vmware-azure-set-up-process-server-scale.md).<br/>3. Följ anvisningarna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).<br/> 4. Om problemet kvarstår kör den [Distributionshanteraren](http://aka.ms/asr-v2a-deployment-planner) för VMware/fysisk serverreplikering.
![Varning][yellow] | Minne användning > 80% för de senaste 15 minuterna. |  1. Lägg inte till nya datorer.<br/>2. Kontrollera att antalet virtuella datorer som använder processervern stämmer överens med [definierade gränser](site-recovery-plan-capacity-vmware.md#capacity-considerations), och du bör konfigurera en [kompletterande processervern](vmware-azure-set-up-process-server-scale.md).<br/>3. Följer du instruktionerna som är associerade med varning.<br/> 4. Om problemet kvarstår, följ instruktionerna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).
![Kritisk][red] | Minne användning > 95% under de senaste 15 minuterna. | 1. Lägg inte till nya datorer och installerar en [kompletterande processervern](vmware-azure-set-up-process-server-scale.md).<br/> 2. Följer du instruktionerna som är associerade med varning.<br/> 3. 4. Om problemet kvarstår, följ instruktionerna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).<br/> 4. Om problemet kvarstår kör den [Distributionshanteraren](http://aka.ms/asr-v2a-deployment-planner) för VMware/fysisk server replikeringsproblem.
![Varning][yellow] | Cachemapp ledigt utrymme < 30% under de senaste 15 minuterna. | 1. Inte lägga till nya datorer och du bör konfigurera en [kompletterande processervern](vmware-azure-set-up-process-server-scale.md).<br/>2. Kontrollera att antalet virtuella datorer som använder processervern stämmer överens med [riktlinjer](site-recovery-plan-capacity-vmware.md#capacity-considerations).<br/> 3. Följ anvisningarna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).
![Kritisk][red] |  Ledigt utrymme < 25% för de senaste 15 minuterna | 1. Följ anvisningarna som är associerade med en varning för det här problemet.<br/> 2. 3. Följ anvisningarna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).<br/> 3. Om problemet kvarstår kör den [Distributionshanteraren](http://aka.ms/asr-v2a-deployment-planner) för VMware/fysisk serverreplikering.
![Kritisk][red] | Inget pulsslag från processervern under 15 minuter eller mer. Tjänsten tmansvs kommunicerar inte med konfigurationsservern. | (1) kontrollera att processervern är igång.<br/> 2. Kontrollera att tmassvc körs på processervern.<br/> 3. Följ anvisningarna nedan för att [felsöka problem med anslutningen och replikering](#check-connectivity-and-replication).


![tabellnyckel](./media/vmware-physical-azure-troubleshoot-process-server/table-key.png)


## <a name="step-2-check-process-server-services"></a>Steg 2: Kontrollera processervertjänsterna

Tjänster som ska köras på processervern sammanfattas i tabellen nedan. Det finns vissa skillnader i tjänster, beroende på hur processervern ska distribueras. 

Kontrollera att StartType är inställt på för alla tjänster utom Microsoft Azure Recovery Services Agent (obengine) **automatisk** eller **automatiskt (förskjuten Start)**.
 
**Distribution** | **Tjänster som körs**
--- | ---
**Processervern på konfigurationsservern** | Process; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Ladda upp Loggtjänsten (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services-agenten (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc; World Wide Web Publishing Service (W3SVC); MySQL; Microsoft Azure Site Recovery-tjänsten (dra)
**Processerver körs som en fristående server** | Process; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Ladda upp Loggtjänsten (LogUpload); InMage Scout Application Service; Microsoft Azure Recovery Services-agenten (obengine); InMage Scout VX Agent-Sentinel/Outpost (svagents); tmansvc.
**Processervern distribueras i Azure för återställning efter fel** | Process; ProcessServerMonitor; cxprocessserver; InMage PushInstall; Ladda upp Loggtjänsten (LogUpload)


## <a name="step-3-check-the-process-server-heartbeat"></a>Steg 3: Kontrollera process serverpulsslag

Om det finns inget pulsslag från processervern (felkod: 806), gör du följande:

1. Kontrollera att processervern VM är igång.
2. De här loggarna efter fel.

    C:\ProgramData\ASR\home\svsystems\eventmanager *.log  C\ProgramData\ASR\home\svsystems\monitor_protection*.log

## <a name="check-connectivity-and-replication"></a>Kontrollera anslutningen och replikering

 Initiala lösenord och fortlöpande replikeringsfel orsakas ofta av problem med nätverksanslutningen mellan källdatorer och processervern eller mellan processervern och Azure. De här stegen sammanfattas i följande bild, följt av förfaranden för att utföra stegen.

![Felsöka anslutning och replikering](./media/vmware-physical-azure-troubleshoot-process-server/troubleshoot-connectivity-replication.png)


## <a name="step-4-verify-time-sync-on-source-machine"></a>Steg 4: Kontrollera tidssynkronisering på källdatorn

Kontrollera att det systemdatumet och systemtiden för den replikerade datorn är synkroniserade. [Läs mer](https://docs.microsoft.com/windows-server/networking/windows-time-service/accurate-time)

## <a name="step-5-check-anti-virus-software-on-source-machine"></a>Steg 5: Kontrollera ett antivirusprogram på källdatorn

Kontrollera att inga ett antivirusprogram på den replikerade datorn blockerar Site Recovery. Om du vill exkludera Site Recovery från antivirusprogram kan du läsa [i den här artikeln](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program).

## <a name="step-6-check-connectivity-from-source-machine"></a>Steg 6: Kontrollera anslutningen mellan källdatorn


1. Installera den [Telnet-klienten](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx) på källdatorn om du behöver. Använd inte Ping.
2. Pinga processervern på HTTPS-porten med Telnet från källdatorn. Som standard är 9443 HTTPS-port för replikeringstrafik.

    `telnet <process server IP address> <port>`

3. Kontrollera om anslutningen är klar.


**Anslutning** | **Detaljer** | **Åtgärd**
--- | --- | ---
**Successful** | Telnet visar en tom skärm och processervern kan nås. | Ingen ytterligare åtgärd krävs.
**Misslyckade** | Du kan inte ansluta | Se till att tillåts inkommande port 9443 på processervern. Exempel: Om du har ett perimeternätverk eller ett bevakat undernät. Kontrollera anslutningen igen.
**Delvis genomförd** | Du kan ansluta men källdatorn rapporterar att processervern inte kan nås. | Fortsätt med nästa procedur för felsökning.

## <a name="step-7-troubleshoot-an-unreachable-process-server"></a>Steg 7: Felsöka en kan inte nås processerver

Om processervern inte kan nås från källdatorn, visas fel 78186. Om inte åtgärdas problemet leder till båda programkonsekvent och kraschkonsekventa återställningspunkter skapas inte som förväntat.

Felsöka genom att kontrollera om källdatorn kan nå IP-adressen för processervern och köra verktyget cxpsclient på källdatorn, för att kontrollera anslutningen för slutpunkt till slutpunkt.


### <a name="check-the-ip-connection-on-the-process-server"></a>Kontrollera IP-anslutning på processervern

Om telnet är lyckades men källdatorn rapporterar att processervern inte kan nås, kontrollerar du om du kan nå IP-adressen för processervern.

1. Försök att nå IP-adress https://<PS_IP>:<PS_Data_Port>/ i en webbläsare.
2. Om den här kontrollen visar ett fel för HTTPS-certifikat, är det normala. Om du ignorerar felet bör du se en 400 - Felaktig begäran. Det innebär att servern inte kan hantera begäran för webbläsare och att standard HTTPS-anslutningen är bra.
3. Om den här kontrollen inte fungerar, Observera du webbläsaren ett felmeddelande. Till exempel en 407 som anger ett problem med proxyautentisering.

### <a name="check-the-connection-with-cxpsclient"></a>Kontrollera anslutningen med cxpsclient

Dessutom kan du köra verktyget cxpsclient för att kontrollera anslutningen för slutpunkt till slutpunkt.

1. Kör verktyget på följande sätt:

    ```
    <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>
    ```

2. Kontrollera de genererade loggarna i dessa mappar på processervern:

    C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err  C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer



### <a name="check-source-vm-logs-for-upload-failures-error-78028"></a>Kontrollera att källan VM för Prestandaloggar fel (fel 78028)

Problem med upp blockeras från källdatorer till process service kan resultera i båda kraschkonsekventa och appkonsekventa återställningspunkter skapas. 

1. Om du vill felsöka fel vid uppladdning av nätverk, kan du leta efter fel i den här loggfilen:

    C:\Program filer (x86) \Microsoft Azure Site Recovery\agent\svagents*.log 

2. Använd resten av procedurerna i den här artikeln kan hjälpa dig att lösa problem med överföringen.



## <a name="step-8-check-whether-the-process-server-is-pushing-data"></a>Steg 8: Kontrollera om processervern är att skicka data

Kontrollera om processervern aktivt skicka data till Azure.

  1. Öppna Aktivitetshanteraren (tryck på Ctrl + Skift + Esc) på processervern.
  2. Välj den **prestanda** fliken > **öppna Resursövervakaren**.
  3. I **Resursövervakaren** väljer den **nätverk** fliken. Under **processer med nätverksaktivitet**, kontrollera om cbengine.exe aktivt skickar en stor vNotolume av data.

       ![Volymer under processer med nätverksaktivitet](./media/vmware-physical-azure-troubleshoot-process-server/cbengine.png)

  Om cbengine.exe inte skickar stora mängder data kan du slutföra stegen i följande avsnitt.

## <a name="step-9-check-the-process-server-connection-to-azure-blob-storage"></a>Steg 9: Kontrollera process server-anslutning till Azure blob storage

1. Resursövervakaren, Välj **cbengine.exe**.
2. Under **TCP-anslutningar**, kontrollera om det finns en anslutning från processervern till Azure storage.

  ![Anslutningen mellan cbengine.exe och URL: en för Azure Blob storage](./media/vmware-physical-azure-troubleshoot-process-server/rmonitor.png)

### <a name="check-services"></a>Kontrollera tjänster

Om det finns ingen nätverksanslutning från processervern till Azure blob storage-Webbadressen, kontrollerar du att tjänsterna är igång.

1. I Kontrollpanelen, väljer **Services**.
2. Kontrollera att följande tjänster körs:

    - cxprocessserver
    - InMage Scout VX Agent – Sentinel/Outpost
    - Microsoft Azure Recovery Services-agent
    - Microsoft Azure Site Recovery-tjänsten
    - tmansvc

3. Starta eller starta om alla tjänster som inte körs.
4. Kontrollera att processervern är ansluten och kan nås. 

## <a name="step-10-check-the-process-server-connection-to-azure-public-ip-address"></a>Steg 10: Kontrollera process server-anslutning till Azure offentlig IP-adress

1. På processervern, i **%programfiles%\Microsoft Azure Recovery Services Agent\Temp**, öppna den senaste CBEngineCurr.errlog-filen.
2. I filen, söka efter **443**, eller efter strängen **anslutningsförsöket misslyckades**.

  ![Felloggar i Temp-mappen](./media/vmware-physical-azure-troubleshoot-process-server/logdetails1.png)

3. Om du ser problem finns dina Azure offentlig IP-adress i filen CBEngineCurr.currLog via port 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

5. Använda Telnet pinga din Azure offentlig IP-adress på kommandoraden på processervern.
6. Om du inte kan ansluta, följer du i nästa procedur.

## <a name="step-11-check-process-server-firewall-settings"></a>Steg 11: Kontrollera inställningarna för process server-brandväggen. 

Kontrollera om IP-adressbaserade brandväggsregler på processervern blockerar åtkomst.

1. För IP-adressbaserade brandväggsregler:

    (a) ladda ned den fullständiga listan med [Microsoft Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

    (b) Lägg till IP-adressintervall i brandväggskonfigurationen så att brandväggen tillåter kommunikation till Azure (och HTTPS-standardporten, 443).

    Tillåt c) IP-adressintervall för Azure-regionen för din prenumeration och för Azure regionen västra USA (används för access control och Identitetshantering).

2. URL-baserade brandväggar, lägger du till URL: er i följande tabell för att brandväggskonfigurationen.

    [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  


## <a name="step-12-verify-process-server-proxy-settings"></a>Steg 12: Kontrollera process server proxy-inställningar 

1. Om du använder en proxyserver, se till att namnet på proxyservern har åtgärdats av DNS-servern. Kontrollera värdet som du angav när du ställer in konfigurationsservern på registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure plats Recovery\ProxySettings**.
2. Se till att samma inställningar som används av Azure Site Recovery-agenten för att skicka data.

    (a) letar **Microsoft Azure Backup**.

    (b) öppna **Microsoft Azure Backup**, och välj **åtgärd** > **ändra egenskaper för**.

    c) på den **proxykonfiguration** fliken Proxyadressen ska vara densamma som den Proxyadressen som visas i registerinställningarna. Annars kan du ändra den till samma adress.

## <a name="step-13-check-bandwidth"></a>Steg 13: Kontrollera bandbredd

Öka bandbredden mellan processervern och Azure och kontrollera om problemet kvarstår.


## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du ställa din fråga i den [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). 

[green]: ./media/vmware-physical-azure-troubleshoot-process-server/green.png
[yellow]: ./media/vmware-physical-azure-troubleshoot-process-server/yellow.png
[red]: ./media/vmware-physical-azure-troubleshoot-process-server/red.png