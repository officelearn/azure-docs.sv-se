---
title: Datafiltrering – anpassad översättare
titleSuffix: Azure Cognitive Services
description: När du skickar in dokument som ska användas för att träna ett anpassat system genomgår dokumenten en serie bearbetnings- och filtreringssteg för att förbereda för utbildning.
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 02/21/2019
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: 1028443eaaf6c483cd7cd57289b0dcf2a9f11902
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595898"
---
# <a name="data-filtering"></a>Datafiltrering

När du skickar in dokument som ska användas för att träna ett anpassat system genomgår dokumenten en serie bearbetnings- och filtreringssteg för att förbereda för utbildning. Dessa steg förklaras här. Kunskapen om filtreringen kan hjälpa dig att förstå antalet meninger som visas i anpassad översättare samt de åtgärder du kan vidta för att förbereda dokumenten för utbildning med Custom Translator.

## <a name="sentence-alignment"></a>Meningsjustering
Om dokumentet inte finns i XLIFF-, TMX- eller ALIGN-format justerar Custom Translator käll- och måldokumentens meningar mot varandra, mening för mening. Översättaren utför inte dokumentjustering – det följer ditt namn på dokumenten för att hitta det matchande dokumentet på det andra språket. I dokumentet försöker Custom Translator hitta motsvarande mening på det andra språket. Den använder dokumentmarkering som inbäddade HTML-taggar för att hjälpa till med justeringen.  

Om du ser en stor avvikelse mellan antalet meningar i käll- och målsidans dokument kanske dokumentet inte har varit parallellt i första hand eller av andra skäl inte kunde justeras. Dokumentet parar ihop med en stor skillnad (>10%) av meningar på varje sida motiverar en andra titt för att se till att de verkligen är parallella. Anpassad översättare visar en varning bredvid dokumentet om antalet meninger skiljer sig åt på ett misstänkt sätt.  


## <a name="deduplication"></a>Deduplicering
Custom Translator tar bort de meningar som finns i test- och justeringsdokument från träningsdata.Borttagningen sker dynamiskt inuti träningskörningen, inte i databehandlingssteget. Custom Translator rapporterar antalet meningen till dig i projektöversikten innan sådan borttagning.  

## <a name="length-filter"></a>Längdfilter
* Ta bort meningar med bara ett ord på vardera sidan.
* Ta bort meningar med mer än 100 ord på vardera sidan.Kinesiska, japanska, koreanska är undantagna.
* Ta bort meningar med färre än 3 tecken. Kinesiska, japanska, koreanska är undantagna.
* Ta bort meningar med mer än 2000 tecken för kinesiska, japanska, koreanska.
* Ta bort meningar med mindre än 1 % alfatecken.
* Ta bort ordlisteposter som innehåller fler än 50 ord.

## <a name="white-space"></a>Tomt utrymme
* Ersätt alla sekvenser av blankstegstecken, inklusive flikar och CR/LF-sekvenser med ett enda blankstegstecken.
* Ta bort inledande eller avslutande utrymme i meningen

## <a name="sentence-end-punctuation"></a>Skiljetecken i meningsslut
Ersätt flera skiljetecken i meningen med en enda instans.  

## <a name="japanese-character-normalization"></a>Japansk teckennormalisering
Konvertera bokstäver och siffror med full bredd till halvbreddstecken.

## <a name="unescaped-xml-tags"></a>Xml-taggar som inte har kaps med
Filtrering omvandlar icke kapslade taggar till förrymda taggar:
* `&lt;`Blir`&amp;lt;`
* `&gt;`Blir`&amp;gt;`
* `&amp;`Blir`&amp;amp;`

## <a name="invalid-characters"></a>Ogiltiga tecken
Anpassad översättare tar bort meningar som innehåller Unicode-tecknet U+FFFD. Tecknet U+FFFD anger en misslyckad kodningskonvertering.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md) i Custom Translator.
