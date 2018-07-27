---
title: Border anslutning integration Nätverksöverväganden för integrerade Azure Stack-system | Microsoft Docs
description: Lär dig vad du kan göra för att planera för datacenter kantlinje nätverksanslutning med flera noder Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282016"
---
# <a name="border-connectivity"></a>Kantlinje anslutning 
Nätverksplanering integration är en viktig nödvändig komponent för lyckad distribution av integrerade Azure Stack-system, åtgärden och hantering. Planera för kantlinje anslutning börjar genom att välja huruvida du använder dynamisk routning med BGP (border gateway protocol) eller inte. Detta kräver att tilldela en 16-bitars BGP autonomt systemnummer (offentliga eller privata) eller med statisk routning, där en statisk standardväg tilldelas till enheter kantlinje.

> [!IMPORTANT]
> Överst växlar rack (TOR) kräver Layer 3 överordnade länkar med Point-to-Point IP-adresser (/ 30 nätverk) konfigurerats på de fysiska gränssnitt. Det går inte för att använda Layer-2 överordnade länkar med TOR-växlar som stöder Azure Stack-åtgärder. 

## <a name="bgp-routing"></a>BGP-Routning
Med hjälp av ett dynamiskt routningprotokoll som BGP garanterar att datorn alltid är medveten om ändringar i nätverket och underlättar administration. 

I följande diagram visas annonsering av den privata IP-Adressen utrymme på TOR-växeln begränsas med en Prefixlistan. Listorna prefix nekar de privata IP-undernät och tillämpa det som en väg karta på anslutningen mellan TOR och kantlinjen.

Programvara Load Balancer (SLB) som körs i Azure Stack-lösningen peer-datorer till TOR-enheter så att den kan dynamiskt annonsera VIP-adresser.

För att säkerställa att användartrafik omedelbart och transparent återställs från fel, VPC eller MLAG konfigurerats mellan TOR-enheter tillåter användning av flera chassi länk network aggregering till värdar och HSRP eller VRRP som ger redundans för IP-nätverk.

![BGP-Routning](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statisk routning
Statisk routning kräver ytterligare konfiguration för att border-enheter. Det kräver mer manuella åtgärder och hantering samt omfattande analys innan ändrings- och problem som orsakas av ett konfigurationsfel kan ta längre tid att återställa beroende på ändringar som görs. Det är inte den rekommenderade metoden för routning, men det stöds.

Om du vill integrera Azure Stack i din nätverksmiljö med statisk routning, alla fyra fysiska länkar mellan kanten och TOR-enheten måste vara ansluten och kan inte garanteras hög tillgänglighet på grund av hur statisk routning fungerar.

Kantlinje enheten måste konfigureras med statiska vägar som pekar till enheterna i TOR P2P för trafik till det externa nätverket eller offentliga virtuella IP-adresser och nätverkets infrastruktur. Statiska vägar till BMC-nätverk krävs för distributionen. Kunder kan välja att lämna statiska vägar i kantlinje åtkomst till vissa resurser som finns i BMC-nätverket.  Lägga till statiska vägar till *växel infrastruktur* och *växla management* nätverk är valfritt.

TOR-enheter har konfigurerat en statisk standardväg skickar all trafik till enheterna som kantlinje. Det enda undantaget trafik till Standardregeln är för det privata utrymmet som blockeras med hjälp av en åtkomstkontrollista som tillämpas på TOR kantlinje anslutning.

Statisk routning gäller endast för överordnade länkar mellan växlarna TOR och kantlinje. Dynamisk BGP-routning används i racket eftersom det är ett viktigt verktyg för SLB och andra komponenter och inte kan inaktiveras eller tas bort.

![Statisk routning](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Transparent proxy
Om ditt datacenter kräver all trafik ska använda en proxyserver, måste du konfigurera en *transparent proxy* att bearbeta all trafik från rack att hantera det enligt principer för att separera trafiken mellan zoner i nätverket.

> [!IMPORTANT]
> Azure Stack-lösningen stöder inte normala webbproxyservrar.  

En transparent proxy (även kallat en spärra, infogade eller tvingad proxy) fångar upp normal kommunikation på nätverksnivå utan någon särskild klientkonfiguration. Klienter behöver inte vara medveten om proxyn.

![Transparent proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Nästa steg
[DNS-integrering](azure-stack-integrate-dns.md)