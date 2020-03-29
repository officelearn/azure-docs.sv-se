---
title: Mallar för Azure Notification Hubs
description: Läs mer om hur du använder mallar för Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 7d88f57fe92b9da62cc9f90d64bdec4c27642fb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76263752"
---
# <a name="templates"></a>Mallar

Mallar gör det möjligt för ett klientprogram att ange det exakta formatet för de meddelanden som det vill ta emot. Med hjälp av mallar kan en app dra nytta av flera olika fördelar, bland annat följande:

- En plattform-agnostisk backend
- Anpassade meddelanden
- Oberoende i klientversionen
- Enkel lokalisering

Det här avsnittet innehåller två djupgående exempel på hur du använder mallar för att skicka plattformsoberoende meddelanden som riktar sig till alla dina enheter på alla plattformar och för att anpassa broadcast-meddelanden till varje enhet.

## <a name="using-templates-cross-platform"></a>Använda mallar plattformsoberoende

Standardsättet att skicka push-meddelanden är att för varje meddelande som ska skickas skicka en specifik nyttolast till plattformsmeddelandetjänster (WNS, APNS). Om du till exempel vill skicka en avisering till APNS är nyttolasten ett JSON-objekt i följande formulär:

```json
{"aps": {"alert" : "Hello!" }}
```

Om du vill skicka ett liknande popup-meddelande i ett Windows Store-program är XML-nyttolasten följande:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Du kan skapa liknande nyttolaster för MPNS-plattformar (Windows Phone) och FCM (Android).

Detta krav tvingar appens backend att producera olika nyttolaster för varje plattform och gör effektivt backend ansvarig för en del av presentationen lagret av appen. Några problem är lokalisering och grafiska layouter (särskilt för Windows Store-appar som innehåller meddelanden för olika typer av paneler).

Mallfunktionen Notification Hubs gör det möjligt för en klientapp att skapa särskilda registreringar, så kallade mallregistreringar, som förutom uppsättningen taggar innehåller en mall. Mallfunktionen Notification Hubs gör det möjligt för en klientapp att associera enheter med mallar oavsett om du arbetar med installationer (föredras) eller Registreringar. Med tanke på de föregående nyttolastexemplen är den enda plattformsoberoende informationen det faktiska varningsmeddelandet (Hello!). En mall är en uppsättning instruktioner för meddelandehubben om hur du formaterar ett plattformsoberoende meddelande för registrering av den specifika klientappen. I föregående exempel är det plattformsoberoende meddelandet `message = Hello!`en enda egenskap: .

Följande bild illustrerar processen:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Mallen för registrering av iOS-klientappar är följande:

```json
{"aps": {"alert": "$(message)"}}
```

Motsvarande mall för Windows Store-klientappen är:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Observera att det faktiska meddelandet ersätts med uttrycket $(message). Det här uttrycket instruerar meddelandehubben att skapa ett meddelande som följer det och växlar i det gemensamma värdet när det skickar ett meddelande till just den här registreringen.

Om du arbetar med installationsmodellen innehåller installationsnyckeln "mallar" en JSON med flera mallar. Om du arbetar med registreringsmodellen kan klientprogrammet skapa flera registreringar för att kunna använda flera mallar. Till exempel en mall för varningsmeddelanden och en mall för paneluppdateringar. Klientprogram kan också blanda inbyggda registreringar (registreringar utan mall) och mallregistreringar.

Meddelandehubben skickar ett meddelande för varje mall utan att ta hänsyn till om de tillhör samma klientapp. Det här beteendet kan användas för att översätta plattformsoberoende meddelanden till fler meddelanden. Samma plattformsoberoende meddelande till meddelandehubben kan till exempel översättas sömlöst i en popup-avisering och en paneluppdatering, utan att serverda krävs för att vara medveten om den. Vissa plattformar (till exempel iOS) kan komprimera flera meddelanden till samma enhet om de skickas på kort tid.

