---
title: Konfigurera fysiskt baserat renderingsmaterial i Maya
description: Förklarar hur du konfigurerar fysiskt baserat åter givnings material i Maya och exporterar det till FBX-format
author: muxanickms
ms.author: misams
ms.date: 06/16/2020
ms.topic: tutorial
ms.openlocfilehash: 5579994b0746a2de4b0f2ca927027ac709940024
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/17/2020
ms.locfileid: "84977852"
---
# <a name="tutorial-setting-up-physically-based-rendering-materials-in-maya"></a>Självstudie: Konfigurera fysiskt baserat åter givnings material i Maya

## <a name="overview"></a>Översikt
I de här självstudierna får du lära dig att

> [!div class="checklist"]
>
> * koppla material med en avancerad belysnings modell till objekt i scenen.
> * Hantera instanser av objekt och material.
> * Exportera en scen till FBX-format och viktiga alternativ att välja.

Att skapa ett [PBR-material (fysiskt baserat åter givning)](../../overview/features/pbr-materials.md) i `Maya` är en relativt rak aktivitet, på liknande sätt som i många olika sätt att installera PBR i andra appar för innehålls skapande som `3DS Max` . Följande självstudie är en guide till grundläggande konfiguration av PBR-skuggning och FBX export för ARR-projekt. 

Exempel scenen i den här självstudien innehåller ett antal `Polygon Box` objekt som har tilldelats ett antal olika material, metall, målade metaller, plast och gummi. Varje material innehåller i stort sett alla eller de flesta av följande texturer 

* `Albedo`, är material färg kartan, kallas även för `Diffuse` eller`BaseColor`
* `Metalness`, som avgör om ett material är metallisk och vilka delar som är metalliska. 
* `Roughness`, som avgör hur grovt eller jämna ut en yta är, påverkar skärpan eller blurriness av reflektioner och högdagrar på en yta.
* `Normals`, som lägger till information till en yta, till exempel pitting och indrag på en metall yta eller kornig het i trä utan att behöva lägga till fler polygoner.
* `Ambient Occlusion`, som används för att lägga till mjuka skuggor och kontakt skuggor i en modell. Det är en Greyscale-karta som visar vilka områden i en modell som får fullständig belysning (vit) eller en fullständig nyans (svart). 

## <a name="prerequisites"></a>Krav
* `Autodesk Maya 2017`eller senare

## <a name="setting-up-materials-in-the-scene"></a>Konfigurera material i scenen
I Maya är processen för att ställa in ett PBR-material följande:

För att börja med, som du ser i exempel scenen har vi skapat ett antal Box-objekt, som representerar en annan typ av material. Observera, som du ser i bilden nedan, att vart och ett av dessa objekt har fått sitt eget namn 

> Det är värt att notera innan du börjar skapa till gångar för Azure Remote rendering (ARR) som används mätare för mått och upp-riktningen är Y-axeln. Därför är det lämpligt att ställa in dina scen enheter i Maya till meter. Dessutom är det tillrådligt att exportera för att ange enheter till mätare i FBX export inställningar. 

> [!TIP]
Det är en bra idé att namnge modellens till gångar, vanligt vis med relevant del eller material typ, eftersom namnen gör det enklare att navigera objekt – tungtre scener.

![Objekt namn](media/object-names.jpg)

När du har skapat/förvärvat dina strukturer – beroende på dina behov, kanske du vill skapa unika texturer för en modell i Texturing-appar som `Quixel Suite` , `Photoshop` eller så kan `Substance Suite` du använda allmänna strukturer från andra källor, men du kan använda dem i modellen på följande sätt:

* I ditt scen visnings område väljer du din modell/geometri och högerklickar på den. I menyn som visas klickar du på`Assign New Material`
* I `Assign New Material` alternativen går du till `Maya` > `Stingray PBS` . Den här åtgärden tilldelar ett PBR-material till din modell. 

`Maya 2020`Det finns ett antal olika tillgängliga PBR-skuggningar – `Maya Standard Surface` , `Arnold Standard Surface` och `Stingray PBR` . `Maya Standard Surface Shader`Är inte export bar via `FBX plugin 2020` , men `Arnold Standard Surface Shader` kan exporteras med FBX-filer. I de flesta andra avseenden är det identiskt med `Maya Standard Surface Shader` och är detsamma som `Physical Material` i `3D Studio Max` .

**`The Stingray PBR Shader`** är kompatibel med många andra program och stämmer bäst överens med kraven i `ARR` och stöds sedan `Maya 2017` . Det är också enkelt att den här typen av material som visualiseras i ett visnings område liknar vad som kommer att visualiseras senare i ARR.

