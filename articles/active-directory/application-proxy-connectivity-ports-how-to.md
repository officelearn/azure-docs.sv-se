---
title: "Så här öppnar du portar i brandväggen krävs för ett program med Application Proxy | Microsoft Docs"
description: "Ta reda på vilka portar som bör öppnas för Azure AD Application Proxy ska fungera korrekt"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: 8ecd6d7e666d362194126a4abba7a65f2c7b8b6b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Så här öppnar du portar i brandväggen krävs för ett program med Application Proxy

En fullständig lista över portarna som krävs och funktionerna i varje port finns i avsnittet förutsättningar i den [Application Proxy dokumentationen](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-enable). Observera att Application Proxy endast använder utgående portar.

Du kan också kontrollera om du har alla de nödvändiga portarna öppna genom att öppna den [anslutningsverktyget portar Test](https://aadap-portcheck.connectorporttest.msappproxy.net/) från ditt lokala nätverk. Flera gröna bockmarkeringarna innebär större flexibilitet. 

## <a name="app-proxy-regions"></a>Appen Proxy regioner

Vi arbetar på ett sätt så att du vet vilken av dessa regioner måste vara grön för dig. Kontrollera att alla är för tillfället. Centrala USA krävs oavsett vilken region som du befinner dig i.

Om du vill se till att verktyget ger rätt resultat, måste du kontrollera att:

-   Öppna verktyget i en webbläsare från den server där du har installerat kopplingen.

-   Se till att alla proxyservrar och brandväggar gäller för din anslutningstjänst också används för den här sidan. Detta kan göras i Internet Explorer genom att gå till **inställningar**  - &gt; **Internetalternativ**  - &gt; **anslutningar**  - &gt; **Lan-inställningar**. Fältet ”Använd en Proxy-Server för ditt LAN” visas på den här sidan. Välj den här rutan och placera Proxyadressen i fältet ”adress”.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-kopplingar](application-proxy-understand-connectors.md)
