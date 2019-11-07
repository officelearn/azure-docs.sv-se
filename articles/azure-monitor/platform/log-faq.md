---
title: Log Analytics vanliga frågor och svar | Microsoft Docs
description: Svar på vanliga frågor om tjänsten Azure Monitor logs Analytics.
ms.service: azure-monitor
ms.subservice: logs
ms.topic: conceptual
author: MGoedtel
ms.author: magoedte
ms.date: 11/01/2019
ms.openlocfilehash: 9eb921fc8ea19486db0fc3311764931f09e11464
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579313"
---
# <a name="log-analytics-faq"></a>Vanliga frågor och svar om Log Analytics

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor Log Analytics-arbetsytan. Om du har ytterligare frågor om Log Analytics går du till [diskussions forumet](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) och publicerar dina frågor. När en fråga ofta är tillfrågad, lägger vi till den i den här artikeln så att den snabbt och enkelt kan hittas.


## <a name="new-logs-experience"></a>Nya loggar upplevelse

### <a name="q-whats-the-difference-between-the-new-logs-experience-and-log-analytics"></a>F: Vad är skillnaden mellan nya loggar och Log Analytics?

A: de är samma sak. [Log Analytics integreras som en funktion i Azure Monitor](../../azure-monitor/azure-monitor-rebrand.md) för att ge en mer enhetlig övervaknings upplevelse. Den nya logg upplevelsen i Azure Monitor är exakt samma som de Log Analytics frågor som många kunder redan har använt.

### <a name="q-can-i-still-use-log-search"></a>F: kan jag fortfarande använda loggs ökning? 

A: loggs ökning är för närvarande fortfarande tillgänglig i OMS-portalen och i Azure Portal under namn **loggarna (klassisk)** . OMS-portalen kommer att dras tillbaka officiellt den 15 januari 2019. Den klassiska logg upplevelsen i Azure Portal kommer gradvis att dras tillbaka och ersätta den nya logg upplevelsen. 

### <a name="q-can-i-still-use-advanced-analytics-portal"></a>F. Kan jag fortfarande använda Advanced Analytics-portalen? 
Nya loggar i Azure Portal baseras på Advanced Analytics-portalen, men den kan fortfarande nås utanför Azure Portal. Översikten för att ta bort den här externa portalen kommer snart att meddelas.

### <a name="q-why-cant-i-see-query-explorer-and-save-buttons-in-the-new-logs-experience"></a>F. Varför kan jag inte se Query Explorer och spara knappar i den nya loggar upplevelsen?

**Fråga Utforskaren**, **Spara** och **Ställ in aviserings** knappar är inte tillgängliga när du utforskar loggar i kontexten för en speciell resurs. Om du vill skapa aviseringar, Spara eller läsa in en fråga måste loggar vara begränsad till en arbets yta. Om du vill öppna loggar i arbets ytans kontext väljer du **alla tjänster** > **övervaka** > **loggar**. Den senast använda arbets ytan har valts, men du kan välja en annan arbets yta. Mer information finns i [Visa och analysera data i Log Analytics](../log-query/portals.md) .

### <a name="q-how-do-i-extract-custom-fields-in-the-new-logs-experience"></a>F. Hur gör jag för att extrahera anpassade fält i den nya loggar upplevelsen? 

A: anpassade fält som kan extraheras i den klassiska loggar upplevelsen. 

### <a name="q-where-do-i-find-list-view-in-the-new-logs"></a>F. Var hittar jag listvyn i de nya loggarna? 

A: listvyn är inte tillgänglig i de nya loggarna. Det finns en pil till vänster om varje post i resultat tabellen. Klicka på den här pilen för att öppna informationen för en speciell post. 

### <a name="q-after-running-a-query-a-list-of-suggested-filters-are-available-how-can-i-see-filters"></a>F. När du har kört en fråga är en lista över föreslagna filter tillgängliga. Hur kan jag se filter? 

