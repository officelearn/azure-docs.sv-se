---
title: Log Analytics vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om tjänsten Azure Monitor logs Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/01/2019
ms.openlocfilehash: 77159e0fa73a1f56688c867c55ae46f28016992c
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75394789"
---
# <a name="log-analytics-faq"></a>Vanliga frågor och svar om Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor Log Analytics-arbetsytan. Om du har ytterligare frågor om Log Analytics kan du gå till den [diskussionsforum](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) och ställa frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.


## <a name="new-logs-experience"></a>Nya upplevelsen för loggar

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>F: Vad är skillnaden mellan den nya upplevelsen för loggar och Log Analytics?

S: de är samma sak. [Log Analytics ingår som en funktion i Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) att tillhandahålla en mer enhetlig övervakning miljö. Den nya upplevelsen för loggar i Azure Monitor är exakt samma som Log Analytics-frågor som många kunder redan har använt.

### <a name="q-can-i-still-use-log-search"></a>F: kan jag fortfarande använda Log Search? 

S: log Search finns för närvarande är fortfarande tillgängliga i OMS-portalen och i Azure-portalen under namnet **loggar (klassisk)** . OMS-portalen kommer officiellt dras tillbaka den 15 januari 2019. Den klassiska upplevelsen för loggar i Azure-portalen kommer att dras tillbaka gradvis och ersätts den nya upplevelsen för loggar. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>F. Kan jag fortfarande använda Advanced Analytics-portalen? 
Den nya upplevelsen för loggar i Azure-portalen är baserad på Advanced Analytics-portalen, men den fortfarande kan användas utanför Azure-portalen. En översikt över för att ta bort den här externa portalen tillkännages snart.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>F. Varför kan inte se Query Explorer och spara knappar i den nya upplevelsen för loggar?

**Fråga Explorer**, **spara** och **ange avisering** knappar är inte tillgängliga när du utforskar loggar i kontexten för en viss resurs. För att skapa aviseringar, spara eller läsa in en fråga, begränsas loggar till en arbetsyta. För att öppna loggar i kontexten för arbetsytan, Välj **alla tjänster** > **övervakaren** > **loggar**. Senaste använda arbetsytan har valts, men du kan välja en annan arbetsyta. Se [visa och analysera data i Log Analytics](../log-query/portals.md) för mer information.

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>F. Hur extraherar anpassade fält i den nya upplevelsen för loggar? 

S: anpassade fält extrahering stöds för närvarande i den klassiska loggar upplevelse. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>F. Var hittar jag listvy i de nya loggarna? 

S: listvyn är inte tillgänglig i de nya loggarna. Det finns en pil till vänster om varje post i resultattabellen. Klicka på pilen för att öppna informationen för en viss post. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>F. När du har kört en fråga är en lista över föreslagna filter tillgängliga. Hur kan jag se filter? 

S: Klicka på filter i den vänstra rutan om du vill se en förhands granskning av de nya filtren-implementeringen. Detta är nu baserat på hela resultatuppsättningen i stället för att vara begränsad till 10 000 postgränsen av Användargränssnittet. Det här är en lista över de mest populära filter och 10 vanligaste värden för varje filter. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>F. Varför får jag felet: ”registrera resursprovidern” Microsoft.Insights ”för den här prenumerationen att aktivera den här frågan” i loggar efter Detaljgranskning i från virtuell dator? 

