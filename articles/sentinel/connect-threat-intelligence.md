---
title: Samla in threat intelligence-data i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Läs mer om hur du ansluter threat intelligence-data till Azure Sentinel.
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 56412543-5664-44c1-b026-2dbaf78a9a50
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/20/2019
ms.author: rkarlin
ms.openlocfilehash: bc8a644f99d9a84e1f2c177a87e2668ae9a57868
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400635"
---
# <a name="collect-data-from-threat-intelligence-providers"></a>Samla in data från säkerhetsexperter 

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

När du strömma data till Azure Sentinel, kan du utöka den med hotinformationen feed som du använder i organisationen. 

Så att du mellan efter dina aviseringar och regler med SANT hotinformation, till exempel om du får en avisering från en specifik IP-adress kommer din threat intelligence providern integrering att kunna meddela dig om IP-adress nyligen visade sig vara skadliga , Azure Sentinel möjliggör integrering med [hot intelligence providers](https://aka.ms/graphsecuritytips). 

Du kan strömma loggar från säkerhetsexperter till Sentinel-Azure med ett enda klick. Den här anslutningen gör att du kan införliva indikatorer som innehåller olika typer av observables, till exempel IP-adress, domän, URL: en och hash-värdet att söka efter och skapa anpassade varnar regler i Azure Sentinel.  
> [!NOTE]
> Du kan ange anpassade threat indikatorer i Azure-Sentinel för användning i Varningsregler, instrumentpaneler och jakt scenarier genom att integrera med den [Microsoft Graph Security tiIndicator](https://aka.ms/graphsecuritytiindicators) entitet eller genom att använda en [Microsoft Graph-säkerheten integrerad plattform för Threat Intelligence](https://aka.ms/graphsecuritytips).

## <a name="prerequisites"></a>Förutsättningar  

- Användare med global administratör eller administratörsbehörighet för säkerhet 

- Threat intelligence-appar som är integrerad med Microsoft Intelligent Security Graph 

## <a name="connect-to-threat-intelligence"></a>Ansluta till hotinformation 

1. Om du redan använder en threat intelligence provider, måste du bläddra till programmets tips och ge behörighet att skicka indikatorer till Microsoft och ange tjänsten som Azure Sentinel.  

2. I Azure Sentinel väljer **datainsamling** och klicka sedan på den **Hotinformation** panelen.

3. Klicka på **Anslut**. 

4. Om du vill använda relevanta schema i Log Analytics för insamling av hotinformation, Sök efter **ThreatIntelligenceIndicator**. 

 
## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du ansluter din Hotinformation-provider till Sentinel-Azure. Mer information om Azure Sentinel finns i följande artiklar.

- Om du vill komma igång med Azure Sentinel, behöver du en prenumeration på Microsoft Azure. Om du inte har någon prenumeration kan du registrera dig för en [kostnadsfri utvärderingsversion](https://azure.microsoft.com/free/).
- Lär dig hur du [publicera dina data till Azure Sentinel](quickstart-onboard.md), och [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
