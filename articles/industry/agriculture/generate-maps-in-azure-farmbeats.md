---
title: Generera kartor
description: I den här artikeln beskrivs hur du skapar kartor i Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79271777"
---
# <a name="generate-maps"></a>Generera kartor

Med Hjälp av Azure FarmBeats kan du generera följande kartor med hjälp av satellitbilder och sensordataindata. Kartor hjälper dig att se den geografiska platsen för din servergrupp och identifiera lämplig plats för dina enheter.

  - **Sensorplaceringskarta:** Ger rekommendationer om hur många sensorer som ska användas och var de ska placeras på en gård.
  - **Satellitindex karta:** Visar vegetation index och vatten index för en gård.
  - **Värmekarta för jordfukt:** Visar markfuktfördelning genom att smälta satellitdata och sensordata.

## <a name="sensor-placement-map"></a>Karta över sensorplacering

En FarmBeats Sensor Placering karta hjälper dig med placeringen av mark fuktsensorer. Kartutdata består av en lista över koordinater för sensordistribution. Ingångarna från dessa sensorer används tillsammans med satellitbilder för att generera värmekartan För jordfukt.

Denna karta härleds genom att segmentera baldakinen sett över flera datum under hela året. Även bar jord och byggnader är en del av baldakinen. Du kan ta bort sensorer som inte behövs på platsen. Den här kartan är för vägledning, och du kan ändra position och siffror något baserat på din anpassade kunskap. Lägga sensorer kommer inte att gå tillbaka mark fukt heatmap resultat, men det finns en risk för försämring av heatmap noggrannhet om sensorn antalet minskas.

## <a name="before-you-begin"></a>Innan du börjar

Uppfylla följande förutsättningar innan du försöker skapa en karta över sensorplacering:

- Gårdens storlek måste vara mer än ett tunnland.
- Antalet molnfria Sentinel-scener måste vara fler än sex för det valda datumintervallet.
- Minst sex molnfria Sentinel-scener måste ha ett normaliserat vegetationsindex (NDVI) som är större än eller lika med 0,3.

    > [!NOTE]
    > Sentinel tillåter endast två samtidiga trådar per användare. Därför hamnar jobb i kö och det kan ta längre tid att slutföra.

### <a name="dependencies-on-sentinel"></a>Beroenden på Sentinel

Följande beroenden gäller Sentinel:

- Vi är beroende av Sentinels prestanda för att ladda ner satellitbilder. Kontrollera Sentinels prestandastatus och [underhållsaktiviteter](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome).
- Sentinel tillåter endast två samtidiga [nedladdningar trådar](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) per användare.
- Precision kartgenerering påverkas av [Sentinel täckning och återkomma frekvens]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Skapa en karta över sensorplacering
I det här avsnittet beskrivs procedurerna för att skapa kartor för sensorplacering.

> [!NOTE]
> Du kan initiera en kartkarta för sensorplacering från sidan **Kartor** eller från listrutan **Generera precisionskartor** på sidan **Servergruppsinformation.**

Följ de här stegen.

