---
title: 'Azure AD-programproxy: Versionsversionshistorik | Microsoft-dokument'
description: I den här artikeln visas alla versioner av Azure AD Application Proxy och nya funktioner och fasta problem beskrivs
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/07/2020
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: f027fbce66a73306165a0ad35d1ba3faa7a5c0bc
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2020
ms.locfileid: "80983899"
---
# <a name="azure-ad-application-proxy-version-release-history"></a>Azure AD-programproxy: Versionsversionshistorik
I den här artikeln visas de versioner och funktioner i Azure Active Directory (Azure AD) Application Proxy som har släppts. Azure AD-teamet uppdaterar regelbundet programproxy med nya funktioner och funktioner. Application Proxy-kopplingar uppdateras automatiskt när en ny version släpps. 

Vi rekommenderar att du ser till att automatiska uppdateringar är aktiverade för dina kontakter för att säkerställa att du har de senaste funktionerna och buggfixarna. Microsoft ger direkt stöd för den senaste anslutningsversionen och en version tidigare.

Här är en lista över relaterade resurser:

Resurs |  Information
--------- | --------- |
Aktivera programproxy | Förutsättningar för att aktivera Programproxy och installera och registrera en anslutning beskrivs i den här [självstudien](application-proxy-add-on-premises-application.md).
Förstå Azure AD-programproxy-kopplingar | Läs mer om [anslutningshantering](application-proxy-connectors.md) och hur kopplingar [automatiskt uppgraderas](application-proxy-connectors.md#automatic-updates).
Hämtning av Azure AD-programproxyanslutning |  [Ladda ner den senaste kontakten](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download).

## <a name="1515260"></a>1.5.1526.0

### <a name="release-status"></a>Versionsstatus

April 07, 2020: Släppt för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar
-   Kopplingar använder endast TLS 1.2 för alla anslutningar. Se [Anslutningsförespråkarna](application-proxy-add-on-premises-application.md#before-you-begin) för mer information.
- Förbättrad signalering mellan Connector- och Azure-tjänsterna. Detta inkluderar att stödja tillförlitliga sessioner för WCF-kommunikation mellan Connector- och Azure-tjänsterna och förbättringar av DNS-cachelagring för WebSocket-kommunikation.
- Stöd för att konfigurera en proxy mellan kopplingen och serverkopplingsprogrammet. Mer information finns i [Arbeta med befintliga lokala proxyservrar](application-proxy-configure-connectors-with-proxy-servers.md).

### <a name="fixed-issues"></a>Åtgärdade problem
- Tog bort att falla tillbaka till port 8080 för kommunikation från Anslutningsappen till Azure-tjänsterna.
- Lade till felsökningsspårningar för WebSocket-kommunikation. 
- Löst bevara SameSite-attributet när det är inställt på bakåtsträvningsprogramcookies.

## <a name="156120"></a>1.5.612.0

### <a name="release-status"></a>Versionsstatus

September 20, 2018: Släppt för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Lade till WebSocket-stöd för QlikSense-programmet. Mer information om hur du integrerar QlikSense med Application Proxy finns i den här [genomgången](application-proxy-qlik.md). 
- Förbättrade installationsguiden för att göra det enklare att konfigurera en utgående proxy. 
- Ange TLS 1.2 som standardprotokoll för kopplingar. 
- Lade till ett nytt licensavtal för slutanvändare (EULA).  

### <a name="fixed-issues"></a>Åtgärdade problem

- Fixade ett fel som orsakade vissa minnesläckor i kontakten.
- Uppdaterade Azure Service Bus-versionen, som innehåller en felkorrigering för tidsgränsen för anslutning.

## <a name="154020"></a>1.5.402.0

### <a name="release-status"></a>Versionsstatus

19 januari 2018: Släppt för nedladdning

### <a name="fixed-issues"></a>Åtgärdade problem

- Lade till stöd för anpassade domäner som behöver domänöversättning i cookien.

## <a name="151320"></a>1.5.132.0

### <a name="release-status"></a>Versionsstatus 

25 maj 2017: Släppt för nedladdning 

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar 

Förbättrad kontroll över kopplingarnas utgående anslutningsgränser. 

## <a name="15360"></a>1.5.36.0

### <a name="release-status"></a>Versionsstatus

April 15, 2017: Släppt för nedladdning

### <a name="new-features-and-improvements"></a>Nya funktioner och förbättringar

- Förenklad introduktion och hantering med färre nödvändiga portar. Application Proxy kräver nu att endast två vanliga utgående portar öppnas: 443 och 80. Programproxy fortsätter att använda endast utgående anslutningar, så du behöver fortfarande inga komponenter i en DMZ. Mer information finns i vår [konfigurationsdokumentation](application-proxy-add-on-premises-application.md).  
- Om det stöds av din externa proxy eller brandvägg kan du nu öppna nätverket via DNS i stället för IP-intervall. Programproxytjänster kräver endast anslutningar till *.msappproxy.net och *.servicebus.windows.net.


## <a name="earlier-versions"></a>Tidigare versioner

Om du använder en Application Proxy-anslutningsversion tidigare än 1.5.36.0 uppdaterar du till den senaste versionen för att säkerställa att du har de senaste funktionerna som stöds.

## <a name="next-steps"></a>Nästa steg
- Läs mer om [fjärråtkomst till lokala program via Azure AD Application Proxy](application-proxy.md).
- Mer om du vill börja använda Programproxy finns i [Självstudiekurs: Lägg till ett lokalt program för fjärråtkomst via Programproxy](application-proxy-add-on-premises-application.md).
