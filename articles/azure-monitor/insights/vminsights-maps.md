---
title: Visa appberoenden med Azure Monitor för virtuella datorer
description: Karta är en funktion i Azure Monitor för virtuella datorer. Den identifierar automatiskt programkomponenter på Windows- och Linux-system och mappar kommunikationen mellan tjänster. Den här artikeln innehåller information om hur du använder kartfunktionen i olika scenarier.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/20/2020
ms.openlocfilehash: acb96984a49e4ad8535f87a41da11b3b63ae207b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80283862"
---
# <a name="use-the-map-feature-of-azure-monitor-for-vms-to-understand-application-components"></a>Använda kartfunktionen i Azure Monitor för virtuella datorer för att förstå programkomponenter
I Azure Monitor för virtuella datorer kan du visa identifierade programkomponenter på Windows och Virtuella Linux-datorer (VMs) som körs i Azure eller din miljö. Du kan observera de virtuella datorerna på två sätt. Visa en karta direkt från en virtuell dator eller visa en karta från Azure Monitor för att se komponenterna i grupper av virtuella datorer. Den här artikeln hjälper dig att förstå dessa två visningsmetoder och hur du använder kartfunktionen. 

Information om hur du konfigurerar Azure Monitor för virtuella datorer finns i [Aktivera Azure Monitor för virtuella datorer](vminsights-enable-overview.md).

## <a name="sign-in-to-azure"></a>Logga in på Azure
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="introduction-to-the-map-experience"></a>Introduktion till kartupplevelsen
Innan du dyker in i kartupplevelsen bör du förstå hur den presenterar och visualiserar information. Oavsett om du väljer kartfunktionen direkt från en virtuell dator eller från Azure Monitor, presenterar kartfunktionen en konsekvent upplevelse. Den enda skillnaden är att från Azure Monitor visar en karta alla medlemmar i ett program eller kluster på flera nivåer.

Kartfunktionen visualiserar vm-beroenden genom att identifiera processer som körs som har: 

- Aktiva nätverksanslutningar mellan servrar.
- Inkommande och utgående anslutningsfördröjning.
- Portar över en TCP-ansluten arkitektur över ett angivet tidsintervall.  
 
Expandera en virtuell dator för att visa processinformation och endast de processer som kommunicerar med den virtuella datorn. Klientgruppen visar antalet frontend-klienter som ansluter till den virtuella datorn. Serverportgrupperna visar antalet backend-servrar som den virtuella datorn ansluter till. Expandera en serverportgrupp om du vill se en detaljerad lista över servrar som ansluter via porten.  

När du väljer den virtuella datorn visar fönstret **Egenskaper** till höger den virtuella datorns egenskaper. Egenskaper inkluderar systeminformation som rapporteras av operativsystemet, egenskaperna för Den virtuella Azure-datorn och ett ringdiagram som sammanfattar de identifierade anslutningarna. 

![Fönstret Egenskaper](./media/vminsights-maps/properties-pane-01.png)

Till höger i fönstret väljer du **Logga händelser** för att visa en lista över data som den virtuella datorn har skickat till Azure Monitor. Dessa data är tillgängliga för frågor.  Välj valfri posttyp för att öppna sidan **Loggar,** där du ser resultaten för den posttypen. Du ser också en förkonfigurerad fråga som filtreras mot den virtuella datorn.  

![Fönstret Logghändelser](./media/vminsights-maps/properties-pane-logs-01.png)

Stäng sidan **Loggar** och gå tillbaka till fönstret **Egenskaper.** Där väljer du **Aviseringar** för att visa aviseringar om hälsovillkor för virtuella datorer. Kartfunktionen integreras med Azure-aviseringar för att visa aviseringar för den valda servern i det valda tidsintervallet. Servern visar en ikon för aktuella aviseringar och i fönstret **Datorvarningar** visas aviseringarna. 

![Fönstret Aviseringar](./media/vminsights-maps/properties-pane-alerts-01.png)

