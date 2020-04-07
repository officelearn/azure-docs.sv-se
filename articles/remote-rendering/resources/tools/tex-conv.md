---
title: TexConv - Konverteringsverktyg för textur
description: Användarhandbok för kommandoradsverktyget TexConv
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 1d9b2ca163b70435a6c0e245e66492e8e2866639
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680029"
---
# <a name="texconv---texture-conversion-tool"></a>TexConv - Konverteringsverktyg för textur

TexConv är ett kommandoradsverktyg för att bearbeta texturer från typiska indataformat som PNG, TGA, JPEG och DDS till optimerade format för körningsförbrukning.
Det vanligaste scenariot är att konvertera `A.xxx` en enda `B.yyy`indatafil till ett optimerat format, men verktyget har många ytterligare alternativ för avancerade användningsområden.

## <a name="command-line-help"></a>Hjälp om kommandoraden

Om du kör TexConv.exe med parametern `--help` visas alla tillgängliga alternativ. Dessutom skriver TexConv ut de använda alternativen när de körs, för att förstå vad den gör. Mer information finns i den här utdata.

## <a name="general-usage"></a>Allmän användning

TexConv producerar alltid **exakt en utdatafil.** Den kan använda **flera indatafiler** för att montera utdata från. För sammansättningen behöver den också en **kanalmappning**, som talar om vilken kanal (*Röd, Grön, Blå* eller *Alfa*) att ta från vilken indatafil och flytta den till vilken kanal av utdatabilden.

Den mest rakt fram kommandoraden är denna:

```cmd
TexConv.exe -out D:/result.dds -in0 D:/img.jpg -rgba in0
```

- `-out`anger utdatafilen och formatet
- `-in0`anger den första indatabilden
- `-rgba`säger att utdatabilden ska använda alla fyra kanalerna och att de ska tas 1:1 från indatabilden

## <a name="multiple-input-files"></a>Flera inmatningsfiler

Om du vill montera utdata från flera `-in` indatafiler anger du varje indatafil med alternativet med ett ökande antal:

```cmd
-in0 D:/img0.jpg -in1 D:/img1.jpg -in2 D:/img2.jpg ...
```

När du monterar en kubkarta från 2D-texturer kan `-bottom` `-front`man `-back` `-px`också `-nx` `-py`använda `-ny` `-right` `-pz`, `-nz` `-left`, `-top`, , eller , , , , , , .

För att mappa dessa indata till utdatafilen behövs en korrekt kanalmappning.

## <a name="channel-mappings"></a>Kanalmappningar

Alternativen för kanalmappning anger från vilken inmatning som ska fylla de angivna utkanalerna. Du kan ange indata för varje kanal individuellt så här:

```cmd
-r in0.b -g in0.g -b in0.r -a in1.r
```

Här RGB kanaler av produktionen skulle fyllas med den första ingången bilden men rött och blått kommer att få bytas. Utdatas alfakanal fylls med värdena från den röda kanalen i den andra indatabilden.

Om du anger mappningen för varje kanal separat får du största möjliga flexibilitet. För enkelhetens skull kan det vara samma sak skrivet med hjälp av "swizzling" operatörer:

```cmd
-rgb in0.bgr -a in1.r
```

### <a name="output-channels"></a>Utdatakanaler

Följande alternativ för kanalmappning är tillgängliga:

- `-r`, `-g` `-b`, `-a` , : Dessa anger tilldelningar med en kanal
- `-rg`: Ange tilldelningar av röda och gröna kanaler.
- `-rgb`: Ange tilldelningar av röda, gröna och blå kanaler.
- `-rgba`: Anger alla fyra kanaltilldelningar.

Om du bara nämner R-, RG- eller RGB-kanalen instruerar TexConv att skapa en utdatafil med endast 1, 2 respektive 3 kanaler.

### <a name="input-swizzling"></a>Indata swizzling

När du anger vilken ingångsstruktur som ska fylla vilken utkanal, kan man vrida ingången:

- `-rgba in0`motsvarar`-rgba in0.rgba`
- `-rgba in0.bgra`kommer att snurra ingångarna
- `-rgb in0.rrr`duplicerar den röda kanalen i alla kanaler

Man kan också fylla kanaler med antingen svart eller vitt:

- `-rgb in0 -a white`kommer att skapa en 4-kanals utdatastruktur men ställer in alfa till helt ogenomskinlig
- `-rg black -b white`kommer att skapa en helt blå textur

## <a name="common-options"></a>Vanliga alternativ

