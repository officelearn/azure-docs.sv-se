---
title: PBR-material
description: Beskriver PBR-materialtypen.
author: jakrams
ms.author: jakras
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 64553506f75451c50a87932904f00a7275ea9286
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680263"
---
# <a name="pbr-materials"></a>PBR-material

*PBR-material* är en av de [materialtyper](../../concepts/materials.md) som stöds i Azure Remote Rendering. De används för [maskor](../../concepts/meshes.md) som bör få realistisk belysning.

PBR står för **P**hysically **B**ased **R**endering och innebär att materialet beskriver de visuella egenskaperna hos en yta på ett fysiskt rimligt sätt, så att realistiska resultat är möjliga under alla ljusförhållanden. De flesta moderna spelmotorer och verktyg för att skapa innehåll stöder PBR-material eftersom de anses vara den bästa tillnärmningen av verkliga scenarier för rendering i realtid.

![Hjälm glTF provmodell återges av ARR](media/helmet.png)

PBR material är inte en universell lösning, dock. Det finns material som reflekterar färg på olika sätt beroende på betraktningsvinkeln. Till exempel vissa tyger eller bilfärger. Dessa typer av material hanteras inte av standard PBR-modellen och stöds för närvarande inte av Azure Remote Rendering. Detta inkluderar PBR-förlängningar, såsom *Thin-Film* (flerskiktade ytor) och *Clear-Coat* (för bilfärger).

## <a name="common-material-properties"></a>Vanliga materialegenskaper

Dessa egenskaper är gemensamma för alla material:

* **albedoFärg:** Den här färgen multipliceras med andra färger, till exempel *albedoMap-* eller *vertex-färgerna*. Om *genomskinlighet* är aktiverat på ett material används alfakanalen för `1` att justera opaciteten, med full ogenomskinlig och `0` helt genomskinlig. Standard är vit.

  > [!NOTE]
  > När ett PBR-material är helt transparent, som en helt ren glasbit, speglar det fortfarande miljön. Ljusa fläckar som solen är fortfarande synliga i reflektionen. Detta är annorlunda för [färgmaterial](color-materials.md).

* **albedoKarta:** En [2D-textur](../../concepts/textures.md) för albedo-värden per pixel.

* **alphaClipEnabled** och **alphaClipThreshold:** Om *alphaClipEnabled* är sant, kommer alla pixlar där albedo-alfavärdet är lägre än *alphaClipThreshold* inte att dras. Alfaurklipp kan användas även utan att aktivera genomskinlighet och är mycket snabbare att rendera. Alfa klippta material är fortfarande långsammare att göra än helt ogenomskinliga material, dock. Som standard är alfaurklippet inaktiverat.

* **textureCoordinateScale** och **textureCoordinateOffset:** Skalan multipliceras med UV-texturkoordinaterna, förskjutningen läggs till den. Kan användas för att sträcka ut och flytta texturerna. Standardskalan är (1, 1) och förskjutning är (0, 0).

* **useVertexColor:** Om nätet innehåller hörnfärger och det här alternativet är aktiverat multipliceras maskornas hörnfärger i *albedoColor* och *albedoMap*. Som standard är hörnfärger inaktiverade.

* **ärDoubleSided:** Om dubbelsidighet är inställd på true, trianglar med detta material återges även om kameran tittar på deras ryggytor. För PBR material belysning beräknas också korrekt för ryggytor. Som standard är det här alternativet inaktiverat. Se även [Enkelsidig rendering](single-sided-rendering.md).

## <a name="pbr-material-properties"></a>PBR materialegenskaper

