---
title: Azure Event Grid Premium-och Basic-nivåer
description: I den här artikeln beskrivs skillnaden mellan Azure Event Grid Premium-och Basic-nivåer och när du använder dem
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79300720"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid Premium-och Basic-nivåer
Azure Event Grid har två nivåer: **Premium** och **Basic**. På Basic-nivån används förbrukningen eller priset för betala per användning. Du får alla de grundläggande pub/underverktyg som du behöver för att använda Event Grid för händelse drivna programmerings modeller. Premium-nivån tar detta steg ytterligare med säkerhetsfunktioner riktade mot företaget. Premium-nivån är i tidig för **hands version** med många av dess funktioner fortfarande under utveckling.

## <a name="overview"></a>Översikt
Alla anpassade ämnen och domäner i Event Grid tillhör antingen Basic-nivån eller Premium-nivån. Från och med `2020-04-01-preview` API-versionen kan du välja den önskade nivån som en del av att skapa ett ämne eller en domän. Standardvärdet är Basic-nivån. Ämnen och domäner som skapats med äldre API-versioner som standard till Basic-nivån. Om du vill ändra pris nivån för dina ämnen och domäner, se [Uppdatera nivån för ämnen och domäner](update-tier.md).

## <a name="capabilities-and-features"></a>Funktioner och funktioner

I följande tabell beskrivs skillnaderna mellan nivåerna:

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| Regler för IP-brandvägg för ingångs händelser                          | Förhandsversion  | Förhandsversion |
| Service märken för utgående                                | Förhandsversion  | Förhandsversion |
| VNet-integrering med privat slut punkt i ingress          | Inte tillgängligt   | Förhandsversion |

## <a name="availability"></a>Tillgänglighet
Under den första för hands versionen är Premium nivå ämnen och domäner med privat slut punkts integrering tillgängliga i följande regioner:

- USA, östra
- USA, västra 2
- USA, södra centrala

## <a name="pricing-and-quotas"></a>Priser och kvoter
Se [Event Grid priser](https://azure.microsoft.com/pricing/details/event-grid/) för pris information för att använda Basic-nivån. Priset på Premium-nivån är ännu inte meddelad och är kostnads fritt tills priset är tillgängligt.

De befintliga kvoterna för ämne och antal domäner och data flödet gäller för både Premium-och Basic-nivå resurser tills priset på Premium-nivån har annonser ATS.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- Information om hur du uppgraderar från Basic-nivån till Premium-nivån finns i artikeln [Uppdatera pris nivå](update-tier.md) . 
- Du kan konfigurera IP-brandväggen för din Event Grid resurs för att begränsa åtkomsten via det offentliga Internet från endast en Välj uppsättning IP-adresser eller IP-adressintervall. Stegvisa instruktioner finns i [Konfigurera brand vägg](configure-firewall.md).
- Du kan konfigurera privata slut punkter för att begränsa åtkomsten från enbart från valda virtuella nätverk. Stegvisa instruktioner finns i [Konfigurera privata slut punkter](configure-private-endpoints.md).