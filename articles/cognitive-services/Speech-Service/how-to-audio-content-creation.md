---
title: Ljud innehålls skapande – tal tjänst
titleSuffix: Azure Cognitive Services
description: Att skapa ljud innehåll är ett online verktyg som gör att du kan anpassa och finjustera Microsofts text till tal-utdata för dina appar och produkter.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: dapine
ms.openlocfilehash: 4785356963e7df1d7b175ac21ceebc921f851ffb
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2020
ms.locfileid: "77913658"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>Förbättra syntesen med att skapa ljud innehåll

Att [skapa ljud innehåll](https://aka.ms/audiocontentcreation) är ett online verktyg som gör att du kan anpassa och finjustera Microsofts text till tal-utdata för dina appar och produkter. Du kan använda det här verktyget för att finjustera offentliga och anpassade röster för mer exakta naturliga uttryck och hantera dina utdata i molnet.

Verktyget för att skapa ljud innehåll baseras på [SSML (Speech syntes Markup Language)](speech-synthesis-markup.md). För att förenkla anpassningen och justeringen kan du med hjälp av ljud innehåll visuellt inspektera dina text till tal-utdata i real tid.

## <a name="how-does-it-work"></a>Hur fungerar det?

Det här diagrammet visar de steg som krävs för att justera och exportera anpassade tal-till-text-utdata. Använd länkarna nedan för att lära dig mer om varje steg.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Det första steget är att [skapa ett Azure-konto, registrera en tal resurs och hämta en prenumerations nyckel](#create-a-speech-resource). När du har en prenumerations nyckel kan du använda den för att anropa röst tjänsten och för att få åtkomst till [ljud innehåll](https://aka.ms/audiocontentcreation).
2. [Skapa en fil för ljud justering](#create-an-audio-tuning-file) med oformaterad text eller SSML.
3. Välj den röst och det språk som du vill justera. Framställning av ljud innehåll innehåller alla [Microsofts text till tal-röster](language-support.md#text-to-speech). Du kan använda standard, neurala eller din egen anpassade röst.
   >[!NOTE]
   > Gated Access är tillgängligt för anpassade neurala-röster, vilket gör att du kan skapa hög definitions röster som liknar naturliga ljud. Mer information finns i [Hantera process](https://aka.ms/ignite2019/speech/ethics).

4. Granska standard resultatet. Använd sedan justerings verktyget för att justera uttal, färgdjup, Rate, intonation, röst stil med mera. En fullständig lista över alternativ finns i [tal syntes märknings språk](speech-synthesis-markup.md).
5. Spara och [exportera det anpassade ljudet](#export-tuned-audio). När du sparar justerings spåret i systemet kan du fortsätta att arbeta och iterera på utdata. När du är nöjd med utdata kan du skapa en ljud skapande aktivitet med funktionen Exportera. Du kan se statusen för export uppgiften och hämta utdata för användning med dina appar och produkter.
6. Det sista steget är att använda den anpassade rösten i dina appar och produkter.

## <a name="create-a-speech-resource"></a>Skapa en tal resurs

Följ dessa steg om du vill skapa en tal resurs och ansluta den till tal Studio.

1. Följ de här anvisningarna för att [Registrera dig för ett Azure-konto](get-started.md#new-resource) och [skapa en tal resurs](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure). Kontrol lera att pris nivån är inställd på **S0**. Om du använder någon av neurala-rösterna ser du till att du skapar din resurs i en [region som stöds](regions.md#standard-and-neural-voices).
2. Logga in på att [skapa ljud innehåll](https://aka.ms/audiocontentcreation).
3. Välj ett befintligt projekt eller klicka på **Skapa nytt**.
4. Du kan ändra din prenumeration när som helst med alternativet **Inställningar** , som finns i det övre navigerings fältet.

## <a name="create-an-audio-tuning-file"></a>Skapa en fil för ljud justering

Det finns två sätt att hämta innehåll till verktyget för att skapa ljud innehåll.

**Alternativ 1:**

1. När du har loggat in på att skapa [ljud innehåll](https://aka.ms/audiocontentcreation)klickar du på **ljud justering** för att skapa en ny fil för ljud justering.
2. När redigerings fönstret visas kan du ange upp till 10 000 tecken.
3. Glöm inte att spara.

**Alternativ 2:**

1. När du har loggat in på att [skapa ljud innehåll](https://aka.ms/audiocontentcreation)klickar du på **överför** för att importera en eller flera textfiler. Både oformaterad text och SSML stöds.
2. När du laddar upp textfilerna kontrollerar du att innehållet uppfyller dessa krav.

   | Egenskap | Värde/anteckningar |
   |----------|---------------|
   | Fil format | Oformaterad text (. txt)<br/> SSML text (. txt)<br/> Zip-filer stöds inte |
   | Kodnings format | UTF-8 |
   | Filnamn | Varje fil måste ha ett unikt namn. Dubbletter stöds inte. |
   | Text längd | Textfiler får inte överstiga 10 000 tecken. |
   | SSML-begränsningar | Varje SSML-fil får bara innehålla en enda del av SSML. |

### <a name="plain-text-example"></a>Exempel på oformaterad text

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML text exempel

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Exportera anpassat ljud

När du har granskat din ljud uppspelning och är nöjd med justeringen och justeringen kan du exportera ljudet.

1. Från verktyget för att skapa ljud [innehåll](https://aka.ms/audiocontentcreation) klickar du på **Exportera** för att skapa en ljud skapande aktivitet.
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
> [Tal Studio](https://speech.microsoft.com)
