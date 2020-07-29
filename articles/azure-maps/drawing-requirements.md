---
title: Krav för ritnings paket i Azure Maps Creator
description: Lär dig mer om kraven för ritnings paket för att konvertera design filer för anläggningar för att mappa data med hjälp av Azure Maps Conversion service
author: anastasia-ms
ms.author: v-stharr
ms.date: 6/12/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philMea
ms.openlocfilehash: 4a57719ec9e7b22ed81ee6f07a568a993846de42
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374328"
---
# <a name="drawing-package-requirements"></a>Krav för ritningspaket

Med [tjänsten Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) kan du konvertera uppladdade ritnings paket till kart data. I den här artikeln beskrivs kraven för ritnings paket för konverterings-API: et. Om du vill visa ett exempel paket kan du hämta exempel [ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="prerequisites"></a>Krav

Ritnings paketet innehåller ritningar som sparats i DWG-format, vilket är det interna fil formatet för Autodesks AutoCAD® program vara, som [tillhör Autodesk, Inc](https://www.autodesk.com/company/legal-notices-trademarks/trademarks/guidelines-for-use#section12).

Du kan välja valfri CAD-programvara för att skapa ritningarna i ritnings paketet.  

[Tjänsten Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) konverterar ritnings paketet till kart data.  Konverterings tjänsten har utvecklats och testats med AutoCAD DWG-filformat. `AC1032`är den interna format versionen för DWG-filerna. Du uppmanas att välja `AC1032` för den interna versionen av DWG-filformatet.  

Ord lista med termer som används i det här dokumentet.

| Term  | Definition |
|:-------|:------------|
| Skikt | Ett AutoCAD DWG-lager.|
| Nivå | Ett del i en byggnad med en angiven höjning. Till exempel en byggnads golv. |
| XREF  |En fil i AutoCAD DWG-filformat (. dwg) som är kopplad till den primära ritningen som en extern referens.  |
| Funktion | Ett objekt som kombinerar en geometri med ytterligare metadatainformation. |
| Funktions klasser | En gemensam skiss för funktioner. Till exempel är en enhet en funktions klass och ett kontor är en funktion. |

## <a name="drawing-package-structure"></a>Ritnings paketets struktur

Ett Drawing-paket är ett zip-arkiv som innehåller följande filer:

* DWG-filer i AutoCAD DWG-filformat.
* En _manifest.jspå_ fil för en enda funktion.

DWG-filerna kan ordnas på valfritt sätt i mappen, men manifest filen måste vara Live i mappens rot Katalog. Mappen måste vara zippad i en enda arkivfil med fil namns tillägget. zip. Nästa avsnitt innehåller information om kraven för DWG-filerna, manifest filen och innehållet i dessa filer.  

## <a name="dwg-files-requirements"></a>Krav för DWG-filer

En enskild DWG-fil krävs för varje nivå i anläggningen. Nivåns data måste finnas i en enskild DWG-fil. Alla externa referenser (_xrefs_) måste vara kopplade till den överordnade ritningen. Dessutom är varje DWG-fil:

* Måste definiera de _yttre_ och _enhets_ lagren. Du kan också definiera följande valfria lager: _vägg_, _dörr_, _UnitLabel_, _zon_och _ZoneLabel_.
* Får inte innehålla funktioner från flera nivåer.
* Får inte innehålla funktioner från flera anläggningar.

[Tjänsten Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) kan extrahera följande funktions klasser från en DWG-fil:

* Nivåer
* Enheter
* Zoner
* Öppningar
* Väggar
* Lodräta inträngare

Alla konverterings jobb resulterar i en minimal uppsättning standard kategorier: Room, Structure. vägg, Open. dörr, Zone och Facility. Ytterligare kategorier är för varje kategori namn som objekt refererar till.  

Ett DWG-lager måste innehålla funktioner i en enskild klass. Klasser får inte dela ett lager. Enheter och väggar kan till exempel inte dela ett lager.

DWG-lager måste också följa följande kriterier:

* Ritningarnas ursprung för alla DWG-filer måste anpassas till samma latitud och longitud.
* Varje nivå måste ha samma orientering som de andra nivåerna.
* Själv överlappande polygoner kommer automatiskt att repareras och [tjänsten Azure Maps Conversion](https://docs.microsoft.com/rest/api/maps/conversion) kommer att generera en varning. Vi rekommenderar att du kontrollerar de reparerade resultaten manuellt eftersom de kanske inte överensstämmer med de förväntade resultaten.

Alla lager enheter måste vara en av följande typer: linje, polylinje, polygon, cirkelformad båge, cirkel, text (enskild linje). Andra entitetstyper kommer att ignoreras.

I tabellen nedan beskrivs de entitetstyper som stöds och funktioner som stöds för varje lager. Om ett lager innehåller enhets typer som inte stöds, ignorerar [Azure Maps Conversion service](https://docs.microsoft.com/rest/api/maps/conversion) dessa entiteter.  

| Skikt | Entitetstyper | Funktioner |
| :----- | :-------------------| :-------
| [Yttre](#exterior-layer) | Polygon, PolyLine (stängd), cirkel | Nivåer
| [Enhet](#unit-layer) |  Polygon, PolyLine (stängd), cirkel | Lodräta inträngare, enheter
| [Byggnad](#wall-layer)  | Polygon, PolyLine (stängd), cirkel | Inte tillämpligt. Mer information finns i [vägg lagret](#wall-layer).
| [Door](#door-layer) | Polygon, PolyLine, linje, CircularArc, cirkel | Öppningar
| [Zon](#zone-layer) | Polygon, PolyLine (stängd), cirkel | Zon
| [UnitLabel](#unitlabel-layer) | Text (enskild linje) | Inte tillämpligt. Det här lagret kan bara lägga till egenskaper till enhets funktionerna från enhets skiktet. Mer information finns i UnitLabel- [skiktet](#unitlabel-layer).
| [ZoneLabel](#zonelabel-layer) | Text (enskild linje) | Inte tillämpligt. Det här lagret kan bara lägga till egenskaper till zon funktioner från ZonesLayer. Mer information finns i ZoneLabel- [skiktet](#zonelabel-layer)

Nästa avsnitt innehåller information om kraven för varje lager.

### <a name="exterior-layer"></a>Yttre lager

DWG-filen för varje nivå måste innehålla ett lager för att definiera nivåns perimeter. Det här skiktet kallas för det yttre lagret. Om en anläggning till exempel innehåller två nivåer måste den ha två DWG-filer, med ett utvändigt lager för varje fil.

Nu när det finns många enhets ritningar i det yttre lagret innehåller [data uppsättningen för den resulterande](tutorial-creator-indoor-maps.md#create-a-feature-stateset) funktionen bara **en** nivå funktion för varje DWG-fil. Dessutom:

* Yttre måste ritas som polygon, PolyLine (stängd), cirkel.

* Yttre kan överlappa varandra, men kommer att lösas in i en geometri.

Om lagret innehåller flera överlappande polylinjer kommer polylinjerna att lösas in i en enda nivå-funktion. Alternativt, om lagret innehåller flera non_overlapping polylinjes, kommer funktionen på den resulterande nivån att ha en multipolygon-representation.

Ett exempel på det yttre lagret kan ses som dispositions lager i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unit-layer"></a>Enhets skikt

DWG-filen för varje nivå bör definiera ett lager som innehåller enheter.  Enheter är portalerna utrymmen i byggnaden, till exempel kontor, korridoren, trappor och hissar. Enhets lagret måste uppfylla följande krav:

* Enheter måste ritas som polygon, PolyLine (stängda), cirkel.
* Enheter måste ligga innanför gränserna för funktionen yttre perimeter.
* Enheter får inte delvis överlappa varandra.
* Enheter får inte innehålla en egen överlappande geometri.

 Namnge en enhet genom att skapa ett text objekt i _unitLabel_ -skiktet och sedan placera objektet innanför enhetens gränser. Mer information finns i UnitLabel- [skiktet](#unitlabel-layer).

Ett exempel på enhets lagret kan ses som ENHETs skiktet i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="wall-layer"></a>Vägg lager

DWG-filen för varje nivå kan innehålla ett lager som definierar de fysiska omfattningarna för väggar, kolumner och annan byggnads struktur.

* Väggarna måste ritas som polygon, PolyLine (stängd), cirkel.
* Vägg lager (ar) får bara innehålla geometri som tolkas som en byggnads struktur.

Ett exempel på ett sidoytor-lager visas som väggarna i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="door-layer"></a>Dörr skikt

Du kan inkludera ett DWG-lager som innehåller dörrar. Varje dörr måste överlappa en enhets kant från enhets lagret.

Dörrar som öppnas i en Azure Maps data uppsättning representeras som ett enda linje segment som överlappar flera enhets gränser. Följande steg vidtas för att omvandla geometrin i dörr lagret för att öppna funktioner i en data uppsättning.

![Steg för att skapa öppningar](./media/drawing-requirements/opening-steps.png)

### <a name="zone-layer"></a>Zon skikt

DWG-filen för varje nivå kan innehålla ett zon lager som definierar de fysiska omfattningarna för zoner. En zon kan vara ett tomt utrymme i inomhus eller en ryggs varv.

* Zoner måste ritas som polygon, PolyLine (stängd), cirkel.
* Zoner kan överlappa varandra.
* Zoner kan falla innanför eller utanför anläggningens yttre perimeter.

Namnge en zon genom att skapa ett text objekt i _zoneLabel_ -skiktet och placera textobjektet innanför zonens gränser. Mer information finns i [ZoneLabel-skiktet](#zonelabel-layer).

Ett exempel på zonerna lager kan ses som zon skikt i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="unitlabel-layer"></a>UnitLabel-skikt

DWG-filen för varje nivå kan innehålla ett lager för enhets etiketter. Enhets etikett lagret lägger till en namn egenskap i enheter som extraheras från enhets lagret. Enheter med en namn-egenskap kan ha ytterligare information som anges i manifest filen.

* Enhets etiketter måste vara text enheter med en rad.
* Enhets etiketter måste ligga innanför gränserna för deras enhet.
* Enheter får inte innehålla flera text enheter i enhets etikett skiktet.

Ett exempel på UnitLabel-lagret kan ses som UNITLABELS-skiktet i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

### <a name="zonelabel-layer"></a>ZoneLabel-skikt

DWG-filen för varje nivå kan innehålla ett zon etikett lager. Det här lagret lägger till en namn egenskap till zoner som har extraherats från zon skiktet. Zoner med en namn egenskap kan ha ytterligare information som anges i manifest filen.

* Zon etiketter måste vara text enheter med en rad.
* Zon etiketter måste ligga innanför gränserna för zonen.
* Zoner får inte innehålla flera text enheter i zon etikett skiktet.

Ett exempel på Zonelabel-lagret kan ses som ZONELABELS-skiktet i [exempel ritnings paketet](https://github.com/Azure-Samples/am-creator-indoor-data-examples).

## <a name="manifest-file-requirements"></a>Manifest fil krav

Zip-mappen måste innehålla en manifest fil på rot nivån i katalogen och filen måste ha namnet **manifest.jspå**. Den beskriver DWG-filerna för att tillåta att [Azure Maps konverterings tjänsten](https://docs.microsoft.com/rest/api/maps/conversion) tolkar sitt innehåll. Endast de filer som identifieras av manifestet kommer att matas in. Filer som finns i zip-mappen, men som inte anges korrekt i manifestet, kommer att ignoreras.

Fil Sök vägarna, i **buildingLevels** -objektet i manifest filen, måste vara relativa till rotmappen för zip-mappen. DWG-filnamnet måste exakt matcha namnet på anläggnings nivån. En DWG-fil för nivån "Basement" skulle till exempel vara "Basement. DWG". En DWG-fil för nivå 2 får namnet "level_2. DWG." Använd ett under streck om ditt nivå namn har ett blank steg.

Även om det finns krav när du använder manifest objekt krävs inte alla objekt. Tabellen nedan visar de obligatoriska och valfria objekten för version 1,1 av [Azure Maps Conversion service](https://docs.microsoft.com/rest/api/maps/conversion).

| Objekt | Krävs | Beskrivning |
| :----- | :------- | :------- |
| version | true |Schema version för manifest. För närvarande stöds endast version 1,1.|
| directoryInfo | true | Beskriver lokal geografiska och kontakt information. Det kan också användas för att disponera en kontakts geografiska och kontakt uppgifter. |
| buildingLevels | true | Anger nivåerna för byggnader och de filer som innehåller nivåernas design. |
| referens | true | Innehåller numerisk geografisk information för ritningen. |
| dwgLayers | true | Visar en lista med namnen på lagren och varje lager visar namnen på dess egna funktioner. |
| unitProperties | falskt | Kan användas för att infoga ytterligare metadata för enhets funktionerna. |
| zoneProperties | falskt | Kan användas för att infoga ytterligare metadata för zon funktionerna. |

Nästa avsnitt innehåller information om kraven för varje objekt.

### <a name="directoryinfo"></a>directoryInfo

| Egenskap  | typ | Krävs | Beskrivning |
|-----------|------|----------|-------------|
| name      | sträng | true   |  Namn på byggnaden. |
| streetAddress|    sträng |    falskt    | Byggnadens adress. |
|unit     | sträng    |  falskt    |  Enheten i byggnaden. |
| plats |    sträng |    falskt |    Namn på ett område, i eller region. Till exempel "översjöning" eller "Central distrikt". Lokal är inte en del av e-postadressen. |
| adminDivisions |    JSON-matris med strängar |    falskt     | En matris som innehåller adress utformningar (land, delstat, stad) eller (land, prefekturen, stad, stad). Använd ISO 3166-lands koder och ISO 3166-2-statliga/distrikts koder. |
| Post nummer |    sträng    | falskt    | E-postsorterings koden. |
| hoursOfOperation |    sträng |     falskt | Följer formatet för [OSM Open hours](https://wiki.openstreetmap.org/wiki/Key:opening_hours/specification) . |
| phone    | sträng |    falskt |    Telefonnummer som är associerat med byggnaden. Måste innehålla lands koden. |
| webbplats    | sträng |    falskt    | Webbplats som är kopplad till byggnaden. M börjar med http eller https. |
| ej offentlig |    boolesk    | falskt | Flagga som anger om byggnaden är öppen för allmänheten. |
| anchorLatitude | numeric |    falskt | Latitud för en fäst punkt (kartnål). |
| anchorLongitude | numeric |    falskt | Longitud för en fäst punkt (kartnål). |
| anchorHeightAboveSeaLevel  | numeric | falskt | Höjden på anläggningens jord golv över havs nivån, i meter. |
| defaultLevelVerticalExtent | numeric | falskt | Standard höjd (tjocklek) på en nivå för den här funktionen som ska användas när en nivå `verticalExtent` är odefinierad. |

### <a name="buildinglevels"></a>buildingLevels

`buildingLevels`Objektet innehåller en JSON-matris med byggnader-nivåer.

| Egenskap  | Typ | Krävs | Beskrivning |
|-----------|------|----------|-------------|
|levelName    |sträng    |true |    Namn på beskrivande nivå. Till exempel: våning 1, lobbyn, blå parkering, Basement och så vidare.|
|numret | heltal |    true | Ordnings tal används för att bestämma den lodräta ordningen för nivåer. Varje funktion måste ha en nivå med ordnings tal 0. |
|heightAboveFacilityAnchor | numeric | falskt |    Nivåns höjd över ankaret i meter. |
| verticalExtent | numeric | falskt | Nivån av nivån i meter för golv till tak. |
|filename |    sträng |    true |    Fil Systems Sök väg för CAD-ritningen för en skapande nivå. Det måste vara i förhållande till roten i byggnadens zip-fil. |

### <a name="georeference"></a>referens

| Egenskap  | Typ | Krävs | Beskrivning |
|-----------|------|----------|-------------|
|koder    | numeric |    true |    Decimal representation av grader latitud vid ritningens ursprung. Ursprungets koordinater måste vara i WGS84 Web Mercator ( `EPSG:3857` ).|
|lon    |numeric|    true|    Decimal representation av grader longitud vid ritningens ursprung. Ursprungets koordinater måste vara i WGS84 Web Mercator ( `EPSG:3857` ). |
|vinkel|    numeric|    true|   Vinkeln medsols, i grader, mellan sant norr och ritningens lodräta axel (Y).   |

### <a name="dwglayers"></a>dwgLayers

| Egenskap  | Typ | Krävs | Beskrivning |
|-----------|------|----------|-------------|
|yttre    |Strängmatris|    true|    Namn på lager (n) som definierar den yttre skapande profilen.|
|unit|    Strängmatris|    true|    Namn på skikt som definierar enheter.|
|byggnad|    Strängmatris    |falskt|    Namn på lager (n) som definierar väggar.|
|dörren    |Strängmatris|    falskt   | Namn på lager (n) som definierar dörrar.|
|unitLabel    |Strängmatris|    falskt    |Namn på lager (n) som definierar namn på enheter.|
|zon | Strängmatris    | falskt    | Namn på lager (n) som definierar zoner.|
|zoneLabel | Strängmatris |     falskt |    Namn på lager (n) som definierar namn på zoner.|

### <a name="unitproperties"></a>unitProperties

`unitProperties`Objektet innehåller en JSON-matris med enhets egenskaper.

| Egenskap  | Typ | Krävs | Beskrivning |
|-----------|------|----------|-------------|
|unitName    |sträng    |true    |Namnet på den enhet som ska associeras med den här `unitProperty` posten. Den här posten är endast giltig när en etikett matchning finns `unitName` i `unitLabel` lager. |
|categoryName|    sträng|    falskt    |Kategori namn. En fullständig lista över kategorier finns i [Kategorier](https://aka.ms/pa-indoor-spacecategories). |
|navigableBy| Strängmatris |    falskt    |Anger vilka typer av navigerings agenter som kan passera enheten. Till exempel "gående". Den här egenskapen meddelar wayfinding-funktionerna.  De tillåtna värdena är,,,,,,,,, `pedestrian` `wheelchair` `machine` `bicycle` `automobile` `hiredAuto` `bus` `railcar` `emergency` `ferry` `boat` , och `disallowed` .|
|routeThroughBehavior|    sträng|    falskt    |Enhetens funktions sätt. De tillåtna värdena är `disallowed` , `allowed` och `preferred` . Standardvärdet är `allowed` .|
|passagerare    |Matris med directoryInfo-objekt |falskt    |Lista över personer för enheten. |
|nameAlt|    sträng|    falskt|    Alternativt namn på enheten. |
|nameSubtitle|    sträng    |falskt|    Under rubrik för enheten. |
|addressRoomNumber|    sträng|    falskt|    Enhetens rum/enhet/Apartment/Suite-nummer.|
|verticalPenetrationCategory|    sträng|    falskt| När den här egenskapen definieras blir den resulterande funktionen en vertikal inträngning (VRT) i stället för en enhet. VRTs kan användas för att navigera till andra VRT-funktioner på nivåerna ovan eller under. Vertikal inträngning är ett [kategori](https://aka.ms/pa-indoor-spacecategories) namn. Om den här egenskapen har definierats åsidosätts egenskapen kategori namn med verticalPenetrationCategory. |
|verticalPenetrationDirection|    sträng|    falskt    |Om `verticalPenetrationCategory` har definierats kan du ange en giltig riktning för resan. De tillåtna värdena är `lowToHigh` , `highToLow` , `both` och `closed` . Standardvärdet är `both` .|
| ej offentlig | boolesk | falskt | Anger om enheten är öppen för offentlig. |
| isRoutable | boolesk | falskt | När det är inställt på `false` , kan enheten inte navigera till eller till. Standardvärdet är `true` . |
| isOpenArea | boolesk | falskt | Tillåter att navigerings agenten anger enheten utan att behöva öppna den i enheten. Som standard är det här värdet inställt på `true` för enheter utan öppningar; `false` för enheter med öppningar.  Om du manuellt ställer in `isOpenArea` `false` på en enhet utan öppningar resulterar det i en varning. Detta beror på att den resulterande enheten inte kan kontaktas av en navigerings agent.|

### <a name="the-zoneproperties-object"></a>ZoneProperties-objektet

`zoneProperties`Objektet innehåller en JSON-matris med zon egenskaper.

| Egenskap  | Typ | Krävs | Beskrivning |
|-----------|------|----------|-------------|
|Zonnamn        |sträng    |true    |Namnet på zonen som ska associeras med `zoneProperty` posten. Posten är endast giltig när en etikett matchning `zoneName` hittas i `zoneLabel` zonens skikt.  |
|categoryName|    sträng|    falskt    |Kategori namn. En fullständig lista över kategorier finns i [Kategorier](https://aka.ms/pa-indoor-spacecategories). |
|zoneNameAlt|    sträng|    falskt    |Alternativt namn på zonen.  |
|zoneNameSubtitle|    sträng |    falskt    |Under rubrik för zonen. |
|zoneSetId|    sträng |    falskt    | Ange ID för att upprätta en relation mellan flera zoner så att de kan frågas eller väljas som en grupp. Till exempel zoner som sträcker sig över flera nivåer. |

### <a name="sample-drawing-package-manifest"></a>Exempel på paket manifest för ritning

Nedan visas en exempel manifest fil för exempel ritnings paketet. Klicka på [exempel ritnings paket](https://github.com/Azure-Samples/am-creator-indoor-data-examples)för att ladda ned hela paketet.

#### <a name="manifest-file"></a>Manifest fil

```JSON
{
    "version": "1.1", 
    "directoryInfo": { 
        "name": "Contoso Building", 
        "streetAddress": "Contoso Way", 
        "unit": "1", 
        "locality": "Contoso eastside", 
        "postalCode": "98052", 
        "adminDivisions": [ 
            "Contoso city", 
            "Contoso state", 
            "Contoso country" 
        ], 
        "hoursOfOperation": "Mo-Fr 08:00-17:00 open", 
        "phone": "1 (425) 555-1234", 
        "website": "www.contoso.com", 
        "nonPublic": false, 
        "anchorLatitude": 47.636152, 
        "anchorLongitude": -122.132600, 
        "anchorHeightAboveSeaLevel": 1000, 
        "defaultLevelVerticalExtent": 3  
    }, 
    "buildingLevels": { 
        "levels": [ 
            { 
                "levelName": "Basement", 
                "ordinal": -1, 
                "filename": "./Basement.dwg" 
            }, { 
                "levelName": "Ground", 
                "ordinal": 0, 
                "verticalExtent": 5, 
                "filename": "./Ground.dwg" 
            }, { 
                "levelName": "Level 2", 
                "ordinal": 1, 
                "heightAboveFacilityAnchor": 3.5, 
                "filename": "./Level_2.dwg" 
            } 
        ] 
    }, 
    "georeference": { 
        "lat": 47.636152, 
        "lon": -122.132600, 
        "angle": 0 
    }, 
    "dwgLayers": { 
        "exterior": [ 
            "OUTLINE", "WINDOWS" 
        ], 
        "unit": [ 
            "UNITS" 
        ], 
        "wall": [ 
            "WALLS" 
        ], 
        "door": [ 
            "DOORS" 
        ], 
        "unitLabel": [ 
            "UNITLABELS" 
        ], 
        "zone": [ 
            "ZONES" 
        ], 
        "zoneLabel": [ 
            "ZONELABELS" 
        ] 
    }, 
    "unitProperties": [ 
        { 
            "unitName": "B01", 
            "categoryName": "room.office", 
            "navigableBy": ["pedestrian", "wheelchair", "machine"], 
            "routeThroughBehavior": "disallowed", 
            "occupants": [ 
                { 
                    "name": "Joe's Office", 
                    "phone": "1 (425) 555-1234" 
                } 
            ], 
            "nameAlt": "Basement01", 
            "nameSubtitle": "01", 
            "addressRoomNumber": "B01", 
            "nonWheelchairAccessible": false, 
            "nonPublic": true, 
            "isRoutable": true, 
            "isOpenArea": true 
        }, 
        { 
            "unitName": "B02" 
        }, 
        { 
            "unitName": "B05", 
            "categoryName": "room.office" 
        }, 
        { 
            "unitName": "STRB01", 
            "verticalPenetrationCategory": "verticalPenetration.stairs", 
            "verticalPenetrationDirection": "both" 
        }, 
        { 
            "unitName": "ELVB01", 
            "verticalPenetrationCategory": "verticalPenetration.elevator", 
            "verticalPenetrationDirection": "high_to_low" 
        } 
    ], 
    "zoneProperties": 
    [ 
        { 
            "zoneName": "WifiB01", 
            "categoryName": "Zone", 
            "zoneNameAlt": "MyZone", 
            "zoneNameSubtitle": "Wifi", 
            "zoneSetId": "1234" 
        }, 
        { 
            "zoneName": "Wifi101",
            "categoryName": "Zone",
            "zoneNameAlt": "MyZone",
            "zoneNameSubtitle": "Wifi",
            "zoneSetId": "1234"
        }
    ]
}
```

## <a name="next-steps"></a>Nästa steg

När ditt Draw-paket uppfyller kraven kan du använda [Azure Maps Conversion service](https://docs.microsoft.com/rest/api/maps/conversion) för att konvertera paketet till en karta-datauppsättning. Sedan kan du använda data uppsättningen för att generera en insamlad karta med modulen inomhus Maps. Läs mer om att använda modulen inomhus Maps genom att läsa följande artiklar:

> [!div class="nextstepaction"]
>[Skapare för inomhus Maps](creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Självstudie: skapa en skapare inomhus karta](tutorial-creator-indoor-maps.md)

> [!div class="nextstepaction"]
> [Dynamisk formatering för inomhus-kartor](indoor-map-dynamic-styling.md)
