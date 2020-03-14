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
ms.openlocfilehash: 15a94a9724fac29d36f6bb88ee4810b3bc7ca607
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79245257"
---
# <a name="improve-your-secure-score-in-azure-security-center"></a>Förbättra dina säkra poäng i Azure Security Center

> [!NOTE]
> Det finns en förbättrad säker Poäng som är tillgänglig i för hands versionen. Den förbättrade säkra poängen kommer att ersätta de befintliga säkra poängen, men för en stund kommer de att köras sida vid sida för att under lätta över gången.
>
> Mer information om fördelarna med förbättrade säkra Poäng finns [här](secure-score-security-controls.md).
>
> Om du vill delta i förhands granskningen öppnar du Azure Portal, startar Azure Security Center och väljer säkra poäng. Därifrån visas en banderoll överst på sidan som erbjuder den nya säkra poängen. Du kan också klicka [här](https://aka.ms/ascnewscore).

Med så många tjänster som erbjuder säkerhets förmåner är det ofta svårt att veta vilka steg du bör vidta för att först skydda och torka din arbets belastning. Säkra Poäng granskar dina säkerhets rekommendationer och prioriterar dem åt dig, så att du vet vilka rekommendationer som ska utföras först. Detta hjälper dig att hitta de allvarliga säkerhets sårbarheterna så att du kan prioritera undersökningen. Säkra poäng är ett verktyg som hjälper dig att utvärdera din position för arbets belastnings säkerhet.

## <a name="secure-score-calculation"></a>Säker Poäng beräkning

Security Center imiterar arbetet med en säkerhetsanalytiker, kontrollerar dina säkerhets rekommendationer och tillämpar avancerade algoritmer för att fastställa hur viktiga varje rekommendation är.
Azure Security Center granskar ständigt dina aktiva rekommendationer och beräknar dina säkra Poäng utifrån dem, resultatet av en rekommendation härleds från dess allvarlighets grad och säkerhets metoder som påverkar din arbets belastnings säkerhet.

Security Center ger också en **övergripande säker Poäng**. 

**Övergripande säkra Poäng** är en ackumulering av alla dina rekommendations poäng. Du kan visa dina övergripande säkra poäng i dina prenumerationer eller hanterings grupper, beroende på vad du väljer. Poängen varierar beroende på vilken prenumeration som valts och de aktiva rekommendationerna för dessa prenumerationer.

För att kontrol lera vilka rekommendationer som påverkar dina säkra Poäng mest kan du Visa de tre mest förtroliga rekommendationerna i Security Center instrument panelen eller så kan du sortera rekommendationerna på bladet rekommendationer på listan med hjälp av kolumnen **säker Poäng påverkan** .

Så här visar du de övergripande säkra poängen:

1. Klicka på **Security Center** på Azure-instrumentpanelen och klicka sedan på **säkra Poäng**.

2. Längst upp kan du se säkra Poäng höjd punkter:
   - Den **övergripande säkra poängen** visar poängen per principer, per vald prenumeration
   - **Säkra poäng per kategori** visar vilka resurser som behöver mest uppmärksamhet
   - De **främsta rekommendationerna med säker Poäng påverkan** ger dig en lista över rekommendationer som förbättrar din säkra poäng om du implementerar dem.
 
   ![Säkra Poäng](./media/security-center-secure-score/secure-score-dashboard.png)

3. I tabellen nedan kan du se var och en av dina prenumerationer och de övergripande säkra poängen för var och en.

   > [!NOTE]
   > Summan av de säkra poängen för varje prenumeration är inte lika med de övergripande säkra poängen. De säkra poängen är en beräkning baserad på förhållandet mellan dina friska resurser och dina totala resurser per rekommendation, inte en total mängd säkra resultat för dina prenumerationer. 
   >
4. Klicka på **Visa rekommendationer** för att se rekommendationerna för den prenumerationen som du kan åtgärda för att förbättra dina säkra poäng.
4. I listan över rekommendationer kan du se att för varje rekommendation finns det en kolumn som representerar den **säkra Poäng effekten**. Det här värdet anger hur mycket din totala säkra poäng kommer att öka om du följer rekommendationerna. På skärmen nedan, om du till exempel **reparerar säkerhets risker i behållar säkerhetskonfigurationer**, kommer dina säkra poäng att öka med 35 punkter.

   ![Säkra Poäng](./media/security-center-secure-score/security-center-secure-score1.png)



## <a name="individual-secure-score"></a>Individuella säkra Poäng

Om du dessutom vill visa enskilda säkra Poäng kan du hitta dem på bladet individuell rekommendation.  

**Rekommendationen säker Poäng** är en beräkning baserad på förhållandet mellan dina friska resurser och dina totala resurser. Om antalet felfria resurser motsvarar det totala antalet resurser får du högsta möjliga säkerhet för rekommendationen 50. För att försöka få dina säkra Poäng närmare den högsta poängen, korrigerar du de skadade resurserna genom att följa rekommendationerna.

Med **rekommendations effekten** kan du se hur mycket dina säkra Poäng ökar om du använder rekommendations stegen. Om din säkra poäng till exempel är 42 och **rekommendations effekten** är + 3, kan du utföra stegen som beskrivs i rekommendationen för att bli 45.

Rekommendationen visar vilka hot din arbets belastning exponeras för om reparations stegen inte vidtas.

![individuell rekommendation säker Poäng](./media/security-center-secure-score/indiv-recommendation-secure-score.png)




## <a name="next-steps"></a>Nästa steg
Den här artikeln visar hur du kan förbättra din säkerhets position med **säkra Poäng** i Azure Security Center. I följande avsnitt kan du lära dig mer om Security Center:

* [Azure Security Center vanliga](faq-general.md)frågor och svar – hitta vanliga frågor om tjänsten och säkra poäng.
* [Övervakning av säkerhetshälsa i Azure Security Center](security-center-monitoring.md): Här kan du läsa om hur du övervakar dina Azure-resursers hälsa.
* [Säker Poäng – förbättrad](secure-score-security-controls.md)– Läs mer om fördelarna med de förbättrade säkra poängen för närvarande i för hands versionen.