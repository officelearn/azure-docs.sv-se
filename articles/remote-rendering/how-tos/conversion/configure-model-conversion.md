---
title: Konfigurera modellkonverteringen
description: Beskrivning av alla modell konverterings parametrar
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: 104a583122fa08cf145191b8bcee49ce5f042599
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/27/2020
ms.locfileid: "84021406"
---
# <a name="configure-the-model-conversion"></a>Konfigurera modellkonverteringen

I det här kapitlet dokumenteras alternativen för modell konvertering.

## <a name="settings-file"></a>Inställnings fil

Om en fil `ConversionSettings.json` som heter finns i behållaren indata i indata-modellen, används den för att tillhandahålla ytterligare konfiguration för modell konverterings processen.

Innehållet i filen bör uppfylla följande JSON-schema:

```json
{
    "$schema" : "http://json-schema.org/schema#",
    "description" : "ARR ConversionSettings Schema",
    "type" : "object",
    "properties" :
    {
        "scaling" : { "type" : "number", "exclusiveMinimum" : 0, "default" : 1.0 },
        "recenterToOrigin" : { "type" : "boolean", "default" : false },
        "opaqueMaterialDefaultSidedness" : { "type" : "string", "enum" : [ "SingleSided", "DoubleSided" ], "default" : "DoubleSided" },
        "material-override" : { "type" : "string", "default" : "" },
        "gammaToLinearMaterial" : { "type" : "boolean", "default" : false },
        "gammaToLinearVertex" : { "type" : "boolean", "default" : false },
        "sceneGraphMode": { "type" : "string", "enum" : [ "none", "static", "dynamic" ], "default" : "dynamic" },
        "generateCollisionMesh" : { "type" : "boolean", "default" : true },
        "unlitMaterials" : { "type" : "boolean", "default" : false },
        "fbxAssumeMetallic" : { "type" : "boolean", "default" : true },
        "deduplicateMaterials" : { "type" : "boolean", "default" : true },
        "axis" : {
            "type" : "array",
            "items" : {
                "type" : "string",
                "enum" : ["default", "+x", "-x", "+y", "-y", "+z", "-z"]
            },
            "minItems": 3,
            "maxItems": 3
        }
    },
    "additionalProperties" : false
}
```

En exempel `ConversionSettings.json` fil kan vara:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometri parametrar

* `scaling`– Den här parametern skalar en modell enhetligt. Skalning kan användas för att utöka eller krympa en modell, till exempel för att visa en byggnads modell i en tabell. Eftersom åter givnings motorn förväntar sig att längderna ska anges i meter, uppstår en annan viktig användning av den här parametern när en modell definieras i olika enheter. Till exempel, om en modell definieras i centimeter, bör du återge modellen med rätt storlek genom att använda en skala på 0,01.
Vissa käll data format (till exempel. FBX) tillhandahåller ett tips för enhets skalning, i så fall konverteringen av implicit skalar modellen till mätnings enheter. Den implicita skalningen som tillhandahålls av käll formatet kommer att tillämpas ovanpå skalnings parametern.
Den slutgiltiga skalnings faktorn tillämpas på geometri hörnen och de lokala transformeringarna i diagrammets graf-noder. Skalningen för rot entitetens transformering ändras inte.

* `recenterToOrigin`-Anger att en modell ska konverteras så att dess avgränsnings ruta centreras vid ursprunget.
Centrering är viktigt om käll modellen har förplacerats långt från ursprunget, eftersom problem med flytt ALS precision kan orsaka åter givning av artefakter.

