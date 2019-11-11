---
title: 'Självstudie: batch-testning för att hitta problem – LUIS'
titleSuffix: Azure Cognitive Services
description: Den här självstudien visar hur du använder batch-testning för att hitta uttryck förutsägelse problem i din app och åtgärda dem.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 68a0016e034f4642c4e4ff166a1456f7ecf1ee3c
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904234"
---
# <a name="tutorial-batch-test-data-sets"></a>Självstudie: data uppsättningar för batch-test

Den här självstudien visar hur du använder batch-testning för att hitta uttryck förutsägelse problem i din app och åtgärda dem.  

Med batch-testning kan du verifiera den aktiva, tränade modellens tillstånd med en känd uppsättning märkta yttranden och entiteter. I den JSON-formaterade kommando filen lägger du till yttranden och anger de enhets etiketter som du behöver förutsäga inuti uttryck. 

Krav för batch-testning:

* Högst 1000 yttranden per test. 
* Inga dubbletter. 
* Tillåtna entitetstyper: endast enhets belärt entiteter för enkel och sammansatt. Batch-testning är bara användbart för inlärda avsikter och entiteter.

När du använder en annan app än den här själv studie kursen ska du *inte* använda exemplet yttranden som redan har lagts till i ett avsikts syfte. 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

**I den här självstudiekursen får du lära du dig att:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Importera exempelappen
> * Skapa en batch-testfil 
> * Köra ett batch-test
> * Granska test resultat
> * Åtgärda fel 
> * Testa om batchen

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>Importera exempelappen

Fortsätt med appen du skapade i föregående självstudie med namnet **HumanResources**. 

Använd följande steg:

1.  Ladda ned och spara [JSON-filen för appen](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/custom-domain-review-HumanResources.json).

2. Importera JSON-koden till en ny app.

3. I avsnittet **Hantera** går du till fliken **Versioner**, klonar versionen och ger den namnet `batchtest`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. Eftersom versionsnamnet används i webbadressen får namnet inte innehålla några tecken som är ogiltiga i webbadresser. 

4. Träna appen.

## <a name="batch-file"></a>Kommando fil

1. Skapa `HumanResources-jobs-batch.json` i en text redigerare eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-jobs-batch.json) den. 

2. I den JSON-formaterade kommando filen lägger du till yttranden med den **avsikt** som du vill förutsäga i testet. 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>Kör batchen

1. Välj **test** i det övre navigerings fältet. 

