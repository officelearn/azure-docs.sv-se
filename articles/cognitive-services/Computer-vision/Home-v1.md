---
title: Datorn Vision API för kognitiva Microsoft-tjänster | Microsoft Docs
description: Använd avancerade algoritmer i datorn Vision API för att bearbeta bilder och returnerar information i kognitiva Microsoft-tjänster.
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 08/10/2017
ms.author: kefre
ms.openlocfilehash: 86e0441c600162e479c678d3cb1dbeaad423ddb5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354744"
---
# <a name="what-is-computer-vision-api-version-10"></a>Vad är datorn Vision API Version 1.0?

> [!IMPORTANT]
> En ny version av datorn Vision API är nu tillgängliga, se:
>- [Översikt](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home)
>- [Datorn Vision API-Version 2.0](https://westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44)

Moln-baserad dator Vision API ger utvecklare med åtkomst till avancerade algoritmer för bearbetning av bilder och returnera information. Genom att ladda upp en bild eller ange en bild-URL, analysera Microsoft datorn Vision algoritmer visuellt innehåll på olika sätt beroende på indata och användarens val. Med API: et för datorn Vision analysera bilder till:
* [Tagga bilder baserat på innehåll.](#Tagging)
* [Kategorisera bilder.](#Categorizing)
* [Identifiera typ och kvalitet av avbildningar.](#Identifying)
* [Identifiera mänsklig ytor och returnera deras koordinater. ](#Faces)
* [Identifiera innehåll för domänen.](#Domain-Specific)
* [Generera beskrivningar av innehållet.](#Descriptions)
* [Använd OCR för att identifiera tryckt text i bilder.](#OCR)
* [Identifiera handskriven text.](#RecognizeText)
* [Skilja färgscheman.](#Color)
* [Flagga vuxet innehåll.](#Adult)
* [Beskär foton som ska användas som miniatyrbilder.](#Thumbnails)

## <a name="requirements"></a>Krav
* Stöd för inmatningsmetoder: Raw binära i form av ett program/oktett dataströmmen eller bild-URL-bild.
* Bildformat som stöds: JPEG, PNG, GIF, BMP.
* Filen Bildstorlek: mindre än 4 MB.
* Dimension för avbildningen: större än 50 x 50 bildpunkter.

## <a name="tagging-images"></a>Tagga bilder
Datorn Vision API returnerar taggar baserat på mer än 2000 okänt objekt, levande människor, landskap och åtgärder. När taggarna är tvetydigt eller inte vanliga knowledge API-svar innehåller tips om du för att förtydliga taggen i samband med en känd inställning. Taggar ordnas inte som en taxonomi och det finns inga arvshierarkier. En samling innehåll taggar utgör grunden för en avbildning ”beskrivning” visas som mänsklig läsbar språk som är formaterade i hela meningar. Observera att nu engelska är det enda språket som stöds för avbildningsbeskrivningen.

När du överför en bild eller ange en bild-URL, utdata datorn Vision API algoritmer taggar baserat på objekt, levande människor och åtgärder som anges i bilden. Taggning är inte begränsat till huvudsakliga ämnet, till exempel en person i förgrunden, men även inställningen (inom eller utomhus), möbler, verktyg, växter, djur, Tillbehör, Tillbehör osv.

### <a name="example"></a>Exempel
![House_Yard](./Images/house_yard.jpg) '

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
Förutom taggning och beskrivningar returnerar datorn Vision API taxonomi-baserade kategorier som definierats i tidigare versioner. Kategorierna är ordnade som en taxonomi med överordnade-underordnade ärftliga hierarkier. Alla kategorier är på engelska. De kan användas fristående eller med våra nya modeller.

### <a name="the-86-category-concept"></a>Konceptet 86 kategori
Baserat på en lista över 86 begrepp som visas i följande diagram kan visual funktioner som finns i en avbildning kategoriseras allt från bred till specifika. Fullständig taxonomi i textformat, se [kategori taxonomi](https://docs.microsoft.com/azure/cognitive-services/computer-vision/category-taxonomy).

![Analysera kategorier](./Images/analyze_categories.jpg)

Bild                                                  | Svar
------------------------------------------------------ | ----------------
![Kvinna tak](./Images/woman_roof.jpg)                 | personer
![Family foto](./Images/family_photo.jpg)             | people_crowd
![Gulliga hund](./Images/cute_dog.jpg)                     | animal_dog
![Utomhus Mountain](./Images/mountain_vista.jpg)       | outdoor_mountain
![Vision analysera matbröd](./Images/bread.jpg)       | food_bread

## <a name="identifying-image-types"></a>Identifiera bildtyper
Det finns flera sätt att kategorisera bilder. Datorn Vision API kan ange en boolesk flagga som indikerar om en avbildning är Monokromt eller färg. Det kan också ange en flagga som indikerar om en avbildning är en rad ritning eller inte. Det kan också ange om en avbildning är clip bilder eller inte och ange dess kvaliteten som exempel på en skala på 0-3.

### <a name="clip-art-type"></a>Clip senaste typ
Identifierar om en avbildning är clip bilder eller inte.  

Värde | Betydelse
----- | --------------
0     | Icke-clip-bilder
1     | tvetydig
2     | Normal clip art
3     | bra clip art

Bild|Svar
----|----
![Vision analysera ost ClipArt](./Images/cheese_clipart.jpg)|3 bra clip-senaste
![Vision analysera House Yard](./Images/house_yard.jpg)|0 icke-clip-bilder

### <a name="line-drawing-type"></a>Linjeteckningstyp
Identifierar om en avbildning är en rad ritning eller inte.

Bild|Svar
----|----
![Vision analysera Lion ritning](./Images/lion_drawing.jpg)|True
![Vision analysera blomma](./Images/flower.jpg)|False

### <a name="faces"></a>Ansikten
Identifierar mänsklig ytor i en bild och genererar ansikte koordinaterna, rektangel för framsidan, kön och ålder. Funktionerna visual är en delmängd av metadata som har genererats för yta. Använd Ansikts-API för mer omfattande metadata som genererats för ytor (ansikte identifiering, attityd identifiering med mera).  

Bild|Svar
----|----
![Vision analysera kvinna tak yta](./Images/woman_roof_face.png) | [{”ålder”: 23 ”kön”: ”hondjur”, ”faceRectangle”: {”left”: 1379, ”top”: 320, ”bredd”: 310, ”höjd”: 310}}]
![Vision analysera Mom dotter yta](./Images/mom_daughter_face.png) | [{”ålder”: 28 ”kön”: ”hondjur”, ”faceRectangle”: {”left”: 447, ”top”: 195, ”bredd”: 162, ”höjd”: 162}}, {”ålder”: 10, ”kön”: ”han”, ”faceRectangle”: {”left”: 355, ”top”: 87, ”bredd”: 143, ”höjd”: 143}}]
![Vision analysera Family Phot yta](./Images/family_photo_face.png) | [{”ålder”: 11, ”kön”: ”han”, ”faceRectangle”: {”left”: 113, ”top”: 314, ”bredd”: 222, ”höjd”: 222}}, {”ålder”: 11, ”kön”: ”hondjur”, ”faceRectangle”: {”left”: 1200, ”top”: 632, ”bredd”: 215, ”höjd”: 215}}, {”ålder”: 41 ”kön”: ”han” ”, faceRectangle ”: {” left ”: 514,” top ”: 223,” bredd ”: 205,” höjd ”: 205}}, {” ålder ”: 37,” kön ”:” hondjur ”,” faceRectangle ”: {” left ”: 1008,” top ”: 277,” bredd ”: 201,” höjd ”: 201}}]


## <a name="domain-specific-content"></a>Innehåll för domänen

Dessutom stöd till märkning och översta kategorisering datorn Vision API också för specialiserad (eller domänspecifika) information. Särskild information kan implementeras som en fristående metod eller med den övergripande kategoriseringen. Den fungerar som ett sätt att förfina 86 kategori taxonomi genom att lägga till specifika-modeller.

Endast särskilda information som stöds för närvarande ryktbarhet igenkänning och Landmärke igenkänning. De är domänspecifika förfining för personer och personer grupp kategorier och landmärken runtom i världen.

Det finns två alternativ för domänspecifika modeller:

### <a name="option-one---scoped-analysis"></a>Alternativet en - begränsade analys
Analysera endast valda modellen, genom att anropa en HTTP POST-anrop. För det här alternativet om du vet vilken modell som du vill använda kan du ange modellens namn, och du endast få information relevanta för den modellen. Du kan till exempel använda det här alternativet för att bara söka efter ryktbarhet igenkänning. Svaret innehåller en lista över potentiella matchar Kändisars, tillsammans med deras förtroende resultat.

### <a name="option-two---enhanced-analysis"></a>Alternativet två - förbättrad analys
Analysera för att ge ytterligare information som rör kategorier från 86 kategori taxonomi. Det här alternativet är tillgängligt för användning i program där användare vill få allmän bildanalys utöver information från en eller flera specifika-modeller. När den här metoden anropas, kallas 86 kategori taxonomi klassificeraren först. Om någon av kategorierna matchar som kända/matchande modeller följer en andra sändning av klassificerare anrop. Till exempel om ' information = all ”eller” information ”innehåller” Kändisars', ryktbarhet klassificeraren metodanrop när 86 kategori klassificeraren anropas. Resultatet innehåller taggar som börjar med 'people_'.

## <a name="generating-descriptions"></a>Generera beskrivningar 
Datorn Vision API algoritmer analysera innehållet i en bild. Den här analysen utgör grunden för en ”beskrivning” visas som läsbart språk i hela meningar. Beskrivningen sammanfattar vad som finns i avbildningen. Datorn Vision API algoritmer generera olika beskrivningar baserat på de objekt som identifierats i avbildningen. Beskrivningarna är varje utvärderas och en förtroende poäng genereras. En lista returneras beställda från högsta förtroende poäng till lägsta. Ett exempel på en bot som använder den här tekniken för att generera bildtexter hittar [här](https://github.com/Microsoft/BotBuilder-Samples/tree/master/CSharp/intelligence-ImageCaption).  

### <a name="example-description-generation"></a>Exempel beskrivning Generation
![B & W byggnader](./Images/bw_buildings.jpg) '
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
Algoritmen datorn Vision extraherar färger från en avbildning. Färgerna analyseras i tre olika kontexter: förgrund, bakgrund och helhet. De grupperas i tolv 12 företag accent färger. Dessa accent färger är svarta, blå, Jansson, grå, grönt, orange, rosa, lila, röd, mörkturkos, vit och gul. Beroende på färger i en bild returneras enkla Monokromt eller accent färger i hexadecimalt färgkoder.

Bild                                                       | Förgrunden |Bakgrund| Färger
----------------------------------------------------------- | --------- | ------- | ------
![Utomhus Mountain](./Images/mountain_vista.jpg)            | Svart     | Svart   | Vit
![Vision analysera blomma](./Images/flower.jpg)               | Svart     | Vit   | Vit, svart, grönt
![Vision analysera Train Station](./Images/train_station.jpg) | Svart     | Svart   | Svart

### <a name="accent-color"></a>Accent färg
Färgen som extraheras från en avbildning som utformats för att representera den mest häpnadsväckande färgen till användarna via en blandning av företag färger och mättnad.

Bild                                                       | Svar
----------------------------------------------------------- | ----
![Utomhus Mountain](./Images/mountain_vista.jpg)            | #BC6F0F
![Vision analysera blomma](./Images/flower.jpg)               | #CAA501
![Vision analysera Train Station](./Images/train_station.jpg) | #484B83


### <a name="black--white"></a>Svart och vitt
Boolesk flagga som indikerar om en avbildning är svart & vit eller inte.

Bild                                                      | Svar
---------------------------------------------------------- | ----
![Vision analysera byggnad](./Images/bw_buildings.jpg)      | True
![Vision analysera House Yard](./Images/house_yard.jpg)      | False

## <a name="flagging-adult-content"></a>Flagga vuxet innehåll
Är gruppen vuxna och dyr som möjliggör identifiering av material som är olämpligt för barn och begränsar visningen av avbildningar som innehåller sex innehåll mellan olika visuella kategorier. Filtret för vuxna och dyr innehåll identifiering kan ställas in på en glidande skala för användarens inställningar.

## <a name="optical-character-recognition-ocr"></a>OCR (OCR)
OCR teknik identifierar textinnehåll i en bild och extraherar identifierade texten till ett maskinläsbar teckenflöde. Du kan använda resultatet för sökning och många andra syften som medicinska poster, säkerhet och bank. Den identifierar språket automatiskt. OCR sparar tid och ger praktiska funktioner för användare genom att ge dem fotografera text i stället för att skriva text.

OCR stöder 25 språk. Dessa språk är: arabiska, förenklad kinesiska, traditionell kinesiska, tjeckiska, danska, nederländska, engelska, finska, franska, tyska, grekiska, ungerska, italienska, japanska, koreanska, norska, polska, portugisiska, rumänska, ryska, Serbiska (kyrilliska och latinska tecken) Slovakiska, spanska, svenska och turkiska.

Om det behövs, korrigerar OCR rotation av den tolkade texten i grader runt axelns vågrät bild. OCR ger ram koordinaterna för varje ord som visas i nedan bild.

![Översikt över OCR](./Images/vision-overview-ocr.png) krav för OCR:
- Storleken på inkommande avbildningen måste vara mellan 40 x 40 och 3200 x 3200 bildpunkter.
- Bilden får inte vara större än 10 megapixel.

Inkommande avbildningen kan roteras en multipel av 90 grader plus en liten vinkel av upp till ”40 grader.

Riktighet text recognition är beroende av kvaliteten på bilden. Läsa ett fel kan orsakas av följande situationer:
- Suddiga bilder.
- Handskriven eller kursiva text.
- Konstnärlig teckensnitt.
- Liten textstorlek.
- Komplexa bakgrund, skuggor eller antireflexbehandlad över text eller perspektiv förvrängning.
- Stora eller saknas versaler i början av ord
- Nedsänkt text, upphöjd eller genomstrykning text.

Begränsningar: På foton där text är företag, falska positiva identifieringar kan komma från delvis okänt ord. På vissa foton, särskilt foton utan någon text variera precision mycket beroende på vilken typ av bild.

## <a name="recognize-handwritten-text"></a>Identifiera handskriven Text
Den här tekniken kan du identifiera och extrahera handskriven text från anteckningar, bokstäver, uppsatser från, whiteboardtavlor, formulär, osv. Den fungerar med olika ytor och bakgrunder, som vitt papper, gula anteckningslappar och whiteboardtavlor.

Med handskriven textigenkänning sparar du tid och kraft och blir effektiv. Du kan öka produktiviteten eftersom du kan ta bilder av text istället för att behöva skriva ner allt. Gör det möjligt att digitalisera anteckningar. Den här digitization kan du implementera snabb och enkel sökning. Du slipper också den oreda som uppstår när du har för mycket papper omkring dig.

Inkommande krav:
- Bildformat som stöds: JPEG, PNG och BMP.
- Bildens filstorlek måste vara mindre än 4 MB.
- Bildstorleken måste vara minst 40 x 40, högst 3200 x 3200.

Obs! Den här tekniken är för tillfället tillgänglig som förhandsversion och endast för engelsk text.

## <a name="generating-thumbnails"></a>Genererar miniatyrer
En miniatyr är en liten representation av en bilden. Olika enheter, till exempel telefoner, surfplattor och datorer skapa behovet av en annan användare experience (UX) layouter och miniatyrer. Med hjälp av smart Beskär löste den här datorn Vision API-funktionen problemet.

Efter att ladda upp en bild, en hög kvalitet miniatyr hämtar genereras och datorn Vision API-algoritmen analyserar objekt i bilden. Den sedan Beskär bilden så att den passar organisationens krav region intressanta (ROI). Utdata hämtar visas i ett särskilt ramverk enligt nedan bild. Genererade miniatyren kan anges med en aspekt ransonen som skiljer sig från den ursprungliga avbildningen för en användares behov proportioner.

Algoritmen miniatyr fungerar på följande sätt:

1. Tar bort störande element från avbildningen och identifierar Huvudsyftet region intressanta (ROI).
2. Beskär bilden baserat på den identifiera regionen av intresse.
3. Ändras så att den passar mål miniatyr proportionerna.

![Miniatyrbilder](./Images/thumbnail-demo.png)