De mest intressanta alternativen är listade nedan. Fler alternativ visas `TexConv --help`i .

### <a name="output-type"></a>Utdatatyp

- `-type 2D`: Utgången kommer att vara en vanlig 2D-bild.
- `-type Cubemap`: Utdata kommer att vara en kubkarta bild. Stöds endast för DDS-utdatafiler. När detta anges kan man montera kubkartan från 6 vanliga 2D-indatabilder.

### <a name="image-compression"></a>Bildkomprimering

- `-compression none`: Utdatabilden kommer att komprimeras.
- `-compression medium`: Om den stöds använder utdatabilden komprimering utan att ge avkall på för mycket kvalitet.
- `-compression high`: Om den stöds, kommer utdatabilden att använda komprimering och offra kvalitet till förmån för en mindre fil.

### <a name="mipmaps"></a>Mipmaps

Som standard genererar TexConv mipmaps när utdataformatet stöder det.

- `-mipmaps none`: Mipmaps kommer inte att genereras.
- `-mipmaps Linear`: Om det stöds genereras mipmaps med hjälp av ett lådfilter.

### <a name="usage-srgb--gamma-correction"></a>Användning (sRGB / gammakorrigering)

Alternativet `-usage` anger syftet med utdata och berättar därmed för TexConv om gammakorrigering ska tillämpas på in- och utdatafilerna. Användningen påverkar bara RGB-kanalerna. Alfakanalen anses alltid innehålla "linjära" värden. Om användning inte anges kommer "auto"-läget att försöka identifiera användningen från formatet och filnamnet på den första indatabilden. Till exempel är utgångsformat med en och två kanaler alltid linjära. Kontrollera utdata för att se vilket beslut TexConv gjort.

- `-usage Linear`: Utdatabilden innehåller värden som inte representerar färger. Detta är vanligtvis fallet för metalliska och ojämnhet texturer, liksom alla typer av masker.

- `-usage Color`: Utdatabilden representerar färg, till exempel diffusa/albedokartor. SRGB-flaggan kommer att ställas in i DDS-utdatahuvudet.

- `-usage HDR`: Utdatafilen bör använda mer än 8 bitar per pixel för kodning. Följaktligen lagras alla värden i linjärt utrymme. För HDR texturer spelar det ingen roll om data representerar färg eller andra data.

- `-usage NormalMap`: Utdatabilden representerar en normal karta för tangentrymden. Värdena normaliseras och mipmap-beräkningen optimeras något.

- `-usage NormalMap_Inverted`: Utgången är en normal karta med tangentutrymme med Y som pekar i motsatt riktning än ingången.

### <a name="image-rescaling"></a>Bilduppskalning

- `-minRes 64`: Anger den minsta upplösningen för utdata. Om indatabilden är mindre skalas den upp.
- `-maxRes 1024`: Anger den maximala upplösningen för utdata. Om indatabilden är större skalas den ned.
- `-downscale 1`: Om detta är större än 0, kommer indatabilderna att halveras i upplösning N gånger. Använd detta för att tillämpa en övergripande kvalitetsminskning.

## <a name="examples"></a>Exempel

### <a name="convert-a-color-texture"></a>Konvertera en färgstruktur

```cmd
TexConv.exe -out D:/diffuse.dds -in0 D:/diffuse.jpg -rgba in0 -usage color
```

### <a name="convert-a-normal-map"></a>Konvertera en normal karta

```cmd
TexConv.exe -out D:/normalmap.dds -in0 D:/normalmap.png -rgb in0 -usage normalmap
```

### <a name="create-an-hdr-cubemap"></a>Skapa en HDR-kubkarta

```cmd
TexConv.exe -out "D:/skybox.dds" -in0 "D:/skymap.hdr" -rgba in0 -type cubemap -usage hdr
```

En bra källa för HDR cubemaps är [hdrihaven.com](https://hdrihaven.com/hdris/).

### <a name="bake-multiple-images-into-one"></a>Baka flera bilder till en

```cmd
TexConv.exe -out "D:/Baked.dds" -in0 "D:/metal.tga" -in1 "D:/roughness.png" -in2 "D:/DiffuseAlpha.dds" -r in1.r -g in0.r -b black -a in2.a -usage linear
```

### <a name="extract-a-single-channel"></a>Extrahera en enda kanal

```cmd
TexConv.exe -out D:/alpha-mask-only.dds -in0 D:/DiffuseAlpha.dds -r in0.a
```
