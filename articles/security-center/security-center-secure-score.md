---
title: Säker poäng i Azure Security Center | Microsoft-dokument
description: " Prioritera dina säkerhetsrekommendationer med hjälp av secure score i Azure Security Center. "
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/15/2019
ms.author: memildin
ms.openlocfilehash: 30405ce5cc875144fcd1cf83d4a3f883a0304989
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79415774"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Förbättra dina säkra poäng i Azure Security Center

> [!NOTE]
> Den här artikeln handlar om den tidigare versionen av den säkra poängen. Den här säkra poängupplevelsen är fortfarande tillgänglig från användargränssnittet men kommer att fasas ut över tiden. De två säkra poängupplevelserna körs sida vid sida för att möjliggöra en smidigare övergång.
>
> Mer information om den nyare säkra poängen finns [här](secure-score-security-controls.md).
>

Med så många tjänster som erbjuder säkerhetsfördelar är det ofta svårt att veta vilka åtgärder du ska vidta först för att skydda och skärpa din arbetsbelastning. Secure Score granskar dina säkerhetsrekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer du ska utföra först. Detta hjälper dig att hitta de allvarligaste säkerhetsproblemen så att du kan prioritera undersökningen. Secure Score är ett verktyg som hjälper dig att bedöma din arbetsbelastningssäkerhetsposition.

## <a name="secure-score-calculation"></a>Beräkning av säker poäng

Security Center härmar arbetet för en säkerhetsanalytiker, granska dina säkerhetsrekommendationer och tillämpa avancerade algoritmer för att avgöra hur viktig varje rekommendation är.
Azure Security Center granskar ständigt dina aktiva rekommendationer och beräknar din Secure Score baserat på dem, poängen för en rekommendation härleds från dess allvarlighetsgrad och säkerhet bästa praxis som kommer att påverka din arbetsbelastning säkerhet mest.

Security Center ger dig också en **övergripande säker poäng**. 

**Övergripande Secure Score** är en ackumulering av alla dina rekommendationspoäng. Du kan visa din totala säkra poäng i dina prenumerationer eller hanteringsgrupper, beroende på vad du väljer. Poängen varierar beroende på vilken prenumeration som valts och de aktiva rekommendationerna för dessa prenumerationer.

Om du vill kontrollera vilka rekommendationer som påverkar din säkra poäng mest kan du visa de tre mest effektfulla rekommendationerna på Instrumentpanelen i Säkerhetscenter eller sortera rekommendationerna i rekommendationerlistan med hjälp av **konsekvenskräfsten Säker poäng.**

Så här visar du ditt totala säkra resultat:

1. Klicka på **Säkerhetscenter** på Azure-instrumentpanelen och klicka sedan på **Säker poäng**.

2. Högst upp kan du se höjdpunkterna i Secure Score:
   - Den **övergripande säkra poängen** representerar poängen per policy, per vald prenumeration
   - **Säker poäng efter kategori** visar vilka resurser som behöver mest uppmärksamhet
   - **De bästa rekommendationerna från Secure Score-effekten** ger dig en lista över de rekommendationer som förbättrar din säkra poäng mest om du implementerar dem.
 
   ![Säkra poäng](./media/security-center-secure-score/secure-score-dashboard.png)

3. I tabellen nedan kan du se var och en av dina prenumerationer och den totala Secure Score för varje.

   > [!NOTE]
   > Summan av secure score för varje prenumeration är inte lika med det totala säkra resultatet. Secure Score är en beräkning som baseras på förhållandet mellan dina felfria resurser och dina totala resurser per rekommendation, inte en summa säkra poäng över dina prenumerationer. 
   >
4. Klicka på **Visa rekommendationer** om du vill se rekommendationerna för den prenumerationen som du kan åtgärda för att förbättra ditt säkra resultat.
4. I listan med rekommendationer kan du se att det för varje rekommendation finns en kolumn som representerar **effekten Säker poäng**. Det här numret representerar hur mycket din totala secure score kommer att förbättras om du följer rekommendationerna. Om du till exempel **åtgärdar säkerhetsproblem i behållarens säkerhetskonfigurationer**ökar din säkra poäng med 35 poäng med 35 poäng.

   ![Säkra poäng](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Individuella säkra poäng

Om du vill visa enskilda säkra poäng hittar du dessutom dessa i det enskilda rekommendationsbladet.  

**Rekommendationen säker poäng** är en beräkning baserad på förhållandet mellan dina felfria resurser och dina totala resurser. Om antalet felfria resurser är lika med det totala antalet resurser får du den maximala säkra poängen för rekommendationen 50. Om du vill försöka få din säkra poäng närmare maxpoängen lägger du fast de felaktiga resurserna genom att följa rekommendationerna.

**Rekommendationseffekten** visar hur mycket din Secure Score förbättrar om du tillämpar rekommendationsstegen. Om ditt säkra resultat till exempel är 42 och **rekommendationseffekten** är +3, förbättrar du stegen som beskrivs i rekommendationen att din poäng blir 45.

Rekommendationen visar vilka hot din arbetsbelastning exponeras för om reparationsåtgärderna inte vidtas.

![individuell rekommendation säker poäng](./media/security-center-secure-score/indiv-recommendation-secure-score.png)


## <a name="next-steps"></a>Nästa steg
Den här artikeln visade hur du kan förbättra din säkerhetsposition med **Secure Score** i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
