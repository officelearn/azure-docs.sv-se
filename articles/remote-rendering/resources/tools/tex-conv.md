---
title: Konverterings verktyg för TexConv-textur
description: Användar handbok för kommando rads verktyget TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80680029"
---
# <a name="texconv---texture-conversion-tool"></a>Konverterings verktyg för TexConv-textur

TexConv är ett kommando rads verktyg som används för att bearbeta texturer från vanliga indataformat som PNG, TGA, JPEG och DDS i optimerade format för körnings användning.
Det vanligaste scenariot är att konvertera en enstaka indatafil `A.xxx` till ett optimerat format `B.yyy`, men verktyget har många ytterligare alternativ för avancerade användnings områden.

## <a name="command-line-help"></a>Kommando rads hjälp

Om du `--help` kör TexConv. exe med parametern visas alla tillgängliga alternativ. Dessutom kan TexConv skriva ut de använda alternativen när de körs, för att hjälpa till att förstå vad det gör. Se dessa utdata för mer information.

## <a name="general-usage"></a>Allmän användning

TexConv skapar alltid **exakt en utdatafil** . Det kan använda **flera indatafiler** för att sammanställa utdata från. För sammansättningen krävs också en **kanal mappning**, som anger vilken kanal (*röd, grön, blå* eller *alfa*) som ska tas från den indatafilen och flyttas till den kanal som bilden visas i.

Den mest raka kommando raden är följande:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`anger utdatafilen och formatet
- `-in0`anger den första indata-bilden
- `-rgba`anger att utmatnings bilden ska använda alla fyra kanaler och att de ska tas 1:1 från inmatnings bilden

## <a name="multiple-input-files"></a>Flera indatafiler

Om du vill sammanställa utdata från flera indatafiler anger du varje indatafil med `-in` alternativet med ett ökande tal:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

När du monterar en cubemap från 2D-texturer kan du `-right`också `-left`använda `-top`, `-bottom`, `-front`, `-back` , `-px`eller `-nx`, `-py` `-ny` `-pz`,,, `-nz`,.

En korrekt kanal mappning krävs för att mappa dessa indata till utdatafilen.

## <a name="channel-mappings"></a>Kanal mappningar

Alternativen för kanal mappning anger från vilka indata som ska fylla i de angivna utkanalerna. Du kan ange indatamängden för varje kanal individuellt så här:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Här kommer RGB-kanalerna för utdata att fyllas med den första inmatnings bilden, men röd och blå kommer att växlas. Alfa kanalen för utdata fylls med värdena från den röda kanalen i den andra inmatnings bilden.

Att ange mappningen för varje kanal separat ger störst flexibilitet. För enkelhetens skull kan du skriva med hjälp av "swizzling"-operatörer:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Utmatnings kanaler

Följande alternativ för kanal mappning är tillgängliga:

- `-r``-b`, `-g`,, `-a` : Dessa anger tilldelningar för enskild kanal
- `-rg`: Ange de röda och gröna kanal tilldelningarna.
- `-rgb`: Ange de röda, gröna och blå kanal tilldelningarna.
- `-rgba`: Anger alla fyra kanal tilldelningarna.

Om du bara anger R-, RG-eller RGB-kanalen uppmanas TexConv att skapa en utdatafil med endast 1, 2 respektive 3 kanaler.

### <a name="input-swizzling"></a>Swizzling för indatamängd

När du anger vilken inmatnings struktur som ska fylla vilken utmatnings kanal, kan en swizzle indata:

- `-rgba in0`motsvarar`-rgba in0.rgba`
- `-rgba in0.bgra`kommer att swizzle inkanalerna
- `-rgb in0.rrr`duplicerar den röda kanalen till alla kanaler

En kan också fylla kanaler med antingen svart eller vitt:

- `-rgb in0 -a white`skapar en 4-kanals utmatnings struktur men ställer in alfa till fullständigt ogenomskinligt
- `-rg black -b white`kommer att skapa en helt blå struktur

## <a name="common-options"></a>Vanliga alternativ

De mest intressanta alternativen visas nedan. Fler alternativ visas av `TexConv --help`.

### <a name="output-type"></a>Utdatatyp

- `-type 2D`: Utdata är en vanlig 2D-bild.
- `-type Cubemap`: Utdata blir en cubemap-bild. Stöds endast för DDS-utdatafiler. När detta anges kan en sammanställa cubemap från 6 vanliga 2D-ingångs bilder.

### <a name="image-compression"></a>Bild komprimering

- `-compression none`: Utdata-avbildningen kommer att komprimeras.
- `-compression medium`: Om det stöds kommer utmatnings avbildningen att använda komprimering utan att offra för mycket kvalitet.
- `-compression high`: Om det stöds, kommer utdata-avbildningen att använda komprimering och offra kvalitet för en mindre fil.

### <a name="mipmaps"></a>Mipmaps

Som standard genererar TexConv mipmaps när utdataformatet stöder det.

- `-mipmaps none`: Mipmaps kommer inte att genereras.
- `-mipmaps Linear`: Om det stöds genereras mipmaps med ett Box filter.

### <a name="usage-srgb--gamma-correction"></a>Användning (sRGB/gamma korrigering)

`-usage` Alternativet anger syftet med utdata och anger därför TexConv om du vill använda gamma korrigering för indata-och utdatafiler. Användningen påverkar bara RGB-kanalerna. Alfa kanalen anses alltid innehålla linjära värden. Om användning inte anges kommer läget Auto att försöka identifiera användningen från formatet och fil namnet för den första indata-bilden. Till exempel är enkel-och dubbel kanals utdataformat alltid linjära. Kontrol lera utdata för att se vilka beslut som TexConv har gjort.

- `-usage Linear`: Utdata-avbildningen innehåller värden som inte representerar färger. Detta är vanligt vis fallet för metallisk och tuffa texturer, samt alla typer av masker.

- `-usage Color`: Utmatnings bilden representerar färg, till exempel diffuse/albedo Maps. Flaggan sRGB anges i den utgående DDS-rubriken.

- `-usage HDR`: Utdatafilen bör använda mer än 8 bitar per bild punkt för kodning. Därför lagras alla värden i linjärt utrymme. För HDR-texturer spelar det ingen roll om data representerar färger eller andra data.

- `-usage NormalMap`: Utmatnings bilden representerar en normal karta med tangens-utrymme. Värdena är normaliserade och mipmap-beräkningen optimeras något.

- `-usage NormalMap_Inverted`: Utdata är en normal karta med tangens och Y som pekar på motsatt riktning än indata.

### <a name="image-rescaling"></a>Skalning av bild

- `-minRes 64`: Anger den minsta upplösningen för utdata. Om den inmatade bilden är mindre, kommer den att bli förhöjd.
- `-maxRes 1024`: Anger den maximala upplösningen för utdata. Om indatabilden är större kommer den att få downscaled.
- `-downscale 1`: Om det här är större än 0 blir de inmatade bilderna hälften i lösning N gånger. Använd detta för att tillämpa en övergripande kvalitets minskning.

## <a name="examples"></a>Exempel

### <a name="convert-a-color-texture"></a>Omvandla en färg struktur

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Konvertera en normal karta

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Skapa en HDR-cubemap

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

En fantastisk källa för HDR-cubemaps är [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Bageri flera bilder i en

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extrahera en enskild kanal

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
