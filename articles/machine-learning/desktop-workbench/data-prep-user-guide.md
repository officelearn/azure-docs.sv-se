---
title: Utförliga instruktioner om hur du använder Azure Machine Learning-Dataförberedelser | Microsoft Docs
description: Det här dokumentet innehåller en översikt och information om hur du löser problem med Azure Machine Learning Dataförberedelser
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: jmartens, jasonwhowell, mldocs
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.custom: ''
ms.devlang: ''
ms.topic: article
ms.date: 02/01/2018
ms.openlocfilehash: e4db8d70d2a96fe3ad24797d7a32d75e1a792e32
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35647561"
---
# <a name="data-preparations-user-guide"></a>Användarhandbok för data förberedelser 
Upplevelsen Dataförberedelser för Azure Machine Learning erbjuder en mängd olika funktioner. Den här artikeln beskrivs de djupaste delarna i miljön.

### <a name="step-execution-history-and-caching"></a>Körningen av steget, historik och cachelagring 
Historik för förberedelser steg upprätthåller en serie cacheminnen av prestandaskäl. Om du väljer ett steg och den når en cache, kör den inte igen. Om du har ett Skriv-block i slutet av steg historik och vänd fram och tillbaka på stegen men utan ändringar, aktiveras skrivning inte efter den första gången. En ny skrivning inträffar och skriver över det tidigare om du:

- Göra ändringar i write-block.
- Lägg till ett nytt transform-block och flytta den ovan write-block, vilket genererar en cache-ogiltigförklarade.
- Ändra egenskaperna för ett block ovan write-block, vilket genererar en cache-ogiltigförklarade.
- Välj att uppdatera på ett sampel (ogiltigförklara därför alla cacheminnen).

### <a name="error-values"></a>Felvärden

Dataomvandlingar misslyckas för ett indatavärde, eftersom detta värde inte kan hanteras på rätt sätt. Till exempel misslyckas tvång när det gäller typ tvång åtgärder om Indatasträngen värdet inte kan typkonverteras till den angivna måltypen. En typ tvång åtgärd kan konvertera en kolumn av strängtypen till ett numeriskt värde eller boolesk typ eller försök att duplicera en kolumn som inte finns. (Det här felet uppstår till följd av att flytta den *ta bort kolumnen X* åtgärder innan den *dubbletter av kolumnnamn X* igen.)

I dessa fall kan genererar Dataförberedelser ett felvärde som utdata. Felvärden tyda på att en tidigare åtgärd misslyckades för det angivna värdet. Internt, de är behandlas som en första klassens värdetyp, men deras närvaro påverkar inte den underliggande typen för en kolumn, även om en kolumn bestå enbart av felvärdena.

Felvärden är lätt att identifiera. De är markerat i rött och läsa ”Error”. Ta reda på orsaken till felet genom att hovra över ett felvärde om du vill se en beskrivning av felet.

Felvärden spridas. Efter ett fel visas, sprids i de flesta fall som ett fel via de flesta åtgärder. Det finns tre sätt att ersätta eller ta bort dem:

* Ersätt
    -  Högerklicka på en kolumn och välj **ersätta felvärdena**. Du kan sedan välja ett ersättningsvärde för varje felvärde hittades i kolumnen.

* Ta bort
    - Dataförberedelser innehåller interaktiva filter för att bevara eller ta bort felvärdena.
    - Högerklicka på en kolumn och välj **Filterkolumn**. Om du vill behålla eller ta bort felvärden genom att skapa en villkorlig med villkoret *”är fel”* eller *”är inte fel”.*

