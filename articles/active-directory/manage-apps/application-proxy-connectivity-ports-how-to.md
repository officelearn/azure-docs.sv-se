---
title: Så här öppnar du brandväggsportar som krävs för en programproxyapp
description: Ta reda på vilka portar som ska öppnas för Azure AD Application Proxy för att fungera korrekt
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275555"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Så här öppnar du brandväggsportarna som krävs för ett programproxyprogram

Om du vill se en fullständig lista över de portar som krävs och funktionen för varje port läser du avsnittet om förutsättningar i [dokumentationen för programproxy](application-proxy-add-on-premises-application.md). Observera att Programproxy endast använder utgående portar.

Du kan också kontrollera om du har alla nödvändiga portar öppna genom att öppna [testverktyget för anslutningsportar](https://aadap-portcheck.connectorporttest.msappproxy.net/) från det lokala nätverket. Mer gröna bockar innebär större återhämtning. 

## <a name="app-proxy-regions"></a>App proxy regioner

Vi arbetar på ett sätt att låta dig veta vilken av dessa regioner som behöver vara grön för dig. För tillfället, se till att alla är. Centrala USA krävs också oavsett vilken region du befinner dig i.

Se till att verktyget ger dig rätt resultat:

-   Öppna verktyget i en webbläsare från servern där du har installerat anslutningsappen.

-   Kontrollera att alla proxyservrar eller brandväggar som är tillämpliga på din Anslutning också tillämpas på den här sidan. Detta kan göras i Internet Explorer genom att gå till **Inställningar**  - &gt; **Internet Options**  - &gt; **Connections**  - &gt; **LAN-inställningar**. På den här sidan visas fältet "Använd en proxyserver för ditt LAN". Markera den här rutan och placera proxyadressen i fältet "Adress".

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD-programproxy-kopplingar](application-proxy-connectors.md)