S: Klicka på filter i den vänstra rutan om du vill se en förhands granskning av de nya filtren-implementeringen. Detta är nu baserat på din fullständiga resultat uppsättning i stället för att begränsas av begränsningen för 10 000-poster i användar gränssnittet. Detta är en lista över de mest populära filtren och de 10 vanligaste värdena för varje filter. 

### <a name="q-why-am-i-getting-the-error-register-resource-provider-microsoftinsights-for-this-subscription-to-enable-this-query-in-logs-after-drilling-in-from-vm"></a>F. Varför får jag fel meddelandet "Registrera Resource providern Microsoft. Insights för den här prenumerationen för att aktivera den här frågan i loggar efter detalj inläsning från virtuell dator? 

En: som standard registreras många resurs leverantörer automatiskt, men du kan behöva registrera vissa resurs leverantörer manuellt. Detta konfigurerar din prenumeration så att den fungerar med resurs leverantören. Omfånget för registreringen är alltid prenumerationen. Mer information finns i [Resursproviders och resurstyper](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

### <a name="q-why-am-i-am-getting-no-access-error-message-when-accessing-logs-from-a-vm-page"></a>F. Varför får jag inget fel meddelande vid åtkomst till loggar från en virtuell dator sida? 

A: om du vill visa VM-loggar måste du beviljas med Läs behörighet till de arbets ytor som lagrar de virtuella dator loggarna. I dessa fall måste administratören bevilja dig behörigheter i Azure.

### <a name="q-why-can-i-can-access-my-workspace-in-oms-portal-but-i-get-the-error-you-have-no-access-in-the-azure-portal"></a>F. Varför kan jag få åtkomst till min arbets yta i OMS-portalen, men jag får felet "du har ingen åtkomst" i Azure Portal?  

A: för att få åtkomst till en arbets yta i Azure måste du ha tilldelats Azure-behörighet. Det finns vissa fall där du kanske inte har rätt åtkomst behörighet. I dessa fall måste administratören bevilja dig behörigheter i Azure. mer information finns i [OMS-portalen flytta till Azure](oms-portal-transition.md) .

### <a name="q-why-cant-i-cant-see-view-designer-entry-in-logs"></a>F. Varför kan jag inte se posten View Designer i loggar?

A: View Designer är bara tillgänglig i loggar för användare som har tilldelats deltagar behörigheter eller högre.

### <a name="q-can-i-still-use-the-analytics-portal-outside-of-azure"></a>F. Kan jag fortfarande använda Analytics Portal utanför Azure?

A. Ja, sidan loggar i Azure och Advanced Analytics-portalen baseras på samma kod. Log Analytics integreras som en funktion i Azure Monitor för att ge en mer enhetlig övervaknings upplevelse. Du kan fortfarande komma åt Analytics Portal med URL: en: https:\/\/Portal. loganalytics. io/Subscriptions/{subscriptionId}/ResourceGroups/{resourceGroupName}/arbets ytor/{workspaceName}.



## <a name="general"></a>Allmänt

### <a name="q-how-can-i-see-my-views-and-solutions-in-azure-portal"></a>F. Hur kan jag se mina vyer och lösningar i Azure Portal? 

A: listan över vyer och installerade lösningar finns i Azure Portal. Klicka på **Alla tjänster**. I listan över resurser väljer du **övervaka**och klickar sedan på **... Mer**. Den senast använda arbets ytan har valts, men du kan välja en annan arbets yta. 

### <a name="q-why-i-cant-create-workspaces-in-west-central-us-region"></a>F. Varför jag inte kan skapa arbets ytor i västra centrala USA-regionen? 

A: den här regionen har en tillfällig kapacitets gräns. Den här gränsen planeras för att åtgärdas i slutet av september 2019.


### <a name="q-does-log-analytics-use-the-same-agent-as-azure-security-center"></a>F. Använder Log Analytics samma agent som Azure Security Center?

Ett: i början av juni 2017 började Azure Security Center använda Microsoft Monitoring Agent för att samla in och lagra data. Mer information Azure Security Center finns i [vanliga frågor och svar om migrering av plattform](../../security-center/security-center-enable-data-collection.md).

### <a name="q-what-checks-are-performed-by-the-ad-and-sql-assessment-solutions"></a>F. Vilka kontroller utförs av AD och SQL-utvärdering lösningar?

A: följande fråga visar en beskrivning av alla kontroller som utförs för närvarande:

```
(Type=SQLAssessmentRecommendation OR Type=ADAssessmentRecommendation) | dedup RecommendationId | select FocusArea, ActionArea, Recommendation, Description | sort Type, FocusArea,ActionArea, Recommendation
```

Resultaten kan sedan exporteras till Excel för ytterligare granskning.

### <a name="q-why-do-i-see-something-different-than-oms-in-the-system-center-operations-manager-console"></a>F. Varför ser jag något annorlunda än OMS i System Center Operations Manager-konsolen?

A: beroende på vilken Samlad uppdatering av Operations Manager du är på kan du se en nod för *System Center Advisor*, *Operational Insights*eller *Log Analytics*.

Text sträng uppdateringen till *OMS* ingår i ett hanterings paket som måste importeras manuellt. Om du vill se aktuell text och funktionalitet följer du anvisningarna i artikeln senaste System Center Operations Manager Samlad uppdatering KB och uppdaterar-konsolen.

### <a name="q-is-there-an-on-premises-version-of-log-analytics"></a>F: finns det en lokal version av Log Analytics?

A: Nej. Log Analytics är en skalbar moln tjänst som bearbetar och lagrar stora mängder data. 

### <a name="q-how-can-i-be-notified-when-data-collection-stops"></a>F. Hur kan jag få ett meddelande när data insamlingen stoppas?

A: Använd stegen som beskrivs i [skapa en ny logg avisering](../../azure-monitor/platform/alerts-metric.md) som ska meddelas när data insamlingen stoppas.

När du skapar en avisering för när data insamlingen stoppas anger du:

- **Definiera aviseringsvillkor** ange Log Analytics-arbetsytan som mål för resursen.
- **Aviseringskriterier** ange följande:
   - **Signal namn** Välj **anpassad loggs ökning**.
   - **Sökfråga** till`Heartbeat | summarize LastCall = max(TimeGenerated) by Computer | where LastCall < ago(15m)`
   - **Aviseringslogik** är **Baserad på** *antal resultat* och **Villkor** som är *Större än* ett **Tröskelvärde** på *0*
   - **Tids period** på *30* minuter och **aviserings frekvens** var *tionde* minut
- **Definiera aviseringsinformation** ange följande:
   - **Namn** till *data insamling stoppades*
   - **Allvarlighetsgrad** till *varning*

Ange en befintlig eller skapa en ny [Åtgärds grupp](../../azure-monitor/platform/action-groups.md) så att du får ett meddelande om att ett pulsslag saknas i mer än 15 minuter när logg aviseringen matchar kriterierna.

## <a name="configuration"></a>Konfiguration

### <a name="q-can-i-change-the-name-of-the-tableblob-container-used-to-read-from-azure-diagnostics-wad"></a>F. Kan jag ändra namnet på den Table/BLOB-behållare som används för att läsa från Azure-diagnostik (WAD)?

A. Nej, det är för närvarande inte möjligt att läsa från godtyckliga tabeller eller behållare i Azure Storage.

### <a name="q-what-ip-addresses-does-the-log-analytics-service-use-how-do-i-ensure-that-my-firewall-only-allows-traffic-to-the-log-analytics-service"></a>F. Vilka IP-adresser använder tjänsten Log Analytics? Hur gör jag för att se till att min brand vägg endast tillåter trafik till tjänsten Log Analytics?

A. Tjänsten Log Analytics skapas ovanpå Azure. Log Analytics IP-adresser finns i [Microsoft Azure Data Center IP-intervall](https://www.microsoft.com/download/details.aspx?id=41653).

När tjänst distributioner görs ändras de faktiska IP-adresserna för den Log Analytics tjänsten. De DNS-namn som ska tillåtas genom brand väggen dokumenteras i [nätverks krav](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).

### <a name="q-i-use-expressroute-for-connecting-to-azure-does-my-log-analytics-traffic-use-my-expressroute-connection"></a>F. Jag använder ExpressRoute för att ansluta till Azure. Använder min Log Analytics-anslutning min ExpressRoute-anslutning?

A. De olika typerna av ExpressRoute-trafik beskrivs i [ExpressRoute-dokumentationen](../../expressroute/expressroute-faqs.md#supported-services).

Trafik till Log Analytics använder den offentliga peering ExpressRoute-kretsen.

### <a name="q-is-there-a-simple-and-easy-way-to-move-an-existing-log-analytics-workspace-to-another-log-analytics-workspaceazure-subscription"></a>F. Finns det ett enkelt och enkelt sätt att flytta en befintlig Log Analytics arbets yta till en annan Log Analytics arbets yta/Azure-prenumeration?

A. Med `Move-AzResource` cmdlet kan du flytta en Log Analytics arbets yta och även ett Automation-konto från en Azure-prenumeration till en annan. Mer information finns i [Move-AzResource](https://msdn.microsoft.com/library/mt652516.aspx).

Den här ändringen kan också göras i Azure Portal.

Du kan inte flytta data från en Log Analytics arbets ytan till en annan eller ändra den region som Log Analytics data lagras i.

### <a name="q-how-do-i-add-log-analytics-to-system-center-operations-manager"></a>F: Hur gör jag för att lägga till Log Analytics till System Center Operations Manager?

A: om du uppdaterar till den senaste samlade uppdateringen och importerar hanterings paket kan du ansluta Operations Manager till Log Analytics.

>[!NOTE]
>Operations Manager anslutningen till Log Analytics är bara tillgänglig för System Center Operations Manager 2012 SP1 och senare.

### <a name="q-how-can-i-confirm-that-an-agent-is-able-to-communicate-with-log-analytics"></a>F: Hur kan jag bekräfta att en agent kan kommunicera med Log Analytics?

A: om du vill säkerställa att agenten kan kommunicera med Log Analytics arbets ytan går du till: kontroll panelen, säkerhets & inställningar, **Microsoft Monitoring Agent**.

Under fliken **Azure Log Analytics (OMS)** letar du efter en grön bock markering. En grön kryss markerings ikon bekräftar att agenten kan kommunicera med Azure-tjänsten.

En gul varnings ikon innebär att agenten har problem med att kommunicera med Log Analytics. En vanlig orsak är att Microsoft Monitoring Agent-tjänsten har stoppats. Använd Service Control Manager för att starta om tjänsten.

### <a name="q-how-do-i-stop-an-agent-from-communicating-with-log-analytics"></a>F: Hur gör jag för att stoppa en agent från att kommunicera med Log Analytics?

A: ta bort datorn från listan Log Analytics hanterade datorer i System Center Operations Manager. Operations Manager uppdaterar agentens konfiguration så att den inte längre rapporterar till Log Analytics. För agenter som är anslutna till Log Analytics direkt kan du hindra dem från att kommunicera via: kontroll panelen, säkerhets & inställningar, **Microsoft Monitoring Agent**.
Ta bort alla arbets ytor som visas under **Azure Log Analytics (OMS)** .

### <a name="q-why-am-i-getting-an-error-when-i-try-to-move-my-workspace-from-one-azure-subscription-to-another"></a>F: Varför får jag ett fel när jag försöker flytta min arbets yta från en Azure-prenumeration till en annan?

A: om du vill flytta en arbets yta till en annan prenumeration eller resurs grupp måste du först ta bort länken till Automation-kontot i arbets ytan. Borttagning av de här lösningarna kräver att dessa lösningar tas bort om de är installerade på arbets ytan: Uppdateringshantering, Ändringsspårning eller Starta/stoppa virtuella datorer när de inte används tas bort. När de här lösningarna har tagits bort tar du bort länken till Automation-kontot genom att välja **länkade arbets ytor** i den vänstra rutan i resursen Automation-konto och klickar på **ta bort arbets ytan** i menyfliksområdet.
 > Borttagna lösningar måste installeras om på arbets ytan, och automation-länken till arbets ytan måste inflationsjusteras efter flytten.

Se till att du har behörighet för båda Azure-prenumerationerna.

### <a name="q-why-am-i-getting-an-error-when-i-try-to-update-a-savedsearch"></a>F: Varför får jag ett fel när jag försöker uppdatera en SavedSearch?

S: du måste lägga till ' etag ' i bröd texten i API: et eller Azure Resource Manager mal Lav Bildegenskaper:
```
"properties": {
   "etag": "*",
   "query": "SecurityEvent | where TimeGenerated > ago(1h) | where EventID == 4625 | count",
   "displayName": "An account failed to log on",
   "category": "Security"
}
```

## <a name="agent-data"></a>Agent data
### <a name="q-how-much-data-can-i-send-through-the-agent-to-log-analytics-is-there-a-maximum-amount-of-data-per-customer"></a>F. Hur mycket data kan jag skicka genom agenten för att Log Analytics? Finns det en maximal mängd data per kund?
A. Det finns ingen gräns för mängden data som överförs, baserat på det prissättnings alternativ du väljer – kapacitets reservation eller betala per användning. En Log Analytics arbets yta är utformad för att automatiskt skala upp för att hantera volymen som kommer från en kund, även om den är terabyte per dag. Mer information finns i [pris information](https://azure.microsoft.com/pricing/details/monitor/).

Log Analytics agenten har utformats för att säkerställa att den har ett litet utrymme. Data volymen varierar beroende på vilka lösningar du aktiverar. Du kan hitta detaljerad information om data volymen och se fördelningen efter lösning på sidan [användning](../../azure-monitor/platform/data-usage.md) .

För mer information kan du läsa en [kund blogg](https://thoughtsonopsmgr.blogspot.com/2015/09/one-small-footprint-for-server-one.html) som visar resultatet när du har utvärderat resursutnyttjande (avtryck) för Log Analytics agenten.

### <a name="q-how-much-network-bandwidth-is-used-by-the-microsoft-management-agent-mma-when-sending-data-to-log-analytics"></a>F. Hur mycket nätverks bandbredd används av Microsofts hanterings agent (MMA) när du skickar data till Log Analytics?

A. Bandbredd är en funktion för mängden data som skickas. Data komprimeras när de skickas över nätverket.

### <a name="q-how-much-data-is-sent-per-agent"></a>F. Hur mycket data skickas per agent?

A. Mängden data som skickas per agent beror på:

* De lösningar du har aktiverat
* Antalet loggar och prestanda räknare som samlas in
* Data mängden i loggarna

Den övergripande användningen visas på sidan [användning](../../azure-monitor/platform/data-usage.md) .

För datorer som kan köra WireData-agenten använder du följande fråga för att se hur mycket data som skickas:

```
Type=WireData (ProcessName="C:\\Program Files\\Microsoft Monitoring Agent\\Agent\\MonitoringHost.exe") (Direction=Outbound) | measure Sum(TotalBytes) by Computer
```

## <a name="next-steps"></a>Nästa steg

[Kom igång med Azure Monitor](../../azure-monitor/overview.md) och lär dig mer om Log Analytics och kom igång på bara några minuter.
