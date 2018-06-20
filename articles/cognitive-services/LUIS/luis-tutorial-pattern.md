---
title: Självstudier med mönster för att förbättra THOMAS förutsägelser - Azure | Microsoft Docs
titleSuffix: Azure
description: I den här kursen Använd mönster för avsikter för att förbättra THOMAS avsikt och entitet förutsägelser.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr;
ms.openlocfilehash: ff5572366be548132b28e5ce03b9595e7f98128c
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265324"
---
# <a name="tutorial-use-patterns-to-improve-predictions"></a>Självstudier: Använda mönster för att förbättra förutsägelser

I den här självstudiekursen att använda mönster för att öka avsikt och entitet förutsägelse.  

> [!div class="checklist"]
* Så här identifierar du att ett mönster hjälper din app
* Så här skapar du ett mönster 
* Använda fördefinierade och anpassade entiteter i ett mönster 
* Så här kontrollerar du mönstret förutsägelse förbättringar
* Hur du lägger till en roll till en entitet att hitta sammanhang-baserade enheter
* Hur du lägger till en Pattern.any för att hitta Friform entiteter

För den här artikeln behöver du ett kostnadsfritt [THOMAS] [ LUIS] konto för att kunna redigera THOMAS programmet.

## <a name="import-humanresources-app"></a>Importera personal app
Den här självstudiekursen importerar en personal-app. Appen har tre avsikter: None, GetEmployeeOrgChart, GetEmployeeBenefits. Appen har två entiteter: färdiga antal och medarbetare. Medarbetare entiteten är en enkel enhet att extrahera en anställds namn. 

1. Skapa en ny THOMAS appfil och ger den namnet `HumanResources.json`. 

2. Kopiera följande app definition till filen:

   [!code-json[Add the LUIS model](~/samples-luis/documentation-samples/tutorial-patterns/HumanResources.json?range=1-164 "Add the LUIS model")]

3. På THOMAS **appar** väljer **Importera nya app**. 

4. I den **Importera nya app** markerar den `HumanResources.json` fil som du skapade i steg 1.

5. Välj den **GetEmployeeOrgChart** avsikt och sedan ändra från **entiteter visa** till **Tokens visa**. Flera exempel utterances visas. Varje utterance innehåller ett namn som är en medarbetare entitet. Observera att namnen är olika och att placering av ordval är olika för varje utterance. Dessa skilda hjälper THOMAS lär du dig en mängd olika utterances.

    ![Skärmbild av avsikt sida med entiteter vy växlas](media/luis-tutorial-pattern/utterances-token-view.png)

6. Välj **träna** i det övre navigeringsfältet för att träna appen. Vänta tills fältet grön lyckades.

7. Välj **Test** i den övre panelen. Ange `Who does Patti Owens report to?` och sedan anger du väljer. Välj **granska** under utterance vill se mer information om testet.
    
    Namnet på medarbetare, Patti Owens har inte använts i en exempel-utterance ännu. Detta är ett test för att se hur väl THOMAS lärt dig den här utterance avser den `GetEmployeeOrgChart` avsikt och medarbetare entiteten ska vara `Patti Owens`. Resultatet bör vara mindre än 50% (. 50) för den `GetEmployeeOrgChart` avsikt. Avsikten är korrekt är poängsättningen låg. Den anställde också korrekt identifieras som `Patti Owens`. Mönster öka det här första förutsägelse resultatet. 

    ![Skärmbild av Test-panelen](media/luis-tutorial-pattern/original-test.png)

8. Stäng test-panelen genom att välja den **testa** knappen i det övre navigeringsfältet. 

## <a name="patterns-teach-luis-common-utterances-with-fewer-examples"></a>Mönster utbilda THOMAS vanliga utterances med färre exempel
På grund av uppbyggnad personal domänen finns det några vanliga sätt att be om medarbetaren relationer i organisationer. Exempel:

