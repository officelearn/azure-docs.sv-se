---
title: Alternativ för Azure Media Player
description: Azure Media Player-inbäddningskoden är helt enkelt en HTML5-videotagg, så för många av alternativen kan du använda standardtaggattributen för att ange alternativen.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.openlocfilehash: e26215115b4c4484e5e05a2fd94a4d2c6680a4d0
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81727169"
---
# <a name="options"></a>Alternativ #

## <a name="setting-options"></a>Inställningsalternativ ##

Azure Media Player-inbäddningskoden är helt enkelt en HTML5-videotagg, så för många av alternativen kan du använda standardtaggattributen för att ange alternativen.

`<video controls autoplay ...>`

Du kan också använda attributet datainställningar för att ange alternativ i [JSON-formatet.](http://json.org/example.html) Det är också så här du anger alternativ som inte är standard för videotaggen.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Slutligen, om du inte använder attributet datainställningar för att utlösa spelarinställningarna, kan du skicka in ett objekt med spelaralternativen som det andra argumentet i JavaScript-inställningsfunktionen.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Alternativ i konstruktorn anges bara på den första initieringen innan källan ställs in.  Om du vill ändra alternativen för samma initierade Azure Media Player-element måste du uppdatera alternativen innan du ändrar källan. Du kan uppdatera alternativen i `myPlayer.options({/*updated options*/});`JavaScript med hjälp av . Observera att endast ändrade alternativ påverkas, alla andra tidigare inställda alternativ kommer att finnas kvar.

## <a name="individual-options"></a>Individuella alternativ ##

> [!NOTE]
>Video Tag Attribut kan bara vara sant eller falskt (boolean), du helt enkelt inkludera attributet (inga lika tecken) för att slå på den, eller utesluta den för att stänga av den. Till exempel för att aktivera `<video controls="true" ...>` `<video controls ...>` kontroller: FEL RÄTT Det största problemet som personer stöter på är att försöka ange dessa värden till false med falskt som värde (t.ex. kontroller="false") som faktiskt gör det motsatta och anger värdet till true eftersom attributet fortfarande ingår.

### <a name="controls"></a>kontroller ###

Kontrollalternativet anger om spelaren har kontroller som användaren kan interagera med eller inte. Utan kontroller är det enda sättet att starta videouppspelningen med attributet autoplay eller via API:et.

`<video controls ...>` eller `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Om autoplay är sant börjar videon spelas upp så snart sidan läses in (utan interaktion från användaren).

> [!NOTE]
> Det här alternativet stöds inte av mobila enheter som Windows Phone, Apple iOS och Android. Mobila enheter blockerar autoplay-funktionen för att förhindra överanvändning av konsumentens månatliga dataplaner (ofta dyra). En användare touch / klick krävs för att starta videon i detta fall.

`<video autoplay ...>`Eller`{ "autoplay": true }`

### <a name="poster"></a>Affisch ###
Affischattributet anger bilden som visas innan videon börjar spelas upp. Detta är ofta en bildruta i videon eller en anpassad titelskärm. Så fort användaren klickar på spela bilden kommer att försvinna.

`<video poster="myPoster.jpg" ...>` eller `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>bredd ###

Attributet width anger videons visningsbredd.

`<video width="640" ...>` eller `{ "width": 640 }`

### <a name="height"></a>höjd ###

Höjdattributet anger videons visningshöjd.

`<video height="480" ...>` eller `{ "height": 480 }`

### <a name="plugins"></a>Plugins ###

Plugins JSON avgör vilka plugins få laddas med den instansen av AMP kan du konfigurera alla alternativ som plugin kan ha.

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

För mer information om plugin utveckling och användning, se [skriva plugins](azure-media-player-writing-plugins.md)

### <a name="other-options"></a>andra alternativ ###

Andra alternativ kan ställas `<video>` in på `data-setup` taggen med hjälp av parametern som tar en JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Detta är uttryckligen inställt på falskt. Genom att ange false, kommer det att tillåta Azure Media Player-skalet att återges på samma sätt på olika plattformar.  Dessutom, i motsats till namnet, touch kommer fortfarande att aktiveras.

### <a name="fluid"></a>Vätska ###

Genom att ställa in det här alternativet på true video element kommer att ta full bredd på den överordnade behållaren och höjd kommer att justeras för att passa en video med en standard 16:9 bildförhållande.

`<video ... data-setup='{"fluid": true}'>`

`fluid`åsidosätter explicit `width` `height` och inställningar. Det här alternativet är endast `2.0.0` tillgängligt i Azure Media Player-versionen och senare.

### <a name="playbackspeed"></a>uppspelningShastighet ###

`playbackSpeed`alternativet styr uppspelningShastighetskontrollen och uppsättningen inställningar för uppspelningshastighet som är tillgängliga för användaren. `playbackSpeed`tar ett objekt. För att aktivera uppspelningshastighetskontrollen i `enabled` kontrollfältet måste objektets egenskap ställas in på true. Ett exempel på hur du aktiverar uppspelningshastighet i markering:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Andra egenskaper `playbackSpeed` för inställningen anges av [Objektet PlaybackSpeedOptions.](https://docs.microsoft.com/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions)

Exempel på inställning av alternativ för uppspelningshastighet i JavaScript:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Det här alternativet är endast tillgängligt i Azure Media Player version 2.0.0 och senare.

### <a name="staledatatimelimitinsec"></a>inaktuellaDataTimeLimitInSec ###

Alternativet `staleDataTimeLimitInSec` är en optimering som låter dig konfigurera hur många sekunder av inaktuella data som du vill behålla i mediaSource-buffertarna. Alternativet är inaktiverat som standard.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

Om du ställer in true kan du visa direkt CEA-textning i dina livestreamar och livearkiv. Etikettattributet krävs inte, om det inte ingår kommer spelaren att falla tillbaka till en standardetikett.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Det här alternativet är endast tillgängligt i Azure Media Player version 2.1.1 och senare.

## <a name="next-steps"></a>Nästa steg ##

- [Snabbstart för Azure Media Player](azure-media-player-quickstart.md)