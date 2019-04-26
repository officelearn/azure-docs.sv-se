---
title: 'Azure AD Application Proxy: Versionshistorik | Microsoft Docs'
description: Den här artikeln visar en lista över alla versioner av Azure AD Application Proxy och beskriver nya funktioner och åtgärdade problem
services: active-directory
documentationcenter: ''
author: msmimart
manager: celested
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/05/2019
ms.subservice: manage-apps
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf9ee43c6c6b332c05286da8e330812d7e0db6c2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60441669"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD Application Proxy: Versionshistorik
Den här artikeln visas de versioner och funktioner för Azure Active Directory (Azure AD) Application Proxy som har frisläppts. Azure AD-teamet uppdaterar regelbundet programproxy med nya funktioner. Anslutningar för programproxy som uppdateras automatiskt när en ny version har släppts.

Här är en lista över relaterade resurser:

Resurs |  Information
--------- | --------- |
Så här aktiverar du Application Proxy | Förutsättningar för att aktivera Application Proxy och installera och registrera en koppling beskrivs i det här [självstudien](application-proxy-add-on-premises-application.md).
Förstå Azure AD Application Proxy-anslutningar | Lär dig mer om [-kopplingens](application-proxy-connectors.md) och hur kopplingar [automatiska uppgraderingar](application-proxy-connectors.md#automatic-updates).
Hämta Azure AD Application Proxy Connector |  [Ladda ned den senaste connector](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Versionsstatus

September 20, 2018: Lanserats för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- WebSocket-stöd för QlikSense-programmet har lagts till. Om du vill veta mer om hur du integrerar QlikSense med Application Proxy kan du se den här [genomgången](application-proxy-qlik.md). 
- Förbättrat installationsguiden för att göra det enklare att konfigurera en utgående proxy. 
- Ange TLS 1.2 som standardprotokoll för kopplingar. 
- Lägga till en ny licensavtalet (EULA).  

### <a name="fixed-issues"></a>Åtgärdade problem

- Ett fel som orsakade att vissa minnesläckor i connector har åtgärdats.
- Uppdatera Azure Service Bus-versionen, vilket inkluderar en buggfix för anslutningstjänsten timeout-problem.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Versionsstatus

19 januari 2018: Lanserats för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

- Tillagt stöd för anpassade domäner som behöver domän translation i cookien.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Versionsstatus 

Den 25 maj 2017: Lanserats för nedladdning 

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 

Bättre kontroll över kopplingarnas utgående anslutningsgränser. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Versionsstatus

Den 15 april 2017: Lanserats för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Förenklad registrering och hantering med färre portar som krävs. Application Proxy kräver nu att öppna endast två standard utgående portar: 443 och 80. Programproxy fortsätter att använda endast utgående anslutningar, så du fortfarande inte behöver alla komponenter i en DMZ. Mer information finns i vår [dokumentationen configuration](application-proxy-add-on-premises-application.md).  
- Om det stöds av externa proxyservern eller brandväggen, kan du nu öppnar ditt nätverk av DNS i stället för IP-intervall. Programproxytjänster kräver anslutningar till *. msappproxy.net och *. servicebus.windows.net endast.


## <a name="earlier-versions"></a>Tidigare versioner

Om du använder en Application Proxy connector-version äldre än 1.5.36.0, uppdatera till den senaste versionen så du får senast fullständiga funktioner som stöds.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [fjärråtkomst till lokala program via Azure AD Application Proxy](application-proxy.md).
- Om du vill börja använda programproxy, se [självstudien: Lägga till ett lokalt program för fjärråtkomst via programproxy](application-proxy-add-on-premises-application.md).
