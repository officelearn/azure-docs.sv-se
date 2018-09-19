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
ms.date: 09/21/2018
ms.author: rkarlin
ms.openlocfilehash: fc521db9ad753c4162b65abfd2f9f23c318fa994
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/18/2018
ms.locfileid: "46131324"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Förbättra dina säker poäng i Azure Security Center


Med så många tjänster som erbjuder säkerhetsfördelarna, är det ofta svårt att veta vilka steg du ska vidta först att skydda och förstärka din arbetsbelastning. Azure Security Center säker poängen går igenom din säkerhetsrekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer för att utföra först, vilket hjälper dig att hitta de mest allvarliga säkerhetsriskerna, så att du kan prioritera undersökningen. Säker poängen är ett mått-verktyg som hjälper dig att skydda din säkerhet för att uppnå en säker arbetsbelastning.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-intro-sentence.md)]


![Skydda score instrumentpanel](./media/security-center-secure-score/secure-score-dashboard.png)

## <a name="secure-score-calculation"></a>Skydda poängberäkningen

Security Center imiterar verk som tillhör säkerhetsanalytiker, granska dina säkerhetsrekommendationer och tillämpa avancerade algoritmer för att avgöra hur viktigt är varje rekommendation.
Azure Security center ständigt granskar du aktiva rekommendationer och beräknar dina säker poäng som baseras på dem, poängen för en rekommendation härleds från dess allvarlighetsgrad och metodtips som påverkar säkerheten för din arbetsbelastning mest.

Den **skyddar score** är en beräkning som baserat på förhållandet mellan din felfria resurser och dina totala resurser. Om antalet felfria resurser är lika med det totala antalet resurser, får du maximal säker poäng 50. Försök att hämta säkra poängen närmare att högst poäng genom att åtgärda skadade resurser genom att följa rekommendationerna.

Security Center innehåller också en säker övergripande poäng. 

**Skydda övergripande poäng** är en anhopning av alla rekommendationer. Du kan visa din övergripande säker poäng över dina prenumerationer och hanteringsgrupper, beroende på vad du väljer. Poängen varierar beroende på prenumerationen som valts och aktiva rekommendationer för dessa prenumerationer.

 

Du kan visa topp 3 störst rekommendationerna i instrumentpanelen i Security Center för att kontrollera vilka rekommendationerna är påverka säker poängen mest, eller du kan sortera rekommendationerna i den rekommendationer bladet med hjälp av den **säker poäng påverkan** kolumn.


Visa dina övergripande säker poäng:

1. I Azure-instrumentpanelen klickar du på **Security Center** och klicka sedan på **rekommendationer**.
2. Överst ser du säkra poäng, som representerar poäng per principer per valda prenumerationen. 
2. I tabellen nedan som visar en lista över rekommendationer, du kan se att det för varje rekommendation finns en kolumn som representerar den **skyddar score påverkan**. Det här talet anger hur mycket den totala säkra dina poäng förbättras om du följer rekommendationerna. Till exempel på skärmen nedan om du **åtgärda sårbarheter i behållaren säkerhetskonfigurationer**, säker resultatet kommer att ökas med 35 punkter.

   ![säker poäng](./media/security-center-secure-score/security-center-secure-score1.png)

## <a name="individual-secure-score"></a>Enskilda säker poäng

Dessutom om du vill visa enskilda säker poäng hittar du dessa alternativ inom bladet enskilda rekommendation.  

Den **rekommendation skyddar score** är en beräkning som baserat på förhållandet mellan din felfria resurser och dina totala resurser. Om antalet felfria resurser är lika med det totala antalet resurser, får du den maximala säkra poängen för rekommendationen. Försök att hämta säkra poängen närmare att högst poäng genom att åtgärda skadade resurser genom att följa anvisningarna för reparation.

Den **rekommendation inverkan** gör att du vet hur mycket säkra dina poäng förbättras om du har följt instruktionerna rekommendation. Till exempel om ditt säkra poängen är 42 och **rekommendation inverkan** är + 3, om du utför stegen i rekommendationen för dina säker poäng förbättras om du vill bli 45.

Rekommendationen visar vilka hot arbetsbelastningen utsätts för om åtgärdssteg inte utförs.

![enskilda rekommendation säker poäng](./media/security-center-secure-score/indiv-recommendation-secure-score.png)

## <a name="next-steps"></a>Nästa steg
Den här artikeln visar dig hur du kan förbättra din säkerhet hållning med hjälp av **säker poäng** i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.


