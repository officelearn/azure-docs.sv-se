---
title: Säkra poäng i Azure Security Center | Microsoft Docs
description: " Prioritera dina säkerhets rekommendationer med hjälp av säkra poäng i Azure Security Center. "
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
ms.openlocfilehash: 79154a13722654ef5cbbe7ac99bb67d4b761fe60
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76903441"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Förbättra dina säkra poäng i Azure Security Center

> [!NOTE]
> Det finns en förbättrad säker Poäng som är tillgänglig i för hands versionen. Den förbättrade säkra poängen kommer att ersätta de befintliga säkra poängen, men för en stund kommer de att köras sida vid sida för att under lätta över gången.
>
> Mer information om fördelarna med förbättrade säkra Poäng finns [här](secure-score-security-controls.md).
>
> Om du vill delta i förhands granskningen öppnar du Azure Portal, startar Azure Security Center och väljer säkra poäng. Därifrån visas en banderoll överst på sidan som erbjuder den nya säkra poängen. Du kan också klicka [här](https://aka.ms/ascnewscore).

Med så många tjänster som erbjuder säkerhets förmåner är det ofta svårt att veta vilka steg du bör vidta för att först skydda och torka din arbets belastning. Azures säkra Poäng granskar dina säkerhets rekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer som ska utföras först. Detta hjälper dig att hitta de allvarliga säkerhets sårbarheterna så att du kan prioritera undersökningen. Secure Score är ett verktyg som hjälper dig att utvärdera säkerhetsstatusen för dina arbetsbelastningar.

## <a name="secure-score-calculation"></a>Säker Poäng beräkning

Security Center imiterar arbetet med en säkerhetsanalytiker, kontrollerar dina säkerhets rekommendationer och tillämpar avancerade algoritmer för att fastställa hur viktiga varje rekommendation är.
Azure Security Center granskar ständigt dina aktiva rekommendationer och beräknar dina säkra Poäng utifrån dem, resultatet av en rekommendation härleds från dess allvarlighets grad och säkerhets metoder som påverkar din arbets belastnings säkerhet.

Security Center ger också en **övergripande säker Poäng**. 

**Övergripande säkra Poäng** är en ackumulering av alla dina rekommendations poäng. Du kan visa dina övergripande säkra poäng i dina prenumerationer eller hanterings grupper, beroende på vad du väljer. Poängen varierar beroende på vilken prenumeration som valts och de aktiva rekommendationerna för dessa prenumerationer.

För att kontrol lera vilka rekommendationer som påverkar dina säkra Poäng mest kan du Visa de tre mest förtroliga rekommendationerna i Security Center instrument panelen eller så kan du sortera rekommendationerna på bladet rekommendationer på listan med hjälp av kolumnen **säker Poäng påverkan** .

Så här visar du de övergripande säkra poängen:

1. Klicka på **Security Center** på Azure-instrumentpanelen och klicka sedan på **säkra Poäng**.

2. Längst upp kan du se säkra Poäng höjd punkter:
   - **Övergripande säkerhetspoäng** motsvarar antal poäng per principer per vald prenumeration
   - **Säkra poäng per kategori** visar vilka resurser som behöver mest uppmärksamhet
   - De **främsta rekommendationerna med säker Poäng påverkan** ger dig en lista över rekommendationer som förbättrar din säkra poäng om du implementerar dem.
 
   ![säkra Poäng](./media/security-center-secure-score/secure-score-dashboard.png)

3. I tabellen nedan kan du se var och en av dina prenumerationer och de övergripande säkra poängen för var och en.

   > [!NOTE]
   > Summan av säkerhetspoängen för varje prenumeration är inte lika med den övergripande säkerhetspoängen. Säkerhetspoängen är en beräkning som bygger på förhållandet mellan dina felfria resurser och ditt sammanlagda antal resurser per rekommendation, inte summan av säkerhetspoäng för dina prenumerationer. 
   >
4. Klicka på **Visa rekommendationer** för att se rekommendationerna för den prenumerationen som du kan åtgärda för att förbättra dina säkra poäng.
4. I listan över rekommendationer kan du se att för varje rekommendation finns det en kolumn som representerar den **säkra Poäng effekten**. Det här värdet anger hur mycket din totala säkra poäng kommer att öka om du följer rekommendationerna. På skärmen nedan, om du till exempel **reparerar säkerhets risker i behållar säkerhetskonfigurationer**, kommer dina säkra poäng att öka med 35 punkter.

   ![säkra Poäng](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Individuella säkra Poäng

Om du dessutom vill visa enskilda säkra Poäng kan du hitta dem på bladet individuell rekommendation.  

**Säkerhetspoäng för rekommendation** är en beräkning som bygger på förhållandet mellan dina felfria resurser och ditt sammanlagda antal resurser. Om antalet felfria resurser är lika med det totala antalet resurser, får du 50 poäng, vilket är högsta möjliga säkerhetspoäng för rekommendationer. Åtgärda resurserna med fel genom att följa rekommendationerna så kommer du närmare högsta möjliga säkerhetspoäng.

**Rekommendationernas inverkan** visar med hur mycket dina säkerhetspoäng ökar om du utför de rekommenderade stegen. Exempel: om din säkerhetspoäng är 42 och **rekommendationens inverkan** är + 3 förbättras dina poäng till 45 om du utför stegen i rekommendationen.

Rekommendationen visar vilka hot din arbets belastning exponeras för om reparations stegen inte vidtas.

![individuell rekommendation säker Poäng](./media/security-center-secure-score/indiv-recommendation-secure-score.png)







## <a name="next-steps"></a>Nästa steg
Den här artikeln visar hur du kan förbättra din säkerhets position med **säkra Poäng** i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Vanliga frågor och svar om Azure Security Center](security-center-faq.md): Här finns vanliga frågor om tjänsten.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.


