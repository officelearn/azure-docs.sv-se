---
title: Generera kartor
description: Beskriver hur du genererar kartor i FarmBeats
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 635431fb87e5f164f92ab4b7a1027ee96e9d801a
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891005"
---
# <a name="generate-maps"></a>Generera kartor

Med hjälp av Azure FarmBeats kan du generera följande Maps med hjälp av satellit-bilder och indata från sensorer. Maps hjälper dig att Visa Server gruppens geografiska plats och identifiera lämplig plats för dina enheter.

  -  **Karta över sensor placering** – ger rekommendationer om hur många sensorer som ska användas och var de ska placeras i en grupp.
  - **Kartor över satellit** index – visar vegetations index och vatten index för en Server grupp.
  - **Jord fuktighets karta** – visar jord fuktighets fördelningen genom fusing satellit data och sensor data.

## <a name="sensor-placement-maps"></a>Sensor placerings kartor

FarmBeats sensor placerings karta hjälper dig att placera jord fuktighets sensorer. Kart resultatet består av en lista över koordinater för sensor distribution. Indata från dessa sensorer används tillsammans med satellit-bilder för att skapa jordens vatten termisk karta.  

Den här kartan härleds genom att segmenterar Canopy som ses över flera dagar under året, även om jorden och byggnaderna är en del av Canopy. Du kan ta bort sensorer som inte behövs på platsen. Den här kartan är till för vägledning och du kan ändra position och siffror något baserat på din anpassade kunskap (om du inte lägger till sensorer i regress av jord-och återställnings resultat, men det finns möjlighet till försämring av värme kart noggrannheten om sensor numret minskar).  

## <a name="before-you-begin"></a>Innan du börjar  

Se till att du har följande innan du försöker skapa en sensor placerings karta:

- Server gruppens storlek måste vara större än en Acre.
- Antalet moln fria kontroll scener måste vara större än sex för det valda datum intervallet.  
- Minst sex moln fria kontroll scener måste ha NDVI > = 0,3.

    > [!NOTE]
    > Kontroll bara tillåter två samtidiga trådar per användare. Det innebär att jobben hamnar i kö och kan ta längre tid att slutföra.

### <a name="dependencies-on-sentinel"></a>Beroenden för kontroll  

Följande är kontroll beroenden:

- Vi är beroende av Sentinel-prestanda för nedladdning av satellit avbildningar. Kontrol lera prestanda status och underhålls [aktiviteter](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)för Sentinel.
- Kontroll bara tillåter två samtidiga [hämtnings trådar](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) per användare.
- Generering av precisions kartor kommer att påverkas av [kontroll täckning och]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage)besöks frekvens.

## <a name="create-sensor-placement-map"></a>Skapa sensor placerings karta
Avsnittet beskriver procedurerna för att skapa sensor placerings kartor.

> [!NOTE]
> Du kan initiera sensor placering från sidan **kartor** eller från den nedrullningsbara menyn **generera precisions kartor** på sidan **Server grupps information** .

Använd följande steg:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.
2. Välj **skapa kartor** och välj **sensor placering** i den nedrullningsbara menyn.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Välj **sensor placering**.
  Sensor placerings fönstret visas.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Välj en Server grupp på den nedrullningsbara menyn **Server grupp** .  
   Om du vill söka efter och välja Server gruppen kan du antingen bläddra i list rutan eller ange namnet på Server gruppen i text rutan.
