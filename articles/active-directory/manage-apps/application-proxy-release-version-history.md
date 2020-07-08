---
title: 'Azure AD-programproxy: versions historik | Microsoft Docs'
description: Den här artikeln innehåller en lista över alla versioner av Azure AD-programproxy och beskriver nya funktioner och problem som har åtgärd ATS
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: kenwith
ms.collection: M365-identity-device-management
ms.openlocfilehash: 83c41a357ebaf65cca8465bfc48dda3dd065c3e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760005"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD-programproxy: versions historik
Den här artikeln innehåller versioner och funktioner i Azure Active Directory (Azure AD) Application Proxy som har släppts. Azure AD-teamet uppdaterar regelbundet programproxy med nya funktioner och funktioner. Application Proxy-kopplingar uppdateras automatiskt när en ny version släpps. 

Vi rekommenderar att du ser till att automatiska uppdateringar har Aktiver ATS för dina anslutningar så att du har de senaste funktionerna och fel korrigeringarna. Microsoft tillhandahåller direkt support för den senaste anslutnings versionen och en version.

Här är en lista över relaterade resurser:

Resurs |  Information
--------- | --------- |
Så här aktiverar du tillämpningsproxy | För hands krav för att aktivera programproxy och installera och registrera en anslutning beskrivs i den här [självstudien](application-proxy-add-on-premises-application.md).
Förstå Azure AD-programproxy-kopplingar | Lär dig mer om [anslutnings hantering](application-proxy-connectors.md) och hur anslutningar [uppgraderas automatiskt](application-proxy-connectors.md#automatic-updates).
Hämtning av Azure AD-programproxy Connector |  [Hämta den senaste anslutningen](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Versionsstatus

07, 2020: släpps för hämtning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
-   Kopplingar använder bara TLS 1,2 för alla anslutningar. Se [krav för anslutning](application-proxy-add-on-premises-application.md#before-you-begin) för mer information.
- Förbättrad signalering mellan anslutnings tjänsten och Azure-tjänster. Detta inkluderar stöd för tillförlitliga sessioner för WCF-kommunikation mellan anslutnings-och Azure-tjänster och förbättringar av DNS-cachelagring för WebSocket-kommunikation.
- Stöd för att konfigurera en proxy mellan anslutningen och Server dels programmet. Mer information finns i [arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Åtgärdade problem
- Försvinner tillbaka till Port 8080 för kommunikation från anslutnings tjänsten till Azure-tjänster.
- Fel söknings spårning för WebSocket-kommunikation har lagts till. 
- Matchning av SameSite-attributet i för Server dels program cookies.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Versionsstatus

20 september 2018: har släppts för hämtning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- WebSocket-stöd har lagts till för QlikSense-programmet. Mer information om hur du integrerar QlikSense med Application Proxy finns i den här [genom gången](application-proxy-qlik.md). 
- Förbättrade installations guiden för att göra det enklare att konfigurera en utgående proxy. 
- Ange TLS 1,2 som standard protokoll för kopplingar. 
- Lade till ett nytt licens avtal för slutanvändare (EULA).  

### <a name="fixed-issues"></a>Åtgärdade problem

- En bugg har åtgärd ATS som orsakade minnes läckor i anslutningen.
- Den Azure Service Bus versionen har uppdaterats, vilket innehåller en fel korrigering för anslutnings-timeout-problem.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Versionsstatus

19 januari 2018: släpps för hämtning

### <a name="fixed-issues"></a>Åtgärdade problem

- Stöd har lagts till för anpassade domäner som behöver domän översättning i cookien.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Versionsstatus 

25 maj 2017: frigjord för nedladdning 

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 

Förbättrad kontroll över anslutnings gränser för utgående anslutningar. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Versionsstatus

15 april 2017: har släppts för hämtning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Förenklad onboarding och hantering med färre portar som krävs. Programproxyn behöver nu bara öppna två standard portar för utgående trafik: 443 och 80. Programproxyn fortsätter att använda endast utgående anslutningar, så du behöver fortfarande inte några komponenter i en DMZ. Mer information finns i vår [konfigurations dokumentation](application-proxy-add-on-premises-application.md).  
- Om den externa proxyn eller brand väggen stöder det, kan du nu öppna nätverket med DNS i stället för IP-intervall. Application Proxy-tjänster kräver bara anslutningar till *. msappproxy.net och *. servicebus.windows.net.


## <a name="earlier-versions"></a>Tidigare versioner

Om du använder en version av Application Proxy Connector som är tidigare än 1.5.36.0 uppdaterar du till den senaste versionen för att se till att du har de senaste fullt stödda funktionerna.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [fjärråtkomst till lokala program via Azure AD-programproxy](application-proxy.md).
- Information om hur du börjar använda Application Proxy finns i [Självstudier: Lägg till ett lokalt program för fjärråtkomst via programproxy](application-proxy-add-on-premises-application.md).
