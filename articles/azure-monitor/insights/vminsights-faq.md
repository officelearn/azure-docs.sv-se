---
title: Vanliga frågor och svar om Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Azure Monitor för virtuella datorer (förhandsversion) är en lösning i Azure som kombinerar hälsotillstånd och prestanda för övervakning av Azure VM-operativsystem, samt automatiskt identifierar programkomponenter och beroenden till andra resurser och mappar kommunikationen varandra. Den här artikeln innehåller vanliga frågor och svar.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/08/2018
ms.author: magoedte
ms.openlocfilehash: 124488d13d1d303743900ab1de41768633e4f6e0
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2018
ms.locfileid: "51715728"
---
# <a name="azure-monitor-for-vms-preview-frequently-asked-questions"></a>Vanliga och frågor svar om Azure Monitor för virtuella datorer (förhandsversion)
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
>Vi konfigurerar prestandaräknare för den arbetsyta som påverkar alla virtuella datorer som rapporterar till arbetsytan, oavsett om du har valt att publicera dem till Azure Monitor för virtuella datorer. Mer information om hur prestandaräknare är konfigurerade för arbetsytan finns i vår [dokumentation](../../log-analytics/log-analytics-data-sources-performance-counters.md). Information om de räknare som konfigurerats för Azure Monitor för virtuella datorer finns i vår [dokumentation om](vminsights-onboard.md#performance-counters-enabled).  

## <a name="why-did-my-vm-fail-to-onboard"></a>Varför min virtuella dator kunde inte publicera?
När onboarding en Azure-dator från Azure-portalen, genomförs följande steg:

* Standard Log Analytics-arbetsytan skapas, om som har valts.
* Prestandaräknarna har konfigurerats för den valda arbetsytan. Om det här steget misslyckas kan du se att inte några av de prestandadiagram och tabeller som visar data för den virtuella datorn du publicerat. Du kan åtgärda detta genom att köra PowerShell-skriptet som beskrivs [här](vminsights-onboard.md#enable-with-powershell).
* Log Analytics-agenten installeras på virtuella Azure-datorer med hjälp av en VM-tillägg, om det krävs.  
* I Azure Monitor för virtuella datorer kartan beroendeagenten installeras på virtuella Azure-datorer med hjälp av ett tillägg, om det krävs.  
* Azure Monitor-komponenter som stöder funktionen hälsotillstånd har konfigurerats, om det behövs och den virtuella datorn är konfigurerad för att rapportera hälsa data.

Under processen publicera Kontrollera vi status för var och en av ovan för att returnera statusen meddelande till dig i portalen.  Konfiguration av arbetsytan och agentinstallationen brukar ta 5 – 10 minuter.  Visa övervakning och hälsorapporter data i portalen ta en ytterligare 5 – 10 minuter.  

Om du har initierat onboarding och se meddelanden som indikerar den virtuella datorn behöver som ska publiceras, kan du i upp till 30 minuter för den virtuella datorn ska slutföras. 

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
* Panelerna på klientsidan har uppdaterats, men har ännu inte den fullständiga uppsättningen integration's som hade stöd i Tjänstkarta - uppdateringshantering, ändringsspårning, säkerhet och support. 
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

## <a name="are-their-limitations-if-i-am-on-the-log-analytics-free-pricing-plan"></a>Har sina begränsningar om jag på Log Analytics kostnadsfria prisplanen?
Om du har konfigurerat Azure Monitor med en Log Analytics-arbetsytan med den *kostnadsfri* prisnivå, Azure Monitor för virtuella datorer kartan funktionen stöder bara fem anslutna datorer är anslutna till arbetsytan. Om du har fem virtuella datorer är anslutna till en kostnadsfri arbetsyta kan du koppla från en av de virtuella datorerna och sedan ansluta en ny virtuell dator, den nya virtuella datorn inte övervakas och visas på sidan kartan.  

Under det här tillståndet uppmanas du att med den **Testa nu** när du öppnar den virtuella datorn och välj **Insights (förhandsversion)** i den vänstra rutan, även när den har redan installerats på den virtuella datorn.  Men ange du inte med alternativ som normalt skulle inträffa om den här virtuella datorn inte har integrerats i Azure Monitor för virtuella datorer. 

## <a name="next-steps"></a>Nästa steg
Granska [publicera Azure Monitor för virtuella datorer](vminsights-onboard.md) att förstå kraven och metoder för att övervaka dina virtuella datorer.
