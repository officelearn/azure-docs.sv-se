---
title: Azure Monitor-mått för Application Gateway
description: Lär dig hur du använder mått för att övervaka prestanda för programgateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 2/5/2019
ms.author: absha
ms.openlocfilehash: abff2f16d9559f015417711820a993badd636f7c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133081"
---
# <a name="metrics-for-application-gateway"></a>Mått för Application Gateway

Application Gateway publicerar datapunkter, så kallade mått, till [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/overview) för prestanda för dina Application Gateway- och serverdinstanser. Dessa mått är numeriska värden i en ordnad uppsättning tidsseriedata som beskriver någon aspekt av programgatewayen vid en viss tidpunkt. Om det finns begäranden som flödar genom Application Gateway mäter och skickar den sina mått i 60-sekundersintervall. Om det inte finns några begäranden som flödar genom Application Gateway eller inga data för ett mått, rapporteras inte måttet. Mer information finns i [Azure Monitor-mått](https://docs.microsoft.com/azure/azure-monitor/platform/data-platform-metrics).

## <a name="metrics-supported-by-application-gateway-v2-sku"></a>Mått som stöds av Application Gateway V2 SKU

### <a name="timing-metrics"></a>Tidsmått

Application Gateway innehåller flera inbyggda tidsmått som är relaterade till begäran och svar, som alla mäts i millisekunder. 

![](./media/application-gateway-metrics/application-gateway-metrics.png)

> [!NOTE]
>
> Om det finns fler än en lyssnare i application gateway, filtrera alltid efter *lyssnardimension* samtidigt som du jämför olika svarstidsmått för att få meningsfull inferens.

- **Anslutningstid för backend**

  Tid som lagts ned på att upprätta en anslutning till backend-programmet. 

  Detta inkluderar nätverksfördröjningen samt den tid det tar för serverdserverns TCP-stack att upprätta nya anslutningar. När det gäller TLS, det innehåller också den tid som spenderas på handslag. 

- **Svarstid för första byte för servering**

  Tidsintervall mellan starten för att upprätta en anslutning till server för server för server som är serverad och ta emot den första bytet i svarshuvudet. 

  Detta approximerar summan av *Serverd-anslutningstid*, den tid det tar för begäran att nå servergången från Application Gateway, tid som tas av backend-programmet för att svara (den tid servern tog för att generera innehåll, eventuellt hämta databasfrågor) och den tid det tog för första byte av svaret för att nå Application Gateway från serverkopplingen.

- **Svarstid för servering senast byte**

  Tidsintervall mellan starten för att upprätta en anslutning till serverdelsservern och ta emot den sista bytet av svarstexten. 

  Detta approximerar summan av *serverda svarstid* för första byte och dataöverföringstid (det här antalet kan variera kraftigt beroende på storleken på de begärda objekten och svarstiden för servernätverket).

- **Total tid för programgateway**

  Genomsnittlig tid som det tar för en begäran att tas emot, bearbetas och dess svar skickas. 

  Detta är intervallet från den tidpunkt då Application Gateway tar emot den första bytet av HTTP-begäran till den tidpunkt då den senaste svarsbytet har skickats till klienten. Detta inkluderar bearbetningstiden för Application Gateway, *svarstiden för serverings sista byte*, den tid det tar för Application Gateway att skicka alla svar och *klient-RTT*.

- **RTT för klient**

  Genomsnittlig tur och returtid mellan klienter och Application Gateway.



Dessa mått kan användas för att avgöra om den observerade avmattningen beror på klientnätverket, Application Gateway-prestanda, serveranslutningsnätverket och serverservern TCP-stackmättnad, serveråtkomstprogramprestanda eller stor filstorlek.

Om det till exempel finns en topp i *svarstid för Serverda första byte svarstid* men *backend-anslutningstidstrenden* är stabil, kan man dra slutsatsen att programgatewayen till servertidsfördröjning och den tid det tar att upprätta anslutningen är stabil och spiken orsakas på grund av en ökning av svarstiden för backend-programmet. Å andra sidan, om spiken i *Backend första byte svarstid* är associerad med en motsvarande topp i *Backend-anslutningstid*, då kan det härledas att antingen nätverket mellan Application Gateway och backend server eller backend server TCP stacken har mättat. 

Om du märker en topp i *Serverda senaste byte svarstid* men *Backend första byte svarstiden* är stabil, då kan det dras slutsatsen att spiken beror på en större fil som begärs.

På samma sätt, om *application gateway total tid* har en topp men *Backend sista byte svarstiden* är stabil, då kan det antingen vara ett tecken på prestanda flaskhals vid Application Gateway eller en flaskhals i nätverket mellan klient och Application Gateway. Dessutom, om *klienten RTT* också har en motsvarande topp, då det indikerar att försämringen beror på nätverket mellan klienten och Application Gateway.

### <a name="application-gateway-metrics"></a>Mätvärden för programgateway

För Application Gateway är följande mått tillgängliga:

- **Mottagna Byte**

   Antal byte som tagits emot av Application Gateway från klienterna

- **Skickade byte**

   Antal byte som skickats av programgatewayen till klienterna

- **TLS-protokoll för klient**

   Antal TLS- och icke-TLS-begäranden som initierats av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokolldistribution filtrerar du efter dimensionen TLS Protocol.

- **Enheter för strömkapacitet**

   Antal kapacitetsenheter som förbrukas för att lasta trafik. Det finns tre bestämningsfaktorer för kapacitetsenhet - beräkningsenhet, beständiga anslutningar och dataflöde. Varje kapacitetsenhet består av högst: 1 beräkningsenhet eller 2500 beständiga anslutningar, eller 2,22 Mbit/s dataflöde.

- **Aktuella beräkningsenheter**

   Antal processorkapacitet som förbrukas. Faktorer som påverkar beräkningsenhet är TLS-anslutningar/sek, URL Skriv om beräkningar och WAF-regelbearbetning. 

- **Aktuella anslutningar**

   Det totala antalet samtidiga anslutningar som är aktiva från klienter till Application Gateway
   
- **Uppskattade fakturerade kapacitetsenheter**

  Med v2 SKU drivs prismodellen av förbrukning. Kapacitetsenheter mäter förbrukningsbaserad kostnad som debiteras utöver den fasta kostnaden. *Uppskattade fakturerade kapacitetsenheter* anger antalet kapacitetsenheter som beräknas med faktureringen. Detta beräknas som det större värdet mellan *strömkapacitetsenheter* (kapacitetsenheter som krävs för att belastningsbalansera trafiken) och *enheter med fast fakturerbar kapacitet* (enheter med minimal kapacitet hålls etablerade).

- **Misslyckade begäranden**

  Antal misslyckade begäranden som Application Gateway har tjänat. Antalet begäranden kan filtreras ytterligare för att visa antal per varje/specifik serversida pool-http inställningskombination.
   
- **Enheter med fast utbytbar kapacitet**

  Det minsta antalet kapacitetsenheter som hålls etablerade enligt inställningen *för minsta skalningsenheter* (en instans översätts till 10 kapacitetsenheter) i application gateway-konfigurationen.
   
 - **Nya anslutningar per sekund**

   Genomsnittligt antal nya TCP-anslutningar per sekund som upprättats från klienter till Application Gateway och från Application Gateway till backend-medlemmar.


- **Svarsstatus**

   HTTP-svarsstatus som returneras av Application Gateway. Responsstatuskodsfördelningen kan kategoriseras ytterligare för att visa svar i kategorierna 2xx, 3xx, 4xx och 5xx.

- **Dataflöde**

   Antal byte per sekund som Application Gateway har tjänat

- **Totalt antal begäranden**

   Antal lyckade begäranden som Application Gateway har tjänat. Antalet begäranden kan filtreras ytterligare för att visa antal per varje/specifik serversida pool-http inställningskombination.

### <a name="backend-metrics"></a>Svar på backend

För Application Gateway är följande mått tillgängliga:

- **Svarsstatus för backend**

  Antal HTTP-svarsstatuskoder som returneras av backends. Detta inkluderar inte några svarskoder som genereras av Application Gateway. Responsstatuskodsfördelningen kan kategoriseras ytterligare för att visa svar i kategorierna 2xx, 3xx, 4xx och 5xx.

- **Antal felfria värdar**

  Antalet serverd som bestäms friska av hälsosonden. Du kan filtrera på en per serverad pool bas för att visa antalet felfria värdar i en viss serverda pool.

- **Antal värdar med fel**

  Antalet serverd som bedöms vara ohälsosamt av hälsoavsökningen. Du kan filtrera på en per serverad pool bas för att visa antalet ohälsosamma värdar i en viss serverda pool.
  
- **Begäranden per minut och felfri värd**

  Det genomsnittliga antalet begäranden som tas emot av varje felfri medlem i en backend-pool på en minut. Du måste ange backend-poolen med *dimensionen BackendPool HttpSettings.*  
  

## <a name="metrics-supported-by-application-gateway-v1-sku"></a>Mått som stöds av Application Gateway V1 SKU

### <a name="application-gateway-metrics"></a>Mätvärden för programgateway

För Application Gateway är följande mått tillgängliga:

- **CPU-användning**

  Visar användningen av de processorer som tilldelats programgatewayen.  Under normala förhållanden bör CPU-användningen inte regelbundet överstiga 90 %, eftersom detta kan orsaka svarstid på de webbplatser som finns bakom Application Gateway och störa klientupplevelsen. Du kan indirekt styra eller förbättra CPU-användningen genom att ändra konfigurationen av Application Gateway genom att öka instansantalet eller genom att flytta till en större SKU-storlek eller göra båda.

- **Aktuella anslutningar**

  Antal aktuella anslutningar som upprättats med Application Gateway

- **Misslyckade begäranden**

  Antal misslyckade begäranden som Application Gateway har tjänat. Antalet begäranden kan filtreras ytterligare för att visa antal per varje/specifik serversida pool-http inställningskombination.

- **Svarsstatus**

  HTTP-svarsstatus som returneras av Application Gateway. Responsstatuskodsfördelningen kan kategoriseras ytterligare för att visa svar i kategorierna 2xx, 3xx, 4xx och 5xx.

- **Dataflöde**

  Antal byte per sekund som Application Gateway har tjänat

- **Totalt antal begäranden**

  Antal lyckade begäranden som Application Gateway har tjänat. Antalet begäranden kan filtreras ytterligare för att visa antal per varje/specifik serversida pool-http inställningskombination.

- **Antal blockerade begäranden om webbprogram**
- **Distribution av blockerade begäranden för webbprogram**
- **Regeldistribution av brandväggssumtal för webbprogram**

### <a name="backend-metrics"></a>Svar på backend

För Application Gateway är följande mått tillgängliga:

- **Antal felfria värdar**

  Antalet serverd som bestäms friska av hälsosonden. Du kan filtrera på en per serverad pool bas för att visa antalet felfria värdar i en viss serverda pool.

- **Antal värdar med fel**

  Antalet serverd som bedöms vara ohälsosamt av hälsoavsökningen. Du kan filtrera på en per serverad pool bas för att visa antalet ohälsosamma värdar i en viss serverda pool.

## <a name="metrics-visualization"></a>Visualisering av mätvärden

Bläddra till en programgateway under **Övervaka** välj **mått**. Om du vill visa de tillgängliga värdena väljer du listrutan **MÅTT**.

I följande bild visas ett exempel med tre mått som visats under de senaste 30 minuterna:

[![](media/application-gateway-diagnostics/figure5.png "Metric view")](media/application-gateway-diagnostics/figure5-lb.png#lightbox)

Information om hur du ser en aktuell lista över mått finns [i Mått som stöds med Azure Monitor](../azure-monitor/platform/metrics-supported.md).

### <a name="alert-rules-on-metrics"></a>Varningsregler för mått

Du kan starta varningsregler baserat på mått för en resurs. En avisering kan till exempel anropa en webhook eller skicka e-post till en administratör om dataflödet för programgatewayen är över, under eller vid ett tröskelvärde under en angiven period.

I följande exempel går du igenom att skapa en aviseringsregel som skickar ett e-postmeddelande till en administratör efter att dataflödet har överskrider ett tröskelvärde:

1. välj **Lägg till måttavisering** för att öppna sidan **Lägg till regel.** Du kan också nå den här sidan från statistiksidan.

   ![Knappen "Lägg till måttvarning"][6]

2. På sidan **Lägg till regel** fyller du i avsnitten namn, villkor och meddelande och väljer **OK**.

   * I **villkorväljaren** väljer du ett av de fyra värdena: **Större än**, **Större än eller lika**, Mindre **än**, eller Mindre än eller **lika med**.

   * I **periodväljaren** väljer du en punkt från fem minuter till sex timmar.

   * Om du väljer **E-postägare, deltagare och läsare**kan e-postmeddelandet vara dynamiskt baserat på de användare som har åtkomst till resursen. Annars kan du ange en kommaavgränsad lista över användare i rutan **Ytterligare administratörs-e-post.**

   ![Sidan Lägg till regel][7]

Om tröskelvärdet överskrids kommer ett e-postmeddelande som liknar det i följande bild:

![E-post för bruten tröskel][8]

En lista med aviseringar visas när du har skapat en måttavisering. Det ger en översikt över alla varningsregler.

![Lista över aviseringar och regler][9]

Mer information om aviseringar finns i [Ta emot aviseringar](../monitoring-and-diagnostics/insights-receive-alert-notifications.md).

Om du vill förstå mer om webhooks och hur du kan använda dem med aviseringar besöker du [Konfigurera en webhook på en Azure-måttavisering](../azure-monitor/platform/alerts-webhooks.md).

## <a name="next-steps"></a>Nästa steg

* Visualisera räknar- och händelseloggar med hjälp av [Azure Monitor-loggar](../azure-monitor/insights/azure-networking-analytics.md).
* [Visualisera din Azure-aktivitetslogg med Power](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) BI-blogginlägg.
* [Visa och analysera Azure-aktivitetsloggar i Power BI och mer](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) blogginlägg.

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
