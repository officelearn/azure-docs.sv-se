---
title: Köra Python machine learning-skript
titleSuffix: Azure Machine Learning Studio
description: Lär dig hur du använder Python i Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: 4b4f3877b56752756050de0af226571ac2a93293
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888168"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio"></a>Kör skript för Python-maskininlärning i Azure Machine Learning Studio

Python är ett ovärderligt verktyg i verktyget bröstet många dataexperter. Den används i varje skede av typisk machine learning-arbetsflöden, bland annat datagranskning, extrahering av funktionen, modellträning och verifiering och distribution.

Den här artikeln beskrivs hur du kan använda modulen kör Python-skript för att använda Python-kod i Azure Machine Learning Studio-experiment och webbtjänster.

## <a name="using-the-execute-python-script-module"></a>Med hjälp av modulen kör Python-skript

Det primära gränssnittet till Python i Studio är via den [kör Python-skript] [ execute-python-script] modulen. Den accepterar upp till tre indata och ger upp till två utdata som liknar den [kör R-skript] [ execute-r-script] modulen. Python-kod har angetts i parameterrutan via en särskild startpunkt-funktion som kallas `azureml_main`.

![Köra Python-skriptet modul](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Python-exempelkoden i modulen parametern box](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Indataparametrar

Indata till Python-modulen visas som Pandas dataramar. Den `azureml_main` funktionen accepterar upp till två valfria Pandas dataramar som parametrar.

Mappningen mellan indataportar och funktionsparametrar är namngivna:

- Den första anslutna indataporten mappas till den första parametern för funktionen.
- Andra indata (om ansluten) mappas till den andra parametern för funktionen.
- Tredje indata används för att [importera ytterligare Python-moduler](#import-modules).

Mer detaljerad semantiken för hur de inkommande portarna mappas till parametrarna för den `azureml_main` funktionen visas nedan.

![Tabell över indataporten konfigurationer och resulterande Python-signatur](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Utdata returvärden

Den `azureml_main` funktionen måste returnera en enda Pandas-DataFrame paketeras i ett Python [sekvens](https://docs.python.org/2/c-api/sequence.html) , till exempel en tuppel, lista eller NumPy matris. Det första elementet i den här sekvensen returneras till den första utdataporten för modulen. Den andra utdataporten för modulen används för [visualiseringar](#visualizations) och kräver inte ett returvärde. Det här schemat visas nedan.

![Mappning av inkommande portar till parametrar och returnerar värdet till utdataporten](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Översättning av inkommande och utgående datatyper

Studio datauppsättningar är inte samma som Panda dataramar. Därför indatauppsättningar i Studio konverteras till Pandas-DataFrame och utdata dataramar konverteras till Studio datauppsättningar. Under den här konverteringen utförs också följande översättningarna:

 **Python-datatypen** | **Studio translation proceduren** |
| --- | --- |
| Strängar och siffror| Översatta skick |
| Pandas 'NA ” | Översatta som saknas-värde |
| Index vektorer | Stöds inte * |
| Kolumnnamn för icke-sträng | Anropa `str` på kolumnnamn |
| Dubbletter av kolumnnamn | Lägg till numeriskt suffix: (1), (2), (3), och så vidare.

**Alla indata bildrutor i Python-funktionen alltid ska ha ett 64-bitars numeriskt index från 0 till antalet rader minus 1*

## <a id="import-modules"></a>Importera befintliga Python-skript-moduler

Serverdelen som används för att köra Python baseras på [Anaconda](https://store.continuum.io/cshop/anaconda/), ett används mycket vetenskapliga Python-distribution. Medföljer nära 200 av de vanligaste Python-paketen som används i datacentrerade arbetsbelastningar. Studio stöder för närvarande inte användning av paketet av innehållshanteringssystem som Pip eller Conda för att installera och hantera externa bibliotek.  Om du upptäcker att behöva lägga till ytterligare bibliotek, Använd följande scenario som en vägledning.

Det är ett vanligt användningsfall att införliva befintliga Python-skript i Studio-experiment. Den [kör Python-skript] [ execute-python-script] accepterar en zip-fil som innehåller Python-moduler på den tredje indataporten för modulen. Filen är packat av ramverket för körning vid körning och innehållet läggs till i sökvägen till bibliotek för Python-tolk. Den `azureml_main` startpunkt funktion kan sedan importera dessa moduler direkt. 

Överväg att filen Hello.py som innehåller en enkel ”Hello, World”-funktion som ett exempel.

![Användardefinierad funktion i Hello.py-filen](./media/execute-python-scripts/figure4.png)

Nu ska skapa vi en fil Hello.zip som innehåller Hello.py:

![ZIP-filen som innehåller användardefinierade Python-kod](./media/execute-python-scripts/figure5.png)

Ladda upp zip-filen som en datauppsättning i Studio. Sedan skapar och kör ett experiment som använder Python-koden i filen Hello.zip genom att koppla den till den tredje porten på den **kör Python-skript** modulen enligt följande bild.

![Exempelexperimentet med Hello.zip som indata till en kör Python-skript-modul](./media/execute-python-scripts/figure6a.png)

![Användardefinierade Python-kod som laddas upp som en zip-fil](./media/execute-python-scripts/figure6b.png)

Modulen utdata visar att zip-filen har oförpackat och att funktionen `print_hello` har körts.

![Modulen utdata visar användardefinierad funktion](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Åtkomst till Azure Storage-Blobbar

Du kan komma åt data som lagras i ett Azure Blob Storage-konto med de här stegen:

1. Ladda ned den [Azure Blob Storage-paketet för Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokalt.
1. Ladda upp zip-filen till Studio-arbetsyta som en datauppsättning.
1. Skapa BlobService-objekt med `protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Inaktivera **säker överföring krävs** i Storage **Configuration** inställningsfliken

![Inaktivera säker överföring krävs i Azure portal](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operationalisera Python-skript

Alla [kör Python-skript] [ execute-python-script] moduler som används i en arbetsflödesbaserad experiment anropas när publiceras som en webbtjänst. Bilden nedan visar exempelvis en arbetsflödesbaserad experiment som innehåller koden för att utvärdera ett enda Python-uttryck.

![Studio-arbetsyta för en webbtjänst](./media/execute-python-scripts/figure3a.png)

![Python Pandas-uttryck](./media/execute-python-scripts/python-script-with-python-pandas.png)

En webbtjänst som skapats från det här experimentet vidtar följande åtgärder:

1. Ta ett Python-uttryck som indata (som sträng)
1. Skicka Python-uttryck till Python-tolk
1. Returnerar en tabell som innehåller både uttrycket och det beräknade resultatet.

## <a id="visualizations"></a>Arbeta med visualiseringar

Diagrammen som skapats med hjälp av MatplotLib kan returneras av den [kör Python-skript][execute-python-script]. Dock omdirigeras områden inte automatiskt till avbildningar som de är med R. Så måste användaren explicit spara alla områden i PNG-filer.

Om du vill skapa avbildningar från MatplotLib, måste du utföra följande steg:

1. Växla serverdelen till ”%{AGG/” från standardåtergivning Qt-baserade.
1. Skapa en ny bild-objekt.
1. Få axeln och generera alla områden i den.
1. Spara bilden på en PNG-fil.

Den här processen illustreras i följande avbildningar som skapar en matris för diagram av punktdiagram med hjälp av funktionen scatter_matrix i Pandas.

![Kod för att spara MatplotLib figurerna i bilder](./media/execute-python-scripts/figure-v1-8.png)

![Klicka på visualisera på en kör Python-skript-modul för att se siffrorna](./media/execute-python-scripts/figure-v2-9a.png)

![Visualisera områden för en exempelexperimentet med hjälp av Python-kod](./media/execute-python-scripts/figure-v2-9b.png)

Det är möjligt att returnera flera bilder genom att spara dem i olika bilder. Studio-runtime hämtar alla avbildningar och sammanfogar dem för visualisering.

## <a name="advanced-examples"></a>Avancerade exempel

Anaconda-miljö som installerats i Studio innehåller vanliga paket som till exempel NumPy, SciPy och lär du dig Scikits. Dessa paket kan användas effektivt för att bearbeta i machine learning-pipeline.

Till exempel illustrerar följande experiment och skript användningen av ensemble deltagarna i Scikits – Lär dig beräkna funktionen vikten poäng för en datauppsättning. Poängen kan användas för att utföra övervakat Funktionsurval innan som matas in en annan modell.

Här är Python-funktion som används för att beräkna vikten poängen och ordning funktionerna baserat på poängen:

![Funktionen för att rangordnas funktioner av resultat](./media/execute-python-scripts/figure8.png)

Följande experiment finns sedan beräknar och returnerar poängen vikten av funktioner i datauppsättningen ”Pima indiska Diabetes” i Azure Machine Learning Studio:

![Experimentera till rangordnas funktioner i Pima indiska Diabetes datauppsättningen med hjälp av Python](./media/execute-python-scripts/figure9a.png)

![Visualisering av utdata från modulen kör Python-skript](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Begränsningar

Den [kör Python-skript] [ execute-python-script] modulen har för närvarande följande begränsningar:

### <a name="sandboxed-execution"></a>I begränsat läge körning

Python-körningen är för närvarande i begränsat läge och tillåter inte åtkomst till nätverket eller det lokala filsystemet på ett beständigt sätt. Alla filer som sparats lokalt är isolerade och tas bort när modulen är klar. Python-koden kan inte komma åt de flesta kataloger på datorn som används på, undantaget den aktuella katalogen och dess underkataloger.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Brist på avancerade utveckling och felsökning

Python-modulen stöder för närvarande inte IDE-funktioner som intellisense och felsökning. Även om modulen inte vid körning, finns fullständiga stackspårningen för Python. Men den visas i utdataloggen för modulen. För närvarande rekommenderar vi att utveckla och felsöka Python-skript i en miljö, till exempel IPython och importera sedan koden i modulen.

### <a name="single-data-frame-output"></a>Enkel data frame utdata

Startpunkten Python tillåts bara för att returnera en enda dataram som utdata. Det går för närvarande inte att returnera valfri Python-objekt, till exempel anpassade modeller direkt till Studio-runtime. Som [kör R-skript][execute-r-script], som har samma begränsningar, det är möjligt i många fall att pickle objekt i en byte-matris och returnera som inuti en dataram.

### <a name="inability-to-customize-python-installation"></a>Det går inte att anpassa Python-installationen

Det enda sättet att lägga till anpassad Python-moduler är för närvarande via mekanismen zip-fil som beskrivs ovan. Även om det är möjligt för små moduler, är det besvärligt för stora moduler (särskilt moduler med interna DLL: er) eller ett stort antal moduler.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Python Developer Center](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script