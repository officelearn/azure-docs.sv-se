---
title: Skapande av ljudinnehåll – taltjänst
titleSuffix: Azure Cognitive Services
description: Skapandet av ljudinnehåll är ett onlineverktyg som gör att du kan anpassa och finjustera Microsofts text-till-tal-utdata för dina appar och produkter.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 8184d30471b4d9171a23c8d03e17b2a54dca2ece
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2020
ms.locfileid: "80397825"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Förbättra syntesen när ljudinnehåll skapas

[Skapandet av ljudinnehåll](https://aka.ms/audiocontentcreation) är ett onlineverktyg som gör att du kan anpassa och finjustera Microsofts text-till-tal-utdata för dina appar och produkter. Du kan använda det här verktyget för att finjustera offentliga och anpassade röster för mer exakta naturliga uttryck och hantera dina utdata i molnet.

Verktyget För att skapa ljudinnehåll baseras på [SSML (Speech Synthesis Markup Language).](speech-synthesis-markup.md) För att förenkla anpassning och justering kan du visuellt granska dina text-till-tal-utdata i realtid.

## <a name="how-does-it-work"></a>Hur fungerar det?

I det här diagrammet visas de steg som krävs för att stämma och exportera anpassade tal-till-text-utdata. Använd länkarna nedan om du vill veta mer om varje steg.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Det första steget är att [skapa ett Azure-konto, registrera en talresurs och få en prenumerationsnyckel](#create-a-speech-resource). När du har en prenumerationsnyckel kan du använda den för att anropa taltjänsten och komma åt [skapandet av ljudinnehåll](https://aka.ms/audiocontentcreation).
2. [Skapa en ljudjusteringsfil](#create-an-audio-tuning-file) med oformaterad text eller SSML.
3. Välj den röst och det språk som du vill ställa in. Skapandet av ljudinnehåll innehåller alla [Microsofts text-till-tal-röster](language-support.md#text-to-speech). Du kan använda standard, neurala eller din egen anpassade röst.
   >[!NOTE]
   > Gated access är tillgänglig för Custom Neural Voices, som gör att du kan skapa HD-röster som liknar naturligt klingande tal. Mer information finns i [Gating-processen](https://aka.ms/ignite2019/speech/ethics).

4. Granska standardresultatet. Använd sedan trimverktyget för att justera uttal, tonhöjd, hastighet, intonation, röststil med mera. En fullständig lista med alternativ finns i [Markeringsspråk för talsyntes](speech-synthesis-markup.md).
5. Spara och [exportera det inställda ljudet](#export-tuned-audio). När du sparar inställningsspåret i systemet kan du fortsätta att arbeta och iterera på utdata. När du är nöjd med utdata kan du skapa en datauppgift för att skapa ljud med exportfunktionen. Du kan observera status för exportuppgiften och hämta utdata för användning med dina appar och produkter.
6. Det sista steget är att använda den anpassade trimmade rösten i dina appar och produkter.

## <a name="create-a-speech-resource"></a>Skapa en talresurs

Följ dessa steg för att skapa en talresurs och ansluta den till Speech Studio.

1. Följ dessa instruktioner för att [registrera dig för ett Azure-konto](get-started.md#new-resource) och skapa en [talresurs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource). Kontrollera att din prisnivå är inställd på **S0**. Om du använder en av neurala röster, se till att du skapar din resurs i en [region som stöds](regions.md#standard-and-neural-voices).
2. Logga in på [skapandet av ljudinnehåll](https://aka.ms/audiocontentcreation).
3. Markera ett befintligt projekt eller klicka på **Skapa ny**.
4. Du kan ändra din prenumeration när som helst med alternativet **Inställningar,** som finns i den övre navigeringsvisaren.

## <a name="create-an-audio-tuning-file"></a>Skapa en ljudjusteringsfil

Det finns två sätt att få in ditt innehåll i verktyget För att skapa ljudinnehåll.

**Alternativ 1:**

1. När du har loggat in på [Skapa ljudinnehåll](https://aka.ms/audiocontentcreation)klickar du på **Ljudjustering** för att skapa en ny ljudjusteringsfil.
2. När redigeringsfönstret visas kan du mata in upp till 10 000 tecken.
3. Glöm inte att rädda.

**Alternativ 2:**

1. När du har loggat in på [Skapa ljudinnehåll](https://aka.ms/audiocontentcreation)klickar du på **Ladda upp** för att importera en eller flera textfiler. Både oformaterad text och SSML stöds.
2. När du laddar upp textfilerna kontrollerar du att innehållet uppfyller dessa krav.

   | Egenskap | Värde / Anteckningar |
   |----------|---------------|
   | Filformat | Oformaterad text (.txt)<br/> SSML-text (.txt)<br/> Zip-filer stöds inte |
   | Kodningsformat | UTF-8 |
   | Filnamn | Varje fil måste ha ett unikt namn. Dubbletter stöds inte. |
   | Textens längd | Textfiler får inte överstiga 10 000 tecken. |
   | SSML-begränsningar | Varje SSML-fil kan bara innehålla en enda SSML-fil. |

### <a name="plain-text-example"></a>Exempel på oformaterad text

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Exempel på SSML-text

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportera anpassat ljud

När du har granskat ljudutgången och är nöjd med justeringen och justeringen kan du exportera ljudet.

1. I verktyget [Skapa ljudinnehåll](https://aka.ms/audiocontentcreation) klickar du på **Exportera** för att skapa en uppgift för att skapa ljud.
2. Välj utdataformat för det inställda ljudet. En lista över format och samplingsfrekvens som stöds finns nedan.
3. Du kan visa aktivitetens status på fliken **Exportera aktivitet.** Om aktiviteten misslyckas läser du den detaljerade informationssidan för en fullständig rapport.
4. När uppgiften är klar kan du hämta ljudet på fliken **Ljudbibliotek.**
5. Klicka på **Ladda ner**. Nu är du redo att använda ditt anpassade ljud i dina appar eller produkter.

### <a name="supported-audio-formats"></a>Ljudformat som stöds

| Format | 16 kHz samplingsfrekvens | 24 kHz samplingsfrekvens |
|--------|--------------------|--------------------|
| Wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| Mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Se även

* [Api för långt ljud](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Talstudio](https://speech.microsoft.com)
