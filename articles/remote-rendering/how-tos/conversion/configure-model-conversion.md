---
title: Konfigurera modellkonverteringen
description: Beskrivning av alla modellkonverteringsparametrar
author: florianborn71
ms.author: flborn
ms.date: 03/06/2020
ms.topic: how-to
ms.openlocfilehash: eb287b812c477b2e472c48d7bd8f44574a398bac
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681576"
---
# <a name="configure-the-model-conversion"></a>Konfigurera modellkonverteringen

I det här kapitlet dokumenteras alternativen för modellkonverteringen.

## <a name="settings-file"></a>Inställningar fil

Om en `ConversionSettings.json` fil som anropas hittas i indatabehållaren bredvid indatamodellen används den för att tillhandahålla ytterligare konfiguration för modellkonverteringsprocessen.

Innehållet i filen bör uppfylla följande json schema:

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

En `ConversionSettings.json` exempelfil kan vara:

```json
{
    "scaling" : 0.01,
    "recenterToOrigin" : true,
    "material-override" : "box_materials_override.json"
}
```

### <a name="geometry-parameters"></a>Geometriparametrar

* `scaling`- Den här parametern skalar en modell på ett enhetligt sätt. Skalning kan användas för att växa eller krympa en modell, till exempel för att visa en byggmodell på en bordsskiva. Eftersom renderingsmotorn förväntar sig att längder anges i mätrar, uppstår en annan viktig användning av denna parameter när en modell definieras i olika enheter. Om till exempel en modell definieras i centimeter ska en skala på 0,01 återge modellen i rätt storlek om du använder en skala på 0,01.
Vissa källdataformat (till exempel .fbx) ger en enhetsskalningsledtråd, i vilket fall konverteringen implicit skalar modellen till mätarenheter. Den implicita skalningen som tillhandahålls av källformatet kommer att tillämpas ovanpå skalningsparametern.
Den slutliga skalningsfaktorn tillämpas på geometrivert och de lokala transformeringarna av scendiagramnoderna. Skalningen för rotentitetens transformering förblir oförändrad.

* `recenterToOrigin`- Anger att en modell bör konverteras så att dess begränsningsram är centrerad på ursprunget.
Centrering är viktigt om källmodellen förskjuts långt från ursprunget, eftersom flyttalsprecisionsproblem i så fall kan orsaka renderingsartefakter.

* `opaqueMaterialDefaultSidedness`- Renderingsmotorn förutsätter att ogenomskinliga material är dubbelsidiga.
Om det inte är det avsedda beteendet bör den här parametern ställas in på "SingleSided". Mer information finns i [enkelsidig rendering](../../overview/features/single-sided-rendering.md).

### <a name="material-overrides"></a>Material åsidosättningar

* `material-override`- Denna parameter gör det möjligt att anpassa bearbetningen av material [under konverteringen.](override-materials.md)

### <a name="color-space-parameters"></a>Parametrar för färgrymd

Renderingsmotorn förväntar sig att färgvärdena ska vara i linjärt utrymme.
Om en modell definieras med gammarymd bör dessa alternativ ställas in på true.

* `gammaToLinearMaterial`- Konvertera materialfärger från gammarymd till linjärt utrymme
* `gammaToLinearVertex`- Konvertera hörnfärger från gammarymd till linjärt utrymme

> [!NOTE]
> För FBX-filer är `true` dessa inställningar inställda på som standard. För alla andra filtyper `false`är standardvärdet .

### <a name="scene-parameters"></a>Scenparametrar

* `sceneGraphMode`- Definierar hur scendiagrammet i källfilen konverteras:
  * `dynamic`(standard): Alla objekt i filen exponeras som [entiteter](../../concepts/entities.md) i API:et och kan omformas oberoende av dem. Nodhierarkin vid körning är identisk med strukturen i källfilen.
  * `static`: Alla objekt exponeras i API:et men kan inte omvandlas oberoende av sig.
  * `none`: Scendiagrammet komprimeras till ett objekt.

Varje läge har olika körningsprestanda. I `dynamic` läge skalas prestandakostnaden linjärt med antalet [entiteter](../../concepts/entities.md) i diagrammet, även när ingen del flyttas. Den bör endast användas när rörliga delar är nödvändigt för programmet, till exempel för en "explosionsvy"-animering.

