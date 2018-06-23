Vissa Bing svar innehålla URL: er att miniatyrbilder som hanteras av Bing. Du kan ändra storlek och Beskär miniatyrbilder. 

> [!NOTE]
> Kontrollera storleken och med miniatyren tillhandahålla ett scenario för sökning och respektera tredje parts rättigheter som krävs av Bing Search API-användning och visa krav.


Om du vill ändra storlek på en bild, inkludera bredd (b) och höjd () fråga parametrar i på miniatyrbilden URL. Ange bredd och höjd i bildpunkter. Exempel:  
  
`https://<host>/th?id=JN.5l3yzwy%2f%2fHj59U6XhssIQ&pid=Api&w=200&h=200`  
  
Om du ändrar storlek på bilden bevaras dess proportionerna. Om du vill behålla proportionerna kan vit utfyllnad att lägga till kant för bilden. Om du ändrar storlek på en 480 x 359 avbildningen till 200 x 200 utan Beskär full bredd innehåller bilden men höjd innehåller 25 bildpunkter vit utfyllnad högst upp och längst ned i bilden. Samma skulle vara fallet om bilden var 359 x 480 utom till vänster och höger kantlinjer skulle innehålla vit utfyllnad. Om du Beskär bilden läggs inte vit utfyllnad.  

 
Följande bild visar den ursprungliga storleken för en miniatyrbild (480 x 300).  
  
![Den ursprungliga liggande avbildning](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape.PNG)  
  
Följande bild visar bildens storlek ska ändras till 200 x 200. Proportionerna underhålls och de övre och nedre kantlinjerna utfyllnad med vitt (svart kantlinje ingår att visa utfyllnaden).  
  
![Ändrade liggande bild](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape-resized.PNG)  



Om du anger dimensioner som är större än bildens ursprungliga Breddoch höjd utfyllt avbildningen med vitt på den vänstra och övre kantlinjen.  
  
Beskär bilden genom att inkludera c (Beskär) Frågeparametern. Följande är möjliga värden som du kan ange.  
  
- 4&mdash;nedsatt syn förhållandet  
- 7&mdash;förhållandet för smartkort  
  
Om du begär Smart förhållandet Beskär (c = 7), bilden beskärs från mitten av avbildningens region intressanta passiv Behåll bildens proportionerna. Intressant område är området bildens Bing anger innehåller de flesta importera delar. Nedan visas ett exempel region av intresse.  
  
![Intressant område](./media/cognitive-services-bing-resize-crop/bing-resize-crop-regionofinterest.PNG)

Om du ändrar storlek på en avbildning och begära Smart förhållandet Beskär har bilden minskats till den begärda storleken samtidigt proportionerna. Bilden beskärs sedan baserat på de nya storleken dimensionerna. Till exempel om storleksändrade bredden är mindre än eller lika med höjden, beskärs bilden till vänster och höger i mitten av intressant område. Annars beskärs bilden upp och längst ned i mitten av intressant område.  
  
 
Nedan visas minskas till 200 x 200 med Smart förhållandet Beskär bilden.  
  
![Liggande bilden beskärs till 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x200c7.PNG)
  
Nedan visas minskas till 200 x 100 med Smart förhållandet Beskär bilden.  
   
![Liggande bilden beskärs till 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape200x100c7.PNG)
  
Nedan visas minskas till 100 x 200 med Smart förhållandet Beskär bilden.  
  
![Liggande bilden beskärs till 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-landscape100x200c7.PNG)



Om Bing inte kan avgöra intressant bildens område, använder Bing hemlig förhållandet Beskär.  
  
Om du begär hemlig förhållandet Beskär (c = 4), Bing används följande regler Beskär bilden.  
  
- Om (ursprungliga bredd / ursprungliga avbildningen höjd) < (begärt bredd / begärda avbildningen höjd) bilden mäts från övre vänstra hörnet och beskäras längst ned.  
- Om (ursprungliga bredd / ursprungliga avbildningen höjd) > (begärt bredd / begärda avbildningen höjd) avbildningen mätt från center och beskäras till vänster och höger.  



Nedan visas ett stående avbildning som 225 x 300.  
  
![Ursprungliga direkt konsumtion avbildning](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower.PNG)
  
Nedan visas minskas till 200 x 200 med hemlig förhållandet Beskär bilden. Bilden mäts från det övre vänstra hörnet ledde till längst ned i bilden beskärs.  
  
![Direkt konsumtion bilden beskärs till 200 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x200c4.PNG)
  
Nedan visas minskas till 200 x 100 med hemlig förhållandet Beskär bilden. Bilden mäts från det övre vänstra hörnet ledde till längst ned i bilden beskärs.  
  
![Direkt konsumtion bilden beskärs till 200 x 100](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower200x100c4.PNG)
  
Nedan visas minskas till 100 x 200 med hemlig förhållandet Beskär bilden. Bilden mäts från vilket resulterar i de vänstra och högra delarna av bilden beskärs center.  
  
![Direkt konsumtion bilden beskärs till 100 x 200](./media/cognitive-services-bing-resize-crop/bing-resize-crop-sunflower100x200c4.PNG)

