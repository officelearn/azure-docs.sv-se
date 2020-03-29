---
title: Iterativ app design - LUIS
titleSuffix: Azure Cognitive Services
description: LUIS lär sig bäst i en iterativ cykel av modelländringar, uttrycksexempel, publicera och samla in data från slutpunktsfrågor.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: diberry
ms.openlocfilehash: c1c1b2df301634a435b610c395a1a58aa5573da3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "74422600"
---
# <a name="iterative-app-design-for-luis"></a>Iterativ appdesign för LUIS

En LUIS-app (Language Understanding) lär sig och presterar mest effektivt med iteration. Här är en typisk iteration cykel:

* Skapa ny version
* Redigera LUIS-appschemat. Det här omfattar:
    * Avsikter med exempelyttranden
    * Entiteter
    * Funktioner
* Träna, testa och publicera
    * Testa vid förutsägelseslutpunkten för aktiv inlärning
* Samla in data från slutpunktsfrågor

![Redigeringscykel](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Skapa ett LUIS-schema

En apps schema definierar vad användaren ber om _(avsikt_ eller _avsikt)_ och vilka delar av avsikten som innehåller information (kallas _entiteter)_ som används för att avgöra svaret. 

Appschemat måste vara specifikt för appdomänerna för att bestämma ord och fraser som är relevanta, samt för att bestämma typisk ordordning. 

Exempelyttranden representerar användarindata, till exempel tolkat tal eller text, som appen förväntar sig vid körning. 

Schemat kräver avsikter och _bör ha_ entiteter. 

### <a name="example-schema-of-intents"></a>Exempel schema av avsikter

Det vanligaste schemat är ett avsiktsschema som organiseras med avsikter. Den här typen av schema använder LUIS för att avgöra en användares avsikt. 

Avsiktsschematypen kan ha entiteter om det hjälper LUIS att avgöra användarens avsikt. En leveransentitet (som en deskriptor till en avsikt) hjälper till exempel LUIS att avgöra en leveransavsikt. 

### <a name="example-schema-of-entities"></a>Exempel schema för entiteter

Ett entitetsschema fokuserar på entiteter, vilket är de data som extraheras från användaryttranden. Om en användare till exempel skulle säga :"Jag vill beställa tre pizzor". Det finns två enheter som skulle utvinnas: _tre_ och _pizzor_. Dessa används för att uppfylla avsikten, som var att göra en order. 

För ett entitetsschema är avsikten med uttryck mindre viktig för klientprogrammet. 

En vanlig metod för att organisera ett entitetsschema är att lägga till alla exempelyttranden i **avsikten Ingen.** 

### <a name="example-of-a-mixed-schema"></a>Exempel på ett blandat schema

Det mest kraftfulla och mogna schemat är ett avsiktsschema med ett komplett utbud av entiteter och funktioner. Det här schemat kan börja som antingen en avsikt eller entitet schema och växa till att omfatta begrepp för båda, eftersom klientprogrammet behöver dessa bitar av information. 

## <a name="add-example-utterances-to-intents"></a>Lägga till exempelyttranden i avsikter

LUIS behöver några exempel yttranden i varje **avsikt**. Exempelyttrandena behöver tillräckligt med variation av ordval och ordföljd för att kunna avgöra vilken avsikt yttrandet är avsett för. 

> [!CAUTION]
> Lägg inte till exempelyttranden i grupp. Börja med 15 till 30 specifika och varierande exempel. 

Varje exempel yttrande måste ha alla **nödvändiga data för att extrahera** utformade och märkta med **entiteter**. 

|Nyckelelement|Syfte|
|--|--|
|Avsikt|**Klassificera** användaryttranden i en enda avsikt eller åtgärd. Exempel `BookFlight` är `GetWeather`och .|
|Entitet|**Extrahera** data från yttrande krävs för att slutföra avsikt. Exempel är datum och tid för resor och plats.|

En LUIS-app kan utformas för att ignorera yttranden som inte är relevanta för en apps domän genom att tilldela uttryck till **ingen** avsikt.

## <a name="test-and-train-your-app"></a>Testa och träna din app

När du har 15 till 30 olika exempelyttranden i varje avsikt, med de nödvändiga entiteterna märkta, måste du testa och [träna](luis-how-to-train.md) luis-appen. 

## <a name="publish-to-a-prediction-endpoint"></a>Publicera till en förutsägelseslutpunkt

LUIS-appen måste publiceras så att den är tillgänglig för dig i [slutpunktsområdena för listförutsägelse](luis-reference-regions.md).

## <a name="test-your-published-app"></a>Testa ditt publicerade program

Du kan testa din publicerade LUIS-app från slutpunkten för HTTPS-förutsägelse. Testning från förutsägelseslutpunkten gör det möjligt för LUIS att välja alla yttranden med lågt förtroende för [granskning](luis-how-to-review-endpoint-utterances.md).  

## <a name="create-a-new-version-for-each-cycle"></a>Skapa en ny version för varje cykel

Varje version är en ögonblicksbild i tid för LUIS-appen. Skapa en ny version innan du gör ändringar i appen. Det är lättare att gå tillbaka till en äldre version än att försöka ta bort avsikter och yttranden till ett tidigare tillstånd.

Versions-ID:t består av tecken, siffror eller '.' och får inte vara längre än 10 tecken.

Den ursprungliga versionen (0.1) är den aktiva standardversionen. 

### <a name="begin-by-cloning-an-existing-version"></a>Börja med att klona en befintlig version

Klona en befintlig version som ska användas som utgångspunkt för varje ny version. När du har klonat en version blir den nya versionen den **aktiva** versionen. 

### <a name="publishing-slots"></a>Publicering av ankomst- och avgångstider

Du kan publicera till antingen scenen och/eller produktionsplatserna. Varje plats kan ha en annan version eller samma version. Detta är användbart för att verifiera ändringar innan du publicerar till produktion, som är tillgängligt för robotar eller andra LUIS-anropsappar. 

Tränade versioner är inte automatiskt tillgängliga på LUIS-appens [slutpunkt](luis-glossary.md#endpoint). Du måste [publicera](luis-how-to-publish-app.md) eller publicera om en version för att den ska vara tillgänglig på luis-appens slutpunkt. Du kan publicera till **Mellanlagring** och **produktion**, vilket ger dig två versioner av appen som är tillgängliga vid slutpunkten. Om fler versioner av appen behöver vara tillgängliga vid en slutpunkt bör du exportera versionen och importera den igen till en ny app. Den nya appen har ett annat app-ID.

### <a name="import-and-export-a-version"></a>Importera och exportera en version

En version kan importeras på appnivå. Den versionen blir den aktiva versionen och `versionId` använder versions-ID i egenskapen för appfilen. Du kan också importera till en befintlig app på versionsnivå. Den nya versionen blir den aktiva versionen. 

En version kan också exporteras på app- eller versionsnivå. Den enda skillnaden är att den exporterade versionen på appnivå är den aktiva versionen på versionsnivå kan du välja vilken version som helst att exportera på sidan **[Inställningar.](luis-how-to-manage-versions.md)** 

Den exporterade filen innehåller **inte:**

* Maskininlärd information eftersom appen omskolas efter att den har importerats
* Information om bidragsgivare

För att säkerhetskopiera luis-appschemat exporterar du en version från [LUIS-portalen](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Hantera deltagarändringar med versioner och deltagare

LUIS använder begreppet bidragsgivare till en app genom att tillhandahålla Azure-behörighet på resursnivå. Kombinera detta koncept med versionshantering för att ge riktat samarbete. 

Använd följande tekniker för att hantera deltagarändringar i appen.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Hantera flera versioner i samma app

Börja med [kloning](luis-how-to-manage-versions.md#clone-a-version) från en basversion för varje författare. 

Varje författare gör ändringar i sin egen version av appen. När författaren är nöjd med modellen exporterar du de nya versionerna till JSON-filer.  

Exporterade appar, .json- eller .lu-filer, kan jämföras för ändringar. Kombinera filerna för att skapa en enda fil i den nya versionen. Ändra `versionId` egenskapen för att ange den nya kopplade versionen. Importera den versionen till den ursprungliga appen. 

Med den här metoden kan du ha en aktiv version, en stegversion och en publicerad version. Du kan jämföra resultaten av den aktiva versionen med en publicerad version (scen eller produktion) i det [interaktiva testfönstret](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Hantera flera versioner som appar

[Exportera](luis-how-to-manage-versions.md#export-version) basversionen. Varje författare importerar versionen. Personen som importerar appen är ägare till versionen. När de är klara med att ändra appen exporterar du versionen. 

Exporterade appar är JSON-formaterade filer, som kan jämföras med basexporten för ändringar. Kombinera filerna för att skapa en enda JSON-fil i den nya versionen. Ändra **egenskapen versionId** i JSON för att ange den nya kopplade versionen. Importera den versionen till den ursprungliga appen.

Läs mer om att skapa bidrag från [medarbetare](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Granska slutpunktsyttranden för att påbörja den nya iterativa cykeln

När du är klar med en iterationscykel kan du upprepa processen. Börja med [att granska förutsägelseslutpunkt yttranden](luis-how-to-review-endpoint-utterances.md) LUIS markeras med låg konfidens. Kontrollera dessa yttranden för både korrekt förutsedd avsikt och korrekt och fullständig entitet som extraherats. När du har granskat och accepterat ändringar ska granskningslistan vara tom.  

## <a name="next-steps"></a>Nästa steg

Lär dig begrepp om [samarbete](luis-concept-keys.md).
