---
title: Azure Monitor för nätverk (för hands version)
description: En snabb översikt för Azure Monitor för nätverk som ger en omfattande vy över hälsa och mått för alla distribuerade nätverks resurser utan någon konfiguration.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/24/2020
ms.openlocfilehash: 5f076f477c36f96d1807ce7071720225a6df8e03
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "91803814"
---
# <a name="azure-monitor-for-networks-preview"></a>Azure Monitor för nätverk (för hands version)
Azure Monitor för nätverk ger en omfattande vy över [hälsa](https://docs.microsoft.com/azure/service-health/resource-health-checks-resource-types) och [mått](../platform/metrics-supported.md) för alla distribuerade nätverks resurser utan någon konfiguration.  Den ger också till gång till [alla funktioner för](../../network-watcher/network-watcher-monitoring-overview.md#diagnostics) nätverks övervakning, till exempel [anslutnings övervakaren](../../network-watcher/connection-monitor-preview.md), [flödes loggning för nätverks säkerhets grupper (NSG: er)](../../network-watcher/network-watcher-nsg-flow-logging-overview.md), [trafikanalys](../../network-watcher/traffic-analytics.md)och andra funktioner för nätverksdiagnostik.

Azure Monitor för nätverk är strukturerad runt följande viktiga komponenter i övervakningen:
- [Nätverks hälsa och-mått](#networkhealth)
- [Anslutning](#connectivity)
- [Trafik](#traffic)
- [Diagnostikverktyg](#diagnostictoolkit)

## <a name="network-health-and-metrics"></a><a name="networkhealth"></a>Nätverks hälsa och-mått

**Översikts** sidan för Azure Monitor för nätverk är ett smidigt sätt att visualisera inventeringen av dina nätverks resurser tillsammans med resurs hälsa och aviseringar. Den är uppdelad i fyra viktiga funktions områden – sökning och filtrering, Resource Health och statistik, aviseringar. och beroende vy

![Översikts sida](media/network-insights-overview/overview.png)

### <a name="search-and-filtering"></a>Sökning och filtrering
Vyn resurs hälsa och aviseringar kan anpassas med hjälp av filter som **prenumeration**, **resurs grupp** och **resurs typ**. Sökrutan ger möjlighet att söka igenom resurs egenskaper.

Du kan använda sökrutan för att söka efter resurser och associerade resurser. Till exempel är en offentlig IP-adress kopplad till en Application Gateway. Om du söker efter offentliga IP DNS-namn identifieras både offentlig IP-adress och tillhör ande Application Gateway.

![Azure Monitor för nätverk – Sök](media/network-insights-overview/search.png)


### <a name="resource-health-and-metric"></a>Resource Health och mått
Varje panel representerar en resurs typ med antalet instanser som distribueras över alla prenumerationer som valts tillsammans med resursens hälso status. I exemplet nedan finns det 45 ER-och VPN-anslutningar, 44 är felfria och 1 otillgängliga.

![Azure Monitor för nätverk – resurs hälsa](media/network-insights-overview/resource-health.png)

Om du klickar på de otillgängliga återställnings-och VPN-anslutningarna öppnas en Metric-vy. 

![Azure Monitor för nätverk – Metric-vy](media/network-insights-overview/metric-view.png)

Du kan klicka på varje-element i diagramvyn. Klicka på hälso ikonen för att omdirigera till resurs hälsa för den anslutningen. Klicka på aviseringar för att omdirigera till aviseringar och mått sidan för anslutningen. 

### <a name="alerts"></a>Aviseringar
I rutnätet **aviseringar** till höger får du en översikt över alla aviseringar som har genererats för de valda resurserna i alla prenumerationer. Klicka på sidan antal aviseringar för att gå till sidan detaljerade aviseringar.

### <a name="dependency-view"></a>Beroende vy
**Beroende** vyn hjälper till att visualisera hur resursen konfigureras. För hands beroende vy stöds nu för Application Gateway, virtuellt WAN och Load Balancer. Om du till exempel vill Application Gateway kan beroende vy nås genom att klicka på Application Gateway resurs namnet från vyn mått rutnät. Detta gäller även för virtuella WAN-och Load Balancer.

![Azure Monitor för nätverk – Application Gateway vy](media/network-insights-overview/application-gateway.png)

**Beroende** vyn för Application Gateway ger en förenklad vy över hur klient delens IP-adresser är anslutna till lyssnare, regler och backend-pool. De anslutande kanterna är färgkodade och ger ytterligare information baserat på Server delens hälso tillstånd. Vyn innehåller också en detaljerad vy över Application Gateway mått och mått för alla relaterade Server dels pooler, till exempel virtuell dators skalnings uppsättning och VM-instanser.

![Azure Monitor för nätverk – beroende vy](media/network-insights-overview/dependency-view.png)

Med det beroende diagrammet kan du enkelt navigera till konfigurations inställningar. Högerklicka på en backend-pool för att få åtkomst till andra funktioner. Om till exempel backend-poolen är en virtuell dator kan du direkt komma åt VM-insikter och Network Watcher anslutnings fel sökning för att identifiera anslutnings problem.

![Azure Monitor för nätverk – beroende Visa-menyn](media/network-insights-overview/dependency-view-menu.png)

Sök-och filter fältet i beroende vyn ger ett smidigt sätt att söka igenom grafen. Om du till exempel söker efter *AppGWTestRule* i exemplet nedan så begränsas den grafiska vyn till alla noder som är anslutna via *AppGWTestRule*.

![Azure Monitor för nätverk – Sök exempel](media/network-insights-overview/search-example.png)

Olika filter ger hjälp att begränsa till en specifik sökväg och tillstånd. Välj till exempel endast *ohälsosamt* från List rutan **hälso status** för att visa alla kanter där tillståndet är *dåligt*.

Klicka på **vyn detaljerat mått** om du vill starta en förkonfigurerad arbets bok med detaljerade mått för Application Gateway, alla resurser för Server dels poolen och klient delens IP-adresser. 

## <a name="connectivity"></a><a name="connectivity"></a>Anslutningsmöjlighet

Fliken **anslutning** är ett smidigt sätt att visualisera alla tester som kon figurer ATS med anslutnings övervakaren och [anslutnings övervakaren (för hands version)](../../network-watcher/connection-monitor-preview.md) för den valda uppsättningen prenumerationer.

![Fliken anslutning i Azure Monitor för nätverk](media/network-insights-overview/azure-monitor-for-networks-connectivity-tab.png)

Test grupperas efter paneler och destinationer och visar tillgänglighets status för varje test. Tillgängliga inställningar ger enkel åtkomst till att konfigurera kriterier för tillgänglighet baserat på kontroller misslyckades (%) och Sökefter (MS). När värdena har angetts uppdateras status för varje test baserat på urvalskriterierna.

![Anslutnings test i Azure Monitor för nätverk](media/network-insights-overview/azure-monitor-for-networks-connectivity-tests.png)

Om du klickar på en käll-eller mål panel öppnas en Metric-vy.

![Anslutnings mått i Azure Monitor för nätverk](media/network-insights-overview/azure-monitor-for-networks-connectivity-metrics.png)


Du kan klicka på varje-element i diagramvyn. Klicka på ikonen för **nåbarhet** för att omdirigera till Portal sidan **anslutnings övervakare** för att Visa hopp om hopp sto pol Ogin och anslutnings problem som identifieras. Klicka på **aviseringar** för att omdirigera till varningar och **kontrollerna misslyckades procent/fördröjning-** svars tid för att omdirigera till mått sidan för den valda anslutnings övervakaren.

 **Aviserings**   rutnätet till höger innehåller en vy över alla aviseringar som har genererats för de anslutnings test som kon figurer ATS i alla prenumerationer. Klicka på sidan antal aviseringar för att gå till sidan detaljerade aviseringar.

## <a name="traffic"></a><a name="traffic"></a>Trafik
Fliken trafik ger åtkomst till alla NSG: er som kon figurer ATS för [NSG flödes loggar](../../network-watcher/network-watcher-nsg-flow-logging-overview.md) och [trafikanalys](../../network-watcher/traffic-analytics.md) för den valda uppsättningen prenumerationer och grupperas efter platser. Sök funktionen på den här fliken aktiverar identifiering av NSG: er som kon figurer ATS för den genomsökta IP-adressen. Du kan söka efter valfri IP-adress i din miljö och den regionala vyn i vyn visar alla NSG: er tillsammans med NSG flödes loggar och konfigurations status för trafik analys.

![Vyn trafik i Azure Monitor för nätverk](media/network-insights-overview/azure-monitor-for-networks-traffic-view.png)

Om du klickar på en region panel öppnas en rutnätsvy som gör det enkelt att visa och konfigurera NSG flödes loggar och Trafikanalys.  

![Vyn trafik region i Azure Monitor för nätverk](media/network-insights-overview/azure-monitor-for-networks-traffic-region-view.png)

Du kan klicka på varje-element i diagramvyn. Klicka på konfigurations status för att redigera NSG flödes logg och Trafikanalys konfiguration. Klicka på aviseringar för att omdirigera till trafik aviseringarna som kon figurer ATS för den valda NSG. På samma sätt kan du navigera till Trafikanalys visning genom att klicka på arbets ytan.  

 **Aviserings**   rutnätet till höger innehåller en vy över alla trafikanalys arbets ytans baserade aviseringar över alla prenumerationer. Klicka på sidan antal aviseringar för att gå till sidan detaljerade aviseringar.

## <a name="diagnostic-toolkit"></a><a name="diagnostictoolkit"></a> Diagnostikverktyg
Diagnostic Toolkit ger till gång till alla diagnostiska funktioner som är tillgängliga för fel sökning av nätverket. I den här List rutan får du till gång till funktioner som [paket avbildning](../../network-watcher/network-watcher-packet-capture-overview.md), [VPN-fel sökning](../../network-watcher/network-watcher-troubleshoot-overview.md), [anslutnings fel sökning](../../network-watcher/network-watcher-connectivity-overview.md), [nästa hopp](../../network-watcher/network-watcher-next-hop-overview.md) och [kontrol lera IP-flöde](../../network-watcher/network-watcher-ip-flow-verify-overview.md).

![Fliken diagnostiska verktyg](media/network-insights-overview/azure-monitor-for-networks-diagnostic-toolkit.png)

## <a name="troubleshooting"></a>Felsökning 

Allmän fel söknings vägledning finns i artikeln om dedikerad informations [fel sökning](troubleshoot-workbooks.md)för arbets böcker.

I det här avsnittet får du hjälp med diagnos och fel sökning av några vanliga problem som kan uppstå när du använder Azure Monitor för nätverk. Använd listan nedan för att hitta den information som är relevant för det aktuella problemet.

### <a name="resolving-performance-issues-or-failures"></a>Lösa prestanda problem eller fel

Information om hur du felsöker problem med nätverks problem som du identifierar med Azure Monitor för nätverk finns i fel söknings dokumentationen för resurs fel. Problem med att felsöka Länkar för tjänster med hög användning visas nedan.
* Virtual Network (VNET)
* Application Gateway
* VPN Gateway
* ExpressRoute 
* Lastbalanserare 

### <a name="why-dont-i-see-the-resources-from-all-the-subscriptions-i-have-selected"></a>Varför visas inte resurserna från alla prenumerationer jag har valt

Nätverks insikter kan bara visa resurser från 5 prenumerationer åt gången. 

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-network-insights-how-do-i-do-so"></a>Jag vill göra ändringar eller lägga till ytterligare visualiseringar i nätverks insikter, hur gör jag för att göra det

Om du vill göra ändringar väljer du "redigerings läge" för att ändra arbets boken. sedan kan du spara arbetet som en ny arbets bok som är knuten till en angiven prenumeration och resurs grupp.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Vad är tids kornen när vi fäster någon del av arbets böckerna

Vi använder "Auto" Time-kornig het, vilket innebär att den är beroende av vilket tidsintervall som väljs.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Vad är tidsintervallet när någon del av arbets boken är fäst

Tidsintervallet beror på instrument panelens inställningar.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-network-insights"></a>Vad händer om jag vill se andra data eller göra egna visualiseringar? Hur kan jag göra ändringar i nätverks insikter

Du kan redigera arbets boken som visas i valfri sida-panel och i vyn mått, genom att använda redigerings läget och sedan spara ditt arbete som en ny arbets bok som kommer att ha alla dina nya ändringar.


## <a name="next-steps"></a>Nästa steg

- Läs mer om nätverks övervakning i [Vad är Azure Network Watcher?](../../network-watcher/network-watcher-monitoring-overview.md).
- Lär dig mer om arbets böckerna i scenarier är utformade för att stödja, hur du skapar nya och anpassar befintliga rapporter och mer genom att granska [skapa interaktiva rapporter med Azure Monitor arbets böcker](../platform/workbooks-overview.md).
