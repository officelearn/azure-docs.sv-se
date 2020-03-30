---
title: Nätverkssäkerhet för Azure Event Hubs
description: I den här artikeln beskrivs hur du konfigurerar åtkomst från privata slutpunkter
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: spelluru
ms.openlocfilehash: ab85cdb2854de5c147c68afd8e4fe5e17ac2899b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477946"
---
# <a name="network-security-for-azure-event-hubs"></a>Nätverkssäkerhet för Azure Event Hubs 
I den här artikeln beskrivs hur du använder följande säkerhetsfunktioner med Azure Event Hubs: 

- Tjänsttaggar
- REGLER för IP-brandväggen
- Slutpunkter för nätverkstjänsten
- Privata slutpunkter (förhandsgranskning)


## <a name="service-tags"></a>Tjänsttaggar
En tjänsttagg representerar en grupp IP-adressprefix från en viss Azure-tjänst. Microsoft hanterar adressprefixen som omfattas av servicetag och uppdaterar automatiskt servicetag när adresserna ändras, vilket minimerar komplexiteten i frekventa uppdateringar av nätverkssäkerhetsregler. Mer information om tjänsttaggar finns i [Översikt över tjänsttaggar](../virtual-network/service-tags-overview.md).

Du kan använda tjänsttaggar för att definiera nätverksåtkomstkontroller i [nätverkssäkerhetsgrupper](../virtual-network/security-overview.md#security-rules) eller [Azure-brandväggen](../firewall/service-tags.md). Använd tjänsttaggar i stället för specifika IP-adresser när du skapar säkerhetsregler. Genom att ange tjänsttagnamnet (till exempel **EventHub**) i lämpligt *käll-* eller *målfält* för en regel kan du tillåta eller neka trafik för motsvarande tjänst.

| Tjänsttagg | Syfte | Kan du använda inkommande eller utgående? | Kan vara regional? | Kan du använda med Azure-brandväggen? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **EventHub** | Azure-händelsehubbar. | Utgående | Ja | Ja |


## <a name="ip-firewall"></a>IP-brandvägg 
Som standard är Event Hubs namnområden tillgängliga från internet så länge begäran levereras med giltig autentisering och auktorisering. Med IP-brandväggen kan du begränsa den ytterligare till endast en uppsättning IPv4-adresser eller IPv4-adressintervall i [CIDR-notation (Classless Inter-Domain Routing).](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing)

Den här funktionen är användbar i scenarier där Azure Event Hubs endast bör vara tillgängliga från vissa välkända platser. Med brandväggsregler kan du konfigurera regler för att acceptera trafik som kommer från specifika IPv4-adresser. Om du till exempel använder Event Hubs med [Azure Express Route][express-route] kan du skapa en **brandväggsregel** för att tillåta trafik från endast dina lokala IP-adresser för infrastruktur. 

IP-brandväggsreglerna tillämpas på namnområdesnivån Event Hubs. Därför gäller reglerna för alla anslutningar från klienter som använder protokoll som stöds. Alla anslutningsförsök från en IP-adress som inte matchar en tillåten IP-regel på namnområdet Event Hubs avvisas som obehörigt. Svaret nämner inte IP-regeln. IP-filterregler tillämpas i ordning och den första regeln som matchar IP-adressen bestämmer åtgärden acceptera eller avvisa.

Mer information finns i [Så här konfigurerar du IP-brandväggen för en händelsehubb](event-hubs-ip-filtering.md)

## <a name="network-service-endpoints"></a>Slutpunkter för nätverkstjänsten
Integreringen av eventhubbar med [VNet-tjänstslutpunkter för virtuellt nätverk][vnet-sep] möjliggör säker åtkomst till meddelandefunktioner från arbetsbelastningar som virtuella datorer som är bundna till virtuella nätverk, där nätverkstrafiksökvägen är skyddad på båda ändar.

När den har konfigurerats för att bindas till minst en slutpunkt för virtuella nätverksundernätstjänsten accepterar respektive namnområde för eventhubbar inte längre trafik från var som helst utan auktoriserade undernät i virtuella nätverk. Från det virtuella nätverksperspektivet konfigurerar bindning av ett namnområde för eventhubbar till en tjänstslutpunkt en isolerad nätverkstunnel från det virtuella nätverksundernätet till meddelandetjänsten. 

Resultatet är en privat och isolerad relation mellan arbetsbelastningarna som är bundna till undernätet och respektive händelsehubbarnamnområde, trots att den observerbara nätverksadressen för slutpunkten för meddelandetjänsten är i ett offentligt IP-intervall. Det finns ett undantag från det här beteendet. Om du aktiverar en tjänstslutpunkt som `denyall` standard aktiveras regeln i [IP-brandväggen](event-hubs-ip-filtering.md) som är associerad med det virtuella nätverket. Du kan lägga till specifika IP-adresser i IP-brandväggen för att aktivera åtkomst till den offentliga slutpunkten för händelsehubben. 

> [!IMPORTANT]
> Virtuella nätverk stöds på **standardnivå** och **dedikerade** nivåer för Event Hubs. Det stöds inte i den **grundläggande** nivån.

### <a name="advanced-security-scenarios-enabled-by-vnet-integration"></a>Avancerade säkerhetsscenarier som aktiveras av VNet-integrering 

Lösningar som kräver tät och uppdelad säkerhet, och där virtuella nätverksundernät tillhandahåller segmentering mellan de uppdelade tjänsterna, behöver fortfarande kommunikationsvägar mellan tjänster som finns i dessa avdelningar.

Varje omedelbar IP-väg mellan avdelningarna, inklusive de som transporterar HTTPS över TCP/IP, medför risk för utnyttjande av sårbarheter från nätskiktet uppåt. Meddelandetjänster tillhandahåller isolerade kommunikationsvägar, där meddelanden till och med skrivs till disk när de övergår mellan parterna. Arbetsbelastningar i två olika virtuella nätverk som båda är bundna till samma Event Hubs-instans kan kommunicera effektivt och tillförlitligt via meddelanden, medan respektive nätverksisoleringsgränsintegritet bevaras.
 
Det innebär att dina säkerhetskänsliga molnlösningar inte bara får tillgång till Azures branschledande tillförlitliga och skalbara asynkrona meddelandefunktioner, utan de kan nu använda meddelanden för att skapa kommunikationsvägar mellan säkra lösningsfack som är i sig säkrare än vad som kan uppnås med alla peer-to-peer-kommunikationsläge, inklusive HTTPS och andra TLS-säkrade socketprotokoll.

### <a name="bind-event-hubs-to-virtual-networks"></a>Binda händelsehubbar till virtuella nätverk

**Virtuella nätverksregler** är brandväggssäkerhetsfunktionen som styr om namnområdet Azure Event Hubs accepterar anslutningar från ett visst virtuellt nätverksundernät.

Att binda ett namnområde för händelsehubbar till ett virtuellt nätverk är en tvåstegsprocess. Du måste först skapa en slutpunkt för en **virtuell nätverkstjänst** i ett virtuellt nätverks undernät och aktivera den för **Microsoft.EventHub** enligt beskrivningen i [tjänstslutpunktsöversikt][vnet-sep]-artikeln. När du har lagt till tjänstslutpunkten binder du namnområdet Event Hubs till den med en **virtuell nätverksregel**.

Regeln om virtuellt nätverk är en associering av namnområdet Event Hubs med ett virtuellt nätverksundernät. Medan regeln finns beviljas alla arbetsbelastningar som är bundna till undernätet åtkomst till namnområdet Event Hubs. Event Hubs själv upprättar aldrig utgående anslutningar, behöver inte få åtkomst och beviljas därför aldrig åtkomst till ditt undernät genom att aktivera den här regeln.

Mer information finns i [Så här konfigurerar du slutpunkter för virtuella nätverkstjänster för en händelsehubb](event-hubs-service-endpoints.md)

## <a name="private-endpoints"></a>Privata slutpunkter

[Azure Private Link-tjänsten](../private-link/private-link-overview.md) gör att du kan komma åt Azure Services (till exempel Azure Event Hubs, Azure Storage och Azure Cosmos DB) och Azure-värdbaserade kund-/partnertjänster via en **privat slutpunkt** i ditt virtuella nätverk.

En privat slutpunkt är ett nätverksgränssnitt som ansluter dig privat och säkert till en tjänst som drivs av Azure Private Link. Den privata slutpunkten använder en privat IP-adress från ditt virtuella nätverk, vilket effektivt för in tjänsten i ditt virtuella nätverk. All trafik till tjänsten kan dirigeras via den privata slutpunkten, så inga gateways, NAT-enheter, ExpressRoute- eller VPN-anslutningar eller offentliga IP-adresser behövs. Trafik mellan ditt virtuella nätverk och tjänsten passerar över Microsofts stamnätverk, vilket eliminerar exponering från det offentliga Internet. Du kan ansluta till en instans av en Azure-resurs, vilket ger dig den högsta detaljnivån i åtkomstkontrollen.

> [!NOTE]
> Den här funktionen stöds endast med den **dedikerade** nivån. Mer information om den dedikerade nivån finns i [Översikt över dedikerade eventhubbar](event-hubs-dedicated-overview.md). 
>
> Den här funktionen är för närvarande i **förhandsgranskning**. 


Mer information finns i [Så här konfigurerar du privata slutpunkter för en händelsehubb](private-link-service.md)


## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- [Konfigurera IP-brandvägg för en händelsehubb](event-hubs-ip-filtering.md)
- [Konfigurera slutpunkter för virtuella nätverkstjänster för en händelsehubb](event-hubs-service-endpoints.md)
- [Konfigurera privata slutpunkter för en händelsehubb](private-link-service.md)