---
title: Visa beroenden för appen med Azure Monitor för virtuella datorer (förhandsversion) | Microsoft Docs
description: Kartan är en funktion i Azure Monitor för virtuella datorer. Automatiskt identifierar programkomponenter i Windows och Linux-system och mappar kommunikationen mellan tjänster. Den här artikeln innehåller information om hur du använder funktionen för kartan i olika scenarier.
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
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: f6273e9b6c7ed0c4685479976343497f01201b0b
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206766"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-preview-to-understand-application-components"></a>Använda funktionen karta över Azure Monitor för virtuella datorer (förhandsversion) för att förstå programkomponenter
I Azure Monitor för virtuella datorer kan visa du identifierade programkomponenter på Windows och Linux-datorer (VM) som körs i Azure eller din miljö. Du kan se de virtuella datorerna på två sätt. Visa en karta direkt från en virtuell dator eller visa en karta från Azure Monitor för att se komponenterna i grupper med virtuella datorer. Den här artikeln hjälper dig att förstå dessa två metoder för visning och hur du använder funktionen kartan. 

Information om hur du konfigurerar Azure Monitor för virtuella datorer finns i [aktivera Azure Monitor för virtuella datorer](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på [Azure Portal](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introduktion till kartan-upplevelse
Innan du börjar kartan upplevelse, bör du förstå hur den visas och visar information. Om du väljer funktionen kartan direkt från en virtuell dator eller från Azure Monitor, anger funktionen kartan en konsekvent upplevelse. Den enda skillnaden är att från Azure Monitor en karta visar alla medlemmar i en flera nivåer program eller ett kluster.

Funktionen kartan hjälper dig att visualisera VM-beroenden genom att identifiera processer som körs som har: 

- Aktiva nätverksanslutningar mellan servrar.
- Svarstid för inkommande och utgående anslutningar.
- Portar för alla TCP-anslutna arkitekturer under ett angivet tidsintervall.  
 
Expandera en virtuell dator för att visa information om och bara de processer som kommunicerar med den virtuella datorn. Klientgruppen visar antal frontend-klienter som ansluter till den virtuella datorn. Server-port-grupper visas antalet backend-servrar som den virtuella datorn ansluter till. Expandera en server-port grupp om du vill se en detaljerad lista över servrar som ansluter via den porten.  

När du väljer den virtuella datorn, den **egenskaper** till höger visar den Virtuella datorns egenskaper. Egenskaper för omfattar systeminformation som rapporteras av operativsystemet, egenskaper för virtuell Azure-dator och ett ringdiagram som sammanfattar de identifierade anslutningarna. 

![Egenskapsrutan](./media/vminsights-maps/properties-pane-01.png)

På höger sida av fönstret, Välj **logghändelser** att visa en lista över data som den virtuella datorn har skickats till Azure Monitor. Dessa data är tillgängliga för frågor.  Välj valfri typ av post att öppna den **loggar** sidan där du ser resultatet för den posttypen. Du kan också se en förkonfigurerad fråga som har filtrerats mot den virtuella datorn.  

![Fönstret logghändelser](./media/vminsights-maps/properties-pane-logs-01.png)

Stäng den **loggar** sidan och gå tillbaka till den **egenskaper** fönstret. Där väljer **aviseringar** att visa aviseringar för VM-health-villkor. Funktionen kartan kan integreras med Azure-aviseringar att visa aviseringar för den valda servern i det valda tidsintervallet. Servern visas en ikon för aktuella aviseringar och **datorn aviseringar** rutan visas aviseringarna. 

![Aviseringspanelen](./media/vminsights-maps/properties-pane-alerts-01.png)

Skapa en aviseringsregel som gäller för en specifik dator för att göra funktionen kartan för att visa relevanta aviseringar:

- Innehåller en instruktion i gruppen aviseringar per dator (till exempel **datorn intervall 1 minut**).
- Basera aviseringen på ett mått.

Läs mer om Azure-aviseringar och skapa Varningsregler [Unified aviseringar i Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

I det övre högra hörnet i **förklaring** alternativet beskrivs symboler och roller på kartan. Använd zoom-kontroller för en närmare titt på kartan och flytta runt i det nedre högra hörnet. Du kan ange zoomnivån och passa in kartan till storleken på sidan.  

## <a name="connection-metrics"></a>Anslutningsmått
Den **anslutningar** fönstret visar standardmått för den valda anslutningen från den virtuella datorn via TCP-port. Mått är svarstid, begäranden per minut, genomflöde och länkar.  

![Network connectivity diagram i fönstret anslutningar](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Misslyckade anslutningar
Kartan visar misslyckade anslutningar för processer och datorer. En röd streckad linje anger ett klientsystem kan inte nå en process eller port. Agenten rapporterar om misslyckade anslutningsförsök för system som använder beroendeagenten. Funktionen kartan övervakar en process genom att följa TCP uttag som det gick inte att upprätta en anslutning. Det här felet kan bero på en brandvägg kan en felaktig konfiguration i klienten eller servern eller en otillgänglig fjärrtjänsten.

![En misslyckad anslutning på kartan](./media/vminsights-maps/map-group-failed-connection-01.png)

Förstå misslyckade anslutningar kan hjälpa dig att felsöka, verifiera migrering, analysera säkerhet och beskriver den övergripande arkitekturen i tjänsten. Misslyckade anslutningar är ibland ofarliga, men de pekar ofta på ett problem. Anslutningar misslyckas, till exempel när en redundans miljö blir plötsligt kan inte nås eller när två nivåer av programmet inte kan kommunicera med varandra efter en molnmigrering.

### <a name="client-groups"></a>Klientgrupper
På kartan representerar klientgrupper klientdatorer som ansluter till den mappade datorn. En enskild klientgrupp representerar klienter för en enskild process eller dator.

![En klientgrupp på kartan](./media/vminsights-maps/map-group-client-groups-01.png)

Välj gruppen om du vill se de övervakade klienterna och IP-adresser för system i en klientgrupp. Innehållet i gruppen visas nedan.  

![En klientgrupp lista med IP-adresser på kartan](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Om gruppen innehåller övervakade och oövervakade klienter, kan du välja lämpligt avsnitt i den gruppen ringdiagram att filtrera klienterna.

### <a name="server-port-groups"></a>Serverport grupper
Serverport säkerhetsgrupper som representerar portar på servrar som har inkommande anslutningar från den mappade datorn. Gruppen innehåller server-port och en uppräkning av antalet servrar som har anslutningar till porten. Välj gruppen som du vill visa enskilda servrar och anslutningar. 

![En grupp för server-port på kartan](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Om gruppen innehåller övervakade och oövervakade servrar, kan du välja lämpligt avsnitt i den gruppen ringdiagram att filtrera servrarna.

## <a name="view-a-map-from-a-vm"></a>Visa en karta från en virtuell dator 

Öppna Azure Monitor för virtuella datorer direkt från en virtuell dator:

1. I Azure-portalen väljer du **virtuella datorer**. 
2. Välj en virtuell dator i listan. I den **övervakning** väljer **Insights (förhandsversion)** .  
3. Välj den **kartan** fliken.

Kartan visar den Virtuella datorns beroenden genom att identifiera kör processgrupper och processer som har aktiva nätverksanslutningar under ett angivet tidsintervall.  

Kartan visas som standard de senaste 30 minuterna. Om du vill se hur beroenden tittat tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Kör frågan med den **TimeRange** väljare i det övre vänstra hörnet. Du kan köra en fråga, exempelvis under en incident eller vill se status innan en ändring.  

![Direct VM: översikt](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Visa en karta från en VM-skalningsuppsättning

Komma åt Azure Monitor för virtuella datorer direkt från en VM-skalningsuppsättning:

1. I Azure-portalen väljer du **VM-skalningsuppsättningar**.
2. Välj en virtuell dator i listan. I den **övervakning** väljer **Insights (förhandsversion)** .  
3. Välj den **kartan** fliken.

Kartan visualiserar alla instanser i skalningsuppsättningen som en gruppnod tillsammans med gruppens beroenden. Noden expanderade visar instanserna i skalningsuppsättningen. Du kan bläddra igenom dessa instanser 10 i taget. 

Läs in en karta för en viss instans genom att först välja instansen på kartan. Välj sedan den **ellipsen** knappen (...) till höger och välj **Läs in Servermappning**. Kartan som visas, visas processgrupper och processer som har aktiva nätverksanslutningar under ett angivet tidsintervall. 

Kartan visas som standard de senaste 30 minuterna. Om du vill se hur beroenden tittat tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Kör frågan med den **TimeRange** väljare. Du kan köra en fråga, exempelvis under en incident eller vill se status innan en ändring.

![Direct VM: översikt](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Du kan också komma åt en karta för en specifik instans från den **instanser** vyn för din skalningsuppsättning för virtuell dator. I den **inställningar** går du till avsnittet **instanser** > **Insights (förhandsversion)** .

## <a name="view-a-map-from-azure-monitor"></a>Visa en karta från Azure Monitor
Funktionen karta innehåller en global vy över dina virtuella datorer och deras beroenden i Azure Monitor. Öppna kartan-funktionen i Azure Monitor:

1. I Azure-portalen väljer du **övervakaren**. 
2. I den **Insights** väljer **virtuella datorer (förhandsversion)** .
3. Välj den **kartan** fliken.

   ![Azure Monitor-översikten över flera virtuella datorer](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Välj en arbetsyta med hjälp av den **arbetsytan** Väljaren överst på sidan. Om du har fler än en Log Analytics-arbetsyta väljer du den arbetsyta som har aktiverats med lösningen och som har virtuella datorer som rapporterar till den. 

Den **grupp** väljare returnerar prenumerationer, resursgrupper, [datorgrupper](../../azure-monitor/platform/computer-groups.md), och VM scale sets med datorer som är relaterade till den valda arbetsytan. Ditt val gäller enbart för funktionen kartan och sprids inte till prestanda och hälsa.

Kartan visas som standard de senaste 30 minuterna. Om du vill se hur beroenden tittat tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Kör frågan med den **TimeRange** väljare. Du kan köra en fråga, exempelvis under en incident eller vill se status innan en ändring.  

## <a name="next-steps"></a>Nästa steg
- Läs hur du använder funktionen hälsotillstånd i [visa Azure VM-hälsa](vminsights-health.md). 
- Om du vill identifiera flaskhalsar, kontrollera prestanda, och förstå övergripande användning av dina virtuella datorer, se [visa prestandatillstånd för Azure Monitor för virtuella datorer](vminsights-performance.md). 
