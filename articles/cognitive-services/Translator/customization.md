---
title: Anpassning av översättning - API för textöversättning
titlesuffix: Azure Cognitive Services
description: Använd Microsoft Translator Hub för att skapa dina egna maskinöversättning system med hjälp av dina önskade terminologi och stil.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/21/2019
ms.author: v-jansko
ms.openlocfilehash: b9a182da0ce03bf3964b6da30f1ed68de38c8141
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61468371"
---
# <a name="customize-your-text-translations"></a>Anpassa dina textöversättningar

Anpassad Microsoft Translator är en funktion i tjänsten Microsoft Translator, vilket gör att användare kan anpassa Microsoft Translator avancerade neural maskinöversättning översätta text med Translator Text API (endast version 3).

Funktionen kan också användas för att anpassa talöversättning när det används med [Cognitive Services tal](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Med anpassade Translator, kan du skapa neural översättningssystem som förstår de termer som används i din egen verksamhet och branschen. Den anpassade översättningssystemet sedan integreras i befintliga program, arbetsflöden och webbplatser.

### <a name="how-does-it-work"></a>Hur fungerar det?

Använd dina tidigare översatta dokument (bipacksedlar, webbsidor, dokumentation, osv.) att skapa en översättningssystemet som återspeglar ditt domänspecifika terminologi och stil, bättre än en allmän översättningssystemet. Användarna kan ladda upp TMX, XLIFF, TXT, DOCX och XLSX dokument.  

Systemet kan också användas med data som är parallella på dokumentnivå men ännu inte är justerade på nivån mening. Om användarna har åtkomst till versioner av samma innehåll i flera språk, men i separata dokument kommer anpassad Translator att kunna matcha automatiskt meningar för dokument.  Systemet kan också använda enspråkig data på ena eller båda språk för att komplettera parallell utbildning-data för att förbättra översättningarna.

Anpassat system är sedan tillgängligt via ett reguljärt anrop till Microsoft Translator Text API med hjälp av parametern kategori.

Beroende på rätt typ och mängden utbildningsdata som det är inte ovanligt att förvänta sig vinster mellan 5 och 10 eller ännu mer BLEU pekar på översättningen med hjälp av anpassade Translator.

Mer information om de olika nivåerna på anpassning som är baserad på tillgänglig data finns i den [användarhandboken för anpassad Translator](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> Äldre Microsoft Translator Hub tas ur bruk den 30 April 2019. [Visa information om viktiga migrering och datum](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Anpassade Translator jämfört med Hub

|   | **Hubb** | **Anpassade Translator**|
|:-----|:----:|:----:|
|Anpassning av funktionsstatus   | Allmän tillgänglighet  | Allmän tillgänglighet |
| API för textöversättning version  | Endast v2   | Endast v3 |
| SMT anpassning | Ja   | Nej |
| NMT anpassning | Nej    | Ja |
| Nya enhetliga Speech services anpassning | Nej    | Ja |
| [Ingen spårning](https://www.aka.ms/notrace) | Ja  | Ja |

## <a name="collaborative-translations-framework"></a>Collaborative Translations Framework

> [!NOTE]
> Från och med den 1 februari 2018 AddTranslation() och AddTranslationArray() är inte längre tillgängliga för användning med Translator Text API V2.0. De här metoderna misslyckas och inget ska skrivas. Translator Text API V3.0 har inte stöd för dessa metoder.
> 
> Liknande funktionalitet är tillgänglig i hubben Translator-API. Se [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera ett anpassat språksystem med hjälp av anpassade Translator](https://aka.ms/CustomTranslatorDocs)
