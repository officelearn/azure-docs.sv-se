---
title: Så här öppnar du portar i brandväggen krävs för ett program med Application Proxy | Microsoft Docs
description: Ta reda på vilka portar som bör öppnas för Azure AD Application Proxy ska fungera korrekt
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.openlocfilehash: 9f6c45f548b69ed71215593d52cff9b1e42852c4
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589737"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Så här öppnar du portar i brandväggen krävs för ett program med Application Proxy

En fullständig lista över portarna som krävs och funktionerna i varje port finns i avsnittet förutsättningar i den [Application Proxy dokumentationen](manage-apps/application-proxy-enable.md). Observera att Application Proxy endast använder utgående portar.

Du kan också kontrollera om du har alla de nödvändiga portarna öppna genom att öppna den [anslutningsverktyget portar Test](https://aadap-portcheck.connectorporttest.msappproxy.net/) från ditt lokala nätverk. Flera gröna bockmarkeringarna innebär större flexibilitet. 

## <a name="app-proxy-regions"></a>Appen Proxy regioner

Vi arbetar på ett sätt så att du vet vilken av dessa regioner måste vara grön för dig. Kontrollera att alla är för tillfället. Centrala USA krävs oavsett vilken region som du befinner dig i.

Om du vill se till att verktyget ger rätt resultat, måste du kontrollera att:

-   Öppna verktyget i en webbläsare från den server där du har installerat kopplingen.

-   Se till att alla proxyservrar och brandväggar gäller för din anslutningstjänst också används för den här sidan. Detta kan göras i Internet Explorer genom att gå till **inställningar**  - &gt; **Internetalternativ**  - &gt; **anslutningar**  - &gt; **Lan-inställningar**. Fältet ”Använd en Proxy-Server för ditt LAN” visas på den här sidan. Välj den här rutan och placera Proxyadressen i fältet ”adress”.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-kopplingar](manage-apps/application-proxy-connectors.md)
