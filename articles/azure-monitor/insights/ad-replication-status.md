---
title: Övervaka Active Directory-replikeringsstatus
description: Lösningspaketet för Active Directory Replication övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/24/2018
ms.openlocfilehash: 30b0c7c87f6d55586b931be1445b175ce58565d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80055903"
---
# <a name="monitor-active-directory-replication-status-with-azure-monitor"></a>Övervaka Active Directory-replikeringsstatus med Azure Monitor

![AD-replikeringsstatussymbol](./media/ad-replication-status/ad-replication-status-symbol.png)

Active Directory är en viktig komponent i en IT-miljö för företag. För att säkerställa hög tillgänglighet och hög prestanda har varje domänkontrollant en egen kopia av Active Directory-databasen. Domänkontrollanter replikeras med varandra för att sprida ändringar i hela företaget. Fel i den här replikeringsprocessen kan orsaka en mängd olika problem i hela företaget.

AD-replikeringsstatuslösningen övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand-solution.md)]

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

### <a name="prerequisites"></a>Krav

* AD-replikeringsstatuslösningen kräver en version av .NET Framework 4.6.2 eller senare installerad på varje dator som har Log Analytics-agenten för Windows (kallas även MICROSOFT Monitoring Agent (MMA)) installerad.  Agenten används av System Center 2016 - Operations Manager, Operations Manager 2012 R2 och Azure Monitor.
* Lösningen stöder domänkontrollanter som kör Windows Server 2008 och 2008 R2, Windows Server 2012 och 2012 R2 och Windows Server 2016.
* En Log Analytics-arbetsyta för att lägga till Active Directory Health Check-lösningen från Azure-marknadsplatsen i Azure-portalen. Det krävs ingen ytterligare konfiguration.


### <a name="install-agents-on-domain-controllers"></a>Installera agenter på domänkontrollanter
Du måste installera agenter på domänkontrollanter som är medlemmar i domänen som ska utvärderas. Du måste också installera agenter på medlemsservrar och konfigurera agenterna för att skicka AD-replikeringsdata till Azure Monitor. Information om hur du ansluter Windows-datorer till Azure Monitor finns i [Ansluta Windows-datorer till Azure Monitor](../../azure-monitor/platform/agent-windows.md). Om domänkontrollanten redan är en del av en befintlig System Center Operations Manager-miljö som du vill ansluta till Azure Monitor läser du [Anslut Operations Manager till Azure Monitor](../../azure-monitor/platform/om-agents.md).

### <a name="enable-non-domain-controller"></a>Aktivera icke-domänkontrollant
Om du inte vill ansluta någon av dina domänkontrollanter direkt till Azure Monitor kan du använda vilken annan dator som helst i domänen som är ansluten till Azure Monitor för att samla in data för AD Replication-statuslösningspaketet och få den att skicka data.

