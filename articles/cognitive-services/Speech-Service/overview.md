---
title: Vad är Speech Service (förhandsversion)?
description: 'Taltjänsten, som är en del av Microsoft Cognitive Services, förenar flera Azure-taltjänster som tidigare var tillgängliga separat: Taligenkänning i Bing (som består av taligenkänning och text till tal), Custom Speech och talöversättning.'
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
ms.service: cognitive-services
ms.component: speech-service
ms.topic: overview
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 922320bb0b880e933b27025257e6a533fe257680
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/27/2018
ms.locfileid: "43091480"
---
# <a name="what-is-the-speech-service"></a>Vad är Speech Service?

Taltjänsten förenar Azure-talfunktionerna som tidigare var tillgängliga via funktionerna [Taligenkänning i Bing](https://docs.microsoft.com/azure/cognitive-services/speech/home), [Translator Speech](https://docs.microsoft.com/azure/cognitive-services/translator-speech/), [Custom Speech](https://docs.microsoft.com/azure/cognitive-services/custom-speech-service/cognitive-services-custom-speech-home) och [Custom Voice](http://customvoice.ai/). Nu ger en prenumeration åtkomst till alla dessa funktioner.

Precis som de andra Azure-taltjänsterna drivs taltjänsten av de beprövade taltekniker som används i produkter som Cortana och Microsoft Office. Du kan räkna med kvalitativa resultat och tillförlitligheten från Azure-molnet.

> [!NOTE]
> Speech-tjänsten är för närvarande en offentlig förhandsversion. Kom tillbaka hit då och då för att få dokumentationsuppdateringar, nya kodexempel och mycket mer.

## <a name="main-speech-service-functions"></a>Huvudfunktioner i taltjänsten

Taltjänstens främsta funktioner är tal till text (kallas även taligenkänning och taltranskription), text till tal (talsyntes) och talöversättning.

|Funktion|Funktioner|
|-|-|
|[Tal till text](speech-to-text.md)| <ul><li>Transkriberar kontinuerlig realtidsöversättning av tal till text.<li>Kan masstranskribera tal från ljudinspelningar. <li>Erbjuder igenkänningslägen för interaktiva, konversations- och dikteringsanvändningsfall.<li>Stöder mellanliggande resultat, identifiering av talslut, automatisk textformatering och svordomsmaskering. <li>Kan anropa [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS) för att härleda användaravsikten från transkriberat tal.\*|
|[Text till tal](text-to-speech.md)| <ul><li>Konverterar text till naturligt tal. <li>Erbjuder flera kön eller dialekter för många språk. <li>Stöder inmatning av oformaterad text eller Speech Synthesis Markup Language (SSML). |
|[Talöversättning](speech-translation.md)| <ul><li>Översätter strömmande ljud i nära realtid<li> Kan också bearbeta inspelat tal<li>Ger resultat som text eller syntetiserat tal. |

\* *För avsiktsigenkänning krävs en LUIS-prenumeration.*


## <a name="customizing-speech-features"></a>Anpassa talfunktioner

Med taltjänsten kan du använda dina egna data för att träna modellerna med underliggande taltjänsts tal till text och text till tal-funktioner. 

|Funktion|Modell|Syfte|
|-|-|-|
|Tal till text|[Akustisk modell](how-to-customize-acoustic-models.md)|Hjälper till att transkribera vissa talare och miljöer, till exempel bilar eller fabriker|
||[Språkmodell](how-to-customize-language-model.md)|Hjälper till att transkribera områdesspecifika ord och grammatik, till exempel medicin eller IT-jargong|
||[Uttalsmodell](how-to-customize-pronunciation.md)|Hjälper till att transkribera förkortningar och akronymer |
|Text till tal|[Rösttyp](how-to-customize-voice-font.md)|Ge appen en egen röst genom att träna modellen med exempel på mänskligt tal.|

När dina anpassade modeller har skapats kan de användas var du vill där du skulle använda standardmodellerna i appens funktioner tal till text eller text till tal.


## <a name="using-the-speech-service"></a>Använda taltjänsten

För att förenkla utvecklingen av talaktiverade program tillhandahåller Microsoft en [SDK för tal](speech-sdk.md) som ska användas med den nya taltjänsten. SDK:n för tal tillhandahåller konsekvent tal till text och API:er för talöversättning för C#, C++ och Java. Om du utvecklar med någon av dessa språk gör tal-SDK:n utvecklingen enklare genom att hantera nätverksinformationen åt dig.

Taltjänsten har även en [REST API](rest-apis.md) som fungerar med alla programmeringsspråk som kan göra HTTP-förfrågningar. Men REST-gränssnittet erbjuder inte samma realtidsfunktion för strömning som SDK:n.

|<br>Metod|Tal<br>till text|Text till<br>Tal|Tal<br>Översättning|<br>Beskrivning|
|-|-|-|-|-|
|[Speech SDK](speech-sdk.md)|Ja|Nej|Ja|Interna API:er för C#, C++ och Java för att förenkla utvecklingen.|
|[REST](rest-apis.md)|Ja|Ja|Nej|En enkel HTTP-baserad API som gör det enkelt att lägga till tal i dina program.|

### <a name="websockets"></a>WebSockets

Taltjänsten har även WebSockets-protokoll för att strömma tal till text och talöversättning. SDK:erna för tal använder protokollen för att kommunicera med taltjänsten. Du ska använda tal-SDK:n istället för att försöka implementera din egen WebSockets-kommunikation med taltjänsten.

Men om du redan har kod som använder Taligenkänning i Bing eller Translator Speech via WebSockets kan du uppdatera den för att använda taltjänsten. WebSockets-protokollen är kompatibla. Endast slutpunkterna är olika.

### <a name="speech-devices-sdk"></a>Speech Devices SDK

[Speech Devices SDK](speech-devices-sdk.md) är en inbyggd plattform för maskin- och programvara för utvecklare av talaktiverade enheter. Vår maskinvarupartner tillhandahåller referensdesign och utveckling av enheter. Microsoft erbjuder en enhetsoptimerad SDK som fullständigt utnyttjar maskinvarans kapacitet.


## <a name="speech-scenarios"></a>Tal-scenarier

Användningsfall för taltjänster omfattar:

> [!div class="checklist"]
> * Skapa röstutlösta appar
> * Skriva av kundtjänstinspelningar
> * Implementera röstrobotar

### <a name="voice-user-interface"></a>Röstanvändargränssnitt

Röstinmatning är ett bra sätt att göra appen flexibel, handsfree och snabb att använda. I en röstaktiverad app kan användarna bara be om den information de vill ha istället för att behöva navigera till den.

Om din app är avsedd för användning av allmänheten kan du använda standardmodellen för taligenkänning. De är bra på att känna igen många olika talare i vanliga miljöer.

Om din app ska användas i en viss domän (till exempel medicin eller IT) kan du skapa en [språkmodell](how-to-customize-language-model.md) för att lära taltjänsten den särskilda terminologi som används i appen.

Om din app ska användas i en bullrig miljö, exempelvis en fabrik, kan du skapa en anpassad [akustisk modell](how-to-customize-acoustic-models.md) för att taltjänsten ska kunna skilja tal från brus bättre.

Du kommer igång genom att ladda ned [Speech SDK](speech-sdk.md) och välja en relevant [snabbstartsartikel](quickstart-csharp-dotnet-windows.md).

### <a name="call-center-transcription"></a>Avskrifter av kundtjänst

Kundtjänstinspelningar används oftast bara om det uppstår problem med ett samtal. Med taltjänsten är det enkelt att skriva av allt till text. När det har blivit text kan du enkelt indexera dem för [textsökning](https://docs.microsoft.com/azure/search/search-what-is-azure-search) eller använda [Textanalys](https://docs.microsoft.com/azure/cognitive-services/Text-Analytics/) för att identifiera sentiment, språk och nyckelfraser.

Om dina kundtjänstinspelningar kretsar kring specialiserad terminologi (som produktnamn eller IT-jargong) kan du skapa en [språkmodell](how-to-customize-language-model.md) för att lära taltjänsten det ordförrådet. En anpassad [akustisk modell](how-to-customize-acoustic-models.md) kan hjälpa taltjänsten att förstå sämre telefonanslutningar.

Mer information om det här scenariot finns i [batch-transkription](batch-transcription.md) med taltjänsten.

### <a name="voice-bots"></a>Röstrobotar

[Robotar](https://dev.botframework.com/) blir alltmer populärt för att ansluta användare med informationen du önskar, och kunder med företag de uppskattar. När du lägger till ett användargränssnitt för konversationer till din webbplats eller app blir funktionerna lättare att hitta och de kan nås snabbare. Med taltjänsten går konversationen in i en ny dimension av smidighet genom att svara på talade frågor av olika slag.

Om du vill ge din röstaktiverade robot en unik personlighet (och stärka ditt varumärke) kan du ge den en egen röst. Du skapar en anpassad röst genom en tvåstegsprocess. Först [gör du inspelningar](record-custom-voice-samples.md) av rösten som du vill använda. Därefter [skickar du inspelningarna](how-to-customize-voice-font.md) (tillsammans med en textavskrift) till taltjänstens [röstanpassningsportal](https://cris.ai/Home/CustomVoice) som sköter resten. När du har skapat en anpassad röst är det enkelt att använda den i appen.

## <a name="next-steps"></a>Nästa steg

Hämta en prenumerationsnyckel för taltjänsten.

> [!div class="nextstepaction"]
> [Prova Speech Service kostnadsfritt](get-started.md)
