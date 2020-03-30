---
title: Azure Event Grid Premium och grundläggande nivåer
description: I den här artikeln beskrivs skillnaden mellan Azure Event Grid Premium och Basic-nivåerna och när du ska använda varje
services: event-grid
author: banisadr
ms.service: event-grid
ms.topic: overview
ms.date: 03/11/2020
ms.author: babanisa
ms.openlocfilehash: 7ae73337ccccc2b7d2402cfa2a54ae93552d2465
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79300720"
---
# <a name="azure-event-grid-premium-and-basic-tiers"></a>Azure Event Grid premium och grundläggande nivåer
Azure Event Grid har två nivåer: **Premium** och **Basic**. Den grundläggande nivån använder förbrukning eller användningsbaserad prissättning. Det ger dig alla de grundläggande pub / sub verktyg du behöver för att använda Event Grid för händelse-driven programmering modeller. Premium-nivån tar detta ett steg längre med säkerhetsfunktioner som riktar sig till företaget. Premium-nivån är i tidig **förhandsversion** med många av dess funktioner fortfarande under utveckling.

## <a name="overview"></a>Översikt
Alla anpassade ämnen och domäner i Event Grid tillhör antingen grundläggande nivå eller premiumnivå. Från `2020-04-01-preview` och med API-versionen kan du välja önskad nivå som en del av att skapa ett ämne eller en domän. Standardvärdet är grundläggande nivå. De ämnen och domäner som skapats med äldre API-versioner som standard är den grundläggande nivån. Information om hur du ändrar prisnivån för dina ämnen och domäner finns i [Så här uppdaterar du nivå för ämnen och domäner](update-tier.md).

## <a name="capabilities-and-features"></a>Funktioner och funktioner

I följande tabell beskrivs skillnader mellan nivåerna:

|       &nbsp;                                           | Basic           | Premium        |
| ------------------------------------------------------ | --------------- | -------------- |
| IP-brandväggsregler för inträngning                          | Förhandsversion  | Förhandsversion |
| Tjänsttaggar för utgående                                | Förhandsversion  | Förhandsversion |
| Integrering av den privata slutpunkten vnet vid inträngning          | Inte tillgängligt   | Förhandsversion |

## <a name="availability"></a>Tillgänglighet
Under den första förhandsversionen är avsnitt och domäner på premiumnivå med integrering av privata slutpunkter tillgängliga i följande regioner:

- USA, östra
- USA, västra 2
- USA, södra centrala

## <a name="pricing-and-quotas"></a>Priser och kvoter
Se Priser för [Event Grid](https://azure.microsoft.com/pricing/details/event-grid/) för prisinformation för att använda den grundläggande nivån. Premium-nivåprissättningen är ännu inte aviserad och är gratis tills prissättningen är tillgänglig.

De befintliga kvoterna för ämne och domänantal och dataflöde gäller både premium- och basnivåresurser tills prissättning på premiumnivå tillkännages.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar:

- Information om hur du uppgraderar från grundläggande nivå till premiumnivå finns i artikeln [Uppdatera prisnivå.](update-tier.md) 
- Du kan konfigurera IP-brandväggen för din Event Grid-resurs för att begränsa åtkomsten via det offentliga internet från endast en utvald uppsättning IP-adresser eller IP-adressintervall. Steg-för-steg-instruktioner finns i [Konfigurera brandvägg](configure-firewall.md).
- Du kan konfigurera privata slutpunkter för att begränsa åtkomsten från endast valda virtuella nätverk. Steg-för-steg-instruktioner finns i [Konfigurera privata slutpunkter](configure-private-endpoints.md).