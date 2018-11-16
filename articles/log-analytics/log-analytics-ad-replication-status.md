---
title: Övervaka status för replikering av Active Directory med Azure Log Analytics | Microsoft Docs
description: Active Directory-replikeringsstatus-lösningspaket övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: 1b988972-8e01-4f83-a7f4-87f62778f91d
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 3092e8edf16e271826aef95575d92749b29f8b56
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51711522"
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Övervaka status för Active Directory-replikering med Log Analytics

![Symbol för AD-replikeringsstatus](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Active Directory är en viktig del av ett företags IT-miljö. För att säkerställa hög tillgänglighet och hög prestanda, har varje domänkontrollant sin egen kopia av Active Directory-databasen. Domänkontrollanterna med varandra för att sprida ändringarna i hela företaget. Fel i replikeringsprocessen kan orsaka en mängd olika problem i hela företaget.

AD-replikeringsstatus-lösningspaket övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel.

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

* Du måste installera agenter på domänkontrollanter som är medlemmar i domänen som ska utvärderas. Eller så du måste installera agenter på medlemsservrar och konfigurera agenter för att skicka data för AD-replikering till Log Analytics. Information om hur du ansluter Windows-datorer till Log Analytics finns i [ansluta Windows-datorer till Log Analytics](log-analytics-windows-agent.md). Om domänkontrollanten finns redan i en befintlig System Center Operations Manager-miljö som du vill ansluta till Log Analytics finns i [ansluta Operations Manager till Log Analytics](log-analytics-om-agents.md).
* Lägga till Active Directory-replikeringsstatus-lösning för Log Analytics-arbetsytan med processen som beskrivs i [lägga till Log Analytics-lösningar från lösningsgalleriet](../azure-monitor/insights/solutions.md).  Det krävs ingen ytterligare konfiguration.

## <a name="ad-replication-status-data-collection-details"></a>AD-replikeringsstatus data samling information
I följande tabell visas data samlingsmetoder och annan information om hur data samlas in för AD-replikeringsstatus.

| Plattform | Direktagent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agenten data som skickas via hanteringsgruppen | Insamlingsfrekvens |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |var femte dag |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Du kan också aktivera en icke-domänkontrollant att skicka AD-data till Log Analytics
Om du inte vill att ansluta alla dina domänkontrollanter direkt till Log Analytics, kan du använda någon annan dator i domänen ansluten till Log Analytics för att samla in data för AD-replikeringsstatus-lösningspaketet och den skickar data.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Så här aktiverar du en icke-domänkontrollant att skicka AD-data till Log Analytics
1. Kontrollera att datorn är medlem i den domän som du vill övervaka med hjälp av AD-replikeringsstatus-lösning.
2. [Anslut Windows-datorn till Log Analytics](log-analytics-windows-agent.md) eller [ansluta den med hjälp av din befintliga Operations Manager-miljö till Log Analytics](log-analytics-om-agents.md), om den inte redan är ansluten.
3. På datorn, anger du följande registernyckel:

   * Nyckel: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management grupper\<ManagementGroupName > \Solutions\ADReplication**
   * Värde: **IsTarget**
   * Värdedata: **SANT**

   > [!NOTE]
   > Dessa ändringar inte gälla förrän din starta om tjänsten Microsoft Monitoring Agent (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Förstå replikeringsfel
När du har AD status replikeringsdata som skickas till Log Analytics kan se du en panel som liknar följande bild i Log Analytics som anger hur många replikeringsfel som du har för närvarande.  
![AD-replikeringsstatus panelen](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Kritiskt replikeringsfel** finns fel eller högre än 75% av den [tombstone-livslängden](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) för Active Directory-skogen.

När du klickar på panelen kan visa du mer information om felen.
![AD-replikeringsstatus](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status för målserver och Status för källserver
Dessa kolumner visar status för målservrarna och källservrar som upplever replikeringsfel. Siffran efter varje domänkontrollantens namn anger hur många replikeringsfel på domänkontrollanten.

Fel för både målservrarna och källservrar visas eftersom vissa problem är enklare att felsöka från perspektiv för källan server och andra mål server perspektiv.

I det här exemplet ser du att många målservrar har ungefär samma antal fel, men det finns en källserver (ADDC35) som har många fler fel än alla andra. Det är troligt att det finns problem på ADDC35 som orsakar den för att kunna skicka data till dess replikeringspartners. Åtgärda problemen på ADDC35 kan du lösa många av de fel som visas i målområdet för servern.

### <a name="replication-error-types"></a>Typ av replikeringsfel
Det här området innehåller information om vilka typer av fel som identifierats i hela företaget. Varje fel innehåller en unik numerisk kod och ett meddelande som kan hjälpa dig avgöra den bakomliggande orsaken till felet.

Ringdiagram överst ger dig en uppfattning om vilka fel förekomma mer och mindre ofta i din miljö.

Den visar när flera domänkontrollanter får samma replikeringsfel. I det här fallet kan du identifiera eller identifiera en lösning på en domänkontrollant och sedan upprepa på andra domänkontrollanter som påverkas av samma fel.

### <a name="tombstone-lifetime"></a>Tombstone-livstid
Tombstone-livslängden avgör hur lång tid ett borttaget objekt, kallas en tombstone, sparas i Active Directory-databasen. När ett borttaget objekt har passerat tombstone-livslängden bort datainsamlingsprocess för skräpinsamling automatiskt från Active Directory-databasen.

Standard tombstone-livstiden är 180 dagar för de senaste versionerna av Windows, men det var 60 dagar på äldre versioner och kan ändras uttryckligen en Active Directory-administratör.

Det är viktigt att veta om du har replikeringsfel som närmar eller som har passerat tombstone-livslängden. Om två domänkontrollanter uppstår ett replikeringsfel som kvarstår efter tombstone-livslängden inaktivera replikering mellan de två domänkontrollanterna, även om den underliggande replikeringsfel har lösts.

Området Tombstone-livslängden hjälper dig att identifiera platser där inaktiverad replikering är i risk för händer. Varje fel i den **över 100% TLS** kategori representerar en partition som inte har replikerat mellan dess källa och mål för minst tombstone-livslängden för skogen.

I så fall kan bara åtgärda replikering inte räcker. Du måste undersöka manuellt för att identifiera och rensa kvarstående objekt innan du kan starta om replikeringen som ett minimum. Du kan även behöva ställa av en domänkontrollant.

Förutom att identifiera eventuella replikeringsfel som beständiga tidigare tombstone-livslängden du även vill ta hänsyn till eventuella fel i den **50 75% TLS** eller **75 – 100% TLS** kategorier.

Det här är fel som är tydligt kvarvarande, tillfälligt, så att de behöver förmodligen göra något att lösa. Den goda nyheten är att de inte har nått tombstone-livslängden. Om du åtgärda problemen snabbt och *innan* de når tombstone-livslängden, replikering kan starta om med minimal manuella åtgärder.

Som nämnts tidigare, på instrumentpanelen för AD-replikeringsstatus-lösningen visar hur många *kritiska* replikeringsfel i din miljö som har definierats som fel som är över 75% av tombstone-livslängden (inklusive fel som är över 100% av TLS). Strävar efter att hålla det här värdet 0.

> [!NOTE]
> Alla tombstone livslängd procentberäkningar baseras på faktiska tombstone-livslängden för dina Active Directory-skog så att du kan lita på att dessa procenttal är korrekta, även om du har ett värde för anpassad tombstone-livstid som anges.
>
>

### <a name="ad-replication-status-details"></a>Statusinformation för AD-replikering
När du klickar på ett objekt i någon av listor visas ytterligare information om den med hjälp av Loggsökning. Resultaten filtreras för att visa de fel som rör objektet. Exempel: Om du klickar på den första domänkontrollanten i listan under **Status för målserver (ADDC02)**, visas sökresultat som filtrerats till Visa fel med den domänkontrollanten som listas som målservern:

![AD-replikering status fel i sökresultaten](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Härifrån kan du filtrera ytterligare, ändra sökfrågan och så vidare. Läs mer om hur du använder Log Search [Loggsökningar](log-analytics-queries.md).

Den **HelpLink** fältet visar Webbadressen till en TechNet-sida med ytterligare information om det specifika felet. Du kan kopiera och klistra in den här länken i webbläsarfönstret för att visa information om felsökning och åtgärda felet.

Du kan också klicka på **exportera** att exportera resultaten till Excel. Exportera data kan hjälpa dig att visualisera data för replikering fel på något sätt som du vill ha.

![exporterade AD status replikeringsfel i Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD-replikeringsstatus vanliga frågor och svar
**F: hur ofta är AD status replikeringsdata uppdateras?**
S: informationen uppdateras var femte dag.

**F: finns det ett sätt att konfigurera hur ofta data ska uppdateras?**
S: inte just nu.

**F: Jag behöver lägga till alla domänkontrollanter i min Log Analytics-arbetsyta om du vill se replikeringsstatusen?**
S: Nej, endast en enda domänkontrollant måste läggas till. Om du har flera domänkontrollanter i Log Analytics-arbetsytan skickas data från alla till Log Analytics.

**F: Jag vill inte att lägga till alla domänkontrollanter i Min arbetsyta för Log Analytics. Kan jag fortfarande använda AD-replikeringsstatus-lösningen?**
S: Ja. Du kan ange värdet för en registernyckel för att aktivera den. Se [att aktivera en icke-domänkontrollant att skicka AD-data till Log Analytics](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**F: Vad är namnet på processen som gör datainsamlingen?**
S: AdvisorAssessment.exe

**F: hur lång tid tar det för de data som samlas in?**
S: data collection tiden beror på storleken på Active Directory-miljö, men det tar normalt mindre än 15 minuter.

**F: vilken typ av data som samlas in?**
S: replikering information som samlas in via LDAP.

**F: finns det ett sätt att konfigurera när data har samlats in?**
S: inte just nu.

**F: vilka behörigheter behöver jag att samla in data?**
S: normala användarbehörigheter till Active Directory är tillräckliga.

## <a name="troubleshoot-data-collection-problems"></a>Felsöka problem med insamling
För att samla in data, kräver AD-replikeringsstatus-lösningspaket minst en domänkontrollant som är anslutna till Log Analytics-arbetsytan. Tills du ansluter en domänkontrollant, visas ett meddelande som anger att **data samlas fortfarande**.

Om du behöver hjälp med att ansluta en av domänkontrollanterna kan du visa dokumentationen på [ansluta Windows-datorer till Log Analytics](log-analytics-windows-agent.md). Om domänkontrollanten är redan ansluten till en befintlig System Center Operations Manager-miljö kan du också visa dokumentationen på [ansluta System Center Operations Manager till Log Analytics](log-analytics-om-agents.md).

Om du inte vill att någon av dina domänkontrollanter ansluta direkt till Log Analytics eller till System Center Operations Manager, se [att aktivera en icke-domänkontrollant att skicka AD-data till Log Analytics](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>Nästa steg
* Använd [Loggsökningar i Log Analytics](log-analytics-queries.md) att visa detaljerad status för data för Active Directory-replikering.
