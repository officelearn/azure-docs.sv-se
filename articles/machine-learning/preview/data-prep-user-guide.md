---
title: "Detaljerad vägledning om hur du använder Azure Machine Learning Data förberedelser | Microsoft Docs"
description: "Det här dokumentet innehåller en översikt och information om hur du löser problem med Azure Machine Learning Data förberedelser"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: 9fc0ae8929f12447123321a5c64ac89309c68dca
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2018
---
# <a name="data-preparations-user-guide"></a>Data användarhandboken för förberedelser 
Azure Machine Learning Data förberedelser upplevelsen ger mycket omfattande funktioner. Den här artikeln dokumenteras djupaste delarna i miljön.

### <a name="step-execution-history-and-caching"></a>Körningen av steget, historik och cachelagring 
Data förberedelser steg historik upprätthåller en serie cachelagring av prestandaskäl. Om du väljer ett steg och den når en cache kan kör den inte igen. Om du har ett write block i slutet av tidigare steg och du Vänd fram och tillbaka på stegen men gör inga ändringar, är inte skrivning Utlöses efter första gången. En ny skrivning inträffar och skriver över det tidigare om du:

- Ändra write-block.
- Lägg till ett nytt transform-block och flyttas över write block, vilket genererar en cache-ogiltigförklarade.
- Ändra egenskaper för ett block ovan write block, vilket genererar en cache-ogiltigförklarade.
- Välj att uppdatera på ett sampel (ogiltigförklara därför alla cacheminnen).

### <a name="error-values"></a>Felvärdena

Datatransformationer kan misslyckas för ett indatavärde eftersom detta värde inte kan hanteras på rätt sätt. Till exempel misslyckas tvång när det gäller typ tvång åtgärder om inkommande strängvärdet inte kan omvandlas till den angivna måltypen. En åtgärd med tvång typ kan konvertera en kolumn av strängtypen till ett numeriskt eller booleskt eller försök att kopiera en kolumn som inte finns. (Det här felet uppstår till följd av att flytta den *ta bort kolumnen X* åtgärden innan den *dubbletter av kolumnnamn X* igen.)

I dessa fall genererar Data förberedelser ett felvärde som utdata. Felvärden anger att en tidigare åtgärd misslyckades för det angivna värdet. Internt, de är behandlas som en förstklassig värdetyp, men deras närvaro påverkar inte den underliggande typen för en kolumn, även om en kolumn enbart består av felaktiga värden.

Felvärden är lättare att identifiera. De rödmarkerade och läsa ”Error”. Hovra över ett felvärde om du vill se en beskrivning av felet för att fastställa orsaken till felet.

Felvärdena spridas. När ett fel inträffar värde sprids i de flesta fall som ett fel med de flesta åtgärder. Det finns tre sätt att ersätta eller ta bort dem:

* Ersätt
    -  Högerklicka på en kolumn och markera **ersätter felvärdena**. Du kan sedan välja ett ersättningsvärde för varje felvärde hittades i kolumnen.

* Ta bort
    - Data förberedelser innehåller interaktiva filter för att bevara eller ta bort felaktiga värden.
    - Högerklicka på en kolumn och markera **filterkolumnen**. Om du vill behålla eller ta bort felvärdena, skapa en villkorlig med villkoret *”är fel”* eller *”är inte fel”.*

