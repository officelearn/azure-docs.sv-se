---
title: Anpassa en språkmodell med API för videoindexerare
titlesuffix: Azure Media Services
description: Läs om hur du anpassar en språkmodell med API:et för videoindexerare.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: anzaman
ms.openlocfilehash: 19067bbbaf93c9abc9a9220b09dd482ce9115655
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127987"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Anpassa en språkmodell med API:et för videoindexerare

Med Video Indexer kan du skapa anpassade språkmodeller för att anpassa taligenkänning genom att ladda upp anpassningstext, nämligen text från den domän vars ordförråd du vill att motorn ska anpassa sig till. När du tränar din modell kommer nya ord som visas i anpassningstexten att kännas igen.

En detaljerad översikt och metodtips för anpassade språkmodeller finns i [Anpassa en språkmodell med Video Indexer](customize-language-model-overview.md).

Du kan använda API:erna för videoindexerare för att skapa och redigera anpassade språkmodeller i ditt konto, enligt beskrivningen i det här avsnittet. Du kan också använda webbplatsen enligt beskrivningen i [Anpassa språkmodellen med hjälp av videoindexerarens webbplats](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Skapa en språkmodell

[Skapa ett språkmodell-API](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) skapar en ny anpassad språkmodell i det angivna kontot. Du kan ladda upp filer för språkmodellen i det här samtalet. Du kan också skapa språkmodellen här och ladda upp filer för modellen senare genom att uppdatera språkmodellen.

> [!NOTE]
> Du måste fortfarande träna modellen med dess aktiverade filer för att modellen ska lära sig innehållet i dess filer. Anvisningar om utbildning ett språk finns i nästa avsnitt.

Om du vill ladda upp filer som ska läggas till i språkmodellen måste du ladda upp filer i brödtexten med FormData förutom att ange värden för de parametrar som krävs ovan. Det finns två sätt att utföra den här uppgiften:

* Nyckeln kommer att vara filnamnet och värdet kommer att txt filen.
* Nyckeln kommer att vara filnamnet och värdet kommer att vara en URL till txt fil.

### <a name="response"></a>Svar

Svaret innehåller metadata på den nyskapade språkmodellen tillsammans med metadata på var och en av modellens filer enligt formatet för det här exemplet JSON-utdata:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Träna en språkmodell

[Tåget en språkmodell](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) API tränar en anpassad språkmodell i det angivna kontot med innehållet i filerna som laddades upp till och aktiveras i språkmodellen.

> [!NOTE]
> Du måste först skapa språkmodellen och ladda upp dess filer. Du kan ladda upp filer när du skapar språkmodellen eller genom att uppdatera språkmodellen.

### <a name="response"></a>Svar

Svaret innehåller metadata på den nyligen utbildade språkmodellen tillsammans med metadata på var och en av modellens filer enligt formatet för det här exemplet JSON-utdata:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Den `id` returnerade är ett unikt ID som `languageModelId` `linguisticModelId` används för att skilja mellan språkmodeller, medan används både för att ladda upp en video för att [indexera](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) och [indexera om en video-API:er](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) (kallas även i Video Indexer upload/reindex API:er).

## <a name="delete-a-language-model"></a>Ta bort en språkmodell

[Ta bort ett språkmodell-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) tar bort en anpassad språkmodell från det angivna kontot. Alla videoklipp som använde den borttagna språkmodellen behåller samma index tills du indexerar om videon. Om du indexerar om videon kan du tilldela videon en ny språkmodell. Annars använder Video Indexer sin standardmodell för att indexera om videon.

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när språkmodellen tas bort.

## <a name="update-a-language-model"></a>Uppdatera en språkmodell

[Uppdateringen av ett språkmodell-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) uppdaterar en anpassad språkpersonmodell i det angivna kontot.

> [!NOTE]
> Du måste redan ha skapat språkmodellen. Du kan använda det här anropet för att aktivera eller inaktivera alla filer under modellen, uppdatera namnet på språkmodellen och ladda upp filer som ska läggas till i språkmodellen.

Om du vill ladda upp filer som ska läggas till i språkmodellen måste du ladda upp filer i brödtexten med FormData förutom att ange värden för de parametrar som krävs ovan. Det finns två sätt att utföra den här uppgiften:

* Nyckeln kommer att vara filnamnet och värdet kommer att txt filen.
* Nyckeln kommer att vara filnamnet och värdet kommer att vara en URL till txt fil.

### <a name="response"></a>Svar

Svaret innehåller metadata på den nyligen utbildade språkmodellen tillsammans med metadata på var och en av modellens filer enligt formatet för det här exemplet JSON-utdata:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Använd `id` filerna som returneras i svaret för att hämta innehållet i filen.

## <a name="update-a-file-from-a-language-model"></a>Uppdatera en fil från en språkmodell

Med uppdateringen av [en fil](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) kan `enable` du uppdatera namnet och tillståndet för en fil i en anpassad språkmodell i det angivna kontot.

### <a name="response"></a>Svar

Svaret innehåller metadata för filen som du uppdaterade efter formatet för exemplet JSON ut nedan.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Använd `id` filens som returneras i svaret för att hämta innehållet i filen.

## <a name="get-a-specific-language-model"></a>Skaffa en specifik språkmodell

Hämta [get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API returnerar information om den angivna språkmodellen i det angivna kontot, till exempel språk och de filer som finns i språkmodellen.

### <a name="response"></a>Svar

Svaret innehåller metadata på den angivna språkmodellen tillsammans med metadata på var och en av modellens filer enligt formatet för det här exemplet JSON-utdata:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Använd `id` filens som returneras i svaret för att hämta innehållet i filen.

## <a name="get-all-the-language-models"></a>Hämta alla språkmodeller

Hämta [alla](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) API returnerar alla anpassade språkmodeller i det angivna kontot i en lista.

### <a name="response"></a>Svar

Svaret innehåller en lista över alla språkmodeller i ditt konto och var och en av deras metadata och filer enligt formatet för det här exemplet JSON-utdata:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Ta bort en fil från en språkmodell

Borttagnings-API:et tar bort den angivna filen från den angivna språkmodellen i det angivna kontot. [delete](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete)

### <a name="response"></a>Svar

Det finns inget returnerat innehåll när filen tas bort från språkmodellen.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Hämta metadata på en fil från en språkmodell

Hämta [metadata för ett fil-API](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) returnerar innehållet i och metadata på den angivna filen från den valda språkmodellen i ditt konto.

### <a name="response"></a>Svar

Svaret innehåller innehållet och metadata för filen i JSON-format, liknande det här exemplet:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Innehållet i den här exempelfilen är orden "hej" och värld i två separata rader.

## <a name="download-a-file-from-a-language-model"></a>Ladda ned en fil från en språkmodell

[Hämtningen](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) av ett fil-API hämtar en textfil som innehåller innehållet i den angivna filen från den angivna språkmodellen i det angivna kontot. Den här textfilen ska matcha innehållet i textfilen som ursprungligen laddades upp.

### <a name="response"></a>Svar

Svaret kommer att vara nedladdningen av en textfil med innehållet i filen i JSON-format.

## <a name="next-steps"></a>Nästa steg

[Anpassa språkmodell med hjälp av webbplats](customize-language-model-with-website.md)
