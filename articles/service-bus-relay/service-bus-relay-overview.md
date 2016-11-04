---
title: Översikt över Service Bus Relay | Microsoft Docs
description: Översikt över Service Bus-vidarebefordran
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 09/01/2016
ms.author: sethm

---
# <a name="overview-of-service-bus-relay"></a>Översikt över Service Bus Relay
En central komponent i Service Bus är en centraliserad (men med hög belastningsutjämning) tjänst för *vidarebefordran* som gör att du kan skapa hybridprogram som körs i både ett Azure-datacenter och i din egen lokala företagsmiljö.  Service Bus Relay stöder en mängd olika transportprotokoll och webbtjänststandarder. Detta inkluderar SOAP, WS-*, och även REST. Tjänsten underlättar för dina hybridprogram genom att på ett säkert sätt exponera tjänster för Windows Communication Foundation (WCF) i ett företagsnätverk mot det offentliga molnet, utan att behöva öppna en brandväggsanslutning eller kräva störande ändringar i företagets nätverksinfrastruktur. 

![Relay-begrepp](./media/service-bus-relay-overview/sb-relay-01.png)

Den vidarebefordrande tjänsten stöder traditionella envägsmeddelanden, fråga-och-svar-meddelanden och peer-to-peer-meddelanden. Den stöder även händelsedistribution på Internet-skala för att möjliggöra publicerings- och prenumerationsscenarier och dubbelriktad socketkommunikation för ökad effektivitet punkt till punkt. 

I mönstret vidarebefordrande meddelandetjänster ansluter en lokal tjänst till den vidarebefordrande tjänsten via en utgående port och skapar en dubbelriktad socket för kommunikation som är kopplad till en viss rendezvous-adress. Klienten kan sedan kommunicera med den lokala tjänsten genom att skicka meddelanden till den vidarebefordrande tjänsten med rendezvous-adressen som mål. Den vidarebefordrande tjänsten kommer sedan att ”vidarebefordra” meddelanden till den lokala tjänsten via den dubbelriktade socketen som redan är upprättad. Klienten behöver ingen direkt anslutning till den lokala tjänsten och behöver inte heller veta var den finns. Den lokala tjänsten behöver inte ha några öppna ingående portar i brandväggen.

Du upprättar anslutningen mellan din lokala tjänst och den vidarebefordrande tjänsten med hjälp av en uppsättning ”vidarebefordrande” WCF-bindningar. I bakgrunden mappas vidarebefordringsbindningarna till nya transportbindningselement som är utformade för att skapa WCF-kanalkomponenter som integreras med Service Bus i molnet. 

## <a name="next-steps"></a>Nästa steg
Mer information om Service Bus-vidarebefordran finns i följande avsnitt.

* [Arkitekturell översikt för Azure Service Bus](../service-bus/service-bus-fundamentals-hybrid-solutions.md)
* [Använd Service Bus Relay-tjänsten](service-bus-dotnet-how-to-use-relay.md)

<!--HONumber=Oct16_HO3-->


