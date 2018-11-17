---
title: Köra Python machine learning-skript | Microsoft Docs
description: Beskrivs designprinciper underliggande stöd för Python-skript i Azure Machine Learning och grundläggande Användningsscenarier, funktioner och begränsningar.
keywords: Python maskininlärning, pandas, python pandas, python-skript, köra python-skript
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.custom: (previous ms.author hshapiro)
ms.author: amlstudiodocs
manager: hjerez
editor: cgronlun
ms.assetid: ee9eb764-0d3e-4104-a797-19fc29345d39
ms.service: machine-learning
ms.component: studio
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.openlocfilehash: 1d79eaacf61a51a9c74a6db8a508050cde20ab46
ms.sourcegitcommit: 8899e76afb51f0d507c4f786f28eb46ada060b8d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2018
ms.locfileid: "51821216"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Kör skript för Python-maskininlärning i Azure Machine Learning Studio

Det här avsnittet beskrivs de designprinciperna underliggande aktuella stöd för Python-skript i Azure Machine Learning. De viktigaste funktionerna markeras också, inklusive:

- köra grundläggande Användningsscenarier
- poäng ett experiment i en webbtjänst
- stöd för att importera befintlig kod
- Exportera visualiseringar
- utföra övervakat Funktionsurval
- Förstå vissa begränsningar

[Python](https://www.python.org/) är ett oumbärligt verktyg i verktyget bröstet många dataexperter. Den har:

* en elegant och tydlig syntax 
* support för alla plattformar 
* en omfattande uppsättning kraftfulla bibliotek, och 
* mogen utvecklingsverktyg. 

Python som används i alla faser i ett arbetsflöde som vanligtvis används i machine learning modellering:

- inmatning av data och bearbetning 
- funktionen konstruktion
- Modellträning 
- modell-verifiering
- distribution av modeller

Bädda in Python-skript i olika delar av en machine learning-experiment och publicera dem också smidigt som webbtjänster på Microsoft Azure har stöd för Azure Machine Learning Studio.

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]


## <a name="design-principles-of-python-scripts-in-machine-learning"></a>Designprinciper för Python-skript i Machine Learning

Det primära gränssnittet till Python i Azure Machine Learning Studio är via den [kör Python-skript] [ execute-python-script] modulen som visas i bild 1.

