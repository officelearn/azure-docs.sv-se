---
title: Mallar
description: Det här avsnittet beskriver mallar för Azure notification hub.
services: notification-hubs
documentationcenter: .net
author: jwargo
manager: patniko
editor: spelluru
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: jowargo
ms.openlocfilehash: 02473eb5649c7d201b6a54fd57faea997c1a21cc
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54450242"
---
# <a name="templates"></a>Mallar

Med mallar kan ett klientprogram att ange det exakta formatet för den vill ta emot meddelanden. Med hjälp av mallar, kan en app spara flera olika fördelar, inklusive följande:

- En plattformsagnostiska-serverdel
- Anpassade meddelanden
- Oberoende klient-version
- Enkelt lokalisering

Det här avsnittet innehåller två djupgående exempel på hur du använder mallar att skicka plattformsoberoende meddelanden på alla enheter på plattformar och för att anpassa broadcast-meddelande till varje enhet.

## <a name="using-templates-cross-platform"></a>Med hjälp av mallar plattformsoberoende

Standardmetoden för att skicka push-meddelanden är att skicka för varje meddelande som ska skickas, en specifik nyttolast till plattformarnas meddelandetjänster (WNS, APNS). Om du vill skicka en avisering till APNS, är exempelvis nyttolasten ett JSON-objekt i följande format:

```json
{"aps": {"alert" : "Hello!" }}
```

Om du vill skicka ett liknande popup-meddelande på ett Windows Store-program, är XML-nyttolasten följande:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Du kan skapa liknande nyttolaster för MPNS (Windows Phone) och FCM (Android) plattformar.

Det här kravet tvingar appserverdelen att skapa olika nyttolaster för varje plattform och gör effektivt serverdelen ansvarig för en del av presentationslagret av appen. Vissa frågor är lokalisering och grafiska layouter (särskilt för Windows Store-appar som innehåller meddelanden för olika typer av paneler).

Med Meddelandehubbar mall-funktionen kan ett klientprogram att skapa särskilda registreringar som kallas mallregistreringar, bland annat, förutom uppsättningen taggar, en mall. Med Meddelandehubbar mall-funktionen kan ett klientprogram att associera enheter med mallar, oavsett om du arbetar med installationer (rekommenderas) eller registreringar. Med föregående exempel nyttolasten kan är informationen bara plattformsoberoende den faktiska varning (Hej!). En mall är en uppsättning instruktioner för Meddelandehubben om hur du formaterar ett plattformsoberoende meddelande för registreringen av den specifika klient-appen. I föregående exempel plattformsoberoende meddelandet är en enskild egenskap: `message = Hello!`.

Följande bild visar processen:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Mallen för iOS-app klientregistrering är följande:

```json
{"aps": {"alert": "$(message)"}}
```

Motsvarande mallen för Windows Store-klientappen är:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Observera att det faktiska meddelandet ersätts för uttrycket $(meddelande). Det här uttrycket instruerar Meddelandehubben, när du skickar den ett meddelande till den här specifika registreringen att skapa ett meddelande som följer det och växlar i vanliga värdet.

Om du arbetar med installationsmodell innehåller ”mallar” installationsnyckel en JSON över flera mallar. Om du arbetar med registrering modell kan klientprogrammet skapa flera registreringar för att kunna använda flera mallar. till exempel uppdaterar en mall för aviseringsmeddelanden och en mall för panel. Klientprogram kan även blanda interna registreringar (registreringar med ingen mall) och mallregistreringar.

Notification Hub skickar ett meddelande för varje mall utan att överväga om de tillhör samma klientappen. Det här beteendet kan användas för att översätta plattformsoberoende meddelanden till flera meddelanden. Till exempel kan samma plattformsoberoende meddelande till Notification Hub sömlöst översättas i en popup-avisering och en tile-uppdatering utan serverdelen känna till den. Vissa plattformar (till exempel iOS) kan komprimera flera meddelanden till samma enhet om de skickas i en kort tidsperiod.

