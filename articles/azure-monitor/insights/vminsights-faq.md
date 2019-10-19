---
title: Vanliga frågor och svar om Azure Monitor for VMs (för hands version) | Microsoft Docs
description: Azure Monitor for VMs är en lösning i Azure som kombinerar hälso-och prestanda övervakning av operativ systemet på den virtuella Azure-datorn samt identifierar program komponenter och beroenden automatiskt med andra resurser och mappar kommunikationen mellan objekten. I den här artikeln besvaras vanliga frågor.
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 01/09/2018
ms.openlocfilehash: 675277a33a5613507297f1c77ee9cef3215b22a2
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2019
ms.locfileid: "72555210"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Vanliga frågor och svar om Azure Monitor for VMs (för hands version)
Microsoft FAQ (vanliga frågor och svar) är en lista över vanliga frågor om Azure Monitor for VMs. Om du har ytterligare frågor om lösningen går du till [diskussions forumet](https://feedback.azure.com/forums/34192--general-feedback) och publicerar dina frågor. När en fråga ofta är tillfrågad, lägger vi till den i den här artikeln så att den snabbt och enkelt kan hittas.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Kan jag publicera till en befintlig arbets yta?
Om de virtuella datorerna redan är anslutna till en Log Analytics arbets yta kan du fortsätta att använda arbets ytan vid inregistrering till Azure Monitor for VMs, förutsatt att den är i någon av de regioner som [stöds.](vminsights-enable-overview.md#prerequisites)

Vid onboarding konfigurerar vi prestanda räknare för arbets ytan som gör att alla de virtuella datorerna rapporterar data till arbets ytan för att börja samla in den här informationen för visning och analys i Azure Monitor for VMs.  Därför visas prestanda data från alla virtuella datorer som är anslutna till den valda arbets ytan.  Funktionerna för hälso tillstånd och kartor är bara aktiverade för de virtuella datorer som du har angett att publicera.

Mer information om vilka prestanda räknare som aktive ras finns i artikeln [Aktivera översikt](vminsights-enable-overview.md#performance-counters-enabled) .

## <a name="can-i-onboard-to-a-new-workspace"></a>Kan jag publicera till en ny arbets yta? 
Om dina virtuella datorer inte är anslutna till en befintlig Log Analytics arbets yta måste du skapa en ny arbets yta för att lagra dina data. Att skapa en ny standard arbets yta görs automatiskt om du konfigurerar en enskild virtuell Azure-dator för Azure Monitor for VMs via Azure Portal.

Om du väljer att använda den skriptbaserade metoden beskrivs de här stegen i artikeln [aktivera Azure Monitor for VMS (för hands version) med hjälp av Azure PowerShell eller Resource Manager-mall](vminsights-enable-at-scale-powershell.md) . 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Vad gör jag om min virtuella dator redan rapporterar till en befintlig arbets yta?
Om du redan samlar in data från dina virtuella datorer kanske du redan har konfigurerat den för att rapportera data till en befintlig Log Analytics-arbetsyta.  Så länge arbets ytan finns i en av våra regioner som stöds, kan du aktivera Azure Monitor for VMs till den befintliga arbets ytan.  Om den arbets yta som du redan använder inte finns i någon av våra regioner som stöds, kan du inte publicera till Azure Monitor for VMs för tillfället.  Vi arbetar aktivt med att stödja ytterligare regioner.

>[!NOTE]
>Vi konfigurerar prestanda räknare för arbets ytan som påverkar alla virtuella datorer som rapporterar till arbets ytan, oavsett om du har valt att publicera dem på Azure Monitor for VMs. Mer information om hur prestanda räknare konfigureras för arbets ytan finns i vår [dokumentation](../../azure-monitor/platform/data-sources-performance-counters.md). Information om de räknare som har kon figurer ATS för Azure Monitor for VMs finns i artikeln [aktivera Azure Monitor for VMS](vminsights-enable-overview.md#performance-counters-enabled) .  

## <a name="why-did-my-vm-fail-to-onboard"></a>Varför gick det inte att publicera den virtuella datorn?
När du registrerar en virtuell Azure-dator från Azure Portal inträffar följande steg:

* En standard arbets yta Log Analytics skapas, om alternativet har valts.
* Prestanda räknarna konfigureras för den valda arbets ytan. Om det här steget Miss lyckas ser du att några av prestanda diagrammen och tabeller inte visar data för den virtuella datorn som du har registrerat. Du kan åtgärda detta genom att köra PowerShell-skriptet som dokumenteras [här](vminsights-enable-at-scale-powershell.md#enable-performance-counters).
* Log Analytics Agent installeras på virtuella Azure-datorer med ett VM-tillägg, om det är klart krävs det.  
* Azure Monitor for VMs mappnings beroende Agent installeras på virtuella Azure-datorer med ett tillägg, om det är bestämt krävs det.  
* Azure Monitor-komponenter som stöder hälsofunktionen konfigureras vid behov, och den virtuella datorn är konfigurerad för att rapportera hälso data.

Under onboard-processen kontrollerar vi status för var och en av ovanstående för att returnera en meddelande status till dig i portalen. Konfigurationen av arbets ytan och Agent installationen tar vanligt vis 5 till 10 minuter. Det tar ytterligare 5 till 10 minuter att Visa övervaknings-och hälso data i portalen.  

Om du har initierat onboarding och ser meddelanden som anger att den virtuella datorn måste registreras, kan du göra upp till 30 minuter för att den virtuella datorn ska kunna slutföra processen. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Jag har bara aktiverat Azure Monitor for VMs, varför ser jag alla mina virtuella datorer som övervakas av hälso funktionen?
Hälso funktionen är aktive rad för alla virtuella datorer som är anslutna till Log Analytics arbets ytan, även när åtgärden initieras för en enskild virtuell dator.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Kan jag ändra schemat för när hälso villkoren utvärderar ett villkor?
Nej, tids perioden och frekvensen för hälso villkor kan inte ändras i den här versionen. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Kan jag inaktivera hälso kriterier för ett villkor som jag inte behöver övervaka?
Hälso kriterier kan inte inaktive ras i den här versionen.

## <a name="are-the-health-alert-severities-configurable"></a>Kan hälso varnings allvarlighets grader konfigureras?  
Det går inte att ändra allvarlighets grad för hälso avisering, de kan bara aktive ras eller inaktive ras. Dessutom kan vissa varnings allvarlighets grader uppdateras baserat på hälso tillståndet. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Om jag konfigurerar om inställningarna för ett visst hälso villkor, kan de begränsas till en viss instans?  
Om du ändrar någon inställning för en hälso tillstånds instans ändras alla hälso villkors instanser av samma typ på den virtuella Azure-datorn. Till exempel, om tröskelvärdet för det lediga disk utrymme som motsvarar den logiska disken C: ändras, gäller detta tröskelvärde för alla andra logiska diskar som identifieras och övervakas för samma virtuella dator.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Övervakar hälso funktionen logiska processorer och kärnor?
Nej, enskilda processor-och logiska processor nivå hälso kriterier ingår inte i ett Windows, endast den totala processor användningen övervakas som standard för att effektivt utvärdera processor belastningen baserat på det totala antalet logiska processorer som är tillgängliga för den virtuella Azure-datorn. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Kan alla tröskelvärden för hälso kriterier konfigureras?  
Tröskelvärden för hälso villkor som riktar sig mot en virtuell Windows-dator kan inte ändras eftersom deras hälso tillstånd är inställda på att *köras* eller *är tillgängliga*. När du frågar hälso tillståndet från API: [t för arbets belastnings övervakning](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)visas *comparisonOperator* -värdet för **LessThan** eller **GreaterThan** med *tröskelvärdet* **4** för tjänsten eller entiteten om:
   - DNS-klient Service Health – tjänsten körs inte. 
   - Tjänsten DHCP client service Health – tjänsten körs inte. 
   - RPC-Service Health – tjänsten körs inte. 
   - Tjänsten Windows Firewall service Health – tjänsten körs inte.
   - Tjänsten Windows händelse logg tjänst hälsa – tjänsten körs inte. 
   - Server tjänstens hälso tillstånd – tjänsten körs inte. 
   - Hälso tillstånd för Windows Remote Management Service – tjänsten körs inte. 
   - Fel eller fel i fil systemet – den logiska disken är inte tillgänglig.

Tröskelvärden för följande Linux-hälsovillkor kan inte ändras eftersom deras hälso tillstånd redan har angetts till *True*. Hälso tillståndet visar *comparisonOperator* med värdet **LessThan** och *tröskelvärdet* **1** när det efter frågas från API: et för arbets belastnings övervakning för entiteten, beroende på dess kontext:
   - Status för logisk disk – den logiska disken är inte online/tillgänglig
   - Disk status – disken är inte online/tillgänglig
   - Status för nätverkskort-nätverkskort har inaktiverats

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hur gör jag för att ändra aviseringar som ingår i hälso funktionen?
Aviserings regler som definieras för varje hälso kriterium visas inte i Azure Portal. Du kan endast aktivera eller inaktivera en hälso aviserings regel i [API: t för arbets belastnings övervakning](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Du kan inte heller tilldela en [Azure Monitor åtgärds grupp](../../azure-monitor/platform/action-groups.md) för hälso aviseringar i Azure Portal. Du kan bara använda API: et för aviserings inställningar för att konfigurera en åtgärds grupp som ska utlösas när en hälso varning utlöses. För närvarande kan du tilldela åtgärds grupper mot en virtuell dator så att alla *hälso aviseringar* som utlöses mot den virtuella datorn utlöser samma åtgärds grupper. Till skillnad från traditionella Azure-aviseringar finns det inget begrepp för en separat åtgärds grupp för varje hälso varnings regel. Dessutom stöds endast åtgärds grupper som är konfigurerade för att tillhandahålla e-post eller SMS-meddelanden när hälso varningar utlöses. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Jag ser inte några eller några data i prestanda diagrammen för min virtuella dator
Om du inte ser prestanda data i disk tabellen eller i vissa prestanda diagram, kanske prestanda räknarna inte konfigureras på arbets ytan. Kör följande [PowerShell-skript](vminsights-enable-at-scale-powershell.md#enable-with-powershell)för att lösa problemet.

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hur skiljer sig Azure Monitor for VMs Map-funktionen från Tjänstkarta?
Azure Monitor for VMs Map-funktionen baseras på Tjänstkarta, men har följande skillnader:

* Du kan komma åt kart visningen från bladet VM och från Azure Monitor for VMs under Azure Monitor.
* Anslutningarna på kartan är nu klickbara och visar en vy över anslutnings måttets data på sido panelen för den valda anslutningen.
* Det finns ett nytt API som används för att skapa Maps för att bättre stödja mer komplexa kartor.
* Övervakade virtuella datorer ingår nu i noden klient grupp och ring diagrammet visar förhållandet mellan övervakade och oövervakade virtuella datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Övervakade virtuella datorer ingår nu i noderna i server port gruppen och ring diagrammet visar förhållandet mellan övervakade och oövervakade datorer i gruppen.  Den kan också användas för att filtrera listan över datorer när gruppen expanderas.
* Kart formatet har uppdaterats så att det blir mer konsekvent med app Map från Application Insights.
* Sido panelerna har uppdaterats och har inte den fullständiga uppsättningen integreringar som stöds i Tjänstkarta-Uppdateringshantering, Ändringsspårning, säkerhet och Service Desk. 
* Alternativet för att välja grupper och datorer som ska mappas har uppdaterats och stöder nu prenumerationer, resurs grupper, skalnings uppsättningar för virtuella Azure-datorer och moln tjänster.
* Du kan inte skapa nya Tjänstkarta dator grupper i Azure Monitor for VMs Map-funktionen.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Varför visar mina prestanda diagram prickade linjer?
Detta kan inträffa av några skäl.  I de fall där det finns en lucka i data insamlingen visas linjerna som prickade.  Om du har ändrat data samplings frekvensen för aktiverade prestanda räknare (Standardinställningen är att samla in data var 60 sekund), kan du se prickade linjer i diagrammet om du väljer ett smalt tidsintervall för diagrammet och samplings frekvensen är mindre än den Bucket-storlek som används i diagrammet (till exempel samplings frekvensen är var tionde minut och varje Bucket i diagrammet är 5 minuter).  Om du väljer ett bredare tidsintervall som ska visas bör det leda till att diagram linjerna visas som heldragna linjer i stället för punkter i det här fallet.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Stöds grupper med Azure Monitor for VMs?
Ja, när du har installerat beroende agenten samlar vi in information från de virtuella datorerna för att Visa grupper baserat på prenumeration, resurs grupp, skalnings uppsättningar för virtuella datorer och moln tjänster.  Om du har använt Tjänstkarta och skapat dator grupper visas dessa även.  Dator grupper visas också i filtret grupper om du har skapat dem för den arbets yta som du visar. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hur gör jag för att se information om vad som driver den 95 percentils linjen i de sammanställda prestanda diagrammen?
Som standard sorteras listan för att visa de virtuella datorer som har det högsta värdet för 95-percentilen för det valda måttet, förutom det tillgängliga minnes diagrammet, som visar datorerna med det lägsta värdet för den femte percentilen.  Om du klickar på diagrammet öppnas den **översta N-listvyn** med lämpligt mått valt.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hur hanterar kart funktionen dubbla IP-adresser över olika virtuella nätverk och undernät?
Om du duplicerar IP-intervall med virtuella datorer eller skalnings uppsättningar för virtuella Azure-datorer över undernät och virtuella nätverk kan det orsaka att Azure Monitor for VMs karta visar felaktig information. Detta är ett känt problem och vi undersöker alternativ för att förbättra den här upplevelsen.

## <a name="does-map-feature-support-ipv6"></a>Stöder kart funktionen IPv6?
Kart funktionen stöder för närvarande endast IPv4 och vi undersöker stödet för IPv6. Vi stöder också IPv4 som är tunnlad i IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>När jag läser in en karta för en resurs grupp eller en annan stor grupp är det svårt att visa kartan
Vi har gjort förbättringar i kartan för att hantera stora och komplexa konfigurationer, men vi inser att en karta kan ha många noder, anslutningar och noder som fungerar som ett kluster.  Vi strävar efter att fortsätta att förbättra supporten för att öka skalbarheten.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Varför ser nätverks diagrammet på fliken prestanda annorlunda ut än nätverks diagrammet på översikts sidan för Azure VM?

Översikts sidan för en virtuell Azure-dator visar diagram baserat på värdens mätning av aktivitet på den virtuella gäst datorn.  För nätverks diagrammet på Azure VM-översikten visar det bara nätverks trafik som kommer att faktureras.  Detta omfattar inte trafik mellan virtuella nätverk.  Data och diagram som visas för Azure Monitor for VMs baseras på data från den virtuella gäst datorn och nätverks diagrammet visar all TCP/IP-trafik som är inkommande och utgående till den virtuella datorn, inklusive Inter-VNET.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hur mäts svars tiden för data som lagras i VMConnection och visas i anslutnings panelen och arbets böcker?

Svars tiden är en uppskattning. Eftersom vi inte instrumenterar koden för programmet, vet vi egentligen inte när en begäran börjar och när svaret kommer. I stället noterar vi data som skickas på en anslutning och sedan data som kommer tillbaka på den anslutningen. Vår agent håller koll på dessa skickar och tar emot och försöker para ihop dem: en sekvens med sändningar, följt av en sekvens med mottagna, tolkas som ett begär ande/svar-par. Tiden mellan dessa åtgärder är svars tiden. Den omfattar nätverks fördröjningen och Server bearbetnings tiden.

Den här uppskattningen fungerar bra för protokoll som är Request/Response-baserade: en enskild begäran skickas över anslutningen och ett enda svar tas emot. Detta är fallet för HTTP (S) (utan pipelinering), men inte uppfyllt för andra protokoll.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Är deras begränsningar om jag är på Log Analytics kostnads fri pris plan?
Om du har konfigurerat Azure Monitor med en Log Analytics arbets yta som använder den *kostnads fria* pris nivån, kommer Azure Monitor for VMS Map-funktionen bara att ha stöd för fem anslutna datorer som är anslutna till arbets ytan. Om du har fem virtuella datorer som är anslutna till en kostnads fri arbets yta, kopplar du från en av de virtuella datorerna och senare ansluter en ny virtuell dator. den nya virtuella datorn övervakas inte och visas inte på kart sidan.  

Under det här tillståndet uppmanas du att välja alternativet **prova nu** när du öppnar den virtuella datorn och väljer **insikter (för hands version)** i det vänstra fönstret, även efter att det redan har installerats på den virtuella datorn.  Du uppmanas dock inte att ange alternativ som normalt skulle inträffa om den virtuella datorn inte har publicerats till Azure Monitor for VMs. 

## <a name="next-steps"></a>Nästa steg
Läs [aktivera Azure Monitor for VMS](vminsights-enable-overview.md) för att förstå krav och metoder för att aktivera övervakning av dina virtuella datorer.
