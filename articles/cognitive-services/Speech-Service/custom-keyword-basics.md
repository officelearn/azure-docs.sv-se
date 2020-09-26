---
title: Skapa anpassade nyckelord – tal tjänsten
titleSuffix: Azure Cognitive Services
description: Enheten lyssnar alltid efter ett nyckelord (eller en fras). När användaren säger nyckelordet skickar enheten allt efterföljande ljud till molnet tills användaren slutar att tala. Att anpassa ditt nyckelord är ett effektivt sätt att särskilja din enhet och förbättra ditt varumärke.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/20/2019
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 188824b9c43387cf106a3cdd53a09fd6bcb198b8
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91360675"
---
# <a name="custom-keyword-basics"></a>Grundläggande anpassade nyckelord

I den här artikeln får du lära dig grunderna i att arbeta med anpassade nyckelord, med tal Studio och talet SDK. Ett nyckelord är ett ord eller en kort fras som gör att produkten röst aktive rad. Du skapar nyckelords modeller i tal Studio och exporterar sedan en modell fil som du använder med talet SDK i dina program.

## <a name="prerequisites"></a>Förutsättningar

Stegen i den här artikeln kräver en tal prenumeration och talet SDK. Om du inte redan har en prenumeration kan du [prova röst tjänsten kostnads fritt](overview.md#try-the-speech-service-for-free). Information om hur du hämtar SDK finns i [installations guiden](quickstarts/setup-platform.md) för din plattform.

## <a name="create-a-keyword-in-speech-studio"></a>Skapa ett nyckelord i tal Studio

Innan du kan använda ett anpassat nyckelord måste du skapa ett nyckelord med hjälp av sidan [anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) i [tal Studio](https://aka.ms/sdsdk-speechportal). När du har angett ett nyckelord genererar det en `.table` fil som du kan använda med talet SDK.

> [!IMPORTANT]
> Anpassade nyckelords modeller och resulterande `.table` filer kan **bara** skapas i tal Studio.
> Du kan inte skapa anpassade nyckelord från SDK eller med REST-anrop.

1. Gå till [tal Studio](https://aka.ms/sdsdk-speechportal) och **Logga** in, eller Välj [**skapa en prenumeration**](https://go.microsoft.com/fwlink/?linkid=2086754)om du inte har en tal prenumeration än.

1. Skapa ett **nytt projekt**på sidan [anpassat nyckelord](https://aka.ms/sdsdk-wakewordportal) . 

1. Ange ett **namn**, en valfri **Beskrivning**och välj språket. Du behöver ett projekt per språk och supporten är för närvarande begränsad till `en-US` språket.

    ![Beskriv ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-new-project.png)

1. Välj ditt projekt i listan. 

    ![Välj ditt nyckelords projekt](media/custom-keyword/custom-kws-portal-project-list.png)

1. Om du vill skapa en ny nyckelords modell klickar du på **träna modell**.

1. Ange ett **namn** för modellen, en valfri **Beskrivning**och valfritt **nyckelord** , och klicka sedan på **Nästa**. Se [rikt linjerna](speech-devices-sdk-kws-guidelines.md#choose-an-effective-keyword) för att välja ett effektivt nyckelord.

    ![Ange ditt nyckelord](media/custom-keyword/custom-kws-portal-new-model.png)

1. Portalen skapar kandidat uttal för ditt nyckelord. Lyssna på varje kandidat genom att klicka på uppspelnings knapparna och ta bort kontrollerna bredvid eventuella uttal som är felaktiga. När endast väluttal är markerat klickar du på **träna** för att börja generera nyckelords modellen. 

    ![Granska ditt nyckelord](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. Det kan ta upp till trettio minuter innan modellen genereras. Nyckelords listan ändras från **bearbetning** till **lyckades** när modellen har slutförts. Sedan kan du hämta filen.

    ![Granska ditt nyckelord](media/custom-keyword/custom-kws-portal-download-model.png)

1. Den nedladdade filen är ett `.zip` Arkiv. Extrahera arkivet så ser du en fil med `.table` tillägget. Detta är den fil som du använder med SDK i nästa avsnitt, så se till att anteckna sökvägen. fil namnet speglar nyckelordets namn, till exempel om en nyckelords **aktiverings enhet** har fil namnet `Activate_device.table` .

## <a name="use-a-keyword-model-with-the-sdk"></a>Använda en nyckelords modell med SDK

Börja med att läsa in din nyckelords modell fil med hjälp av den `FromFile()` statiska funktionen, som returnerar en `KeywordRecognitionModel` . Använd sökvägen till `.table` filen som du laddade ned från tal Studio. Dessutom skapar du en `AudioConfig` med hjälp av standard mikrofonen och instansierar sedan en ny `KeywordRecognizer` med hjälp av ljud konfigurationen.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Sedan utförs körning av nyckelords igenkänning med ett anrop till `RecognizeOnceAsync()` genom att skicka modell objekt. Detta startar en session för att känna igen som varar tills nyckelordet identifieras. Därför använder du vanligt vis det här design mönstret i program med flera trådar, eller i användnings fall där du kan vänta ett Väcknings ord i oändlighet.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Exemplet som visas här använder lokal nyckelords igenkänning eftersom det inte kräver ett `SpeechConfig` objekt för autentiserings kontext och inte kontaktar Server delen. Du kan dock köra både nyckelords igenkänning och verifiering för att [använda en kontinuerlig backend-anslutning](https://docs.microsoft.com/azure/cognitive-services/speech-service/tutorial-voice-enable-your-bot-speech-sdk#view-the-source-code-that-enables-keyword).

## <a name="next-steps"></a>Nästa steg

Testa ditt anpassade nyckelord med [tal enheter SDK snabb start](https://aka.ms/sdsdk-quickstart).
