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
ms.date: 04/05/2019
ms.subservice: app-mgmt
ms.author: mimart
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9ecd193282ed9b7333df44689530b4d057ad7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68693905"
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
