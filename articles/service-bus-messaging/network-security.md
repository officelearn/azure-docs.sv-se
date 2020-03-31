---
title: Nätverkssäkerhet för Azure Service Bus
description: I den här artikeln beskrivs nätverkssäkerhetsfunktioner som tjänsttaggar, IP-brandväggsregler, tjänstslutpunkter och privata slutpunkter.
services: service-bus-messaging
documentationcenter: .net
author: axisc
editor: spelluru
ms.service: service-bus-messaging
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: aschhab
ms.openlocfilehash: 95f8c2a3b47b59bab7df909be43dacdb1f9c58f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79479285"
---
# <a name="network-security-for-azure-service-bus"></a>Nätverkssäkerhet för Azure Service Bus 
I den här artikeln beskrivs hur du använder följande säkerhetsfunktioner med Azure Service Bus: 

- Tjänsttaggar
- REGLER för IP-brandväggen
- Slutpunkter för nätverkstjänsten
- Privata slutpunkter (förhandsgranskning)


## <a name="service-tags"></a>Tjänsttaggar
En tjänsttagg representerar en grupp IP-adressprefix från en viss Azure-tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras, vilket minimerar komplexiteten i frekventa uppdateringar av nätverkssäkerhetsregler. Mer information om tjänsttaggar finns i [Översikt över tjänsttaggar](../virtual-network/service-tags-overview.md).

