---
title: Konfigurera fysiskt baserat åter givnings material i Maya
description: Förklarar hur du konfigurerar fysiskt baserat åter givnings material i Maya och exporterar dem till FBX-format.
author: FlorianBorn71
ms.author: flborn
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 56aa0d91372ac2d21a20f28b1044f0811c716b0c
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/25/2020
ms.locfileid: "91358040"
---
# <a name="tutorial-set-up-physically-based-rendering-materials-in-maya"></a>Självstudie: Konfigurera fysiskt baserat åter givnings material i Maya

## <a name="overview"></a>Översikt
I den här självstudien får du lära dig att:

> [!div class="checklist"]
>
> * Tilldela material med avancerad belysning till objekt i scenen.
> * Hantera instanser av objekt och material.
> * Exportera en scen till FBX-format och välj viktiga alternativ.

Att skapa ett [PBR-material (fysiskt baserat åter givning)](../../overview/features/pbr-materials.md) i Maya är en relativt enkel uppgift. Det liknar på många sätt att installera PBR i andra appar för innehålls skapande som 3DS Max. Den här självstudien är en guide till grundläggande PBR Shader-installation och FBX export för Azure-fjärrrendering-projekt. 

Exempel scenen i den här självstudien innehåller ett antal polygon-Box-objekt. De har tilldelats olika material, till exempel trä, metall, målad metall, plast och gummi. Varje material innehåller i stort sett alla eller de flesta av följande texturer:

* **Albedo**, som är material färg kartan och kallas även för **diffusion** eller **BaseColor**.
* **Metall**, som avgör om ett material är metallisk och vilka delar som är metalliska. 
* **Grovhet**, som avgör hur grovt eller jämna ut en yta är och påverkar skärpan eller blurriness av reflektioner och högdagrar på en yta.
* **Normal**, som lägger till information på en yta utan att behöva lägga till fler polygoner. Exempel på Detaljer kan vara pitting och indrag på en metall yta eller kornig het i trä.
* **Omgivande ocklusion**, som används för att lägga till mjuka skuggor och kontakt skuggor i en modell. Det är en grå Skale karta som visar vilka områden i en modell som får fullständig belysning (vit) eller en fullständig nyans (svart). 

## <a name="prerequisites"></a>Förutsättningar
* Autodesk Maya 2017 eller senare

## <a name="set-up-materials-in-the-scene"></a>Konfigurera material i scenen
Så här konfigurerar du ett PBR-material i Maya.

Som du ser i exempel scenen har vi skapat ett antal Box-objekt. Varje objekt representerar en annan typ av material. Som du ser i bilden har var och en av dessa objekt fått sitt eget namn.

I Azure fjärrrendering används mätare för mått och upp-riktningen är Y-axeln. Innan du börjar skapa till gångar rekommenderar vi att du ställer in dina scen enheter i Maya till meter. För att exportera anger du enheter till mätare i FBX export inställningar.

> [!TIP]
> Ge modellen till gång till lämpliga namn baserat på relevant del eller material typ. Meningsfulla namn gör det enklare att navigera bland objekt – tungt scener.

![Objekt namn](media/object-names.jpg)

När du har skapat eller skaffat strukturer kan du också skapa unika texturer. Du kan använda Texturing-appar som Quixel Suite, PhotoShop eller substans Suite eller hämta allmänna strukturer från andra källor.

Tillämpa texturer på din modell:

1. I ditt scen visnings område väljer du din modell eller geometri och högerklickar på den. I menyn som visas väljer du **tilldela nytt material**.
1. I dialog rutan **tilldela nytt material** går du till **Maya**  >  **Stingray PBS**. Den här åtgärden tilldelar ett PBR-material till din modell. 

I Maya 2020 är ett antal olika PBR-skuggningar tillgängliga. De omfattar **Maya standard-Surface**, **Arnold standard-ytan**och **Stingray PBR**. **Maya standard Surface Shader** har inte exporter ATS än via plugin-programmet FBX 2020. **Arnold standard Surface Shader** kan exporteras med FBX-filer. I de flesta andra avseenden är det identiskt med **Maya standard Surface Shader**. Det är detsamma som **fysiskt material** i 3D Studio Max.

**STINGRAY PBR-skuggningen** är kompatibel med många andra program och stämmer bäst överens med kraven i Azure Remote rendering. Den stöds sedan Maya 2017. När den här typen av material visualiseras i visnings området, är det liknande vad som visualiseras senare i Azure Remote rendering.

![Stingray material](media/stingray-material.jpg)