Läget `static` exporterar hela scendiagrammet, men delar inuti det här diagrammet har en konstant transformering i förhållande till dess rotdel. Rotnoden för objektet kan dock fortfarande flyttas, roteras eller skalas utan någon betydande prestandakostnad. Dessutom [returnerar rumsliga frågor](../../overview/features/spatial-queries.md) enskilda delar och varje del kan ändras via [tillståndsidosättning.](../../overview/features/override-hierarchical-state.md) Med det här läget är körningen overhead per objekt försumbar. Den är idealisk för stora scener där du fortfarande behöver inspektion per objekt men inga transformeringar per objekt ändras.

Läget `none` har minst körtid overhead och även något bättre laddningstider. Inspektion eller omvandling av enskilda objekt är inte möjligt i det här läget. Användningsfall är till exempel fotogrammetrimodeller som inte har ett meningsfullt scendiagram från början.

> [!TIP]
> Många program kommer att läsa in flera modeller. Du bör optimera konverteringsparametrarna för varje modell beroende på hur den ska användas. Om du till exempel vill visa modellen för en bil som användaren ska ta isär `dynamic` och inspektera i detalj måste du konvertera den med läge. Men om du dessutom vill placera bilen i en show room-miljö `sceneGraphMode` kan `static` den `none`modellen konverteras med inställd på eller till och med .

### <a name="physics-parameters"></a>Fysikparametrar

* `generateCollisionMesh`- Om du behöver stöd för [rumsliga frågor](../../overview/features/spatial-queries.md) på en modell måste det här alternativet aktiveras. I värsta fall kan skapandet av ett kollisionsnät fördubbla konverteringstiden. Modeller med kollisionsnät tar längre tid `dynamic` att ladda och när de använder ett scendiagram har de också högre körtidsprestanda. För övergripande optimal prestanda bör du inaktivera det här alternativet på alla modeller där du inte behöver rumsliga frågor.

### <a name="unlit-materials"></a>Obelysta material

* `unlitMaterials`- Som standard omvandlingen kommer att föredra att skapa [PBR material](../../overview/features/pbr-materials.md). Det här alternativet talar om för omvandlaren att behandla alla material som [färgmaterial](../../overview/features/color-materials.md) i stället. Om du har data som redan innehåller belysning, till exempel modeller som skapats genom fotogrammetri, kan du med det här alternativet snabbt framtvinga rätt konvertering för alla material, utan att behöva [åsidosätta varje material](override-materials.md) individuellt.

### <a name="converting-from-older-fbx-formats-with-a-phong-material-model"></a>Konvertera från äldre FBX-format, med en Phong materialmodell

* `fbxAssumeMetallic`- Äldre versioner av FBX-formatet definierar sina material med hjälp av en Phong materialmodell. Konverteringsprocessen måste dra slutsatsen hur dessa material mappas till renderarens [PBR-modell](../../overview/features/pbr-materials.md). Vanligtvis fungerar detta bra, men en tvetydighet kan uppstå när ett material har inga texturer, höga speglande värden och en icke-grå albedofärg. I detta fall har omvandlingen att välja mellan att prioritera de höga speglande värden, definiera en mycket reflekterande, metalliskt material där albedo färgen löser sig bort, eller prioritera albedo färg, definiera något som liknar en glänsande färgglad plast. Som standard förutsätter konverteringsprocessen att mycket speglande värden innebär ett metalliskt material i de fall där tvetydighet gäller. Den här parametern `false` kan ställas in på att växla till det motsatta.

### <a name="coordinate-system-overriding"></a>Koordinatsystemet åsidosätter

* `axis`- För att åsidosätta koordinatsystemenhetsvektorer. Standardvärden `["+x", "+y", "+z"]`är . I teorin har FBX-formatet en rubrik där dessa vektorer definieras och konverteringen använder den informationen för att omvandla scenen. GlTF-formatet definierar också ett fast koordinatsystem. I praktiken har vissa tillgångar antingen felaktig information i huvudet eller sparades med en annan koordinatsystemkonvention. Med det här alternativet kan du åsidosätta koordinatsystemet för att kompensera. Till exempel: `"axis" : ["+x", "+z", "-y"]` kommer att byta ut Z-axeln och Y-axeln och hålla koordinatsystemet handedness genom att invertera Y-axeln riktning.

### <a name="vertex-format"></a>Hörnformat