```
Who does Mike Jones report to?
Who reports to Mike Jones? 
```

Dessa utterances är för Stäng för att fastställa kontextuella är unikt för varje utan att ange många utterance exempel. Lär sig vanliga utterance mönster för syftet utan att ange många utterance exempel genom att lägga till ett mönster för syftet, THOMAS. 

Exempel mallen utterances till detta avsiktshantering är:

```
Who does {Employee} report to?
Who reports to {Employee}? 
```

Mönstret är en kombination av matchning med reguljära uttryck och maskininlärning. Därefter innehåller vissa mallen utterance exempel för THOMAS Läs mönstret. De här exemplen, tillsammans med avsiktshantering utterances ger THOMAS bättre förståelse för vilka utterances passar avsikten och där, inom utterance entiteten finns. <!--A pattern is specific to an intent. You can't duplicate the same pattern on another intent. That would confuse LUIS, which lowers the prediction score. -->

## <a name="add-the-template-utterances"></a>Lägg till mall utterances

1. I det vänstra navigeringsfönstret under **förbättra prestanda för**väljer **mönster** från det vänstra navigeringsfönstret.

2. Välj den **GetEmployeeOrgChart** avsikt, ange följande utterances från mall, en i taget, välja RETUR efter varje mall utterance:

    ```
    Does {Employee} have {number} subordinates?
    Does {Employee} have {number} direct reports?
    Who does {Employee} report to?
    Who reports to {Employee}?
    Who is {Employee}'s manager?
    Who are {Employee}'s subordinates?
    ```

    Den `{Employee}` syntax markerar entitet plats i mallen utterance som samt vilken entitet som den är. 

    ![Skärmbild av hur du anger mallen utterances för avsikt](./media/luis-tutorial-pattern/enter-pattern.png)

3. Välj **Train** i det övre navigeringsfältet. Vänta tills fältet grön lyckades.

4. Välj **Test** i den övre panelen. Ange `Who does Patti Owens report to?` i textrutan. Välj ange. Det här är samma utterance testas i föregående avsnitt. Resultatet bör vara högre för den `GetEmployeeOrgChart` avsikt. 

    Poängen är nu mycket bättre. THOMAS lärt dig mönstret som är relevanta för avsikten utan att ange många exempel.

    ![Skärmbild av Test-panel med högsta poäng leda](./media/luis-tutorial-pattern/high-score.png)

    Entiteten finns först och sedan mönstret påträffas, som anger syftet. Du måste lägga till fler exempel utterances på avsikten (inte mönstret) om du har ett testresultat där enheten identifieras inte och därför mönstret hittas inte. 

5. Stäng test-panelen genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="use-an-entity-with-a-role-in-a-pattern"></a>Använda en enhet med en roll i ett mönster
THOMAS appen används för att flytta anställda från en plats till en annan. Ett exempel utterance är `Move Bob Jones from Seattle to Los Colinas`. Varje plats i utterance har en annan betydelse. Seattle är den ursprungliga platsen och Los Colinas är målplatsen för flytten. För att kunna skilja mellan dessa platser i mönstret i följande avsnitt du skapar en enkel enhet för plats med två roller: ursprung och destination. 

### <a name="create-a-new-intent-for-moving-people-and-assets"></a>Skapa en ny avsett för att flytta personer och tillgångar
Skapa en ny avsett för alla utterances som rör personer eller tillgångar.

1. Välj **Intents** från vänstra navigeringsfönstret
2. Välj **skapa nya avsikt**
3. Namn på ny avsikten `MoveAssetsOrPeople`
4. Lägg till exempel utterances:

    ```
    Move Bob Jones from Seattle to Los Colinas
    Move Dave Cooper from Redmond to Seattle
    Move Jim Smith from Toronto to Vancouver
    Move Jill Benson from Boston to London
    Move Travis Hinton from Portland to Orlando
    ```
    ![Skärmbild av exempel utterance för MoveAssetsOrPeople avsikt](./media/luis-tutorial-pattern/intent-moveasserts-example-utt.png)

    Syftet med exempel utterances är att ge tillräckligt med exempel. Om du senare i det här testet plats entiteten är inte identifierats och därför mönstret inte identifierats, gå tillbaka till det här steget och Lägg till fler exempel. Sedan träna och testa igen. 