1. Gå till **Kartor** från den vänstra navigeringsmenyn på startsidan.
2. Välj **Skapa kartor**och välj **Sensorplacering** på den nedrullningsbara menyn.

    ![Välj sensorplacering](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. När du har valt **Sensorplacering**visas **fönstret Sensorplacering.**

    ![Fönstret Placering av sensor](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Välj en servergrupp på listrutan **Servering.**
   Om du vill söka efter och markera servergruppen kan du antingen rulla i listrutan eller ange namnet på servergruppen i textrutan.
5. Om du vill generera en karta för det senaste året väljer du **Rekommenderat**.
6. Om du vill skapa en karta för ett anpassat datumintervall väljer du alternativet **Välj datumintervall**. Ange start- och slutdatum som du vill generera kartan För sensorplacering.
7. Välj **Generera kartor**.
 Ett bekräftelsemeddelande med jobbinformation visas.

  Information om jobbstatus finns i avsnittet **Visa jobb**. Om jobbstatusen visas *Misslyckades*visas ett detaljerat felmeddelande i verktygstipset för statusen *Misslyckades.* Upprepa föregående steg i så fall och försök igen.

  Om problemet kvarstår läser [du avsnittet Felsöka](troubleshoot-azure-farmbeats.md) eller kontaktar [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-a-sensor-placement-map"></a>Visa och ladda ner en karta över sensorplacering

Följ de här stegen.

1. Gå till **Kartor** från den vänstra navigeringsmenyn på startsidan.

    ![Välj Kartor på den vänstra navigeringsmenyn](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **Filter** från fönstrets vänstra navigering.
  I **fönstret Filter** visas sökvillkor.

    ![Fönstret Filter](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Välj **Värdena Typ,** **Datum**och **Namn** på de nedrullningsbara menyerna. Välj sedan **Använd** för att söka efter den karta som du vill visa.
  Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.
4. Bläddra igenom listan med kartor som är tillgängliga med hjälp av navigeringsfälten i slutet av sidan.
5. Markera den karta som du vill visa. I ett popup-fönster visas förhandsgranskningen för den valda kartan.
6. Välj **Hämta**och hämta GeoJSON-filen med sensorkoordinater.

    ![Förhandsgranskning av förhandsgranskning av kartan för sensorplacering](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Karta över satellitindex

I följande avsnitt beskrivs de förfaranden som är involverade i att skapa och visa en satellitindexkarta.

> [!NOTE]
> Du kan initiera en satellitindexkarta från sidan **Kartor** eller från listrutan **Generera precisionskartor** på sidan **Servergruppsinformation.**

FarmBeats ger dig möjlighet att generera NDVI, Enhanced Vegetation Index (EVI) och Normalized Difference Water Index (NDWI) kartor för gårdar. Dessa index hjälper till att avgöra hur grödan för närvarande växer, eller har vuxit tidigare, och de representativa vattennivåerna i marken.


> [!NOTE]
> En Sentinel-bild krävs för de dagar som kartan genereras för.


## <a name="create-a-satellite-indices-map"></a>Skapa en karta över satellitindex

Följ de här stegen.

1. Gå till **Kartor** från den vänstra navigeringsmenyn på startsidan.
2. Välj **Skapa kartor**och välj Satellitindex på den nedrullningsbara menyn. **Satellite Indices**

    ![Välj Satellitindex på rullgardinsmenyn](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. När du har valt **Satellitindex**visas fönstret **Satellitindex.**

    ![Fönstret Satellitindex](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Välj en servergrupp på den nedrullningsbara menyn.
   Om du vill söka efter och välja din servergrupp kan du antingen bläddra i listrutan eller ange namnet på servergruppen.   
5. Om du vill generera en karta för den senaste veckan väljer du **Den här veckan**.
6. Om du vill skapa en karta för ett anpassat datumintervall väljer du alternativet **Välj datumintervall**. Ange start- och slutdatum som du vill generera kartan Satellitindex för.
7. Välj **Generera kartor**.
    Ett bekräftelsemeddelande med jobbinformation visas.

    ![Bekräftelsemeddelande för satellitindex kartbekräftelse](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Information om jobbstatus finns i avsnittet **Visa jobb**. Om jobbstatusen visas *Misslyckades*visas ett detaljerat felmeddelande i verktygstipset för statusen *Misslyckades.* Upprepa föregående steg i så fall och försök igen.

    Om problemet kvarstår läser [du avsnittet Felsöka](troubleshoot-azure-farmbeats.md) eller kontaktar [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-a-map"></a>Visa och ladda ner en karta

Följ de här stegen.

1. Gå till **Kartor** från den vänstra navigeringsmenyn på startsidan.

    ![Välj kartor](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **Filter** från fönstrets vänstra navigering. I **fönstret Filter** visas sökvillkor.

    ![Filterfönstret visar sökvillkor](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Välj **Värdena Typ,** **Datum**och **Namn** på de nedrullningsbara menyerna. Välj sedan **Använd** för att söka efter den karta som du vill visa.
  Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.

4. Bläddra igenom listan med kartor som är tillgängliga med hjälp av navigeringsfälten i slutet av sidan.
5. För varje kombination av **servergruppsnamn** och **datum**finns följande tre kartor tillgängliga:
    - NDVI (NDVI)
    - Evi
    - NDWI (på andra sätt)
6. Markera den karta som du vill visa. I ett popup-fönster visas förhandsgranskningen för den valda kartan.
7. Välj **Hämta** på rullgardinsmenyn för att välja nedladdningsformat. Kartan hämtas och lagras i din lokala mapp på datorn.

    ![Förhandsgranskning av karta för markerade satellitindex](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Värmekarta för jordfukt

Markfukt är vattnet som hålls i utrymmena mellan jordpartiklar.Soil Moisture heatmap hjälper dig att förstå marken fukt data på alla djup, med hög upplösning inom din gård. För att generera en korrekt och användbar heatm-värmekarta för markfukt krävs en enhetlig användning av sensorer. Alla sensorer måste komma från samma leverantör. Olika leverantörer har skillnader i hur markfukt mäts tillsammans med skillnader i kalibrering. Värmekartan genereras för ett visst djup med hjälp av de sensorer som används på det djupet.

### <a name="before-you-begin"></a>Innan du börjar

Uppfylla följande förutsättningar innan du försöker generera en värmekarta för jordfukt:

- Minst tre markfuktsensorer måste sättas in. Försök inte att skapa en värmekarta för jordfukt innan sensorer sätts in och associeras med gården.
- Generera en Soil Moisture heatmap påverkas av Sentinel väg täckning, molntäcke och moln skugga. Minst en molnfri Sentinel Scene måste vara tillgänglig för de senaste 120 dagarna, från den dag då Soil Moisture heatmap begärdes.
- Minst hälften av sensorerna som distribueras på servergruppen måste vara online och ha data som direktuppspelar till datahub.
- Värmekartan måste genereras med hjälp av sensormått från samma leverantör.


## <a name="create-a-soil-moisture-heatmap"></a>Skapa en värmekarta för jordfukt

Följ de här stegen.

1. På startsidan går du till **Kartor** från den vänstra navigeringsmenyn för att visa **sidan Kartor.**
2. Välj **Skapa kartor**och välj **Markfukt på** rullgardinsmenyn.

    ![Välj Jordfukt på rullgardinsmenyn](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. När du har valt **Jordfukt**visas fönstret **Jordfukt.**

    ![Fönster i jordfukt](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Välj en servergrupp på listrutan **Servering.**
   Om du vill söka och välja din servergrupp kan du antingen bläddra i listrutan eller ange namnet på servergruppen på listrutan **Välj servergrupp.**
5. På rullgardinsmenyn **Välj markfuktsensor Mät** väljer du det markfuktsensormått (djup) som du vill generera kartan för.
För att hitta sensormåttet, gå till **Sensorer**och välj någon markfuktsensor. Använd sedan värdet i **Måttnamn**under avsnittet **Sensoregenskaper** .
6. Om du vill skapa en karta för **Idag** eller **Den här veckan**väljer du ett av alternativen.
7. Om du vill skapa en karta för ett anpassat datumintervall väljer du alternativet **Välj datumintervall**. Ange start- och slutdatum som du vill generera värmekartan för jordfukt.
8. Välj **Generera kartor**.
 Ett bekräftelsemeddelande med jobbinformation visas.

   ![Bekräftelsemeddelande för jordfuktskarta](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Information om jobbstatus finns i avsnittet **Visa jobb**. Om jobbstatusen visas *Misslyckades*visas ett detaljerat felmeddelande i verktygstipset för statusen *Misslyckades.* Upprepa föregående steg i så fall och försök igen.

    Om problemet kvarstår läser [du avsnittet Felsöka](troubleshoot-azure-farmbeats.md) eller kontaktar [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-a-map"></a>Visa och ladda ner en karta

Följ de här stegen.

1. Gå till **Kartor** från den vänstra navigeringsmenyn på startsidan.

    ![Gå till Kartor](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **Filter** från fönstrets vänstra navigering. **Filterfönstret** visas varifrån du kan söka efter kartor.

    ![Välj Filter från vänster navigering](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Välj **Värdena Typ,** **Datum**och **Namn** på de nedrullningsbara menyerna. Välj sedan **Använd** för att söka efter den karta som du vill visa. Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.
4. Välj ikonen **Sortera** bredvid tabellrubrikerna som ska sorteras efter **Servergrupp,** **Datum**, **Skapad på,** **Jobb-ID**och **Jobbtyp**.
5. Bläddra igenom listan med kartor som är tillgängliga med hjälp av navigeringsknapparna i slutet av sidan.
6. Markera den karta som du vill visa. I ett popup-fönster visas förhandsgranskningen för den valda kartan.
7. Välj **Hämta** på rullgardinsmenyn för att välja nedladdningsformat. Kartan hämtas och lagras i den angivna mappen.

    ![Förhandsgranskning av värmekarta för värmekarta för markfukt](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