S: som standard många resursproviders registreras automatiskt, men du kan behöva registrera några resursproviders manuellt. Den här koden konfigurerar din prenumeration för att arbeta med resursprovidern. Omfattningen för registrering är alltid prenumerationen. Mer information finns i [Resursproviders och resurstyper](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>F. Varför kan jag får ingen åtkomst felmeddelande vid åtkomst till loggar från en sida för virtuell dator? 

S: Om du vill visa VM-loggar måste beviljas läsbehörighet till arbetsytor som lagrar VM-loggarna. I dessa fall kan måste administratören bevilja dig behörigheter i Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>F. Varför får jag åtkomst till Min arbetsyta i OMS-portalen, men jag får felet ”du har inte behörighet” i Azure-portalen?  

S: Om du vill öppna en arbetsyta i Azure måste du ha Azure behörigheter. Det finns tillfällen där du inte kanske har rätt behörighet. I dessa fall kan administratören måste ge dig med behörigheter i Azure.See [OMS-portalen som flyttar till Azure](oms-portal-transition.md) för mer information.

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>F. Varför ser jag inte kan inte Vydesigner posten i loggarna?

S: view Designer finns bara i loggarna för användare som har tilldelats med deltagarbehörighet eller högre.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>F. Kan jag fortfarande använda analysportalen utanför Azure?

A. Ja, sidan loggar i Azure och Advanced Analytics-portalen är baserade på samma kod. Log Analytics ingår som en funktion i Azure Monitor för att ge en mer enhetlig upplevelse för övervakning. Du kan fortfarande komma åt Analytics Portal med URL: en: https:\/\/Portal. loganalytics. io/Subscriptions/{subscriptionId}/ResourceGroups/{resourceGroupName}/arbets ytor/{workspaceName}.



## <a name="general"></a>Allmänt

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>F. Hur hittar jag Mina vyer och lösningar i Azure-portalen? 

S: en lista över vyer och installerade lösningar är tillgängliga i Azure-portalen. Klicka på **Alla tjänster**. Välj i listan över resurser, **övervakaren**, klicka sedan på **... Mer**. Senaste använda arbetsytan har valts, men du kan välja en annan arbetsyta. 

### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>F. Använder Log Analytics samma agent som Azure Security Center?

S: med tidig juni 2017 började Azure Security Center använder Microsoft Monitoring Agent för att samla in och lagra data. Mer information finns i [Azure Security Center-plattformen migrering vanliga frågor och svar](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>F. Vilka kontroller som utförs av AD och SQL-bedömning lösningar?

S: följande fråga visar en beskrivning av alla kontroller som utförs för närvarande:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Resultaten kan sedan exporteras till Excel för vidare undersökning.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>F. Varför ser jag något annat än OMS i System Center Operations Manager-konsolen?

S: beroende på vilka Update Rollup av Operations Manager du har, kan du se en nod för *System Center Advisor*, *Operational Insights*, eller *Log Analytics*.

Text sträng uppdateringen *OMS* ingår i ett hanteringspaket som importeras manuellt. Om du vill se aktuell text och funktioner, följer du anvisningarna i den senaste System Center Operations Manager Update Rollup KB-artikeln och uppdatera konsolen.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>F: finns det en lokal version av Log Analytics?

S: Nej. Log Analytics är en skalbar molnbaserad tjänst som bearbetar och lagrar stora mängder data. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>F. Hur kan jag bli meddelad när datainsamlingen slutar?

S: med stegen som beskrivs i [skapa en ny log-avisering](../../azure-monitor/platform/alerts-metric.md) meddelas när datainsamlingen slutar.

Ange när du skapar aviseringen för när datainsamlingen slutar den:

- **Definiera aviseringsvillkor** ange Log Analytics-arbetsytan som mål för resursen.
- **Aviseringskriterier** ange följande:
   - **Signalnamn** Välj **anpassade loggsökning**.
   - **Sökfråga** till`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Aviseringslogik** är **Baserad på** *antal resultat* och **Villkor** som är *Större än* ett **Tröskelvärde** på *0*
   - **Tidsperiod** av *30* minuter och **Varningsfrekvens** till varje *10* minuter
- **Definiera aviseringsinformation** ange följande:
   - **Namnet** till *insamling av Data stoppades*
   - **Allvarlighetsgrad** till *varning*

Ange en befintlig eller skapa en ny [åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) så att när aviseringen log matchar villkoren, du får ett meddelande om du har ett pulsslag saknas för mer än 15 minuter.

## <a name="configuration"></a>Konfiguration

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>F. Kan jag ändra namnet på tabell-/ blob-behållaren som används för att läsa från Azure Diagnostics SÄKERHETSSPECIFIKA?

A. Nej, det går inte för närvarande att läsa från valfri tabeller eller behållare i Azure storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>F. IP-adresser använder den Log Analytics-tjänsten? Hur undviker jag att min brandvägg endast tillåter trafik till Log Analytics-tjänsten?

A. Log Analytics-tjänsten bygger på Azure. Log Analytics-IP-adresser som tillhör den [IP-intervall i Microsoft Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653).

Eftersom tjänstdistributioner görs, ändra faktiska IP-adresserna för Log Analytics-tjänsten. DNS-namn ska tillåtas via brandväggen finns dokumenterade i [krav på](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>F. Jag bara använder ExpressRoute för att ansluta till Azure. Använder Mina Log Analytics-trafik min ExpressRoute-anslutning?

A. De olika typerna av ExpressRoute trafik beskrivs i den [dokumentation om ExpressRoute](../../expressroute/expressroute-faqs.md#supported-services).

Trafik till Log Analytics använder offentliga peering ExpressRoute-kretsen.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>F. Finns det ett enkelt och smidigt sätt att flytta en befintlig Log Analytics-arbetsyta till en annan Log Analytics-arbetsyta/Azure-prenumeration?

A. Den `Move-AzResource` cmdlet kan du flytta en Log Analytics-arbetsyta och ett Automation-konto från en Azure-prenumeration till en annan. Mer information finns i [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Den här ändringen kan också göras i Azure-portalen.

Du kan inte flytta data från en Log Analytics-arbetsyta till en annan eller ändra den region som Log Analytics-data lagras i.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>F: hur gör jag för att lägga till Log Analytics till System Center Operations Manager?

S: uppdatera till den senaste samlade uppdateringen och importera hanteringspaket kan du ansluta Operations Manager till Log Analytics.

>[!NOTE]
>Operations Manager-anslutning till Log Analytics är endast tillgänglig för System Center Operations Manager 2012 SP1 och senare.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>F: hur kan jag bekräfta att en agent ska kunna kommunicera med Log Analytics?

A: om du vill säkerställa att agenten kan kommunicera med Log Analytics arbets ytan går du till: kontroll panelen, säkerhets & inställningar, **Microsoft Monitoring Agent**.

Under den **Azure Log Analytics (OMS)** fliken ska du leta efter en grön bockmarkering. En grön bockmarkering som bekräftar att agenten ska kunna kommunicera med Azure-tjänsten.

En gul varningsikon innebär att agenten har problem med kommunikationen med Log Analytics. En vanlig orsak är att Microsoft Monitoring Agent-tjänsten har stoppats. Använda tjänsthanteraren för att starta om tjänsten.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>F: hur stoppar jag en agent från att kommunicera med Log Analytics?

A: ta bort datorn från listan Log Analytics hanterade datorer i System Center Operations Manager. Operations Manager uppdaterar konfigurationen av agenten inte längre rapporten till Log Analytics. Agenter som är direkt anslutna till Log Analytics, kan du hindra dem från kommunicerar via: Kontrollpanelen-, säkerhets- och inställningar, **Microsoft Monitoring Agent**.
Under **Azure Log Analytics (OMS)** , ta bort alla arbetsytor som visas.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>F: Varför får jag ett fel när jag försöker flytta Min arbetsyta från en Azure-prenumeration till en annan?

S: Om du vill flytta en arbetsyta till en annan prenumeration eller resursgrupp du måste först ta bort länken till Automation-konto på arbetsytan. Tar bort länken ett Automation-konto kräver borttagning av dessa lösningar om de är installerade på arbetsytan: uppdateringshantering, ändringsspårning eller starta/stoppa VM under kontorstid tas bort. När dessa lösningar har tagits bort, ta bort länken till Automation-kontot genom att välja **länkad arbetsytor** i det vänstra fönstret i Automation-kontot resursen och klicka på **ta bort arbetsytans länk** i menyfliksområdet.
 > Ta bort lösningar behöver installeras på arbetsytan och Automation-länk till arbetsytan måste räknas efter flytten.

Se till att du har behörighet i både Azure-prenumerationer.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>F: Varför får jag ett fel när jag försöker uppdatera en SavedSearch?

S: du behöver lägga till ”etag' i brödtexten i API: et eller i Azure Resource Manager-mallens egenskaper:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Agentinformationen
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>F. Hur mycket data kan jag skicka via agenten till Log Analytics? Finns det en maximal mängd data per kund?
A. Det finns ingen gräns för mängden data som överförs, baserat på det prissättnings alternativ du väljer – kapacitets reservation eller betala per användning. En Log Analytics arbets yta är utformad för att automatiskt skala upp för att hantera volymen som kommer från en kund, även om den är terabyte per dag. Mer information finns i [pris information](https://azure.microsoft.com/pricing/details/monitor/).

Log Analytics-agenten har utformats för att se till att den har ett litet utrymme. Datavolymen varierar beroende på de lösningar som du aktiverar. Du kan hitta detaljerad information på datavolymen och se en analys på detaljnivå av lösningen i den [användning](../../azure-monitor/platform/data-usage.md) sidan.

För mer information kan du läsa en [kund blogg](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) som visar resultatet när du har utvärderat resursutnyttjande (avtryck) för Log Analytics agenten.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>F. Hur mycket nätverksbandbredd används av Microsoft Management Agent (MMA) när du skickar data till Log Analytics?

A. Bandbredd är en funktion för mängden data som skickas. Data komprimeras som skickas över nätverket.

### <a name="q-how-much-data-is-sent-per-agent"></a>F. Hur mycket data skickas per agent?

A. Mängden data som skickats per agent beror på:

* Lösningar som du har aktiverat
* Antalet loggar och prestandaräknare som samlas in
* Mängden data i loggarna

Den totala användningen visas på den [användning](../../azure-monitor/platform/data-usage.md) sidan.

Använd följande fråga för datorer som kommer köras WireData-agenten kan se hur mycket data som skickas:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Nästa steg

[Kom igång med Azure Monitor](../../azure-monitor/overview.md) och lär dig mer om Log Analytics och kom igång på bara några minuter.
