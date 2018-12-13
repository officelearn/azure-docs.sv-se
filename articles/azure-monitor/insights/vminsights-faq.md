---
title: Vanliga frågor och svar om Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Azure Monitor för virtuella datorer (förhandsversion) är en lösning i Azure som kombinerar hälso- och prestandaövervakning av Azure VM-operativsystem. Den automatiskt identifierar programkomponenter och beroenden till andra resurser och mappar kommunikationen mellan dem. Den här artikeln innehåller vanliga frågor och svar.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: a97a7be0eaa8438a4df27b610106ec6ab9f60d30
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53184400"
---
# <a name="azure-monitor-for-vms-preview-faq"></a>Azure Monitor för virtuella datorer (förhandsversion) vanliga frågor och svar
Den här artikeln innehåller vanliga frågor och svar om Azure Monitor för virtuella datorer. Om du har ytterligare frågor om lösningen går du till den [Azure-Diskussionsforumet](https://feedback.azure.com/forums/34192--general-feedback) och ställa frågor. När frågor tillfrågas ofta vi lägga till dem i den här artikeln så att de kan hittas snabbt och enkelt.

## <a name="can-i-deploy-vms-to-an-existing-workspace"></a>Kan jag distribuera virtuella datorer till en befintlig arbetsyta?
Om dina virtuella datorer är redan ansluten till en Log Analytics-arbetsyta, kan du fortsätta att använda arbetsytan när du distribuerar dem till Azure Monitor för virtuella datorer. Arbetsytan måste finnas i något av de regioner som stöds anges i avsnittet ”krav” i [distribuera Azure Monitor för virtuella datorer (förhandsversion)](vminsights-onboard.md#prerequisites).

Vi konfigurerar prestandaräknare för arbetsytan under distributionen. Den här åtgärden gör de virtuella datorerna som visar rapportdata till arbetsytan för att börja samla in information för och analys i Azure Monitor för virtuella datorer. Därför visas data från alla virtuella datorer som är anslutna till den valda arbetsytan. Hälsa och kartan funktioner aktiveras endast för de virtuella datorerna som du har angett för distribution.

Läs mer om vilka räknare är aktiverade, [distribuera Azure Monitor för virtuella datorer (förhandsversion)](vminsights-onboard.md).

## <a name="can-i-deploy-vms-to-a-new-workspace"></a>Kan jag distribuera virtuella datorer till en ny arbetsyta? 
Om dina virtuella datorer är inte ansluten till en befintlig Log Analytics-arbetsyta som du behöver skapa en ny arbetsyta för att lagra dina data. Du kan skapa en automatiskt genom att konfigurera en enskild virtuell dator för Azure Monitor för virtuella datorer i Azure-portalen.

Om du väljer att använda metoden skriptbaserade [distribuera Azure Monitor för virtuella datorer (förhandsversion)](vminsights-onboard.md). 

## <a name="what-can-i-do-if-my-vm-is-already-reporting-to-an-existing-workspace"></a>Vad kan jag göra om min virtuella dator redan rapporterar till en befintlig arbetsyta?
Om du redan samlar in data från dina virtuella datorer, kanske du redan har konfigurerat dem till rapportdata till en befintlig Log Analytics-arbetsyta. Så länge arbetsytan är i något av våra regioner som stöds, kan du aktivera Azure Monitor för virtuella datorer för den befintliga arbetsytan. Vi arbetar aktivt för att stödja fler regioner.

>[!NOTE]
>Vi konfigurerar prestandaräknare för den arbetsyta som påverkar alla virtuella datorer som rapporterar till arbetsytan, oavsett om du har valt att distribuera dem till Azure Monitor för virtuella datorer. Mer information om hur prestandaräknare är konfigurerade för arbetsytan finns i avsnittet ”Konfigurera prestandaräknare” i [Windows och Linux prestanda datakällor i Log Analytics](../../azure-monitor/platform/data-sources-performance-counters.md). Information om de räknare som konfigurerats för Azure Monitor för virtuella datorer finns i [distribuera Azure Monitor för virtuella datorer (förhandsversion)](vminsights-onboard.md). 

## <a name="why-did-my-vm-deployment-fail"></a>Varför Mina VM-distributionen misslyckas?
När du distribuerar en virtuell Azure-dator från Azure-portalen, händer följande:

* Standard Log Analytics-arbetsytan skapas, om som har valts.
* Prestandaräknare är konfigurerade för den valda arbetsytan. Om det här steget misslyckas visar inte några av de prestandadiagram och tabeller data för den virtuella datorn som du har distribuerat. Du kan åtgärda det här problemet genom att köra PowerShell-skriptet som beskrivs i avsnittet ”Aktivera med PowerShell” i [distribuera Azure Monitor för virtuella datorer (förhandsversion)](vminsights-onboard.md#enable-with-powershell).
* Log Analytics-agenten är installerad på virtuella Azure-datorer med en VM-tillägg, om det krävs. 
* I Azure Monitor för virtuella datorer kartan beroendeagenten installeras på virtuella Azure-datorer med ett tillägg, om det krävs. 
* Azure Monitor-komponenter som stöder funktionen hälsotillstånd har konfigurerats, om det behövs och den virtuella datorn är konfigurerad för att rapportera hälsa data.

Under distributionen måste vi Kontrollera status för var och en av de föregående stegen och returnera statusen meddelande till dig i portalen. Konfiguration av arbetsytan och agentinstallationen brukar ta 5 – 10 minuter. Visa övervakning och hälsorapporter data i Azure-portalen ta en ytterligare 5 – 10 minuter. 

Om du har initierat distributionen och se meddelanden som indikerar att den virtuella datorn måste distribueras, kan du upp till 30 minuter för den virtuella datorn ska slutföras. 

## <a name="i-dont-see-some-or-any-data-in-the-performance-charts-for-my-vm"></a>Jag ser inte vissa eller alla data i prestandadiagram för den virtuella datorn
Om prestandadata inte visas i tabellen disk eller prestandadiagram kan kan dina prestandaräknare konfigureras inte på arbetsytan. Lös problemet genom att köra PowerShell-skriptet som beskrivs i avsnittet ”Aktivera med PowerShell” i [distribuera Azure Monitor för virtuella datorer (förhandsversion)](vminsights-onboard.md#enable-with-powershell).

## <a name="how-is-the-azure-monitor-for-vms-map-feature-different-from-service-map"></a>Hur skiljer sig i Azure Monitor för virtuella datorer kartan funktion från Service Map?
I Azure Monitor för virtuella datorer kartan funktionen är baserad på Tjänstkartan, men den har följande skillnader:

* Du kan komma åt kartvyn på panelen VM och från Azure Monitor för virtuella datorer under Azure Monitor.
* Anslutningar på kartan är nu klickbara och visa måttdata för anslutning på panelen på klientsidan.
* Ett nytt API används för att skapa mer komplexa kartor för maps att ge bättre stöd.
* Övervakade virtuella datorer är nu i gruppnoden klienten och ringen diagrammet visar förhållandet mellan övervakade till oövervakade virtuella datorer. Du kan också filtrera listan med datorer när gruppen har expanderats.
* Övervakade virtuella datorer finns nu i servernoder port grupp och ringdiagrammet visar förhållandet mellan övervakade till oövervakade datorer. Du kan också filtrera listan med datorer när gruppen har expanderats.
* Formatmallen kartan har uppdaterats för att vara mer konsekvent med Programkartan från Azure Application Insights.
* Panelerna på klientsidan har uppdaterats, men ännu har inte en fullständig uppsättning integreringar som hade stöd i Tjänstkarta: Uppdateringshantering, ändringsspårning, säkerhet och support. 
* Alternativet för att välja grupper och datorer för att mappa har uppdaterats. Det stöder nu prenumerationer, resursgrupper, skalningsuppsättningar för virtuella Azure-datorer och molntjänster.
* Du kan inte skapa nya Service Map datorgrupper i Azure Monitor för virtuella datorer kartan funktion. 

## <a name="why-do-my-performance-charts-show-dotted-lines"></a>Varför visar min prestandadiagram kantlinjerna?

Prestandadiagram Visar prickade linjer i stället för solid rader för av flera olika orsaker:
* Det kan finnas en lucka i insamling av data. 

* Standardinställningen för datasampling är var 60: e sekund. Du kan se kantlinjerna om du väljer en smal tidsintervall för diagrammet och din samplingsfrekvensen är mindre än bucket-storleken som används i diagrammet. Anta att du har valt en samplingsfrekvensen på 10 minuter och varje bucket i diagrammet är 5 minuter. I det här fallet bör välja fler tid att visa orsaka raderna som diagrammet ska visas som heldragna linjer i stället för punkter.

## <a name="are-groups-supported-with-azure-monitor-for-vms"></a>Grupper stöds med Azure Monitor för virtuella datorer?
Ja, när du har installerat beroendeagenten som vi samlar in information från de virtuella datorerna ska visa grupper baserat på prenumerationen, resursgruppen, VM-skalningsuppsättningar och molntjänster. Om du har använt Tjänstkarta och har skapat datorgrupper, visas dessa grupper också. Datorgrupper visas också i filtret grupper om du har skapat dem för den arbetsyta som du visar. 

## <a name="how-can-i-display-the-details-about-whats-driving-the-95th-percentile-line-in-the-aggregate-performance-charts"></a>Hur kan jag visa information om vad som aktiverar den 95: e percentilen linjediagram i samlingen prestanda?
Listan sorteras som standard för att visa de virtuella datorer som har det högsta värdet för den 95: e percentilen för det valda måttet. Ett undantag är den **tillgängligt minne** diagram som visar datorer med det lägsta värdet för den femte: e percentilen. Välj diagrammet för att öppna den **topp-N** vy med den lämpliga mått har valts.

## <a name="how-does-the-map-feature-handle-duplicate-ips-across-various-virtual-networks-and-subnets"></a>Hur funktionen kartan hanterar dubbla IP-adresser mellan olika virtuella nätverk och undernät?
Om du duplicera IP-adressintervall med hjälp av antingen virtuella datorer eller Azure VM-skalningsuppsättningar i undernät och virtuella nätverk, kan den orsaka i Azure Monitor för virtuella datorer kartan funktionen att visa felaktig information. Vi är medvetna om problemet och vi undersöker alternativ för att förbättra upplevelsen.

## <a name="does-the-map-feature-support-ipv6"></a>Funktionen karta som har stöd för IPv6?
Funktionen kartan stöder för närvarande endast IPv4 och vi undersöker stöd för IPv6. Vi har också stöd för IPv4 tunneldata i IPv6.

## <a name="when-i-load-a-map-for-a-resource-group-or-other-large-group-why-is-the-map-difficult-to-view"></a>När jag läser in en karta för en resursgrupp eller annan stor grupp varför du är på kartan svårt att visa?
Även om vi har förbättrat funktionen kartan för att hantera stora och komplexa konfigurationer, förstår vi en karta kan ha många noder, anslutningar och noder som arbetar i ett kluster. Vi är måna om du fortsätter att förbättra stöd för att öka skalbarheten.  

## <a name="why-does-the-network-chart-on-the-performance-tab-look-different-from-the-network-chart-on-the-azure-vm-overview-page"></a>Varför ser nätverk diagrammet på fliken prestanda skiljer sig från nätverket diagrammet på översiktssidan för Azure VM?

Översiktssidan för en Azure-dator visar diagram baserat på värdens mätning av aktiviteten i den Virtuella gästdatorn. Nätverk-diagrammet på översiktssidan för Azure VM visar endast nätverkstrafik som kommer att debiteras. Den här vyn innehåller inte trafik mellan virtuella nätverk. Data och diagram som visas för Azure Monitor för virtuella datorer är baserade på data från den Virtuella gästdatorn och nätverk-diagrammet visar alla TCP/IP-trafik som har inkommande och utgående trafik till den virtuella datorn, inklusive trafik mellan virtuella nätverk.

## <a name="what-are-the-limitations-of-the-log-analytics-free-pricing-plan"></a>Vilka är begränsningarna i Log Analytics kostnadsfria prisplanen?
Om du har konfigurerat Azure Monitor med en Log Analytics-arbetsyta med hjälp av den *kostnadsfri* prisnivå, i Azure Monitor för virtuella datorer kartan funktionen stöder anslutning av endast fem datorer till arbetsytan. 

Anta exempelvis att du har fem virtuella datorer är anslutna till en kostnadsfri arbetsyta. Om du koppla från en virtuell dator och sedan ansluta en ny, är inte den nya virtuella datorn övervakas och visas på sidan kartan. I det här scenariot när du öppnar den nya virtuella datorn uppmanas du att använda den **Testa nu** och välja **Insights (förhandsversion)** i den vänstra rutan, även när det har installerats på den virtuella datorn. Men ombeds du inte med alternativ som du normalt skulle vara om den virtuella datorn inte distribuerats till Azure Monitor för virtuella datorer. 

## <a name="next-steps"></a>Nästa steg
För att förstå de krav och metoder för att aktivera övervakning av dina virtuella datorer, granska [distribuera Azure Monitor för virtuella datorer (förhandsversion)](vminsights-onboard.md).
