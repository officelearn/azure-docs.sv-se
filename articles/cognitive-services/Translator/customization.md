---
title: Anpassning av översättning - Api för översättare
titleSuffix: Azure Cognitive Services
description: Använd Microsoft Translator Hub för att bygga ditt eget maskinöversättningssystem med din föredragna terminologi och stil.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: be9c06d45da4d83c26f82343c9cb7b19ba19b4ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "71257629"
---
# <a name="customize-your-text-translations"></a>Anpassa dina textöversättningar

Microsoft Custom Translator är en del av Tjänsten Microsoft Translator, som gör det möjligt för användare att anpassa Microsoft Translators avancerade neurala maskinöversättning när de översätter text med Translator Text API (endast version 3).

Funktionen kan också användas för att anpassa talöversättning när den används med [Cognitive Services Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Med Custom Translator kan du bygga neurala översättningssystem som förstår den terminologi som används i ditt eget företag och din egen bransch. Det anpassade översättningssystemet integreras sedan i befintliga program, arbetsflöden och webbplatser.

### <a name="how-does-it-work"></a>Hur fungerar det?

Använd dina tidigare översatta dokument (broschyrer, webbsidor, dokumentation osv.) för att bygga ett översättningssystem som återspeglar din domänspecifika terminologi och stil, bättre än ett standardöversättningssystem. Användare kan ladda upp TMX-, XLIFF-, TXT-, DOCX- och XLSX-dokument.  

Systemet accepterar också data som är parallella på dokumentnivå men som ännu inte är justerade på meningsnivå. Om användarna har tillgång till versioner av samma innehåll på flera språk men i separata dokument kan Custom Translator automatiskt matcha meningar mellan dokument.  Systemet kan också använda enspråkiga data på båda eller båda språken för att komplettera de parallella utbildningsdata för att förbättra översättningarna.

Det anpassade systemet är sedan tillgängligt via ett vanligt anrop till Microsoft Translator Text API med kategoriparametern.

Med tanke på lämplig typ och mängd utbildningsdata är det inte ovanligt att förvänta sig vinster mellan 5 och 10, eller ännu fler BLEU-poäng på översättningskvalitet med hjälp av Custom Translator.

Mer information om de olika anpassningsnivåerna baserat på tillgängliga data finns i [användarhandboken för anpassade översättare](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

> [!NOTE]
> Den äldre Microsoft Translator Hub kommer att dras tillbaka den 17 maj 2019. [Visa viktig migreringsinformation och viktiga datum](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Anpassad översättare kontra hubb

|   | **Nav** | **Custom Translator**|
|:-----|:----:|:----:|
|Status för anpassningsfunktionen   | Allmän tillgänglighet  | Allmän tillgänglighet |
| Text-API-version  | Endast V2   | Endast V3 |
| SMT-anpassning | Ja   | Inga |
| NMT-anpassning | Inga    | Ja |
| Ny anpassning av enhetliga taltjänster | Inga    | Ja |
| [Inga spår](https://www.aka.ms/notrace) | Ja  | Ja |

## <a name="collaborative-translations-framework"></a>Samarbetsramen för översättningar

> [!NOTE]
> Från och med den 1 februari 2018 är AddTranslation() och AddTranslationArray() inte längre tillgängliga för användning med Translator Text API V2.0. Dessa metoder kommer att misslyckas och ingenting kommer att skrivas. Translator Text API V3.0 stöder inte dessa metoder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera ett anpassat språksystem med anpassad översättare](https://aka.ms/CustomTranslatorDocs)
