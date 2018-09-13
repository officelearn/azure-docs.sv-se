---
title: API för visuellt innehåll för Microsoft Cognitive Services | Microsoft Docs
description: Använd avancerade algoritmer i API för visuellt innehåll för att bearbeta bilder och returnerar information i Microsoft Cognitive Services.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 84d931ad79bf32b39a4d771f6afd1c9a05ad2395
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "44714827"
---
# <a name="what-is-computer-vision-api-version-10"></a>Vad är datorn Vision API Version 1.0?

> [!IMPORTANT]
> En ny version av API för visuellt innehåll är nu tillgänglig, se:
>- [Översikt](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Datorn Vision API-Version 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Det molnbaserade API:et för visuellt innehåll ger utvecklare åtkomst till avancerade algoritmer för bearbetning av bilder och returnering av information. Genom att ladda upp en bild eller ange en bilds webbadress kan Microsofts algoritmer för visuellt innehåll analysera visuellt innehåll på olika sätt beroende på indata och användarens val. Med API för visuellt innehåll kan användare analysera bilder för att:
* [Tagga bilder baserat på innehållet.](#Tagging)
* [Kategorisera bilder.](#Categorizing)
* [Identifiera resursens typ och kvaliteten på bilder.](#Identifying)
* [Identifiera ansikten och returnera sina koordinater. ](#Faces)
* [Identifiera domänspecifika innehåll.](#Domain-Specific)
* [Generera beskrivningar av innehållet.](#Descriptions)
* [Använd optisk teckenläsning för att identifiera tryckt text i bilder.](#OCR)
* [Identifiera handskriven text.](#RecognizeText)
* [Skilja färgscheman.](#Color)
* [Flagga för vuxet innehåll.](#Adult)
* [Beskär foton som ska användas som miniatyrbilder.](#Thumbnails)

## <a name="requirements"></a>Krav
* Stöds inmatningsmetoder: Raw binära i form av ett program/oktett stream eller bild-URL-bild.
* Bildformat som stöds: JPEG, PNG, GIF, BMP.
* Bild filstorlek: mindre än 4 MB.
* Bild dimension: är större än 50 x 50 bildpunkter.

## <a name="tagging-images"></a>Tagga bilder
API för visuellt innehåll returnerar taggar som baseras på över 2 000 identifierbara objekt, levande varelser, landskap och åtgärder. När taggar är tvetydigt eller inte är allmän kännedom ger API-svaren ”tips” för att tydliggöra taggarnas i samband med en känd inställning betydelse. Taggar är inte ordnade som en taxonomi och inga arvshierarkier finns. En samling innehåll taggar utgör grunden för en avbildning ”beskrivning” visas som mänskliga läsbara språk som är formaterade i fullständiga meningar. Observera att nu engelska är det enda språket som stöds för Bildbeskrivning.

När du laddar upp en bild eller ange en bild-URL, utdata API för visuellt innehålls algoritmer taggar baserat på de objekt, levande varelser och åtgärder som identifierats i avbildningen. Taggar är inte begränsat till huvudsakliga ämnet, till exempel en person i förgrunden, men innehåller även inställningen (inom eller utomhus), möbler, verktyg, anläggningar, djur, Tillbehör, Tillbehör osv.

### <a name="example"></a>Exempel
![House_Yard](./Images/house_yard.png) '

```json
Returned Json
{
   'tags':[
      {
         "name":"grass",
         "confidence":0.999999761581421
      },
      {
         "name":"outdoor",
         "confidence":0.999970674514771
      },
      {
         "name":"sky",
         "confidence":999289751052856
      },
      {
         "name":"building",
         "confidence":0.996463239192963
      },
      {
         "name":"house",
         "confidence":0.992798030376434
      },
      {
         "name":"lawn",
         "confidence":0.822680294513702
      },
      {
         "name":"green",
         "confidence":0.641222536563873
      },
      {
         "name":"residential",
         "confidence":0.314032256603241
      },
   ],
}
```
## <a name="categorizing-images"></a>Kategorisera bilder
Förutom taggning och beskrivningar returnerar API för visuellt innehåll taxonomi-baserade kategorier som definierats i tidigare versioner. Kategorierna är ordnade som en taxonomi med överordnad/underordnad ärftliga hierarkier. Alla kategorier är på engelska. De kan användas fristående eller med våra nya modeller.

### <a name="the-86-category-concept"></a>Konceptet 86-kategori
Baserat på en lista över 86 begrepp som visas i följande diagram, kan visuella funktioner som hittas i en bild kategoriseras sträcker sig från bred till specifika. Läs den fullständiga taxonomifält i textformat [Kategoritaxonomi](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analysera kategorier](./Images/analyze_categories.png)

Bild                                                  | Svar
------------------------------------------------------ | ----------------
![Kvinna tak](./Images/woman_roof.png)                 | personer
![Programvarufamiljer foto](./Images/family_photo.png)             | people_crowd
![Söta hund](./Images/cute_dog.png)                     | animal_dog
![Utomhus Mountain](./Images/mountain_vista.png)       | outdoor_mountain
![Visuellt innehåll analyserar matbröd](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identifiera bildtyper
Det finns flera sätt att kategorisera bilder. API för visuellt innehåll kan ange en boolesk flagga som visar om en bild är svartvit eller i färg. Det kan också ange en flagga som indikerar om en bild är en Linjeteckning eller ej. Det kan också indikera om en bild är ClipArt eller ej och ange dess kvaliteten därför på en skala på 0-3.

### <a name="clip-art-type"></a>Clip-art-typ
Identifierar om en bild är ClipArt eller ej.  

Värde | Betydelse
----- | --------------
0     | Icke-ClipArt
1     | tvetydig
2     | Normal ClipArt
3     | bra ClipArt

Bild|Svar
----|----
![Visuellt innehåll analyserar ost ClipArt](./Images/cheese_clipart.png)|3 bra--ClipArt
![Visuellt innehåll analyserar House Yard](./Images/house_yard.png)|0 icke--ClipArt

### <a name="line-drawing-type"></a>Linjeteckningstyp
Identifierar om en bild är en Linjeteckning eller ej.

Bild|Svar
----|----
![Visuellt innehåll analyserar Lion ritning](./Images/lion_drawing.png)|True
![Visuellt innehåll analyserar blommor](./Images/flower.png)|False

### <a name="faces"></a>Ansikten
Identifierar ansikten i bilder och genererar ansikts-koordinater, rektangel för ansikte, kön och ålder. Dessa visuella funktioner är en delmängd av metadata som har genererats för ansikte. Använda Ansikts-API för mer omfattande metadata som genererats för ansikten (ansiktsigenkänning identifiering, posering identifiering med mera).  

Bild|Svar
----|----
![Visuellt innehåll analyserar kvinna tak ansikte](./Images/woman_roof_face.png) | [{”age”: 23 ”kön”: ”kvinnliga”, ”faceRectangle”: {”left”: 1379, ”top”: 320, ”bredd”: 310, ”höjd”: 310}}]
![Visuellt innehåll analyserar Mom dotter ansikte](./Images/mom_daughter_face.png) | [{”age”: 28, ”kön”: ”kvinnliga”, ”faceRectangle”: {”left”: 447, ”top”: 195, ”bredd”: 162, ”höjd”: 162}}, {”age”: 10, ”kön”: ”manlig”, ”faceRectangle”: {”left”: 355, ”top”: 87, ”bredd”: 143, ”höjd”: 143}}]
![Visuellt innehåll analyserar Family Phot ansikte](./Images/family_photo_face.png) | [{”age”: 11, ”kön”: ”manlig”, ”faceRectangle”: {”left”: 113, ”top”: 314, ”bredd”: 222, ”höjd”: 222}}, {”age”: 11, ”kön”: ”kvinnliga”, ”faceRectangle”: {”left”: 1200, ”top”: 632, ”bredd”: 215, ”höjd”: 215}}, {”age”: 41, ”kön”: ”manlig” ”, faceRectangle ”: {” left ”: 514,” top ”: 223,” bredd ”: 205,” höjd ”: 205}}, {” age ”: 37,” kön ”:” kvinnliga ”,” faceRectangle ”: {” left ”: 1008,” top ”: 277,” bredd ”: 201,” höjd ”: 201}}]


## <a name="domain-specific-content"></a>Domänspecifika innehåll

Dessutom stöder också till taggning och översta kategorisering, API för visuellt innehåll specialiserad (eller domänspecifika) information. Särskild information kan implementeras som en fristående metod eller med den övergripande kategoriseringen. Den fungerar som ett sätt att ytterligare förfina 86 kategoritaxonomi genom tillägg av domänspecifika modeller.

Den enda särskilda information som stöds för närvarande igenkänning av kändisar och landmärken erkännande. De är domänspecifika sökvillkoren för personer och kategorierna för personer och landmärken runt om i världen.

Det finns två alternativ för att använda domänspecifika modeller:

### <a name="option-one---scoped-analysis"></a>Alternativet One – begränsade analys
Analysera bara en vald modell, genom att aktivera en HTTP POST-anrop. För det här alternativet om du vet vilken modell som du vill använda kan du ange modellens namn och du bara få information relevanta för den modellen. Du kan till exempel använda det här alternativet för att bara söka efter kändisigenkänning. Svaret innehåller en lista över potential matchar kändisar, tillsammans med sina förtroende poäng.

### <a name="option-two---enhanced-analysis"></a>Alternativ två - förbättrad analys
Analysera för att tillhandahålla ytterligare information som rör kategorier från den 86 kategoritaxonomi. Det här alternativet är tillgängligt för användning i program där användare vill få allmän bildanalys utöver information från en eller flera domänspecifika modeller. När den här metoden har anropats kallas 86 kategoritaxonomi klassificeraren först. Om någon av kategorierna matchar som kända/matchar modeller följer en igenom av klassificerare anrop. Till exempel om ”information = all” eller ”information” inkludera ”kändisar”, metoden anropar kändisar klassificeraren när 86-kategori-klassificerare kallas. Taggar som börjar med ”people_” omfattar resultatet.

## <a name="generating-descriptions"></a>Generera beskrivningar 
API för visuellt innehålls algoritmer analysera innehållet i en bild. Den här analysen utgör grunden för en ”beskrivning” visas som läsbara språk i fullständiga meningar. Beskrivningen sammanfattar vad som finns i bild. API för visuellt innehålls algoritmer generera olika beskrivningar baserat på de objekt som identifierats i avbildningen. Beskrivningarna utvärderas och förtroendepoäng genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng. Ett exempel på en robot som använder den här tekniken för att skapa avbildningen undertexter kan hittas [här](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Exempel beskrivning Generation
![B & W byggnader](./Images/bw_buildings.png) '
```json
 Returned Json

'description':{
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a large city',
         'confidence':0.607638706850331
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a photo of a large city',
         'confidence':0.577256764264197
      }
   ]   
   "captions":[
      {
         "type":"phrase",
         'text':'a black and white photo of a city',
         'confidence':0.538493271791207
      }
   ]   
   'description':[
      "tags":{
         "outdoor",
         "city",
         "building",
         "photo",
         "large",
      }
   ]
}
```

## <a name="perceiving-color-schemes"></a>Perceiving färgscheman
Algoritmen för visuellt innehåll extraherar färger från en avbildning. Färgerna analyseras i tre olika kontexter: förgrund, bakgrund och helhet. De grupperas i 12 dominanta accentfärger. Dessa accentfärger är svarta, blå, brown, grå, grön, orange, rosa, lila, röd, mörkturkos, vit och gul. Beroende på färger i en bild returneras enkel svartvit eller accentfärger i hexadecimal färgkoder.

Bild                                                       | förgrund |Bakgrund| Färger
----------------------------------------------------------- | --------- | ------- | ------
![Utomhus Mountain](./Images/mountain_vista.png)            | Svart     | Svart   | Vit
![Visuellt innehåll analyserar blommor](./Images/flower.png)               | Svart     | Vit   | Vit, svart, grön
![Visuellt innehåll analyserar träna Station](./Images/train_station.png) | Svart     | Svart   | Svart

### <a name="accent-color"></a>Accentfärg
Färg som extraheras från en avbildning som utformats för att representera mest fantastiska färgen för användare via en blandning av dominanta färger och mättnad.

Bild                                                       | Svar
----------------------------------------------------------- | ----
![Utomhus Mountain](./Images/mountain_vista.png)            | #BC6F0F
![Visuellt innehåll analyserar blommor](./Images/flower.png)               | #CAA501
![Visuellt innehåll analyserar träna Station](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Svart och vit
Boolesk flagga som anger om en bild är svart och vit eller inte.

Bild                                                      | Svar
---------------------------------------------------------- | ----
![Visuellt innehåll analyserar byggnad](./Images/bw_buildings.png)      | True
![Visuellt innehåll analyserar House Yard](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Flagga för vuxet innehåll
Bland de olika visuella kategorierna är gruppen vuxet och vågat innehåll, vilket möjliggör identifiering av material som är olämpligt för barn och begränsar visningen av avbildningar som innehåller sexuellt innehåll. Filtret för vuxet och vågat innehåll kan ställas in på en glidande skala för användarens inställningar.

## <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)
OCR-teknik identifierar textinnehåll i en bild och extraherar identifierade texten till en maskinläsningsbar teckenström. Du kan använda resultatet för sökning och flera andra ändamål som medicinska journaler, säkerhet och banktjänster. Den identifierar språket automatiskt. OCR sparar tid och underlättar för användarna genom att de kan ta foton av text istället för att skriva texten.

OCR stöder 25 olika språk. Dessa språk är: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, Serbiska (kyrillisk och latinsk) Slovakiska, spanska, svenska och turkiska.

Om det behövs, korrigerar OCR rotationen av den tolkade texten i grader kring vågrät bild-axeln. OCR ger ramens koordinaterna i varje ord som visas i under bilden.

![Översikt över OCR](./Images/vision-overview-ocr.png) krav för OCR:
- Storleken på den inkommande avbildningen måste vara mellan 40 x 40 och 3200 x 3200 bildpunkter.
- Bilden får inte vara större än 10 megapixlar.

Inmatad bild kan roteras dubbla av 90 grader plus en liten vinkel av upp till ' 40 grader.

Hur korrekt referensdatorns textigenkänning beror på kvaliteten på avbildningen. En felaktig läsning kan ha orsakats av följande situationer:
- Suddiga bilder.
- Handskriven eller kursiv text.
- Konstnärlig teckensnittsstilar.
- Liten textstorlek.
- Komplexa bakgrunder, skuggor eller antireflex via text eller ett perspektiv förvrängningar.
- Stora eller saknas versaler i början av ord
- Nedsänkt text, upphöjd eller genomstruken text.

Begränsningar: På foton där texten är dominanta, falska positiva identifieringar kan komma från delvis identifierade ord. På vissa foton, särskilt foton någon text varierar precision mycket beroende på vilken typ av avbildning.

## <a name="recognize-handwritten-text"></a>Identifiera handskriven Text
Den här tekniken kan du identifiera och extrahera handskriven text från anteckningar, brev, uppsatser, whiteboardtavlor, formulär, osv. Den fungerar med olika ytor och bakgrunder, som vitt papper, gula anteckningslappar och whiteboardtavlor.

Med handskriven textigenkänning sparar du tid och kraft och blir effektiv. Du kan öka produktiviteten eftersom du kan ta bilder av text istället för att behöva skriva ner allt. Det gör det möjligt att digitalisera anteckningar. Den här digitization kan du implementera snabba och enkla sökningar. Du slipper också den oreda som uppstår när du har för mycket papper omkring dig.

Krav för indata:
- Bildformat som stöds: JPEG, PNG och BMP.
- Bildens filstorlek måste vara mindre än 4 MB.
- Bilddimensioner måste vara minst 40 x 40, högst 3200 x 3200.

Obs! Den här tekniken är för tillfället tillgänglig som förhandsversion och endast för engelsk text.

## <a name="generating-thumbnails"></a>Generera miniatyrer
En miniatyr är en liten representation av en bilden. Olika enheter, till exempel telefoner, surfplattor och datorer skapa behovet av en annan användare användarupplevelsen (UX) layouter och miniatyrer. Med hjälp av smart beskärning, löser API för visuellt innehåll funktionen problemet.

När du har överfört en bild, en högkvalitativ miniatyr hämtar genereras och algoritmen för API för visuellt innehåll analyserar objekten i bilden. Den sedan Beskär bilden för att passa kraven för region of interest (ROI). Utdata visas inom ett särskilt ramverk enligt nedan bild. Den genererade miniatyrbilden kan anges med en aspekt ransonen som skiljer sig från den ursprungliga bilden för en användares behov proportioner.

Algoritmen för miniatyr fungerar på följande sätt:

1. Tar bort störande element från avbildningen och känner igen det viktigaste objektet, region of interest (ROI).
2. Beskär bilden baserat på identifierade region of interest.
3. Ändrar proportionerna så att den passar target miniatyrbilder.

![Miniatyrbilder](./Images/thumbnail-demo.png)
