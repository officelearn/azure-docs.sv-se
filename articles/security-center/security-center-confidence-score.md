---
title: Förtroendepoäng i Azure Security Center | Microsoft-dokument
description: Security Center genererar förtroendepoäng som hjälper ditt team att avgöra om ett hot är legitimt och hur du triage och prioritera aviseringar.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: memildin
ms.openlocfilehash: 8aa17f473c550c99d91862754e6a746575aca202
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77604380"
---
# <a name="alert-confidence-score-preview"></a>Poäng för förtroende för avisering (förhandsgranskning)

Azure Security Center ger dig synlighet över de resurser du kör i Azure och varnar dig när den identifierar potentiella problem. Mängden aviseringar kan vara en utmaning för en säkerhetsoperationsgrupp att ta itu med individuellt, och det blir nödvändigt att prioritera vilka aviseringar som ska undersökas. Att undersöka aviseringar kan vara komplicerat och tidskrävande, och som ett resultat ignoreras vissa aviseringar.

Förtroendepoängen (för närvarande i förhandsversion) i Security Center kan hjälpa ditt team att triage och prioritera aviseringar. Security Center tillämpar automatiskt branschens bästa praxis, intelligenta algoritmer och processer som används av analytiker för att avgöra om ett hot är legitimt och ger dig meningsfulla insikter i form av en förtroendepoäng.

## <a name="how-the-confidence-score-is-triggered"></a>Hur förtroendepoängen utlöses

Aviseringar genereras när misstänkta processer upptäcks körs på dina virtuella datorer. Security Center granskar och analyserar dessa aviseringar på virtuella Windows-datorer som körs i Azure. Den utför automatiska kontroller och korrelationer med hjälp av avancerade algoritmer över flera entiteter och datakällor i hela organisationen, och alla dina Azure-resurser, och presenterar med en konfidenspoäng som är ett mått på hur säker Security Center är att varningen är äkta och behöver undersökas.

## <a name="understanding-the-confidence-score"></a>Förstå förtroendepoängen

Konfidenspoängen varierar mellan 1 och 100 och representerar det förtroende Security Center har att aviseringen måste undersökas. Ju högre poäng är, desto mer säker Security Center är att aviseringen indikerar äkta skadlig aktivitet. Konfidenspoängen innehåller en lista över de främsta anledningarna till att varningen fick sin förtroendepoäng. Förtroendepoängen gör det lättare för säkerhetsanalytiker att prioritera sina svar på varningar och ta itu med de mest akuta attackerna först, vilket i slutändan minskar den tid det tar att svara på attacker och överträdelser.

Så här visar du konfidenspoängen:
- Öppna bladet Säkerhetsvarningar i Säkerhetscenter-portalen.
-  Aviseringarna och incidenterna är organiserade från högsta till lägsta, vilket innebär att ju mer säker Security Center är att en avisering utgör ett hot, desto närmare är det till toppen av sidan. 


 ![Förtroendepoäng][1]

Så här visar du data som bidrog till Security Centers förtroende för en avisering:
- I säkerhetsvarningsbladet, under **Förtroende**, visa de observationer som bidrog till förtroendepoängen och få insikter relaterade till aviseringen. Detta ger dig mer insikt i vilken typ av aktiviteter som orsakade aviseringen.

  ![Misstänkt förtroendepoäng][2]

Använd Security Centers förtroendepoäng för att prioritera aviseringstriage i din miljö. Förtroendepoängen sparar tid och ansträngning genom att automatiskt undersöka aviseringar, tillämpa branschens bästa praxis och intelligenta algoritmer och fungera som en virtuell analytiker för att avgöra vilka hot som är verkliga och var du behöver fokusera din uppmärksamhet.


<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