Det är möjligt att justera hörnformatet för ett nät, för att handla precision för minnesbesparingar. Med lägre minnesavtryck kan du läsa in större modeller eller uppnå bättre prestanda. Beroende på dina data kan dock fel format avsevärt påverka renderingskvaliteten.

> [!CAUTION]
> Ändra vertex-formatet bör vara en sista utväg när modeller inte passar in i minnet längre, eller när optimera för bästa möjliga prestanda. Ändringar kan enkelt introducera renderingsartefakter, både uppenbara och subtila. Om du inte vet vad du ska se upp med, bör du inte ändra standard.

Dessa justeringar är möjliga:

* Specifika dataströmmar kan uttryckligen inkluderas eller uteslutas.
* Noggrannheten i dataströmmar kan minskas för att minska minnesavtrycket.

Följande `vertex` avsnitt i `.json` filen är valfritt. För varje del som inte uttryckligen anges återgår konverteringstjänsten till standardinställningen.

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

Genom att tvinga `NONE`en komponent till , är det garanterat att utdatanätet inte har respektive ström.

#### <a name="component-formats-per-vertex-stream"></a>Komponentformat per hörnström

Dessa format är tillåtna för respektive komponenter:

| Vertex-komponent | Format som stöds (fet = standard) |
|:-----------------|:------------------|
|position| **32_32_32_FLOAT**, 16_16_16_16_FLOAT |
|color0 (färg0)| **8_8_8_8_UNSIGNED_NORMALIZED**, INGEN |
|färg1| 8_8_8_8_UNSIGNED_NORMALIZED, **INGEN**|
|Normal| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, INGEN |
|Tangens| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, INGEN |
|binormal (binormal)| **8_8_8_8_SIGNED_NORMALIZED**, 16_16_16_16_FLOAT, INGEN |
|texcoord0| **32_32_FLOAT**, 16_16_FLOAT, INGEN |
|texcoord1| **32_32_FLOAT**, 16_16_FLOAT, INGEN |

#### <a name="supported-component-formats"></a>Komponentformat som stöds

Minnesfotavtrycken för formaten är följande:

| Format | Beskrivning | Byte per hörn |
|:-------|:------------|:---------------|
|32_32_FLOAT|precision i full flyttalsprecision med två komponenter|8
|16_16_FLOAT|precision på två komponenter|4
|32_32_32_FLOAT|precisionen i trekomponenterna|12
|16_16_16_16_FLOAT|precision på fyra komponenter|8
|8_8_8_8_UNSIGNED_NORMALIZED|fyrakomponentsbyte, normaliserad `[0; 1]` till intervall|4
|8_8_8_8_SIGNED_NORMALIZED|fyrakomponentsbyte, normaliserad `[-1; 1]` till intervall|4

#### <a name="best-practices-for-component-format-changes"></a>Metodtips för ändringar av komponentformat

* `position`: Det är ovanligt att nedsatt noggrannhet är tillräcklig. **16_16_16_16_FLOAT** introducerar märkbara kvantiseringsartefakter, även för små modeller.
* `normal`, `tangent` `binormal`, : : Vanligtvis ändras dessa värden tillsammans. Om det inte finns märkbara belysning artefakter som följer av normal kvantisering, det finns ingen anledning att öka deras noggrannhet. I vissa fall kan dock dessa komponenter ställas in **på NONE:**
  * `normal`, `tangent`och `binormal` behövs endast när minst ett material i modellen ska tändas. I ARR är detta fallet när ett [PBR-material](../../overview/features/pbr-materials.md) används på modellen när som helst.
  * `tangent`och `binormal` behövs endast när något av de upplysta materialen använder en normal kartstruktur.
* `texcoord0`, `texcoord1` : Texturkoordinater kan använda minskad noggrannhet **(16_16_FLOAT)** `[0; 1]` när deras värden stannar i intervallet och när de adresserade texturerna har en maximal storlek på 2048 x 2048 pixlar. Om dessa gränser överskrids, kvaliteten på textur kartläggning kommer att lida.

#### <a name="example"></a>Exempel

Anta att du har en fotogrammetri modell, som har belysning bakas in i texturer. Allt som behövs för att återge modellen är hörnpositioner och texturkoordinater.

