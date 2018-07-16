---
title: Använd batch testning för att förbättra LUIS förutsägelser | Microsoft Docs
titleSuffix: Azure
description: Läsa in batch test, granska resultaten och förbättra LUIS förutsägelser med ändringar.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/06/2018
ms.author: v-geberr
ms.openlocfilehash: b695783c6d68876d39482ed5abec24f45087603d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/14/2018
ms.locfileid: "39054870"
---
# <a name="improve-app-with-batch-test"></a>Förbättra app med batch-test

Den här självstudien visar hur du använder batch testning för att hitta uttryck förutsägelse problem.  

I den här guiden får du lära dig att:

<!-- green checkmark -->
> [!div class="checklist"]
* Skapa en batchfil för testning 
* Köra ett batch-test
* Granskningsresultat
* Åtgärda fel för avsikter
* Testa om batch

För den här artikeln behöver du ett kostnadsfritt [LUIS-konto](luis-reference-regions.md#luis-website) för att kunna redigera LUIS-programmet.

## <a name="before-you-begin"></a>Innan du börjar
Om du inte har personalapp från den [granska endpoint yttranden](luis-tutorial-review-endpoint-utterances.md) självstudien [importera](luis-how-to-start-new-app.md#import-new-app) JSON-koden i en ny app i den [LUIS](luis-reference-regions.md#luis-website) webbplats. Importeringsappen finns på [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json)-GitHub-lagringsplatsen.

Om du vill behålla den ursprungliga Human Resources-appen (Personalfrågor) klonar du versionen på sidan [Settings](luis-how-to-manage-versions.md#clone-a-version) (Inställningar) och ger den namnet `batchtest`. Kloning är ett bra sätt att prova på olika LUIS-funktioner utan att påverka originalversionen. 

## <a name="purpose-of-batch-testing"></a>Syftet med batch-testning
Batch-testning kan du kontrollera tillstånd för en modell med en känd uppsättning av test yttranden och märkt med entiteter. I JSON-formaterade kommandofilen, Lägg till talade och ange etiketter för entiteten som du behöver förutse inuti uttryck. 

Rekommenderade test-strategin för LUIS använder tre separata uppsättningar av data: exempel yttranden till modellen, batch test yttranden och slutpunkten yttranden. Kontrollera att du inte använder yttranden från antingen exempel yttranden (läggs till ett intent) eller slutpunkten yttranden i den här självstudien. 

Kontrollera din batch test yttranden mot exempel yttranden och slutpunkten yttranden [exportera](luis-how-to-start-new-app.md#export-app) appen och [hämta](luis-how-to-start-new-app.md#export-endpoint-logs) frågeloggen. Jämför app exempel uttryck och fråga log yttranden till batch test yttranden. 

Krav för att testa batch:

* 1000 yttranden per test. 
* Inga dubbletter. 
* Entitetstyper tillåts: enkel och sammansatta.

## <a name="create-a-batch-file-with-utterances"></a>Skapa en batchfil med yttranden
1. Skapa `HumanResources-jobs-batch.json` i en textredigerare som [VSCode](https://code.visualstudio.com/). 

2. Lägg till yttranden med i JSON-formaterade kommandofilen, den **avsikt** du vill att förväntade i testet. 

    ```JSON
    [
        {
        "text": "Are there any janitorial jobs currently open?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "I would like a fullstack typescript programming with azure job",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Is there a database position open in Los Colinas?",
        "intent": "GetJobInformation",
        "entities": []
        },
        {
        "text": "Can I apply for any database jobs with this resume?",
        "intent": "GetJobInformation",
        "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>Kör om gruppen

1. Välj **Test** i det övre navigeringsfältet. 

    [ ![Skärmbild av LUIS-app med Test som markerats i övre, högra navigeringsfältet](./media/luis-tutorial-batch-testing/hr-first-image.png)](./media/luis-tutorial-batch-testing/hr-first-image.png#lightbox)

2. Välj **Batch-testning panelen** i den högra panelen. 

    [ ![Skärmbild av LUIS-app med Batch test panelen markerat](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. Välj **importera datauppsättningen**.

    [ ![Skärmbild av LUIS-app med importera datauppsättningen markerat](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. Välj system sökvägen till den `HumanResources-jobs-batch.json` filen.

5. Namnge datauppsättningen `intents only` och välj **klar**.

    ![Välj fil](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. Klicka på knappen **Kör**. Vänta tills testet är klart.

    [ ![Skärmbild av LUIS-app med markerat som körs](./media/luis-tutorial-batch-testing/hr-run-button.png)](./media/luis-tutorial-batch-testing/hr-run-button.png#lightbox)

7. Välj **se resultat**.

8. Granska resultatet i diagram och legend.

    [ ![Skärmbild av LUIS-app med testresultaten för batch](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>Granska resultatet från batch
Batch-diagrammet visar fyra quadrants resultat. Är ett filter till höger om diagrammet. Filtret är som standard första avsikten i listan. Filtret innehåller alla avsikter och endast enkla, hierarkisk (överordnade endast), och sammansatta entiteter. När du väljer en del av diagrammet eller en punkt i diagrammet visas de associerade utterance(s) under diagrammet. 

Vid hovring över diagrammet, ett mushjul förstora eller minska visas i diagrammet. Detta är användbart när det finns många saker i diagrammet klustrade nära tillsammans. 

Diagrammet är i fyra quadrants med två av de avsnitt som visas i rött. **Dessa finns i avsnitt fokuserar på**. 

### <a name="applyforjob-test-results"></a>ApplyForJob testresultat
Den **ApplyForJob** test visas i filtret visar att 1 av fyra förutsägelser lyckades. Välj namnet **falsklarm** över övre högra quadrant att se yttranden under diagrammet. 

![LUIS batch test yttranden](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

Tre talade hade en främsta syftet med **ApplyForJob**. Avsikten som anges i batchfilen hade en lägre poäng. Varför hände detta? Två avsikter relaterade mycket nära när det gäller word val och word placering. Det finns dessutom nästan tre gånger så många exempel för **ApplyForJob** än **GetJobInformation**. Den här ojämnheter av exempel yttranden väger **ApplyForJob** avsikts fördel. 

Lägg märke till att båda avsikter har samma antal fel: 

![LUIS batch test filterfel](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

Det uttryck som motsvarar den övre punkten i den **falsklarm** avsnittet är `Can I apply for any database jobs with this resume?`. Ordet `resume` har endast används i **ApplyForJob**. 

De andra två punkterna i diagrammet hade mycket lägre poäng för fel avsikt, vilket innebär att de är närmare till rätt avsikt. 

## <a name="fix-the-app-based-on-batch-results"></a>Åtgärda appen baserat på batch-resultat
Målet med det här avsnittet är att ha tre yttranden som var felaktigt förväntas **ApplyForJob** till att korrekt förutse för **GetJobInformation**, när appen har lösts. 

En till synes snabb korrigering är att lägga till dessa batch filen yttranden till rätt avsikt. Det är inte vad du vill göra om. Vill du LUIS för att förutsäga dessa yttranden utan att lägga till dem som exempel. 

Du kanske också undrar om att ta bort yttranden från **ApplyForJob** tills antalet uttryck är samma som **GetJobInformation**. Som kan åtgärdas testresultaten men skulle hindra LUIS från att förutsäga detta syfte korrekt nästa gång. 

Första korrigeringen är att lägga till fler yttranden till **GetJobInformation**. Andra korrigeringen är att minska vikten för ordet `resume` mot den **ApplyForJob** avsikt. 

### <a name="add-more-utterances-to-getjobinformation"></a>Lägg till mer yttranden till **GetJobInformation**
1. Stänga panelen batch test genom att välja den **testa** knappen i övre navigeringsfönstret. 

    [ ![Skärmbild av LUIS med testknappen markerat](./media/luis-tutorial-batch-testing/hr-close-test-panel.png)](./media/luis-tutorial-batch-testing/hr-close-test-panel.png#lightbox)

2. Välj **GetJobInformation** från listan över avsikter. 

    [ ![Skärmbild av LUIS med testknappen markerat](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png)](./media/luis-tutorial-batch-testing/hr-select-intent-to-fix-1.png#lightbox)

3. Lägg till mer yttranden som varieras för längd, word val och word placering, se till att inkludera villkoren `resume` och `c.v.`:

    ```JSON
    Is there a new job in the warehouse for a stocker?
    Where are the roofing jobs today?
    I heard there was a medical coding job that requires a resume.
    I would like a job helping college kids write their c.v.s. 
    Here is my resume, looking for a new post at the community college using computers.
    What positions are available in child and home care?
    Is there an intern desk at the newspaper?
    My C.v. shows I'm good at analyzing procurement, budgets, and lost money. Is there anything for this type of work?
    Where are the earth drilling jobs right now?
    I've worked 8 years as an EMS driver. Any new jobs?
    New food handling jobs?
    How many new yard work jobs are available?
    Is there a new HR post for labor relations and negotiations?
    I have a masters in library and archive management. Any new positions?
    Are there any babysitting jobs for 13 year olds in the city today?
    ```

4. Träna appen genom att välja **träna** i det övre högra navigeringsfältet.

## <a name="verify-the-fix-worked"></a>Verifiera korrigeringen fungerade
För att verifiera att yttranden i batch-testet är korrekt förutse, kör du batch-testet igen.

1. Välj **Test** i det övre navigeringsfältet. Om batch-resultat är fortfarande öppen väljer **tillbaka till listan**.  

2. Välj ellipsen (***...*** ) till höger om batch-namn och välj **kör datauppsättning**. Vänta tills det batch-testet är klart. Observera att den **se resultat** är nu grön. Det innebär att hela batchen har körts.

3. Välj **se resultat**. Avsikter ska ha grön ikonerna till vänster om avsiktlig namnen. 

    [ ![Skärmbild av LUIS med batch resulterar knappen markerad](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png#lightbox)


## <a name="what-has-this-tutorial-accomplished"></a>Vad har den här självstudien göra detta?
Den här appen förutsägelsefunktionen har ökat genom att söka efter fel i batchen och åtgärda modellen genom att lägga till fler exempel yttranden rätt avsikten och utbildning. 

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort LUIS-appen när den inte längre behövs. Välja **My apps** (Mina appar) på menyn längst upp till vänster. Välj ellipsen **...**  till höger om appnamnet i programlistan, Välj **ta bort**. På popup-dialogrutan **Delete app?** (Ta bort appen?) väljer du **Ok**.


## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om mönster](luis-tutorial-pattern.md)

