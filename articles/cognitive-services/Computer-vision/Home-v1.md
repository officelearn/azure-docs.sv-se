---
title: Vad är API för visuellt innehåll?
titlesuffix: Azure Cognitive Services
description: Via tjänsten API för visuellt innehåll har utvecklare tillgång till avancerade algoritmer för bearbetning av bilder och returnering av information.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 08/10/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 35002ccfc294b59a13e6826b4ca75a1ab6c68b62
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870307"
---
# <a name="what-is-computer-vision-api-version-10"></a>Vad är visuellt innehåll API version 1.0?

> [!IMPORTANT]
> En ny version av API för visuellt innehåll är nu tillgänglig, se:
>- [Översikt](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [API v2.0 för visuellt innehåll](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Det molnbaserade API:et för visuellt innehåll ger utvecklare åtkomst till avancerade algoritmer för bearbetning av bilder och returnering av information. Genom att ladda upp en bild eller ange en bilds webbadress kan Microsofts algoritmer för visuellt innehåll analysera visuellt innehåll på olika sätt beroende på indata och användarens val. Med API för visuellt innehåll kan användare analysera bilder för att:
* Tagga bilder baserat på innehållet.
* Kategorisera bilder.
* Identifiera bilders typ och kvalitet.
* [Identifiera ansikten och returnera deras koordinater. ](#Faces)
* Identifiera domänspecifikt innehåll.
* Generera beskrivningar av innehållet.
* Använda optisk teckenläsning för att identifiera tryckt text i bilder.
* Identifiera handskriven text.
* Skilja mellan färgscheman.
* Flagga innehåll som är olämpligt för barn.
* Beskära foton som ska användas som miniatyrbilder.

## <a name="requirements"></a>Krav
* Indatametoder som stöds: Binära rawbildfiler i form av ett program-/oktettflöde eller bild-URL.
* Bildformat som stöds: JPEG, PNG, GIF, BMP.
* Storlek på bildfil: mindre än 4 MB.
* Bilddimensioner: större än 50 x 50 bildpunkter.

## <a name="tagging-images"></a>Tagga bilder
API för visuellt innehåll returnerar taggar som baseras på tusentals identifierbara objekt, levande varelser, landskap och åtgärder. I de fall där taggarna är tvetydiga eller dess betydelse inte är allmän kännedom ger API-svaren ”ledtrådar” för att tydliggöra taggarnas betydelse i en viss kontext. Taggar är inte ordnade som en taxonomi och det finns inga arvshierarkier. En samling innehållstaggar utgör grunden för en ”bildbeskrivning” som visas som språk som kan läsas av människor som är formaterade i fullständiga meningar. Observera att engelska för tillfället är det enda språket som stöds för bildbeskrivning.

När du laddar upp en bild eller anger en bild-URL skapar API för visuellt innehålls algoritmer taggar baserat på de objekt, levande varelser och åtgärder som identifierats i avbildningen. Taggar är inte begränsade till huvudföremålet på bilden, som till exempel en person i förgrunden, utan finns även för saker som bakgrund (inomhus eller utomhus), möbler, verktyg, växter, djur, accessoarer, saker och så vidare.

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
Förutom taggning och beskrivningar returnerar API för visuellt innehåll taxonomi-baserade kategorier som definierats i tidigare versioner. Kategorierna är ordnade som en taxonomi med ärftliga överordnade/underordnade hierarkier. Alla kategorier är på engelska. De kan användas fristående eller med våra nya modeller.

### <a name="the-86-category-concept"></a>Konceptet 86-kategori
Baserat på en lista över 86 begrepp som visas i följande diagram kan visuella funktioner som hittas i en bild kategoriseras i breda till specifika kategorier. Läs den fullständiga taxonomin i textformat i [Kategoritaxonomi](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analysera kategorier](./Images/analyze_categories.png)

Bild                                                  | Svar
------------------------------------------------------ | ----------------
![Kvinna tak](./Images/woman_roof.png)                 | personer
![Familjefoto](./Images/family_photo.png)             | people_crowd
![Gullig hund](./Images/cute_dog.png)                     | animal_dog
![Berg utomhus](./Images/mountain_vista.png)       | outdoor_mountain
![Visuell analys mat bröd](./Images/bread.png)       | food_bread

## <a name="identifying-image-types"></a>Identifierar bildtyper
Du kan kategorisera bilder på flera sätt. API för visuellt innehåll kan ange en boolesk flagga som visar om en bild är svartvit eller i färg. Det kan också ange en flagga som indikerar om en bild är en linjeteckning eller ej. Det kan också indikera om en bild är ClipArt eller ej och ange dess kvalitet på en skala på 0-3.

### <a name="clip-art-type"></a>Typ av ClipArt
Identifierar om en bild är ClipArt eller ej.  

Värde | Betydelse
----- | --------------
0     | Non-clip-art
1     | ambiguous
2     | normal-clip-art
3     | good-clip-art

Bild|Svar
----|----
![Visuellt innehåll identifierar ClipArt-ost](./Images/cheese_clipart.png)|3 good-clip-art
![Visuellt innehåll husgård](./Images/house_yard.png)|0 Non-clip-art

### <a name="line-drawing-type"></a>Linjeteckningstyp
Identifierar om en bild är en linjeteckning eller ej.

Bild|Svar
----|----
![Visuellt innehåll analyserar teckning av lejon](./Images/lion_drawing.png)|True
![Visuellt innehåll analyserar blommor](./Images/flower.png)|False

### <a name="faces"></a>Ansikten
Identifierar ansikten i bilder och genererar ansiktskoordinater och rektangel för ansikte, kön och ålder. Dessa visuella funktioner är en delmängd av metadata som har genererats för ansiktet. Använd API för ansiktsigenkänning för mer utförliga metadata för ansikten (ansiktsigenkänning, attitydbestämning med mera).  

Bild|Svar
----|----
![Visuellt innehåll analyserar kvinnoansikte på tak](./Images/woman_roof_face.png) | [ { "age": 23, "gender": "Female", "faceRectangle": { "left": 1379, "top": 320, "width": 310, "height": 310 } } ]
![Visuellt innehåll analyserar ansikte på mamma och dotter](./Images/mom_daughter_face.png) | [ { "age": 28, "gender": "Female", "faceRectangle": { "left": 447, "top": 195, "width": 162, "height": 162 } }, { "age": 10, "gender": "Male", "faceRectangle": { "left": 355, "top": 87, "width": 143, "height": 143 } } ]
![Visuellt innehåll analyserar ansikte i familjefoto](./Images/family_photo_face.png) | [ { "age": 11, "gender": "Male", "faceRectangle": { "left": 113, "top": 314, "width": 222, "height": 222 } }, { "age": 11, "gender": "Female", "faceRectangle": { "left": 1200, "top": 632, "width": 215, "height": 215 } }, { "age": 41, "gender": "Male", "faceRectangle": { "left": 514, "top": 223, "width": 205, "height": 205 } }, { "age": 37, "gender": "Female", "faceRectangle": { "left": 1008, "top": 277, "width": 201, "height": 201 } } ]


## <a name="domain-specific-content"></a>Domänspecifikt innehåll

Utöver taggning och översta kategorisering stöder API för visuellt innehåll även specialiserad (eller domänspecifika) information. Specialiserad information kan implementeras som en fristående metod eller med den övergripande kategoriseringen. Denna information kompletterar 86-kategoritaxonomin genom att lägga till domänspecifika modeller.

Den enda specialiserade informationen som stöds för närvarande är igenkänning av kändisar och landmärken. De är domänspecifika sökvillkor för kategorierna personer och persongrupper samt landmärken runt om i världen.

Det finns två alternativ för att använda domänspecifika modeller:

### <a name="option-one---scoped-analysis"></a>Alternativ ett – begränsad analys
Analysera bara en vald modell genom att aktivera ett HTTP POST-anrop. För det här alternativet, om du vet vilken modell som du vill använda, kan du ange modellens namn och bara få information som är relevant för den modellen. Du kan till exempel använda det här alternativet för att bara söka efter kändisigenkänning. Svaret innehåller en lista över potentiella matchande kändisar, tillsammans med deras förtroendepoäng.

### <a name="option-two---enhanced-analysis"></a>Alternativ två – förbättrad analys
Analysera för att tillhandahålla ytterligare information som rör kategorier från 86-kategoritaxonomin. Alternativet är tillgängligt för program där du vill få allmän bildanalys utöver information från en eller flera domänspecifika modeller. När den här metoden används anropas 86 kategoritaxonomi-klassificeraren först. Om någon av kategorierna motsvarar en känd eller motsvarande modell kommer ett andra klassificeraranrop att ske. Till exempel om ”information = all” eller ”information” inkludera ”kändisar”, metoden anropar metoden klassificeraren kändisar innan 86-kateogeriklassificeraren anropas. Taggar som börjar med ”people_” omfattas i resultatet.

## <a name="generating-descriptions"></a>Skapa beskrivnignar 
API för visuellt innehåller algoritmer för att analysera innehållet i en bild. Denna analys utgör grunden för en ”bildbeskrivning” som visas som språk som kan läsas av människor i fullständiga meningar. Beskrivningen sammanfattar vad som finns i bilden. Algoritmer för API:et för visuellt innehåll genererar olika beskrivningar som baseras på de objekt som identifierats i bilden. Beskrivningarna utvärderas och förtroendepoäng genereras. Sedan returneras en lista som är sorterad efter högsta till lägsta förtroendepoäng. Du kan hitta ett exempel på en robot som använder den här tekniken för att skapa bildtexter [här](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Exempel på att skapa beskrivning
![Svartvita byggnader](./Images/bw_buildings.png) '
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

## <a name="perceiving-color-schemes"></a>Identifiera färgscheman
Algoritmen för visuellt innehåll extraherar färger från en bild. Färgerna analyseras i tre olika kontexter: förgrund, bakgrund och helhet. De grupperas i 12 dominanta accentfärger. Dessa accentfärger är svarta, blå, brown, grå, grön, orange, rosa, lila, röd, mörkturkos, vit och gul. Beroende på färgerna i en bild returneras enkel svartvit eller accentfärger i hexadecimal-färgkoder.

Bild                                                       | Förgrund |Bakgrund| Färger
----------------------------------------------------------- | --------- | ------- | ------
![Berg utomhus](./Images/mountain_vista.png)            | Svart     | Svart   | Vit
![Visuellt innehåll analyserar blommor](./Images/flower.png)               | Svart     | Vit   | Vit, svart, grön
![Visuellt innehåll tågstation](./Images/train_station.png) | Svart     | Svart   | Svart

### <a name="accent-color"></a>Accentfärg
Färg som extraheras från en bild som utformats för att representera mest fantastiska färgen för användaren via en blandning av dominanta färger och mättnad.

Bild                                                       | Svar
----------------------------------------------------------- | ----
![Berg utomhus](./Images/mountain_vista.png)            | #BC6F0F
![Visuellt innehåll analyserar blommor](./Images/flower.png)               | #CAA501
![Visuellt innehåll tågstation](./Images/train_station.png) | #484B83


### <a name="black--white"></a>Svart och vit
Boolesk flagga som anger om en bild är svart och vit.

Bild                                                      | Svar
---------------------------------------------------------- | ----
![Visuellt innehåll analyserar byggnad](./Images/bw_buildings.png)      | True
![Visuellt innehåll husgård](./Images/house_yard.png)      | False

## <a name="flagging-adult-content"></a>Markera innehåll som är olämpligt för barn
Bland de olika visuella kategorierna är gruppen med innehåll som är olämpligt för barn som känner igen sådant material och begränsar bilder med sexuellt innehåll. Filtret för stötande och olämpligt innehåll kan ställas in på en skala för att passa dina preferenser.

## <a name="optical-character-recognition-ocr"></a>Optisk teckenläsning (OCR)
OCR-teknik identifierar textinnehåll i en bild och extraherar den identifierade texten till en maskinläsningsbar teckenström. Du kan använda resultatet för sökning och flera andra ändamål som medicinska journaler, säkerhet och banktjänster. Den identifierar språket automatiskt. OCR sparar tid och underlättar för användarna genom att göra det möjligt för dem att helt enkelt ta ett kort istället för att skriva ner informationen för hand.

OCR stöder 25 olika språk. Dessa språk är: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, serbiska (kyrillisk och latinsk) slovakiska, spanska, svenska och turkiska.

Om det behövs korrigerar OCR rotationen av den tolkade texten i grader kring den vågräta bildaxeln. OCR anger ramkoordinater för varje ord enligt nedanstående bild.

![Översikt över OCR](./Images/vision-overview-ocr.png) Krav för OCR:
- Storleken på den inkommande bilden måste vara mellan 40 x 40 och 3200 x 3200 bildpunkter.
- Bilden får inte vara större än 10 megapixlar.

Den inmatade bilden kan roteras i valfri vinkel som är delbar med 90 samt små vinklar upp till 40 grader.

Hur korrekt referensdatorns textigenkänning är beror på bildkvaliteten. En felaktig inläsning kan ha orsakats av följande situationer:
- Suddiga bilder.
- Handskriven eller kursiv text.
- Konstnärliga teckensnittsstilar.
- Liten teckenstorlek.
- Komplexa bakgrunder, skuggor eller motljus över text eller perspektivförvrängningar.
- För stora eller saknade versaler i början på ord
- Nedsänkt text, upphöjd eller genomstruken text.

Begränsningar: På foton där texten är dominant kan falska positiva identifieringar komma från delvis identifierade ord. På vissa foton, särskilt foton utan text varierar precisionen mycket beroende på bildtypen.

## <a name="recognize-handwritten-text"></a>Identifiera handskriven text
Med den här tekniken kan du identifiera och extrahera handskriven text från anteckningar, brev, uppsatser, whiteboardtavlor, formulär och så vidare. Den fungerar med olika ytor och bakgrunder, som vitt papper, gula anteckningslappar och whiteboardtavlor.

Med handskriven textigenkänning sparar du tid och kraft och blir effektiv. Du kan öka produktiviteten eftersom du kan ta bilder av text istället för att behöva skriva ner allt. Det gör det möjligt att digitalisera anteckningar. Med den här digitaliseringen kan du implementera snabba och enkla sökningar. Du slipper också den oreda som uppstår när du har för mycket papper omkring dig.

Indatakrav:
- Bildformat som stöds: JPEG, PNG och BMP.
- Bildfilstorleken måste vara mindre än 4 MB.
- Bilddimensioner måste vara minst 40 x 40, högst 3200 x 3200.

Obs! Den här tekniken är för tillfället tillgänglig som förhandsversion och endast för engelsk text.

## <a name="generating-thumbnails"></a>Generera miniatyrer
En miniatyr är en liten representation av en bild. Olika enheter, till exempel telefoner, surfplattor och datorer skapar ett behov av andra storlekar på användarupplevelser, (UX) layouter och miniatyrer. Med hjälp av smart beskärning, löser API för visuellt innehåll problemet.

När du har överfört en bild skapas en högkvalitativ miniatyr hämtar och algoritmen för API för visuellt innehåll analyserar objekten i bilden. Visuellt innehåll beskär bilden för att passa kraven för ”det intressanta området” (ROI). Utdata visas inom ett särskilt ramverk enligt bilden nedan. Den genererade miniatyrbilden kan vid behov anges med proportioner som skiljer sig från proportionerna på den ursprungliga bilden.

Algoritmen för miniatyr fungerar på följande sätt:

1. Den tar bort störande element från bilden och känner igen det viktigaste objektet, ”det intressanta området” (ROI).
2. Den beskär bilden baserat på det identifierade intressanta området.
3. Den ändrar proportionerna så att den passar målmåttet.

![Miniatyrbilder](./Images/thumbnail-demo.png)