## <a name="using-templates-for-personalization"></a>Med hjälp av mallar för anpassning

En annan fördel med att använda mallar är möjligheten att använda Meddelandehubbar för att utföra per registrering anpassning av meddelanden. Anta exempelvis att en väder-app som visar en panel med vädret på en viss plats. En användare kan välja mellan grader Celsius eller Fahrenheit och en enkel eller fem dagar prognos. Med hjälp av mallar, kan varje klientinstallation för appen registreras för det format som krävs (1 dag Celsius, 1-dags Fahrenheit, 5 dagar Celsius, 5 dagar Fahrenheit), och har serverdelen skicka ett enda meddelande som innehåller den information som krävs för att fylla dessa mallar (till exempel vara en femdagars gör prognoser i grader Celsius och Fahrenheit).

Mallen för prognosen endagsevenemang med Celsius temperaturer är följande:

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

Meddelandet som skickas till Meddelandehubben innehåller följande egenskaper:

```html
<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>
```

Genom att använda det här mönstret kan skickas serverdelen endast ett enskilt meddelande utan att behöva lagra specifika anpassningsalternativ för app-användare. Följande bild visar det här scenariot:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Så här registrerar du mallar

Om du vill registrera med mallar med installationsmodell (rekommenderas) eller registrering av modellen, se [Registreringshantering](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Språk för malluttryck

Mallar är begränsade till XML eller JSON-dokument-format. Du kan också bara placera uttryck på specifika platser. till exempel noden attribut eller värden för XML, sträng egenskapsvärden för JSON.

I följande tabell visas de språk som tillåts i mallar:

| Uttryck       | Beskrivning |
| ---------------- | --- |
| $(prop)          | Referens till en händelseegenskap med det angivna namnet. Egenskapsnamn är inte skiftlägeskänsliga. Det här uttrycket matchas till egenskapsvärdet text eller till en tom sträng om egenskapen inte finns. |
| $(prop, n)       | Som ovan, men texten är uttryckligen klipps n tecken, till exempel $(rubrik, 20) klipp innehållet i egenskapen Rubrik på 20 tecken. |
| . (prop, n)       | Som ovan, men texten suffix med tre punkter som den beskärs. Den totala storleken på förkortade strängen och suffixet överstiger inte n tecken. . (rubrik, 20) med en indata-egenskap av ”är rubriken” resulterar i **detta är titeln...** |
| %(prop)          | Liknar $(name) förutom att utdata är URI-kodad. |
| #(prop)          | Används i JSON-mallar (till exempel för iOS och Android-mallar).<br><br>Den här funktionen fungerar likadant som $(prop) tidigare angiven, förutom när det används i JSON-mallar (till exempel Apple-mallar). I det här fallet, om den här funktionen inte omges av ”{', '}” (till exempel ”myJsonProperty”: ”#(namn)”), och det resulterar i ett tal i Javascript-format, till exempel regexp: (0&#124;(&#91;1 – 9&#93;&#91;0-9&#93;*)) (\.&#91;0-9&#93;+)? ((e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, och sedan utdata JSON är ett tal.<br><br>Till exempel ”märket: '#(namn)' blir 'ge en skylt”: 40 (och inte 40). |
| ”text” eller ”text” | En literal. Litteraler innehåller godtyckliga text mellan enkla eller dubbla citattecken. |
| Uttr1 + uttr2    | Operator för strängsammanfogning koppla två uttryck i en sträng. |

Uttryck som kan vara något av de föregående formulär.

När du använder sammanfogning hela uttrycket måste omges av `{}`. Till exempel `{$(prop) + ‘ - ’ + $(prop2)}`.

Till exempel är följande mall inte en giltig XML-mall:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Enligt beskrivningen tidigare, när du använder sammanfogning, måste uttryck omges av klammerparenteser. Exempel:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```
