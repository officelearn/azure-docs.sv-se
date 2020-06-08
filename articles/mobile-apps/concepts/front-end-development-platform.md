---
title: Välj plattforms oberoende utvecklings plattform för att skapa klient program med Visual Studio och Azure-tjänster
description: Lär dig mer om de inbyggda och plattforms oberoende språken som stöds för att bygga klient program.
author: codemillmatt
ms.service: mobile-services
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 06/05/2020
ms.author: masoucou
ms.openlocfilehash: 7f31c89924ac6c3752a3916987c8aba027641deb
ms.sourcegitcommit: f57fa5f3ce40647eda93f8be4b0ab0726d479bca
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/07/2020
ms.locfileid: "84482658"
---
# <a name="choose-mobile-development-frameworks"></a>Välj ramverk för mobil utveckling
Utvecklare kan använda tekniker på klient sidan för att bygga mobilappar själva genom att använda specifika ramverk och mönster för en plattforms oberoende metod. Utvecklare kan bygga på grund av sina besluts faktorer:
- Inbyggda program med en enda plattform med hjälp av språk som mål C och Java
- Plattforms oberoende program med hjälp av Xamarin, .NET och C #
- Hybrid program med hjälp av Cordova och dess varianter

## <a name="native-platforms"></a>Inbyggda plattformar
Att skapa ett internt program kräver plattformsspecifika programmeringsspråk, SDK: er, utvecklings miljöer och andra verktyg som tillhandahålls av OS-leverantörer.

### <a name="ios"></a>iOS
IOS har skapats och utvecklats av Apple och används för att skapa appar på Apple-enheter, nämligen iPhone och iPad.

- **Programmeringsspråk**: mål-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Android har utformats av Google och de mest populära operativ systemen i världen, och används för att skapa program som kan köras på en rad olika smartphones och surfplattor.

- **Programmeringsspråk**: Java, Kotlin 
- **IDE**: Android Studio och Android-utvecklingsverktyg 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Programmeringsspråk**: C #
- **IDE**: Visual Studio, Visual Studio Code
- **SDK**: Windows SDK

#### <a name="pros"></a>Fördelar
- Utmärkt användar upplevelse
- Program som svarar med hög prestanda och möjlighet att samverka med interna bibliotek
- Hög säkra program

#### <a name="cons"></a>Nackdelar
- Programmet körs bara på en plattform
- Fler utvecklares resurser intensivt och kostsamt för att bygga ett program
- Lägre kod åter användning

## <a name="cross-platforms-and-hybrid-applications"></a>Plattformar och hybrid program
Program över plattformar ger dig möjlighet att skriva interna mobila program en gång, dela kod och köra dem på iOS, Android och Windows.

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/) som ägs av Microsoft, används för att bygga robusta mobila program mellan plattformar i C#. Xamarin har ett klass bibliotek och en körning som fungerar på flera plattformar, t. ex. iOS, Android och Windows. Den sammanställer även inbyggda (icke-tolkade) program som ger hög prestanda. Xamarin kombinerar alla förmågor för de ursprungliga plattformarna och lägger till ett antal kraftfulla funktioner.

- **Programmeringsspråk**: C #
- **IDE**: Visual Studio på Windows eller Mac

### <a name="react-native"></a>Reagera inbyggd
Från Facebook i 2015 är [reagera inbyggd](https://facebook.github.io/react-native/) som ett JavaScript [-ramverk med öppen källkod](https://github.com/facebook/react-native) för att skriva verkliga, internt åter givning av mobil program för iOS och Android. Den baseras på reagerar, Facebook: s JavaScript-bibliotek för att skapa användar gränssnitt. I stället för att rikta in webbläsaren, är den riktad mot mobila plattformar. Reagera inbyggda använder inbyggda komponenter i stället för webb komponenter som bygg stenar.
 
- **Programmeringsspråk**: Java Script
- **IDE**: Visual Studio Code

### <a name="unity"></a>Unity
 Unity är en motor som är optimerad för att skapa spel. Du kan använda den för att hantverka 2D-eller 3D-program med hög kvalitet med C# för plattformar som Windows, iOS, Android och Xbox.

### <a name="cordova"></a>Cordova
Med Cordova kan du bygga hybrid program med hjälp av Visual Studio Tools för Apache Cordova eller Visual Studio Code med tillägg för Cordova. Med hybrid metoden kan du dela komponenter med webbplatser och återanvända webb serverbaserade program med värdbaserade webb programs metoder baserade på Cordova.

#### <a name="pros"></a>Fördelar
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