5. Markera entiteter i exempel utterances med entiteten medarbetare genom att välja förnamn sedan efternamnet i en utterance och därefter välja medarbetare entiteten i listan.

    ![Skärmbild av utterances i MoveAssetsOrPeople som markerats med medarbetare entitet](./media/luis-tutorial-pattern/intent-moveasserts-employee.png)

6. Markera texten `portland` i utterance `move travis hinton from portland to orlando`. Ange det nya namnet i popup-fönstret `Location`, och välj **Skapa ny entitet**. Välj den **enkel** entitetstypen och välj **klar**.

    ![Skärmbild för att skapa en ny plats entitet](./media/luis-tutorial-pattern/create-new-location-entity.png)

    Markera resten av platsnamn i utterances. 

    ![Skärmbild av alla enheter som har markerats](./media/luis-tutorial-pattern/moveasset-all-entities-labeled.png)

    Mönstret för word val och ordning är uppenbara i föregående bild. Om du **inte** med mönster och utterances på avsikten har ett uppenbart mönster som är en bra indikation på bör du använda mönster. 

    Om du förväntar dig en mängd olika utterances i stället för ett mönster skulle dessa vara fel exempel utterances. I så fall vill du ofta varierande utterances i termen eller word val, utterance längd och placering för entiteten. 

<!--TBD: what guidance to move from hier entities to patterns with roles -->
<!--    The [Hierarchical entity quickstart](luis-quickstart-intent-and-hier-entity.md) uses the  same idea of location but uses child entities to find origin and destination locations. 
-->
### <a name="add-role-to-location-entity"></a>Lägg till plats entitet 
Roller kan bara användas för mönster. Lägg till roller och mål i plats-entiteten. 

1. Välj **entiteter** i navigeringsfältet till vänster, sedan **plats** från listan över enheter.

2. Lägg till `Origin` och `Destination` roller till entiteten.

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/location-entity.png)

    Rollerna som inte har markerats på sidan MoveAssetsOrPeople avsiktshantering eftersom roller som inte finns på avsiktshantering utterances. De finns bara på mönstret mallen utterances. 

### <a name="add-template-utterances-that-uses-location-and-destination-roles"></a>Lägg till mall utterances som använder plats-och mål
Lägg till mall utterances som använder den nya entiteten.

1. Välj **mönster** från det vänstra navigeringsfönstret.

2. Välj den **MoveAssetsOrPeople** avsikt.

3. Ange en ny mall utterance med hjälp av den nya entiteten `Move {Employee} from {Location:Origin} to {Location:Destination}`. Syntax för en enhet och en roll i en mall utterance `{entity:role}`.

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/pattern-moveassets.png)

4. Träna app för nya avsikt, entitet och mönster.

### <a name="test-the-new-pattern-for-role-data-extraction"></a>Testa det nya mönstret för rollen data parameterextrahering
Validera det nya mönstret med ett test.

1. Välj **Test** i den övre panelen. 
2. Ange utterance `Move Tammi Carlson from Bellingham to Winthrop`.
3. Välj **granska** under resultat för att se testresultaten för entiteten och avsikt.

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/test-with-roles.png)

    Entiteterna finns först och sedan mönstret påträffas, som anger syftet. Du måste lägga till fler exempel utterances på avsikten (inte mönstret) om du har ett testresultat där enheterna identifieras inte och därför mönstret hittas inte. 

