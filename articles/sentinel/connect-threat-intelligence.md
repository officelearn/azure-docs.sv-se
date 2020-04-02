---
title: Anslut hotinformationsdata till Azure Sentinel| Microsoft-dokument
description: Läs mer om hur du ansluter hotinformationsdata till Azure Sentinel.
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/22/2019
ms.author: yelevin
ms.openlocfilehash: eec07a01edc6b126bb7cd3a814912ea5c5b14195
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80529102"
---
# <a name="connect-data-from-threat-intelligence-providers"></a>Koppla data från leverantörer av hotinformationsunderrättelser

> [!IMPORTANT]
> Hot Intelligence-dataanslutningarna i Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Med Azure Sentinel kan du importera de hotindikatorer som din organisation använder, vilket kan förbättra dina säkerhetsanalytikers förmåga att identifiera och prioritera kända hot. Flera funktioner från Azure Sentinel blir sedan tillgängliga eller förbättras:

- **Analytics** innehåller en uppsättning schemalagda regelmallar som du kan aktivera för att generera aviseringar och incidenter baserat på matchningar av logghändelser från dina hotindikatorer.

- **Arbetsböcker** ger sammanfattad information om hotindikatorer som importeras till Azure Sentinel och alla aviseringar som genereras från analysregler som matchar dina hotindikatorer.

- **Jaktfrågor** gör det möjligt för säkerhetsutredare att använda hotindikatorer inom ramen för gemensamma jaktscenarier.

- **Anteckningsböcker** kan använda hotindikatorer när du undersöker avvikelser och söker efter skadliga beteenden.

Du kan strömma hotindikatorer till Azure Sentinel genom att använda en av de integrerade TIP-produkterna (Threat Intelligence Platform) som anges i nästa avsnitt, ansluta till TAXII-servrar eller genom direkt integrering med API:et för [Microsoft Graph Security tiIndicators](https://aka.ms/graphsecuritytiindicators).

## <a name="integrated-threat-intelligence-platform-products"></a>Integrerade produkter för plattform för hotinformation

- [MISP Hot underrättelseplattform för öppen källkod](https://www.misp-project.org/)
    
    Ett exempelskript som ger klienter MED MISP-instanser för att migrera hotindikatorer till Microsoft Graph Security API finns i [FELPA till Microsoft Graph Security Script](https://github.com/microsoftgraph/security-api-solutions/tree/master/Samples/MISP).

- [Anomali ThreatStream](https://www.anomali.com/products/threatstream)

    Information om hur du hämtar ThreatStream Integrator och Extensions och instruktionerna för att ansluta ThreatStream-intelligens till Microsoft Graph Security API finns på sidan [ThreatStream-hämtningar.](https://ui.threatstream.com/downloads)

- [Palo Alto Nätverk MineMeld](https://www.paloaltonetworks.com/products/secure-the-network/subscriptions/minemeld)
    
    Guidade instruktioner finns i [Skicka IOCs till Microsoft Graph Security API med MineMeld](https://live.paloaltonetworks.com/t5/MineMeld-Articles/Sending-IOCs-to-the-Microsoft-Graph-Security-API-using-MineMeld/ta-p/258540).

- [ThreatConnect-plattform](https://threatconnect.com/solution/)

    Mer information finns i [ThreatConnect-integreringar](https://threatconnect.com/integrations/) och leta efter Microsoft Graph Security API på sidan.


## <a name="connect-azure-sentinel-to-your-threat-intelligence-platform"></a>Anslut Azure Sentinel till din plattform för hotinformation

## <a name="prerequisites"></a>Krav  

- Azure AD-rollen för antingen global administratör eller säkerhetsadministratör för att bevilja behörigheter till din TIP-produkt eller anpassade program som använder direkt integrering med API:et för Microsoft Graph Security tiIndicators.

- Läs och skriv behörigheter till Azure Sentinel-arbetsytan för att lagra dina hotindikatorer.

## <a name="instructions"></a>Instruktioner

1. [Registrera ett program](/graph/auth-v2-service#1-register-your-app) i Azure Active Directory för att hämta ett program-ID, programhemlighet och Azure Active Directory-klient-ID. Du behöver dessa värden när du konfigurerar din integrerade TIP-produkt eller app som använder direkt integrering med Microsoft Graph Security tiIndicators API.

2. [Konfigurera API-behörigheter](/graph/auth-v2-service#2-configure-permissions-for-microsoft-graph) för det registrerade programmet: Lägg till microsoft graph-programbehörigheten **ThreatIndicators.ReadWrite.OwnedBy** i ditt registrerade program.

3. Be din Azure Active Directory-klientadministratör att bevilja administratörsgodkännande till det registrerade programmet för din organisation. Från Azure-portalen: **Azure Active Directory** > **App registreringar** > **\<_appnamn_>** > **Visa API-behörigheter** > Bevilja**administratörsgodkännande för \< _klientnamn_>**.

4. Konfigurera din TIP-produkt eller -app som använder direkt integrering med Microsoft Graph Security tiIndicators API för att skicka indikatorer till Azure Sentinel genom att ange följande:
    
    a. Värdena för det registrerade programmets ID, hemlighet och klient-ID.
    
    b. För målprodukten anger du Azure Sentinel.
    
    c. För åtgärden anger du avisering.

5. I Azure-portalen navigerar du till **Azure Sentinel** > **Data-kopplingar** och väljer sedan **threat intelligence platforms (Preview).**

6. Välj **Öppna kopplingssida**och **anslut**sedan .

7. Om du vill visa de hotindikatorer som importeras till Azure Sentinel **navigerar** > du till Azure Sentinel - Loggar**SecurityInsights**och expanderar sedan **ThreatIntelligenceIndicator**.

## <a name="connect-azure-sentinel-to-taxii-servers"></a>Ansluta Azure Sentinel till TAXII-servrar

## <a name="prerequisites"></a>Krav  

- Läs och skriv behörigheter till Azure Sentinel-arbetsytan för att lagra dina hotindikatorer.

- TAXII 2.0 server URI och Collection ID.

## <a name="instructions"></a>Instruktioner

1. I Azure-portalen navigerar du till **Azure Sentinel** > **Data-kopplingar** och väljer sedan **Threat Intelligence - TAXII (Preview)-anslutningsappen.**

2. Välj **Öppna kopplingssida**.

3. Ange nödvändig och valfri information i textrutorna.

4. Välj **Lägg till** om du vill aktivera anslutningen till TAXII 2.0-servern.

5. Om du har ytterligare TAXII 2.0 servrar: Upprepa steg 3 och 4.

6. Om du vill visa de hotindikatorer som importeras till Azure Sentinel **navigerar** > du till Azure Sentinel - Loggar**SecurityInsights**och expanderar sedan **ThreatIntelligenceIndicator**.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter din hotinformationsprovider till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar.

- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).
