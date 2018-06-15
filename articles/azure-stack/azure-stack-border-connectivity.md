---
title: Border anslutningen integration Nätverksöverväganden för Azure-stacken integrerat system | Microsoft Docs
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
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 93dd609df90adac2c84ba8c62cf0d18f55a317bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919442"
---
# <a name="border-connectivity"></a>Kantlinje anslutning 
Planering för nätverket integrering är en viktig nödvändig komponent för lyckad distribution av Azure-stacken integrerat system, åtgärden och hantering. Kantlinje anslutningen planera börjar genom att välja om du vill använda dynamisk routning med border gateway protocol (BGP) eller inte. Detta kräver att tilldela ett 16-bitars BGP autonoma systemnummer (offentliga eller privata) eller med statisk routning, där en statisk standardväg tilldelas till enheter som kantlinje.

> [!IMPORTANT]
> Överkant växlar rack (TOR) kräver Layer 3 överordnade länkar med Point-to-Point IP-adresser (/ 30 nätverk) konfigurerats på de fysiska gränssnitt. Det går inte för att använda nivå 2 överordnade länkar med TOR-växlar som stöder Azure Stack-åtgärder. 

## <a name="bgp-routing"></a>BGP-Routning
Med hjälp av ett dynamiskt routningprotokoll som BGP garanterar att datorn alltid är medveten om ändringar i nätverket och underlättar administration. 

I följande diagram visas annonserar privata IP-utrymme på TOR-växeln begränsas med prefix-listan. Listorna prefix nekar privata IP-undernät och tillämpa det som en väg karta för anslutningen mellan TOR och kantlinje.

Den programvara belastningen belastningsutjämnare (SLB) körs i Azure-stacken lösningen peer-datorer till TOR-enheter så att den kan dynamiskt annonsera VIP-adresser.

För att säkerställa att användaraktiviteten omedelbart och transparent återställs från fel, Virtual PC eller MLAG konfigurerats mellan enheterna som TOR tillåter användning av flera chassi länken network aggregering till värdar och HSRP eller VRRP som ger redundans för IP-nätverk.

![BGP-Routning](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>statisk routning
Med statiska vägar lägger till fler fast configuration kantlinje och TOR-enheter. Det krävs noggrann analys innan några ändringar. Problem som orsakas av ett konfigurationsfel kan ta längre tid att återställa beroende på de ändringar som gjorts. Den är inte den rekommenderade metoden för routning, men det stöds.

Om du vill integrera Azure Stack i din nätverksmiljö med den här metoden, måste RAM-enhet konfigureras med statiska vägar som pekar på TOR-enheter för trafik till externa nätverk offentliga virtuella IP-adresser.

TOR-enheter måste konfigureras med en statisk standardväg skicka all trafik till enheter som kantlinje. Det enda undantaget trafik till den här regeln är för privat utrymmet som kommer att blockeras med hjälp av en åtkomstkontrollista som tillämpas på TOR kantlinje anslutning.

Allt annat ska vara samma som den första metoden. BGP dynamisk routning fortfarande används i racket eftersom det är ett viktigt verktyg för SLB och andra komponenter och inte kan inaktiveras eller tas bort.

![statisk routning](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Transparent proxy
Om ditt datacenter kräver all trafik till använder en proxyserver, måste du konfigurera en *transparent proxy* bearbeta all trafik från rack ska hanteras enligt principer för att separera trafiken mellan zoner i nätverket.

> [!IMPORTANT]
> Azure Stack-lösningen stöder inte normala webbproxyservrar.  

En transparent proxy (även kallat en fånga upp, infogade eller framtvingad proxy) fångar upp normal kommunikation på nätverksnivå utan någon särskild klientkonfiguration. Klienterna behöver inte vara medveten om förekomsten av proxyservern.

![Transparent proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Nästa steg
[DNS-integrering](azure-stack-integrate-dns.md)