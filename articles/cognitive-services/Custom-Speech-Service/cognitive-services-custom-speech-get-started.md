---
title: Kom igång med Custom Speech Service på Azure | Microsoft Docs
description: Prenumerera på anpassat tal-tjänst och länkar tjänsteaktiviteter till en Azure-prenumeration du tränar en modell och gör en distribution.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: article
ms.date: 02/08/2017
ms.author: panosper
ms.openlocfilehash: 4bbd4c57556fd4bfd176c915e26be4a4d198418a
ms.sourcegitcommit: 1aacea6bf8e31128c6d489fa6e614856cf89af19
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49339941"
---
# <a name="get-started-with-custom-speech-service"></a>Kom igång med Custom Speech Service

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-custom-speech-deprecation-note.md)]

Utforska de viktigaste funktionerna i Custom Speech Service och lär dig att bygga, distribuera och använda- och språkdata modeller för dina programbehov. Mer omfattande dokumentation och stegvisa instruktioner hittar när du har registrerat dig på Custom Speech Services-portalen.

## <a name="samples"></a>Exempel  
Det är ett bra exempel som vi tillhandahåller för att komma igång som du hittar [här](https://github.com/Microsoft/Cognitive-Custom-Speech-Service).

## <a name="prerequisites"></a>Förutsättningar  

### <a name="subscribe-to-custom-speech-service-and-get-a-subscription-key"></a>Prenumerera på Custom Speech Service och få en prenumerationsnyckel
Innan du spelar med ovanstående exempel, du måste prenumerera på Custom Speech Service och få en prenumeration nyckel finns i [prenumerationer](https://portal.azure.com/#create/Microsoft.CognitiveServices/apitype/CustomSpeech) eller följa förklaringarna [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-subscribe.md). Både den primära och sekundära nyckeln kan användas i den här självstudien. Se till att följa bästa praxis för att hålla din viktiga API-hemlighet och säkra.

### <a name="get-the-client-library-and-example"></a>Hämta bibliotek och exempel
Du kan ladda ned en klientbiblioteket och alla exempel via [SDK](https://www.microsoft.com/cognitive-services/en-us/SDK-Sample?api=bing%20speech&category=sdk). Den hämta zip-filen måste extraheras till en mapp väljer, många användare väljer du mappen för Visual Studio 2015.

## <a name="creating-a-custom-acoustic-model"></a>Skapa en anpassad akustisk modell
För att anpassa den akustiska modellen för en viss domän krävs en samling taldata. Den här samlingen består av en uppsättning ljudfiler med taldata och en textfil med transkriptioner av varje ljudfil. Ljuddata ska vara representativ för ett scenario där du vill använda Igenkännande

Till exempel: Om du vill att känna igen tal i en miljö med bort störande factory ljudfiler ska bestå av personer som talar bort störande fabrik.
Om du är intresserad av att optimera prestanda för en enda talare, t.ex. du skulle vilja transkribera alla FDR'S Fireside Chat och ljudfiler ska bestå av flera exempel på den talaren endast.

Du hittar en detaljerad beskrivning om hur du skapar en anpassad akustisk modell [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-acoustic-model.md).

## <a name="creating-a-custom-language-model"></a>Skapa en anpassad språkmodell
Proceduren för att skapa en anpassad språkmodell liknar skapar en akustisk modell, förutom att det finns inga ljud data endast text. Texten ska bestå av flera exempel på frågor eller uttryck som du förväntar dig användare att säga har inloggad användare säger (eller skriver) i ditt program.

Du hittar en detaljerad beskrivning om hur du skapar en anpassad språkmodell [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-language-model.md).

## <a name="creating-a-custom-speech-to-text-endpoint"></a>Skapa en anpassad tal till text-slutpunkt
När du har skapat anpassade akustiska modeller och/eller språkmodeller, kan de distribueras i en anpassad tal till text-slutpunkt. Om du vill skapa en ny anpassad slutpunkt, klickar du på ”distribution” på ”CRIS”-menyn överst på sidan. Detta tar dig till en tabell med namnet ”distribution” aktuella anpassade slutpunkter. Om du inte har skapat några slutpunkter är i tabellen tom. Den aktuella nationella inställningen visas i tabellrubriken. Om du vill skapa en distribution för ett annat språk klickar du på ”Ändra nationella inställningar”. Mer information om språk som stöds finns i avsnittet om ändra nationella inställningar.

Du hittar en detaljerad beskrivning om hur du skapar en slutpunkt för anpassat tal text [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-create-endpoint.md).

## <a name="using-a-custom-speech-endpoint"></a>Med hjälp av en anpassad slutpunkt
Förfrågningar kan skickas till en slutpunkt för CRIS tal till text på ungefär samma sätt som standard Microsoft Cognitive Services tal slutpunkt. Observera att de här slutpunkterna är funktionen identisk med standardslutpunkterna för API för taligenkänning. Därför är på samma sätt som är tillgängliga via klientbibliotek eller REST API för API för taligenkänning också tillgängligt för din anpassade slutpunkt.

Du hittar en detaljerad beskrivning om hur du använder en anpassad tal till text-slutpunkt [här](CustomSpeech-How-to-Topics/cognitive-services-custom-speech-use-endpoint.md).


Observera att de slutpunkter som skapats via CRIS kan bearbeta olika antal samtidiga begäranden beroende på vilken nivå som prenumerationen är kopplad till. Om flera igenkänningar än den som begärs kan de returnerar felkod 429 (för många förfrågningar). Mer information finns i [prisinformation](https://www.microsoft.com/cognitive-services/en-us/pricing). Det finns dessutom en månatliga kvot för begäranden för den kostnadsfria nivån. I fall du åtkomst till din slutpunkt på den kostnadsfria nivån ovanför din månatliga kvot tjänsten returnerar felkoden 403 Forbidden.

Tjänsten förutsätter att ljudet överförs i realtid. Om det skickas snabbare betraktas begäran köras tills dess varaktighet i realtid har passerat.

* [Översikt](cognitive-services-custom-speech-home.md)
* [Vanliga frågor och svar](cognitive-services-custom-speech-faq.md)
* [Ordlista](cognitive-services-custom-speech-glossary.md)
