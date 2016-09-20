<properties
   pageTitle="QoS-krav för ExpressRoute | Microsoft Azure"
   description="Den här sidan innehåller detaljerade krav för att konfigurera och hantera QoS för ExpressRoute-kretsar."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""/>
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/19/2016"
   ms.author="cherylmc"/>

# QoS-krav för ExpressRoute

Skype för företag har olika arbetsbelastningar som kräver särskild QoS-behandling. Om du tänker använda rösttjänster via ExpressRoute bör du följa kraven som beskrivs nedan.

![](./media/expressroute-qos/expressroute-qos.png)

>[AZURE.NOTE] QoS-kraven gäller endast för Microsoft-peering. De DSCP-värden i din nätverkstrafik som togs emot på Azures offentliga peering och Azure privata peering kommer att återställas till 0. 

Följande tabell innehåller en lista över DSCP-markeringar som används av Skype för företag. Se [Hantera QoS för Skype för företag](https://technet.microsoft.com/library/gg405409.aspx) för mer information.

| **Trafikklass** | **Behandling (DSCP-markering)** | **Arbetsbelastningar i Skype för företag** |
|---|---|---|
| **Röst** | EF (46) | Skype-/Lync-röst |
| **Interaktiv** | AF41 (34) | Video |
|   | AF21 (18) | Appdelning | 
|   | CS3 (24) | SIP-signalering |
| **Standard** | AF11 (10) | Filöverföring|
|   | CS0 (0) | Annat| 


- Du bör klassificera arbetsbelastningarna och markera rätt DSCP-värden. Följ riktlinjerna [här](https://technet.microsoft.com/library/gg405409.aspx) om hur du anger DSCP-markeringar i nätverket.

- Du bör konfigurera och ge stöd för flera QoS-köer i nätverket. Röst måste vara en fristående klass och få den EF-behandling som anges i RFC 3246. 

- Du kan bestämma kömekanism, princip för överbelastningsidentifiering samt bandbreddsallokering per trafikklass. Men DSCP-markeringen för arbetsbelastningar i Skype för företag måste bevaras. Om du använder DSCP-markeringar som inte visas ovan, t.ex. AF31 (26), måste du skriva DSCP-värdet som 0 innan paketet skickas till Microsoft. Microsoft skickar endast paket som har markerats med DSCP-värdet i ovanstående tabellen. 

## Nästa steg

- Se kraven för [Routning](expressroute-routing.md) och [NAT](expressroute-nat.md).
- Se följande länkar för att konfigurera ExpressRoute-anslutningen.

    - [Skapa en ExpressRoute-krets](expressroute-howto-circuit-classic.md)
    - [Konfigurera routning](expressroute-howto-routing-classic.md)
    - [Länka ett VNet till en ExpressRoute-krets](expressroute-howto-linkvnet-classic.md)



<!--HONumber=sep16_HO1-->


