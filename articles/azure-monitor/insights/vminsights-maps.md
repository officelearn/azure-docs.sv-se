---
title: Visa beroenden för appen med Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Kartan är en funktion i Azure Monitor för virtuella datorer som automatiskt identifierar programkomponenter i Windows och Linux-system och mappar kommunikationen mellan olika tjänster. Den här artikeln innehåller information om hur du använder det på en mängd olika scenarier.
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
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: 3f485d2e7c2aefaede7d0334fd89ce3bea9e3f3a
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2018
ms.locfileid: "53187916"
---
# <a name="using-azure-monitor-for-vms-preview-map-to-understand-application-components"></a>Med Azure Monitor för virtuella datorer (förhandsversion) mappas och förstå programkomponenter
Visa de identifierade programkomponenterna i Windows och Linux-datorer som körs i Azure som din miljö kan observeras på två sätt med Azure Monitor för virtuella datorer från en virtuell dator direkt eller i grupper med virtuella datorer från Azure Monitor. 

Den här artikeln hjälper dig att förstå upplevelse mellan två perspektiv och hur du använder funktionen kartan. Information om hur du konfigurerar Azure Monitor för virtuella datorer finns i [aktivera Azure Monitor för virtuella datorer](vminsights-onboard.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com).

## <a name="introduction-to-map-experience"></a>Introduktion till kartan upplevelse
Innan du börjar granska karta för en enskild virtuell dator eller en grupp med virtuella datorer, är det viktigt att vi ger en kort introduktion till funktionen så att du förstår hur informationen visas och visualiseringar representerar.  

Om du väljer funktionen kartan direkt från en virtuell dator eller från Azure Monitor, anger en konsekvent upplevelse.  Den enda skillnaden är från Azure Monitor kan du se alla medlemmar i en flernivåapp eller ett kluster på en karta.

Maps hjälper dig att visualisera beroenden för virtuella datorer genom att identifiera köra processer med aktiva nätverksanslutningar mellan servrar, svarstid för inkommande och utgående anslutningar och portar i alla TCP-anslutna arkitekturer under ett angivet tidsintervall.  Expandera en virtuell dator visas information om processen och de processer som kommunicerar med den virtuella datorn visas. Antal frontend-klienter som ansluter till den virtuella datorn visas en klientgruppen. Antal backend-servrar som den virtuella datorn ansluter till anges på Port servergrupper. Expandera en servergrupp porten om du vill se en detaljerad lista över servrar som är anslutna via den porten.  

När du klickar på den virtuella datorn, den **egenskaper** fönstret utökas till höger för att visa egenskaperna för objekt som har valts, till exempel systeminformation som rapporteras av operativsystemet, egenskaper för virtuell Azure-dator och ett ringdiagram Sammanfatta identifierade anslutningar. 

![Systemegenskaper på datorn](./media/vminsights-maps/properties-pane-01.png)

På höger sida av fönstret, klicka på den **logghändelser** ikon för att växla fokus för att visa en lista över tabeller som samlats in data från den virtuella datorn har skickats till Log Analytics och är tillgänglig för frågor.  När du klickar på någon av de typer av poster i listan öppnas den **loggar** sidan för att visa resultaten för den typen med en förkonfigurerad fråga filtrerade mot specifik virtuell dator.  

![Log söklistan i egenskapsrutan](./media/vminsights-maps/properties-pane-logs-01.png)

Stäng *loggar** och återgå till den **egenskaper** rutan och välj **aviseringar** kunna se aviseringar som varnar har aktiverats för den virtuella datorn från health-villkor. Kartan kan integreras med Azure-aviseringar att visa aktiverade aviseringar för den valda servern i det valda tidsintervallet. Servern visas en ikon om det finns aktuella aviseringar och datorn aviseringsfönstret visar en lista över aviseringarna. 

![Datorn aviseringar i egenskapsrutan](./media/vminsights-maps/properties-pane-alerts-01.png)

Om du vill aktivera funktionen kartan att visa relevanta aviseringar måste du skapa en aviseringsregel som utlöses för en specifik dator. Skapa rätt aviseringar:
- Innehåller en instruktion i gruppen per dator (till exempel **datorn intervall 1 minut**).
- Välj varningar baserat på metriska måttenheter.

Läs mer om Azure-aviseringar och skapa Varningsregler [Unified aviseringar i Azure Monitor](../../monitoring-and-diagnostics/monitoring-overview-alerts.md)

Den **förklaring** alternativ i det övre högra hörnet beskriver symboler och roller på en karta.  Zooma in för en närmare titt på kartan och flytta it runt zoomning kontrollerna längst till höger på sidan anger zoomningsgraden och anpassa till sida för storleken på den aktuella sidan.  

## <a name="connection-metrics"></a>Anslutningsmått
Den **anslutningar** fönstret visar standard anslutning mått för den valda anslutningen från den virtuella datorn via TCP-port. Mått är svarstid, begäranden per minut, genomflöde och länkar.  

