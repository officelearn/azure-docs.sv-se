---
title: Microsoft Translator Text API översättning anpassning | Microsoft Docs
description: Använd Microsoft översättare Hub för att skapa egna maskinöversättning system som använder din önskade terminologi och format.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 05/10/2018
ms.author: v-jansko
ms.openlocfilehash: 1db22a414c41f338c4e7fd6ce9dc7ac739fa9237
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354966"
---
# <a name="customize-your-text-translations"></a>Anpassa din textöversättningar

Microsoft anpassad konverterare förhandsversionen ingår i tjänsten Microsoft Translator, som gör att användare kan anpassa Microsoft Translator avancerade neural maskinöversättning när översätta text med Microsoft översättare Text-API (endast version 3). 

Funktionen kan också användas för att anpassa tal översättning när det används med [kognitiva Services tal preview](https://docs.microsoft.com/en-us/azure/cognitive-services/speech-service/).

## <a name="custom-translator"></a>Anpassade konverterare
Du kan skapa neural översättning system som förstår de termer som används i din egen företag och industrin med anpassad konverterare. Anpassade översättning systemet sedan integreras i befintliga program, arbetsflöden och webbplatser. 

### <a name="how-does-it-work"></a>Hur fungerar det?
Använd tidigare översatta dokument (foldrar, webbsidor, dokumentation o.s.v.) att skapa en översättning system som motsvarar din domän-specifika termer och format, bättre än ett allmänt översättning system. Användarna kan ladda upp TMX, XLIFF, TXT, DOCX och XLSX-dokument.  

Systemet kan också användas med data som är parallell på dokumentnivå men ännu inte har justerats på nivån meningen. Om användarna har åtkomst till versioner av samma innehåll på flera olika språk, men i separata dokument kommer anpassad konverterare att kunna matcha automatiskt meningar mellan dokument.  Systemet kan också använda enspråkig data på ena eller båda språk för att komplettera parallella utbildning data för att förbättra översättningar. 

Anpassade systemet är sedan tillgängliga via en vanlig anrop till Microsoft översättare Text-API med parametern kategori.

Angivna rätt typ och utbildning datamängden det är inte ovanligt kan förvänta sig vinster mellan 5 och 10 eller ännu mer BLEU pekar på översättningen genom att använda anpassade konverterare.

Mer information om de olika typer av anpassningar baserat på tillgängliga data finns i den [användarhandboken för anpassad konverterare](http://aka.ms/CustomTranslatorDocs).


## <a name="microsoft-translator-hub"></a>Microsoft Translator Hub

Äldre Microsoft översättare Hub kan användas för att översätta statistiska maskinöversättning. [Läs mer](https://www.microsoft.com/en-us/translator/hub.aspx) 

## <a name="custom-translator-versus-hub"></a>Anpassade konverterare jämfört med nav

|   | **Hubb** | **Anpassade konverterare**|
|:-----|:----:|:----:|
|Anpassning av funktionsstatus   | Allmän tillgänglighet  | Förhandsversion |
| API-Textversion  | Endast v2   | Endast v3 |
| SMT anpassning | Ja   | Nej | 
| NMT anpassning | Nej    | Ja |
| Ny enhetlig tal services anpassning | Nej    | Ja | 
| [Ingen spårning](http://www.aka.ms/notrace) | Ja   | Ja | 

## <a name="collaborative-translations-framework"></a>Samarbetsfunktioner översättningar Framework

> [!NOTE]
> Från och med den 1 februari 2018 är AddTranslation() och AddTranslationArray() inte längre tillgängliga för användning med API-V2.0 översättare Text. Dessa metoder misslyckas och ingenting kommer att skrivas. Översättare Text API V3.0 har inte stöd för dessa metoder.

>Liknande funktionalitet är tillgänglig i översättare hubb API. Se [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger). 

## <a name="next-steps"></a>Nästa steg
> [!div class="nextstepaction"]
> [Konfigurera en anpassad version av operativsystemet med hjälp av anpassade översättare](http://aka.ms/CustomTranslatorDocs)
