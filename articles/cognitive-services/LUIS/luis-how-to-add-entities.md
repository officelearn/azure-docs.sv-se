---
title: Lägg till entiteter i LUIS-appar
titleSuffix: Azure Cognitive Services
description: Lägg till entiteter (viktiga data i ditt programs domän) i appar för Språkförståelse (LUIS).
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 3fe76afca2eb8b14641589e4e29fc20b5d3de7fa
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/15/2018
ms.locfileid: "45632297"
---
# <a name="manage-entities"></a>Hantera entiteter
När du har identifierat appens [avsikter](luis-concept-intent.md), måste du [märka exempel yttranden](luis-concept-utterance.md) med [entiteter](luis-concept-entity-types.md). Entiteter är viktiga delar av ett kommando eller en fråga och kan vara viktiga för din klientapp att utföra sitt uppdrag. 

Du kan lägga till, redigera eller ta bort entiteter i din app genom den **entitetslistan** på den **entiteter** sidan. LUIS erbjuder två typer av entiteter: [förskapade entiteter](luis-reference-prebuilt-entities.md), och dina egna anpassade entiteter.

I följande avsnitt är bara tillgängliga i en LUIS-app från den **skapa** avsnittet. Den **skapa** länken är i det övre navigeringsfältet. En gång inuti den **skapa** väljer **entiteter** från den vänstra navigeringsmenyn. När en entitet läggs till programmet, om entiteten är datorn lärt dig, kan du [märka entiteten](luis-how-to-add-example-utterances.md) inuti uttryck. När appen har tränats och publiceras, du kan ta emot entitetsdata [extraheras](luis-concept-data-extraction.md) från förutsägelser. 

