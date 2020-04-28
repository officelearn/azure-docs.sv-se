---
title: Öppna brand Väggs portar som krävs för en Application Proxy-app
description: Ta reda på vilka portar som ska öppnas för Azure-AD-programproxy att fungera korrekt
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b676508c73ff0233526f19b865bb9e4f7b80f12
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/27/2020
ms.locfileid: "74275555"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Så här öppnar du de brand Väggs portar som krävs för ett program för programproxy

Om du vill se en fullständig lista över de portar som krävs och varje ports funktion, se avsnittet krav i dokumentationen för [programproxyn](application-proxy-add-on-premises-application.md). Observera att Application Proxy endast använder utgående portar.

Du kan också kontrol lera om du har alla nödvändiga portar öppna genom att öppna [test verktyget för anslutnings portar](https://aadap-portcheck.connectorporttest.msappproxy.net/) från ditt lokala nätverk. Mer gröna markeringar innebär större återhämtning. 

## <a name="app-proxy-regions"></a>App proxy-regioner

Vi arbetar på ett sätt för att ta reda på vilken av dessa regioner som måste vara gröna. Kontrol lera nu att alla är. Centrala USA krävs också oavsett vilken region du befinner dig i.

För att se till att verktyget ger dig rätt resultat, se till att:

-   Öppna verktyget i en webbläsare från den server där du har installerat anslutningen.

-   Se till att alla proxyservrar eller brand väggar som är tillämpliga på din anslutning också tillämpas på den här sidan. Detta kan göras i Internet Explorer genom att gå till **Inställningar**  - &gt; **Internet alternativ**  - &gt; **anslutningar**  - &gt; **LAN-inställningar**. På den här sidan ser du fältet "Använd en proxyserver för ditt LAN". Markera den här rutan och Lägg till proxyadress i fältet "adress".

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
