---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80409658"
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
