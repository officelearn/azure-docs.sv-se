---
title: Azure Security Center incidenter – smarta korrelationer av aviseringar
description: I det här avsnittet beskrivs hur Fusion använder Cloud Smart Alert-korrelation för att generera säkerhets incidenter i Azure Security Center.
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
ms.openlocfilehash: b60798ad1fab0ed66f3d4a39b2fd7faf79fd515e
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268079"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Moln Smart aviserings korrelation i Azure Security Center (incidenter)

Azure Security Center kontinuerligt analyserar hybrid moln arbets belastningar med hjälp av avancerad analys och hot information för att varna dig om skadlig aktivitet.

Risken för hot täckning växer. Behovet av att identifiera även den lättaste kompromissen är viktigt och det kan vara svårt att prioritering de olika aviseringarna och identifiera ett faktiskt angrepp. Security Center hjälper till att analysera den här aviseringen. Den hjälper till att diagnostisera attacker när de sker, genom att korrelera olika aviseringar och låg åter givning i säkerhets incidenter.

Fusions analys är den teknik och den analytiska Server delen som ger Security Center incidenter, vilket gör det möjligt att korrelera olika aviseringar och kontextuella signaler tillsammans. Fusion tittar på de olika signaler som rapporteras i en prenumeration över resurserna. Fusion hittar mönster som avslöjar angrepps förlopp eller signaler med delad sammanhangsbaserad information, vilket indikerar att du bör använda en enhetlig svars procedur för dem.

Fusions analys kombinerar säkerhets domänens kunskap med AI för att analysera aviseringar, identifiera nya angrepps mönster när de inträffar. 

Security Center utnyttjar MITRE-attackens matris för att koppla aviseringar till deras uppfattade avsikt, vilket underlättar formalisera säkerhets domän kunskap. Genom att använda den information som samlats in för varje steg i en attack kan Security Center dessutom utesluta aktiviteter som verkar vara steg för en attack, men egentligen inte.

Eftersom angrepp ofta sker över olika klienter kan Security Center kombinera AI-algoritmer för att analysera angrepps sekvenser som rapporteras för varje prenumeration. Den här tekniken identifierar angrepps sekvenser som vanliga aviserings mönster, i stället för att bara vara incidenter som är associerade med varandra.

Under en undersökning av en incident behöver analytiker ofta extra kontext för att uppnå en bedömning om hotets beskaffenhet och hur det kan minimeras. Till exempel även om en nätverks avvikelse identifieras, utan att förstå vad som händer i nätverket eller med avseende på mål resursen, är det svårt att förstå vilka åtgärder som ska vidtas härnäst. För att under lätta kan en säkerhets incident innehålla artefakter, relaterade händelser och information. Den ytterligare information som är tillgänglig för säkerhets incidenter varierar beroende på typ av hot som upptäckts och konfigurationen av din miljö. 

![Skärm bild av identifierad rapport för säkerhets incident](./media/security-center-alerts-cloud-smart/security-incident.png)

För att bättre förstå säkerhets incidenter, se [hantera säkerhets incidenter i Azure Security Center](security-center-incident.md).

