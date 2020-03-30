---
title: Azure Security Center-incidenter – smarta korrelationer av aviseringar
description: I det här avsnittet beskrivs hur fusion använder molnsmart varningskorrelation för att generera säkerhetsincidenter i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e9d5a771-bfbe-458c-9a9b-a10ece895ec1
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/02/2019
ms.author: memildin
ms.openlocfilehash: b26f0bab073ce248ca23bb8a815fa3e293ddba51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73686487"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Molnsmart varningskorrelation i Azure Security Center (incidenter)

Azure Security Center analyserar kontinuerligt hybridmolnarbetsbelastningar med hjälp av avancerad analys- och hotinformation för att varna dig om skadlig aktivitet.

Bredden på hottäckningen växer. Behovet av att upptäcka minsta kompromiss är viktigt, och det kan vara en utmaning för säkerhetsanalytiker att triage de olika larmen och identifiera en verklig attack. Security Center hjälper analytiker att hantera denna varning trötthet. Det hjälper till att diagnostisera attacker när de inträffar, genom att korrelera olika varningar och lågåtergivningssignaler till säkerhetsincidenter.

Fusion analytics är den teknik och analytiska backend som driver Security Center incidenter, gör det möjligt att korrelera olika varningar och kontextuella signaler tillsammans. Fusion tittar på de olika signaler som rapporteras på en prenumeration över resurserna. Fusion hittar mönster som avslöjar attackprogression eller signaler med delad kontextuell information, vilket indikerar att du bör använda en enhetlig svarsprocedur för dem.

Fusion analytics kombinerar kunskap om säkerhetsområden med AI för att analysera aviseringar och upptäcka nya attackmönster när de inträffar. 

Security Center utnyttjar MITRE Attack Matrix för att associera aviseringar med deras upplevda avsikt, vilket hjälper till att formalisera kunskap om säkerhetsdomäner. Genom att använda den information som samlas in för varje steg i en attack kan Säkerhetscenter dessutom utesluta aktivitet som verkar vara steg i en attack, men som faktiskt inte är det.

Eftersom attacker ofta förekommer över olika klienter kan Security Center kombinera AI-algoritmer för att analysera attacksekvenser som rapporteras för varje prenumeration. Den här tekniken identifierar attacksekvenserna som förhärskande varningsmönster, i stället för att bara vara för övrigt associerade med varandra.

Under en utredning av en incident, analytiker behöver ofta extra sammanhang för att nå en dom om vilken typ av hot och hur man kan mildra det. Till exempel, även när en nätverksanvikelse upptäcks, utan att förstå vad som händer i nätverket eller när det gäller den riktade resursen, är det svårt att förstå vilka åtgärder som ska vidtas härnäst. För att hjälpa till kan en säkerhetsincident innehålla artefakter, relaterade händelser och information. Den ytterligare information som är tillgänglig för säkerhetsincidenter varierar beroende på vilken typ av hot som har upptäckts och konfigurationen av din miljö. 

![Skärmbild av rapporten Säkerhetsincident har upptäckts](./media/security-center-alerts-cloud-smart/security-incident.png)

Information om hur du bättre kan förstå säkerhetsincidenter finns [i Så här hanterar du säkerhetsincidenter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