Som standard måste konverteraren anta att du kanske vill använda PBR-material på `normal`en `tangent`modell `binormal` någon gång, så det kommer att generera , och data för dig. `position` Följaktligen är minnesanvändningen per vertex (12 `texcoord0` byte) + (8 byte) + `normal` `tangent` (4 byte) `binormal` + (4 byte) + (4 byte) = (4 byte) = 32 byte. Större modeller av denna typ kan lätt ha många miljoner hörn vilket resulterar i modeller som kan ta upp flera gigabyte minne. Sådana stora mängder data kommer att påverka prestanda och du kan även på minne.

Att veta att du aldrig behöver dynamisk belysning på modellen, `[0; 1]` och att `normal`veta `tangent`att `binormal` `NONE` alla `texcoord0` texturkoordinater är inom räckhåll, kan du ställa in , , och till och till halv precision (`16_16_FLOAT`), vilket resulterar i endast 16 byte per hörn. Genom att halvera nätdata kan du läsa in större modeller och eventuellt förbättra prestanda.

## <a name="typical-use-cases"></a>Typiska användningsfall

Att hitta bra importinställningar för ett visst användningsfall kan vara en tråkig process. Å andra sidan kan konverteringsinställningar ha en betydande inverkan på körningsprestanda.

Det finns vissa klasser av användningsfall som uppfyller kraven för specifika optimeringar. Några exempel ges nedan.

### <a name="use-case-architectural-visualization--large-outdoor-maps"></a>Användningsfall: Arkitektonisk visualisering / stora utomhuskartor

* Dessa typer av scener tenderar att vara statiska, vilket innebär att de inte behöver rörliga delar. Följaktligen kan `sceneGraphMode` kan ställas `static` in `none`på eller till och med , vilket förbättrar körningen prestanda. Med `static` läge kan scenens rotnod fortfarande flyttas, roteras och skalas, till exempel för att dynamiskt växla mellan 1:1-skala (för förstapersonsvyn) och en tabellvy.

* När du behöver flytta runt delar innebär det vanligtvis också att du behöver stöd för raycasts eller andra [rumsliga frågor](../../overview/features/spatial-queries.md), så att du kan välja dessa delar i första hand. Å andra sidan, om du inte tänker flytta något runt, är chansen hög att du inte heller behöver det `generateCollisionMesh` för att delta i rumsliga frågor och därför kan stänga av flaggan. Den här växeln har stor inverkan på konverteringstider, laddningstider och även uppdateringskostnader per bildruta.

* Om programmet inte använder [flygplan](../../overview/features/cut-planes.md) `opaqueMaterialDefaultSidedness` ska flaggan stängas av. Prestandaökningen är vanligtvis 20%-30%. Klipp plan kan fortfarande användas, men det kommer inte att vara back-ansikten när man tittar in i de inre delarna av objekt, som ser kontraintuitivt. Mer information finns i [enkelsidig rendering](../../overview/features/single-sided-rendering.md).

### <a name="use-case-photogrammetry-models"></a>Användningsfall: Fotogrammetrimodeller

När du återger fotogrammetrimodeller finns det vanligtvis inget behov `sceneGraphMode` av `none`ett scendiagram, så du kan ställa in till . Eftersom dessa modeller sällan innehåller en komplex scen diagram till att börja med, bör effekten av detta alternativ vara obetydlig, dock.

Eftersom belysning redan är bakad i texturer, behövs ingen dynamisk belysning. Därför:

* Ställ `unlitMaterials` in `true` flaggan på att förvandla alla material till obelysta [färgmaterial](../../overview/features/color-materials.md).
* Ta bort data som inte behövers från hörnformatet. Se [exemplet](#example) ovan.

### <a name="use-case-visualization-of-compact-machines-etc"></a>Användningsfall: Visualisering av kompakta maskiner, etc.

I dessa användningsfall har modellerna ofta mycket hög detalj inom en liten volym. Renderaren är kraftigt optimerad för att hantera sådana fall väl. De flesta av de optimeringar som nämns i det tidigare användningsfallet gäller dock inte här:

* Enskilda delar bör vara valbara och `sceneGraphMode` rörliga, `dynamic`så måste lämnas till .
* Ray kastar är vanligtvis en integrerad del av ansökan, så kollision maskor måste genereras.
* Klipp plan ser `opaqueMaterialDefaultSidedness` bättre ut med flaggan aktiverad.

## <a name="next-steps"></a>Nästa steg

* [Modellkonvertering](model-conversion.md)
* [Färgmaterial](../../overview/features/color-materials.md)
* [PBR-material](../../overview/features/pbr-materials.md)
* [Åsidosätt material under modellkonvertering](override-materials.md)
