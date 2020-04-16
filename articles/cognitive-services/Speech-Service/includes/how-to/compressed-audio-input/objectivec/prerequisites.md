---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421876"
---
Hantering av komprimerat ljud implementeras med [GStreamer](https://gstreamer.freedesktop.org). Av licensskäl kompileras inte GStreamer-binärfiler och länkas till Tal-SDK. I stället måste ett omslagsbibliotek som innehåller dessa funktioner byggas och levereras med apparna med SDK.

Om du vill skapa det här omslagsbiblioteket hämtar och installerar du [först GStreamer SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Hämta sedan **Xcode-projektet** för [omslagsbiblioteket](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Öppna projektet i **Xcode** och bygg det för **generic iOS-enhetsmålet** – det kommer *inte* att fungera att bygga det för ett specifikt mål.

Byggsteget genererar ett dynamiskt rampaket med ett dynamiskt bibliotek `GStreamerWrapper.framework`för alla nödvändiga arkitekturer med namnet .

Det här ramverket måste inkluderas i alla appar som använder komprimerade ljudströmmar med taltjänsten SDK.

Använd följande inställningar i **Xcode-projektet** för att åstadkomma detta:

1. Kopiera `GStreamerWrapper.framework` dig nyss byggt och ramen för Cognitive Services Speech SDK, som du kan ladda ner [härifrån](https://aka.ms/csspeech/iosbinary), till katalogen som innehåller ditt exempelprojekt.
1. Justera banorna till ramverken i *projektinställningarna*.
   1. På fliken **Allmänt** under rubriken **Inbäddade binärfiler** lägger du till SDK-biblioteket som ett ramverk: **Lägg till inbäddade binärfiler** > **Lägg till andra...** > Navigera i den katalog du valde och välj båda ramverken.
   1. Gå till fliken **Build Settings** (Versionsinställningar) och aktivera **All** (Alla).
1. Lägg till katalogen `$(SRCROOT)/..` i _Framework Search Paths_ (Sökvägar för ramverket) under rubriken **Search Paths** (Sökvägar).
