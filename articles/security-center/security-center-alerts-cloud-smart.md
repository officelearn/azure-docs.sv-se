---
title: Moln Smart aviserings korrelation i Azure Security Center (incidenter) | Microsoft Docs
description: I det här avsnittet beskrivs hur Fusion använder Cloud Smart Alert-korrelation för att generera säkerhets incidenter i Azure Security Center.
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
ms.author: v-mohabe
ms.openlocfilehash: 7ba2cf14d9fac100f44a1ef23997b27ba062bee0
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/17/2019
ms.locfileid: "68295865"
---
# <a name="cloud-smart-alert-correlation-in-azure-security-center-incidents"></a>Moln Smart aviserings korrelation i Azure Security Center (incidenter)

Security Center kontinuerligt analyserar hybrid moln arbets belastningar med avancerad analys och hot information för att varna dig om skadlig aktivitet.

När risken för hot växer ökar och behovet av att identifiera även den lättaste anvisningen av komprometterade ökningar, kan det vara svårt för säkerhetsanalytiker att prioritering de olika aviseringarna och identifiera ett faktiskt angrepp. Security Center hjälper till att analysera problem med varnings utmattning och diagnostisera attacker när de uppstår, genom att korrelera olika aviseringar och låg åter givning i säkerhets incidenter.

Fusion är den teknik och den analytiska Server delen som ger Security Center incidenter, vilket gör det möjligt att korrelera olika aviseringar och kontextuella signaler tillsammans. Fusion fungerar genom att titta på olika signaler som rapporteras i en prenumeration på resurserna och hitta vanliga mönster som visar angrepps förlopp eller signaler med delad sammanhangsbaserad information som anger att en enhetlig svars procedur ska vara vidtagna.

Fusions analys kombinerar kunskap om säkerhets domäner med AI för att analysera aviseringar, identifiera nya angrepps mönster när de inträffar. 

Security Center utnyttjar MITRE-attackens matris för att koppla aviseringar till deras uppfattade avsikt, vilket underlättar formalisera säkerhets domän kunskap. Med hjälp av den information som samlas in för varje steg i ett angrepp kan Security Center dessutom utesluta aktiviteter som verkar vara steg för en attack, men inte.  

Eftersom angrepp ofta sker över olika klienter, kan Security Center kombinera AI-algoritmer för att analysera angrepps sekvenser som rapporteras för varje prenumeration för att identifiera dem som vanliga aviserings mönster i stället för att bara incidenten associeras med var och en andra.

Under en undersökning av en incident behöver analytiker ofta extra kontext för att uppnå en bedömning om hotets beskaffenhet och hur det kan minimeras. Till exempel, även om en nätverks avvikelse upptäcks, utan att förstå vad som händer i nätverket eller med avseende på mål resursen, är det svårt att förstå vilka åtgärder som ska vidtas härnäst. För att under lätta kan en säkerhets incident innehålla artefakter, relaterade händelser och information. Den ytterligare information som är tillgänglig för säkerhets incidenter varierar beroende på typ av hot som upptäckts och konfigurationen av din miljö. 

![Information om säkerhets incidenter](./media/security-center-alerts-cloud-smart/security-incident.png)

För att bättre förstå säkerhets incidenter, se [hantera säkerhets incidenter i Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-incident).

