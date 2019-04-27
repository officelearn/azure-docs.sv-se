---
title: Felsöka problem med replikering för akutåterställning av virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery | Microsoft Docs
description: Den här artikeln innehåller information om att felsöka vanliga replikeringsproblem under haveriberedskap för virtuella VMware-datorer och fysiska servrar till Azure med hjälp av Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/14/2019
ms.author: mayg
ms.openlocfilehash: 1aaf13f01c7e7197001f3099fabd4b8be8545f0d
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60565080"
---
# <a name="troubleshoot-replication-issues-for-vmware-vms-and-physical-servers"></a>Felsöka problem med replikering för virtuella VMware-datorer och fysiska servrar

Du kan se ett specifikt felmeddelande när du skyddar dina virtuella VMware-datorer eller fysiska servrar med hjälp av Azure Site Recovery. Den här artikeln beskriver några vanliga problem som kan uppstå när du replikerar lokala virtuella VMware-datorer och fysiska servrar till Azure med hjälp av [Site Recovery](site-recovery-overview.md).

## <a name="monitor-process-server-health-to-avoid-replication-issues"></a>Övervaka hälsa för Process Server att undvika problem med replikering

Vi rekommenderar att övervaka hälsotillståndet Process Server (PS) på portalen för att säkerställa att replikeringen fortlöper för din associerade källdatorer. Gå till hantera i valvet > Site Recovery-infrastruktur > Konfigurationsservrar. Klicka på Processervern under associerade servrar på Configuration Server-bladet. Process Server-bladet som öppnas med dess hälsostatistik. Du kan spåra processoranvändning, minnesanvändning, status för PS-tjänster som krävs för replikering, certifikat upphör att gälla och tillgängligt ledigt utrymme. Status för all statistik vara grön. 

