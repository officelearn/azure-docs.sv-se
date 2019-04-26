---
title: Ändra, träna appen, Java
titleSuffix: Language Understanding - Azure Cognitive Services
description: I den här Java-snabbstarten lägger du till exempelyttranden till en app för hemautomatisering och tränar appen.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 43af108b821daaf0d8b030879816ec339e432dee
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60196753"
---
# <a name="quickstart-change-model-using-java"></a>Snabbstart: Ändra modell med hjälp av Java 

[!INCLUDE [Quickstart introduction for endpoint](../../../includes/cognitive-services-luis-qs-endpoint-intro-para.md)]

## <a name="prerequisites"></a>Nödvändiga komponenter

[!INCLUDE [Quickstart prerequisites for endpoint](../../../includes/cognitive-services-luis-qs-change-model-prereq.md)]
* [JDK SE](https://aka.ms/azure-jdks) (Java Development Kit, Standard Edition)
* [Googles GSON JSON-bibliotek](https://github.com/google/gson).

[!INCLUDE [Quickstart note about code repository](../../../includes/cognitive-services-luis-qs-change-model-luis-repo-note.md)]

## <a name="example-utterances-json-file"></a>JSON-fil med exempelyttranden

[!INCLUDE [Quickstart explanation of example utterance JSON file](../../../includes/cognitive-services-luis-qs-change-model-json-ex-utt.md)]

## <a name="create-quickstart-code"></a>Kod för att skapa snabbstart

1. Lägg till Java-beroendena till filen med namnet `AddUtterances.java`.

   [!code-java[Java Dependencies](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=23-26 "Java Dependencies")]

2. Skapa klassen `AddUtterances`. Den här klassen innehåller alla kodstycken som följer.

    ```Java
    public class AddUtterances {
        // Insert code here
    }
    ```

3. Lägg till LUIS-konstanterna i klassen. Kopiera följande kod och ändra till din redigeringsnyckel, ditt program-ID och ditt versions-ID.

   [!code-java[LUIS-based IDs](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=33-44 "LUIS-based IDs")]

4. Lägg till metoden för att anropa LUIS API till klassen AddUtterances. 

   [!code-java[HTTP request to LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=46-168 "HTTP request to LUIS")]

5. Lägg till metoden för HTTP-svaret från LUIS API till klassen AddUtterances.

   [!code-java[HTTP response from LUIS](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=170-202 "HTTP response from LUIS")]

6. Lägg till undantagshantering i klassen AddUtterances. 

   [!code-java[Exception Handling](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=205-243 "Exception Handling")]

7. Lägg till huvudfunktionen i klassen AddUtterances.

   [!code-java[Add main function](~/samples-luis/documentation-samples/quickstarts/change-model/java/AddUtterances.java?range=245-278 "Add main function")]

## <a name="build-code"></a>Kompilera koden

Kompilera AddUtterance med beroendena

```console
> javac -classpath gson-2.8.2.jar AddUtterances.java
```

## <a name="run-code"></a>Köra koden
Om `AddUtterance` anropas utan argument läggs LUIS-yttrandena till i appen, men de tränas inte.

```console
> java -classpath .;gson-2.8.2.jar AddUtterances
```

Den här kommandoraden visar resultatet av anrop av API för att lägga till yttranden. 

[!INCLUDE [Quickstart response from API calls](../../../includes/cognitive-services-luis-qs-change-model-json-results.md)]

## <a name="clean-up-resources"></a>Rensa resurser
När du är klar med snabbstarten tar du bort alla filer som skapas i den här snabbstarten. 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"] 
> [Skapa en LUIS-app programmässigt](luis-tutorial-node-import-utterances-csv.md) 
