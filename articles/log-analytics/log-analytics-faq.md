---
title: Logga Analytics vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor och svar om Azure Log Analytics-tjänsten.
services: log-analytics
documentationcenter: ''
author: MGoedtel
manager: carmonm
editor: ''
ms.assetid: ad536ff7-2c60-4850-a46d-230bc9e1ab45
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/21/2018
ms.author: magoedte
ms.openlocfilehash: 398a62cbba952f35f29c1b1f411a6d5b901d2973
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="log-analytics-faq"></a>Vanliga frågor och svar om Log Analytics
Den här Microsoft-FAQ är en lista över vanliga frågor om logganalys i Microsoft Azure. Om du har några ytterligare frågor om logganalys går du till den [diskussionsforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) och dina frågor. När en fråga är vanliga vi lägga till den i den här artikeln så att den finns snabbt och enkelt.

## <a name="general"></a>Allmänt

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>FRÅGOR. Använder logganalys samma agent som Azure Security Center?

A. I tidig juni 2017 började Azure Security Center med hjälp av Microsoft Monitoring Agent att samla in och lagra data. Läs mer i [Azure Security Center-plattformen migrering vanliga frågor och svar](../security-center/security-center-platform-migration-faq.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>FRÅGOR. Vilka kontroller som utförs av AD och SQL-bedömning lösningar?

A. Följande fråga visar en beskrivning av alla kontroller som utförs för närvarande:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Resultatet kan sedan exporteras till Excel för vidare undersökning.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>F: Varför visas något annat än OMS i System Center Operations Manager-konsolen?

S: beroende på vilka Update Rollup av Operations Manager på, kan du se en nod för *System Center Advisor*, *åtgärdsinformation*, eller *logganalys*.

Text sträng uppdateringen *OMS* ingår i ett hanteringspaket som måste importeras manuellt. Om du vill visa aktuell text och funktioner, följer du anvisningarna i den senaste samlade KB för System Center Operations Manager-artikeln och uppdatera konsolen.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>F: finns det en lokal version av Log Analytics?

S: Nej. Log Analytics är en skalbar molnbaserad tjänst som bearbetar och lagrar stora mängder data. 

### <a name="q-how-do-i-troubleshoot-if-log-analytics-is-no-longer-collecting-data"></a>FRÅGOR. Hur felsöker jag om logganalys inte längre att samla in data?

S: Om du på den kostnadsfria prisnivån och har skickat mer än 500 MB data under en dag, stoppar datainsamling för resten av dagen. Når den dagliga gränsen är en vanlig orsak till att logganalys slutar att samla in data eller data verkar saknas.  

Log Analytics skapar en händelse av typen *pulsslag* och kan användas för att avgöra om datainsamling avbryts. 

Kör följande fråga i sökning för att kontrollera om du når den dagliga gränsen och data som saknas: `Heartbeat | summarize max(TimeGenerated)`

Om du vill kontrollera en viss dator, kör du följande fråga: `Heartbeat | where Computer=="contosovm" | summarize max(TimeGenerated)`

När datainsamlingen slutar, beroende på det tidsintervall som valts visas inte några poster som returneras.   

I följande tabell beskrivs skäl som datainsamling stoppar och en rekommenderad åtgärd för att återuppta datainsamling:

| Insamling av orsak stoppar                       | Att återuppta datainsamling |
| -------------------------------------------------- | ----------------  |
| Gränsen för ledigt data nåtts<sup>1</sup>       | Vänta tills nästa månad för samling som du vill starta om automatiskt eller<br> Ändra till en betald prisnivå |
| Azure-prenumeration är i ett pausat tillstånd på grund av: <br> Kostnadsfri utvärderingsversion avslutades <br> Azure-pass upphört att gälla <br> Varje månad utgiftsgräns uppnåtts (till exempel på en MSDN- eller Visual Studio-prenumeration)                          | Konvertera till en betald prenumeration <br> Konvertera till en betald prenumeration <br> Ta bort gränsen eller vänta tills gränsen återställs |

<sup>1</sup> om arbetsytan finns på den kostnadsfria prisnivån, du är begränsad till att skicka 500 MB data per dag till tjänsten. När du når den dagliga gränsen slutar datainsamlingen förrän följande dag. Data som skickas när datainsamling har stoppats är inte indexerat och är inte tillgängligt för sökning. När datainsamlingen återställs sker bearbetning endast för nya data som skickas. 

Log Analytics använder UTC-tid varje dag startar vid midnatt UTC. Om arbetsytan når den dagliga gränsen, återupptar bearbetningen under den första timmen UTC nästa dag.

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>FRÅGOR. Hur kan jag få meddelanden när datainsamling stoppar?

S: med stegen som beskrivs i [skapa en aviseringsregel](log-analytics-alerts-creating.md#create-an-alert-rule) ska meddelas när datainsamling stoppar.

Ange när du skapar en avisering om när datainsamling slutar på:
- **Namnet** till *datainsamling har stoppats*
- **Allvarlighetsgrad** till *varning*
- **Sökfråga** till`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
- **Tidsfönstret** till *30 minuter*.
- **Varna frekvens** till varje *tio* minuter.
- **Skapa en avisering baserat på** som *antal resultat*
- **Antalet resultat** som *större än 0*

Den här aviseringen utlöses när frågan returnerar resultat om du har pulsslag saknas för mer än 15 minuter.  Använd stegen som beskrivs i [Lägga till åtgärder i varningsregler](log-analytics-alerts-actions.md) för att konfigurera e-post, webhook eller runbook-åtgärd för regeln.

## <a name="configuration"></a>Konfiguration
### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>FRÅGOR. Kan jag ändra namnet på tabellen/blob-behållaren som används för att läsa från Azure Diagnostics (BOMULLSTUSS)?

A. Nej, det går inte för närvarande att läsa från valfri tabeller eller behållare i Azure-lagring.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>FRÅGOR. IP-adresser använder den Log Analytics-tjänsten? Hur kan jag vara säker på att min brandvägg bara tillåter trafik till Log Analytics-tjänsten?

A. Log Analytics-tjänsten är byggt på Azure. Log Analytics IP-adresser som tillhör den [IP-intervall i Microsoft Azure Datacenter](http://www.microsoft.com/download/details.aspx?id=41653).

Eftersom tjänstdistributioner görs ändra faktiska IP-adresserna för logganalys-tjänsten. DNS-namn för att tillåta genom brandväggen finns dokumenterade i [konfigurera proxy-och brandväggsinställningarna i logganalys](log-analytics-proxy-firewall.md).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>FRÅGOR. Jag använder ExpressRoute för att ansluta till Azure. Använder Mina logganalys trafik min ExpressRoute-anslutningen?

A. De olika typerna av ExpressRoute trafik beskrivs i den [ExpressRoute dokumentation](../expressroute/expressroute-faqs.md#supported-services).

Trafik till logganalys använder offentlig peering ExpressRoute-kretsen.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>FRÅGOR. Finns det ett enkelt och enkelt sätt att flytta en befintlig logganalys-arbetsyta till en annan logganalys-arbetsytan/Azure-prenumeration?

A. Den `Move-AzureRmResource` cmdleten låter dig flytta logganalys-arbetsytan och ett Automation-konto från en Azure-prenumeration. Mer information finns i [flytta AzureRmResource](http://msdn.microsoft.com/library/mt652516.aspx).

Den här ändringen kan även göras i Azure-portalen.

Du kan inte flytta data från en logganalys-arbetsytan till en annan eller ändra den region som logganalys data lagras i.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>F: hur lägger jag till logganalys i System Center Operations Manager?

S: uppdatera till den senaste samlade uppdateringen och importera hanteringspaket kan du ansluta Operations Manager till logganalys.

>[!NOTE]
>Operations Manager-anslutningen till logganalys är endast tillgängligt för System Center Operations Manager 2012 SP1 och senare.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>F: hur kan jag bekräfta att en agent kan kommunicera med Log Analytics?

S: för att säkerställa att agenten kan kommunicera med OMS, gå till: Kontrollpanelen, säkerhet och inställningar, **Microsoft Monitoring Agent**.

Under den **Azure logganalys (OMS)** fliken, leta efter en grön bock. En grön bockmarkering som bekräftar att agenten kan kommunicera med Azure-tjänsten.

En gul varningsikon innebär att agenten har problem med kommunikation med logganalys. En vanlig orsak är Microsoft Monitoring Agent-tjänsten har stoppats. Använda tjänsthanteraren för att starta om tjänsten.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>F: hur förhindrar en agent från att kommunicera med Log Analytics?

S: i System Center Operations Manager, ta bort datorn från listan över hanterade datorer OMS. Operations Manager uppdaterar konfigurationen av agenten inte längre rapporten till logganalys. Agenter som är direkt anslutna till logganalys, kan du stoppa dem från att kommunicera via: Kontrollpanelen, säkerhet och inställningar, **Microsoft Monitoring Agent**.
Under **Azure logganalys (OMS)**, ta bort alla arbetsytor i listan.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>F: Varför inträffar ett fel när jag försöker flytta Min arbetsyta från en Azure-prenumeration till en annan?

S: Om du använder Azure-portalen, se till att endast arbetsytan har markerats för flyttning. Välj inte lösningar--flyttas de automatiskt när arbetsytan flyttas. 

Se till att du har behörighet i både Azure-prenumerationer.

## <a name="agent-data"></a>Agenten data
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>FRÅGOR. Hur mycket data kan jag skicka via agenten till Log Analytics? Finns det en maximal mängd data per kund?
A. Fria abonnemang anger en daglig fjärrskrivbordsanslutning 500 MB per arbetsytan. Standard och premium-planer har ingen gräns på mängden data som överförs. Som en molntjänst logganalys är utformad för att automatiskt skala upp till referensen volymen kommer från en kund – även om det är TB per dag.

Logganalys-agent har utformats för att se till att den har en kompakta. Datavolym varierar beroende på de lösningar som du aktiverar. Du kan hitta detaljerad information om datavolym och se en analys på detaljnivå av lösning i den [användning](log-analytics-usage.md) sidan.

Mer information kan du läsa en [kunden blogg](http://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) om kompakta OMS-Agent.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>FRÅGOR. Hur mycket nätverksbandbredd används av Microsofts hanteringsagent (MMA) när data skickades till Log Analytics?

A. Bandbredd är en funktion på mängden data som skickas. Data komprimeras som skickas över nätverket.

### <a name="q-how-much-data-is-sent-per-agent"></a>FRÅGOR. Hur mycket data skickas per agent?

A. Mängden data som skickats per agent beror på:

* Lösningar som du har aktiverat
* Antalet loggar och prestandaräknare som samlas in
* Mängden data i loggarna

Den kostnadsfria prisnivån är ett bra sätt att publicera flera servrar och mätaren vanliga datavolym. Den totala användningen visas på den [användning](log-analytics-usage.md) sidan.

Använd följande fråga för att se hur mycket data skickas för datorer som kommer köra WireData agenten:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Nästa steg
* [Kom igång med logganalys](log-analytics-get-started.md) att lära sig mer om logganalys och komma igång i minuter.