## <a name="add-prebuilt-entity"></a>Lägg till fördefinierade entitet
Färdiga entiteter har definierats i den [identifierare fulltext](https://github.com/Microsoft/Recognizers-Text) projekt. Vanliga förskapade entiteter som lagts till i ett program är *nummer* och *datetimeV2*. 

1. I din app från den **skapa** avsnittet och klicka sedan på **entiteter** på den vänstra panelen.
 
2. På den **entiteter** väljer **hantera förskapade entiteter**.

3. I **Lägg till eller ta bort fördefinierade entiteter** dialogrutan den **nummer** och **datetimeV2** förskapade entiteter. Välj sedan **Done** (Klar).

    ![Skärmbild av Lägg till fördefinierade entitet dialogrutan](./media/add-entities/list-of-prebuilt-entities.png)

    Se [Dataextrahering](luis-concept-data-extraction.md#prebuilt-entity-data) mer information om hur du extraherar den fördefinierade entiteten från slutpunkten JSON frågesvaret.

## <a name="add-simple-entities"></a>Lägg till enkel entiteter
En enkel enhet är en allmän entitet som beskriver ett enda koncept. 

1. I din app från den **skapa** avsnittet och klicka sedan på **entiteter** i den vänstra panelen och välj sedan **Skapa ny entitet**.

2. I popup-rutan, skriver `Airline` i den **entitetsnamn** väljer **enkel** från den **entitetstypen** och sedan välja **klar**.

    ![Skärmbild av dialogrutan för att skapa flygbolag enkel enhet](./media/add-entities/create-simple-airline-entity.png)

    Se [Dataextrahering](luis-concept-data-extraction.md#simple-entity-data) mer information om hur du extraherar den enkla enheten från slutpunkten JSON frågesvaret. Testa enkel enhet [snabbstarten](luis-quickstart-primary-and-secondary-data.md) mer information om hur du använder en enkel enhet.

## <a name="add-regular-expression-entities"></a>Lägg till entiteter för reguljärt uttryck
Ett reguljärt uttryck entiteten används för att hämta data från uttryck baserat på ett reguljärt uttryck som du anger. 

1. I din app, väljer **entiteter** vänstra navigeringsfönstret och välj sedan **Skapa ny entitet**.

2. I popup-rutan, ange `AirFrance Flight` i den **entitetsnamn** väljer **reguljärt uttryck** från den **entitetstypen** listan, ange det reguljära uttrycket `AFR[0-9]{3,4}`, och välj sedan **klar**. 

    Den här AirFrance flygning reguljärt uttryck förväntar sig tre tecken bokstavligen `AFR`, sedan 3 eller 4 siffror. Siffrorna kan vara ett tal mellan 0 och 9. Det reguljära uttrycket som matchar AirFrance linjenummer: ”AFR101”, ”ARF1302” och ”AFR5006”. Se [Dataextrahering](luis-concept-data-extraction.md) mer information om hur du extraherar entiteten från slutpunkten JSON frågesvaret.

    ![Bild av dialogrutan Skapa reguljärt uttryck entitet](./media/add-entities/regex-entity-create-dialog.png)

    Se [Dataextrahering](luis-concept-data-extraction.md#regular-expression-entity-data) vill veta mer om hur du extraherar reguljärt uttryck entiteten från slutpunkten JSON frågesvaret. Försök entiteten reguljärt uttryck [snabbstarten](luis-quickstart-intents-regex-entity.md) mer information om hur du använder en entitet för reguljärt uttryck.

## <a name="add-hierarchical-entities"></a>Lägg till hierarkiska entiteter
En hierarkisk entitet är en viss kategori av sammanhangsmässigt inlärda och begreppsmässigt relaterade entiteter. I följande exempel innehåller entiteten ursprung- och målplatserna. 

I uttryck `Book 2 tickets from Seattle to Cairo`, Seattle är ursprungsplatsen och Kairo är målplatsen. Varje plats är sammanhangsmässigt annan, inlärda från ordföljden och word valet i uttryck.

Om du vill lägga till hierarkiska entiteter, gör du följande: 

1. I din app, väljer **entiteter** vänstra navigeringsfönstret och välj sedan **Skapa ny entitet**.

2. I popup-rutan, skriver `Location` i den **entitetsnamn** och väljer sedan **hierarkiska** från den **entitetstypen** lista.

    ![Lägg till hierarkisk entitet](./media/add-entities/hier-location-entity-creation.png)

3. Välj **Lägg till underordnad**, och Skriv ”ursprung” i **underordnade nr 1** box. 

4. Välj **Lägg till underordnad**, och Skriv ”mål” i **underordnade nr 2** box. Välj **Done** (Klar).
5. 
    >[!NOTE]
    >Välj Papperskorgen bredvid den för att ta bort en underordnad.

    >[!CAUTION]
    >Namn på underordnade entiteter måste vara unikt inom alla entiteter i samma app. Två olika hierarkiska entiteter får inte innehålla underordnade entiteter med samma namn. 

    Se [Dataextrahering](luis-concept-data-extraction.md#hierarchical-entity-data) mer information om hur du extraherar den hierarkiska entiteten från slutpunkten JSON frågesvaret. Försök hierarkisk entiteten [snabbstarten](luis-quickstart-intent-and-hier-entity.md) mer information om hur du använder en hierarkisk entitet.

## <a name="add-composite-entities"></a>Lägg till sammansatta entiteter
Du kan definiera relationer mellan flera befintliga entiteter genom att skapa en sammansatt entitet. I följande exempel innehåller entiteten antal biljetter, ursprung och målplatser. 

I uttryck `Book 2 tickets from Seattle to Cairo`, hur många 2 kan matchas med en fördefinierade entitet, Seattle är ursprungsplatsen och Kairo är målplatsen. Varje entitet är en del av en större, överordnade entiteten efter sammansatta entiteten har skapats.

1. Lägg till fördefinierade entiteten i din app, **nummer**. Anvisningar finns i [Lägg till fördefinierade entiteter](#add-prebuilt-entity). 

2. Lägg till entiteten hierarkisk `Location`, inklusive undertyper: `origin`, `destination`. Mer information finns i [Lägg till hierarkiska entiteter](#add-hierarchical-entities). 

3. Välj **entiteter** vänstra navigeringsfönstret och välj sedan **Skapa ny entitet**.

4. I popup-rutan, skriver `TicketsOrder` i den **entitetsnamn** och väljer sedan **sammansatta** från den **entitetstypen** lista.

5. Välj **Lägg till underordnad** att lägga till en ny underordnad.

6. I **underordnade nr 1**, väljer du entitet **nummer** i listan.

7. I **underordnade nr 2**, väljer du entitet **Location::Origin** i listan. 

8. I **underordnade 3**, väljer du entitet **Location::Destination** i listan. 

9. Välj **Done** (Klar).

    ![Bild av dialogrutan för att skapa en sammansatt entitet](./media/add-entities/ticketsorder-composite-entity.png)

    >[!NOTE]
    >Klicka på Papperskorgen bredvid den för att ta bort en underordnad.

    Se [Dataextrahering](luis-concept-data-extraction.md#composite-entity-data) mer information om hur du extraherar den sammansatta entiteten från slutpunkten JSON frågesvaret. Försök sammansatta entiteten [självstudien](luis-tutorial-composite-entity.md) mer information om hur du använder en sammansatt entitet.


## <a name="add-patternany-entities"></a>Lägg till Pattern.any entiteter
[Pattern.ANY](luis-concept-entity-types.md) entiteter är endast giltiga i [mönster](luis-how-to-model-intent-pattern.md). Den här entiteten kan LUIS hitta slutet av entiteter av olika längd och word val. Eftersom den här entiteten används i ett mönster, vet LUIS där slutet av entiteten finns i uttryck.

Om en app har en `FindBookInfo` avsikt, rubrik av boken kan störa avsikt förutsägelser. Använda en Pattern.any inom ett mönster för att förtydliga vilka ord är i rubriken för boken. LUIS-förutsägelse börjar med uttryck. Först uttryck kontrolleras och matchade för entiteter, när entiteterna hittas, och sedan mönstret kontrolleras och matchas. 

I uttryck `Who wrote the book Ask and when was it published?`, bok rubriken, Ask är svårt att eftersom den inte är sammanhangsmässigt uppenbara där titeln slutar och där resten av uttryck börjar. Boktitlar kan vara valfri ordning av ord, inklusive ett enstaka ord komplexa fraser med skiljetecken och meningslöst sorteringen av orden. Ett mönster kan du skapa en entitet där fullständiga och exakta entiteten kan extraheras. När boken rubriken har hittats, den `FindBookInfo` avsikt förväntas eftersom det är avsikten för mönstret.

1. I din app från den **skapa** avsnittet och klicka sedan på **entiteter** i den vänstra panelen och välj sedan **Skapa ny entitet**.

2. I den **Lägg till entitet** skriver `BookTitle` i den **entitetsnamn** rutan och välj **Pattern.any** som den **entitetstypen**.
 
    ![Skärmbild av en pattern.any entitet skapas](./media/add-entities/create-pattern-any-entity.png)

    Om du vill använda entiteten pattern.any, lägga till ett mönster på den **mönster** sida (under den **förbättra apprestanda** avsnitt) med rätt klammerparentesen syntax, som ”för **{BookTitle}** som är författaren ”?.

    Se [Dataextrahering](luis-concept-data-extraction.md#patternany-entity-data) mer information om hur du extraherar Pattern.any entiteten från slutpunkten JSON frågesvaret. Prova den [mönstret](luis-tutorial-pattern.md) självstudiekurs och lär dig mer om hur du använder en Pattern.any entitet.

Om du tycker att din mönstret när det innehåller en Pattern.any, extraherar entiteterna felaktigt, används en [explicit lista](luis-concept-patterns.md#explicit-lists) att lösa problemet. 

## <a name="add-role-to-pattern-based-entity"></a>Lägga till rollen i mönstret-baserade entitet
En roll är en namngiven undertyp till en entitet baserat på kontext. Det kan jämföras med en [hierarkiska](#add-hierarchical-entities) entiteten men roller som endast används i [mönster](luis-how-to-model-intent-pattern.md). 

En plan-biljett har exempelvis en *ursprung Stad* och en *mål Stad*, men båda är städer. LUIS anger att båda är städer och kan fastställa original och beskrivning städer baserat på kontexten för ordföljden och word valet. 

Syntaxen för en roll är **{name: rollen entitetsnamn}** där enhetens namn följt av ett kolon och sedan namnet på rollen. Till exempel ”boken en biljett från {plats: ursprunget} till {plats: mål}”.

1. I din app från den **skapa** avsnittet och välj sedan **entiteter** på den vänstra panelen.

2. Välj **Create new entity** (Skapa ny entitet). Ange namnet på `Location`. Välj typ **enkel** och välj **klar**

3. Välj **entiteter** i vänster panel, välj sedan den nya entiteten **plats** skapade i steg 2.

4. I den **rollnamn** textrutan anger du namnet på rollen `Origin` och ange. Lägg till ett andra rollnamn för `Destination`. Exempelvis kan en flygresa har ett ursprung och en mål-stad. De två rollerna är ”ursprung” och ”mål”.

    ![Skärmbild av Administrationscenter ursprung rollen till platsen entitet](./media/add-entities/roles-enter-role-name-text.png)

    Se [Dataextrahering](luis-concept-data-extraction.md) mer information om hur du extraherar roller från slutpunkten JSON frågesvaret. Prova guiden mönster för mer information om hur du använder en Pattern.any entitet.

## <a name="add-list-entities"></a>Lägg till listan över entiteter
Lista över entiteter representerar en fast, stängda uppsättning närstående ord i systemet. 

Du kan ha två normaliserade värden för en lista över entitet drycker: vatten och soda pop. Varje normaliserade namn har synonymer. För vatten är synonymer H20, gas, fast. Synonymer är frukt, cola, ingefära för soda pop. Du behöver veta alla värden när du har skapat entiteten. Du kan lägga till fler när du har granskat hur användarna yttranden med synonymer.

|Normaliserade namn|Synonymer|
|--|--|
|Vatten|H20 gas, fast|
|Soda pop|Frukt, cola, ingefära|

1. I din app från den **skapa** avsnittet och klicka sedan på **entiteter** i den vänstra panelen och välj sedan **Skapa ny entitet**.

2. I den **Lägg till entitet** skriver `Drinks` i den **entitetsnamn** rutan och välj **lista** som den **entitetstypen**. Välj **Done** (Klar).
 
    ![Bild av dialogrutan Skapa drycker lista entitet](./media/add-entities/menu-list-dialog.png)
  
3.  Sidan med listan över entitet kan du lägga till normaliserade namn. I den **värden** textrutan anger du ett objekt för en lista, t.ex `Water` för de typer av drycker lista och tryck på RETUR på tangentbordet. 

    ![Skärmbild av drycker lista entitet med vatten normaliserade värde i textrutan](./media/add-entities/entity-list-normalized-name.png)

4. Till höger om normaliserade värdet **water**, ange synonymer `h20`, `flat`, och `gas`, tryck på RETUR på tangentbordet efter varje objekt.

    ![Skärmbild av drycker lista entitet med synonymen objekt för vatten markerat](./media/add-entities/menu-list-synonyms.png)

5. Om du vill mer normaliserade objekt lista väljer **rekommenderar** att se alternativ från den [semantiska ordlista](luis-glossary.md#semantic-dictionary).

    ![Skärmbild av dricka lista entitet med rekommenderade objekt markerat](./media/add-entities/entity-list-recommended-list.png)

6. Markera ett objekt i listan med rekommenderade att lägga till det som ett normaliserat värde eller välja **Lägg till alla** att lägga till alla objekt. 

    ![Skärmbild av dricka listan entitet med rekommenderade objekt i öl och lägga till alla knappen markerad](./media/add-entities/list-entity-add-suggestions.png)

    Se [Dataextrahering](luis-concept-data-extraction.md#list-entity-data) mer information om extrahering av listan över entiteter från slutpunkten JSON frågesvaret. Prova den [snabbstarten](luis-quickstart-intent-and-list-entity.md) mer information om hur du använder en entitet i listan.

## <a name="import-list-entity-values"></a>Importera entitet listvärden
Du kan importera värden till en befintlig entitet i listan.

 1. På sidan med listan över entiteten väljer **importera listor**.

 2. I **Importera nya poster** dialogrutan **Välj fil** och välj den JSON-fil som innehåller listan.

    ![Skärmbild av Import lista popup-dialogrutan för entiteten värden](./media/add-entities/menu-list-import-json-dialog-with-file.png)

    >[!NOTE]
    >LUIS importerar filer med det tillägget ”.json” endast.

 3. Mer information om vilken lista som stöds i JSON, Välj **Lär dig mer om listan stöds syntax** att expandera dialogrutan och visa ett exempel på tillåtna syntax. Om du vill minimera dialogrutan och dölja syntax, väljer du Länkrubrik igen.

 4. Välj **Done** (Klar).

    Ett exempel på giltiga json för en **färger** lista entiteten visas i följande JSON-formaterade kod:

    ```
    [
        {
            "canonicalForm": "Blue",
            "list": [
                "navy",
                "royal",
                "baby"
            ]
        },
        {
            "canonicalForm": "Green",
            "list": [
                "kelly",
                "forest",
                "avacado"
            ]
        }
    ]  
    ```

## <a name="edit-entity-name"></a>Redigera entitetsnamn
1. På den **entiteter** , sidan Välj enheten i listan. Den här åtgärden tar dig till den **entitet** sidan.

2. På den **entitet** kan du redigera enhetens namn genom att välja redigeringsikonen intill entitetens namn. Typ av målentitet kan inte redigeras. 

## <a name="delete-entity"></a>Ta bort entitet

På den **entitet** väljer den **ta bort entiteten** knappen. Välj **Ok** i bekräftelsemeddelandet att bekräfta borttagningen.
 
![Skärmbild av plats entitet sida med knappen Ta bort entiteten är markerad](./media/add-entities/entity-delete.png)

>[!NOTE]
>* Tar bort en hierarkisk entiteten tar bort alla underordnade entiteter.
>* Tar bort en sammansatt entiteten tar bort endast sammansatt och delar upp sammansatta relationen, men tar inte bort de enheter som ingår i den.

## <a name="changing-entity-type"></a>Ändra entitetstyp
LUIS kan du inte ändra typ av entiteten eftersom den inte vet vad du ska lägga till eller ta bort för att skapa entiteten. För att ändra typen, är det bättre att skapa en ny entitet av rätt typ. med ett något annorlunda namn. När enheten har skapats i varje uttryck att ta bort gamla taggade entitetsnamnet och lägga till det nya namnet. När alla yttranden har varit relabeled, kan du ta bort den gamla entiteten. 

## <a name="create-a-pattern-from-an-utterance"></a>Skapa ett mönster från ett uttryck
Se [Lägg till mönster från befintliga uttryck på avsikten eller entitet](luis-how-to-model-intent-pattern.md#add-pattern-from-existing-utterance-on-intent-or-entity-page).

## <a name="search-utterances"></a>Sök yttranden
Du kan söka och filtrera yttranden med på förstoringsglaset i verktygsfältet. 

## <a name="train-your-app-after-changing-model-with-entities"></a>Träna din app när du har ändrat modell med entiteter
När du lägger till, redigera eller ta bort entiteter, [träna](luis-how-to-train.md) och [publicera](luis-how-to-publish-app.md) din app för ändringarna att påverka endpoint frågor. 

## <a name="next-steps"></a>Nästa steg
Nu när du har lagt till avsikter, yttranden och entiteter, har du en grundläggande LUIS-app. Lär dig hur du [träna](luis-how-to-train.md), [testa](luis-interactive-test.md), och [publicera](luis-how-to-publish-app.md) din app.
 
