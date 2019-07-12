---
title: Smart incidenthanteringssystem i Azure Security Center (incidenter) i molnet | Microsoft Docs
description: Det här avsnittet förklarar hur fusion använder molnet smart incidenthanteringssystem att generera incidenter finns i Azure Security Center.
services: security-center
documentationcenter: na
author: monhaber
manager: rkarlin
editor: ''
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 7/02/2019
ms.author: monhaber
ms.openlocfilehash: 2ab4dab8cb7729b0c2ca023f22066f7b5d166a02
ms.sourcegitcommit: 1e347ed89854dca2a6180106228bfafadc07c6e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67571782"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Smart incidenthanteringssystem i Azure Security Center (incidenter) i molnet

Security Center analyserar kontinuerligt hybridmolnarbetsbelastningar med avancerad analys och hotinformation för att meddela dig om skadlig aktivitet.

När hot täckning bredd växer och behovet av att identifiera och med den minsta indikation på komprometteras ökar, kan det vara svårt för säkerhetsanalytiker att testa de olika aviseringarna och identifiera något verkligt angrepp. Security Center hjälper till att analytiker explosiva avisering utmattning och diagnostisera attacker när de inträffar genom att korrelera olika aviseringar och otillförlitliga signaler i säkerhetsincidenter.

Fusion är tekniken och analytiska serverdel som driver Security Center incidenter, så att systemet kan kombinera olika aviseringar och kontextuella signaler tillsammans. Fusion fungerar genom att titta på olika signaler som rapporterats för en prenumeration för resurserna och att söka efter vanliga mönster som visar status för angrepp eller signalerar till med delade sammanhangsinformation som anger en enhetlig svar procedur bör vara kommer de.

Fusion analytics kombinera security domänkunskap med AI för att analysera aviseringar, identifiera nya angreppsmönster när de inträffar. 

Security Center använder MITRE Attack matrisen för att associera aviseringar med sina upplevd avsikt, hjälper formalisera kunskap om säkerhet. Security Center kan dessutom försäkra dig om aktivitet som verkar vara steg i en attack, men är inte med hjälp av informationen som samlats in för varje steg i ett angrepp.  

Eftersom attacker sker ofta i olika klientorganisationer, kan Security Center kombinera AI-algoritmer för att analysera attack sekvenser som rapporteras för varje prenumeration för att identifiera dem som vanliga avisering mönster i stället för associerade kanske lägger till var och en andra.

Vid en undersökning av en incident behöver analytikerna ofta en bedömning om vilken typ av hot och hur du löser det sammanhanget. Till exempel även om en nätverksavvikelse upptäcks är utan att förstå vad som sker i nätverket eller resurs det svårt att förstå vilka åtgärder som ska tas härnäst. För att hjälpa, kan en säkerhetsincident omfatta artefakter, relaterade händelser och information. Ytterligare information som är tillgängliga för säkerhetsincidenter varierar beroende på vilken typ av hot som upptäckts och konfigurationen av din miljö. 

![Information om incidenten-säkerhet](./media/security-center-alerts-cloud-smart/security-incident.png)

För att bättre förstå säkerhetsincidenter, se [hur du hanterar säkerhetsincidenter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

