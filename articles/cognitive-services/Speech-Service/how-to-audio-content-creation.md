---
title: Ljud innehålls skapande – tal tjänst
titleSuffix: Azure Cognitive Services
description: Att skapa ljud innehåll är ett online verktyg som gör att du kan anpassa och finjustera Microsofts text till tal-utdata för dina appar och produkter.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224595"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>Förbättra syntesen med verktyget för att skapa ljud innehåll

Att [skapa ljud innehåll](https://aka.ms/audiocontentcreation) är ett online verktyg som gör att du kan anpassa och finjustera Microsofts text till tal-utdata för dina appar och produkter. Du kan använda det här verktyget för att finjustera offentliga och anpassade röster för mer exakta naturliga uttryck och hantera dina utdata i molnet.

Verktyget för att skapa ljud innehåll baseras på [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). För att förenkla anpassningen och justeringen kan du med hjälp av ljud innehåll visuellt inspektera dina text till tal-utdata i real tid.

Se [video guiden](https://www.youtube.com/watch?v=O1wIJ7mts_w) för att skapa ljud innehåll.

## <a name="how-does-it-work"></a>Hur fungerar det?

Det här diagrammet visar de steg som krävs för att finjustera text till tal-utdata. Använd länkarna nedan för att lära dig mer om varje steg.

![Ett diagram över de steg som krävs för att finjustera text till tal-utdata.](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Konfigurera ditt Azure-konto och din tal resurs](#set-up-your-azure-account-and-speech-resource) för att komma igång.
2. [Skapa en fil för ljud justering](#create-an-audio-tuning-file) med hjälp av oformaterad text eller SSML-skript.
3. Välj rösten och språket för ditt skript innehåll. Framställning av ljud innehåll innehåller alla [Microsofts text till tal-röster](language-support.md#text-to-speech). Du kan använda standard, neurala eller din egen anpassade röst.
   >[!NOTE]
   > Gated Access är tillgängligt för anpassade neurala-röster, vilket gör att du kan skapa hög definitions röster som liknar naturliga ljud. Mer information finns i [Hantera process](https://aka.ms/ignite2019/speech/ethics).

4. Granska standard syntes resultatet. Förbättra sedan utdata genom att justera uttal, Break, färgdjup, Rate, intonation, röst stil med mera. En fullständig lista över alternativ finns i [tal syntes märknings språk](speech-synthesis-markup.md). Här är en [video](https://youtu.be/mUvf2NbfuYU) som visar hur du finjusterar tal resultatet med att skapa ljud innehåll. 
5. Spara och [exportera det anpassade ljudet](#export-tuned-audio). När du sparar justerings spåret i systemet kan du fortsätta att arbeta och iterera på utdata. När du är nöjd med utdata kan du skapa en ljud skapande aktivitet med funktionen Exportera. Du kan se statusen för export uppgiften och hämta utdata för användning med dina appar och produkter.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Konfigurera ditt Azure-konto och din tal resurs

1. Du måste ha ett Azure-konto för att kunna arbeta med att skapa ljud innehåll. Du kan skapa ett Azure-konto med ditt Microsoft-konto. Följ dessa anvisningar för att [Konfigurera ett Azure-konto](get-started.md#new-resource). 
2. [Skapa en tal resurs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) till ditt Azure-konto. Kontrol lera att pris nivån är inställd på **S0**. Om du använder någon av neurala-rösterna ser du till att du skapar din resurs i en [region som stöds](regions.md#standard-and-neural-voices).
2. När du har skaffat Azure-kontot och tal resursen kan du använda tal tjänster och komma åt [ljud innehåll](https://aka.ms/audiocontentcreation).
3. Välj den tal resurs som du behöver arbeta med. Du kan också skapa en ny tal resurs här. 
4. Du kan ändra din tal resurs när som helst med alternativet **Inställningar** , som finns i det övre navigerings fältet.

## <a name="create-an-audio-tuning-file"></a>Skapa en fil för ljud justering

Det finns två sätt att hämta innehåll till verktyget för att skapa ljud innehåll.

**Alternativ 1:**

1. Klicka på **ny fil** för att skapa en ny fil för ljud justering.
2. Skriv eller klistra in innehållet i redigerings fönstret. Tecknen för varje fil är upp till 20 000. Om skriptet innehåller fler än 20 000 tecken kan du använda alternativ 2 för att automatiskt dela upp innehållet i flera filer. 
3. Glöm inte att spara.

**Alternativ 2:**

1. Klicka på **överför** för att importera en eller flera textfiler. Både oformaterad text och SSML stöds.
2. Om skript filen är mer än 20 000 tecken kan du dela filen med stycken, med tecken eller med reguljära uttryck. 
3. När du laddar upp dina textfiler bör du kontrol lera att filen uppfyller dessa krav.

   | Egenskap | Värde/anteckningar |
   |----------|---------------|
   | Filformat | Oformaterad text (. txt)<br/> SSML text (. txt)<br/> Zip-filer stöds inte |
   | Kodnings format | UTF-8 |
   | Filnamn | Varje fil måste ha ett unikt namn. Dubbletter stöds inte. |
   | Text längd | Textfiler får inte överstiga 20 000 tecken. |
   | SSML-begränsningar | Varje SSML-fil får bara innehålla en enda del av SSML. |

### <a name="plain-text-example"></a>Exempel på oformaterad text

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML text exempel

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportera anpassat ljud

När du har granskat din ljud uppspelning och är nöjd med justeringen och justeringen kan du exportera ljudet.

1. Klicka på **Exportera** för att skapa en ljud skapande aktivitet. **Export till ljud bibliotek** rekommenderas eftersom det stöder långa ljud utdata och den fullständiga ljud uppspelnings upplevelsen. Du kan också ladda ned ljudet till den lokala disken direkt, men bara de första 10 minuterna är tillgängliga. 
2. Välj utdataformat för det justerade ljudet. En lista över format och samplings frekvenser som stöds finns nedan.
3. Du kan visa status för uppgiften på fliken **Exportera aktivitet** . Om aktiviteten Miss lyckas, se sidan detaljerad information för en fullständig rapport.
4. När uppgiften har slutförts är ljudet tillgängligt för hämtning på fliken **ljud bibliotek** .
5. Klicka på **Hämta**. Nu är du redo att använda ditt anpassade ljud i dina appar eller produkter.

### <a name="supported-audio-formats"></a>Ljud format som stöds

| Format | 16 kHz samplings frekvens | 24 kHz samplings frekvens |
|--------|--------------------|--------------------|
| våg | riff-16khz-bitarsläge-mono-PCM | riff-24khz-bitarsläge-mono-PCM |
| MP3 | Audio-16khz-128kbitrate-mono-MP3 | Audio-24khz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Se även

* [Långt ljud-API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