1. Kontrollera att datorn är medlem i den domän som du vill övervaka med ad-replikeringsstatuslösningen.
2. [Anslut Windows-datorn till Azure Monitor](../../azure-monitor/platform/om-agents.md) eller [anslut den med din befintliga Operations Manager-miljö till Azure Monitor](../../azure-monitor/platform/om-agents.md), om den inte redan är ansluten.
3. Ange följande registernyckel på den datorn:<br>Nyckel: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management Groups\<ManagementGroupName>\Solutions\ADReplication**<br>Värde: **IsTarget**<br>Värdedata: **sant**

   > [!NOTE]
   > Dessa ändringar börjar inte gälla förrän du startar om Tjänsten Microsoft Monitoring Agent (HealthService.exe).
   > ### <a name="install-solution"></a>Installera lösning
   > Följ processen som beskrivs i [Installera en övervakningslösning](solutions.md#install-a-monitoring-solution) för att lägga till **statuslösningen för Active Directory Replication** i logganalysarbetsytan. Det krävs ingen ytterligare konfiguration.


## <a name="ad-replication-status-data-collection-details"></a>Information om insamling av AD-replikeringsstatus
I följande tabell visas datainsamlingsmetoder och annan information om hur data samlas in för AD-replikeringsstatus.

| 
    plattform
   | Direkt agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agentdata som skickas via hanteringsgrupp | insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |var femte dag |



## <a name="understanding-replication-errors"></a>Förstå replikeringsfel

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Ad Replication-statuspanelen visar hur många replikeringsfel du har för tillfället. **Kritiska replikeringsfel** är fel som är på eller över 75 % av [tombstone-livslängden](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) för Active Directory-skogen.

![Panelen AD-replikeringsstatus](./media/ad-replication-status/oms-ad-replication-tile.png)

När du klickar på panelen kan du visa mer information om felen.
![INSTRUMENTPANELEN AD-replikeringsstatus](./media/ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status och status för målserverns status och källserver
Dessa kolumner visar status för målservrar och källservrar som har replikeringsfel. Numret efter varje domänkontrollantnamn anger antalet replikeringsfel på domänkontrollanten.

Felen för både målservrar och källservrar visas eftersom vissa problem är lättare att felsöka från källserverperspektivet och andra från målserverperspektivet.

I det här exemplet kan du se att många målservrar har ungefär samma antal fel, men det finns en källserver (ADDC35) som har många fler fel än alla andra. Det är troligt att det finns vissa problem på ADDC35 som gör att det misslyckas med att skicka data till sina replikeringspartner. Om du åtgärdar problemen på ADDC35 kan många av de fel som visas i målserverområdet lösas.

### <a name="replication-error-types"></a>Replikeringsfeltyper
Det här området ger dig information om vilka typer av fel som har upptäckts i hela företaget. Varje fel har en unik numerisk kod och ett meddelande som kan hjälpa dig att fastställa orsaken till felet.

Munken högst upp ger dig en uppfattning om vilka fel som visas mer och mindre ofta i din miljö.

Den visar när flera domänkontrollanter upplever samma replikeringsfel. I det här fallet kanske du kan identifiera eller identifiera en lösning på en domänkontrollant och sedan upprepa den på andra domänkontrollanter som påverkas av samma fel.

### <a name="tombstone-lifetime"></a>Tombstone Livstid
Tombstone-livslängden avgör hur länge ett borttaget objekt, kallat en gravsten, behålls i Active Directory-databasen. När ett borttaget objekt klarar tombstone-livstiden tas den automatiskt bort från Active Directory-databasen.

Standardlivstiden för tombstone är 180 dagar för de senaste versionerna av Windows, men den var 60 dagar på äldre versioner och kan ändras explicit av en Active Directory-administratör.

Det är viktigt att veta om du har replikeringsfel som närmar sig eller är förbi tombstone-livstiden. Om två domänkontrollanter upplever ett replikeringsfel som kvarstår efter tombstone-livstiden inaktiveras replikeringen mellan dessa två domänkontrollanter, även om det underliggande replikeringsfelet är åtgärdat.

Tombstone Lifetime-området hjälper dig att identifiera platser där inaktiverad replikering riskerar att hända. Varje fel i kategorin **Över 100 % TSL** representerar en partition som inte har replikerats mellan käll- och målservern under minst tombstone-livslängden för skogen.

I det här fallet räcker det inte att åtgärda replikeringsfelet. Du måste åtminstone undersöka manuellt för att identifiera och rensa kvardröjande objekt innan du kan starta om replikeringen. Du kan till och med behöva inaktivera en domänkontrollant.

Förutom att identifiera eventuella replikeringsfel som har kvarstått förbi tombstone-livslängden, vill du också vara uppmärksam på eventuella fel som faller i **50-75% TSL** eller **75-100% TSL-kategorierna.**

Dessa är fel som är klart kvardröjande, inte övergående, så de behöver sannolikt ditt ingripande för att lösa. Den goda nyheten är att de ännu inte har nått gravstenen livstid. Om du åtgärdar dessa problem snabbt och *innan* de når tombstone-livslängden kan replikeringen starta om med minimala manuella åtgärder.

Som nämnts tidigare visar instrumentpanelspanelen för AD Replication-statuslösningen antalet *kritiska* replikeringsfel i din miljö, som definieras som fel som är över 75 % av tombstone-livslängden (inklusive fel som är över 100 % av TSL). Sträva efter att hålla detta nummer på 0.

> [!NOTE]
> Alla livstidsprocentberäkningar för tombstone baseras på den faktiska tombstone-livslängden för Active Directory-skogen, så du kan lita på att dessa procentsatser är korrekta, även om du har angett en anpassad livstid för tombstone.
>
>

### <a name="ad-replication-status-details"></a>STATUSINFORMATION FÖR AD-replikering
När du klickar på ett objekt i någon av listorna visas ytterligare information om det med hjälp av en loggfråga. Resultaten filtreras så att endast de fel som är relaterade till objektet visas. Om du till exempel klickar på den första domänkontrollanten som anges under **ADDC02 (Destination Server Status)** visas frågeresultat filtrerade för att visa fel med domänkontrollanten som anges som målserver:

![AD-replikeringsstatusfel i frågeresultat](./media/ad-replication-status/oms-ad-replication-search-details.png)

Härifrån kan du filtrera vidare, ändra loggfrågan och så vidare. Mer information om hur du använder loggfrågorna i Azure Monitor finns [i Analysera loggdata i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md).

I **fältet HelpLink** visas URL:en för en TechNet-sida med ytterligare information om det specifika felet. Du kan kopiera och klistra in den här länken i webbläsarfönstret om du vill se information om felsökning och åtgärda felet.

Du kan också klicka på **Exportera** om du vill exportera resultaten till Excel. Om du exporterar data kan du visualisera replikeringsfeldata på det sätt du vill.

![exporterade AD-replikeringsstatusfel i Excel](./media/ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Vanliga frågor om AD-replikeringsstatus
**F: Hur ofta uppdateras AD-replikeringsstatusdata?**
S: Informationen uppdateras var femte dag.

**F: Finns det något sätt att konfigurera hur ofta dessa data uppdateras?**
A: Inte just nu.

**F: Måste jag lägga till alla mina domänkontrollanter på min Log Analytics-arbetsyta för att se replikeringsstatus?**
S: Nej, endast en enda domänkontrollant måste läggas till. Om du har flera domänkontrollanter på arbetsytan Log Analytics skickas data från alla till Azure Monitor.

**F: Jag vill inte lägga till några domänkontrollanter på min Log Analytics-arbetsyta. Kan jag fortfarande använda AD-replikeringsstatuslösningen?**

S: Ja. Du kan ange värdet för en registernyckel för att aktivera den. Se [Aktivera icke-domänkontrollant](#enable-non-domain-controller).

**F: Vad heter processen som gör datainsamlingen?**
S: AdvisorAssessment.exe

**F: Hur lång tid tar det innan data samlas in?**
S: Datainsamlingstiden beror på storleken på Active Directory-miljön, men tar vanligtvis mindre än 15 minuter.

**F: Vilken typ av data samlas in?**
S: Replikeringsinformation samlas in via LDAP.

**F: Finns det något sätt att konfigurera när data samlas in?**
A: Inte just nu.

**F: Vilka behörigheter behöver jag för att samla in data?**
S: Normala användarbehörigheter till Active Directory är tillräckliga.

## <a name="troubleshoot-data-collection-problems"></a>Felsöka problem med datainsamling
För att samla in data kräver AD-replikeringsstatuslösningspaketet minst en domänkontrollant som ska vara ansluten till logganalysarbetsytan. Tills du ansluter en domänkontrollant visas ett meddelande som anger att **data fortfarande samlas in**.

Om du behöver hjälp med att ansluta en av domänkontrollanterna kan du visa dokumentation på [Anslut Windows-datorer till Azure Monitor](../../azure-monitor/platform/om-agents.md). Om domänkontrollanten redan är ansluten till en befintlig System Center Operations Manager-miljö kan du visa dokumentation på [Connect System Center Operations Manager till Azure Monitor](../../azure-monitor/platform/om-agents.md).

Om du inte vill ansluta någon av dina domänkontrollanter direkt till Azure Monitor eller Till System Center Operations Manager läser du [Aktivera icke-domänkontrollant](#enable-non-domain-controller).

## <a name="next-steps"></a>Nästa steg
* Använd [loggfrågor i Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) för att visa detaljerade statusdata för Active Directory Replication.