![image1](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![image2](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

Figur 1. Den **kör Python-skript** modulen.

Den [kör Python-skript] [ execute-python-script] modul i Azure ML Studio accepterar upp till tre indata och producerar upp till två utdata (beskrivs i följande avsnitt), som dess R-analog den [köra R Skriptet] [ execute-r-script] modulen. Python-kod som ska köras har angetts i parameterrutan som ett särskilt namngivna startpunkt anropade funktionen `azureml_main`. Här följer de viktiga designprinciperna som används för att implementera den här modulen:

1. *Måste vara idiomatiskt för Python-användare.* De flesta Python användare ta sin kod som funktioner i moduler. Placera så mycket av körbara uttryck i en översta modul är relativt sällsynta. Rutan skriptet tar därför även en särskild Python-funktion i stället för bara en sekvens av uttryck. De objekt som exponeras i funktionen är Standardtyper för Python-bibliotek som [Pandas](http://pandas.pydata.org/) dataramar och [NumPy](http://www.numpy.org/) matriser.
2. *Måste ha hifi mellan lokala och molnbaserade körningar.* Serverdelen som används för att köra Python-koden är baserad på [Anaconda](https://store.continuum.io/cshop/anaconda/), ett används mycket plattformsoberoende vetenskapliga Python-distribution. Medföljer nära 200 av de vanligaste Python-paketen. Dataexperter kan därför Felsök och kvalificera sin kod på sina lokala Azure Machine Learning-kompatibla Anaconda-miljön. Använda en befintlig utvecklingsmiljö, t.ex [IPython](http://ipython.org/) anteckningsboken eller [Python Tools för Visual Studio](https://aka.ms/ptvs), för att köra den som en del av ett Azure ML-experiment. Den `azureml_main` startpunkt är en vanliga Python-funktion och det *** kan skapas utan Azure ML-specifik kod eller SDK: N installerad.
3. *Måste vara sömlöst kan med andra Azure Machine Learning-moduler.* Den [kör Python-skript] [ execute-python-script] modulen accepterar, som indata och utdata, standard Azure Machine Learning-datauppsättningar. Underliggande ramverk överbryggar transparent och effektivt Azure ML och Python-körningar. Så kan Python användas tillsammans med befintliga Azure ML-arbetsflöden, inklusive de som anropa R- och sqlite-felkod. Resultatet blir dataexpert kan skapa arbetsflöden som:
   * använda Python och Pandas för förbearbetning och rensning
   * feed-data till en SQL-omvandling, koppla flera datauppsättningar till formulärfunktioner
   * Träna modeller med algoritmerna i Azure Machine Learning 
   * utvärdera och efter bearbeta resultaten med R.


## <a name="basic-usage-scenarios-in-ml-for-python-scripts"></a>Grundläggande Användningsscenarier i ML för Python-skript

I det här avsnittet ska vi en undersökning några av de grundläggande användning av den [kör Python-skript] [ execute-python-script] modulen. Indata till Python-modulen visas som Pandas dataramar. Funktionen måste returnera en enda Pandas dataram paketeras i ett Python [sekvens](https://docs.python.org/2/c-api/sequence.html) , till exempel en tuppel, lista eller NumPy matris. Det första elementet i den här sekvensen returneras sedan i den första utdataporten för modulen. Det här schemat visas i bild 2.

![image3](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

Figur 2. Mappning av inkommande portar till parametrar och returnerar värdet till utdataporten.

Mer detaljerad semantiken för hur de inkommande portarna mappas till parametrarna för den `azureml_main` funktionen visas i tabell 1:

![image1T](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

Tabell 1. Mappning av inkommande portar till funktionsparametrar.

Mappningen mellan indataportar och funktionsparametrar är namngivna:

- Den första anslutna indataporten mappas till den första parametern för funktionen. 
- Andra indata (om ansluten) mappas till den andra parametern för funktionen.

Se *Python för dataanalys* (O'Reilly 2012) av västra McKinney för mer information om Python Pandas och hur den kan användas för att manipulera data effektivt. 


## <a name="translation-of-input-and-output-types"></a>Översättning av inkommande och utgående typer 
Indatauppsättningar i Azure ML konverteras till dataramar i Pandas. Utdata dataramar konverteras tillbaka till Azure ML-datauppsättningar. Följande konverteringar utförs:

1. Strängvärden och numeriska kolumner konverteras som – är och värden som saknas i en datauppsättning konverteras till ”NA' värden i Pandas. Samma konverteringen sker på väg tillbaka (NA värdena i Pandas konverteras till värden som saknas i Azure ML).
2. Index vektorer i Pandas stöds inte i Azure ML. Alla indata bildrutor i Python-funktionen har alltid ett 64-bitars numeriskt index från 0 till antalet rader minus 1. 
3. Azure ML-datauppsättningar kan inte ha duplicerade kolumnnamn och kolumnnamn som inte är strängar. Om en dataram i utdata innehåller icke-numeriska kolumner, ramverket anropar `str` på kolumnnamnen. På samma sätt fullkvalificeras alla dubbletter av kolumnnamn automatiskt för att se till att namnen är unika. Suffix (2) läggs till den första Dubbletten, (3) till andra dubblett och så vidare.


## <a name="operationalizing-python-scripts"></a>Operationalisera Python-skript

Alla [kör Python-skript] [ execute-python-script] moduler som används i en arbetsflödesbaserad experiment anropas när publiceras som en webbtjänst. Bild 3 visar till exempel en arbetsflödesbaserad experiment som innehåller koden för att utvärdera ett enda Python-uttryck. 

![image4](./media/execute-python-scripts/figure3a.png)

![image5](./media/execute-python-scripts/python-script-with-python-pandas.png)

Figur 3. Webbtjänsten för att utvärdera ett Python-uttryck.

En webbtjänst som skapats från det här experimentet:

- tar som indata ett Python-uttryck (som sträng)
- skickar den till Python-tolk 
- Returnerar en tabell som innehåller både uttrycket och det beräknade resultatet.


## <a name="importing-existing-python-script-modules"></a>Importera befintliga Python-skript-moduler

Det är ett vanligt användningsfall för många dataexperter att införliva befintliga Python-skript i Azure ML-experiment. I stället för att kräva att all kod att sammanfogas och klistras in i en enda skript-ruta i [kör Python-skript] [ execute-python-script] accepterar en zip-fil som innehåller Python-moduler på den tredje indataporten för modulen. Filen är packat av ramverket för körning vid körning och innehållet läggs till i sökvägen till bibliotek för Python-tolk. Den `azureml_main` startpunkt funktion kan sedan importera dessa moduler direkt.

Överväg att filen Hello.py som innehåller en enkel ”Hello, World”-funktion som ett exempel.

![image6](./media/execute-python-scripts/figure4.png)

Figur 4. Användardefinierad funktion i Hello.py-filen.

Nu ska skapa vi en fil Hello.zip som innehåller Hello.py:

![image7](./media/execute-python-scripts/figure5.png)

Figur 5. ZIP-filen som innehåller användardefinierade Python-kod.

Ladda upp zip-filen som en datauppsättning i Azure Machine Learning Studio. Sedan skapar och kör ett experiment som använder Python-koden i filen Hello.zip genom att koppla den till den tredje porten på den **kör Python-skript** modulen, som visas i den här bilden.

![image8](./media/execute-python-scripts/figure6a.png)

![image9](./media/execute-python-scripts/figure6b.png)

Figur 6. Exempelexperimentet med användardefinierade Python-kod laddas upp som en zip-fil.

Modulen utdata visar att zip-filen har oförpackat och att funktionen `print_hello` har körts.
 
![image10](./media/execute-python-scripts/figure7.png)

Figur 7. Användardefinierad funktion som används i den [kör Python-skript] [ execute-python-script] modulen.


## <a name="working-with-visualizations"></a>Arbeta med visualiseringar

Diagrammen som skapats med hjälp av MatplotLib som kan visualiseras i webbläsare kan returneras av den [kör Python-skript][execute-python-script]. Men områdena automatiskt dirigeras inte till avbildningar som de är med R. Så måste du uttryckligen spara alla områden i PNG-filer om de ska returneras tillbaka till Azure Machine Learning. 

Om du vill skapa avbildningar från MatplotLib, måste du utföra följande procedur:

* Växla serverdelen till ”%{AGG/” från Qt-baserade standardåtergivning 
* Skapa ett nytt objekt i bild 
* Hämta axeln och generera alla områden i den 
* Spara bilden i en PNG-fil 

Den här processen illustreras i följande figur 8 som skapar en matris för diagram av punktdiagram med hjälp av funktionen scatter_matrix i Pandas.

![image1v](./media/execute-python-scripts/figure-v1-8.png)

Figur 8. Kod för att spara MatplotLib figurerna i bilder.

Bild 9 illustrerar ett experiment som använder skriptet som visades tidigare för att returnera ritar via andra utdataporten.

![image2v](./media/execute-python-scripts/figure-v2-9a.png) 

![image2v](./media/execute-python-scripts/figure-v2-9b.png) 

Figur 9. Visualisera områden som genereras från Python-kod.

Det är möjligt att returnera flera bilder genom att spara dem i olika bilder, Azure Machine Learning-runtime hämtar alla avbildningar och sammanfogar dem för visualisering.


## <a name="advanced-examples"></a>Avancerade exempel

Anaconda-miljö som installerats i Azure Machine Learning innehåller vanliga paket som till exempel NumPy, SciPy och lär du dig Scikits. Dessa paket kan användas effektivt för olika uppgifter, databearbetning i machine learning-pipeline. Visar användningen av ensemble deltagarna i Scikits – Lär dig beräkna funktionen vikten poäng för en datauppsättning till exempel följande experiment och skript. Poängen kan användas för att utföra övervakat Funktionsurval innan som matas in en annan ML-modell.

Här är Python-funktion som används för att beräkna vikten poängen och ordning funktionerna baserat på poängen:

![image11](./media/execute-python-scripts/figure8.png)

Figur 10. Funktionen för att rangordnas funktioner av resultat.
 
Följande experiment finns sedan beräknar och returnerar poängen vikten av funktioner i datauppsättningen ”Pima indiska Diabetes” i Azure Machine Learning:

![image12](./media/execute-python-scripts/figure9a.png)
![image13](./media/execute-python-scripts/figure9b.png)    

Figur 11. Experimentera till rangordnas funktioner i datauppsättningen Pima indiska Diabetes.

## <a name="limitations"></a>Begränsningar
Den [kör Python-skript] [ execute-python-script] för närvarande har följande begränsningar:

1. *I begränsat läge för körning.* Python-körningen är för närvarande i begränsat läge och därför tillåter inte åtkomst till nätverket eller till det lokala filsystemet på ett beständigt sätt. Alla filer som sparats lokalt är isolerade och tas bort när modulen är klar. Python-koden kan inte komma åt de flesta kataloger på datorn som används på, undantaget den aktuella katalogen och dess underkataloger.
2. *Brist på avancerade utveckling och felsökning.* Python-modulen stöder för närvarande inte IDE-funktioner som intellisense och felsökning. Även om modulen inte vid körning, finns fullständiga stackspårningen för Python. Men den visas i utdataloggen för modulen. För närvarande rekommenderar vi att utveckla och felsöka Python-skript i en miljö, till exempel IPython och importera sedan koden i modulen.
3. *Enkel data frame utdata.* Startpunkten Python tillåts bara för att returnera en enda dataram som utdata. Det går för närvarande inte att returnera valfri Python-objekt, till exempel anpassade modeller direkt till Azure Machine Learning-runtime. Som [kör R-skript][execute-r-script], som har samma begränsningar, det är möjligt i många fall att pickle objekt i en byte-matris och returnera som inuti en dataram.
4. *Det går inte att anpassa Python-installationen*. Det enda sättet att lägga till anpassad Python-moduler är för närvarande via mekanismen zip-fil som beskrivs ovan. Även om det är möjligt för små moduler, är det besvärligt för stora moduler (särskilt de med interna DLL: er) eller ett stort antal moduler. 

## <a name="conclusions"></a>Slutsatser
Den [kör Python-skript] [ execute-python-script] modulen gör någon dataexpert att införliva befintliga Python-kod i molnet maskininlärning arbetsflöden i Azure Machine Learning och till sömlöst operationalisera dem som en del av en webbtjänst. Python-skriptmodul samverkar naturligt med andra moduler i Azure Machine Learning. Modulen kan användas för en serie aktiviteter från datagranskning att bearbeta data i förväg och extrahering av funktionen och sedan till utvärdering och efter bearbetning av resultaten. Backend-runtime som används vid körningen baseras på Anaconda, en väl beprövad och vanligt Python-distribution. Serverdelen gör det enkelt för dig att integrera befintliga kod tillgångar till molnet.

Vi förväntar oss att tillhandahålla ytterligare funktioner i [kör Python-skript] [ execute-python-script] modulen, till exempel möjligheten att träna och operationalisera modeller i Python och att ge bättre stöd för utveckling och felsöka kod i Azure Machine Learning Studio.

## <a name="next-steps"></a>Nästa steg
Mer information finns i [Python Developer Center](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://msdn.microsoft.com/library/azure/cdb56f95-7f4c-404d-bde7-5bb972e6f232/
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
