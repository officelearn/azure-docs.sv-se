---
title: Mallar
description: Det här avsnittet beskriver mallar för Azure notification hubs.
services: notification-hubs
documentationcenter: .net
author: ysxu
manager: erikre
editor: ''
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
ms.openlocfilehash: 1ca24a4bf08ecdbe1c1e47a931613144309a04a9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="templates"></a>Mallar
## <a name="overview"></a>Översikt
Mallar kan ett klientprogram att ange det exakta formatet för den vill ta emot meddelanden. Med hjälp av mallar, Tänk en app flera olika fördelar, inklusive följande:

* En plattformsoberoende serverdel
* Anpassade meddelanden
* Klientversionen oberoende
* Enkelt lokalisering

Det här avsnittet innehåller två ingående exempel på hur du använder mallar att skicka plattformsoberoende meddelanden målobjekt för alla enheter på plattformar och för att anpassa broadcast-meddelanden för varje enhet.

## <a name="using-templates-cross-platform"></a>Med hjälp av mallar plattformar
Standardmetoden för att skicka push-meddelanden är att skicka för varje meddelande som ska skickas, en specifik nyttolast till platform notification services (WNS, APN). Om du vill skicka en avisering till APN är till exempel nyttolasten ett Json-objekt av följande format:

    {"aps": {"alert" : "Hello!" }}

Om du vill skicka ett liknande popup-meddelande på en Windows Store-programmet är nyttolasten i XML:

    <toast>
      <visual>
        <binding template=\"ToastText01\">
          <text id=\"1\">Hello!</text>
        </binding>
      </visual>
    </toast>

Du kan skapa liknande nyttolaster för MPNS (Windows Phone) och (Android) GCM-plattformar.

Det här kravet tvingar appens serverdel för att skapa olika nyttolaster för varje plattform och blir serverdelen som ansvarar för en del av presentation lager av appen. Vissa problem är lokalisering och grafiska layouter (särskilt för Windows Store-appar som innehåller meddelanden för olika typer av paneler).

Notification Hubs mall-funktionen kan ett klientprogram att skapa särskilda registreringar som kallas mall registreringar, bland annat, utöver uppsättningen taggar, en mall. Notification Hubs mall-funktionen kan ett klientprogram att associera enheter med mallar om du arbetar med installationer (rekommenderas) eller registreringar. Nyttolasten i föregående exempel är, endast plattformsoberoende information den faktiska varning (Hej!). En mall är en uppsättning instruktioner för Meddelandehubben om hur du formaterar meddelandet plattformsoberoende för registrering av den specifika klient-app. I föregående exempel plattform oberoende meddelandet är en enskild egenskap: **message = Hej!**.

Följande bild illustrerar processen för ovan:

![](./media/notification-hubs-templates/notification-hubs-hello.png)

Mallen för iOS-app klientregistrering är följande:

    {"aps": {"alert": "$(message)"}}

Motsvarande mallen för Windows Store-klientappen är:

    <toast>
        <visual>
            <binding template=\"ToastText01\">
                <text id=\"1\">$(message)</text>
            </binding>
        </visual>
    </toast>

Observera att det faktiska meddelandet ersätts av uttrycket $(meddelande). Det här uttrycket instruerar Meddelandehubben, när den skickar ett meddelande till den här viss registreringen att skapa ett meddelande som följer den och växlar i vanliga värdet.

Om du arbetar med installationsmodell innehåller nyckeln ”mallar” installationen en JSON över flera mallar. Om du arbetar med registrering modell kan klientprogrammet skapa flera registreringar för att kunna använda flera mallar. till exempel uppdateringar en mall för aviseringar och en mall för bricka. Klientprogram kan också blanda interna registreringar (registreringar utan någon mall) och mallen registreringar.

Notification Hub skickar en avisering för varje mall utan att överväga om de hör till klientappen med samma. Det här beteendet kan användas för att översätta plattformsoberoende meddelanden till flera meddelanden. Till exempel kan samma plattform oberoende meddelande Notification Hub sömlöst översättas i ett popup-meddelande och en sida vid sida-uppdatering utan backend ska vara medvetna om det. Observera att vissa plattformar (till exempel iOS) kan komprimera flera meddelanden till samma enhet om de skickas i en kort tidsperiod.

