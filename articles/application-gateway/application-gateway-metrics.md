---
title: Azure Monitor mått för Application Gateway
description: Lär dig hur du använder mått för att övervaka prestanda för Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 8/29/2019
ms.author: absha
ms.openlocfilehash: 8d75dbe5d4ab819e5bbe64e20ad84eb1c26a87a3
ms.sourcegitcommit: 5b073caafebaf80dc1774b66483136ac342f7808
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/09/2020
ms.locfileid: "75777826"
---
# <a name="metrics-for-application-gateway"></a>Mått för Application Gateway

Application Gateway publicerar data punkter, som kallas mått, för att [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för prestanda för dina Application Gateway-och Server dels instanser. Dessa mått är numeriska värden i en ordnad uppsättning av Time Series-data som beskriver någon aspekt av programgatewayen vid en viss tidpunkt. Om det finns begär Anden som flödar genom Application Gateway, mäter den och skickar sina mått i intervall om 60 sekunder. Om det inte finns några begär Anden som flödar genom Application Gateway eller inga data för ett mått, rapporteras inte måttet. Mer information finns i [Azure Monitor mått](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Mått som stöds av Application Gateway v2 SKU

### <a name="timing-metrics"></a>Tids mått

Följande mått är relaterade till tiden för begäran och svar är tillgängliga. Genom att analysera dessa mått för en specifik lyssnare kan du avgöra om den går långsammare i på grund av WAN, Application Gateway, nätverket mellan Application Gateway och Server dels programmet eller Server delens program prestanda.

> [!NOTE]
>
> Om det finns fler än en lyssnare i Application Gateway ska du alltid filtrera efter *Listener* -dimension samtidigt som du jämför olika svars tids mått för att få en meningsfull härledning.

- **Klient-/klient**

  Genomsnittlig fördröjning mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och returnera bekräftelser. 

- **Total tid för Application Gateway**

  Genomsnittlig tid det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som genomsnitt av intervallet från den tid då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att detta vanligt vis omfattar Application Gateway bearbetnings tid, tid då paket för begäran och svar överförs över nätverket och hur lång tid det tog att svara på backend-servern.
  
Efter att ha filtrerat efter lyssnare, om *klientens* efter svars tid är mycket mer än den *totala tiden för programgatewayen*, kan det härledas att den fördröjning som klienten har observerat beror på nätverks anslutningen mellan klienten och Application Gateway. Om båda svars tiderna är jämförbara kan den långa latensen bero på något av följande: Application Gateway, nätverket mellan Application Gateway och Server dels programmet eller Server delens prestanda.

- **Svars tid för första byte för Server del**

  Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet, ungefär bearbetnings tiden för backend-servern

- **Svars tid för senaste byte för Server delen**

  Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten
  
Om den *totala tiden för programgatewayen* är mycket mer än den *senaste byte svars tiden* för en specifik lyssnare, kan det härledas att den långa svars tiden kan bero på Application Gateway. Å andra sidan, om de två måtten är jämförbara, kan problemet antingen bero på nätverket mellan Application Gateway och Server dels programmet eller prestanda för Server dels programmet.

- **Server dels anslutnings tid**

  Åtgången tid för att upprätta en anslutning till ett Server dels program. I händelse av SSL, innehåller det den tid som ägnats åt hand skakningen. Observera att måttet skiljer sig från de andra svars tiderna eftersom detta bara mäter anslutnings tiden och därför inte ska jämföras direkt i storlek med de andra fördröjningarna. Att jämföra mönstret för *Server dels anslutnings tiden* med mönstret för de andra fördröjningarna kan dock indikera om en ökning av andra fördröjningar kan härledas på grund av en variation i nätverket mellan programmet Gatway och Server dels programmet. 
  

### <a name="application-gateway-metrics"></a>Application Gateway mått

För Application Gateway är följande mått tillgängliga:

- **Mottagna byte**

   Antal byte som tagits emot av Application Gateway från klienterna

- **Skickade byte**

   Antal byte som har skickats av Application Gateway till klienterna

- **Klientens TLS-protokoll**

   Antal TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutning till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.

- **Aktuella kapacitets enheter**

   Antal förbrukade kapacitets enheter. Kapacitets enheter mäter förbruknings-baserad kostnad som debiteras utöver den fasta kostnaden. Det finns tre faktorer för kapacitets enhets beräknings enhet, beständiga anslutningar och data flöde. Varje kapacitets enhet består av högst: 1 beräknings enhet eller 2500 beständiga anslutningar eller 2,22 – Mbps-dataflöde.

- **Aktuella beräknings enheter**

   Antal förbrukade processor kapacitet. Faktorer som påverkar beräknings enheten är TLS-anslutningar/s, URL-omskrivning och bearbetning av WAF-regler. 

- **Aktuella anslutningar**

   Antal aktuella anslutningar som upprättats med Application Gateway

- **Misslyckade förfrågningar**

   Antal misslyckade förfrågningar som Application Gateway har betjänat. Antalet begär Anden kan filtreras ytterligare för att visa antal per/specifika Server dels pool – http-inställnings kombination.


- **Svars status**

   HTTP-svarets status returnerades av Application Gateway. Svars status kod distributionen kan kategoriseras ytterligare för att Visa svar i 2xx-, 3xx-, 4xx-och 5xx-kategorier.

- **Dataflöde**

   Antal byte per sekund som Application Gateway har betjänat

- **Totalt antal förfrågningar**

   Antal lyckade förfrågningar som Application Gateway har betjänat. Antalet begär Anden kan filtreras ytterligare för att visa antal per/specifika Server dels pool – http-inställnings kombination.

- **Regler för matchad brand vägg för webbaserade program**

- **Aktiverade regler för brand vägg för webbaserade program**

### <a name="backend-metrics"></a>Server dels mått

För Application Gateway är följande mått tillgängliga:

- **Svars status för Server del**

  Antal HTTP-svars status koder som returneras av Server delarna. Detta omfattar inte några svars koder som genereras av Application Gateway. Svars status kod distributionen kan kategoriseras ytterligare för att Visa svar i 2xx-, 3xx-, 4xx-och 5xx-kategorier.

- **Antal felfria värdar**

  Antalet Server delar som bedöms felfritt av hälso avsökningen. Du kan filtrera efter en pool per server del för att Visa felfria/Felaktiga värdar i en viss backend-pool.

- **Antal felaktiga värdar**

  Antalet Server delar som avvisats i hälso avsökningen. Du kan filtrera efter en pool per server del för att Visa Felaktiga värdar i en viss backend-pool.

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Mått som stöds av Application Gateway v1 SKU

### <a name="application-gateway-metrics"></a>Application Gateway mått

För Application Gateway är följande mått tillgängliga:

- **PROCESSOR användning**

  Visar användningen av de processorer som har allokerats till Application Gateway.  Under normala förhållanden bör CPU-användningen inte regelbundet överskrida 90%, eftersom detta kan orsaka svars tider på de webbplatser som ligger bakom Application Gateway och störa klient upplevelsen. Du kan styra eller förbättra processor användningen genom att ändra konfigurationen för Application Gateway genom att öka antalet instanser eller genom att flytta till en större SKU-storlek eller göra båda.

- **Aktuella anslutningar**

  Antal aktuella anslutningar som upprättats med Application Gateway

- **Misslyckade förfrågningar**

  Antal misslyckade förfrågningar som Application Gateway har betjänat. Antalet begär Anden kan filtreras ytterligare för att visa antal per/specifika Server dels pool – http-inställnings kombination.

- **Svars status**

  HTTP-svarets status returnerades av Application Gateway. Svars status kod distributionen kan kategoriseras ytterligare för att Visa svar i 2xx-, 3xx-, 4xx-och 5xx-kategorier.

- **Dataflöde**

  Antal byte per sekund som Application Gateway har betjänat

- **Totalt antal förfrågningar**

  Antal lyckade förfrågningar som Application Gateway har betjänat. Antalet begär Anden kan filtreras ytterligare för att visa antal per/specifika Server dels pool – http-inställnings kombination.

- **Regler för matchad brand vägg för webbaserade program**

- **Aktiverade regler för brand vägg för webbaserade program**

### <a name="backend-metrics"></a>Server dels mått

För Application Gateway är följande mått tillgängliga:

- **Antal felfria värdar**

  Antalet Server delar som bedöms felfritt av hälso avsökningen. Du kan filtrera efter en pool per server del för att Visa felfria/Felaktiga värdar i en viss backend-pool.

- **Antal felaktiga värdar**

  Antalet Server delar som avvisats i hälso avsökningen. Du kan filtrera efter en pool per server del för att Visa Felaktiga värdar i en viss backend-pool.

## <a name="metrics-visualization"></a>Mått visualisering

Bläddra till en Application Gateway, under **övervakning** Välj **mått**. Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

I följande bild visas ett exempel med tre mått som visas under de senaste 30 minuterna:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Om du vill se en aktuell lista över mått, se [mått som stöds med Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Aviserings regler för mått

Du kan starta aviserings regler baserat på mått för en resurs. En avisering kan t. ex. anropa en webhook eller skicka en e-post till en administratör om data flödet för programgatewayen är över, under eller vid ett tröskelvärde under en angiven period.

Följande exempel visar hur du skapar en varnings regel som skickar ett e-postmeddelande till en administratör när data flödet har överträtt ett tröskelvärde:

1. Välj **Lägg till mått avisering** för att öppna sidan **Lägg till regel** . Du kan också komma åt den här sidan från sidan mått.

   ![Knappen Lägg till mått avisering][6]

2. På sidan **Lägg till regel** fyller du i avsnitten namn, villkor och meddelande och väljer **OK**.

   * I **villkors** väljaren väljer du något av de fyra värdena: **större än**, **större än eller lika med**, **mindre än**eller **mindre än eller lika**med.

   * I **period** väljaren väljer du en period på fem minuter till sex timmar.

   * Om du väljer **e-postägare, deltagare och läsare**kan e-postmeddelandet vara dynamiskt baserat på de användare som har åtkomst till resursen. Annars kan du ange en kommaavgränsad lista med användare i rutan **ytterligare administratörs e-post (er)** .

   ![Sidan Lägg till regel][7]

Om tröskelvärdet överskrids kommer ett e-postmeddelande som liknar det som finns i följande bild att bli:

![E-postmeddelande för överträtt tröskel][8]

En lista med aviseringar visas när du har skapat en mått-avisering. Den ger en översikt över alla aviserings regler.

![Lista över aviseringar och regler][9]

Mer information om aviserings aviseringar finns i [ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Om du vill veta mer om webhookar och hur du kan använda dem med aviseringar kan du gå till [Konfigurera en webhook på en Azure Metric-avisering](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknare och händelse loggar med hjälp av [Azure Monitor loggar](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera din Azure aktivitets logg med Power BI](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) blogg inlägg.
* [Visa och analysera Azure aktivitets loggar i Power BI och fler](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogg inlägg.

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
