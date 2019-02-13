---
title: Hur du öppnar brandväggsportar som krävs för ett program med Application Proxy | Microsoft Docs
description: Ta reda på vilka portar öppna för Azure AD Application Proxy ska fungera korrekt
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: celested
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: efc8c0f0581878d41eb62cbfbd88aec730ecf789
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56175268"
---
# <a name="how-to-open-the-firewall-ports-required-for-an-application-proxy-application"></a>Hur du öppnar brandväggsportar som krävs för ett Application Proxy-program

En fullständig lista över portarna som krävs och funktionen för varje port finns i avsnittet med förutsättningar i den [Application Proxy-dokumentationen](application-proxy-add-on-premises-application.md). Observera att Application Proxy endast använder utgående portar.

Du kan också kontrollera om du har alla nödvändiga portar öppna genom att öppna den [anslutningsverktyget portar Test](https://aadap-portcheck.connectorporttest.msappproxy.net/) från ditt lokala nätverk. Mer gröna bockmarkeringar innebär större flexibilitet. 

## <a name="app-proxy-regions"></a>App Proxy-regioner

Vi arbetar på ett sätt så att du vet vilka av dessa regioner måste vara grön för dig. Kontrollera att alla är för tillfället. Centrala USA krävs oavsett vilken region som du tillhör.

Om du vill se till att verktyget ger rätt resultat, måste du kontrollera att:

-   Öppna verktyget i en webbläsare från servern där du har installerat anslutningen.

-   Se till att alla proxyservrar och brandväggar som gäller för din Anslutningsapp används också till den här sidan. Detta kan göras i Internet Explorer genom att gå till **inställningar**  - &gt; **Internetalternativ**  - &gt; **anslutningar**  - &gt; **Lan-inställningar**. På den här sidan kan se du fältet ”Använd en Proxy Server för ditt LAN”. Välj den här rutan och placera Proxyadressen i fältet ”adress”.

## <a name="next-steps"></a>Nästa steg
[Förstå Azure AD Application Proxy-anslutningar](application-proxy-connectors.md)
