---
title: När du ska använda ENUM entiteter och ange entitetsåtgärder med en modell för konversationen Learner – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig när det är lämpligt att använda ENUM entiteter och ange entitetsåtgärder med en konversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nolachar
ms.openlocfilehash: ed18d30a0c3f5d51cb3a07b8948863cdda16c1ae
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67845954"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>När du ska använda ENUM entiteter och ange entitetsåtgärder

Den här självstudiekursen förklarar när du ska använda ENUM (uppräkning) entiteter och SET_ENTITY åtgärder.

## <a name="video"></a>Video

[![Ange entiteten självstudiekursen förhandsvisning](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Vad ingår

Den här självstudien introducerar två nya funktioner. En ny typ av enhet kallas ENUM (förkortning av uppräkning) och en ny typ av åtgärd som kallas SET_ENTITY, vilket kan referera till ett av följande enum-värden och som namnet antyder entiteten till det här värdet. När du får lära dig nedan, de här nya funktionerna används tillsammans och förklarar vi vad de är och hur du använder dem nedan. Det är viktigt att förstå vilka dessa funktioner att lösa problem innan vi går in informationen.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problem

Det finns fall i konversationer där enligt ord beror på kontexten.  Normalt taggade nyckelord lärs och extraheras med hjälp av en tjänst för språkförståelse, men i dessa fall dessa system kan inte kunna Läs med märkta exempel.

Anta att du overhear en del av en konversation mellan personer närliggande och du bara hör ordet ”Yes”. Du veta inte vad ”Yes” samtycker till eller bekräftar eftersom du fick inget svar på frågor och svar innan den. Frågor och svar innan du är det sammanhang där betydelse svaret. På samma sätt sedan ”Yes” är ett vanligt svar på många olika frågor som den inte kan hämtas av ger exempel på samma sätt som med [anpassade tränade](04-introduction-to-entities.md) entiteter eftersom sedan det skulle lär sig att märka varje ”Ja” som denna entitet.

### <a name="example"></a>Exempel

Nu ska vi du beskriva diagrammets med följande exempel:

Bot: Vill du ändra Azure Cognitive Services?
Användare: Ja Bot: Vill du ändra glass?
Användare: Ja

I tidigare självstudier vi har tittat på [anpassade tränade](04-introduction-to-entities.md) entiteter och dina första ansedda kan vara att skapa en entitet med namnet ”likesCogServices” och etikettera den första ”Ja” som den här entiteten.  Systemet etiketten skulle också andra ”Yes”. När vi försökte korrigera etiketten för andra ”Ja” för ”likesIceCream” vi skapar då en konflikt mellan två samma indata ”Yes” betydelse olika saker och skulle ha fastnat.

Det är i dessa fall måste du använda ENUM entiteter och SET_ENTITY åtgärder.

## <a name="when-to-use-enums-or-setentity-actions"></a>När du ska använda uppräkningar eller SET_ENTITY åtgärder

Använd de här reglerna nedan för att veta när du ska använda ENUM entiteter och SET_ENTITY åtgärder:

- Identifiering eller inställning för entiteten är kontextberoende
- Antalet möjliga värden är fast (Ja och inte skulle vara två värden)

Med andra ord, använda dem för eventuella Stäng avslutades uppmaningar som bekräftelse frågor som alltid resulterar i Ja eller Nej.

> [!NOTE]
> Vi har för närvarande en begränsning med upp till 5 värden per enum entitet. Varje värde använder en av platserna i den aktuella gränsen på 64. Se [cl-värden-och-gränser](../cl-values-and-boundaries.md)

Exempel: Bot: Stämmer din beställning?
Användare: Ja

När de möjliga värdena för entiteten är öppna och inte har åtgärdats, skulle du behöva använda en annan funktion som [förväntade entiteten](05-expected-entity.md).

Exempel: Bot: Vad heter du?
Användare: Matt Bot: Vad är din favorit färg?
Användare: Silver

De här anvisningarna anses kunskapsuppsättning eftersom de kan besvaras med valfria värden.

## <a name="what"></a>Vad

### <a name="enum-entities"></a>ENUM entiteter

ENUM entiteter skapas precis som andra entiteter. Liknar ”programmässiga” entiteter, det går inte att etiketter ord som dessa enheter. De måste i stället anges via kod eller SET_ENTITY åtgärder.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Ange entitetsåtgärder

Som nämnts ovan är ange ”ange” entitetsåtgärder att en entitet till ett känt enum-värde. Du kan uppnå samma resultat genom att skapa en motringning API-åtgärd och använda minneshanteraren att ställa in entiteten på ett värde. T.ex. `memory.Set(entityName, entityValue)`. Behöva skriva koden och skapa de här åtgärderna blir vara tidsödande och svårt att hantera – Konversationsdeltagare har särskilda åtgärder för att underlätta arbetet och automatiskt generera åtgärderna när de används. Med dessa som oberoende åtgärder bevarar möjligheten att skapa dessa utan att tillsammans med andra åtgärder eller kod i din robot.

- Ange entiteten åtgärder kan bara skapas när du refererar till ett värde för en entitet enum så att du måste först skapa en enum-entitet.
- Ange entitetsåtgärder är även ”icke-await” eftersom de har inga synliga utdata och måste följas av en ”väntar”-åtgärd som användaren kan se.
- Åtgärder för entiteten är oföränderliga vilket innebär att du inte kan redigera dem när du har skapat.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatisk åtgärd generation

Om det finns en enum-entitet i din modell, Konversationsdeltagare skapar platshållaren åtgärder för var och en av de möjliga värdena och göra dem tillgängliga för att välja under utbildning. När de har valts, skulle åtgärden skapas automatiskt åt dig.

Till exempel, om jag skapar en enum-entitet med värdena som ”Ja” och ”Nej”:

![enum_entity_order_confirmation.png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Även om du inte uttryckligen skapar åtgärder för den här nya enum ser du två nya åtgärder vid träning:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Skapa en robot som använder dessa nya funktioner

### <a name="requirements"></a>Krav

Den här självstudien krävs att Allmänt självstudiekursen bot körs

    npm run tutorial-general

Vi ska skapa en robot för att simulera fast mat ordning. Den innehåller diskreta värden för storleken på drycker och fries (små/MEDIUM/LARGE) och bekräftelse frågor med Ja / inga svar. Båda de här entiteterna uppfyller två reglerna ovan för att kontextberoende svar och fasta värden.

### <a name="create-the-model"></a>Skapa modellen

1. Klicka på ”Import” i webbgränssnittet
2. Välj självstudien med namnet ”självstudien-Enum-Set-entitet”

Detta kommer du till sidan för hantering av modellen.
Observera att modellen innehåller redan några enum-entiteter och ange entitetsåtgärder.

### <a name="create-the-first-dialog"></a>Den första dialogrutan Skapa

1. På den vänstra navigeringspanelen ”Train-dialogrutor” Klicka på knappen ”Ny träna dialogruta”.
2. Som användartyp i vill ”Hej, jag beställa en koks och fries”.
3. Klicka på knappen ”poäng åtgärder”

   > Användaren har inte angetts storlekar för dryck eller fries så vi måste fråga.

4. Välj åtgärden med svar: ”Vilken storlek dryck vill du”?
5. Som användare Skriv in ”stora”
6. Klicka på knappen ”poäng åtgärder”
7. Välj åtgärden SET_ENTITY - ”drinkSize: STORA ”
8. Välj åtgärden med svar: ”Hur stort fries vill du”?
9. Som användningstyp i ”Um, gör de ett medium.
10. Klicka på knappen ”poäng åtgärder”
11. Välj åtgärden SET_ENTITY - ”friesSize: MEDEL ”
12. Välj åtgärden med svar: ”Vill du alla smaktillsatser”?
13. Som användningstyp i ”Ja”
14. Klicka på knappen ”poäng åtgärder”
15. Välj åtgärden SET_ENTITY - ”condimentsConfirmation: JA ”
16. Välj åtgärden med svar: ”Ok, jag har en order för en stor dryck och MEDELHÖG fries. Stämmer det ”?
17. Som användningstyp i ”Ja”
18. Klicka på knappen ”poäng åtgärder”
19. Välj åtgärden SET_ENTITY - ”orderConfirmation: JA ”
20. Välj åtgärden med svar: ”Din ordningsnummer är ok, 58. Vänta där ”.
21. Klicka på ”Spara” för att stänga dialogrutan

Du har just skapat din första dialogrutan med ENUM-entiteter och SET_ENTITY åtgärder. Du kan göra många fler kombinationer av användaren att ange ofullständig information eller experimentera med andra typer av Stäng avslutades frågor.

> [!NOTE]
> Vid träning visas platshållare för åtgärderna som SET_ENTITY som
>
> ![action_set_entity_training.png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> men när skapar loggar eller om du vill använda distribuerade robotar användarna ser inte dessa.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Alternativa indata](./10-alternative-inputs.md)
