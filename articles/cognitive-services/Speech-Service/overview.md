---
title: Vad är taltjänst (förhandsversion)?
description: 'Tjänsten tal, en del av Microsofts Cognitive Services och unites flera Azure taltjänster som tidigare var tillgängliga separat: Bing-tal (som består av taligenkänning och text till tal), anpassat tal och Talöversättning.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 60ff2f71766a14af17ebb1cb9d20825976471296
ms.sourcegitcommit: 1aedb52f221fb2a6e7ad0b0930b4c74db354a569
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/17/2018
ms.locfileid: "41987648"
---
# <a name="what-is-the-speech-service-preview"></a>Vad är taltjänst (förhandsversion)?

Med en prenumeration ger taltjänsten utvecklare ett enkelt sätt att lägga till kraftfulla funktioner som stöd för tal i sina program. Dina appar kan nu funktionen voice-kommandot, transkription, diktering, talsyntes och talöversättning.

Med taltjänsten drivs av de tekniker som används i andra Microsoftprodukter, inklusive Cortana och Microsoft Office.

> [!NOTE]
> Speech-tjänsten är för närvarande i offentlig förhandsversion. Gå tillbaka här regelbundet efter uppdateringar till dokumentation och ytterligare exempel.

## <a name="speech-service-features"></a>Speech service-funktioner

|Funktion|Beskrivning|
|-|-|
|[Tal till text](speech-to-text.md)| Transkriberar ljud till text som ditt program kan acceptera som indata. Är integrerat med den [tjänst för Språkförståelse](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) för att härleda användaravsikt från yttranden.|
|[Text till tal](text-to-speech.md)| Konverterar oformaterad text till tal för naturlig standardrösttyper, levereras till ditt program i en ljudfil. Flera röster, varierande i kön eller accent, är tillgängliga för många språk som stöds. |
|[Talöversättning –](speech-translation.md)| Kan användas antingen för att översätta strömmande ljud i nära realtid eller för att behandla inspelade tal. |
|Anpassad tal till text|Du kan anpassa tal till text genom att skapa din egen [akustiska](how-to-customize-acoustic-models.md) och [språk](how-to-customize-language-model.md) modeller och genom att ange anpassade [uttal](how-to-customize-pronunciation.md) regler. |
|[Anpassad text till tal](how-to-customize-voice-font.md)|Du kan skapa egna röster för text till tal.|
|[Tal enheter SDK](speech-devices-sdk.md)| Med introduktionen av enhetliga Speech-tjänsten erbjuder Microsoft och dess samarbetspartner en plattform för integrerad maskin-och programvara som optimerats för utveckla tal-aktiverade enheter |

## <a name="access-to-the-speech-service"></a>Åtkomst till tjänsten tal

Speech-tjänsten är tillgänglig på två sätt. [SDK: N](speech-sdk.md) avlägsnar information om nätverksprotokoll för enklare utveckling på plattformar som stöds. Den [REST API](rest-apis.md) fungerar med alla programmeringsspråk, men inte tillhandahåller de funktioner som erbjuds av SDK: N.

|<br>Metod|Tal<br>till Text|Text som ska<br>Tal|Tal<br>Översättning|<br>Beskrivning|
|-|-|-|-|-|
|[SDK: er](speech-sdk.md)|Ja|Nej|Ja|Bibliotek för specifika programmeringsspråk, använda Websocket-baserade procotol som förenklar utveckling.|
|[REST](rest-apis.md)|Ja|Ja|Nej|En enkel HTTP-baserad API som gör det enkelt att lägga till tal i ditt program.|

## <a name="speech-scenarios"></a>Tal-scenarier

Några vanliga användningsområden för tal teknik beskrivs kortfattat nedan. Den [tal SDK](speech-sdk.md) är centrala för de flesta av dessa scenarier.

> [!div class="checklist"]
> * Skapa voice-utlöst appar
> * Transkribera anrop center inspelningar
> * Implementera röst robotar

### <a name="voice-triggered-apps"></a>Röst-utlöst appar

Röstinmatning är ett bra sätt att göra din app flexibla, handsfree- och snabbt att använda. Användare kan bara ställa för den information de vill i stället för att behöva navigera till den genom att trycka på en röst-aktiverad app.

Om din app är avsedd att användas av allmänheten, kan du använda baslinje tal igenkänning av modellen tillhandahålls av tjänsten tal. Fungerar bra på att känna igen en mängd olika talare i miljöer.

Om din app kommer att användas i en specifik domän (till exempel medicin eller IT), kan du skapa en [språkmodell](how-to-customize-language-model.md) att lära Speech-tjänsten om den särskilda terminologi som används av din app.

Om din app ska användas i en bort störande miljö, exempelvis en fabrik kan du skapa en anpassad [akustisk modell](how-to-customize-acoustic-models.md) för att bättre Speech-tjänsten att skilja tal från bruset.

Komma igång är lika enkelt som att ladda ned den [tal SDK](speech-sdk.md) och följa en relevanta [snabbstarten](quickstart-csharp-dotnet-windows.md) artikeln.

### <a name="transcribe-call-center-recordings"></a>Transkribera anrop center inspelningar

Ofta samråd anrop center inspelningar endast om ett problem uppstår med ett anrop. Det är enkelt att transkribera varje inspelning till text med Speech-tjänsten. När de är text, kan du enkelt indexera dem för [fulltextsökning](https://docs.microsoft.com/azure/search/search-what-is-azure-search) eller tillämpa [textanalys](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) att identifiera känsla, språk och diskussionsämnen.

Om din anrop center inspelningar innehåller ofta specialiserade terminologi (till exempel produktnamn eller IT-jargong), kan du skapa en [språkmodell](how-to-customize-language-model.md) att lära Speech-tjänsten som ordförråd. En anpassad [akustisk modell](how-to-customize-acoustic-models.md) hjälp med taltjänsten kan förstå mindre än optimala phone anslutningar.

Mer information om det här scenariot, Läs mer om [batch avskrift](batch-transcription.md) med Speech-tjänsten.

### <a name="voice-bots"></a>Röst robotar

[Robotar](https://dev.botframework.com/) är en alltmer populära sätt att ansluta användare med informationen som de vill och kunder med företag som de älskar. Om du lägger till ett konversationsanpassade användargränssnitt till din webbplats eller app blir dess funktionalitet lättare att hitta och snabbare att komma åt. Med Speech-tjänsten tar den här konversationen på en ny dimension av både smidigheten genom att faktiskt svara på frågor om talat med syntetiskt tal.

Om du vill lägga till en personlighet i din röst-aktiverade robot och Stärk ditt varumärke, kan du ge den en röst egna. Skapa en anpassad röst är en tvåstegsprocess. Först du [gör inspelningar](record-custom-voice-samples.md) på röst som du vill använda. Du [skicka dessa inspelningar](how-to-customize-voice-font.md) (tillsammans med text avskrifter) till tjänsten tal [röst anpassning av portalen](https://cris.ai/Home/CustomVoice), vilket gör resten. När du har skapat din anpassade röst, är det enkelt att använda i din app.

## <a name="next-steps"></a>Nästa steg

Få en prenumerationsnyckel för Speech-tjänsten.

> [!div class="nextstepaction"]
> [Prova Speech-tjänsten utan kostnad](get-started.md)