2. Välj **panelen för batch-testning** på den högra panelen. 

    [![skärm bild av LUIS-appen med batch test panel markerat](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Välj **Importera data uppsättning**.

    [![skärm bild av LUIS-appen med importerad data uppsättning markerat](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Välj fil platsen för `HumanResources-jobs-batch.json`s filen.

5. Namnge data uppsättningen `intents only` och välj **färdig**.

    ![Välj fil](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Klicka på knappen **Kör**. 

7. Välj **se resultat**.

8. Granska resultatet i grafen och förklaringen.

    [![skärm bild av LUIS-appen med batch test resultat](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Granska resultat från batch

I batch-diagrammet visas fyra kvadranter av resultat. Till höger om diagrammet är ett filter. Filtret innehåller avsikter och entiteter. När du väljer en [del av diagrammet](luis-concept-batch-test.md#batch-test-results) eller en punkt i diagrammet visas tillhör ande uttryck under diagrammet. 

Vid hovring över diagrammet kan ett mushjul förstora eller minska visningen i diagrammet. Detta är användbart när det finns många punkter i diagrammet grupperade nära varandra. 

Diagrammet är i fyra kvadranter, med två av avsnitten som visas i rött. **Detta är de avsnitt som ska fokuseras på**. 

### <a name="getjobinformation-test-results"></a>GetJobInformation test resultat

Test resultaten för **GetJobInformation** som visas i filtret visar att 2 av de fyra förutsägelserna lyckades. Välj namnet **falskt negativ** längst ned till vänster för att se yttranden under diagrammet. 

Använd tangent bordet, CTRL + E, för att växla till vyn etikett för att se den exakta texten för användaren uttryck. 

Uttryck-`Is there a database position open in Los Colinas?` är märkt som _GetJobInformation_ , men den aktuella modellen förutsäger uttryck som _ApplyForJob_. 

Det finns nästan tre gånger så många exempel som **ApplyForJob** än **GetJobInformation**. Detta är inte ens till exempel yttranden vikter i **ApplyForJob** avsikts förmån, vilket orsakar felaktig förutsägelse. 

Observera att båda metoderna har samma antal fel. En felaktig förutsägelse i en avsikt påverkar även den andra avsikten. Båda har fel på grund av att yttranden felaktigt förväntades för ett avsikts sätt och inte heller är felaktigt förväntat för en annan avsikt. 

<a name="fix-the-app"></a>

## <a name="how-to-fix-the-app"></a>Så här åtgärdar du appen

Målet med det här avsnittet är att alla yttranden ska förutsägas korrekt för **GetJobInformation** genom att åtgärda appen. 

En synes snabb korrigering skulle vara att lägga till de här kommando filen yttranden till rätt avsikt. Det är inte det du vill göra. Du vill att LUIS ska förutsäga dessa yttranden utan att lägga till dem som exempel. 

Du kan också fundera på att ta bort yttranden från **ApplyForJob** tills uttryck-kvantiteten är samma som **GetJobInformation**. Det kan åtgärda test resultatet, men det hindrar LUIS från att förutsäga den här metoden korrekt nästa gången. 

Korrigeringen är att lägga till fler yttranden till **GetJobInformation**. Kom ihåg att variera uttryck längd, ord val och ord ordning medan du fortfarande riktar in dig på att söka efter jobb information, som _inte_ gäller för jobbet.

### <a name="add-more-utterances"></a>Lägg till fler yttranden

1. Stäng fönstret batch-test genom att välja knappen **test** på den övre navigerings panelen. 

2. Välj **GetJobInformation** i listan med intenter. 

3. Lägg till fler yttranden som kan variera för längd, ord och ord, och se till att ta med villkoren `resume`, `c.v.`och `apply`:

    |Exempel på yttranden för **GetJobInformation** avsikt|
    |--|
    |Kräver det nya jobbet i lagret för en Stocker att jag använder med en merit förteckning?|
    |Var finns tak jobben idag?|
    |Jag hörde att det fanns ett läkar kod jobb som kräver en merit förteckning.|
    |Jag vill ha ett jobb som hjälper barn på gymnasium att skriva sina c.v.s. |
    |Här är min merit förteckning och letar efter ett nytt inlägg på communityn med hjälp av datorer.|
    |Vilka platser är tillgängliga i underordnad och Home Care?|
    |Finns det ett internt skriv bord i tidningen?|
    |Mina C.v. visar att jag är nöjd med att analysera inköp, budgetar och förlorade pengar. Finns det något för den här typen av arbete?|
    |Var finns gransknings jobben för Earth just nu?|
    |Jag har arbetat 8 år som en EMS-drivrutin. Eventuella nya jobb?|
    |Nya livsmedels hanterings jobb kräver program?|
    |Hur många nya varv arbets jobb är tillgängliga?|
    |Finns det ett nytt HR-inlägg för arbets relationer och förhandlingar?|
    |Jag har ett huvud i biblioteks-och Arkiv hantering. Alla nya positioner?|
    |Finns det några BabySitting jobb för 13 år Olds i staden idag?|

    Märk inte **jobb** enheten i yttranden. Det här avsnittet av själv studie Kursen fokuserar bara på avsikts förutsägelse.

4. Träna appen genom att välja **träna** i det övre högra navigerings fönstret.

## <a name="verify-the-new-model"></a>Verifiera den nya modellen

För att kontrol lera att yttranden i batch-testet är korrekt förutsägt kör du batch-testet igen.

1. Välj **test** i det övre navigerings fältet. Om batch-resultaten fortfarande är öppna väljer **du tillbaka till listan**.  

1. Välj knappen med tre punkter (***...***) till höger om batch-namnet och välj **Kör**. Vänta tills batch-testet är slutfört. Observera att knappen **Visa resultat** nu är grön. Det innebär att hela batchen har körts.

1. Välj **se resultat**. Avsikterna bör ha gröna ikoner till vänster om namnen på avsikten. 

## <a name="create-batch-file-with-entities"></a>Skapa kommando fil med entiteter 

För att verifiera entiteter i ett batch-test måste entiteterna märkas i batch-JSON-filen. 

Variationen av entiteter för det totala antalet ord ([token](luis-glossary.md#token)) kan påverka förutsägelse kvaliteten. Se till att de tränings data som ges till avsikten med märkta yttranden innehåller en mängd olika längd enheter. 

När du först skriver och testar kommandofiler, är det bäst att börja med några yttranden och entiteter som du vet fungerar, och så mycket som du tror kan vara felaktigt förutsägande. På så sätt kan du snabbt fokusera på problem områdena. När du har testat **GetJobInformation** och **ApplyForJob** med flera olika jobb namn, som inte har förutsägts, utvecklades den här batch-testfilen för att se om det finns ett förutsägelse problem med vissa värden för **jobb** entiteten. 

Värdet för en **Job** -entitet, som tillhandahålls i test-yttranden, är vanligt vis ett eller två ord, med några få exempel på fler ord. Om _din egen_ personal-app vanligt vis har jobb namn för många ord, fungerar inte yttranden som har etiketter ATS med **Job** -entiteten i den här appen bra.

1. Skapa `HumanResources-entities-batch.json` i en text redigerare, till exempel [VSCode](https://code.visualstudio.com/) eller [Ladda ned](https://github.com/Azure-Samples/cognitive-services-language-understanding/blob/master/documentation-samples/tutorials/HumanResources-entities-batch.json) den.

2. I den JSON-formaterade kommando filen lägger du till en matris med objekt som innehåller yttranden med den **avsikt** som du vill förutsäga i testet samt platser för alla entiteter i uttryck. Eftersom en entitet är tokenbaserad, se till att starta och stoppa varje entitet på ett tecken. Påbörja eller avsluta inte uttryck på ett blank steg. Detta orsakar ett fel vid import av kommando filen.  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorials/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>Kör batchen med entiteter

1. Välj **test** i det övre navigerings fältet. 

2. Välj **panelen för batch-testning** på den högra panelen. 

3. Välj **Importera data uppsättning**.

4. Välj fil system platsen för `HumanResources-entities-batch.json`s filen.

5. Namnge data uppsättningen `entities` och välj **färdig**.

6. Klicka på knappen **Kör**. Vänta tills testet är slutfört.

7. Välj **se resultat**.

## <a name="review-entity-batch-results"></a>Granska entitetens batch-resultat

Diagrammet öppnas med alla avsikter som förutsägs korrekt. Rulla nedåt i det högra filtret för att hitta enheten förutsägelser med fel. 

1. Välj **Job** -entiteten i filtret.

    ![Fel vid enhets förutsägelser i filter](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    Diagrammet ändras för att Visa enhets förutsägelserna. 

2. Välj **falskt negativ** i den nedre vänstra kvadranten i diagrammet. Använd sedan tangentkombinationen tangent bord och E för att växla till vyn token. 

    [vy över ![-token för enhets förutsägelser](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    Genom att granska yttranden nedanför diagrammet visas ett konsekvent fel när jobb namnet innehåller `SQL`. Att granska exemplet yttranden och listan med jobb fraser används bara en gång, och endast som en del av ett större jobbnamn `sql/oracle database administrator`.

## <a name="fix-the-app-based-on-entity-batch-results"></a>Åtgärda appen baserat på entitetens batch-resultat

Att åtgärda appen kräver LUIS för att korrekt fastställa varianten av SQL-jobb. Det finns flera alternativ för den korrigeringen. 

* Lägg uttryckligen till fler exempel yttranden, som använder SQL och etiketterar dessa ord som en jobb-entitet. 
* Lägg uttryckligen till fler SQL-jobb i fras listan

De här uppgifterna är kvar för dig.

Om du lägger till ett [mönster](luis-concept-patterns.md) innan entiteten är korrekt förväntas, kommer inte att åtgärda problemet. Detta beror på att mönstret inte matchar förrän alla entiteter i mönstret har identifierats. 

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>Nästa steg

Självstudien använde ett batch-test för att hitta problem med den aktuella modellen. Modellen har fasts och testats om med kommando filen för att kontrol lera att ändringen var korrekt.

> [!div class="nextstepaction"]
> [Lär dig mer om mönster](luis-tutorial-pattern.md)