![Exempel anslutning diagram i nätverket](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Misslyckade anslutningar
Misslyckade anslutningar visas på kartan för processer och datorer med en röd streckad linje som anger att ett klientsystem kan inte nå en process eller port. Misslyckade anslutningar rapporteras från alla system med beroendeagenten om systemet är försöker den felande anslutningen. Kartan mäter den här processen genom att följa TCP uttag som det gick inte att upprätta en anslutning. Felet kan bero på en brandvägg kan en felaktig konfiguration i klienten eller servern eller en fjärrtjänst som som är otillgänglig.

![Exempel på misslyckade anslutningar på kartan](./media/vminsights-maps/map-group-failed-connection-01.png)

Förstå misslyckade anslutningar kan hjälpa till med felsökning, verifiera migreringen, säkerhetsanalys och förstå den övergripande arkitekturen i tjänsten. Misslyckade anslutningar är ibland ofarliga, men de pekar ofta direkt på ett problem, till exempel en redundans miljö plötsligt blir kan inte nås eller två nivåer av programmet att du inte kan kommunicera med varandra efter en molnmigrering.

### <a name="client-groups"></a>Klientgrupper
Klientgrupper på kartan representerar klientdatorer som har anslutningar till den mappade datorn. En enda klientgrupp representerar klienter för en enskild process eller dator.

![Klienten grupper exempel på kartan](./media/vminsights-maps/map-group-client-groups-01.png)

Välj gruppen om du vill se de övervakade klienterna och IP-adresser för system i en klientgrupp. Innehållet i gruppen visas under gruppen.  

![Klientens IP-adress lista exempel på kartan](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Om gruppen innehåller övervakade och oövervakade klienter, kan du välja lämpligt avsnitt i diagrammet på gruppen för att filtrera klienterna.

### <a name="server-port-groups"></a>Serverport grupper
Serverport säkerhetsgrupper som representerar server-portar på servrar som har inkommande anslutningar från den mappade datorn. Gruppen innehåller server-port och en uppräkning av antalet servrar med anslutningar till den porten. Välj gruppen som du vill visa enskilda servrar och anslutningar som anges. 

![Port servergrupp exempel på kartan](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Om gruppen innehåller övervakade och oövervakade servrar, kan du välja lämpligt avsnitt i diagrammet på gruppen för att filtrera servrarna.

## <a name="view-map-directly-from-a-virtual-machine"></a>Visa karta direkt från en virtuell dator 

Utför följande för att komma åt Azure Monitor för virtuella datorer direkt från en virtuell dator.

1. I Azure-portalen väljer du **virtuella datorer**. 
2. I listan, väljer en virtuell dator och i den **övervakning** i avsnittet **Insights (förhandsversion)**.  
3. Välj den **kartan** fliken.

Kartan visualisera beroenden för virtuella datorer, som kör processgrupper och processer med aktiva nätverksanslutningar under ett angivet tidsintervall.  Kartan visas som standard de senaste 30 minuterna.  Med hjälp av den **TimeRange** Väljaren i det övre vänstra hörnet, som du kan fråga efter historiska tidsintervall på upp till en timme att visa hur beroenden tittat tidigare (exempelvis under en incident eller innan en ändring inträffat).  

![Direct VM: översikt](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-map-from-azure-monitor"></a>Visa karta från Azure Monitor
Funktionen karta innehåller en global vy över dina virtuella datorer och deras beroenden från Azure Monitor.  Utför följande för att komma åt funktionen karta från Azure Monitor. 

1. I Azure-portalen väljer du **övervakaren**. 
2. Välj **virtuella datorer (förhandsversion)** i den **Insights** avsnittet.
3. Välj den **kartan** fliken.

![Azure Monitor Konsekvens karta: översikt](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Från den **arbetsytan** Väljaren överst på sidan om du har mer än en Log Analytics-arbetsyta väljer du den arbetsyta som har aktiverats med lösningen och har virtuella datorer som rapporterar till den. Den **grupp** väljare returnerar prenumerationer, resursgrupper, [datorgrupper](../../azure-monitor/platform/computer-groups.md), och VM scale sets med datorer som är relaterade till den valda arbetsytan. Ditt val har endast gäller för funktionen kartan och inget prestanda eller kartan.

Kartan visas som standard de senaste 30 minuterna. Med hjälp av den **TimeRange** Väljaren, som du kan fråga efter historiska tidsintervall på upp till en timme att visa hur beroenden tittat tidigare (exempelvis under en incident eller innan en ändring inträffat).   

## <a name="next-steps"></a>Nästa steg
Läs hur du använder funktionen hälsotillstånd i [visa Azure VM-hälsa](vminsights-health.md), eller för att identifiera flaskhalsar och totala användningen med din prestanda för virtuella datorer, se [visa Azure Monitor för prestanda för virtuella datorer](vminsights-performance.md). 
