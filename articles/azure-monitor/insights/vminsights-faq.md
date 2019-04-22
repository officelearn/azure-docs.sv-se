---
title: Azure Monitor för virtuella datorer (förhandsversion) vanliga frågor och svar | Microsoft Docs
description: Azure Monitor för virtuella datorer är en lösning i Azure som kombinerar hälsotillstånd och prestanda för övervakning av Azure VM-operativsystem, samt automatiskt identifierar programkomponenter och beroenden till andra resurser och mappar kommunikationen mellan dem. Den här artikeln innehåller vanliga frågor och svar.
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 01/09/2018
ms.author: magoedte
ms.openlocfilehash: ade12225a470b64278b9d27676ceab768f64d904
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59698288"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Azure Monitor för virtuella datorer (förhandsversion) vanliga frågor och svar
Den här Microsoft-FAQ är en lista över vanliga frågor om Azure Monitor för virtuella datorer. Om du har ytterligare frågor om lösningen går du till den [diskussionsforum](https://feedback.azure.com/forums/34192--general-feedback) och ställa frågor. När en fråga är vanliga vi lägga till det i den här artikeln så att den finns snabbt och enkelt.

## <a name="can-i-onboard-to-an-existing-workspace"></a>Kan jag lägga till en befintlig arbetsyta?
Om dina virtuella datorer är redan ansluten till en Log Analytics-arbetsyta, du kan fortsätta att använda arbetsytan när Kom igång med Azure Monitor för virtuella datorer, under förutsättning att den är i något av de regioner som stöds visas [här](vminsights-onboard.md#prerequisites).

När onboarding, vi konfigurerar prestandaräknare för den arbetsyta som gör att alla virtuella datorer rapporterar data till arbetsytan för att börja samla in den här informationen för visning och analys i Azure Monitor för virtuella datorer.  Därför visas data från alla de virtuella datorerna är anslutna till den valda arbetsytan.  Hälsa och kartan funktioner aktiveras endast för de virtuella datorerna som du har angett att komma igång.

Mer information på vilka räknare aktiveras finns i vår [onboarding](vminsights-onboard.md) artikeln.

## <a name="can-i-onboard-to-a-new-workspace"></a>Kan jag registrera en ny arbetsyta? 
Om dina virtuella datorer inte är anslutna till en befintlig Log Analytics-arbetsyta, måste du skapa en ny arbetsyta för att lagra dina data.  Skapa en ny standardarbetsyta görs automatiskt om du konfigurerar en virtuell Azure-dator för Azure Monitor för virtuella datorer via Azure portal.

Om du väljer att använda metoden skriptbaserade stegen beskrivs i den [onboarding](vminsights-onboard.md) artikeln. 

## <a name="what-do-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Vad gör jag om min virtuella dator redan rapporterar till en befintlig arbetsyta?
Om du redan samlar in data från dina virtuella datorer, kan du redan ha konfigurerat det till rapportdata till en befintlig Log Analytics-arbetsyta.  Så länge arbetsytan är i något av våra regioner som stöds, kan du aktivera Azure Monitor för virtuella datorer till den befintliga arbetsytan.  Om arbetsytan som du redan använder inte är i något av våra regioner som stöds måste du inte att publicera till Azure Monitor för virtuella datorer just nu.  Vi arbetar aktivt för att stödja fler regioner.

>[!NOTE]
>Vi konfigurerar prestandaräknare för den arbetsyta som påverkar alla virtuella datorer som rapporterar till arbetsytan, oavsett om du har valt att publicera dem till Azure Monitor för virtuella datorer. Mer information om hur prestandaräknare är konfigurerade för arbetsytan finns i vår [dokumentation](../../azure-monitor/platform/data-sources-performance-counters.md). Information om de räknare som konfigurerats för Azure Monitor för virtuella datorer finns i vår [dokumentation om](vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Varför min virtuella dator kunde inte publicera?
När onboarding en Azure-dator från Azure-portalen, genomförs följande steg:

* Standard Log Analytics-arbetsytan skapas, om som har valts.
* Prestandaräknarna har konfigurerats för den valda arbetsytan. Om det här steget misslyckas kan du se att inte några av de prestandadiagram och tabeller som visar data för den virtuella datorn du publicerat. Du kan åtgärda detta genom att köra PowerShell-skriptet som beskrivs [här](vminsights-onboard.md#enable-with-powershell).
* Log Analytics-agenten installeras på virtuella Azure-datorer med hjälp av en VM-tillägg, om det krävs.  
* I Azure Monitor för virtuella datorer kartan beroendeagenten installeras på virtuella Azure-datorer med hjälp av ett tillägg, om det krävs.  
* Azure Monitor-komponenter som stöder funktionen hälsotillstånd har konfigurerats, om det behövs och den virtuella datorn är konfigurerad för att rapportera hälsa data.

Under processen publicera Kontrollera vi status för var och en av ovan för att returnera statusen meddelande till dig i portalen. Konfiguration av arbetsytan och agentinstallationen brukar ta 5 – 10 minuter. Visa övervakning och hälsorapporter data i portalen ta en ytterligare 5 – 10 minuter.  

Om du har initierat onboarding och se meddelanden som indikerar den virtuella datorn behöver som ska publiceras, kan du i upp till 30 minuter för den virtuella datorn ska slutföras. 

## <a name="i-only-enabled-azure-monitor-for-vms-why-do-i-see-all-my-vms-monitored-by-the-health-feature"></a>Jag kan bara aktiveras Azure Monitor för virtuella datorer, varför ser jag mina datorer som övervakas av funktionen Health?
Health-funktionen är aktiverad för alla virtuella datorer som är anslutna till Log Analytics-arbetsytan, även när den startas för en enskild virtuell dator.

## <a name="can-i-modify-the-schedule-for-when-health-criteria-evaluates-a-condition"></a>Kan jag ändra schemat för när Hälsokriterier utvärderar ett villkor?
Nej, den tidsperiod och frekvensen för health-villkor kan inte ändras i den här versionen. 

## <a name="can-i-disable-health-criteria-for-a-condition-i-dont-need-to-monitor"></a>Kan jag inaktivera health-villkor för ett villkor som du inte behöver övervaka?
Health-villkor kan inte inaktiveras i den här versionen.

## <a name="are-the-health-alert-severities-configurable"></a>Är hälsotillståndet avisering allvarlighetsgrader konfigurerbara?  
Allvarlighetsgrad för hälsotillstånd kan inte ändras, de kan endast aktiveras eller inaktiveras. Dessutom kan utifrån vissa avisering allvarlighetsgrader Uppdatera tillståndet för health-villkor. 

## <a name="if-i-reconfigure-the-settings-of-a-particular-health-criteria-can-it-be-scoped-to-a-specific-instance"></a>Om jag konfigurera om inställningarna för en viss health-villkor kan det vara begränsad till en specifik instans?  
Om du ändrar inställningar för en instans för health kriterium ändras alla hälsotillstånd kriterier instanser av samma typ på den virtuella Azure-datorn. Till exempel om tröskelvärdet på disk ledigt utrymme hälsotillstånd kriterium instansen för logisk disk C: ändras, gäller detta tröskelvärde alla logiska diskar som identifieras och övervakas för samma virtuella dator.

## <a name="does-the-health-feature-monitor-logical-processors-and-cores"></a>Övervakar hälsotillståndet funktionen logiska processorer och kärnor?
Nej, enskilda processor och logisk processor på health-villkor inte är inkluderad i en Windows, endast Total processoranvändning övervakas som standard att effektivt utvärdera CPU-belastning baserat på det totala antalet logiska processorer som är tillgängliga Azure-datorn. 

## <a name="are-all-health-criteria-thresholds-configurable"></a>Är alla hälsotillstånd kriterier tröskelvärden konfigurerbara?  
Tröskelvärden för health-villkor som är riktade till en virtuell Windows-dator inte kan ändras, eftersom deras hälsotillstånd är inställda på *kör* eller *tillgängliga*. När du frågar hälsotillståndet från den [arbetsbelastning övervakaren API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components), visas den *jämförelseoperator* värdet för **LessThan** eller **GreaterThan** med en *tröskelvärdet* värdet för **4** för tjänsten eller entiteten om:
   - Tjänstehälsa för DNS-klienten – tjänst inte körs. 
   - Tjänstehälsa för DHCP-klient – tjänst inte körs. 
   - Tjänstehälsa för RPC-tjänsten inte körs. 
   - Tjänstehälsa för Windows-brandväggen – tjänst inte körs.
   - Tjänstehälsa för Windows-händelseloggen – tjänst inte körs. 
   - Tjänstehälsa för Server – tjänst inte körs. 
   - Windows remote management service health – tjänsten körs inte. 
   - Filsystemfel eller skadade data – logisk Disk är inte tillgänglig.

Tröskelvärden för följande kriterier för Linux-hälsotillstånd inte kan ändras, eftersom deras hälsotillstånd har redan angivits för *SANT*. Hälsotillståndet visar den *jämförelseoperator* med ett värde **LessThan** och *tröskelvärdet* värdet för **1** när en förfrågan från den Arbetsbelastningen övervaknings-API för entiteten, beroende på dess kontext:
   - Diskstatus för logisk – logisk disk är inte online / tillgängliga
   - Diskstatus – Disk är inte online / tillgängliga
   - Nätverkskortets Status - nätverkskort är inaktiverad

## <a name="how-do-i-modify-alerts-that-are-included-with-the-health-feature"></a>Hur ändrar jag aviseringar som ingår i funktionen Health?
Varningsregler som har definierats för varje hälsotillstånd kriterium visas inte i Azure-portalen. Du kan aktivera eller inaktivera en hälsovarning regel bara i den [arbetsbelastning övervakaren API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components). Du kan också tilldela en [Azure Monitor åtgärdsgrupp](../../azure-monitor/platform/action-groups.md) för health-aviseringar i Azure-portalen. Du kan bara använda API: et för meddelande inställningen för att konfigurera en åtgärdsgrupp som utlöses när en hälsovarning aktiveras. För närvarande kan du tilldela åtgärdsgrupper mot en virtuell dator så att alla *hälsovarningar* utlösta mot VM-utlösaren samma åtgärdsgrupper. Till skillnad från traditionella Azure-aviseringar finns det ingen i en separat åtgärdsgrupp för varje health-aviseringsregel. Dessutom stöds endast åtgärdsgrupper som är konfigurerade för att tillhandahålla e-post eller SMS-meddelanden när hälsovarningar utlöses. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Jag ser inte vissa eller alla data i prestandadiagram för den virtuella datorn
Om du inte ser prestandadata i tabellen disk eller en del av prestandadiagram kan dina prestandaräknare inte konfigureras på arbetsytan. För att lösa, kör du följande [PowerShell-skript](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hur skiljer sig Azure Monitor för virtuella datorer kartan funktion från Service Map?
I Azure Monitor för virtuella datorer kartan funktionen är baserad på Tjänstkartan, men har följande skillnader:

* Kartvyn kan nås från VM-bladet och från Azure Monitor för virtuella datorer under Azure Monitor.
* Anslutningar på kartan är nu klickbara och visa en vy över anslutningen måttdata på panelen på klientsidan för den valda anslutningen.
* Det finns ett nytt API som används för att skapa mer komplexa kartor för maps att ge bättre stöd.
* Övervakade virtuella datorer ingår nu i noden klienten grupp och ringdiagrammet visar andelen av övervakade vs oövervakade virtuella datorer i gruppen.  Det kan också användas för att filtrera listan med datorer när gruppen har expanderats.
* Övervakade virtuella datorer ingår nu i servernoder port grupp och ringdiagrammet visar andelen av övervakade vs oövervakade datorer i gruppen.  Det kan också användas för att filtrera listan med datorer när gruppen har expanderats.
* Formatmallen kartan har uppdaterats för att vara mer konsekvent med Programkartan från Application insights.
* Panelerna på klientsidan har uppdaterats och har inte den fullständiga uppsättningen integration's som hade stöd i Tjänstkarta - uppdateringshantering, ändringsspårning, säkerhet och support. 
* Alternativet för att välja grupper och datorer för att mappa har uppdaterats och nu har stöd för prenumerationer, resursgrupper, skalningsuppsättningar för virtuella Azure-datorer och molntjänster.
* Du kan inte skapa nya Service Map datorgrupper i Azure Monitor för virtuella datorer kartan funktion.  

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Varför visar min prestandadiagram kantlinjerna?
Detta kan inträffa olika orsaker.  I fall där det finns en lucka i datainsamling vi föreställer raderna som prickad.  Om du har ändrat data samplingsfrekvensen för prestandaräknare som är aktiverad (standardinställningen är att samla in data var 60: e sekund), du kan se prickade linjer i diagrammet om du väljer en smal tidsintervall för diagrammet och din samplingsfrekvensen är mindre än bucket-storleken som används i diagrammet (till exempel samplingsfrekvens är var tionde minut och varje bucket i diagrammet är 5 minuter).  Raderna som diagrammet ska visas som heldragna linjer i stället för punkterna i det här fallet ska göra att välja fler tid att visa.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Grupper stöds med Azure Monitor för virtuella datorer?
Ja, när du installerar beroendeagenten som vi samlar in information från de virtuella datorerna ska visa grupper baserat på prenumerationen, resursgruppen, VM-skalningsuppsättningar och molntjänster.  Om du har använt Tjänstkarta och har skapat datorgrupper, visas dessa också.  Datorgrupper visas också i filtret grupper om du har skapat dem för den arbetsyta som du visar. 

## <a name="how-do-i-see-the-details-for-what-is-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hur kan jag se information för vad som aktiverar den 95: e percentilen linjediagram i samlingen prestanda?
Listan sorteras som standard för att visa de virtuella datorer som har det högsta värdet för den 95: e percentilen för det valda måttet förutom tillgängligt minne i diagrammet, som visar datorer med det lägsta värdet för 5: e percentilen.  Klicka på diagrammet öppnas den **topp-N** vy med den lämpliga mått har valts.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-different-vnets-and-subnets"></a>Hur funktionen kartan hanterar dubbla IP-adresser i olika virtuella nätverk och undernät?
Om du duplicerar IP-intervall med virtuella datorer eller virtuella Azure-datorn antingen skalningsuppsättningar över undernät och virtuella nätverk, kan det orsaka Azure Monitor för virtuella datorer för att visa felaktig information. Detta är ett känt problem och vi undersöker alternativ för att förbättra den här upplevelsen.

## <a name="does-map-feature-support-ipv6"></a>Mappar funktionsstöd IPv6?
Kartan funktionen stöder för närvarande endast IPv4 och vi undersöker stöd för IPv6. Vi har också stöd för IPv4 tunneldata i IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-the-map-is-difficult-to-view"></a>När jag läser in en karta för en resursgrupp eller annan stor grupp är kartan svårt att visa
När vi har gjort förbättringar kartan för att hantera stora och komplexa konfigurationer kan förstår vi en karta kan har ett stort antal noder, anslutningar och noden som fungerar som ett kluster.  Vi är måna om du fortsätter att förbättra stöd för att öka skalbarheten.   

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-than-the-network-chart-on-the-azure-vm-overview-page"></a>Varför ser nätverk diagrammet på fliken prestanda skiljer sig från nätverket diagrammet på översiktssidan för Azure VM?

Översiktssidan för en Azure-dator visar diagram baserat på värdens mätning av aktiviteten i den Virtuella gästdatorn.  För nätverk diagrammet på Azure VM-översikt visas bara trafik som kommer att debiteras.  Detta inkluderar inte inter-vnet-trafik.  Data och diagram som visas för Azure Monitor för virtuella datorer är baserade på data från den Virtuella gästdatorn och nätverk-diagrammet visar alla TCP/IP-trafik som är inkommande och utgående trafik till den virtuella datorn, inklusive inter-vnet.

## <a name="how-is-response-time-measured-for-data-stored-in-vmconnection-and-displayed-in-the-connection-panel-and-workbooks"></a>Hur mäts svarstiden för data som lagras i VMConnection och visas på panelen för anslutning och arbetsböcker?

Svarstiden är ett approximativt värde. Eftersom vi inte instrumenterar koden i programmet, vet vi inte riktigt när en begäran börjar och när svaret tas emot. Vi observerar istället data skickas på en anslutning och sedan data som skickas tillbaka för anslutningen. Vår agent håller reda på dessa skickar och tar emot och försöker att para ihop dem: en sekvens av skickar, följt av en serie tar emot tolkas som ett begäran/svar-par. Tiden mellan dessa åtgärder är svarstiden. Den innehåller svarstiden i nätverk och bearbetningstid för servern.

Den här uppskattning fungerar bra för protokoll som är begäran/svar-baserade: en enskild begäran går för anslutningen och ett enda svar tas emot. Detta är fallet för HTTP (S) (utan pipelining), men inte uppfyllda för andra protokoll.

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Har sina begränsningar om jag på Log Analytics kostnadsfria prisplanen?
Om du har konfigurerat Azure Monitor med en Log Analytics-arbetsytan med den *kostnadsfri* prisnivå, Azure Monitor för virtuella datorer kartan funktionen stöder bara fem anslutna datorer är anslutna till arbetsytan. Om du har fem virtuella datorer är anslutna till en kostnadsfri arbetsyta kan du koppla från en av de virtuella datorerna och sedan ansluta en ny virtuell dator, den nya virtuella datorn inte övervakas och visas på sidan kartan.  

Under det här tillståndet uppmanas du att med den **Testa nu** när du öppnar den virtuella datorn och välj **Insights (förhandsversion)** i den vänstra rutan, även när den har redan installerats på den virtuella datorn.  Men ange du inte med alternativ som normalt skulle inträffa om den här virtuella datorn inte har integrerats i Azure Monitor för virtuella datorer. 

## <a name="next-steps"></a>Nästa steg
Granska [publicera Azure Monitor för virtuella datorer](vminsights-onboard.md) att förstå kraven och metoder för att övervaka dina virtuella datorer.
