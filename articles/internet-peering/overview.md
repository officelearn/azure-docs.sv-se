---
title: Konfigurera peering med Microsoft
titleSuffix: Azure
description: Översikt över peering
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: overview
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 576bc3e37711851acd7d6c7ac811a10e40080710
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75908922"
---
# <a name="internet-peering-overview"></a>Översikt över Internet-peering

Peering är sammankopplingen mellan Microsofts globala nätverk (AS8075) och ditt nätverk i syfte att utbyta internettrafik från/till Microsofts onlinetjänster och Microsoft Azure Services. Operatörer eller tjänsteleverantörer kan begära att få kontakt med Microsoft på någon av våra Edge-platser. Varje begäran granskas av Microsoft för att säkerställa att den följer vår peering-policy. Du kan konfigurera en peering med Microsoft-nätverk på två sätt:

* **Direkt peering:**

    Peering upprättas via direkta fysiska anslutningar mellan Microsoft-nätverk på en Microsoft Edge och ditt nätverk. BGP-sessioner konfigureras över dessa anslutningar enligt vår routningsprincip och med hjälp av förförhandsförhandlade avtal. Detta kallas även PNI.

* **Exchange peering:**

    Detta avser vanliga offentliga peering-anslutningar på Internet Exchanges (IX). De fysiska anslutningarna mellan Microsoft-nätverket och nätverket sker via växelstrukturen som drivs av IX. BGP-sessioner konfigureras med IP-utrymme som tillhandahålls av IX.

## <a name="benefits-of-peering-with-microsoft"></a>Fördelar med peering med Microsoft
* Sänk dina transitkostnader genom att leverera Microsoft-trafik med hjälp av peering med Microsoft.
* Förbättra prestanda för dina kunder genom att minska nätverkshopp och svarstid till Microsoft Edge-nätverket.
* Skydda kundtrafiken mot fel i nätverkets eller transitleverantörens nätverk genom att peering med Microsoft på redundanta platser.
* Lär dig prestandamått om dina peering-anslutningar och använd insikter för att felsöka nätverket.

## <a name="benefits-of-using-azure-to-set-up-peering"></a>Fördelar med att använda Azure för att konfigurera peering

Du kan begära peering med Microsoft med Azure PowerShell eller portal. Peering-uppsättning på detta sätt hanteras som en Azure-resurs och ger följande fördelar:
* Förenklade och automatiserade steg för att konfigurera och hantera peering med Microsoft.
* Snabbt och enkelt sätt att visa och hantera alla dina peerings på ett ställe.
* Spåra status- och bandbreddsdata för alla anslutningar.
* Du kan använda samma prenumeration för att komma åt dina Azure Cloud Services.

Om du redan har etablerat peerings med Microsoft kallas de **äldre peerings**. Du kan välja att hantera sådana peerings som Azure-resurs för att dra nytta av ovanstående fördelar. Om du vill skicka en ny peering-begäran eller konvertera äldre peering till Azure-resurs följer du länkarna i avsnittet **Nästa steg** nedan.

## <a name="peering-policy"></a>Peering-policy
Microsoft har en selektiv men i allmänhet öppen peering-policy. Peers väljs ut baserat på prestanda, kapacitet och där det finns ömsesidiga fördelar, och omfattas av vissa tekniska, kommersiella och juridiska krav. Mer information finns i [peering-principen](policy.md).

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR
Vanliga frågor och svar om peering finns i [Internet peering - vanliga frågor](faqs.md).

## <a name="next-steps"></a>Nästa steg

* Om du vill veta mer om steg för att konfigurera Direkt peering med Microsoft följer du [Direktgenomspelning](walkthrough-direct-all.md)
* Om du vill veta mer om steg för att konfigurera Exchange-peering med Microsoft följer du [Exchange-peering-genomgång](walkthrough-exchange-all.md)
* Lär dig mer om de andra viktiga [nätverksfunktionerna](https://docs.microsoft.com/azure/networking/networking-overview) i Azure.
