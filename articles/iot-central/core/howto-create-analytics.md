---
title: Analysera enhetsdata i ditt Azure IoT Central-program | Microsoft-dokument
description: Analysera enhetsdata i ditt Azure IoT Central-program.
author: ankitscribbles
ms.author: ankitgup
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 40460b58ede0ca0da8fe25475906bdbe41bfffe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158290"
---
# <a name="how-to-use-analytics-to-analyze-device-data"></a>Så här använder du analyser för att analysera enhetsdata

*Den här artikeln gäller för operatörer, kompilerare och administratörer.*



Azure IoT Central tillhandahåller omfattande analysfunktioner för att analysera historiska trender och korrelera olika telemetri från dina enheter. Kom igång genom att besöka **Analytics** i den vänstra rutan.

## <a name="understanding-the-analytics-ui"></a>Förstå användargränssnittet för Analytics
Analytics användargränssnitt består av tre huvudkomponenter:
- **Panelen För datakonfiguration:** Börja med att välja den enhetsgrupp som du vill analysera data för på konfigurationspanelen. Välj sedan den telemetri som du vill analysera och välj aggregeringsmetoden för varje telemetri. **Split By-kontrollen** hjälper till att gruppera data med hjälp av enhetsegenskaperna som dimensioner.

- **Tidskontroll:** Tidskontroll används för att välja hur länge du vill analysera data. Du kan dra i någon av slutet av tidsreglaget för att markera tidsintervallet. Tidskontrollen har också ett skjutreglage **för intervallstorlek** som styr bucketen eller intervallstorleken som används för att aggregera data. 

- **Diagramkontroll:** Diagramkontrollen visualiserar data som ett linjediagram. Du kan växla synligheten för specifika linjer genom att interagera med diagramförklaringen. 


  ![Översikt över användargränssnittet i Analytics](media/howto-create-analytics/analyticsui.png)


## <a name="querying-your-data"></a>Fråga dina data

Du måste börja med att välja en **enhetsgrupp**och den telemetri som du vill analysera. När du är klar väljer du **Analysera** för att börja visualisera dina data.

- **Enhetsgrupp:** En [enhetsgrupp](tutorial-use-device-groups.md) är en användardefinierad grupp av dina enheter. Till exempel alla kylskåp i Oakland, eller Alla version 2.0 vindkraftverk.

- **Telemetri:** Välj den telemetri som du vill analysera och utforska. Du kan välja flera telemetrier som ska analyseras tillsammans. Standardaggregeringsmetoden är inställd på Medel för numeriskt respektive Antal för strängdatatyp. Aggregeringsmetoder som stöds för numeriska datatyper är Medel, Maximum, Minimum, Count och, Summa.  Aggregeringsmetoder som stöds för strängdatatyp räknas.

- **Dela av:** "Split by"-kontrollen hjälper till att gruppera data med hjälp av enhetsegenskaperna som dimensioner. Värden för enheten och molnegenskaperna sammanfogas tillsammans med telemetrin om och när den skickas av enheten. Om moln- eller enhetsegenskapen har uppdaterats visas telemetrin grupperad efter olika värden i diagrammet.

    > [!TIP]
    > Om du vill visa data för varje enhet separat väljer du Enhets-ID i kontrollen Dela efter.

## <a name="interacting-with-your-data"></a>Interagera med dina data

När du har frågat efter dina data kan du börja visualisera dem i linjediagrammet. Du kan visa/dölja telemetri, ändra tidsvaraktighet, visa telemetri i ett datarutnät.

- **Panelen Tidsredigerare:** Som standard hämtar vi data från den senaste dagen. Du kan dra i någon av slutet av tidsreglaget för att ändra tidsvaraktigheten. Du kan också använda kalenderkontrollen för att välja en av de fördefinierade tidsdifestanserna eller välja ett anpassat tidsintervall. Tidskontrollen har också ett skjutreglage **för intervallstorlek** som styr bucketen eller intervallstorleken som används för att aggregera data.

    ![Tid Redaktör](media/howto-create-analytics/timeeditorpanel.png)

    - **Skjutreglaget för det inre datumintervallet**: Använd de två slutpunktskontrollerna genom att dra dem över den tidsperiod du vill ha. Det här inre datumintervallet begränsas av skjutreglaget för det yttre datumintervallet.
    
   
    - **Skjutreglagekontroll för yttre datumintervall**: Använd slutpunktskontrollerna för att välja det yttre datumintervallet, som kommer att vara tillgängligt för den inre datumintervallkontrollen.

    - **Öka och minska knapparna för datumintervall:** Öka eller minska tidsintervallet genom att välja endera knappen för det intervall du vill ha.

    - **Skjutreglage i intervallstorlek**: Använd det för att zooma in och ut i intervall över samma tidsperiod. Den här åtgärden ger mer exakt kontroll av förflyttningar mellan stora tidssegment. Du kan använda den för att se detaljerade, högupplösta vyer av dina data, även ner till millisekunder. Skjutreglagets standardstartpunkt anges som den mest optimala vyn av data från markeringen, som balanserar upplösning, frågehastighet och granularitet.
    
    - **Datumintervallväljare:** Med den här webbkontrollen kan du enkelt välja det datum och den tid som du vill ha. Du kan också använda kontrollen för att växla mellan olika tidszoner. När du har gjort ändringarna som ska tillämpas på den aktuella arbetsytan väljer du Spara.

    > [!TIP]
    > Intervallstorleken bestäms dynamiskt baserat på det valda tidsspannet. Mindre tidsintervall gör det möjligt att sammanställa data till mycket detaljerade intervall på upp till några sekunder.


- **Diagramförklaring:** Diagramförklaringen visar den valda telemetrin i diagrammet. Du kan hovra över varje objekt i förklaringen för att få det att fokusera på diagrammet. När du använder "Split By" grupperas telemetrin efter respektive värden för den valda dimensionen. Du kan växla synligheten för varje specifik telemetri eller hela gruppen genom att klicka på gruppnamnet.  


- **Y-axelformatskontroll:** y-axelläget växlar genom de tillgängliga y-axelvyalternativen. Den här kontrollen är endast tillgänglig när olika telemetri visualiseras. Du kan ställa in y-axeln genom att välja från ett av tre lägen:

    - **Staplade:** Ett diagram för varje telemetri staplas och var och en av diagrammen har sin egen y-axel. Det här läget är inställt som standard.
    - **Delad:** Ett diagram för varje telemetri ritas mot samma y-axel.
    - **Överlappning:** Använd den för att stapla flera rader på samma y-axel, med y-axelns data som ändras baserat på den valda raden.

  ![Ordna data över y-axeln med olika visualiseringslägen](media/howto-create-analytics/yaxiscontrol.png)

- **Zooma kontroll:** Med Zooma kan du öka data. Om du hittar en tidsperiod som du vill fokusera på inom resultatuppsättningen använder du muspekaren för att hämta området och drar det sedan till den slutpunkt du väljer. Högerklicka sedan på det markerade området och klicka på Zooma.

  ![Zooma in i data](media/howto-create-analytics/zoom.png)

Under ellipsen finns det fler diagramkontroller för att interagera med data.

- **Visa rutnät:** Dina resultat är tillgängliga i ett tabellformat, så att du kan visa det specifika värdet för varje datapunkt.

- **Släpp en markör:** Drop Marker-kontrollen är ett sätt att förankra vissa datapunkter i diagrammet. Det är användbart när du försöker jämföra data för flera rader över olika tidsperioder.

  ![Visa rutnätsvyn för dina analyser](media/howto-create-analytics/additionalchartcontrols.png)