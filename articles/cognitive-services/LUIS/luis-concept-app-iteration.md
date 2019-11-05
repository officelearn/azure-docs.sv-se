---
title: Design av iterativa appar – LUIS
titleSuffix: Azure Cognitive Services
description: LUIS lär sig bäst i en iterativ cykel med modell ändringar, uttryck-exempel, publicering och insamling av data från slut punkts frågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 12a1f2304e4255eb9abd04ab2e2d0726066dd1e6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73487776"
---
# <a name="authoring-cycles-and-versions"></a>Redigering av cykler och versioner

LUIS-appen lär sig bäst med en iterativ cykel av:

* Skapa ny version
* redigera app-schema
    * avsikter med exempel yttranden
    * poster
    * funktioner
* utbilda
* test
* publish
    * testa vid förutsägelse slut punkt för aktiv inlärning
* samla in data från slut punkts frågor

![Redigeringscykel](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Skapa ett LUIS-schema

Appens schema syfte är att definiera vad användaren efterfrågar (avsikten eller avsikten) och vilka delar av frågan som innehåller information (entiteter) som hjälper dig att fastställa svaret. 

Appens schema måste vara särskilt för app-domänerna för att kunna fastställa ord och fraser som är relevanta och vanliga ord ordning. 

Exempel yttranden representerar användarindata som appen förväntas få vid körning. 

Schemat kräver avsikter och _ska ha_ entiteter. 

### <a name="example-schema-of-intents"></a>Exempel på schema för avsikter

Det vanligaste schemat är ett avsikts schema som är ordnat med avsikter. Den här typen av schema är beroende av LUIS för att fastställa en användares avsikt. 

Den här schema typen kan ha entiteter om den hjälper LUIS att avgöra avsikten. Till exempel hjälper en frakten het (som en beskrivning till en avsikt) LUIS att fastställa en leverans. 

### <a name="example-schema-of-entities"></a>Exempel schema för entiteter

Ett entitets schema fokuserar på entiteterna, som är de data som ska extraheras från yttranden. 

Avsikten med uttryck är mindre eller inte viktigt för klient programmet. 

En vanlig metod för att organisera ett enhets schema är att lägga till alla exempel yttranden i ingen avsikt. 

### <a name="example-of-a-mixed-schema"></a>Exempel på ett blandat schema

Det mest kraftfulla och mogna schemat är ett bevarande schema med en fullständig uppsättning entiteter och funktioner. Det här schemat kan inledas antingen som ett avsikts-eller enhets schema och växer för att inkludera koncepten för båda, eftersom klient programmet behöver dessa delar av information. 

## <a name="add-example-utterances-to-intents"></a>Lägg till exempel yttranden till avsikter

LUIS behöver några exempel yttranden i varje **avsikt**. Exemplet yttranden kräver tillräckligt många Variations alternativ och ord ordning för att kunna avgöra vilken avsikt som uttryck är avsedd för. 

> [!CAUTION]
> Lägg inte till exempel yttranden i bulk. Börja med 15 till 30 olika och varierande exempel. 

Varje uttryck måste ha alla **data som krävs för att extrahera** utformade och märkta med **entiteter**. 

|Nyckel element|Syfte|
|--|--|
|Avsikt|**Klassificera** användar yttranden i en enda avsikt eller åtgärd. Exempel på detta är `BookFlight` och `GetWeather`.|
|Entitet|**Extrahera** data från uttryck som krävs för att slutföra avsikten. Exempel omfattar datum och tid för resor och plats.|

Du utformar din LUIS-app för att ignorera yttranden som inte är relevanta för appens domän genom att tilldela uttryck till **ingen** avsikt. 

## <a name="test-and-train-your-app"></a>Testa och träna din app

När du har 15 till 30 olika exempel yttranden i varje avsikt, med de obligatoriska enheterna märkta, måste du testa och [träna](luis-how-to-train.md). 

## <a name="publish-to-a-prediction-endpoint"></a>Publicera till en förutsägelse slut punkt

Se till att publicera din app så att den är tillgänglig i de [förutsägelse slut regioner](luis-reference-regions.md) du behöver. 

## <a name="test-your-published-app"></a>Testa ditt publicerade program

Du kan testa din publicerade LUIS-app från HTTPS förutsägelse-slutpunkten. Om du testar från förutsägelse slut punkten kan LUIS välja valfri yttranden med låg exakthet för [granskning](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Skapa en ny version för varje cykel

Versioner, i LUIS, liknar versioner i traditionell programmering. Varje version är en ögonblicks bild i appens tid. Innan du gör ändringar i appen skapar du en ny version. Det är enklare att gå tillbaka till en äldre version och sedan försöka ta bort intentor och yttranden till ett tidigare tillstånd.

Versions-ID: t består av tecken, siffror eller "." och får inte vara längre än 10 tecken.

Den första versionen (0,1) är den aktiva standard versionen. 

### <a name="begin-by-cloning-an-existing-version"></a>Börja med att klona en befintlig version

Klona en befintlig version som ska användas som utgångs punkt för den nya versionen. När du klonar en version blir den nya versionen den **aktiva** versionen. 

### <a name="publishing-slots"></a>Publicerings platser
Du publicerar på antingen stadie-och produktions platserna. Varje plats kan ha en annan version eller samma version. Detta är användbart för att verifiera ändringar innan publicering till produktion, som är tillgängligt för robotar eller andra LUIS-anrops program. 

Utbildade versioner är inte automatiskt tillgängliga i appens [slut punkt](luis-glossary.md#endpoint). Du måste [publicera](luis-how-to-publish-app.md) eller publicera om en version för att den ska vara tillgänglig på din app-slutpunkt. Du kan publicera till **mellanlagring** och **produktion**, vilket ger dig två versioner av appen tillgängliga vid slut punkten. Om du behöver fler versioner av appen tillgänglig vid en slut punkt bör du exportera versionen och importera den till en ny app. Den nya appen har ett annat app-ID.

### <a name="import-and-export-a-version"></a>Importera och exportera en version
Du kan importera en version på App-nivå. Den versionen blir den aktiva versionen och använder versions-ID: t i egenskapen `versionId` för app-filen. Du kan också importera till en befintlig app på versions nivå. Den nya versionen blir den aktiva versionen. 

Du kan exportera en version på appen eller versions nivån. Den enda skillnaden är att den version som exporter ATS på program nivå är den för närvarande aktiva versionen på versions nivå. du kan välja vilken version du vill exportera på sidan **[Inställningar](luis-how-to-manage-versions.md)** . 

Den exporterade filen innehåller inte:

* information som sparats av datorn eftersom appen omtränas när den har importer ATS
* deltagar information

För att kunna säkerhetskopiera LUIS-appen exporterar du en version från LUIS-portalen.

## <a name="manage-contributor-changes-with-versions-and-apps"></a>Hantera deltagar ändringar med versioner och appar

LUIS tillhandahåller begreppet deltagare i en app genom att tillhandahålla Azures behörigheter på resurs nivå. Kombinera det här konceptet med versions hantering för att ge riktat samarbete. 

Använd följande tekniker för att hantera deltagar ändringar i din app.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Hantera flera versioner i samma app
Börja med [kloning](luis-how-to-manage-versions.md#clone-a-version), från en bas version, för varje författare. 

Varje författare gör ändringar i sin egen version av appen. När varje författare är nöjd med modellen exporterar du de nya versionerna till JSON-filer.  

Exporterade appar,. JSON-eller. lu-filer kan jämföras med ändringar. Kombinera filerna för att skapa en enda fil i den nya versionen. Ändra egenskapen **versionId** för att ange den nya sammanfogade versionen. Importera den versionen till den ursprungliga appen. 

Med den här metoden kan du ha en aktiv version, en fas version och en publicerad version. Du kan jämföra resultatet av den aktiva versionen med en publicerad version (fas eller produktion) i fönstret för [interaktiv testning](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Hantera flera versioner som appar
[Exportera](luis-how-to-manage-versions.md#export-version) bas versionen. Varje författare importerar versionen. Den person som importerar appen är ägaren till versionen. När du är färdig med att ändra appen exporterar du versionen. 

Exporterade appar är JSON-formaterade filer som kan jämföras med bas exporten för ändringar. Kombinera filerna för att skapa en enda JSON-fil för den nya versionen. Ändra egenskapen **versionId** i JSON-filen för att ange den nya sammanfogade versionen. Importera den versionen till den ursprungliga appen.

Läs mer om hur du redigerar bidrag från [medarbetare](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-authoring-cycle"></a>Granska slut punkts yttranden för att påbörja den nya redigerings cykeln

När du är färdig med en cykel av redigering kan du börja om från början. Börja med att [Granska förutsägelse slut punkt yttranden](luis-how-to-review-endpoint-utterances.md) Luis markerad med låg exakthet. Kontrol lera dessa yttranden för både korrekt förväntad avsikt och korrekt och fullständig extraherad enhet. När du har granskat och accepterat ändringar bör gransknings listan vara tom.  

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om [samarbete](luis-concept-keys.md).
