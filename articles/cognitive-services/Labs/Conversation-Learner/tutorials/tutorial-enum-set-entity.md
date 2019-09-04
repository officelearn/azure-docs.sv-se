---
title: När du ska använda ENUM-entiteter och ange ENHETs åtgärder med en Conversation Learner modell – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Lär dig när det är lämpligt att använda ENUM-entiteter och ange ENHETs åtgärder med en Conversation Learner modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 429570d81f7e15758d8ea60951bb4d01b96f8f2c
ms.sourcegitcommit: 267a9f62af9795698e1958a038feb7ff79e77909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2019
ms.locfileid: "70256491"
---
# <a name="when-to-use-enum-entities-and-set-entity-actions"></a>När du ska använda ENUM-entiteter och ange ENHETs åtgärder

Den här självstudien förklarar när du bör använda uppräknings enheter och SET_ENTITY-åtgärder.

## <a name="video"></a>Video

[![För hands version av guiden ange entitet](https://aka.ms/cl_Tutorial_v3_SetEntity_Preview)](https://aka.ms/cl_Tutorial_v3_SetEntity)



## <a name="what-is-covered"></a>Vad som omfattas

I den här självstudien kommer vi att lansera två nya funktioner. En ny typ av entitet som kallas ENUM (kort för uppräkning) och en ny typ av åtgärd som kallas SET_ENTITY, som kan referera till något av dessa uppräknings värden och som namnet antyder anger entiteten till det här värdet. Som du lär dig nedan används de här nya funktionerna tillsammans och vi kommer att förklara vad de är och hur de används nedan. Innan vi får information, är det viktigt att förstå vilka problem dessa funktioner kan lösa.

![enum_entity_type.png](../media/tutorial-enum-set-entity/enum_entity_type.png)
![action_set_entity_type.png](../media/tutorial-enum-set-entity/action_set_entity_type.png)

## <a name="problem"></a>Problem

Det finns fall i konversationer där innebörden av ord är beroende av sammanhanget.  Normalt är de märkta nyckelorden inlärt och extraherade med hjälp av en tjänst för språk förståelse, men i dessa fall kanske dessa system inte kan lära sig använda märkta exempel.

Föreställ dig att du overhear en del av en konversation mellan personer i närheten och att du bara hör ordet "Ja". Du vet inte vad "Ja" accepterar eller bekräftar eftersom du inte hörde att du fick frågan. Frågan som frågas före är kontexten, vilket ger svaret. På samma sätt som "Ja" är ett sådant gemensamt svar på många olika frågor som det inte går att läsa genom att tillhandahålla exempel som du skulle göra med [anpassade, utbildade](04-introduction-to-entities.md) entiteter, eftersom det skulle lära sig att märka var "Ja" som entiteten.

### <a name="example"></a>Exempel

Nu ska vi klargöra följande exempel:

Bot Vill du använda Azure Cognitive Services?
Användarvänlig Ja-robot: Gillar du Ice-grädde?
Användarvänlig Ja

I tidigare självstudier tittar vi på [anpassade intränade](04-introduction-to-entities.md) entiteter och din första tanke kan vara att skapa en entitet med namnet "likesCogServices" och etiketten första "Ja" som den här entiteten.  Systemet skulle dock också märka det andra "Ja". När vi försökte korrigera etiketten för det andra "Ja" till "likesIceCream" skulle vi då skapa en konflikt mellan två samma indata, "Ja", vilket innebär olika saker och skulle fastna.

I dessa fall måste du använda ENUM-entiteter och SET_ENTITY-åtgärder.

## <a name="when-to-use-enums-or-set_entity-actions"></a>När du ska använda ENUMs eller SET_ENTITY-åtgärder

Använd följande regler för att veta när du ska använda ENUM-entiteter och SET_ENTITY-åtgärder:

- Identifiering eller inställning av entitet är Sammanhangs beroende
- Antalet möjliga värden är fast (Ja och nej skulle vara två värden)

Med andra ord använder du dessa för stängnings bara frågor, till exempel bekräftelse frågor som alltid resulterar i Ja eller nej.

> [!NOTE]
> Vi har för närvarande en begränsning på upp till 5 värden per uppräknings enhet. Varje värde använder en av platserna i den aktuella 64-gränsen. Se [cl-Values-och-gränser](../cl-values-and-boundaries.md)

Exempel: Bot Är din beställning korrekt?
Användarvänlig Ja

När de möjliga värdena för entiteten är öppna-avslutade och inte åtgärdade, skulle du behöva använda en alternativ funktion, till exempel [förväntad entitet](05-expected-entity.md).

Exempel: Bot Vad heter du?
Användarvänlig Matt bot: Vad är din favorit färg?
Användarvänlig Silver

Dessa frågor betraktas som öppna-avslutade eftersom de kan besvaras med valfria värden.

## <a name="what"></a>Vad

### <a name="enum-entities"></a>RÄKNA upp entiteter

ENUM-entiteter skapas precis som andra entiteter. Precis som "programmering" entiteter kan du inte märka ord som dessa entiteter. De måste i stället anges via kod-eller SET_ENTITY-åtgärder.

![enum_entity_creation.png](../media/tutorial-enum-set-entity/enum_entity_creation.png)

### <a name="set-entity-actions"></a>Ange enhets åtgärder

Som nämnts ovan anger åtgärder för "Ange entitet" bara en entitet till ett känt uppräknings värde. Du kan få samma resultat genom att skapa en åtgärd för API-motringning och använda minnes hanteraren för att ange ett värde för entiteten. T.ex. `memory.Set(entityName, entityValue)`. Att skriva den här koden och skapa de här åtgärderna blir omständlig och svår att hantera – så Conversation Learner har särskilda åtgärder för att under lätta detta arbete och automatiskt generera dessa åtgärder när de används. Med dessa som oberoende åtgärder bevaras möjligheten att skapa dessa utan att kombineras med andra åtgärder eller kod i din robot.

- Det går bara att skapa enhets åtgärder när du refererar till ett värde för en Enum-entitet, så du måste först skapa en Enum-entitet.
- Ange enhets åtgärder är också "icke-väntande" eftersom de inte har några synliga utdata och måste följas av en "vänta"-åtgärd som användaren kan se.
- Ange att enhets åtgärder är oföränderliga innebär att du inte kan redigera dem när du har skapat.

![action_set_entity_creation.png](../media/tutorial-enum-set-entity/action_set_entity_creation.png)

### <a name="automatic-action-generation"></a>Automatisk generering av åtgärder

Om en uppräknings enhet finns i din modell skapar Conversation Learner placeholder-åtgärder för var och en av de möjliga värdena och gör dem tillgängliga för val under träning. Vid val av val skapas åtgärden automatiskt åt dig.

Om du till exempel skapar en uppräknings enhet med värdena "Ja" och "nej":

![enum_entity_order_confirmation. png](../media/tutorial-enum-set-entity/enum_entity_order_confirmation.png)

Även om du inte uttryckligen skapar åtgärder för den här nya uppräkningen visas två nya åtgärder som är tillgängliga under utbildningen:

![action_set_entity_sample.png](../media/tutorial-enum-set-entity/action_set_entity_sample.png)


## <a name="create-a-bot-using-these-new-features"></a>Skapa en robot med de här nya funktionerna

### <a name="requirements"></a>Krav

Den här självstudien kräver att roboten för allmän självstudie körs

    npm run tutorial-general

Vi ska skapa en robot för att simulera snabb livsmedels sortering. Det har diskreta värden för storlekar på drycker och Fries (små/medel stora) och bekräftelse frågor med ja/nej-svar. Båda dessa entiteter uppfyller de två reglerna ovan för att vara Sammanhangs beroende svar och fasta värden.

### <a name="create-the-model"></a>Skapa modellen

1. I webb gränssnittet klickar du på "Importera"
2. Välj självstudien med namnet "självstudie-Enum-set-entity"

Då går du till sidan modell hantering.
Observera att modellen redan innehåller några uppräknings enheter och ange enhets åtgärder.

### <a name="create-the-first-dialog"></a>Skapa den första dialog rutan

1. På den vänstra navigerings panelen klickar du på "träna dialoger" och klickar sedan på knappen "ny träna" dialog ".
2. Som användar typ i, "Hej, jag vill beställa en koks och Fries".
3. Klicka på knappen "Poäng åtgärder"

   > Användaren har inte angett storlekar för drycken eller Fries så vi måste fråga dem.

4. Välj åtgärden med Response: "Vilken storleks dryck vill du ha?"
5. Som användar typ i, "stor"
6. Klicka på knappen "Poäng åtgärder"
7. Välj åtgärden SET_ENTITY-"drinkSize: FÖRSTORING
8. Välj åtgärden med Response: "Vilken storlek Fries vill du ha?"
9. Som användnings typ i, "UM, gör du ett medium.
10. Klicka på knappen "Poäng åtgärder"
11. Välj åtgärden SET_ENTITY-"friesSize: SÄKER
12. Välj åtgärden med Response: "Vill du ha med alla condiments?"
13. Som användnings typ i, "Ja"
14. Klicka på knappen "Poäng åtgärder"
15. Välj åtgärden SET_ENTITY-"condimentsConfirmation: JA
16. Välj åtgärden med Response: "OK, jag har en order för en stor dryck och medelhög Fries. Är det rätt? "
17. Som användnings typ i, "Ja"
18. Klicka på knappen "Poäng åtgärder"
19. Välj åtgärden SET_ENTITY-"orderConfirmation: JA
20. Välj åtgärden med Response: "OK, ditt beställnings nummer är 58. Vänta en stund. "
21. Klicka på "Spara" för att stänga dialog rutan

Du har precis skapat din första dialog med uppräknings enheter och SET_ENTITY-åtgärder. Du kan göra många fler kombinationer av användaren och ange ofullständig information eller experimentera med andra typer av frågor som är nära avslutad.

> [!NOTE]
> Under utbildningen visas plats hållare för SET_ENTITY-åtgärder som
>
> ![action_set_entity_training. png](../media/tutorial-enum-set-entity/action_set_entity_training.png)
>
> men när du skapar logg dialog rutor eller använder distribuerade robotar-användare visas inte dessa.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Alternativa indata](./10-alternative-inputs.md)
