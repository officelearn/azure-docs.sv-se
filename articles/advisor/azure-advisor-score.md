---
title: Optimera Azure-arbetsbelastningar med Advisor-Poäng
description: Använd Advisor-Poäng för att få ut mesta möjliga av Azure
ms.topic: article
ms.date: 09/09/2020
ms.openlocfilehash: c9e6a13b39ac3436a71b6cecee4c91c609589378
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2020
ms.locfileid: "90039098"
---
# <a name="advisor-score-to-get-the-most-out-of-azure"></a>Advisor-Poäng för att få ut mesta möjliga av Azure

## <a name="introduction-to-advisor-score"></a>Introduktion till Advisor-Poäng

Azure Advisor innehåller rekommendationer för bästa praxis för dina arbets belastningar. Dessa rekommendationer är anpassade och åtgärdade för att hjälpa dig:
* Förbättra position i dina arbets belastningar och optimera dina Azure-distributioner
* Förhindra de vanligaste problemen proaktivt genom att följa bästa praxis
* Utvärdera dina Azure-arbetsbelastningar mot de fem grundarna i [Microsoft Azure väl utformat ramverket](https://docs.microsoft.com/azure/architecture/framework/)

Som grund funktion i Advisor, är **Advisor-poängen** lättare att hjälpa dig att uppnå dessa mål effektivt och effektivt. 

För att få ut mesta möjliga av Azure är det viktigt att förstå var du befinner dig i arbets belastnings optimerings resan, vilka tjänster/resurser som konsumeras och vilka som inte är det. Dessutom vill du veta hur du prioriterar dina åtgärder, baserat på rekommendationer, för att maximera resultatet. Det är också viktigt att spåra och rapportera förloppet som du gör i optimerings resan. Med **Advisor-poängen**kan du enkelt göra allt detta med vår nya spelifieringsprogram-upplevelse. Som din personliga moln konsult kan Azure Advisor kontinuerligt utvärdera din användnings telemetri och resurs konfiguration för att söka efter bransch bästa praxis. Advisor sammanställer sedan sina resultat i en enda poäng så att du snabbt kan tala om för dig att du ska vidta de åtgärder som krävs för att bygga pålitliga, säkra och kostnads effektiva lösningar. Rotations poängen består av en övergripande Poäng som kan delas upp i fem kategori resultat, en för varje kategori av Azure Advisor som representerar de fem grundarna i det välstrukturerade ramverket. Du kan spåra förloppet med tiden genom att visa dina totala poäng-och kategori poäng med varje dag, varje vecka och månads vis och du kan ställa in benchmarks för att hjälpa dig att uppnå dina mål. 

 ![Rådgivarens Poäng upplevelse](./media/advisor-score-1.png)

## <a name="how-to-consume-advisor-score"></a>Använda Advisor-Poäng
Advisor visar de övergripande bedömnings poängen och nedbrytningen för Advisor-kategorier i procent. En poäng på 100% i valfri kategori innebär att alla resurser som utvärderas av Advisor följer de metoder som rekommenderas av Advisor. I andra änden av spektrumet innebär resultatet 0% att ingen av dina resurser utvärderas av Advisor enligt rådgivarens rekommendationer. Genom att använda dessa Poäng kärnor kan du enkelt uppnå följande flöde:
* **Advisor-Poäng** för att hjälpa dig att få bas linje hur din arbets belastning/prenumerationer bygger på Advisor-poängen. Du kan också se de historiska trenderna för att förstå vad din trend är.
* **Rådgivarens Kategori Poäng** för att hjälpa dig att förstå vilka kategorier som behöver mer uppmärksamhet och hjälper dig med prioritering
* **Potentiell Poäng ökning** av varje rekommendation som hjälper dig att prioritera dina reparations åtgärder för varje kategori

Bidragen från varje rekommendation till dina poäng visas tydligt på sidan Översikt i Azure Portal. Du kan öka poängen genom att anta bästa praxis och du kan prioritera de rekommendationer som har största **möjliga poäng ökning** för att göra den snabbaste förloppet med den tid som du har.  

![Klassificerings resultat effekt](./media/advisor-score-2.png)

Eftersom rådgivarens bedömnings metodik använder extra vikt till dyrare resurser med långa rekommendationer, kan du få ut så mycket som möjligt genom att åtgärda resurserna med den högsta detaljist kostnaden först. Om rekommendations rekommendationerna inte är relevanta för en enskild resurs kan du stänga dessa rekommendationer för att undanta dem från Poäng beräkningen och skicka feedback till Advisor för att förbättra sina rekommendationer. 

## <a name="how-is-advisor-score-calculated"></a>Hur beräknas Advisor-Poäng?
Advisor visar kategori poängen och den övergripande Advisor-poängen i procent. En poäng på 100% i valfri kategori innebär att alla resurser, *som utvärderas av Advisor*, följer de rekommenderade metoder som rådgivare rekommenderar. I den andra änden av spektrumet innebär resultatet 0% att ingen av dina resurser, utvärderas av Advisor, följ Advisor-rekommendationerna. 
**Var och en av de fem kategorierna har ett högsta möjliga resultat på 100.** Den totala Advisor-poängen beräknas som en summa av varje tillämplig kategori poäng, dividerat med summan av de högsta potentiella poängen från alla tillämpliga kategorier. För de flesta prenumerationer innebär det att Advisor lägger till poängen från varje kategori och dividerar med 500. **Varje kategori Poäng beräknas dock endast om du använder resurser som utvärderas av Advisor.**

### <a name="scoring-methodology"></a>Bedömnings metod: 
Beräkningen av Advisor-poängen kan sammanfattas i fyra steg:
1. Advisor beräknar den **dagliga detalj handels kostnaden för påverkade resurser**, som är resurserna på dina prenumerationer som har minst en rekommendation i Advisor.
2. Advisor beräknar den **dagliga detalj handels kostnaden för utvärderade resurser**, som är de resurser som övervakas av Advisor, oavsett om de har några rekommendationer eller inte. 
3. För varje rekommendations typ beräknar Advisor den **felfria resurs kvoten**, vilket är kostnaden för resurser som påverkas dividerat med kostnaden för utvärderade resurser.
4. Advisor använder tre ytterligare vikter för det felfria resurs förhållandet i varje kategori:
* Rekommendationer med större påverkan viktas tyngre än de med lägre påverkan.
* Resurser med långsiktiga rekommendationer kommer att räkna mer än dina poäng.
* Resurser som du stänger i Advisor tas endast bort från din poäng beräkning. 
    
Advisor tillämpar den här modellen på en rådgivares kategori nivå (säkerhet använder [säker Poäng](https://docs.microsoft.com/azure/security-center/secure-score-security-controls#introduction-to-secure-score) modell) och ger oss Advisor-Poäng för varje kategori och ytterligare ett enkelt genomsnitt ger slut på Advisor-poängen.


## <a name="advisor-score-faq"></a>Vanliga frågor och svar om Advisor
* **Hur ofta uppdateras min Poäng?**
Poängen uppdateras minst en gång per dag. 
* **Behöver jag se rekommendationerna i Advisor för att få poäng för mina Poäng?**
Nej. Poängen visar om du använder bästa praxis som rådgivare rekommenderar, även om du aldrig ser dessa rekommendationer i Advisor och antar bästa praxis proaktivt.  
* **Hur beräknar Advisor den dagliga detalj handels kostnaden för resurser i en prenumeration?**
Advisor använder priserna för *betala per* användning som har publicerats på prissättnings sidan för Azure.com, som inte återspeglar några tillämpliga rabatter, multiplicerat med antalet användnings dagar den sista dagen då resursen allokerades. Om du utelämnar rabatter från beräkningen av resurs kostnaden blir Advisor-resultatet jämförbart över prenumerationer, hyres gäster och registreringar där rabatter kan variera. 
* **Vad händer om en rekommendation inte är relevant?**
Om du stänger av rekommendation från Advisor kommer den att utelämnas från beräkningen av dina poäng. När du stänger av rekommendationerna kan du också förbättra rekommendationernas kvalitet.
* **Varför har min poäng ändring?** Poängen kan ändras om du reparerar påverkade resurser genom att använda bästa praxis som rådgivare rekommenderar. Om du eller någon som har behörighet till din prenumeration har ändrat eller skapat nya resurser, kan du också se variationer i poängen eftersom poängen baseras på en kvot av de resurser som påverkas, i förhållande till den totala kostnaden för alla resurser.
* **Beror resultatet på hur mycket jag tillbringar på Azure?**
Nej. Poängen är utformad för att styra storleken på en prenumeration och tjänst kombination. 
* **Skiljer sig bedömnings metoden mellan arbets belastningar för produktions-och dev-test?**
Nej, inte just nu, men du kan stänga rekommendationer för enskilda resurser om dessa resurser används för utveckling och testning och rekommendationen inte gäller.
* **Kan jag jämföra resultatet mellan en prenumeration med 100 resurser och en prenumeration med 100 000-resurser?**
Bedömnings metoden är utformad för att styra antalet resurser i en prenumeration och tjänst kombination, så prenumerationer med färre resurser kan ha högre eller lägre poäng än prenumerationer med fler resurser. 

## <a name="how-to-access-advisor"></a>Så här ansluter du till Advisor
Advisor-poängen är i offentlig för hands version i Azure Portal. Du måste gå till Advisor-avsnittet så hittar du Advisor-Poäng som det andra meny alternativet i det vänstra navigerings fältet. 

![Advisor-Poäng inmatning-punkt](./media/advisor-score-3.png)

## <a name="next-steps"></a>Nästa steg

Mer information om Advisor-rekommendationer finns i:
* [Introduktion till Advisor](advisor-overview.md)
* [Kom igång med Advisor](advisor-get-started.md)
* [Kostnadsrekommendationer i Advisor](advisor-cost-recommendations.md)
* [Rekommendationer för Advisor-prestanda](advisor-performance-recommendations.md)
* [Rekommendationer för Advisor-säkerhet](advisor-security-recommendations.md)
* [Rekommendationer om operativa rekommendationer](advisor-operational-excellence-recommendations.md)
