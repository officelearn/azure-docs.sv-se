---
title: Anslut hot informations data till Azure Sentinel | Microsoft Docs
description: Lär dig mer om hur du ansluter hot Intelligence-data till Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: rkarlin
ms.openlocfilehash: 44b3830465bf2b5aa06612aa868b086b120f1ece
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2019
ms.locfileid: "72372281"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Anslut data från hot Intelligence-leverantörer

> [!IMPORTANT]
> Data kopplingen för Threat Intelligence-plattformarna i Azure Sentinel är för närvarande en offentlig för hands version.
> Den här funktionen tillhandahålls utan service nivå avtal och rekommenderas inte för produktions arbets belastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Sentinel kan du importera de hot indikatorer som din organisation använder, vilket kan förbättra din säkerhetsanalytikers möjlighet att identifiera och prioritera kända hot. Flera funktioner från Azure Sentinel blir sedan tillgängliga eller förbättrade:

- **Analytics** innehåller en uppsättning schemalagda regelmallar som du kan aktivera för att generera aviseringar och incidenter som baseras på matchningar av logg händelser från dina hot indikatorer.

- **Arbets böcker** ger sammanfattad information om de hot indikatorer som importeras till Azure Sentinel och eventuella varningar som genereras från analys regler som matchar dina hot indikatorer.

- Med **jakt** frågor kan säkerhetsövervakare använda hot indikatorer inom ramen för vanliga jakt scenarier.

- **Antecknings böcker** kan använda hot indikatorer när du undersöker avvikelser och söker efter skadliga beteenden.

Du kan strömma hot indikatorer till Azure Sentinel genom att använda en av de "TIP-produkter (Integrated Threat Intelligence Platform) som anges i nästa avsnitt, eller genom Microsoft Graph att använda direkt integrering med [tiIndicators-API: et för säkerhet](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Integrerade hot Intelligence Platform-produkter

- [MISP för hot information med öppen källkod](https://www.misp-project.org/)
    
    Ett exempel skript som förser klienter med MISP-instanser för att migrera hot indikatorer till Microsoft Graph Security API finns i avsnittet [MISP to Microsoft Graph Security script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Palo-nätverk MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Guidade instruktioner finns i [Skicka IOCs till säkerhets-API: et för Microsoft Graph med MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect-plattform](https://threatconnect.com/solution/)


## <a name="prerequisites"></a>Krav  

- Azure AD-rollen för antingen global administratör eller säkerhets administratör för att ge behörighet till din TIP-produkt eller anpassade program som använder direkt integrering Microsoft Graph med tiIndicators-API: et för säkerhet.

- Läs-och Skriv behörighet till arbets ytan i Azure Sentinel för att lagra dina hot indikatorer.

## <a name="connect-azure-sentinel-to-your-threat-intelligence-provider"></a>Anslut Azure Sentinel till din Threat Intelligence-Provider

1. [Registrera ett program](/graph/auth-v2-service#1-register-your-app) i Azure Active Directory för att hämta ett program-ID, program hemlighet och Azure Active Directory klient-ID. Du behöver dessa värden för när du konfigurerar din integrerade tips produkt eller app som använder direkt integrering med Microsoft Graph Security tiIndicators API.

2. [Konfigurera API-behörigheter](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) för det registrerade programmet: Lägg till behörigheten Microsoft Graph Application **ThreatIndicators. readwrite. OwnedBy** i det registrerade programmet.

3. Be din Azure Active Directory klient administratör att bevilja administrativt medgivande till det registrerade programmet för din organisation. Från Azure Portal: **Azure Active Directory** > **Appregistreringar** >  **\<-_appens namn_>**  > **Visa API-behörigheter**0**tilldela administratörs medgivande för 2_klient organisations namn_ 4**.

4. Konfigurera TIP-produkten eller appen som använder direkt integrering med Microsoft Graph Security tiIndicators API för att skicka indikatorer till Azure Sentinel genom att ange följande:
    
    a. Värdena för det registrerade programmets ID, hemlighet och klient-ID.
    
    b. För mål produkten anger du Azure Sentinel.
    
    c. Ange avisering för åtgärden.

5. I Azure Portal går du till **Azure Sentinel** > **data anslutningar** och väljer sedan **Hot information Platforms (för hands version)** .

6. Välj **Öppna anslutnings sidan**och **Anslut**sedan.

7. Om du vill visa de hot indikatorer som importeras till Azure Sentinel går du till **Azure Sentinel-Logs** > **SecurityInsights**och expanderar sedan **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter din Threat Intelligence-Provider till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar.

- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