Kärntanken med fysiskt baserad rendering är att använda *BaseColor,* *Metalness*och *Roughness* egenskaper för att efterlikna ett brett spektrum av verkliga material. En detaljerad beskrivning av PBR ligger utanför den här artikelns räckvidd. Mer information om PBR finns i [andra källor](http://www.pbr-book.org). Följande egenskaper är specifika för PBR-material:

* **baseColor:** I PBR-material kallas *albedofärgen* *basfärgen*. I Azure Remote Rendering finns *egenskapen albedo color* redan genom de vanliga materialegenskaperna, så det finns ingen ytterligare basfärgsegenskap.

* **ojämnhet** och **ojämnhetKarta:** Ojämnhet definierar hur grov eller slät yta är. Ojämna ytor sprider ljuset i fler riktningar än släta ytor, vilket gör reflektioner suddiga snarare än skarpa. Värdeintervallet är `0.0` `1.0`från till . När `roughness` lika `0.0`med , reflektioner kommer att vara skarp. När `roughness` lika `0.5`med , reflektioner blir suddig.

  Om både ett ojämnhetsvärde och en ojämnhetskarta levereras, kommer det slutliga värdet att vara produkten av de två.

* **metall och** **metalnessMap:** I fysik motsvarar denna egenskap om en yta är ledande eller dielektrisk. Ledande material har olika reflekterande egenskaper, och de tenderar att vara reflekterande utan albedo färg. I PBR-material påverkar den här egenskapen hur mycket en yta återspeglar den omgivande miljön. Värdena `0.0` sträcker `1.0`sig från till . När metalness `0.0`är , albedo färgen är fullt synlig och materialet ser ut som plast eller keramik. När metalness `0.5`är , det ser ut som målad metall. När metalness `1.0`är , ytan nästan helt förlorar sin albedo färg och bara återspeglar omgivningen. Till exempel, `metalness` `1.0` om `roughness` `0.0` är och är då en yta ser ut som verkliga spegel.

  Om både ett metallvärde och en metallkarta levereras, kommer det slutliga värdet att vara produkten av de två.

  ![metall och ojämnhet](./media/metalness-roughness.png)

  På bilden ovan ser sfären i det nedre högra hörnet ut som ett riktigt metallmaterial, bottenvänstern ser ut som keramik eller plast. Albedofärgen förändras också beroende på fysiska egenskaper. Med ökande ojämnhet förlorar materialet reflektionsskärpa.

* **normalMap:** För att simulera detaljerade detaljer kan en [normal karta](https://en.wikipedia.org/wiki/Normal_mapping) tillhandahållas.

* **occlusionKarta** och **aoScale:** [Ambient ocklusion](https://en.wikipedia.org/wiki/Ambient_occlusion) gör objekt med sprickor ser mer realistiskt genom att lägga skuggor till ockluderade områden. Ocklusionvärdet `0.0` sträcker `1.0`sig `0.0` från till , där `1.0` betyder mörker (ockluderat) och betyder inga ocklusioner. Om en 2D-textur tillhandahålls som en ocklusionskarta aktiveras effekten och *aoScale* fungerar som en multiplikator.

  ![Ocklusion Karta](./media/boom-box-ao2.gif)

* **transparent:** För PBR-material finns det bara en genomskinlighetsinställning: den är aktiverad eller inte. Opaciteten definieras av albedofärgens alfakanal. När det är aktiverat anropas en mer komplex återgivningspipeline för att rita halvtransparenta ytor. Azure Remote Rendering implementerar verklig [ordning oberoende transparens](https://en.wikipedia.org/wiki/Order-independent_transparency) (OIT).

  Transparent geometri är dyrt att göra. Om du bara behöver hål i en yta, till exempel för bladen av ett träd, är det bättre att använda alfaklippning istället.

  ![Meddelande](./media/transparency.png) om genomskinlighet i bilden ovan, hur den rätte sfären är helt transparent, men reflektionen är fortfarande synlig.

  > [!IMPORTANT]
  > Om något material ska växlas från ogenomskinligt till genomskinligt vid körning måste renderaren använda [återgivningsläget](../../concepts/rendering-modes.md) *TileBasedComposition* . Denna begränsning gäller inte material som konverteras som genomskinliga material till att börja med.

## <a name="technical-details"></a>Teknisk information

Azure Remote Rendering använder Cook-Torrance mikro-aspekt BRDF med GGX NDF, Schlick Fresnel och en GGX Smith korrelerad synlighet term med en Lambert diffus term. Denna modell är de facto branschstandard för närvarande. För mer djupgående detaljer, se den här artikeln: [Fysiskt baserad Rendering - Cook Torrance](http://www.codinglabs.net/article_physically_based_rendering_cook_torrance.aspx)

 Ett alternativ till *PBR-modellen Metalness-Roughness* som används i Azure Remote Rendering är *PBR-modellen Specular-Glossiness.* Denna modell kan representera ett bredare spektrum av material. Det är dock dyrare, och oftast inte fungerar bra för realtid fall.
Det är inte alltid möjligheten att konvertera från *Specular-Glossiness* till *Metalness-Roughness* som det finns *(Diffust, Spegla)* värdera parar som kan inte konverteras till *(BaseColor, Metalness)*. Omvandlingen i den andra riktningen är enklare och mer precisera, sedan alla *(BaseColor, Metalness)* parar motsvarar till well-defined *(Diffust, Spegla)* parar.

## <a name="next-steps"></a>Nästa steg

* [Färgmaterial](color-materials.md)
* [Texturer](../../concepts/textures.md)
* [Maskor](../../concepts/meshes.md)