* Använd ett Python-uttryck för att använda villkorligt felvärdena. Mer information finns i [avsnitt på Python-tillägg för](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Samling
En fil för datakällor tar i rådata hämtas från en eller flera källor, antingen från det lokala filsystemet eller en annan plats. Exempel-block kan du ange om du vill arbeta med en delmängd av data genom att generera prover. Fungerar på ett urval av data i stället för en stor datauppsättning ofta resulterar i bättre prestanda när du utför åtgärder i senare steg.

Flera exempel kan skapas och lagras för varje fil för datakällor. Bara ett exempel kan ställas in som aktiv exemplet. Du kan skapa, redigera eller ta bort exempel i guiden datakälla eller genom att redigera exempel blocket. Data förberedelser filer som refererar till en datakälla natur Använd exemplet som anges i filen datakällor.

Det finns ett antal prover strategier som kan användas, var och en med olika parametrar som kan konfigureras.

#### <a name="top"></a>Överst
Den här strategin kan tillämpas på lokala eller fjärranslutna filer. Det tar de första N raderna (anges av antal) i datakällan.

#### <a name="random-n"></a>Random N 
Den här strategin kan tillämpas endast för lokala filer. Det tar slumpmässiga N rader (anges av antal) i datakällan. Du kan ange en specifik startvärde för att säkerställa att samma prov genereras, förutsatt att antalet också är samma.

#### <a name="random-"></a>Slumpmässiga % 
Den här strategin kan tillämpas på lokala eller fjärranslutna filer. I båda fallen måste sannolikhet och ett startvärde vara angivna, liknande slumpmässiga N-strategi.

Exempel på fjärrfiler måste ytterligare parametrar anges:

- Exempel generator 
  - Välj ett Spark-kluster eller remote Docker compute mål som ska användas för att generera exempel. Compute-målet måste skapas för projektet i förväg för att det ska visas i listan. Följ stegen i avsnittet ”Skapa ett nytt mål för beräkning” i [hur du använder GPU i Azure Machine Learning](how-to-use-gpu.md) att skapa mål för beräkning.
- Exempel lagring 
  - Ange en mellanliggande lagringsplats för att lagra remote exemplet. Den här sökvägen måste vara en annan katalog från inkommande filplats.

#### <a name="full-file"></a>Fullständig 
Den här strategin kan tillämpas endast för lokala filer, tar den fullständiga filen till datakällan. Om filen är för stor, kan det här alternativet långsammare framtida åtgärder i appen. Du kan hitta mer lämpligt att använda en annan för provtagning.


### <a name="fork-merge-and-append"></a>Duplicera, sammanslagning och tillägg

När du använder ett filter under en datauppsättning igen delas data i två resultatmängder: en uppsättning representerar poster som lyckas i filtret och en annan uppsättning är för de poster som misslyckas. I båda fallen kan användaren välja vilka resultatuppsättningen som ska visas. Användaren kan ta bort andra datauppsättningen eller placera den i ett nytt dataflöde. Det senare alternativet kallas onödiga kostnader.

Att duplicera: 
1. Markera en kolumn, högerklicka och välj den **Filter** kolumn.

2. Under **jag vill att**väljer **Behåll rader** att visa resultatuppsättningen som passerar filtret.

3. Välj **ta bort rader** att visa den misslyckades.

4. Efter **villkor**väljer **skapa dataflöde som innehåller filtreras ut rader** att duplicera-visa resultatet i ett nytt dataflöde.


Den här övningen används ofta för att skilja ut en uppsättning data som kräver ytterligare förberedelser. När du har förberett andelen vridvuxna datamängden är det vanligt att sammanfoga data med resultatuppsättningen i det ursprungliga dataflödet. För dokument (omvänt en förgrening åtgärd), använder du någon av följande åtgärder:

- **Lägg till rader**. Sammanfoga två eller flera dataflöden lodrätt (bindning efter rader). 
- **Lägg till kolumner**. Sammanfoga två eller flera dataflöden vågrätt (Kolumnvis).


>[!NOTE]
>Lägg till kolumner misslyckas om en kolumn kollision inträffar.


Efter en merge-operation en eller flera dataflöden refererar till en källa dataflöde. Data förberedelser meddelar dig med ett meddelande i det nedre högra hörnet av appen under lista över steg.


En åtgärd på det refererade dataflödet kräver överordnade dataflödet uppdatera provet från refererade dataflödet. I så fall ersätter en bekräftelsedialogruta data flödet referens meddelandet i det nedre högra hörnet. Dialogrutan bekräftar att du behöver uppdatera dataflöde för att synkronisera ändringar av eventuella beroende dataflöden.

### <a name="list-of-appendices"></a>Lista över tillägg 
* [Datakällor som stöds](data-prep-appendix2-supported-data-sources.md)  
* [Stöds transformeringar](data-prep-appendix3-supported-transforms.md)  
* [Stöds kontrollanter](data-prep-appendix4-supported-inspectors.md)  
* [Stöds mål](data-prep-appendix5-supported-destinations.md)  
* [Exempel filteruttryck i Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Exempel Transformera data flödet uttryck i Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Exempel datakällor i Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Exempel mål-anslutningar i Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Exempel kolumnen omvandlar i Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
