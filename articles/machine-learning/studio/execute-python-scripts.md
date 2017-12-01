---
title: "Kör skript för Python machine learning | Microsoft Docs"
description: "Ger en översikt över utforma principerna stöd för Python-skript i Azure Machine Learning och grundläggande Användningsscenarier, funktioner och begränsningar."
keywords: "Python maskininlärning pandas, python pandas, python-skript för körning av python-skript"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: bradsev;garye
ms.openlocfilehash: c25f31ca72417672298657c4585184ad72db6c99
ms.sourcegitcommit: 5a6e943718a8d2bc5babea3cd624c0557ab67bd5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/01/2017
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Kör skript för Python-maskininlärning i Azure Machine Learning Studio

Det här avsnittet beskrivs de aktuella stödet för Python-skript i Azure Machine Learning designprinciperna. De huvudsakliga funktionerna som också beskrivs, inklusive:

- köra grundläggande Användningsscenarier
- resultatet av ett experiment i en webbtjänst
- stöd för att importera befintlig kod
- Exportera visualiseringar
- utföra övervakat Funktionsurval
- Förstå vissa begränsningar

[Python](https://www.python.org/) är ett ovärderligt verktyg för verktyget bröstet många data forskare. Det har:

* en elegant och koncis syntax 
* plattformsoberoende stöd 
* en omfattande uppsättning kraftfulla bibliotek och 
* mogen utvecklingsverktyg. 

Python som används i alla faser av ett arbetsflöde som vanligtvis används i machine learning modellering:

- mata in data och bearbetning 
- funktionen konstruktion
- modell-utbildning 
- modellverifiering
- distribution av modeller

Azure Machine Learning Studio stöder inbäddning Python-skript till olika delar av en dator learning experiment och publicera dem också sömlöst som webbtjänster på Microsoft Azure.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Designprinciperna för Python-skript i Machine Learning

Det primära gränssnittet till Python i Azure Machine Learning Studio är via den [köra Python-skriptet] [ execute-python-script] modulen som visas i bild 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Bild 1. Den **köra Python-skriptet** modul.

Den [köra Python-skriptet] [ execute-python-script] modul i Azure ML Studio accepterar upp till tre indata och ger upp till två utdata (beskrivs i följande avsnitt), t.ex. dess R-analog den [köra R-skriptet] [ execute-r-script] modul. För att köra Python-kod har angetts i parameterrutan som ett särskilt namngiven startpunkten anropade funktionen `azureml_main`. Här följer principerna för viktiga designbeslut som används för att implementera den här modulen:

1. *Måste vara idiomatiska för Python-användare.* De flesta Python användare factor koden som funktioner i moduler. Placera så mycket körbara instruktioner i en översta modul är relativt ovanligt. Därför tar skript kryssrutan också en särskild Python-funktion i stället för bara en sekvens av rapporter. De objekt som exponeras i funktionen är till exempel Python biblioteket Standardtyper [Pandas](http://pandas.pydata.org/) dataramar och [NumPy](http://www.numpy.org/) matriser.
2. *Måste ha exakt mellan lokala och molntjänster körningar.* Serverdelen som används för att köra Python-kod är baserad på [Anaconda](https://store.continuum.io/cshop/anaconda/)en vanlig plattformsoberoende vetenskapliga Python-distribution. Den innehåller nära 200 av de vanligaste Python-paket. Därför dataanalytiker felsöka och kvalificera koden på sina lokala Azure Machine Learning-kompatibla Anaconda-miljön. Använd en befintlig utvecklingsmiljö som [IPython](http://ipython.org/) bärbar dator eller [Python Tools för Visual Studio](http://aka.ms/ptvs), för att köra den som en del av ett experiment i Azure ML. Den `azureml_main` startadressen är en vanliga Python-funktion och kan därför *** kan skapas utan Azure ML-specifik kod eller SDK installerad.
3. *Måste vara sömlöst sammanställningsbara med andra Azure Machine Learning-moduler.* Den [köra Python-skriptet] [ execute-python-script] modulen accepterar, som indata och utdata, standard Azure Machine Learning datauppsättningar. Underliggande ramverk få transparent och effektivt sätt körningar Azure ML och Python. Så kan Python användas tillsammans med befintliga Azure ML-arbetsflöden, inklusive de som anropar R och SQLite. Resulterar i att data forskare kan skapa arbetsflöden som:
   * använda Python och Pandas för förbearbetning och rensning
   * feed data till en SQL-omvandling ansluta flera DataSet till formulärfunktioner
   * Träna modeller med algoritmer i Azure Machine Learning 
   * utvärdera och efterbearbetning resultat med R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Grundläggande Användningsscenarier i ML för Python-skript

I det här avsnittet vi en undersökning några grundläggande användning av den [köra Python-skriptet] [ execute-python-script] modul. Indata till Python-modul exponeras som Pandas ramar. Funktionen måste returnera en Pandas data bildruta paketeras i en Python [sekvens](https://docs.python.org/2/c-api/sequence.html) , till exempel en tuppel, lista eller NumPy matris. Det första elementet i den här sekvensen returneras sedan den första utdataporten för modulen. Det här schemat visas i bild 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figur 2. Mappningen av inkommande portar till parametrar och returnera värdet till utdataporten.

Mer detaljerad semantiken för hur portarna hämta mappas till parametrarna för den `azureml_main` funktionen visas i tabell 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabell 1. Mappning av portar till funktionsparametrar.

Mappningen mellan portar och funktionsparametrar är namngivna:

- Den första anslutna indataporten är mappad till den första parametern för funktionen. 
- Andra indata (om ansluten) mappas till den andra parametern för funktionen.

Se *Python för dataanalys* (O'Reilly 2012) av västra McKinney för mer information om Python Pandas och hur det kan användas för att manipulera data effektivt. 


## <a name="translation-of-input-and-output-types"></a>Översättning av inkommande och utgående typer 
Indatauppsättningar i Azure ML konverteras till ramar i Pandas. Utdata dataramar konverteras tillbaka till Azure ML datauppsättningar. Följande konverteringar utförs:

1. Sträng och numeriska kolumner konverteras som-är och värden som saknas i en dataset konverteras till ”NA-värden i Pandas. Samma konverteringen sker på sättet tillbaka (NA värden i Pandas konverteras till värden som saknas i Azure ML).
2. Index angreppsmetoderna i Pandas stöds inte i Azure ML. Alla indata ramar i Python-funktionen har alltid ett 64-bitars numeriskt index från 0 till antalet rader minus 1. 
3. Azure ML-datauppsättningar kan inte ha dubbletter av kolumnnamn och kolumnnamn som inte är strängar. Om en utgående data ram innehåller icke-numeriska kolumner, ramen anropar `str` på kolumnnamn. På samma sätt fullkvalificeras några dubbletter av kolumnnamn automatiskt för att försäkra att namnen är unika. Suffix (2) läggs till de första duplicerade, (3) till andra dubblett och så vidare.


## <a name="operationalizing-python-scripts"></a>Operationalizing Python-skript

Alla [köra Python-skriptet] [ execute-python-script] moduler som används i ett bedömningsprofil experiment anropas när publiceras som en webbtjänst. Bild 3 visar till exempel ett bedömningsprofil experiment som innehåller koden för att utvärdera ett enda Python-uttryck. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Bild 3. Webbtjänsten för utvärdering av ett Python-uttryck.

En webbtjänst som skapas från den här experiment:

- tar som indata en Python-uttryck (som sträng)
- skickar det till Python-tolkning 
- Returnerar en tabell som innehåller både uttrycket och det beräknade resultatet.


## <a name="importing-existing-python-script-modules"></a>Importera befintlig Python skriptmoduler

Ett vanligt användningsområde för många datavetare är att lägga till befintliga Python-skript i Azure ML experiment. I stället för att kräva att all kod som sammanfogas och klistras in i ett enda skript kryssrutan den [köra Python-skriptet] [ execute-python-script] modulen accepterar en zip-fil som innehåller Python-moduler på den tredje indataporten. Filen är uppackade av körningen framework vid körning och innehållet läggs till i sökvägen till bibliotek för Python-tolkning. Den `azureml_main` startpunkten funktion kan sedan importera dessa moduler direkt.

Exempelvis bör du filen Hello.py som innehåller en enkel ”Hello, World”-funktionen.

![image6](./media/execute-python-scripts/figure4.png)

Bild 4. Användardefinierad funktion i Hello.py-filen.

Nu ska skapa vi en fil Hello.zip som innehåller Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Bild 5. ZIP-filen som innehåller användardefinierade Python-kod.

Ladda upp zip-filen som en datamängd i Azure Machine Learning Studio. Sedan skapar och kör ett experiment som använder Python-kod i filen Hello.zip genom att koppla till den tredje indataporten av den **kör Python-skript** modulen som visas i bilden.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Bild 6. Exempelexperimentet med användardefinierade Python-kod överförs som en zip-fil.

Modulen utdata visar att zip-filen har anspråksregellogik och att funktionen `print_hello` har körts.
 
![image10](./media/execute-python-scripts/figure7.png)

Bild 7. Användardefinierad funktion som används i den [köra Python-skriptet] [ execute-python-script] modul.


## <a name="working-with-visualizations"></a>Arbeta med grafik

Områden som skapats med hjälp av MatplotLib som kan vara visualiseras i webbläsare som kan returneras av den [köra Python-skriptet][execute-python-script]. Men områdena omdirigeras inte automatiskt till bilder som de är när du använder R. Så måste användaren uttryckligen spara alla områden i PNG-filer om de ska returneras till Azure Machine Learning. 

Du måste slutföra följande procedur för att generera bilder från MatplotLib:

* Växla serverdelen till ”%{AGG/” från Qt-baserade standardåtergivning 
* Skapa ett nytt objekt i bild 
* Hämta axeln och generera alla områden i det. 
* Spara bilden i en PNG-fil 

Den här processen illustreras i följande figur 8 som skapar en punktdiagram ritytans matris med hjälp av funktionen scatter_matrix i Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Figur 8. Kod för att spara MatplotLib siffror till avbildningar.

Bild 9 illustrerar ett experiment som använder skriptet visats att returnera ritas via andra utdataporten.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Bild 9. Visualisera områden som genereras av Python-kod.

Det är möjligt att returnera flera siffror genom att spara dem i olika bilder, Azure Machine Learning-körningsmiljön hämtar alla bilder och sammanfogar dem för visualisering.


## <a name="advanced-examples"></a>Avancerade exempel

Den Anaconda environment har installerats i Azure Machine Learning innehåller vanliga paket som NumPy, SciPy och Läs Scikits. Dessa paket kan effektivt användas för olika aktiviteter för databehandling i machine learning-pipeline. Exempelvis visar följande experiment och skript hur du använder av ensemble deltagarna i Scikits veta att beräkna funktionen vikten poängen för en dataset. Poängen kan användas för att utföra övervakat Funktionsurval innan som matas in en annan ML-modell.

Här är Python-funktionen som används för att beräkna vikten poängen och ordning funktionerna baserat på poängen:

![image11](./media/execute-python-scripts/figure8.png)

Bild 10. Fungera rank funktioner av resultat.
 Följande experimentet sedan beräknar och returnerar resultaten vikten av funktioner i datauppsättningen ”Pima indiska Diabetes” i Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Figur 11. Experimentera rank funktioner i Pima indiska Diabetes datamängden.

## <a name="limitations"></a>Begränsningar
Den [köra Python-skriptet] [ execute-python-script] för närvarande har följande begränsningar:

1. *Begränsade körning.* Python-körning är för närvarande i begränsat läge och därför tillåter inte åtkomst till nätverket eller till det lokala filsystemet på ett beständigt sätt. Alla filer som sparats lokalt isolerats och tas bort när modulen har slutförts. Python-kod kan inte komma åt de flesta kataloger på datorn som körs på, undantaget som den aktuella katalogen och dess underkataloger.
2. *Ingen avancerade utveckling och felsökning support.* Python-modulen stöder för närvarande inte IDE-funktioner, till exempel intellisense och felsökning. Även om modulen inte vid körning, finns fullständig stack-spårning för Python. Men den visas i utdataloggen för för modulen. För närvarande rekommenderas att utveckla och Felsök Python-skript i en miljö, till exempel IPython och sedan importera koden i modulen.
3. *Enskild ram utdata.* Startpunkten Python tillåts bara för att returnera en enda data-ram som utdata. Det går för närvarande inte ska returneras godtyckliga Python-objekt, till exempel tränade modeller direkt tillbaka till Azure Machine Learning-körningsmiljön. Som [köra R-skriptet][execute-r-script], som har samma begränsningar, om det är möjligt i de flesta fall att pickle objekt i en bytematris och returnera som inuti en data-ram.
4. *När det gäller att anpassa Python installation*. Det enda sättet att lägga till anpassade Python-moduler är för närvarande via zip-filen mekanism som beskrivs ovan. Detta är möjligt för små moduler, är det besvärlig för stora moduler (särskilt de med inbyggda DLL-filer) eller ett stort antal moduler. 

## <a name="conclusions"></a>Slutsatser
Den [köra Python-skriptet] [ execute-python-script] modulen gör att en data-forskare att lägga till befintliga Python-kod i molnbaserade machine learning arbetsflöden i Azure Machine Learning och sömlöst operationalisera dem som en del av en webbtjänst. Modulen Python-skriptet samverkar naturligt med andra moduler i Azure Machine Learning. Modulen kan användas för ett antal uppgifter från datagranskning före bearbetning och extrahering av funktionen och sedan utvärdering och efter bearbetningen av resultaten. Backend-körningsmiljön som används för körning av baseras på Anaconda, en väl testade och vanliga Python-distribution. Den här backend gör det enkelt för dig inbyggd befintlig kod tillgångar till molnet.

Vi räknar med att ge ytterligare funktioner för den [köra Python-skriptet] [ execute-python-script] modulen, till exempel möjligheten att träna och operationalisera modeller i Python och ger bättre stöd för utveckling och felsökning av kod i Azure Machine Learning Studio.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Python Developer Center](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
