---
title: Vad är Speech Service?
description: 'Taltjänsten, som är en del av Microsoft Cognitive Services, förenar flera Azure-taltjänster som tidigare var tillgängliga separat: Taligenkänning i Bing (som består av taligenkänning och text till tal), Custom Speech och talöversättning.'
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 09/24/2018
ms.author: v-jerkin
ms.openlocfilehash: b014144dd819aa354596175068bfb2c5905a4377
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/08/2018
ms.locfileid: "48858000"
---
# <a name="what-is-the-speech-service"></a>Vad är Speech Service?


Precis som de andra Azure-taltjänsterna drivs tjänsten Speech av de taltekniker som används i produkter som Cortana och Microsoft Office.

Taltjänsten förenar Azure-talfunktionerna som tidigare var tillgängliga via funktionerna [Taligenkänning i Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) och [Custom Voice](http://customvoice.ai/). Nu ger en prenumeration åtkomst till alla dessa funktioner.

## <a name="main-speech-service-functions"></a>Huvudfunktioner i taltjänsten

Taltjänstens främsta funktioner är tal till text (kallas även taligenkänning och taltranskription), text till tal (talsyntes) och talöversättning.

|Funktion|Funktioner|
|-|-|
|[Tal till text](speech-to-text.md)| <ul><li>Transkriberar kontinuerlig realtidsöversättning av tal till text.<li>Kan masstranskribera tal från ljudinspelningar. <li>Stöder mellanliggande resultat, identifiering av talslut, automatisk textformatering och svordomsmaskering. <li>Kan anropa [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) för att härleda användaravsikten från transkriberat tal.\*|
|[Text till tal](text-to-speech.md)| <ul><li>Konverterar text till naturligt tal. <li>Erbjuder flera kön eller dialekter för många språk. <li>Stöder inmatning av oformaterad text eller Speech Synthesis Markup Language (SSML). |
|[Talöversättning](speech-translation.md)| <ul><li>Översätter strömmande ljud i nära realtid.<li> Kan också bearbeta inspelat tal.<li>Ger resultat som text eller syntetiserat tal. |

\* *För avsiktsigenkänning krävs en LUIS-prenumeration.*

## <a name="customize-speech-features"></a>Anpassa talfunktioner

Du kan använda dina egna data för att träna modellerna som ligger bakom Speech-tjänstens funktioner för tal till text och text till tal.

|Funktion|Modell|Syfte|
|-|-|-|
|Tal till text|[Akustisk modell](how-to-customize-acoustic-models.md)|Hjälper till att transkribera vissa talare och miljöer, till exempel bilar eller fabriker.|
||[Språkmodell](how-to-customize-language-model.md)|Hjälper till att transkribera områdesspecifika ord och grammatik, till exempel medicin eller IT-jargong.|
||[Uttalsmodell](how-to-customize-pronunciation.md)|Hjälper till att transkribera förkortningar och akronymer. |
|Text till tal|[Rösttyp](how-to-customize-voice-font.md)|Ge appen en egen röst genom att träna modellen med exempel på mänskligt tal.|

Du kan använda anpassade modeller på samma ställen där du använder standardmodellerna i appens funktioner för tal till text eller text till tal.

## <a name="use-the-speech-service"></a>Använda Speech-tjänsten

För att förenkla utvecklingen av talaktiverade program tillhandahåller Microsoft en [SDK för tal](speech-sdk.md) som ska användas med taltjänsten. Speech SDK tillhandahåller konsekvent tal till text och API:er för talöversättning för C#, C++ och Java. Om du utvecklar med något av dessa språk gör Speech SDK utvecklingen enklare genom att hantera nätverksinformationen åt dig.

Taltjänsten har även en [REST API](rest-apis.md) som fungerar med alla programmeringsspråk som kan göra HTTP-förfrågningar. REST-gränssnittet erbjuder inte de strömmande realtidsfunktionerna i SDK.

|<br>Metod|Tal<br>till text|Text till<br>Tal|Tal<br>Översättning|<br>Beskrivning|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|Ja|Nej|Ja|Interna API:er för C#, C++ och Java för att förenkla utvecklingen.|
|[REST](rest-apis.md)|Ja|Ja|Nej|En enkel HTTP-baserad API som gör det enkelt att lägga till tal i dina program.|

### <a name="websockets"></a>WebSockets

Speech-tjänsten har även WebSocket-protokoll för strömmande tal till text och talöversättning. SDK:erna för tal använder protokollen för att kommunicera med taltjänsten. Använd Speech SDK i stället för att försöka implementera din egen WebSocket-kommunikation med Speech-tjänsten.

Om du redan har kod som använder Taligenkänning i Bing eller Translator Speech via WebSockets kan du uppdatera den för att använda Speech-tjänsten. WebSocket-protokollen är kompatibla. Endast slutpunkterna är olika.

### <a name="speech-devices-sdk"></a>Speech Devices SDK

[Speech Devices SDK](speech-devices-sdk.md) är en inbyggd plattform för maskin- och programvara för utvecklare av talaktiverade enheter. Vår maskinvarupartner tillhandahåller referensdesign och utveckling av enheter. Microsoft erbjuder en enhetsoptimerad SDK som fullständigt utnyttjar maskinvarans kapacitet.

## <a name="why-move-to-the-speech-service"></a>Varför övergå till taltjänsten?

Taltjänsten innehåller alla funktioner och mer från API för Bing-taligenkänning och tre andra Azure-taltjänster: Anpassat tal, Anpassad röst och Talöversättning. Vi rekommenderar att användare av dessa tjänster migrerar till taltjänsten.

Taltjänsten omfattar många uppgraderingar till övriga tjänster, däribland:

* Högre tillförlitlighet i taligenkänningen. Vi förbättrar kontinuerligt de modeller som används i tjänsten.

* Bättre skalbarhet. Tjänsten kan hantera flera samtidiga förfrågningar, vilket förkortar svarstiden.

* Taltjänsten använder en tidsbaserad prismodell. Mer information finns i [prissättning för taltjänsten](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

* Taltjänsten finns tillgänglig i [flera regioner](regions.md) för att tillgodose behoven hos kunder över hela världen. Du behöver en [Azure-prenumeration](https://docs.microsoft.com/azure/cognitive-services/welcome) för varje region som används av ditt program.

* En prenumerationsnyckel för taltjänsten ger åtkomst till följande funktioner. Varje funktion mäts separat, så att du bara debiteras för de funktioner du använder.

    * [Tal till text](speech-to-text.md)
    * [Anpassat tal till text](https://cris.ai/CustomSpeech)
    * [Text till tal](text-to-speech.md)
    * [Anpassad text till talade röster](https://cris.ai/CustomVoice)
    * [Talöversättning](speech-translation.md) (omfattar inte [textöversättning](https://docs.microsoft.com/azure/cognitive-services/translator/translator-info-overview))

* Tal till text-funktionen i taltjänsten kan integreras med [Language Understanding Service](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS, intelligent tjänst för språkförståelse) för avsiktsigenkänning. En LUIS-slutpunktsnyckel kan också användas med taltjänsten. Mer information finns i [självstudien om avsiktsigenkänning](how-to-recognize-intents-from-speech-csharp.md).

* Tal till text kräver inte längre att du anger ett igenkänningsläge.

* Taltjänsten stöder 24 KHz-röster för text till tal, vilket förbättrar ljudkvaliteten. När detta skrivs finns det två sådana röster (endast amerikansk engelska): `Jessa24kRUS` och `Guy24kRUS`.

* Taltjänstens [batch-baserade transkription](batch-transcription.md) möjliggör effektiv transkribering till text av stora volymer inspelat tal, till exempel kundtjänstinspelningar, för enkel analys och sökning.

* När du använder tjänstens SDK för tal, finns det ingen tidsgräns för transkription av strömmande tal till text.

* Tjänstens [tal-SDK](speech-sdk.md) ger ett konsekvent API för taltjänsten för flera programmeringsspråk och körningsmiljöer (inklusive Windows 10, UWP och .NET Core), vilket gör utvecklingen enklare, särskilt på flera plattformar.

* Taltjänsten är kompatibel med REST API:er och WebSockets-protokollet som används av andra Azure-taltjänster, vilket gör det enkelt att migrera befintliga klientprogram till taltjänsten.

## <a name="speech-scenarios"></a>Tal-scenarier

Användningsfall för taltjänster omfattar:

> [!div class="checklist"]
> * Skapa röstutlösta appar
> * Skriva av kundtjänstinspelningar
> * Implementera röstrobotar

### <a name="voice-user-interface"></a>Röstanvändargränssnitt

Röstinmatning är ett bra sätt att göra appen flexibel, handsfree och snabb att använda. Med en röstaktiverad app behöver användarna bara fråga efter den information de vill ha.

Om din app är avsedd för användning av allmänheten kan du använda standardmodellen för taligenkänning. De identifierar en mängd olika talare i vanliga miljöer.

Om din app används i en specifik domän, till exempel inom medicin eller IT, kan du skapa en [språkmodell](how-to-customize-language-model.md). Du kan använda den här modellen för att lära Speech-tjänsten om den särskilda terminologin som används av din app.

Om din app används i en högljudd miljö, exempelvis en fabrik, kan du skapa en anpassad [akustisk modell](how-to-customize-acoustic-models.md). Den här modellen hjälper Speech-tjänsten att skilja tal från brus och buller.

Det är enkelt att komma igång. Hämta bara [Speech SDK](speech-sdk.md) och följ anvisningarna i relevant [snabbstartsartikel](quickstart-csharp-dotnet-windows.md).

### <a name="call-center-transcription"></a>Avskrifter av kundtjänst

Kundtjänstinspelningar används oftast bara om det uppstår problem med ett samtal. Med taltjänsten är det enkelt att skriva av allt till text. Du kan enkelt indexera texten för [textsökning](https://docs.microsoft.com/azure/search/search-what-is-azure-search) eller använda [Textanalys](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) för att identifiera sentiment, språk och nyckelfraser.

Om dina kundtjänstinspelningar innehåller specialiserad terminologi, t.ex. produktnamn eller IT-jargong, kan du skapa en [språkmodell](how-to-customize-language-model.md) för att lära tjänsten Speech det ordförrådet. En anpassad [akustisk modell](how-to-customize-acoustic-models.md) kan hjälpa taltjänsten att förstå sämre telefonanslutningar.

Mer information om det här scenariot finns i [batch-transkription](batch-transcription.md) med taltjänsten.

### <a name="voice-bots"></a>Röstrobotar

[Robotar](https://dev.botframework.com/) är ett populärt sätt att koppla användare till den information som de vill ha och kunder till företag som de gillar. När du lägger till ett konversationsanpassat användargränssnitt till din webbplats eller din app är funktionen lättare att hitta och komma åt. Med taltjänsten går konversationen in i en ny dimension av smidighet genom att svara på talade frågor av olika slag.

Om du vill lägga till en unik personlighet till din röstaktiverade robot kan du ge den en egen röst. Du skapar en anpassad röst genom en tvåstegsprocess. Först [gör du inspelningar](record-custom-voice-samples.md) av rösten som du vill använda. Därefter [skickar du inspelningarna](how-to-customize-voice-font.md) tillsammans med en textavskrift till Speech-tjänstens [röstanpassningsportal](https://cris.ai/Home/CustomVoice) som sköter resten. När du har skapat din anpassade röst är det enkelt att börja använda den i din app.

## <a name="next-steps"></a>Nästa steg

Hämta en prenumerationsnyckel för taltjänsten.

> [!div class="nextstepaction"]
> [Prova Speech Service kostnadsfritt](get-started.md)