## <a name="using-templates-for-personalization"></a>Använda mallar för anpassning

En annan fördel med att använda mallar är möjligheten att använda Meddelandehubbar för att utföra anpassning av meddelanden per registrering. Tänk dig till exempel en väderapp som visar en panel med väderförhållandena på en viss plats. En användare kan välja mellan Celsius eller Fahrenheit grader, och en enda eller fem dagars prognos. Med hjälp av mallar kan varje klientappinstallation registrera sig för det format som krävs (1-dagars Celsius, 1-dagars Fahrenheit, 5-dagars Celsius, 5-dagars Fahrenheit) och låta serverdelen skicka ett enda meddelande som innehåller all information som krävs för att fylla dessa mallar (till exempel en femdagarsprognos med Celsius- och Fahrenheitgraderna).

Mallen för endagsprognosen med Celsius temperaturer är följande:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Meddelandet som skickas till meddelandehubben innehåller alla följande egenskaper:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Genom att använda det här mönstret skickar serverdan bara ett enda meddelande utan att behöva lagra specifika anpassningsalternativ för appanvändarna. Följande bild illustrerar det här scenariot:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Så här registrerar du mallar

Information om hur du registrerar dig med mallar med installationsmodellen (föredras) eller registreringsmodellen finns i [Registreringshantering](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Språk för malluttryck

Mallar är begränsade till XML- eller JSON-dokumentformat. Du kan också bara placera uttryck på vissa platser. till exempel nodattribut eller värden för XML, strängegenskapsvärden för JSON.

I följande tabell visas det språk som tillåts i mallar:

| Uttryck       | Beskrivning |
| ---------------- | --- |
| $(prop)          | Referens till en händelseegenskap med förnamnet. Egenskapsnamn är inte skiftlägeskänsliga. Det här uttrycket matchas i egenskapens textvärde eller till en tom sträng om egenskapen inte finns. |
| $(prop, n)       | Som ovan, men texten är uttryckligen klippt på n tecken, till exempel $(titel, 20) klipper innehållet i egenskapen title på 20 tecken. |
| . (prop, n)       | Som ovan, men texten är suffixed med tre punkter som det klipps. Den totala storleken på den klippta strängen och suffixet överstiger inte n tecken. . (titel, 20) med en indataegenskap "Det här är rubrikraden" resulterar i **Detta är titeln...** |
| %(prop)          | Liknar $(name) förutom att utdata är URI-kodad. |
| #(prop)          | Används i JSON-mallar (till exempel för iOS- och Android-mallar).<br><br>Den här funktionen fungerar exakt på samma sätt som $(prop) som tidigare angetts, utom när den används i JSON-mallar (till exempel Apple-mallar). I det här fallet om den här funktionen inte omges av "{','}" (till exempel 'myJsonProperty': '#(name)'), och den utvärderas till ett tal i Javascript-format, till exempel regexp: (0&#124;(&#91;1-9&#93;&#91;0-9&#93;*))(\.&#91;0-9&#93;+)? ((e&#124;E)(+&#124;-)?&#91;0-9&#93;+)?, då produktionen JSON är ett tal.<br><br>Till exempel blir "badge: #(name)" "badge" : 40 (och inte "40"). |
| "text" eller "text" | En bokstavlig. Litteraler innehåller godtycklig text som omges av enkla eller dubbla citattecken. |
| uttr1 + uttr2    | Sammanfogningsoperatorn sammanfogar två uttryck till en enda sträng. |

Uttrycken kan vara något av de föregående formerna.

När du använder sammanfogning måste hela `{}`uttrycket omges av . Till exempel `{$(prop) + ‘ - ’ + $(prop2)}`.

Följande mall är till exempel inte en giltig XML-mall:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Som tidigare förklarats, när du använder sammanfogning, måste uttrycken vara insvepta i lockiga parenteser. Ett exempel:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Nästa steg

[Läs mer om Azure Notification Hubs](notification-hubs-push-notification-overview.md)