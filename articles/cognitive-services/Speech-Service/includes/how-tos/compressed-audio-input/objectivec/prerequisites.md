---
author: IEvangelist
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: dapine
ms.openlocfilehash: 28f6ef7248fada8286bbe90c868cd9f947f0435d
ms.sourcegitcommit: 8f4d54218f9b3dccc2a701ffcacf608bbcd393a6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2020
ms.locfileid: "78943858"
---
Hantering av komprimerat ljud implementeras med [gstreamer](https://gstreamer.freedesktop.org). Av licens skäl GStreamer binärfiler inte kompileras och länkas till tal-SDK: n. I stället måste ett omslutnings bibliotek som innehåller dessa funktioner skapas och levereras med apparna med hjälp av SDK.

För att bygga det här omslutnings biblioteket måste du först ladda ned och installera [GSTREAMER SDK](https://gstreamer.freedesktop.org/data/pkg/ios/1.16.0/gstreamer-1.0-devel-1.16.0-ios-universal.pkg). Hämta sedan **Xcode** -projektet för [omslutnings biblioteket](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/objective-c/ios/compressed-streams/GStreamerWrapper).

Öppna projektet i **Xcode** och skapa det för det **allmänna iOS-enhetens** mål – det fungerar *inte* för att skapa det för ett särskilt mål.

Build-steget genererar ett dynamiskt Framework-paket med ett dynamiskt bibliotek för alla nödvändiga arkitekturer med namnet på `GStreamerWrapper.framework`.

Det här ramverket måste inkluderas i alla appar som använder komprimerade ljud strömmar med Speech service SDK.

Använd följande inställningar i ditt **Xcode** -projekt för att göra detta:

1. Kopiera `GStreamerWrapper.framework` som du precis skapat och ramverket för Cognitive Services talet SDK, som du kan ladda ned härifrån [, till](https://aka.ms/csspeech/iosbinary)katalogen som innehåller ditt exempel projekt.
1. Justera Sök vägarna till ramverken i *projekt inställningarna*.
   1. På fliken **Allmänt** under rubriken **inbäddade binärfiler** lägger du till SDK-biblioteket som ett ramverk: **lägg till inbäddade binärfiler** > **Lägg till andra...** > navigera till den katalog som du har valt och välj båda ramverken.
   1. Gå till fliken **Build Settings** (Versionsinställningar) och aktivera **All** (Alla).
1. Lägg till katalogen `$(SRCROOT)/..` i _Framework Search Paths_ (Sökvägar för ramverket) under rubriken **Search Paths** (Sökvägar).