När du har tilldelat ditt material till din till gång och namngett det kan du nu tilldela dina olika texturer. Följande bilder visar var varje textur typ passar in i PBR-materialet. Med Stingray PBR-material kan du välja vilka attribut som du kan aktivera. Innan du ansluter dina textur kartor måste du aktivera relevanta attribut.

![Material konfiguration](media/material-setup.jpg)

Namnge ditt material på lämpligt sätt genom att ta hänsyn till deras användning eller typ. Ett material som används i en unik del kan namnges för den delen. Ett material som används på ett bredare utbud av områden kan namnges för dess egenskaper eller typ.

Tilldela dina texturer på det sätt som visas i bilden.

![Textur konfiguration](media/texture-setup.jpg)

När ditt PBR-material har skapats och kon figurer ATS bör du tänka på [instans objekt](../../how-tos/conversion/configure-model-conversion.md#instancing) i din scen. Genom att informerar liknande objekt i din scen, till exempel nötter, bultar, skruvar och tvättare, ger betydande besparingar i fil storlek. Instanser av ett huvud objekt kan ha sin egen skalning, rotation och transformering så att de kan placeras på det sätt som krävs i din scen. 

I Maya är indelnings processen enkel.

1. På **Redigera** -menyn går du till **duplicera Special** för att öppna alternativ.
1. I dialog rutan **duplicera specialalternativ** väljer du **instans** alternativet för **geometri typ** . 
1. Välj **duplicera Special**.

   ![Skärm bild som visar ett Maya-fönster med dialog rutan duplicerade alternativ för Special alternativ öppen och duplicerat Special är markerat.](media/instancing.jpg)

Den här åtgärden skapar en instans av objektet. Du kan flytta, rotera eller skala den oberoende av dess överordnade och andra instanser av den överordnade. 

Eventuella ändringar som du gör i en instans medan den är i komponent lägen överförs till alla instanser av objektet. Du kan till exempel arbeta med ett instans objekts komponenter, t. ex. hörn och polygon ansikten. Se till att du vill ha alla ändringar som du har gjort för att påverka alla dessa instanser. 

I exempel scenen inaktiverades varje enskild Box-objekt. Den här åtgärden kommer att ha relevans när vi exporterar scenen till FBX-format.

![Översikt över scen](media/scene-overview.jpg)

> [!TIP]
> Skapa instanser i din scen när du går vidare. Det är mycket svårt att ersätta kopior med instans objekt senare. 

## <a name="fbx-export-process"></a>FBX export process

Vi går vidare till FBX-exporten av din scen-eller scen till gångar. När du exporterar till gångar är det klokt att bara välja de objekt eller till gångar från din scen som du vill exportera. Du kan till exempel ha 100 objekt i en scen. Om du bara vill använda 30 av dem finns det ingen punkt för att exportera hela scenen. 

Så här gör du ditt val:

1. Gå till val av **fil**  >  **export** för att öppna dialog rutan **Exportera val** .
1. I rutan **fil format** väljer du **FBX export** för att Visa export inställningar för FBX. De primära inställningarna för FBX-export är markerade i rött i bilden.

   ![FBX-export](media/FBX-exporting.jpg)

Beroende på dina krav kan du till exempel vilja skicka en till gång till en klient. Du kanske inte vill skicka ett stort antal textur-filer med till gången. Du kan välja att bädda in texturerna i den exporterade FBX-filen. Det här alternativet innebär att du bara har en fil att paketera, men storleken på den FBX till gången ökar markant. Du kan aktivera alternativet för att bädda in texturer genom att välja alternativet **bädda in media** som det visas.

> [!TIP]
> I det här exemplet hette filen för att återspegla det här tillståndet. Den här namngivnings stilen är ett bra sätt att hålla koll på dina till gångar. 

När du har ställt in konfigurationen för export väljer du **Exportera markering** längst ned till höger.

![Bädda in media](media/embedding-media.jpg)

## <a name="conclusion"></a>Slutsats

I allmänhet ser den här typen av material mer realistisk eftersom den baseras på den verkliga fysiken av ljus. Den skapar en extra intensiv påverkan så att scenen verkar finnas i verkligheten.

## <a name="next-steps"></a>Nästa steg

Nu vet du hur du konfigurerar material med avancerad belysning för objekt i en scen. Du vet också hur du exporterar objekten till FBX-format som stöds av Azure Remote rendering. Nästa steg är att konvertera FBX-filen och visualisera den i Azure Remote rendering.

> [!div class="nextstepaction"]
> [Snabb start: konvertera en modell för åter givning](../../quickstarts\convert-model.md)