Om du vill att kartfunktionen ska visa relevanta aviseringar skapar du en varningsregel som gäller för en viss dator:

- Inkludera en sats för att gruppera aviseringar efter dator (till exempel **efter datorintervall 1 minut**).
- Basera aviseringen på ett mått.

Mer information om Azure-aviseringar och skapa varningsregler finns [i Enhetliga aviseringar i Azure Monitor](../../azure-monitor/platform/alerts-overview.md).

I det övre högra hörnet beskriver alternativet **Förklaring** symboler och roller på kartan. Om du vill titta närmare på kartan och flytta runt den använder du zoomkontrollerna i det nedre högra hörnet. Du kan ställa in zoomnivån och anpassa kartan till sidans storlek.  

## <a name="connection-metrics"></a>Anslutningsmått
Fönstret **Anslutningar** visar standardmått för den valda anslutningen från den virtuella datorn över TCP-porten. Måtten omfattar svarstid, begäranden per minut, trafikgenomströmning och länkar.  

![Nätverksanslutningsdiagram i fönstret Anslutningar](./media/vminsights-maps/map-group-network-conn-pane-01.png)  

### <a name="failed-connections"></a>Misslyckade anslutningar
Kartan visar misslyckade anslutningar för processer och datorer. En streckad röd linje indikerar att ett klientsystem inte når en process eller port. För system som använder beroendeagenten rapporterar agenten om misslyckade anslutningsförsök. Kartfunktionen övervakar en process genom att observera TCP-socketar som inte kan upprätta en anslutning. Det här felet kan bero på en brandvägg, en felkonfiguration i klienten eller servern eller en fjärrtjänst som inte är tillgänglig.

![En misslyckad anslutning på kartan](./media/vminsights-maps/map-group-failed-connection-01.png)

Genom att förstå misslyckade anslutningar kan du felsöka, validera migrering, analysera säkerhet och förstå tjänstens övergripande arkitektur. Misslyckade anslutningar är ibland ofarliga, men de pekar ofta på ett problem. Anslutningar kan misslyckas, till exempel när en redundansmiljö plötsligt inte kan nås eller när två programnivåer inte kan kommunicera med varandra efter en molnmigrering.

### <a name="client-groups"></a>Klientgrupper
På kartan representerar klientgrupper klientdatorer som ansluter till den mappade datorn. En enskild klientgrupp representerar klienterna för en enskild process eller dator.

![En klientgrupp på kartan](./media/vminsights-maps/map-group-client-groups-01.png)

Om du vill visa de övervakade klienterna och IP-adresserna för systemen i en klientgrupp väljer du gruppen. Innehållet i gruppen visas nedan.  

![En kundgrupps lista över IP-adresser på kartan](./media/vminsights-maps/map-group-client-group-iplist-01.png)

Om gruppen innehåller övervakade och oövervakade klienter kan du välja lämplig del i gruppens ringdiagram för att filtrera klienterna.

### <a name="server-port-groups"></a>Serverportgrupper
Serverportgrupper representerar portar på servrar som har inkommande anslutningar från den mappade datorn. Gruppen innehåller serverporten och antalet servrar som har anslutningar till porten. Markera gruppen om du vill visa de enskilda servrarna och anslutningarna. 

![En serverportgrupp på kartan](./media/vminsights-maps/map-group-server-port-groups-01.png)  

Om gruppen innehåller övervakade och oövervakade servrar kan du välja lämplig del i gruppens ringdiagram för att filtrera servrarna.

## <a name="view-a-map-from-a-vm"></a>Visa en karta från en virtuell dator 

Så här kommer du åt Azure Monitor för virtuella datorer direkt från en virtuell dator:

1. I Azure-portalen väljer du **Virtuella datorer**. 
2. Välj en virtuell dator i listan. Välj **Insikter**i avsnittet **Övervakning** .  
3. Välj fliken **Karta.**

