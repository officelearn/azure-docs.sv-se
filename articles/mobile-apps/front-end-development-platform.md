---
title: Välj plattforms oberoende utvecklings plattform för att skapa klient program med Visual Studio och Azure-tjänster
description: Lär dig mer om de inbyggda och plattforms oberoende språken som stöds för att bygga klient program.
author: elamalani
manager: elamalani
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 891967e74e52c1311d73cbe13fff8c95e479478c
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/23/2019
ms.locfileid: "72795502"
---
# <a name="choose-mobile-development-framework"></a>Välj ramverk för mobil utveckling
Utvecklare kan använda tekniker på klient sidan för att bygga mobil program själva, med specifika ramverk och mönster för en plattforms oberoende metod. Utvecklare kan bygga interna (interna plattformar med hjälp av språk som mål-C-och Java-program), plattforms oberoende program som använder Xamarin, .NET C#och hybrider (med Cordova) och dess varianter, beroende på deras beslut förhållanden.

## <a name="native-platforms"></a>Inbyggda plattformar
Att skapa ett internt program kräver plattformsspecifika programmeringsspråk, SDK: er, utvecklings miljö och andra verktyg som tillhandahålls av OS-leverantörer.

### <a name="ios"></a>iOS
Som skapats och utvecklats av Apple körs appar som bygger på iOS på Apple-enheter, nämligen iPhone och iPad.

- **Programmeringsspråk** – mål-C, Swift
- **IDE** -Xcode
- **SDK** – iOS SDK

### <a name="android"></a>Android
Program som bygger på Android och som skapats av Google och de mest populära operativ systemen i världen kan köras på en rad olika smartphones och surfplattor.

- **Programmeringsspråk** – Java, Kotlin 
- **IDE** -Android Studio och Android-utvecklarverktyg 
- **SDK** – Android SDK

### <a name="windows"></a>Windows
- **Programmeringsspråk** –C#
- **IDE** – Visual Studio, Visual Studio Code
- **SDK** – Windows SDK

#### <a name="pros"></a>Proffs
- Utmärkt användar upplevelse
- Program som svarar med hög prestanda och möjlighet att samverka med interna bibliotek
- Hög säkra program

#### <a name="cons"></a>Nackdelar
- Programmet körs bara på en plattform
- Fler utvecklares resurser och dyra att bygga ett program
- Lägre kod åter användning

## <a name="cross-platforms-and-hybrid-applications"></a>Plattformar och hybrid program
Med plattforms oberoende program får du möjlighet att skriva interna mobila program en gång, dela kod och köra dem på iOS, Android och Windows.

### <a name="xamarin"></a>Xamarin
Som ägs av Microsoft gör [Xamarin](https://visualstudio.microsoft.com/xamarin/) att du kan bygga robusta, plattforms oberoende C#mobila program i, med ett klass bibliotek och en körnings miljö som fungerar på flera plattformar, inklusive iOS, Android och Windows, samtidigt som du kompilerar inbyggda (icke-tolkade ) program som är mycket presterande. Xamarin kombinerar alla förmågor för de ursprungliga plattformarna och lägger till ett antal kraftfulla funktioner.

- **Programmeringsspråk** –C#
- **IDE** – Visual Studio på Windows eller Mac

### <a name="react-native"></a>React Native
Från Facebook i 2015 är [reagera inbyggd](https://facebook.github.io/react-native/) som ett JavaScript-ramverk med [öppen källkod](https://github.com/facebook/react-native) för att skriva verkliga, internt åter givning av mobil program för iOS och Android. Den baseras på reagerar, Facebook: s JavaScript-bibliotek för att skapa användar gränssnitt, men i stället för att använda webbläsaren, är den riktad till mobila plattformar. Reagera inbyggda använder inbyggda komponenter i stället för webb komponenter som bygg stenar.
 
- **Programmeringsspråk** – Java Script
- **IDE** -Visual Studio-kod

### <a name="unity"></a>Unity
 Unity är en motor som är optimerad för att skapa spel som gör det möjligt för utvecklare att skapa 2D C# /3D-program med hög kvalitet med för olika plattformar, inklusive Windows, iOS, Android och Xbox.

### <a name="cordova"></a>Cordova
Med Cordova kan du bygga hybrid program med Visual Studio-verktyg för Apache Codova eller Visual Studio Code med tillägg för Cordova. Med hybrid metoden kan du dela komponenter med webbplatser och återanvända webb serverbaserade program med värdbaserade webb programs metoder baserade på Cordova.

#### <a name="pros"></a>Proffs
- Ökad kod användbarhet genom att skapa en kodbas för flera plattformar
- Rikta till en bredare publik på flera plattformar
- Dramatisk minskning i utvecklings tiden
- Lätt att starta och uppdatera

#### <a name="cons"></a>Nackdelar
- Lägre prestanda
- Brist på flexibilitet
- Varje plattform har en unik uppsättning funktioner och funktioner för att göra det inbyggda programmet mer kreativt
- Design tid för ökat gränssnitt
- Verktygs begränsning