Du kan använda tjänsttaggar för att definiera nätverksåtkomstkontroller i [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Använd tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (till exempel **ServiceBus**) i lämpligt *käll-* eller *målfält* för en regel kan du tillåta eller neka trafik för motsvarande tjänst.

| Tjänsttagg | Syfte | Kan du använda inkommande eller utgående? | Kan vara regional? | Kan du använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ServiceBus** | Azure Service Bus-trafik som använder Premium-tjänstnivån. | Utgående | Ja | Ja |


## <a name="ip-firewall"></a>IP-brandvägg 
Som standard är Service Bus-namnområden tillgängliga från internet så länge begäran levereras med giltig autentisering och auktorisering. Med IP-brandväggen kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adressintervall i [CIDR-notation (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Den här funktionen är användbar i scenarier där Azure Service Bus endast ska vara tillgänglig från vissa välkända platser. Med brandväggsregler kan du konfigurera regler för att acceptera trafik som kommer från specifika IPv4-adresser. Om du till exempel använder Service Bus med [Azure Express Route][express-route] kan du skapa en **brandväggsregel** för att tillåta trafik från endast dina lokala IP-adresser eller adresser till en FÖRETAGS NAT-gateway. 

IP-brandväggsreglerna tillämpas på servicebussnamnområdesnivå. Därför gäller reglerna för alla anslutningar från klienter som använder protokoll som stöds. Alla anslutningsförsök från en IP-adress som inte matchar en tillåten IP-regel på tjänstbussens namnområde avvisas som obehöriga. Svaret nämner inte IP-regeln. IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden acceptera eller avvisa.

Mer information finns i [Så här konfigurerar du IP-brandväggen för ett servicebussnamnområde](service-bus-ip-filtering.md)

## <a name="network-service-endpoints"></a>Slutpunkter för nätverkstjänsten
Integreringen av Tjänsten Buss med [VNet-tjänstslutpunkter (Virtual Network)](service-bus-service-endpoints.md) möjliggör säker åtkomst till meddelandefunktioner från arbetsbelastningar som virtuella datorer som är bundna till virtuella nätverk, med nätverkstrafiksökvägen som skyddas i båda ändar.

När den har konfigurerats för att vara bunden till minst en slutpunkt för virtuella nätverksundernätstjänsten accepterar respektive tjänstbussnamnområde inte längre trafik från var som helst utan auktoriserade virtuella nätverk. Från det virtuella nätverksperspektivet konfigurerar bindning av ett tjänstbussnamnområde till en tjänstslutpunkt en isolerad nätverkstunnel från det virtuella nätverksundernätet till meddelandetjänsten.

Resultatet är en privat och isolerad relation mellan arbetsbelastningarna som är bundna till undernätet och respektive Service Bus-namnområde, trots att den observerbara nätverksadressen för slutpunkten för meddelandetjänsten är i ett offentligt IP-intervall.

> [!IMPORTANT]
> Virtuella nätverk stöds endast i [premium-nivå](service-bus-premium-messaging.md) servicebussnamnområden.
> 
> När du använder VNet-tjänstslutpunkter med Service Bus bör du inte aktivera dessa slutpunkter i program som blandar standard- och premiumnivåservicebussnamnområden. Eftersom standardnivån inte stöder virtuella nätverk. Slutpunkten är begränsad till endast namnområden på Premium-nivån.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhetsscenarier som aktiveras av VNet-integrering 

Lösningar som kräver tät och uppdelad säkerhet, och där virtuella nätverksundernät tillhandahåller segmentering mellan de uppdelade tjänsterna, behöver i allmänhet fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Varje omedelbar IP-väg mellan avdelningarna, inklusive de som transporterar HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätskiktet uppåt. Meddelandetjänster tillhandahåller helt isolerade kommunikationsvägar, där meddelanden till och med skrivs till disk när de övergår mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som båda är bundna till samma Service Bus-instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive nätverksisoleringsgränsintegritet bevaras.
 
Det innebär att dina säkerhetskänsliga molnlösningar inte bara får tillgång till Azures branschledande tillförlitliga och skalbara asynkrona meddelandefunktioner, utan de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säkra lösningsfack som är i sig säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS-säkrade socketprotokoll.

### <a name="bind-service-bus-to-virtual-networks"></a>Bind servicebuss till virtuella nätverk

*Virtuella nätverksregler* är brandväggssäkerhetsfunktionen som styr om Azure Service Bus-servern accepterar anslutningar från ett visst virtuellt nätverksundernät.

Att binda ett servicebussnamnområde till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en slutpunkt för **tjänsten Virtuellt nätverk** i ett virtuellt nätverk undernät och aktivera den för **Microsoft.ServiceBus** enligt beskrivningen i [tjänstslutpunktsöversikten](service-bus-service-endpoints.md). När du har lagt till tjänstslutpunkten binder du servicebussens namnområde till den med en **virtuell nätverksregel**.

Regeln om virtuellt nätverk är en associering av servicebussnamnområdet med ett virtuellt nätverksundernät. Medan regeln finns beviljas alla arbetsbelastningar som är bundna till undernätet åtkomst till servicebussnamnområdet. Service Bus själv upprättar aldrig utgående anslutningar, behöver inte få åtkomst och beviljas därför aldrig åtkomst till ditt undernät genom att aktivera den här regeln.

Mer information finns i [Så här konfigurerar du slutpunkter för virtuella nätverkstjänster för ett tjänstbussnamnområde](service-bus-service-endpoints.md)

## <a name="private-endpoints"></a>Privata slutpunkter

Azure Private Link Service gör att du kan komma åt Azure-tjänster (till exempel Azure Service Bus, Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-/partnertjänster via en **privat slutpunkt** i ditt virtuella nätverk.

En privat slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gateways, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontrollen.

Mer information finns i [Vad är Azure Private Link?](../private-link/private-link-overview.md)

> [!NOTE]
> Den här funktionen stöds med **premiumnivån** för Azure Service Bus. Mer information om premiumnivån finns i artikeln [Service Bus Premium och Standard meddelandenivåer.](service-bus-premium-messaging.md)
>
> Den här funktionen är för närvarande i **förhandsgranskning**. 


Mer information finns i [Så här konfigurerar du privata slutpunkter för ett servicebussnamnområde](private-link-service.md)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera IP-brandvägg för ett tjänstbussnamnområde](service-bus-ip-filtering.md)
- [Konfigurera slutpunkter för virtuella nätverkstjänster för ett tjänstbussnamnområde](service-bus-service-endpoints.md)
- [Konfigurera privata slutpunkter för ett servicebussnamnområde](private-link-service.md)