**Vi rekommenderar att ha minne och CPU-användning under 70% och ledigt utrymme som är ovan 25%**. Ledigt utrymme refererar till cache diskutrymme i Processervern som används för att lagra data för replikering från källdatorer innan du laddar upp till Azure. Om den minskar till mindre än 20% kommer replikeringen att begränsas för alla associerade källdatorer. Följ den [kapacitet vägledning](./site-recovery-plan-capacity-vmware.md#capacity-considerations) att förstå konfigurationen som krävs för att replikera källdatorer.

Se till att följande tjänster körs på den PS-datorn. Starta eller starta om alla tjänster som inte körs.

**Inbyggd Processerver**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Ladda upp Loggtjänsten (LogUpload)
* InMage Scout Application Service
* Microsoft Azure Recovery Services-agenten (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc
* World Wide Web Publishing Service (W3SVC)
* MySQL
* Microsoft Azure Site Recovery-tjänsten (dra)

**Skalbar Processerver**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Ladda upp Loggtjänsten (LogUpload)
* InMage Scout Application Service
* Microsoft Azure Recovery Services-agenten (obengine)
* InMage Scout VX Agent - Sentinel/Outpost (svagents)
* tmansvc

**Processerver i Azure för återställning efter fel**

* ProcessServer
* ProcessServerMonitor
* cxprocessserver
* InMage PushInstall
* Ladda upp Loggtjänsten (LogUpload)

Se till att StartType för alla tjänster har angetts till **automatisk eller automatiskt (förskjuten Start)**. Microsoft Azure Recovery Services-agenten (obengine) tjänsten behöver inte ha sin StartType som ovan.

## <a name="replication-issues"></a>Replikeringsproblem

Initiala lösenord och fortlöpande replikeringsfel orsakas ofta av problem med nätverksanslutningen mellan käll- och processervern eller mellan processervern och Azure. Du kan felsöka de här problemen genom att följa stegen i följande avsnitt i de flesta fall.

>[!Note]
>Se till att:
>1. Systemet är datum-tid för det skyddade objektet synkroniserade.
>2. Inga ett antivirusprogram hindrar Azure Site Recovery. Lär dig [mer](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) på mappundantag som krävs för Azure Site Recovery.

### <a name="check-the-source-machine-for-connectivity-issues"></a>Kontrollera källdatorn för problem med nätverksanslutningen

I följande lista visas hur du kan kontrollera källdatorn.

*  Använda Telnet pinga processervern via HTTPS-porten genom att köra följande kommando på kommandoraden på källservern. HTTPS-Port 9443 är standardinställningen som används av Processervern för att skicka och ta emot replikeringstrafik. Du kan ändra den här porten vid tidpunkten för registrering. Följande kommando kontrollerar för problem med nätverksanslutningen och problem som blockerar brandväggsporten.


   `telnet <process server IP address> <port>`


   > [!NOTE]
   > Använda Telnet för att testa anslutningen. Använd inte `ping`. Om Telnet inte är installerad, slutför du stegen i [installera Telnet-klienten](https://technet.microsoft.com/library/cc771275(v=WS.10).aspx).

   Om telnet kan ansluta till PS-port, skulle ett tomt fönster visas.

   Om du inte kan ansluta till processervern, kan du inkommande port 9443 på processervern. Exempel: du kan behöva tillåta inkommande port 9443 på processervern om nätverket har ett perimeternätverk eller avskärmat undernät. Kontrollera sedan om problemet kvarstår.

*  Om telnet lyckas och ännu källdatorn rapporterar Processervern inte kan nås, öppna webbläsaren på källdatorn och kontrollera om adressen https://<PS_IP>:<PS_Data_Port>/ kan nås.

    HTTPS-certifikatfel förväntas på stöter på den här adressen. Ignorera certifikatfelet och du fortsätter bör hamnar i 400 – Felaktig begäran, vilket innebär att servern inte kan hantera begäran i webbläsarens och att standard HTTPS-anslutning till servern fungerar bra och bra.

    Om det inte lyckas, ger information om felmeddelandet webbläsare vägledning. För t.ex. om proxy-autentisering är felaktigt proxyservern returnerar 407-Proxy-autentisering krävs tillsammans med nödvändiga åtgärder i felmeddelandet. 

*  Kontrollera följande loggar på den Virtuella källdatorn för fel som rör nätverket uppladdningsfel:

       C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\svagents*.log 

### <a name="check-the-process-server-for-connectivity-issues"></a>Kontrollera processervern för problem med nätverksanslutningen

I följande lista visas hur du kan kontrollera processervern:

> [!NOTE]
> Processervern måste ha en statisk IPv4-adress och bör inte ha NAT IP konfigurerat på den.

* **Kontrollera anslutningen mellan källdatorer och Processervern**
* Om du att telnet från källdatorn och ännu Processervern kan inte nås från källa, kan du kontrollera anslutningen för slutpunkt till slutpunkt med cxprocessserver från den Virtuella källdatorn genom att köra cxpsclient verktyget på den Virtuella källdatorn:

      <install folder>\cxpsclient.exe -i <PS_IP> -l <PS_Data_Port> -y <timeout_in_secs:recommended 300>

   Kontrollera de genererade loggarna på PS i följande kataloger för information om motsvarande fel:

      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.err
      and
      C:\ProgramData\ASR\home\svsystems\transport\log\cxps.xfer
* Kontrollera följande loggar på Processervern om det finns inget pulsslag från PS. Detta har identifierats av **felkoden 806** på portalen.

      C:\ProgramData\ASR\home\svsystems\eventmanager*.log
      and
      C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

* **Kontrollera om processervern aktivt skicka data till Azure**.

  1. Öppna Aktivitetshanteraren (tryck på Ctrl + Skift + Esc) på processervern.
  2. Välj den **prestanda** fliken och välj sedan den **öppna Resursövervakaren** länk. 
  3. På den **Resursövervakaren** väljer den **nätverk** fliken. Under **processer med nätverksaktivitet**, kontrollera om **cbengine.exe** aktivt skickar stora mängder data.

       ![Skärmbild som visar volymerna under processer med nätverksaktivitet](./media/vmware-azure-troubleshoot-replication/cbengine.png)

  Om cbengine.exe inte skickar stora mängder data kan du slutföra stegen i följande avsnitt.

* **Kontrollera om processervern kan ansluta till Azure Blob storage**.

  Välj **cbengine.exe**. Under **TCP-anslutningar**, kontrollera om det finns en anslutning från processervern till Azure-bloggen lagrings-URL.

  ![Skärmbild som visar anslutning mellan cbengine.exe och URL: en för Azure Blob storage](./media/vmware-azure-troubleshoot-replication/rmonitor.png)

  Om det finns ingen nätverksanslutning från processervern till Azure-bloggen storage URL på Kontrollpanelen, välja **Services**. Kontrollera om följande tjänster körs:

  *  cxprocessserver
  *  InMage Scout VX Agent – Sentinel/Outpost
  *  Microsoft Azure Recovery Services-agent
  *  Microsoft Azure Site Recovery-tjänsten
  *  tmansvc

  Starta eller starta om alla tjänster som inte körs. Kontrollera om problemet kvarstår.

* **Kontrollera om processervern kan ansluta till Azure offentlig IP-adress via port 443**.

  Öppna filen senaste CBEngineCurr.errlog i %programfiles%\Microsoft Azure Recovery Services Agent\Temp. I filen, söka efter **443** eller efter strängen **anslutningsförsöket misslyckades**.

  ![Skärmbild som visar felet loggas i Temp-mappen](./media/vmware-azure-troubleshoot-replication/logdetails1.png)

  Om problem visas på kommandoraden på processervern, kan du använda Telnet för att pinga din Azure offentlig IP-adress (IP-adressen maskeras i föregående bild). Du kan hitta din Azure offentlig IP-adress i filen CBEngineCurr.currLog via port 443:

  `telnet <your Azure Public IP address as seen in CBEngineCurr.errlog>  443`

  Om du inte kan ansluta, kontrollera om Åtkomstproblemet på grund av brandväggen eller proxyservern inställningar enligt beskrivningen i nästa steg.

* **Kontrollera om IP-adressbaserade brandväggsregler på processervern blockerar åtkomst**.

  Om du använder IP-adressbaserade brandväggsregler på servern kan hämta den fullständiga listan med [Microsoft Azure datacenter IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653). Lägg till IP-adressintervall i brandväggskonfigurationen av för att se till att brandväggen tillåter kommunikation till Azure (och HTTPS-standardporten, 443). Tillåt IP-adressintervall för Azure-regionen för din prenumeration och Azure regionen västra USA (används för access control och Identitetshantering).

* **Kontrollera om en URL-baserad brandvägg på processervern blockerar åtkomst**.

  Om du använder en URL-baserad brandväggsregel på servern, lägger du till URL: er i följande tabell för att brandväggskonfigurationen:

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

*  **Kontrollera om proxyinställningar på processervern blockerar åtkomst**.

   Om du använder en proxyserver, se till att namnet på proxyservern har åtgärdats av DNS-servern. Du kan kontrollera det värde som du angav när du ställer in konfigurationsservern, välja registernyckeln **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure plats Recovery\ProxySettings**.

   Kontrollera sedan att samma inställningar som används av Azure Site Recovery-agenten för att skicka data: 
      
   1. Sök efter **Microsoft Azure Backup**. 
   2. Öppna **Microsoft Azure Backup**, och välj sedan **åtgärd** > **ändra egenskaper för**. 
   3. På den **proxykonfiguration** fliken bör du se Proxyadressen. Proxyadressen måste vara densamma som den Proxyadressen som visas i registerinställningarna. Annars kan du ändra den till samma adress.

*  **Kontrollera om bandbredden som begränsning är begränsad på processervern**.

   Öka bandbredden och kontrollera om problemet kvarstår.


## <a name="source-machine-isnt-listed-in-the-azure-portal"></a>Källdatorn visas inte i Azure portal

När du försöker markera källdatorn för att aktivera replikering med Site Recovery kanske datorn inte är tillgängliga för en av följande orsaker:

* **Två virtuella datorer med samma instans UUID**: Om två virtuella datorer under vCenter har samma instans UUID, visas den första virtuella datorn som identifierats av konfigurationsservern på Azure-portalen. Se till att inga två virtuella datorer har samma instans UUID för att lösa problemet. Det här scenariot är vanligt i instanser där en säkerhetskopierade virtuell dator blir aktiv och är inloggad i vår identifieringsposter. Referera till [Azure Site Recovery VMware till Azure: Hur du rensar dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx) att lösa.
* **Felaktig vCenter användarautentiseringsuppgifter**: Se till att du har lagt till korrekt vCenter-autentiseringsuppgifter när du ställer in konfigurationsservern med hjälp av OVF-mall eller en enhetlig installation. För att kontrollera de autentiseringsuppgifter som du har lagt till under installationen, se [ändra autentiseringsuppgifter för automatisk identifiering](vmware-azure-manage-configuration-server.md#modify-credentials-for-automatic-discovery).
* **inte tillräcklig behörighet för vCenter**: Om de behörigheter som finns att få åtkomst till vCenter inte har behörigheterna som krävs kan kan identifiera virtuella datorer misslyckas. Se till att behörigheterna som beskrivs i [förbereda ett konto för automatisk identifiering](vmware-azure-tutorial-prepare-on-premises.md#prepare-an-account-for-automatic-discovery) läggs till vCenter-användarkonto.
* **Azure Site Recovery-hanteringsservrar**: Om den virtuella datorn används som hanteringsserver under en eller flera av följande roller - Configuration server /scale-out processervern / Master-målserver, och du inte kommer att kunna välja den virtuella datorn från portalen. Management-servrar kan inte replikeras.
* **Redan skyddad/redundansväxlats via Azure Site Recovery services**: Om den virtuella datorn är redan skyddad eller har redundansväxlats genom Site Recovery, är den virtuella datorn inte kan väljas för skydd i portalen. Se till att den virtuella datorn som du söker i portalen inte redan skyddas av en annan användare eller en annan prenumeration.
* **vCenter som inte är anslutna**: Kontrollera att vCenter är i anslutet tillstånd. Du kan kontrollera genom att gå till Recovery Services-valv > Site Recovery-infrastruktur > Konfigurationsservrar > Klicka på respektive konfigurationsservern > öppnas ett blad på din direkt med information om associerade servrar. Kontrollera om vCenter är ansluten. Om tillståndet ”inte ansluten”, Lös problemet och sedan [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter kan listas virtuella datorer på portalen.
* **ESXi avstängd**: Om ESXi-värd som den virtuella datorn finns i avstängd, har virtuella datorn visas inte eller kan inte väljas på Azure portal. Starta ESXi-värd [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server) på portalen. Därefter kan listas virtuella datorer på portalen.
* **Väntar på omstart**: Om det finns en väntande omstart på den virtuella datorn, kommer sedan du inte att kunna välja dator på Azure-portalen. Se till att slutföra väntande omstart-aktiviteter [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter kan listas virtuella datorer på portalen.
* **Det gick inte att hitta IP**: Om den virtuella datorn inte har en giltig IP-adress som är kopplade till den, kommer sedan du inte att kunna välja dator på Azure-portalen. Se till att tilldela en giltig IP-adress till den virtuella datorn, [uppdatera konfigurationsservern](vmware-azure-manage-configuration-server.md#refresh-configuration-server). Därefter kan listas virtuella datorer på portalen.

## <a name="protected-virtual-machines-are-greyed-out-in-the-portal"></a>Skyddade virtuella datorer är gråmarkerat ut i portalen

Virtuella datorer som replikeras under Site Recovery är inte tillgängliga i Azure-portalen om det finns dubblettvärden i systemet. Om du vill lära dig mer om att ta bort inaktuella poster och lösa problemet, referera till [Azure Site Recovery VMware till Azure: Hur du rensar dubbletter eller inaktuella poster](https://social.technet.microsoft.com/wiki/contents/articles/32026.asr-vmware-to-azure-how-to-cleanup-duplicatestale-entries.aspx).

## <a name="common-errors-and-recommended-steps-for-resolution"></a>Vanliga fel och rekommenderat steg för att matcha

### <a name="initial-replication-issues-error-78169"></a>Inledande replikeringsproblem [fel 78169]

Över en ovan att säkerställa att det finns ingen anslutning, bandbredd eller tid synkronisera relaterad information, se till att:

- Inga ett antivirusprogram hindrar Azure Site Recovery. Lär dig [mer](vmware-azure-set-up-source.md#azure-site-recovery-folder-exclusions-from-antivirus-program) på mappundantag som krävs för Azure Site Recovery.

### <a name="application-consistency-recovery-point-missing-error-78144"></a>Konsekvenspunkt för program som saknas [fel 78144]

 Detta inträffar på grund av problem med Volume Shadow copy Service (VSS). För att lösa detta: 
 
- Kontrollera att den installerade versionen av Azure Site Recovery-agenten är minst 9.22.2. 
- Kontrollera att VSS-providern är installerad som en tjänst i Windows-tjänster och kontrollera komponenten Service MMC för att kontrollera att Azure Site Recovery VSS Provider visas också.
- Om VSS-leverantören inte är installerat finns det [installationsfel felsökningsartikeln](vmware-azure-troubleshoot-push-install.md#vss-installation-failures).

- Om VSS är inaktiverad
    - Kontrollera att starttypen för tjänsten VSS-Provider har angetts till **automatisk**.
    - Starta om följande tjänster:
        - VSS-tjänsten
        - Azure Site Recovery VSS Provider
        - VDS-tjänsten

### <a name="high-churn-on-source-machine-error-78188"></a>Hög omsättning på källdatorn [fel 78188]

Möjliga orsaker:
- Dataändringshastigheten (skrivna byte/sek) på de angivna diskarna för den virtuella datorn är mer än den [stöds gränser för Azure Site Recovery](site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) för kontot för replikeringsmållagring.
- Det finns en plötslig insamling i omsättningsfrekvensen på grund av vilka hög mängden data som är i vänteläge för överföring.

Att lösa problemet:
- Se till att target lagringskontotypen (Standard eller Premium) är etablerad enligt kravet på omsättning vid källan.
- Om den observerade omsättningen är tillfällig, Vänta några timmar innan data väntar överföringen komma ikapp och för att skapa återställningspunkter.
- Om problemet kvarstår kan du använda ASR [Distributionshanteraren](site-recovery-deployment-planner.md#overview) för att planera replikering.

### <a name="no-heartbeat-from-source-machine-error-78174"></a>Inget pulsslag från källdatorn [fel 78174]

Detta händer när Azure Site Recovery-mobilitetsagenten på källdatorn inte kommunicerar med den konfigurationsserver (CS).

Använd följande steg för att lösa problemet genom för att verifiera nätverksanslutningen från den Virtuella källdatorn till Config-Server:

1. Kontrollera att källdatorn är igång.
2. Logga in på källdatorn med ett konto som har administratörsbehörighet.
3. Kontrollera att följande tjänster körs och om inte starta om tjänsterna:
   - Svagents (InMage Scout VX Agent)
   - InMage Scout Application Service
4. På källdatorn, kontrollera loggarna på platsen för felinformation:

       C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log
    
### <a name="no-heartbeat-from-process-server-error-806"></a>Inget pulsslag från Processervern [fel 806]
Om det finns inget pulsslag från Process Server (PS), kontrollerar du att:
1. PS VM är igång
2. Kontrollera följande loggar på PS för felinformation:

       C:\ProgramData\ASR\home\svsystems\eventmanager*.log
       and
       C:\ProgramData\ASR\home\svsystems\monitor_protection*.log

### <a name="no-heartbeat-from-master-target-error-78022"></a>Inget pulsslag från Huvudmålservern [fel 78022]

Detta händer när Azure Site Recovery-mobilitetsagenten på Huvudmålet inte kommunicerar med konfigurationsservern.

Lös problemet genom att använda följande steg för att kontrollera status för tjänsten:

1. Kontrollera att Master Target Virtuella datorn körs.
2. Logga in på Master Target-VM med ett konto som har administratörsbehörighet.
    - Kontrollera att tjänsten svagents körs. Starta om tjänsten om den körs
    - Titta på loggfilerna på plats för felinformation:
        
          C:\Program Files (X86)\Microsoft Azure Site Recovery\agent\svagents*log

### <a name="process-server-is-not-reachable-from-the-source-machine-error-78186"></a>Processervern kan inte nås från källdatorn [fel 78186]

Det här felet leder till appen och krascher konsekventa återställningspunkter som skapas om det inte åtgärdas. Lös problemet genom att följa den nedan felsökning länkar:
1. Se till att [PS-tjänster som körs](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Kontrollera anslutningsproblem för käll-dator](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Kontrollera problem med nätverksanslutningen process server](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) och Följ riktlinjerna för:
    - Kontrollerar anslutningen med källkod
    - Problem med brandväggen och proxyservern

### <a name="data-upload-blocked-from-source-machine-to-process-server-error-78028"></a>Dataöverföringen har blockerats från källdatorn till Processerver [fel 78028]

Det här felet leder till appen och krascher konsekventa återställningspunkter som skapas om det inte åtgärdas. Lös problemet genom att följa den nedan felsökning länkar:

1. Se till att [PS-tjänster som körs](vmware-azure-troubleshoot-replication.md#monitor-process-server-health-to-avoid-replication-issues)
2. [Kontrollera anslutningsproblem för käll-dator](vmware-azure-troubleshoot-replication.md#check-the-source-machine-for-connectivity-issues)
3. [Kontrollera problem med nätverksanslutningen process server](vmware-azure-troubleshoot-replication.md#check-the-process-server-for-connectivity-issues) och Följ riktlinjerna för:
    - Kontrollerar anslutningen med källkod
    - Problem med brandväggen och proxyservern

## <a name="next-steps"></a>Nästa steg

Om du behöver mer hjälp kan du ställa din fråga i den [Azure Site Recovery-forum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr). Vi har en aktiv community och en av våra tekniker kan hjälpa dig.