!["Stingray"-material](media/stingray-material.jpg)

När du har tilldelat ditt material till din till gång och namngett det kan du nu fortsätta med att tilldela olika texturer. Följande bilder innehåller information om varje struktur typ passar in i PBR-materialet. Med `Stingray PBR` materialet kan du välja vilka attribut du kan aktivera, så innan du kan använda `plug in` dina textur Maps måste du aktivera relevanta attribut: 

![Material konfiguration](media/material-setup.jpg)

> [!TIP]
Det är en bra idé att namnge ditt material på lämpligt sätt, med hänsyn till deras användning och/eller typ. Ett material som ska användas i en unik del kan komma att namnges för den delen medan ett material som kan användas på ett större antal områden kan namnges för dess egenskaper eller typ.

Tilldela dina texturer på följande sätt:

![Textur konfiguration](media/texture-setup.jpg)

När ditt PBR-material har skapats och kon figurer ATS är det värt att tänka på [instans objekt](../../how-tos/conversion/configure-model-conversion.md#instancing) i din scen. Informerar liknande objekt i din scen – till exempel nötter, bultar, skruv spolar, i stort sett alla objekt som är desamma kan ge betydande besparingar i fil storlek. Instanser av ett huvud objekt kan ha sin egen skalning, rotation och transformering så att de kan placeras efter behov i din scen. I Maya är indelnings processen enkel.

* I `Edit` menyn går du till `Duplicate Special` och öppnar `Options` , 
* I `Duplicate Special` alternativ växla `Geometry Type` från `Copy` till `Instance` , 
* Klicka på `Duplicate Special`

![Instans](media/instancing.jpg)

Den här åtgärden skapar en instans av objektet som kan flyttas roterat eller skalas oberoende av dess överordnade och andra instanser av den överordnade. 
>Men eventuella ändringar som du gör i en instans i komponent läge överförs till alla instanser av ditt objekt, så om du arbetar med ett instans objekt komponenter – hörn, måste polygonens ansikten vara först noga med att du vill ha de ändringar du gör för att påverka alla dessa instanser.

I exempel scenen har varje enskild Box-objekt inträffat. Den här åtgärden kommer att ha relevans när vi exporterar scenen till FBX-format.

![Översikt över scen](media/scene-overview.jpg)

>Bästa praxis när det gäller indelningen i din scen är att skapa dem när du går vidare, eftersom det är mycket svårt att ersätta kopior med instans objekt senare. 

## <a name="fbx-export-process"></a>FBX export process

Vi kan nu gå vidare till FBX-exporten av din scen-eller scen till gångar. I allmänhet är det bra när du exporterar till gångar för att endast välja för att exportera de objekt/till gångar från din scen som du vill ha. Om du har 100 objekt i en scen men du bara vill använda 30 av dem, finns det ingen punkt för att exportera hela scenen. Så om du inte är nöjd med att exportera hela scenen, gör du ditt val och går till:

* `File` > `Export Selection`och i dialog rutan exportera går du till slutet och anger `Files of Type` till `FBX Export` . I det här fönstret visas export inställningarna för FBX. De primära inställningarna för FBX-export är markerade i rött i bilden nedan.

![FBX-export](media/FBX-exporting.jpg)

Beroende på dina behov – till exempel kanske du vill skicka en till gång till en klient, men inte vill skicka ett stort antal textur-filer med till gången, kan du välja att bädda in texturerna i den exporterade FBX-filen. Det här alternativet innebär att du bara har en fil att paketera, men det ökar storleken på den FBX till gången betydligt. Du kan aktivera alternativet att bädda in texturer genom att växla till `Embed Media` alternativet som visas nedan.

> [!TIP]
> Observera att filen i det här fallet har namngetts för att avspegla det här tillståndet. Detta är en bra metod för att hålla koll på dina till gångar. 

När du har konfigurerat konfigurationen för export klickar du på knappen "Exportera markering" längst ned till höger.

![Bädda in media](media/embedding-media.jpg)

## <a name="conclusion"></a>Slutsats

I allmänhet ser den här typen av material mer verklighetstroget eftersom det baseras på en riktig världs fysik av ljus. Den skapar en extra fördjupad inverkan som scenen finns i den verkliga världen.

## <a name="next-steps"></a>Nästa steg

Nu vet du de viktigaste funktionerna för att ställa in material med avancerad belysning för objekt i en scen och exportera det till FBX-format som stöds av ARR. Nästa steg är att konvertera FBX-filen och visualisera i ARR.

> [!div class="nextstepaction"]
> [Snabb start: konvertera en modell för åter givning](../../quickstarts\convert-model.md)