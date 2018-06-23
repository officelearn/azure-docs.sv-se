---
title: Komma igång med anpassade tal Service i Azure | Microsoft Docs
description: Prenumerera på tjänsten anpassad tal och länkar tjänsteaktiviteter till en Azure-prenumeration och tränar en modell som du kan göra en distribution.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: fe7a140f5ba2d712014f03663a88d516958d188e
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35351744"
---
# <a name="get-started-with-custom-speech-service"></a>Komma igång med anpassade tal Service

Utforska de viktigaste funktionerna i tjänsten anpassad tal och lär dig att skapa, distribuera och använda modellerna acoustic och språk för dina programbehov. Mer omfattande dokumentation och stegvisa instruktioner finns när du har registrerat dig på anpassad tal Services-portalen.

## <a name="samples"></a>Exempel  
Det är ett bra exempel som vi tillhandahåller för att få med dig gå som du hittar [här](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Förutsättningar  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Prenumerera anpassad tal tjänsten och få en nyckel för prenumeration
Innan du spelar med ovanstående exempel, du måste prenumerera anpassad tal tjänsten och få en prenumeration nyckeln finns [prenumerationer](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) eller följa förklaringarna [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Både den primära och sekundära nyckeln kan användas i den här självstudiekursen. Se till att följa de bästa metoderna för att hålla din API-nyckeln hemlig och säkra.

### <a name="get-the-client-library-and-example"></a>Installera klienten för bibliotek och exempel
Du kan ladda ned en klientbiblioteket och exempel via [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Hämtade zip-filen måste extraheras till en valfri mapp, många användare väljer du mappen för Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Skapa en anpassad ljud modell
En samling taldata krävs för att anpassa ljud modellen till en viss domän. Den här samlingen består av en uppsättning ljudfiler tal data och en textfil av transcriptions för varje ljudfil. Ljuddata ska vara representativ för ett scenario där du vill använda tolken

Exempel: Om du vill att känna igen tal i en miljö med mycket brus factory ljudfiler bör bestå av personer som talar störningar fabrik.
Om du vill optimera prestanda för en enskild talare t.ex. vill du transkribera alla FDR'S Fireside Chat sedan ljudfiler bör bestå av många på den talaren endast.

Du hittar en detaljerad beskrivning på hur du skapar en anpassad ljud modell [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Skapa en anpassad språkmodell
Proceduren för att skapa en anpassad språkmodell liknar att skapa en modell för ljud förutom det finns inga ljuddata endast text. Texten består av flera exempel på frågor eller utterances användarna förväntas stå har inloggad användare säger (eller skriva) i ditt program.

Du hittar en detaljerad beskrivning på hur du skapar en anpassad språkmodell [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Skapa en anpassad till text till tal-slutpunkt
När du har skapat anpassade ljud modeller och/eller språk modeller, kan de distribueras i en anpassad till text till tal-slutpunkt. Klicka på ”distributioner” på ”CRI” menyn överst på sidan om du vill skapa en ny anpassad slutpunkt. Då kommer du till en tabell med namnet ”distributioner” aktuella anpassade slutpunkter. Om du inte har skapat några slutpunkter är tabellen tom. Språket visas i rubriken för tabellen. Om du vill skapa en distribution för ett annat språk klickar du på ”Ändra språk”. Mer information om språk som stöds finns i avsnittet om du ändrar nationella inställningar.

Du hittar en detaljerad beskrivning på hur du skapar en anpassad tal-text slutpunkt [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Med hjälp av en anpassad tal-slutpunkt
Begäranden kan skickas till en CRI till text till tal-slutpunkt på ungefär samma sätt som tal standardslutpunkten kognitiva Microsoft-tjänster. Observera att dessa slutpunkter är funktionellt identiska som standardslutpunkterna för tal-API. Därför är samma funktioner som är tillgängliga via klientbibliotek eller REST API för tal-API: T också tillgängligt för anpassade slutpunkten.

Du hittar en detaljerad beskrivning på hur du använder en anpassad till text till tal-slutpunkt [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Observera att de slutpunkter som skapats via CRI kan bearbeta olika antal samtidiga begäranden beroende på nivån prenumerationen är kopplad till. Om flera erkännanden än vad som krävs, de returneras felkoden 429 (för många begäranden). Mer information finns i [prisinformation](https://www.microsoft.com/cognitive-services/en-us/pricing). Dessutom är en månatlig kvot av begäranden för den kostnadsfria nivån. I de fall du åtkomst till din slutpunkt på den kostnadsfria nivån ovanför den månatliga kvoten tjänsten returnerar felkoden 403 förbjuden.

Tjänsten förutsätter ljud överförs i realtid. Om det skickas snabbare beaktas begäran körs förrän varaktigheten i realtid har passerat.

* [Översikt](cognitive-services-custom-speech-home.md)
* [Vanliga frågor och svar](cognitive-services-custom-speech-faq.md)
* [Ordlista](cognitive-services-custom-speech-glossary.md)
