---
title: Översättnings anpassning – översättare
titleSuffix: Azure Cognitive Services
description: Använd Microsoft Translator Hub för att bygga ett eget maskin översättnings system med önskad terminologi och stil.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 6db43300632ec5b2c4f6c18848442901a40561b0
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997006"
---
# <a name="customize-your-text-translations"></a>Anpassa dina text översättningar

Den anpassade översättaren är en funktion i Translator-tjänsten, vilket gör att användare kan anpassa Microsoft translators avancerade neurala för maskin översättning vid översättning av text med hjälp av Translator (endast version 3).

Funktionen kan också användas för att anpassa tal översättning när den används med [Cognitive Services tal](https://docs.microsoft.com/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Custom Translator

Med anpassad översättare kan du bygga neurala översättnings system som förstår terminologin som används i din egen verksamhet och bransch. Det anpassade översättnings systemet integreras sedan i befintliga program, arbets flöden och webbplatser.

### <a name="how-does-it-work"></a>Hur fungerar det?

Använd dina tidigare översatt dokument (broschyrer, webb sidor, dokumentation osv.) för att bygga ett översättnings system som motsvarar din domän specifika terminologi och stil, bättre än ett standard översättnings system. Användare kan ladda upp TMX-, XLIFF-, TXT-, DOCX-och XLSX-dokument.  

Systemet accepterar också data som är parallella på dokument nivån, men som ännu inte har justerats på menings nivå. Om användarna har till gång till versioner av samma innehåll på flera språk, men i separata dokument kan anpassade översättare automatiskt matcha meningar i dokument.  Systemet kan också använda monolingual-data i antingen eller båda språken för att komplettera de parallella tränings data för att förbättra översättningarna.

Det anpassade systemet är sedan tillgängligt via ett vanligt anrop till Translator med hjälp av parametern Category.

Med hänsyn till lämplig typ och mängd av utbildnings data är det inte ovanligt att förväntas vinster mellan 5 och 10, eller till och med fler BLEU punkter om översättnings kvalitet med hjälp av anpassad översättare.

Mer information om olika anpassnings nivåer som baseras på tillgängliga data finns i [användar handboken för anpassad översättare](https://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft Translator-hubb

> [!NOTE]
> Den äldre Microsoft Translator-hubben kommer att dras tillbaka den 17 maj 2019. [Visa viktig information och datum för migreringen](https://www.microsoft.com/translator/business/hub/).  

## <a name="custom-translator-versus-hub"></a>Anpassad översättare jämfört med hubb

|   | **)** | **Custom Translator**|
|:-----|:----:|:----:|
|Status för anpassnings funktion    | Allmän tillgänglighet    | Allmän tillgänglighet |
| Text-API-version    | Endast v2    | Endast v3 |
| SMT-anpassning    | Ja    | Nej |
| Anpassning av NMT    | Nej    | Ja |
| Ny anpassning av enhetligt tal tjänster    | Nej    | Ja |
| [Ingen spårning](https://www.aka.ms/notrace) | Ja    | Ja |

## <a name="collaborative-translations-framework"></a>Ramverk för samarbets översättningar

> [!NOTE]
> Från och med 1 februari 2018 är AddTranslation () och AddTranslationArray () inte längre tillgängliga för användning med Translator v 2.0. Dessa metoder fungerar inte och inget skrivs. Translator v 3.0 stöder inte dessa metoder.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera ett anpassat språk system med anpassad översättare](https://aka.ms/CustomTranslatorDocs)