4. Stäng test-panelen genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="use-a-patternany-entity-to-find-free-form-entities-in-a-pattern"></a>Använda en Pattern.any entitet för att hitta Friform entiteter i ett mönster
Den här appen personal hjälper också att anställda hitta företagets formulär. Många av formulär som har som varierande längd. Olika längd innehåller fraser som kan förvirrande THOMAS om där formulärets namn slutar. Med hjälp av en **Pattern.any** entiteten i ett mönster kan du ange början och slutet på formuläret så THOMAS korrekt extraherar formulärets namn. 

### <a name="create-a-new-intent-for-the-form"></a>Skapa en ny avsett för formuläret
Skapa en ny avsett för utterances som söker efter formulär.

1. Välj **Intents** från vänstra navigeringsfönstret.

2. Välj **skapa nya avsikt**.

3. Namnge den nya avsikten `FindForm`.

4. Lägg till en exempel-utterance.

    ```
    `Where is the form What to do when a fire breaks out in the Lab and who needs to sign it after I read it?`
    ```

    ![Skärmbild av ny entitet med roller](./media/luis-tutorial-pattern/intent-findform.png)

    Formuläret rubriken är `What to do when a fire breaks out in the Lab`. Utterance frågar efter platsen för formuläret och även frågar som måste signera den verifierar medarbetaren läsa den. Utan en Pattern.any enhet, skulle det vara svårt att förstå var rubriken slutar och extrahera rubriken som en entitet av utterance.

### <a name="create-a-patternany-entity-for-the-form-title"></a>Skapa en entitet Pattern.any för formulärrubrik
Entiteten Pattern.any tillåter för entiteter med olika längd. Den fungerar bara i ett mönster eftersom mönstret markerar början och slutet av entiteten. Om du tycker att din mönster, om den innehåller en Pattern.any, extrakt entiteterna felaktigt, används en [explicit lista](luis-concept-patterns.md#explicit-lists) att lösa problemet. 

1. Välj **entiteter** i det vänstra navigeringsfönstret.

2. Välj **Skapa ny entitet**. 

3. Namnet på entiteten `FormName` med typen **Pattern.any**. För den här specifika självstudiekursen behöver inte lägga till några roller i entiteten.

    ![Bild av dialogrutan för enhetsnamn och typ av enhet](./media/luis-tutorial-pattern/create-entity-pattern-any.png)

### <a name="add-a-pattern-that-uses-the-patternany"></a>Lägga till ett mönster som använder Pattern.any

1. Välj **mönster** från det vänstra navigeringsfönstret.

2. Välj den **FindForm** avsikt.

3. Ange en mall utterance ny entitet `Where is the form {FormName} and who needs to sign it after I read it?`

    ![Skärmbild av mallen utterance pattern.any entitet](./media/luis-tutorial-pattern/pattern.any-template-utterance.png)

4. Träna app för nya avsikt, entitet och mönster.

### <a name="test-the-new-pattern-for-free-form-data-extraction"></a>Testa det nya mönstret för extrahering av data i fritt format
1. Välj **testa** från att öppna panelen test översta raden. 

2. Ange utterance `Where is the form Understand your responsibilities as a member of the community and who needs to sign it after I read it?`.

3. Välj **granska** under resultat för att se testresultaten för entiteten och avsikt.

    ![Skärmbild av mallen utterance pattern.any entitet](./media/luis-tutorial-pattern/test-pattern.any-results.png)

    Entiteten finns först och sedan mönstret påträffas, som anger syftet. Du måste lägga till fler exempel utterances på avsikten (inte mönstret) om du har ett testresultat där enheterna identifieras inte och därför mönstret hittas inte.

4. Stäng test-panelen genom att välja den **testa** knappen i det övre navigeringsfältet.

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort THOMAS appen när de inte längre behövs. Om du vill göra det, väljer du menyn med tre punkter (...) till höger om appnamnet i applistan väljer **ta bort**. I popup-fönstret **ta bort appen?** väljer **Ok**.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Använd frasen listan för att förbättra förutsägelserna](luis-tutorial-interchangeable-phrase-list.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions