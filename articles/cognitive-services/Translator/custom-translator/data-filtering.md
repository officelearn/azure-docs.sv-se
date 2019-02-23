---
title: Data Filtering - Custom Translator
titleSuffix: Azure Cognitive Services
description: När du skickar in dokument som ska användas för att träna en anpassad system genomgå dokumenten en serie bearbetning och filtrering stegen för att förbereda för utbildning.
author: jann-skotdal
manager: christw
ms.service: cognitive-services
ms.subservice: custom-translator
ms.date: 02/21/2019
ms.author: v-jansko
ms.topic: conceptual
ms.openlocfilehash: 5fab0446fe28cc279a732f48ca7560792f611e86
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731488"
---
# <a name="data-filtering"></a>Datafiltrering

När du skickar in dokument som ska användas för att träna en anpassad system genomgå dokumenten en serie bearbetning och filtrering stegen för att förbereda för utbildning. De här stegen beskrivs här. Kunskap om filtrering kan hjälpa dig att förstå antalet mening som visas i anpassade translator samt de steg du kan vidta själv för att förbereda dokumenten för utbildning med anpassade Translator.

## <a name="sentence-alignment"></a>Meningsjustering
Om dokumentet inte är i XLIFF, TMX eller JUSTERA format, justerar anpassad Translator meningar av dokument till varandra, mening efter källa och mål. Translator utföra inte dokumentet justering – det följer din namngivning av dokument att hitta det matchande dokumentet för andra språk. I dokumentet, anpassade Translator som försöker hitta motsvarande meningen på andra språk. Den använder dokumentet markup som inbäddat HTML-taggar för att hjälpa till med justering.  

Om du ser en stor avvikelse mellan antalet meningar i källan och rikta in sida dokument, kanske dokumentet inte parallell i första hand eller av andra orsaker som inte bra alignable. Dokumentet kan användas med en stor skillnad (> 10%) garanterar en andra titt att kontrollera att de är verkligen parallella i meningar på varje sida. Anpassade Translator visar en varning bredvid dokumentet om antalet mening skiljer sig misstänkt.  


## <a name="deduplication"></a>Deduplicering
Anpassade Translator tar bort meningar som finns i test och justera dokument från träningsdata. Borttagningen sker dynamiskt i utbildningen körs inte i steg databearbetning. Anpassade Translator rapporterar antalet mening till dig i Projektöversikt innan kontohanteringsrutiner.  

## <a name="length-filter"></a>Längden filter
* Ta bort meningar med bara ett ord på endera sidan.
* Ta bort meningar med fler än 100 ord på endera sidan.  Kinesiska, japanska, koreanska är undantagna.
* Ta bort meningar med färre än 3 tecken. Kinesiska, japanska, koreanska är undantagna.
* Ta bort meningar med mer än 2 000 tecken för kinesiska, japanska, koreanska.
* Ta bort meningar med mindre än 1% alfanumeriska tecken.
* Ta bort dictionary-posterna som innehåller fler än 50 ord.

## <a name="white-space"></a>Blanksteg
* Ersätt en teckenföljd tomt utrymme inklusive flikar och CR/LF sekvenser med ett enda blanksteg.
* Ta bort inledande eller avslutande blanksteg i meningen

## <a name="sentence-end-punctuation"></a>Mening avslutande punkt
Ersätt flera mening slutet skiljetecken med en enda instans.  

## <a name="japanese-character-normalization"></a>Japanska tecken normalisering
Normalisera duplicerade japanska tecken: Konvertera halv bredd till full bredd tecken.

## <a name="unescaped-xml-tags"></a>Unescaped XML-taggar
Filtrering transformeringar unescaped taggar till undantagen taggar:
* `&lt;` blir `&amp;lt;`
* `&gt;` blir `&amp;gt;`
* `&amp;` blir `&amp;amp;`

## <a name="invalid-characters"></a>Ogiltiga tecken
Anpassade Translator tar bort meningar som innehåller Unicode-tecknet U + FFFD. Tecknet U + FFFD anger en misslyckad kodning konvertering.

## <a name="next-steps"></a>Nästa steg

- [Träna en modell](how-to-train-model.md) i anpassade Translator.