* Använd ett Python-uttryck för att villkorligt fungerar på felvärdena. Mer information finns i den [avsnitt om Python-tillägg](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Samling
En fil för datakällor tar rådata från en eller flera källor, från det lokala filsystemet eller en annan plats. Exempel-block kan du ange om du vill arbeta med en delmängd av data genom att generera exempel. Körs på ett exempel på data i stället för en stor datauppsättning ofta leder till bättre prestanda när du utför åtgärder i senare steg.

Flera exempel kan skapas och lagras för varje fil för datakällor. Bara ett exempel kan ställas in som aktiv exemplet. Du kan skapa, redigera eller ta bort exempel i guiden datakälla eller genom att redigera exempel-block. Dataförberedelser filer som refererar till en datakälla sin natur Använd exemplet som anges i filen datakällor.

Det finns ett antal sampling strategier som kan användas, var och en med olika konfigurerbara parametrar.

#### <a name="top"></a>Överkant
Den här strategin kan tillämpas på lokala eller fjärranslutna filer. Det tar de första N raderna (anges med antal) till datakällan.

#### <a name="random-n"></a>Slumpmässig N 
Den här strategin kan tillämpas endast för lokala filer. Det tar slumpmässiga N raderna (anges med antal) till datakällan. Du kan ange en specifik dirigering för att säkerställa att genereras samma prov, förutsatt att antalet är också desamma.

#### <a name="random-"></a>Slumpmässig % 
Den här strategin kan tillämpas på lokala eller fjärranslutna filer. I båda fallen kan måste en sannolikhet och en seed vara angivna, vilket liknar slumpmässiga N-strategi.

Exempel på filer som behöver ytterligare parametrar anges:

- Exemplet generator 
  - Välj ett Spark-kluster eller fjärranslutna Docker beräkningsmål som ska användas för att generera exempel. Beräkningsmål måste skapas för projektet i förväg innan den visas i den här listan. Följ stegen i avsnittet ”Skapa en ny beräkningsmål” i [hur du använder GPU i Azure Machine Learning](how-to-use-gpu.md) att skapa beräkningsmål.
- Exempel-lagring 
  - Ange en mellanliggande lagringsplats för att lagra remote exemplet. Den här sökvägen måste vara en annan katalog från inkommande filplats.

#### <a name="full-file"></a>Fullständig fil 
Den här strategin kan tillämpas endast för lokala filer, tar den fullständiga filen till datakällan. Om filen är för stor kan kan det här alternativet sakta ned framtida åtgärder i appen. Du kan hitta mer effektivt att använda en annan samplingsstrategi.


### <a name="fork-merge-and-append"></a>Förgrena, sammanslagning och tillägg

När du använder ett filter över en datauppsättning igen delas data i två resultatmängder: en uppsättning representerar poster att lyckas i filtret och en annan uppsättning är för de poster som misslyckas. I båda fallen kan användaren välja vilka resultatet ska visas. Användaren kan ta bort andra datauppsättningen eller placera den i ett nytt dataflöde. Det senare alternativet kallas för förgreningen.

Förgrena: 
1. Välj en kolumn, högerklicka och välj den **Filter** kolumn.

2. Under **jag vill att**väljer **Behåll rader** att visa resultatuppsättningen som skickar filtret.

3. Välj **ta bort rader** att visa den misslyckade uppsättningen.

4. Efter **villkor**väljer **skapa dataflöde som innehåller filtrerat ut rader** Förgrena inte visa resultatet i ett nytt dataflöde.


Den här metoden används ofta för att skilja ut en uppsättning data som kräver ytterligare förberedelser. När du har förberett förgrenade datauppsättningen är det vanligt att sammanfoga data med resultatuppsättningen i det ursprungliga dataflödet. Använd någon av följande åtgärder för att utföra en sammanfogning (omvänd för en förgrening åtgärd):

- **Lägga till rader**. Sammanfoga två eller flera dataflöden lodrätt (efter rader). 
- **Lägg till kolumner**. Sammanfoga två eller flera dataflöden vågrätt (Kolumnvis).


>[!NOTE]
>Lägg till kolumner misslyckas om kolumnen kollision inträffar.


När du har en merge-operation refererar en eller flera dataflöden till ett käll-dataflöde. Dataförberedelser meddelar dig ett meddelande i det nedre högra hörnet på appen under listan med steg.


Alla åtgärder på det refererade dataflödet kräver överordnade dataflödet att uppdatera det prov som används från refererade dataflödet. I sådana fall ersätter en bekräftelsedialogruta data flow referens meddelandet i det nedre högra hörnet. Dialogrutan bekräftar att du behöver uppdatera dataflödet att synkronisera ändringar i alla beroende dataflöden.

### <a name="list-of-appendices"></a>Lista över tillägg 
* [Datakällor som stöds](data-prep-appendix2-supported-data-sources.md)  
* [Transformeringar som stöds](data-prep-appendix3-supported-transforms.md)  
* [Kontroller som stöds](data-prep-appendix4-supported-inspectors.md)  
* [Stöds mål](data-prep-appendix5-supported-destinations.md)  
* [Exemplet filteruttryck i Python](data-prep-appendix6-sample-filter-expressions-python.md)  
* [Exemplet Transformera data flow-uttryck i Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
* [Exemplet datakällor i Python](data-prep-appendix8-sample-source-connections-python.md)  
* [Anslutningar till exempel datamål i Python](data-prep-appendix9-sample-destination-connections-python.md)  
* [Exemplet kolumnen transformeringar i Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  