5. Om du vill generera en karta för det senaste året väljer du **rekommenderat** alternativ.
6. Om du vill generera en karta för ett anpassat datum intervall väljer du **Välj datum intervall**och anger start-och slutdatum för vilken du vill generera sensor placerings kartan.
7. Välj **generera kartor**.
 Ett bekräftelse meddelande med jobb information visas.

  Information om jobb status finns i avsnittet **Visa jobb**. Om jobb *statusen visas visas*ett detaljerat fel meddelande i knapp beskrivningen för statusen *misslyckades* .. I det här fallet upprepar du stegen som anges ovan och försöker igen.

  Om problemet kvarstår kan du läsa avsnittet [fel sökning](troubleshoot-project-farmbeats.md) eller kontakta [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-sensor-placement-map"></a>Visa och ladda ned karta över sensor placering

Använd följande steg:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **filter** i det vänstra fönstrets navigerings fönster.
  **Filter** fönstret visar Sök villkor.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Välj **typ**, **datum** och **namn** på den nedrullningsbara menyn och välj sedan **Använd** för att söka efter den karta som du vill visa.
  Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.
4. Bläddra igenom listan över Maps som är tillgängliga med hjälp av navigerings fälten i slutet av sidan.
5. Välj den karta som du vill visa. Ett popup-fönster visar förhands granskningen för den valda kartan.
6. Välj **Ladda ned**och ladda ned den interjson-fil med sensor koordinater.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Karta över satellit index

I följande avsnitt beskrivs de procedurer som ingår i att skapa och visa kartor över satellit index.

> [!NOTE]
> Du kan initiera kartan för satellit index från sidan **kartor** eller från den nedrullningsbara menyn **generera precisions kartor** på sidan **Server grupps information** .

FarmBeats ger dig möjlighet att generera normaliserad differens vegetations index (NDVI), förbättrat vegetations index (EVI) och (normaliserad differens vatten index) för Server grupper. Dessa index hjälper till att fastställa hur grödan för närvarande växer eller har vuxit tidigare och de representativa vatten nivåerna i marken.


> [!NOTE]
> Det krävs en kontroll avbildning för de dagar då kartan genereras.


## <a name="create-satellite-indices-map"></a>Skapa satellit index karta

Använd följande steg:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.
2. Välj **skapa kartor** och välj **satellit index** på den nedrullningsbara menyn.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Välj **satellit index**.
  Fönstret satellit index visas.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Välj en Server grupp på den nedrullningsbara menyn.  
   Om du vill söka efter och välja Server gruppen kan du antingen bläddra i list rutan eller ange namnet på Server gruppen.   
5. Om du vill generera en karta för den senaste veckan väljer du **den här vecko** alternativet.
6. Om du vill generera en karta för ett anpassat datum intervall väljer du **Välj datum intervall**och anger start-och slutdatum för vilken du vill generera kartan för satellit index.
7. Välj **generera kartor**.
    Ett bekräftelse meddelande med jobb information visas.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Information om jobb status finns i avsnittet **Visa jobb**. Om jobb *statusen visas visas*ett detaljerat fel meddelande i knapp beskrivningen för statusen *misslyckades* . I det här fallet upprepar du stegen som anges ovan och försöker igen.

    Om problemet kvarstår kan du läsa avsnittet [fel sökning](troubleshoot-project-farmbeats.md) eller kontakta [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-map"></a>Visa och hämta karta

Använd följande steg:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **filter** i det vänstra navigerings fönstret så visas Sök kriterierna i **filter** fönstret.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Välj **typ**, **datum** och **namn** på den nedrullningsbara menyn och välj sedan **Använd** för att söka efter den karta som du vill visa.
  Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.

4. Bläddra igenom listan över Maps som är tillgängliga med hjälp av navigerings fälten i slutet av sidan.
5. För varje kombination av namn och **datum**för **Server gruppen** är följande tre Maps tillgängliga:
    - NDVI
    - EVI
    - NDWI
6. Välj den karta som du vill visa. Ett popup-fönster visar förhands granskningen för den valda kartan.
7. Välj **hämtnings List rutan** för att välja nedladdnings formatet och kartan laddas ned och lagras i din lokala mapp på din dator.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="get-soil-moisture-heatmap"></a>Hämta jord fukt termisk karta

Jord fukt är det vatten som hålls i utrymmet mellan jord partiklar. Jord-fuktens termisk karta hjälper dig att förstå jord fukts data i djupet med hög upplösning i dina grupper. För att kunna generera en korrekt och användbar jord fukt termisk karta krävs en enhetlig distribution av sensorer, där alla sensorer kommer från samma provider. Olika leverantörer kommer att ha skillnader i hur jord fukten mäts tillsammans med skillnader i kalibreringen. Termisk karta genereras för ett visst djup med de sensorer som distribueras med det djupet.

### <a name="before-you-begin"></a>Innan du börjar  

Se till att följande innan du försöker generera en jord-fukt termisk karta:

- Minst tre jord fuktighets sensorer måste distribueras. Microsoft rekommenderar att du inte försöker skapa en jord fuktighets karta innan sensorer distribueras och är kopplade till Server gruppen.  
- Att generera jord fukt termisk karta påverkas av kontroll Sök väg, moln täckning och moln skugga. Minst en indikator för kostnads fri indikator för molnet måste vara tillgänglig under de senaste 120 dagarna, från den dag då jord fuktighets kartan begärdes.
- Minst hälften av de sensorer som distribueras i Server gruppen måste vara online och ha data strömning till data hubben.
- Termisk karta ska genereras med sensor mått från samma provider.


## <a name="create-soil-moisture-heatmap"></a>Skapa jord fukt termisk karta

Använd följande steg:

1. På Start sidan går du till **kartor** från den vänstra navigerings menyn för att visa sidan kartor.
2. Välj **skapa kartor** och välj **jord fukt** från nedrullningsbar meny.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Välj **jord fuktighet**.
    Fönstret jord fukt visas.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Välj en Server grupp på den nedrullningsbara menyn **Server grupp** .  
   Om du vill söka efter och välja din server grupp kan du antingen bläddra i list rutan eller ange namnet på Server gruppen på list menyn Välj grupp.
5. I list rutan **Välj jord fuktighets sensor** väljer du den jord fuktighets sensor som du vill generera kartan för.
Om du vill hitta sensor måttet går du till **sensorer**och väljer en jord fuktighets sensor. Under avsnittet **sensor egenskaper** använder du sedan värdet mot i **måttets namn**
6. Välj ett av alternativen om du vill generera för **idag** eller **den här veckan**.
7. Om du vill generera ett anpassat datum intervall väljer du **Välj datum intervall**och anger start-och slutdatum för den färg som du vill skapa jord fukts kartan.
8. Välj **generera kartor**.
 Ett bekräftelse meddelande med jobb information visas.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Information om jobb status finns i avsnittet **Visa jobb**. Om jobb *statusen visas visas*ett detaljerat fel meddelande i knapp beskrivningen för statusen *misslyckades* . I det här fallet upprepar du stegen som anges ovan och försöker igen.

    Om problemet kvarstår kan du läsa avsnittet [fel sökning](troubleshoot-project-farmbeats.md) eller kontakta [Azure FarmBeats-forumet för support](https://aka.ms/FarmBeatsMSDN) med relevanta loggar.

### <a name="view-and-download-map"></a>Visa och hämta karta

Använd följande steg:

1. Gå till **kartor** från den vänstra navigerings menyn på Start sidan.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Välj **filter** i den vänstra navigeringen i fönstret så visas fönstret **filter** där du kan söka efter kartor.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Välj **typ**, **datum** och **namn** på den nedrullningsbara menyn och välj sedan **Använd** för att söka efter den karta som du vill visa. Det datum då jobbet skapades visas i formatet type_farmname_YYYY-MM-DD.
4. Välj **sorterings** ikonen bredvid tabell rubrikerna för att sortera efter Server grupp, datum, skapad, jobb-ID och Jobbtyp.
5. Bläddra igenom listan över Maps som är tillgängliga med hjälp av navigerings knapparna i slutet av sidan.
6. Välj den karta som du vill visa. Ett popup-fönster visar förhands granskningen för den valda kartan.
7. Välj **hämtnings List rutan** för att välja nedladdnings format och kartan laddas ned och lagras i den angivna mappen.

    ![Taktslag i projekt grupp](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
