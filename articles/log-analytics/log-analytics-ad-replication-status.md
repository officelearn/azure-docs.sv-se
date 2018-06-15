---
title: Visa replikeringsstatus för Active Directory med Azure Log Analytics | Microsoft Docs
description: Active Directory-replikeringsstatus lösningspaket övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel.
services: log-analytics
documentationcenter: ''
author: MGoedtel
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
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7707c4a1afdc42ef44a7b6f761ceb03b7e7da2f0
ms.sourcegitcommit: b32d6948033e7f85e3362e13347a664c0aaa04c1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2018
ms.locfileid: "29179342"
---
# <a name="monitor-active-directory-replication-status-with-log-analytics"></a>Visa replikeringsstatus för Active Directory med logganalys

![Statussymbolen för AD-replikering](./media/log-analytics-ad-replication-status/ad-replication-status-symbol.png)

Active Directory är en viktig del av ett företags IT-miljö. För att säkerställa hög tillgänglighet och hög prestanda, har varje domänkontrollant sin egen kopia av Active Directory-databasen. Domänkontrollanterna med varandra för att sprida ändringarna över hela företaget. Fel i den här replikeringen kan orsaka en rad olika problem över hela företaget.

Replikeringsstatus för AD-lösningspaket övervakar regelbundet Active Directory-miljön för eventuella replikeringsfel.

## <a name="installing-and-configuring-the-solution"></a>Installera och konfigurera lösningen
Använd följande information för att installera och konfigurera lösningen.

* Du måste installera agenter på domänkontrollanter som är medlemmar i domänen som ska utvärderas. Eller så du måste installera agenter på medlemsservrar och konfigurera agenter för att skicka data för AD-replikering till logganalys. Information om hur du ansluter en Windows-datorer till logganalys finns [ansluta Windows-datorer till logganalys](log-analytics-windows-agent.md). Om domänkontrollanten tillhör redan en befintlig miljö för System Center Operations Manager som du vill ansluta till Log Analytics, se [ansluta Operations Manager till logganalys](log-analytics-om-agents.md).
* Lägg till Active Directory-replikeringsstatus lösningen i logganalys-arbetsytan med processen som beskrivs i [lägga till logganalys lösningar från galleriet lösningar](log-analytics-add-solutions.md).  Det krävs ingen ytterligare konfiguration.

## <a name="ad-replication-status-data-collection-details"></a>AD replikeringsstatus data samling information
I följande tabell visas data collection metoder och annan information om hur data samlas in för AD-replikeringsstatus.

| Plattform | Styr Agent | SCOM-agent | Azure Storage | SCOM krävs? | SCOM-agent data som skickas via management-grupp | Frekvens för samlingen |
| --- | --- | --- | --- | --- | --- | --- |
| Windows |&#8226; |&#8226; |  |  |&#8226; |var femte dag |

## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Du kan också aktivera en icke-domänkontrollant AD data ska skickas till logganalys
Om du inte vill att ansluta alla domänkontrollanter direkt till Log Analytics, kan du använda andra datorer i din domän som är ansluten till logganalys för att samla in data för AD-replikeringsstatus lösningspaket och det skicka data.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-log-analytics"></a>Så här aktiverar du en icke-domänkontrollant AD data ska skickas till logganalys
1. Kontrollera att datorn är medlem i den domän som du vill övervaka replikeringsstatus för AD-lösning.
2. [Ansluta Windows-datorn till logganalys](log-analytics-windows-agent.md) eller [ansluta den med hjälp av din befintliga Operations Manager-miljö till logganalys](log-analytics-om-agents.md), om den inte redan är ansluten.
3. Ange följande registernyckel på datorn:

   * Nyckel: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management grupper\<ManagementGroupName > \Solutions\ADReplication**
   * Värde: **IsTarget**
   * Värdedata: **SANT**

   > [!NOTE]
   > Ändringarna inte gälla förrän din starta om tjänsten Microsoft Monitoring Agent (HealthService.exe).
   >
   >

