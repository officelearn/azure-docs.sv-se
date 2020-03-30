---
title: Välj den främre utvecklingsplattformen för att skapa klientprogram med Visual Studio- och Azure-tjänster
description: Lär dig mer om de språk som stöds för att skapa klientprogram.
author: codemillmatt
ms.service: vs-appcenter
ms.assetid: 355f0959-aa7f-472c-a6c7-9eecea3a34b9
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 714916c755ce50f6e596cee4be85ceb0b142c789
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240865"
---
# <a name="choose-mobile-development-frameworks"></a>Välj ramverk för mobil utveckling
Utvecklare kan använda tekniker på klientsidan för att själva skapa mobila applikationer med hjälp av specifika ramverk och mönster för en plattformsoberoende metod. Baserat på deras beslutsfaktorer kan utvecklare bygga:
- Inbyggda enplattformsprogram med hjälp av språk som mål C och Java
- Plattformsoberoende program med hjälp av Xamarin, .NET och C #
- Hybridapplikationer med Cordova och dess varianter

## <a name="native-platforms"></a>Inbyggda plattformar
För att skapa ett inbyggt program krävs plattformsspecifika programmeringsspråk, SDK:er, utvecklingsmiljöer och andra verktyg som tillhandahålls av OS-leverantörer.

### <a name="ios"></a>iOS
Skapad och utvecklad av Apple, iOS används för att bygga appar på Apple-enheter, nämligen iPhone och iPad.

- **Programmeringsspråk**: Objective-C, Swift
- **IDE**: Xcode
- **SDK**: iOS SDK

### <a name="android"></a>Android
Designad av Google och den mest populära OS i världen, Android används för att bygga program som kan köras på en rad smartphones och tabletter.

- **Programmeringsspråk**: Java, Kotlin 
- **IDE**: Android Studio och Android-utvecklarverktyg 
- **SDK**: Android SDK

### <a name="windows"></a>Windows
- **Programmeringsspråk**: C #
- **IDE**: Visual Studio, Visual Studio-kod
- **SDK**: Windows SDK

#### <a name="pros"></a>Fördelar
- Bra användarupplevelse
- Responsiva program med hög prestanda och förmåga att samverka med inbyggda bibliotek
- Mycket säkra program

#### <a name="cons"></a>Nackdelar
- Programmet körs på endast en plattform
- Mer utvecklare resurskrävande och dyrt att bygga ett program
- Lägre återanvändning av kod

## <a name="cross-platforms-and-hybrid-applications"></a>Plattformsoberoende och hybridapplikationer
Plattformsoberoende program ger dig möjlighet att skriva inbyggda mobilappar en gång, dela kod och köra dem på iOS, Android och Windows.

### <a name="xamarin"></a>Xamarin
[Xamarin](https://visualstudio.microsoft.com/xamarin/) ägs av Microsoft och används för att bygga robusta mobila applikationer över flera plattformar i C#. Xamarin har ett klassbibliotek och körning som fungerar på många plattformar, till exempel iOS, Android och Windows. Den sammanställer också inbyggda (icke-tolkade) program som ger hög prestanda. Xamarin kombinerar alla förmågor av de inbyggda plattformarna och lägger till ett antal kraftfulla funktioner i sin egen.

- **Programmeringsspråk**: C #
- **IDE**: Visual Studio på Windows eller Mac

### <a name="react-native"></a>Reagera inbyggt
[React Native](https://facebook.github.io/react-native/) släpptes av Facebook 2015 och är ett [JavaScript-ramverk](https://github.com/facebook/react-native) med öppen källkod för att skriva riktiga, inbyggda renderingsmobila applikationer för iOS och Android. Den är baserad på React, Facebooks JavaScript-bibliotek för att bygga användargränssnitt. Istället för att rikta webbläsaren, det riktar mobila plattformar. React Native använder inbyggda komponenter i stället för webbkomponenter som byggstenar.
 
- **Programmeringsspråk**: JavaScript
- **IDE**: Visual Studio-kod

### <a name="unity"></a>Unity
 Unity är en motor optimerad för att skapa spel. Du kan använda den för att skapa högkvalitativa 2D- eller 3D-program med C# för plattformar som Windows, iOS, Android och Xbox.

### <a name="cordova"></a>Cordova
Med Cordova kan du bygga hybridprogram med hjälp av Visual Studio Tools för Apache Cordova eller Visual Studio Code med tillägg för Cordova. Med hybridmetoden kan du dela komponenter med webbplatser och återanvända webbserverbaserade program med värdbaserade webbapplikationsmetoder baserade på Cordova.

#### <a name="pros"></a>Fördelar
- Ökad kod användbarhet genom att skapa en kodbas för flera plattformar
- Tillgodose en bredare publik på många plattformar
- Dramatisk minskning av utvecklingstiden
- Lätt att starta och uppdatera

#### <a name="cons"></a>Nackdelar
- Lägre prestanda
- Brist på flexibilitet
- Varje plattform har en unik uppsättning funktioner för att göra det inbyggda programmet mer kreativt
- Ökad designtid för användargränssnittet
- Begränsning av verktyg
