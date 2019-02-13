---
title: Förtroendepoäng i Azure Security Center | Microsoft Docs
description: " Lär dig hur du arbetar med förtroendepoäng Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: e88198f8-2e16-409d-a0b0-a62e68c2f999
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/23/2018
ms.author: rkarlin
ms.openlocfilehash: 67442a5861514eadfebfc877d91e735e05f6f63d
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56117553"
---
# <a name="alert-confidence-score"></a>Förtroendepoäng för avisering 

Azure Security Center får du bättre inblick i de resurser som du kör i Azure och varnar dig när den identifierar potentiella problem. Mängden aviseringar kan vara en utmaning för en security-teamet att adressera och det blir nödvändigt att prioritera vilka aviseringar du undersöker. Undersöka aviseringar kan vara komplicerat och tidskrävande och därför kan vissa aviseringar ignoreras.

Förtroendepoäng i Security Center kan hjälpa ditt team-prioritering och prioritera aviseringar. Security Center tillämpas automatiskt branschens bästa praxis, intelligenta algoritmer och processer som används av analytiker för att avgöra om ett hot är legitim och ger dig meningsfulla insikter i form av ett förtroenderesultat.

## <a name="how-the-confidence-score-is-triggered"></a>Hur förtroendepoäng utlöses

Aviseringar genereras när identifieras misstänkta processer som körs på dina virtuella datorer. Security Center går igenom och analyserar dessa aviseringar på Windows-datorer som körs i Azure. Den utför automatiserade kontroller och samband med hjälp av avancerade algoritmer i flera entiteter och datakällor i organisationen och alla dina Azure-resurser och presenterar tryggt poängen som är ett mått på hur säker Security Center är att aviseringen är äkta och måste undersökas.

## <a name="understanding-the-confidence-score"></a>Förstå förtroendepoäng

Förtroendepoäng intervall mellan 1 och 100 och representerar förtroende som Security Center har att aviseringen måste undersökas. Ju högre poängen är tryggare Security Center att aviseringen indikerar äkta skadlig aktivitet. Förtroendepoäng innehåller en lista över de främsta skälen varför aviseringen fått dess förtroendepoäng. Förtroendepoäng gör det enklare för säkerhetsanalytiker att prioritera deras svar på aviseringar och åtgärda de flesta att först trycka på attacker, slutligen minska hur lång tid det tar för att svara på attacker och överträdelser.

Visa förtroendepoäng:
- Öppna bladet med säkerhetsaviseringar i Security Center-portalen.
-  Aviseringarna och incidenterna ordnas från högsta till lägsta, vilket innebär att tryggare Security Center är att en avisering representerar ett hot, desto närmare att sidans överkant. 


 ![Förtroendepoäng][1]

Visa de data som har bidragit till Security Center förtroende för en avisering:
- Under säkerheten uppmärksamma bladet under **förtroende**, visa observationer som bidrog till förtroendepoäng och få insikter som är relaterade till aviseringen. Det ger dig mer om typen av aktiviteter som orsakade aviseringen.

 ![Misstänkt förtroendepoäng][2]

Använda Security Center förtroendepoäng att prioritera aviseringar prioritering i din miljö. Förtroendepoäng sparar du tid och arbete genom att automatiskt undersöka aviseringar, tillämpa branschens bästa praxis och intelligenta algoritmer och fungerar som ett svar för att avgöra vilka hot är verkliga och där du behöver fokusera din uppmärksamhet.


## <a name="next-steps"></a>Nästa steg
Den här artikeln beskrivs hur du använder förtroendepoäng för att prioritera aviseringar undersökning. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.



<!--Image references-->
[1]: ./media/security-center-confidence-score/confidence-score.png
[2]: ./media/security-center-confidence-score/suspicious-confidence-score.png
