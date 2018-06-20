---
title: Utterances i THOMAS appar i Azure | Microsoft Docs
description: Lägg till utterances i språk förstå Intelligent Service (THOMAS) appar.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 02/13/2018
ms.author: v-geberr
ms.openlocfilehash: 7c2cd84df8f1eccbd30a7c8054ec8d06336cf2dd
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264654"
---
# <a name="utterances-in-luis"></a>Utterances i THOMAS

**Utterances** indata från användaren som din app behöver tolka. För att träna THOMAS att extrahera avsikter och entiteter från dem, är det viktigt att samla in en mängd olika indata för varje avsikt. Aktiva learning eller hur du fortsätter att träna på nya utterances är viktigt att datorn lärt dig intelligence THOMAS tillhandahåller.

Samla in fraser som du tror att användarna ska ange. Inkludera utterances som betyda samma sak men är uppbyggda på olika sätt i word-längd och word placering. 

## <a name="how-to-choose-varied-utterances"></a>Hur du väljer olika utterances
När du först komma igång med [lägger du till exempel utterances] [ add-example-utterances] THOMAS modellen, här är några principer som du bör tänka på.

### <a name="utterances-arent-always-well-formed"></a>Utterances formaterad inte alltid korrekt.
Det kan vara en mening, t.ex. ”bok mig en biljett till Paris” eller ett fragment i en mening, t.ex. ”boka” eller ”Paris svarta”.  Användare gör ofta stavfel. Överväg att oavsett om du stavningen användarindata vidare till THOMAS när du planerar din app. Den [Bing stavningskontroll kontrollera API] [ BingSpellCheck] kan integreras med THOMAS. Du kan associera appen THOMAS med en extern nyckel för Bing stavningskontroll kontrollera API när du publicerar den. Om du inte har angett kontrollera användaren utterances, bör du träna THOMAS på utterances som innehåller Skriv- och stavfel.

### <a name="use-the-representative-language-of-the-user"></a>Använd representativt språket för användaren
När du väljer utterances, Tänk på att du förväntar dig en vanliga ord eller en fras inte kanske till vanliga användare av ditt klientprogram. De kan inte ha domän upplevelse. Så var försiktig när du använder ord eller fraser som en användare bara säger om de vore en expert.

### <a name="choose-varied-terminology-as-well-as-phrasing"></a>Välj olika terminologi samt frasering
Du kommer märka att även om du gör för att skapa olika meningen mönster kan du fortfarande upprepas vissa ordförråd.

Vidta dessa exempel utterances:
```
how do I get a computer?
Where do I get a computer?
I want to get a computer, how do I go about it?
When can I have a computer? 
```
Här, core termen ändras ”dator” inte. De kan säga stationär dator, bärbar dator, arbetsstation eller även bara datorn. THOMAS härleder Intelligent synonymer från kontexten, men när du skapar utterances för träning, är det fortfarande är bättre att variera dem.

## <a name="example-utterances-in-each-intent"></a>Exempel utterances i varje avsikt
Varje avsikt måste ha exempel utterances, minst 10 – 15. Om du har syftet som inte har några exempel utterances kan du inte träna THOMAS. Om du har syftet med en eller mycket få exempel utterances kommer THOMAS inte förutse avsikten. 

## <a name="add-small-groups-of-10-15-utterances-for-each-authoring-iteration"></a>Lägg till mindre grupper av 10 – 15 utterances för varje redigering upprepning
Lägg inte till ett stort antal utterances i varje iteration av modellen. Lägg till utterances i mängder flera. [Train](luis-how-to-train.md), [publicera](publishapp.md), och [testa](interactive-test.md) igen.  

THOMAS skapar effektiva modeller med utterances som väljs noggrant. Lägga till för många utterances är inte viktig eftersom förvirring.  

Det är bättre att börja med ett par utterances sedan [granska endpoint utterances](label-suggested-utterances.md) för korrekt avsiktshantering prognoser och entiteten extrahering.

## <a name="training-utterances"></a>Utbildning utterances
Utbildning är icke-deterministiska: utterance förutsägelser kan variera mellan versioner eller appar.

## <a name="testing-utterances"></a>Testa utterances 

Utvecklare bör börja testar sina THOMAS program med verkliga trafik genom att skicka utterances till slutpunkten. Dessa utterances används för att förbättra prestanda för avsikter och entiteter med [granska utterances](label-suggested-utterances.md). Tester som skickades med webbplatsen THOMAS testning fönstret skickas inte via slutpunkten och därför bidrar inte till aktiva utbildning. 

## <a name="review-utterances"></a>Granska utterances
När din modell är utbildade publicerade och mottagande [endpoint](luis-glossary.md#endpoint) frågor, [granska utterances](label-suggested-utterances.md) föreslås av THOMAS. THOMAS väljer endpoint utterances som har låg poäng för avsikt eller entitet. 

## <a name="best-practices"></a>Bästa praxis
Granska [metodtips](luis-concept-best-practices.md) vill veta mer.

## <a name="next-steps"></a>Nästa steg
Se [lägger du till exempel utterances] [ add-example-utterances] information om hur du tränar en THOMAS app att förstå användaren utterances.

[add-example-utterances]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-how-to-add-example-utterances
[BingSpellCheck]: https://docs.microsoft.com/azure/cognitive-services/bing-spell-check/proof-text