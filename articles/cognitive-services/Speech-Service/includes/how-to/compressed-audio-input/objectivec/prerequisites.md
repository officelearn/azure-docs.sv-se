---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 7106e139108681e1908b20d2daac5e619a63555d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421876"
---
Hantering av komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). Av licens skäl GStreamer binärfiler inte kompileras och länkas till tal-SDK: n. I stället måste ett omslutnings bibliotek som innehåller dessa funktioner skapas och levereras med apparna med hjälp av SDK.

För att bygga det här omslutnings biblioteket måste du först ladda ned och installera [GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Hämta sedan **Xcode** -projektet för [omslutnings biblioteket](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Öppna projektet i **Xcode** och skapa det för det **allmänna iOS-enhetens** mål – det fungerar *inte* för att skapa det för ett särskilt mål.

Build-steget genererar ett dynamiskt Framework-paket med ett dynamiskt bibliotek för alla nödvändiga arkitekturer med namnet `GStreamerWrapper.framework`.

Det här ramverket måste inkluderas i alla appar som använder komprimerade ljud strömmar med Speech service SDK.

Använd följande inställningar i ditt **Xcode** -projekt för att göra detta:

1. Kopiera det `GStreamerWrapper.framework` du precis skapade och ramverket för COGNITIVE Services talet SDK, som du kan ladda ned härifrån [, till](https://aka.ms/csspeech/iosbinary)katalogen som innehåller ditt exempel projekt.
1. Justera Sök vägarna till ramverken i *projekt inställningarna*.
   1. På fliken **Allmänt** under rubriken **inbäddade binärfiler** lägger du till SDK-biblioteket som ett ramverk: **Lägg till inbäddade binärfiler** > **Lägg till annan...** > navigera till den katalog som du har valt och välja båda ramverken.
   1. Gå till fliken **Build Settings** (Versionsinställningar) och aktivera **All** (Alla).
1. Lägg till katalogen `$(SRCROOT)/..` i _Framework Search Paths_ (Sökvägar för ramverket) under rubriken **Search Paths** (Sökvägar).