Kartan visualiserar den virtuella datorns beroenden genom att identifiera löpprocessgrupper och processer som har aktiva nätverksanslutningar över ett angivet tidsintervall.  

Som standard visar kartan de senaste 30 minuterna. Om du vill se hur beroenden såg ut tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Om du vill köra frågan använder du **TimeRange-väljaren** i det övre vänstra hörnet. Du kan köra en fråga, till exempel under en incident eller för att se statusen före en ändring.  

![Översikt över karta över direkt virtuell dator](./media/vminsights-maps/map-direct-vm-01.png)

## <a name="view-a-map-from-a-virtual-machine-scale-set"></a>Visa en karta från en skalningsuppsättning för virtuella datorer

Så här kommer du åt Azure Monitor för virtuella datorer direkt från en skaluppsättning för virtuella datorer:

1. I Azure-portalen väljer du **Skaluppsättningar för virtuella datorer**.
2. Välj en virtuell dator i listan. Välj sedan **Insikter**i avsnittet **Övervakning** .  
3. Välj fliken **Karta.**

Kartan visualiserar alla förekomster i skalningsuppsättningen som en gruppnod tillsammans med gruppens beroenden. Den expanderade noden listar instanserna i skalningsuppsättningen. Du kan bläddra igenom dessa instanser 10 åt gången. 

Om du vill läsa in en karta för en viss instans väljer du först den förekomsten på kartan. Välj sedan **ellipsknappen** (...) till höger och välj **Läs in serverkarta**. På kartan som visas visas processgrupper och processer som har aktiva nätverksanslutningar över ett angivet tidsintervall. 

Som standard visar kartan de senaste 30 minuterna. Om du vill se hur beroenden såg ut tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Om du vill köra **TimeRange** frågan använder du timerange-väljaren. Du kan köra en fråga, till exempel under en incident eller för att se statusen före en ändring.

![Översikt över karta över direkt virtuell dator](./media/vminsights-maps/map-direct-vmss-01.png)

>[!NOTE]
>Du kan också komma åt en karta för en viss instans från **instansvyn** för din skalauppsättning för virtuella datorer. Gå till **Instansstatistik** > i avsnittet **Inställningar** **.**

## <a name="view-a-map-from-azure-monitor"></a>Visa en karta från Azure Monitor

I Azure Monitor ger kartfunktionen en global vy över dina virtuella datorer och deras beroenden. Så här kommer du åt kartfunktionen i Azure Monitor:

1. Välj **Övervaka**i Azure-portalen . 
2. Välj **Virtuella datorer i**avsnittet **Insikter** .
3. Välj fliken **Karta.**

   ![Översiktsöversiktsmappning för Flera virtuella datorer i Azure Monitor](./media/vminsights-maps/map-multivm-azure-monitor-01.png)

Välj en arbetsyta med hjälp av **arbetsyteväljaren** högst upp på sidan. Om du har mer än en Log Analytics-arbetsyta väljer du den arbetsyta som är aktiverad med lösningen och som har virtuella datorer som rapporterar till den. 

**Gruppväljaren** returnerar prenumerationer, resursgrupper, [datorgrupper](../../azure-monitor/platform/computer-groups.md)och skaluppsättningar för virtuella datorer som är relaterade till den valda arbetsytan. Ditt val gäller endast kartfunktionen och överför inte till Prestanda eller Hälsa.

Som standard visar kartan de senaste 30 minuterna. Om du vill se hur beroenden såg ut tidigare kan du fråga efter historiska tidsintervall på upp till en timme. Om du vill köra **TimeRange** frågan använder du timerange-väljaren. Du kan köra en fråga, till exempel under en incident eller för att se statusen före en ändring.  

## <a name="next-steps"></a>Nästa steg

Information om hur du identifierar flaskhalsar, kontrollerar prestanda och förstår det totala utnyttjandet av dina virtuella datorer finns i [Visa prestandastatus för Azure Monitor för virtuella datorer](vminsights-performance.md). 