## <a name="using-templates-for-personalization"></a>Med hjälp av mallar för anpassning
En annan fördel med att använda mallar är möjligheten att använda Notification Hubs för att utföra per registrering anpassningar av meddelanden. Anta till exempel att en väder-app som visar en panel med väder villkor på en viss plats. En användare kan välja mellan Celsius eller Fahrenheit grader och en enkel eller fem dagar prognos. Med hjälp av mallar, kan varje app klientinstallation registreras för det format som krävs (1 dag Celsius, 1 dag Fahrenheit, 5 dagar Celsius 5 dagar Fahrenheit), och skicka ett enda meddelande som innehåller den information som krävs för att fylla mallarna serverdelen (till exempel fem dagar vid en prognos med Celsius och Fahrenheit grader).

Mall för en dag prognosen med Celsius temperaturer är följande:

    <tile>
      <visual>
        <binding template="TileWideSmallImageAndText04">
          <image id="1" src="$(day1_image)" alt="alt text"/>
          <text id="1">Seattle, WA</text>
          <text id="2">$(day1_tempC)</text>
        </binding>  
      </visual>
    </tile>

Meddelandet som skickas till Meddelandehubben innehåller följande egenskaper:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Med hjälp av det här mönstret skickas serverdelen endast ett meddelande utan att behöva lagra specifika anpassningsalternativ för appanvändare. Följande bild visar det här scenariot:

![](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Så här registrerar du mallar
Om du vill registrera med mallar med hjälp av installationsmodell (rekommenderas) eller registrering av modellen, se [registrering Management](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Språk för malluttryck
Mallar är begränsade till XML- eller JSON-dokumentformat. Du kan också bara placera uttryck på särskilda platser. till exempel noden attribut eller värden för XML, sträng egenskapsvärden för JSON.

I följande tabell visas det språk som tillåts i mallar:

| Uttryck | Beskrivning |
| --- | --- |
| $(prop) |Referens till en händelseegenskap med det angivna namnet. Egenskapsnamn är inte skiftlägeskänsliga. Det här uttrycket löser i egenskapsvärdet text eller en tom sträng om egenskapen inte finns. |
| $(prop, n) |Som ovan, men texten är uttryckligen bryts vid n tecken, till exempel $(rubrik 20) klipp innehållet i egenskapen Rubrik på 20 tecken. |
| . (prop, n) |Som ovan, men texten suffixet med tre punkter som den klipps. Den totala storleken på den förkortade strängen och suffixet överstiger inte n tecken. . (rubrik, 20) med en inkommande egenskap ”är rubriken” resultaten i **detta är titeln...** |
| %(prop) |Liknar $(name) förutom att utdata URI-kodad. |
| #(prop) |Används i JSON-mallar (t.ex, för iOS och Android mallar).<br><br>Den här funktionen fungerar exakt samma sätt som $(prop) tidigare angiven förutom när det används i JSON-mallar (till exempel Apple mallar). I det här fallet, om den här funktionen inte är omgiven av ”{” ”,}” (till exempel 'myJsonProperty': '#(namn)'), och utvärderas till ett tal i Javascript-format, till exempel regexp: (0&#124;(&#91;1 – 9&#93;&#91;0-9&#93;*)) (\. &#91;0-9&#93;+)? ((e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, och sedan utdata-JSON är ett tal.<br><br>Till exempel ' Aktivitetsikon: ”#(namn)” blir ”badge': 40 (och inte” 40'). |
| ”text” eller ”text” |En literal. Literaler innehålla godtycklig text inom enkla eller dubbla citattecken. |
| Uttr1 + uttr2 |Operatorn sammanfogning koppla två uttryck till en sträng. |

Uttrycken kan vara något av de föregående formulär.

När du använder sammanfogning måste helt uttryck omges av {}. Till exempel {$(prop) + '-' + $(prop2)}. |

Till exempel är följande inte en giltig XML-mall:

    <tile>
      <visual>
        <binding $(property)>
          <text id="1">Seattle, WA</text>
        </binding>  
      </visual>
    </tile>


Som beskrivs ovan, när du använder sammanfogning, måste uttryck omges av klammerparenteser. Exempel:

    <tile>
      <visual>
        <binding template="ToastText01">
          <text id="1">{'Hi, ' + $(name)}</text>
        </binding>  
      </visual>
    </tile>

