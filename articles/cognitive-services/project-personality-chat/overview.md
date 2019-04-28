---
title: Vad är Project Personality Chat?
titlesuffix: Azure Cognitive Services
description: Den här artikeln är en översikt över Azure Project Personality Chat, som är ett molnbaserat API för att förbättra din chattrobots konversationsförmåga.
services: cognitive-services
author: noellelacharite
manager: nitinme
ms.service: cognitive-services
ms.subservice: personality-chat
ms.topic: overview
ms.date: 05/07/2018
ms.author: nolachar
comment: As a bot developer, I want my bot to be able to handle small talk in a consistent tone so that my bot appears more complete and conversational.
ms.openlocfilehash: c7f7a8c65717acd5a19e92b7e0437dc4b8628909
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62103642"
---
# <a name="what-is-project-personality-chat"></a>Vad är Project Personality Chat?

Project Personality Chat förbättrar chattrobotens konversationsförmåga genom att småprat hanteras i enlighet med en distinkt personlighet du väljer. I Personality Chat används klassificering av avsikter till att identifiera avsikter i vanligt småprat. Sedan genereras svar som ligger i linje med en viss personlighet. Baserat på avsikten och konfidenspoängen väljer chattroboten antingen bästa möjliga svar från en utvald redigeringsbas, eller så genereras ett svar i realtid med hjälp av djupa neurala nätverk (DNN). Du kan välja mellan tre standardpersonligheter. Modellen returnerar svar som ligger närmast den valda personligheten.

Det finns en anpassningsbar frasuppsättning för frågor i vanligt småprat. Du kan enkelt integrera den med Microsoft Bot Framework SDK. Detta SDK gör att du sparar tid och pengar genom att inte behöva skriva fraserna från grunden.

## <a name="getting-started-with-project-personality-chat"></a>Kom igång med Project Personality Chat

Du kan gå till sidan för Project Personality Chat och chatta med den tillgängliga demon, och begära tidig åtkomst när tjänsten blir tillgänglig.
Idag kan du också integrera det anpassningsbara frasbiblioteket i dina chattrobotar via Microsoft Bot Framework SDK. <br>
[Exempel: Integrera Personality Chat i en chattrobot](https://github.com/Microsoft/BotBuilder-PersonalityChat/) <br>
[Prova Personality Chat-biblioteket](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp)

## <a name="generating-responses-using-neural-networks"></a>Generera svar med hjälp av neurala nätverk

I Personality Chat används modeller för djupinlärning till att lära in vanliga konversationsmönster och generera svar. Modellen som genererar svar är ett RNN (recurrent neural network). Den här modellen är inlärd på miljoner konversationer där den lärt sig olika mönster inom mänsklig interaktion. Med hjälp av inlärda mönster över meningsstrukturer kan nya frågor bildas och svar skapas. När du genererar det här nya svaret söker modellen i ett ”ordförråd för skrivning” och väljer de n bästa första orden till svaret. Med hjälp av beam-sökning fortsätter modellen att söka efter de n bästa andra orden till varje genererat första ord. Ett svar anses vara färdigt när modellen väljer ordet ”End of sentence” (EOS). När modellen har alla svar väljer den ut de n bästa svaren baserat på sannolikhetspoängen för hela svaret.

Modellen lär sig att generera kontextmässigt lämpliga svar med rätt ”struktur”. En fråga som ”Vill du prata nu?” säger till exempel ganska mycket om hur ett svar troligen skulle se ut: Det börjar förmodligen med någon form av ”ja” eller ”nej”, och pronomenet är förmodligen ”jag”. Om svaret är ”nej” innehåller svaret förmodligen någon typ av artig förklaring och så vidare.

Systemet försöker att lära sig en språkmodell för grundstrukturen i samtalet. Den här strukturen ska tillåta att svaren delvis påverkas av externa begränsningar som ämnet, personligheten och så vidare.  Eftersom de här begränsningarna lärs in från breda mönster passar de särskilt bra till småprat.

![Sekvens-till-sekvens-modell för svarsgenerering](./media/overview/sequence-to-sequence-model.png)

## <a name="personality-modeling"></a>Modellering av personlighet

 I alla datadrivna konversationsmodeller är det en utmaning att hålla sig till en enhetlig ”personlighet”. PERSONA definieras som den personlighetstyp som framträder i konversationen för användaren. Du kan tänka på en persona som att den består av identitetselement, språkbeteende och stilen i interaktionen. I den aktuella versionen av Personality Chat ligger fokus på språkbeteende och interaktionsstil.

Den här modellen representerar varje enskild deltagare som en vektor eller ett inbäddat element. Den kodar information från deltagaren som sedan påverkar innehållet och stilen i svaren. Modellen lär sig sedan representationer av deltagarna utifrån deras respektive innehåll. Deltagare med liknande svar brukar ha liknande inbäddade element som ligger nära varandra i vektorrummet. Därför kan träningsdata från närliggande deltagare i vektorrummet hjälpa modellen att generalisera. Modellen grupperar effektivt deltagare med liknande representationer i vektorrummet i ett personlighetskluster med ett ”persona-id”.

För en standardpersona används attribut och utvalda svar till att hitta bäst matchande deltagarkluster. Det här klustret väljs sedan som persona-id för respektive standardpersonlighet. Alla typer av personligheter kan fortsätta att utvecklas genom att du använder en uppsättning svar. Sedan skapas konversationer som emulerar individens persona i termer av språkliga svar och andra viktiga egenskaper.

![Personamodellering med hjälp av deltagarkluster](./media/overview/persona-modeling.png)

## <a name="small-talk-intent-understanding"></a>Förståelse av avsikter i småprat

Personality Chat har funktioner för klassificering av avsikter som används till svar i småprat. Dessa klassificerare påverkar inte uppgifter eller informationsfrågor. En övergripande chattklassificerare avgör om frågans avsikt är att småprata. Det görs med hjälp av lexikala och semantiska klassificerare vars poäng sedan räknas ihop. Dessa avsikter tränas upp med hjälp av konversationsdata (som positiva avsiktsexempel) och sökningar/uppgiftsfrågor (för negativa avsiktsexempel).

Andra delklassificerare används till att identifiera typen av småprat så att svaren kan begränsas, som hälsningar, komplimanger, åsikter och så vidare. Dessa djupinlärningsklassificerarna konverterar alla frågor till vektorer med hjälp av CDSSM (Convolutional Deep Structure Semantic Model). De tränas upp med hjälp av tiotusentals utvalda frågor med olika delavsikter. Klassificeraren matchar sedan en fråga med befintliga, identifierade avsiktsklasser genom att söka närmaste matchning i vektorrummet.

Det finns ett antal kontroller som ska hjälpa till att undvika oönskade svar, och de bygger på kunskap från intelligenta agenter som Zo. Som standard är Project Personality Chat inställt på att bara svara på användaravsikter som kan identifieras. Du kan testa om Project Personality Chat passar bra i just din situation. Om du ser någonting där det krävs mer inlärning får du gärna säga det till oss.