## <a name="understanding-replication-errors"></a>Förstå replikeringsfel
När du har AD status replikeringsdata som skickas till logganalys kan se du en panel som liknar följande bild i logganalys som anger hur många replikeringsfel som du har för närvarande.  
![Replikeringsstatus för AD-panelen](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Kritiska replikeringsfel** gäller vid eller över 75% av den [tombstone-livslängden](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) för Active Directory-skogen.

När du klickar på ikonen kan du visa mer information om felen.
![AD replikeringsstatus instrumentpanelen](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)

### <a name="destination-server-status-and-source-server-status"></a>Status för mål-servern och Status för käll-servern
Dessa kolumner visar status för målservrar och källservrar som replikeringsfel har uppstått. Siffran efter varje domänkontrollantens namn anger antalet replikeringsfel på domänkontrollanten.

Fel för både målet och källservrar visas eftersom vissa problem är enklare att felsöka från källan server perspektiv och andra ur mål-servern.

I det här exemplet kan du se att många målservrar har ungefär samma antal fel, men det finns en källserver (ADDC35) som har många fler fel än alla andra. Det är troligt att det finns problem på ADDC35 som orsakar det gick inte att skicka data till replikeringspartner. Åtgärda problemen på ADDC35 kan lösa många av de fel som visas i området för mål-servern.

### <a name="replication-error-types"></a>Replikering feltyper
Det här området innehåller information om vilka typer av fel upptäcktes i hela företaget. Varje fel har en unik numerisk kod och ett meddelande som kan hjälpa dig att avgöra den bakomliggande orsaken till felet.

Ring överst ger dig en uppfattning av vilka fel visas mer och mer sällan i din miljö.

Den visar när flera domänkontrollanter får samma replikeringsfel. I det här fallet kan du kunna identifiera eller identifiera en lösning på en domänkontrollant och sedan upprepa på andra domänkontrollanter som påverkas av samma fel.

### <a name="tombstone-lifetime"></a>Tombstone-livslängden
Tombstone-livslängden anger hur länge ett borttaget objekt, som anges som tombstone, lagras i Active Directory-databasen. När ett borttaget objekt klarar tombstone-livslängden, bort en process som skräp samlingen automatiskt från Active Directory-databasen.

Standard tombstone-livslängden är 180 dagar för de senaste versionerna av Windows, men det var 60 dagar i äldre versioner och kan ändras uttryckligen en Active Directory-administratör.

Det är viktigt att veta om du har replikeringsfel som närmar dig eller har passerat tombstone-livslängden. Om två domänkontrollanter uppstår ett replikeringsfel som kvarstår efter tombstone-livslängden inaktivera replikering mellan de två domänkontrollanterna, även om det underliggande replikering felet har åtgärdats.

Området Tombstone-livslängden hjälper dig att identifiera platser där inaktiverad replikeringen är i fara för inträffar. Varje fel i den **över 100% TLS** kategori representerar en partition som inte har replikerats mellan dess käll- och server för minst tombstone-livslängden för skogen.

I så fall kan bara åtgärda replikering inte finns tillräckligt med. Som minimum måste du undersöka manuellt för att identifiera och rensa kvarstående objekt innan du startar om replikeringen. Du kan även behöva inaktivera en domänkontrollant.

Förutom att identifiera eventuella replikeringsfel beständiga tidigare tombstone-livslängden du också vara uppmärksam på eventuella fel i den **50 75% TLS** eller **75-100% TLS** kategorier.

Det här är fel som tydligt kvarvarande, tillfälligt, så att de behöver förmodligen göra något att lösa. Goda nyheter är att de ännu inte har nått tombstone-livslängden. Om du åtgärda problemen omedelbart och *innan* de når tombstone-livslängden, replikering kan du starta om med minimala manuella åtgärder.

Som nämnts tidigare på instrumentpanelen för replikeringsstatus för AD-lösningen visar hur många *kritiska* replikeringsfel i din miljö, som definieras som fel som är över 75% tombstone-livslängden (inklusive fel som är över 100% av TLS). Strävar efter att det här antalet på 0.

> [!NOTE]
> Alla tombstone livstid procentandel beräkningar baseras på faktiska tombstone-livslängden för Active Directory-skog så att du kan lita på att dessa procenttal är korrekta, även om du har en anpassad tombstone-livslängden inställd.
>
>

### <a name="ad-replication-status-details"></a>Statusinformation för AD-replikering
När du klickar på ett objekt i en av listorna finns mer information om den med hjälp av loggen sökning. Resultaten filtreras för att visa de fel som rör objektet. Till exempel om du klickar på den första domänkontrollanten som visas under **serverstatus för mål (ADDC02)**, finns i sökresultat som filtrerats till Visa fel med domänkontrollanten som anges som målservern:

![AD status replikeringsfel i sökresultat](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

Här kan du filtrera ytterligare, ändra frågan och så vidare. Mer information om hur du använder loggen sökningen finns [logga sökningar](log-analytics-log-searches.md).

Den **HelpLink** fältet visar Webbadressen till en TechNet-sida med ytterligare information om det specifika felet. Du kan kopiera och klistra in den här länken i webbläsaren om du vill se information om felsökning och åtgärda felet.

Du kan också klicka på **exportera** att exportera resultaten till Excel. Med hjälp av export av data kan du visualisera data för replikering fel på något sätt som du vill.

![exporterade AD status replikeringsfel i Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>AD-replikeringsstatus vanliga frågor och svar
**F: hur ofta är AD status replikeringsdata uppdateras?**
S: informationen uppdateras var femte dag.

**F: finns det ett sätt att konfigurera hur ofta dessa data uppdateras?**
S: inte just nu.

**F: behöver jag lägga till alla domänkontrollanter i logganalys-arbetsytan för att visa replikeringsstatus?**
S: inte måste bara en enda domänkontrollant läggas till. Om du har flera domänkontrollanter i logganalys-arbetsytan skickas data från alla till logganalys.

**F: Jag vill inte att lägga till alla domänkontrollanter i logganalys-arbetsytan. Kan jag fortsätta att använda replikeringsstatus för AD-lösningen?**
S: Ja. Du kan ange värdet för en registernyckel för att aktivera den. Se [att aktivera en icke-domänkontrollant AD data ska skickas till logganalys](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**F: Vad är namnet på processen som gör datainsamlingen?**
A: AdvisorAssessment.exe

**F: hur lång tid tar det för data som samlas in?**
S: data collection tid beror på storleken på Active Directory-miljö, men tar vanligtvis mindre än 15 minuter.

**F: vilken typ av data som samlas in?**
S: replikering information som samlas in via LDAP.

**F: finns det ett sätt att konfigurera när data samlas in?**
S: inte just nu.

**F: vilka behörigheter som behöver att samla in data?**
S: normal användarbehörigheter till Active Directory är tillräckliga.

## <a name="troubleshoot-data-collection-problems"></a>Felsökning av problem med samlingen
För att samla in data, kräver lösningspaket AD replikeringsstatus minst en domänkontrollant måste vara ansluten till logganalys-arbetsytan. Tills du ansluter en domänkontrollant, visas ett meddelande som anger att **data samlas fortfarande**.

Om du behöver hjälp med att ansluta en av domänkontrollanterna kan du visa dokumentationen på [ansluta Windows-datorer till logganalys](log-analytics-windows-agent.md). Alternativt, om domänkontrollanten är redan ansluten till en befintlig System Center Operations Manager-miljö, kan du visa dokumentationen på [ansluta System Center Operations Manager till logganalys](log-analytics-om-agents.md).

Om du inte vill att ansluta alla domänkontrollanter direkt till Log Analytics eller System Center Operations Manager finns [att aktivera en icke-domänkontrollant AD data ska skickas till logganalys](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

## <a name="next-steps"></a>Nästa steg
* Använd [logga sökningar i logganalys](log-analytics-log-searches.md) att visa detaljerad statusdata för Active Directory-replikering.
