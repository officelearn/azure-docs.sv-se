---
title: Skydda poäng i Azure Security Center | Microsoft Docs
description: " Prioritera dina säkerhetsrekommendationer som använder säker poängen i Azure Security Center. "
services: security-center
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: c42d02e4-201d-4a95-8527-253af903a5c6
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/5/2018
ms.author: rkarlin
ms.openlocfilehash: 3a377441758fcd7dd91deefb5cae91579e881498
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/05/2018
ms.locfileid: "51007067"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Förbättra dina säker poäng i Azure Security Center


Med så många tjänster som erbjuder säkerhetsfördelarna, är det ofta svårt att veta vilka steg du ska vidta först att skydda och förstärka din arbetsbelastning. Azure säker poängen går igenom din säkerhetsrekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer för att utföra först. Detta hjälper dig att hitta de mest allvarliga säkerhetsriskerna, så att du kan prioritera undersökningen. Säker poängen är ett verktyg som hjälper dig att bedöma din arbetsbelastning säkerhetsposition.

![Skydda score instrumentpanel](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Skydda poängberäkningen

Security Center imiterar verk som tillhör en säkerhetsanalytiker, granska dina säkerhetsrekommendationer och tillämpa avancerade algoritmer att avgöra hur viktigt är varje rekommendation.
Azure Security center ständigt granskar du aktiva rekommendationer och beräknar dina säker poäng som baseras på dem, poängen för en rekommendation härleds från dess allvarlighetsgrad och säkerhetsmetoder som påverkar säkerheten för din arbetsbelastning mest.

Security Center innehåller också en **övergripande skyddar score**. 

**Skydda övergripande poäng** är en anhopning av ditt rekommendation resultat. Du kan visa din övergripande säker poäng över dina prenumerationer och hanteringsgrupper, beroende på vad du väljer. Poängen varierar beroende på prenumerationen som valts och aktiva rekommendationer för dessa prenumerationer.

 
Om du vill kontrollera vilka rekommendationer påverka säker poängen mest, du kan visa de tre översta störst rekommendationerna i instrumentpanelen i Security Center eller du kan sortera rekommendationerna i den rekommendationer bladet med hjälp av den **säker poäng påverkan** kolumn.


Visa dina övergripande säker poäng:

1. I Azure-instrumentpanelen klickar du på **Security Center** och klicka sedan på **rekommendationer**.
2. Överst ser du säkra poäng, som representerar poäng per principer per valda prenumerationen. 
2. I tabellen nedan som visar en lista över rekommendationer, du kan se att det för varje rekommendation finns en kolumn som representerar den **skyddar score påverkan**. Det här talet anger hur mycket den totala säkra dina poäng förbättras om du följer rekommendationerna. Till exempel på skärmen nedan om du **åtgärda sårbarheter i behållaren säkerhetskonfigurationer**, säker resultatet kommer att ökas med 35 punkter.

   ![säker poäng](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Enskilda säker poäng

Dessutom om du vill visa enskilda säker poäng hittar du dessa alternativ inom bladet enskilda rekommendation.  

Den **rekommendation skyddar score** är en beräkning som baserat på förhållandet mellan din felfria resurser och dina totala resurser. Om antalet felfria resurser är lika med det totala antalet resurser, får du den maximala säkra poängen för rekommendation av 50. Försök att hämta säkra poängen närmare att högst poäng genom att åtgärda skadade resurser genom att följa rekommendationerna.

Den **rekommendation inverkan** gör att du vet hur mycket säker poäng förbättrar om du har följt instruktionerna rekommendation. Exempel: om din säker poängen är 42 och **rekommendation inverkan** är + 3, utför stegen i rekommendationen för att förbättra dina poäng blir 45.

Rekommendationen visar vilka hot arbetsbelastningen utsätts för om åtgärdssteg inte utförs.

![enskilda rekommendation säker poäng](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur du kan förbättra din säkerhet hållning med hjälp av **säker poäng** i Azure Security Center. Mer information om Security Center finns:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.