* `opaqueMaterialDefaultSidedness`– Åter givnings motorn förutsätter att ogenomskinligt material är dubbels idiga.
Om det inte är det avsedda beteendet ska den här parametern anges till "SingleSided". Mer information finns i [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Åsidosättningar av material

* `material-override`– Med den här parametern kan bearbetning av material [anpassas under konverteringen](override-materials.md).

### <a name="material-de-duplication"></a>Material avduplicering

* `deduplicateMaterials`– Med den här parametern aktive ras eller inaktive ras automatisk avduplicering av material som delar samma egenskaper och strukturer. Avdupliceringen sker efter att material åsidosättningar har bearbetats. Den är aktive rad som standard.

### <a name="color-space-parameters"></a>Parametrar för färg område

Åter givnings motorn förväntar sig att färg värden ska vara i linjärt avstånd.
Om en modell definieras med hjälp av gamma avstånd ska dessa alternativ anges till sant.

* `gammaToLinearMaterial`– Konvertera material färger från gamma avstånd till linjärt utrymme
* `gammaToLinearVertex`– Konvertera :::no-loc text="vertex"::: färger från gamma avstånd till linjärt utrymme

> [!NOTE]
> För FBX-filer är inställningarna inställda på `true` som standard. För alla andra filtyper är standardvärdet `false` .

### <a name="scene-parameters"></a>Scen parametrar

* `sceneGraphMode`– Definierar hur scen diagrammet i käll filen konverteras:
  * `dynamic`(standard): alla objekt i filen visas som [entiteter](../../concepts/entities.md) i API: et och kan omvandlas oberoende av varandra. Node-hierarkin vid körning är identisk med strukturen i käll filen.
  * `static`: Alla objekt exponeras i API, men kan inte omvandlas separat.
  * `none`: Scen diagrammet komprimeras till ett objekt.

Varje läge har olika körnings prestanda. I `dynamic` läget skalas prestanda kostnaden linjärt med antalet [entiteter](../../concepts/entities.md) i diagrammet, även om ingen del flyttas. Den bör endast användas när du flyttar delar individuellt krävs för programmet, till exempel en animering av explosions visning.

`static`Läget exporterar det fullständiga scen diagrammet, men delar i det här grafen har en konstant transformering i förhållande till dess rot del. Rotnoden för objektet kan dock fortfarande flyttas, roteras eller skalas utan betydande prestanda kostnad. Dessutom returnerar [spatiala frågor](../../overview/features/spatial-queries.md) enskilda delar och varje del kan ändras genom [tillstånds åsidosättningar](../../overview/features/override-hierarchical-state.md). Med det här läget är det försumbara omkostnaderna för körning per objekt försumbara. Det är idealiskt för stora scener där du fortfarande behöver kontroll per objekt men utan omvandlingar per objekt.

`none`Läget har den lägsta omkostnaden för körning och även lite bättre inläsnings tider. Det går inte att kontrol lera eller omvandla enskilda objekt i det här läget. Användnings fall är t. ex. Photogrammetry modeller som inte har ett meningsfullt scen diagram på den första platsen.

> [!TIP]
> Många program kommer att läsa in flera modeller. Du bör optimera konverterings parametrarna för varje modell beroende på hur den ska användas. Om du till exempel vill visa modellen för en bil som användaren kan ta isär och granska i detalj måste du konvertera den till ett `dynamic` läge. Men om du dessutom vill placera bilen i en show Room-miljö kan den modellen konverteras med `sceneGraphMode` inställningen till eller till och med `static` `none` .

### <a name="physics-parameters"></a>Fysik parametrar

* `generateCollisionMesh`– Om du behöver stöd för [spatiala frågor](../../overview/features/spatial-queries.md) i en modell måste det här alternativet aktive ras. I värsta fall kan skapandet av ett kollisions nät dubblera konverterings tiden. Modeller med kollisions nät tar längre tid att läsa in och när `dynamic` du använder ett scen diagram, har de också högre prestanda vid körning. För optimala prestanda bör du inaktivera det här alternativet på alla modeller där du inte behöver spatiala frågor.

### <a name="unlit-materials"></a>Unlit material

* `unlitMaterials`-Konverteringen kommer som standard att föredra att skapa [PBR-material](../../overview/features/pbr-materials.md). Det här alternativet anger att konverteraren ska behandla allt material som [färg material](../../overview/features/color-materials.md) i stället. Om du har data som redan har en belysning, till exempel modeller som skapats via Photogrammetry, kan du med det här alternativet snabbt upprätthålla rätt konvertering för allt material, utan att behöva [åsidosätta varje material](override-materials.md) individuellt.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Konvertera från äldre FBX-format, med en Phong material modell

* `fbxAssumeMetallic`-Äldre versioner av FBX-formatet definierar deras material med en Phong material modell. Konverterings processen måste härleda hur dessa material mappar till åter givnings programmets [PBR-modell](../../overview/features/pbr-materials.md). Vanligt vis fungerar det bra, men en tvetydighet kan uppstå när ett material saknar strukturer, höga spegel värden och en icke-grå albedo färg. I detta fall måste konverteringen välja mellan att prioritera de höga spegel värdena, definiera ett högt reflekterande, metallisk material där albedo-färgen löses bort eller prioriterar albedo-färgen, vilket definierar något som en blank plast i färg. Konverterings processen förutsätter som standard att hög spegel värden innebär ett metalliskt material i fall där tvetydighet gäller. Den här parametern kan anges till `false` att växla till motsatt.

### <a name="coordinate-system-overriding"></a>Koordinera system åsidosättning

* `axis`– Om du vill åsidosätta koordinatsystemet för enhet – vektorer. Standardvärden är `["+x", "+y", "+z"]` . I teorin har FBX-formatet ett sidhuvud där dessa vektorer är definierade och konverteringen använder den informationen för att transformera scenen. GlTF-formatet definierar också ett fast koordinatsystem. I praktiken har vissa till gångar antingen felaktig information i sidhuvudet eller sparats med en annan konvention för koordinerade system. Med det här alternativet kan du åsidosätta koordinatsystemet. Exempel: `"axis" : ["+x", "+z", "-y"]` utbyte Z-axeln och y-axeln och behåll koordinatsystemet genom att invertera Y-axelns riktning.

### <a name="no-loc-textvertex-format"></a>:::no-loc text="Vertex":::formatering

Det är möjligt att justera :::no-loc text="vertex"::: formatet för ett nät till handels precision för minnes besparingar. Med ett lägre minnes utrymme kan du läsa in större modeller eller få bättre prestanda. Men beroende på dina data kan fel format kraftigt påverka åter givnings kvaliteten.

> [!CAUTION]
> Att ändra :::no-loc text="vertex"::: formatet bör vara en sista utväg när modeller inte längre ryms i minnet eller när de optimeras för bästa möjliga prestanda. Ändringar kan enkelt introducera åter givnings artefakter, både uppenbara och diskreta. Om du inte vet vad du ska titta närmare på, bör du inte ändra standardvärdet.

Dessa justeringar är möjliga:

* Vissa data strömmar kan uttryckligen tas med eller undantas.
* Precisionen för data strömmar kan minskas för att minska minnes storleken.

Följande `vertex` avsnitt i `.json` filen är valfritt. För varje del som inte uttryckligen anges är konverterings tjänsten tillbaka till standardinställningen.

```json
{
    ...
    "vertex" : {
        "position"  : "32_32_32_FLOAT",
        "color0"    : "NONE",
        "color1"    : "NONE",
        "normal"    : "NONE",
        "tangent"   : "NONE",
        "binormal"  : "NONE",
        "texcoord0" : "32_32_FLOAT",
        "texcoord1" : "NONE"
    },
    ...
```

Genom att tvinga en komponent till `NONE` garanterar vi att utmatnings nätet inte har respektive data ström.

#### <a name="component-formats-per-no-loc-textvertex-stream"></a>Komponent format per :::no-loc text="vertex"::: data ström

De här formaten är tillåtna för respektive komponenter:

| :::no-loc text="Vertex"::: -komponent | Format som stöds (fet = standard) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**16_16_16_16_FLOAT |
|color0| **8_8_8_8_UNSIGNED_NORMALIZED**, ingen |
|color1| 8_8_8_8_UNSIGNED_NORMALIZED, **ingen**|
|gängse| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, ingen |
|tangenten| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, ingen |
|normal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, ingen |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, ingen |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, ingen |

#### <a name="supported-component-formats"></a>Komponent format som stöds

Minnes formaten för formaten är följande:

| Format | Beskrivning | Byte per:::no-loc text="vertex"::: |
|:-------|:------------|:---------------|
|32_32_FLOAT|full flytt ALS precision med två komponenter|8
|16_16_FLOAT|två komponenter halv flytt ALS precision|4
|32_32_32_FLOAT|full flytt ALS precision med tre komponenter|12
|16_16_16_16_FLOAT|halv flytt ALS precision med fyra komponenter|8
|8_8_8_8_UNSIGNED_NORMALIZED|byte i fyra komponenter, normaliserat till `[0; 1]` intervall|4
|8_8_8_8_SIGNED_NORMALIZED|byte i fyra komponenter, normaliserat till `[-1; 1]` intervall|4

#### <a name="best-practices-for-component-format-changes"></a>Metod tips för komponent format ändringar

* `position`: Det är sällsynt att minskad noggrannhet räcker. **16_16_16_16_FLOAT** introducerar märkbara kvantifieringsfel-artefakter, även för små modeller.
* `normal`, `tangent` , `binormal` : Vanligt vis ändras de här värdena tillsammans. Om det inte finns några märkbara belysnings artefakter som orsakas av normala kvantifieringsfel, finns det ingen anledning att öka deras noggrannhet. I vissa fall kan dessa komponenter ställas in på **ingen**:
  * `normal`, `tangent` och `binormal` behövs bara när minst ett material i modellen ska tändas. I ARR är det fallet när ett [PBR-material](../../overview/features/pbr-materials.md) används i modellen när som helst.
  * `tangent`och `binormal` behövs bara när något av tänd materialet använder en normal kart struktur.
* `texcoord0`, `texcoord1` : Textur koordinater kan använda mindre precision (**16_16_FLOAT**) när värdena ligger i `[0; 1]` intervallet och när de adresserade texturerna har en maximal storlek på 2048 x 2048 bild punkter. Om dessa gränser överskrids påverkas kvaliteten på textur mappningen.

#### <a name="example"></a>Exempel

Anta att du har en Photogrammetry-modell som har belysnings-bakade i texturerna. Allt som behövs för att återge modellen är :::no-loc text="vertex"::: positioner och textur koordinater.

Som standard måste konverteraren anta att du vill använda PBR-material i en modell vid en viss tidpunkt, så att den genererar `normal` , `tangent` och `binormal` data åt dig. Det innebär att minnes användningen per hörn är `position` (12 byte) + `texcoord0` (8 byte) + `normal` (4 byte) + `tangent` (4 byte) + `binormal` (4 byte) = 32 byte. Större modeller av den här typen kan enkelt ha många miljoner :::no-loc text="vertices"::: resultat i modeller som kan ta upp flera gigabyte av minnet. Sådana stora mängder data påverkar prestanda och du kan till och med få slut på minne.

Att veta att du aldrig behöver dynamisk belysning i modellen och att du vet att alla textur koordinater är inom `[0; 1]` räckhåll, kan du ange `normal` , `tangent` och `binormal` till `NONE` och `texcoord0` till hälften precision ( `16_16_FLOAT` ), vilket resulterar i endast 16 byte per :::no-loc text="vertex"::: . Genom att klippa ut nät data på hälften kan du läsa in större modeller och eventuellt förbättra prestanda.

## <a name="typical-use-cases"></a>Typiska användnings fall

Det kan vara en omständlig process att hitta lämpliga import inställningar för ett angivet användnings fall. Konverterings inställningarna kan å andra sidan ha en betydande inverkan på körnings prestanda.

Det finns vissa klasser av användnings fall som är kvalificerade för specifika optimeringar. Några exempel anges nedan.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Användnings fall: arkitektur visualisering/stora utomhus kartor

* Dessa typer av scener är ofta statiska, vilket innebär att de inte behöver rörliga delar. Detta `sceneGraphMode` kan anges till eller till och `static` med `none` , vilket förbättrar körnings prestandan. Med `static` läget kan scenens rotnod fortfarande flyttas, roteras och skalas, till exempel för att dynamiskt växla mellan 1:1-skala (för första personens vy) och en tabells övre vy.

* När du behöver flytta delar runt, innebär det vanligt vis att du behöver stöd för raycasts eller andra [spatialdata](../../overview/features/spatial-queries.md), så att du kan välja dessa delar på första plats. Å andra sidan, om du inte planerar att flytta något runt, är det mycket troligt att du inte behöver den för att delta i spatiala frågor och därmed kan stänga av `generateCollisionMesh` flaggan. Den här växeln har betydande påverkan på konverterings tider, inläsnings tider och även uppdaterings kostnader per ram.

* Om programmet inte använder [styckat plan](../../overview/features/cut-planes.md)bör flaggan stängas `opaqueMaterialDefaultSidedness` av. Prestanda ökningen är vanligt vis 20%-30%. Klipp ut plan kan fortfarande användas, men det finns ingen back-ansikten när du tittar på de inre delarna av objekt, vilket ser till att det är självklart. Mer information finns i [ :::no-loc text="single sided"::: rendering](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Användnings fall: Photogrammetry-modeller

När du återger Photogrammetry-modeller behöver du vanligt vis inte använda något scen diagram, så du kan ställa in `sceneGraphMode` på `none` . Eftersom dessa modeller sällan innehåller ett komplext scen diagram som börjar med, bör effekten av det här alternativet vara obetydlig, men.

Eftersom belysning redan är bakadet i texturerna behövs ingen dynamisk belysning. Därför:

* Ställ in `unlitMaterials` flaggan på `true` att aktivera allt material i unlit [färg material](../../overview/features/color-materials.md).
* Ta bort onödiga data från hörn formatet. Se [exemplet](#example) ovan.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Användnings fall: visualisering av Compact Machines osv.

I dessa användnings fall har modellerna ofta mycket hög detalj nivå i en liten volym. Åter givningen är kraftigt optimerad för att hantera sådana fall bra. De flesta optimeringar som nämns i föregående användnings fall gäller dock inte här:

* Enskilda delar bör vara valbara och flyttbara, så `sceneGraphMode` måste vara kvar på `dynamic` .
* Ray-sändningar är vanligt vis en integrerad del av programmet, så att nät i kollisionen måste genereras.
* Klipp ut plan ser bättre ut när `opaqueMaterialDefaultSidedness` flaggan är aktive rad.

## <a name="next-steps"></a>Nästa steg

* [Modell konvertering](model-conversion.md)
* [Färgmaterial](../../overview/features/color-materials.md)
* [PBR-material](../../overview/features/pbr-materials.md)
* [Åsidosätta material under modellkonverteringen](override-materials.md)
