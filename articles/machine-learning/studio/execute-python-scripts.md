---
title: Köra Python-skript
titleSuffix: ML Studio (classic) - Azure
description: Lär dig hur du använder modulen Kör Python-skript för att använda Python-kod i Machine Learning Studio (klassiska) experiment och webbtjänster.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: previous-author=heatherbshapiro, previous-ms.author=hshapiro
ms.date: 03/12/2019
ms.openlocfilehash: c79f6bd63fa5d8d8c6b22ff271d8ca513a94fd64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79218086"
---
# <a name="execute-python-machine-learning-scripts-in-azure-machine-learning-studio-classic"></a>Kör Python-maskininlärningsskript i Azure Machine Learning Studio (klassiskt)

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Python är ett värdefullt verktyg i verktygskistan hos många datavetar. Den används i varje steg i typiska maskininlärningsarbetsflöden, inklusive datautforskning, funktionsextrahering, modellutbildning och validering och distribution.

I den här artikeln beskrivs hur du kan använda modulen Kör Python-skript för att använda Python-kod i dina klassiska Azure Machine Learning Studio-experiment och webbtjänster.

## <a name="using-the-execute-python-script-module"></a>Använda modulen Kör Python-skript

Det primära gränssnittet till Python i Studio (klassisk) är via [modulen Kör Python Script.][execute-python-script] Den accepterar upp till tre ingångar och producerar upp till två utgångar, liknande [modulen Kör R-skript.][execute-r-script] Python-kod anges i parameterrutan via en särskilt namngiven `azureml_main`startpunktsfunktion som kallas .

![Kör Python-skriptmodul](./media/execute-python-scripts/execute-machine-learning-python-scripts-module.png)

![Exempel på pythonkod i parameterrutan för modul](./media/execute-python-scripts/embedded-machine-learning-python-script.png)

### <a name="input-parameters"></a>Indataparametrar

Indata till Python-modulen visas som Pandas DataFrames. Funktionen `azureml_main` accepterar upp till två valfria Pandas DataFrames som parametrar.

Mappningen mellan indataportar och funktionsparametrar är positionell:

- Den första anslutna indataporten mappas till den första parametern i funktionen.
- Den andra ingången (om den är ansluten) mappas till den andra parametern i funktionen.
- Den tredje indata används för att [importera ytterligare Python-moduler](#import-modules).

Mer detaljerad semantik om hur indataportarna mappas till parametrar för `azureml_main` funktionen visas nedan.

![Tabell över konfigurationer för indataport och den resulterande Python-signaturen](./media/execute-python-scripts/python-script-inputs-mapped-to-parameters.png)

### <a name="output-return-values"></a>Returvärden för utdata

Funktionen `azureml_main` måste returnera en enda Pandas DataFrame som är paketerad i en [Python-sekvens,](https://docs.python.org/2/c-api/sequence.html) till exempel en tuppel, lista eller NumPy-matris. Det första elementet i den här sekvensen returneras till modulens första utdataport. Modulens andra utdataport används för [visualiseringar](#visualizations) och kräver inget returvärde. Detta schema visas nedan.

![Mappa indataportar till parametrar och returnera värde till utdataport](./media/execute-python-scripts/map-of-python-script-inputs-outputs.png)

## <a name="translation-of-input-and-output-data-types"></a>Översättning av datatyper för in- och utdata

Studio-datauppsättningar är inte samma sak som Panda DataFrames. Som ett resultat konverteras indatauppsättningar i Studio (klassisk) till Pandas DataFrame, och utdatadataramar konverteras tillbaka till Studio (klassiska) datauppsättningar. Under den här konverteringsprocessen utförs även följande översättningar:

 **Python-datatyp** | **Förfarande för studioöversättning** |
| --- | --- |
| Strängar och numeriska| Översatt som det är |
| Pandor "NA" | Översatt som "Saknat värde" |
| Indexvektorer | Stöds inte* |
| Kolumnnamn som inte är sträng | Anropa `str` kolumnnamn |
| Duplicera kolumnnamn | Lägg till numeriskt suffix: (1), (2), (3) och så vidare.

**Alla indataramar i Python-funktionen har alltid ett 64-bitars numeriskt index från 0 till antalet rader minus 1*

## <a name="importing-existing-python-script-modules"></a><a id="import-modules"></a>Importera befintliga Python-skriptmoduler

Den backend som används för att köra Python är baserad på [Anaconda](https://www.anaconda.com/distribution/), en allmänt använd vetenskaplig Python-distribution. Den levereras med nästan 200 av de vanligaste Python-paketen som används i datacentrerade arbetsbelastningar. Studio (klassisk) stöder för närvarande inte användningen av pakethanteringssystem som Pip eller Conda för att installera och hantera externa bibliotek.  Om du upptäcker behovet av att införliva ytterligare bibliotek använder du följande scenario som vägledning.

Ett vanligt användningsfall är att införliva befintliga Python-skript i Studio (klassiska) experiment. [Modulen Kör Python-skript][execute-python-script] accepterar en zip-fil som innehåller Python-moduler vid den tredje indataporten. Filen packas upp av körningsramverket vid körning och innehållet läggs till i bibliotekssökvägen för Python-tolken. Startpunktsfunktionen `azureml_main` kan sedan importera dessa moduler direkt. 

Som ett exempel bör du tänka på att filen Hello.py som innehåller en enkel "Hello, World"-funktion.

![Användardefinierad funktion i Hello.py fil](./media/execute-python-scripts/figure4.png)

Därefter skapar vi en fil Hello.zip som innehåller Hello.py:

![Zip-fil som innehåller användardefinierad Python-kod](./media/execute-python-scripts/figure5.png)

Ladda upp zip-filen som en datauppsättning till Studio (klassisk). Skapa och kör sedan ett experiment som använder Python-koden i filen Hello.zip genom att bifoga den till den tredje indataporten i modulen **Kör Python-skript** som visas i följande bild.

![Exempelexperiment med Hello.zip som indata till en Körning Python Script-modul](./media/execute-python-scripts/figure6a.png)

![Användardefinierad Python-kod som laddas upp som en zip-fil](./media/execute-python-scripts/figure6b.png)

Modulutdata visar att zip-filen har packats `print_hello` upp och att funktionen har körts.

![Modulutdata som visar användardefinierad funktion](./media/execute-python-scripts/figure7.png)

## <a name="accessing-azure-storage-blobs"></a>Komma åt Azure Storage Blobbar

Du kan komma åt data som lagras i ett Azure Blob Storage-konto med så här:

1. Hämta [Azure Blob Storage-paketet för Python](https://azuremlpackagesupport.blob.core.windows.net/python/azure.zip) lokalt.
1. Ladda upp zip-filen till din Studio -arbetsyta (klassisk) som en datauppsättning.
1. Skapa ditt BlobService-objekt med`protocol='http'`

```
from azure.storage.blob import BlockBlobService

# Create the BlockBlockService that is used to call the Blob service for the storage account
block_blob_service = BlockBlobService(account_name='account_name', account_key='account_key', protocol='http')
```

1. Inaktivera **säker överföring som krävs** på fliken **Lagringskonfiguration**

![Inaktivera säker överföring som krävs i Azure-portalen](./media/execute-python-scripts/disable-secure-transfer-required.png)

## <a name="operationalizing-python-scripts"></a>Operationalisera Python-skript

Alla [Execute Python Script-moduler][execute-python-script] som används i ett bedömningsexperiment anropas när de publiceras som en webbtjänst. Bilden nedan visar till exempel ett bedömningsexperiment som innehåller koden för att utvärdera ett enda Python-uttryck.

![Studioarbetsyta för en webbtjänst](./media/execute-python-scripts/figure3a.png)

![Python Pandas uttryck](./media/execute-python-scripts/python-script-with-python-pandas.png)

En webbtjänst som skapats från det här experimentet skulle vidta följande åtgärder:

1. Ta ett Python-uttryck som indata (som en sträng)
1. Skicka Python-uttrycket till Python-tolken
1. Returnerar en tabell som innehåller både uttrycket och det utvärderade resultatet.

## <a name="working-with-visualizations"></a><a id="visualizations"></a>Arbeta med visualiseringar

Ritplaner som skapats med MatplotLib kan returneras av [Execute Python Script][execute-python-script]. Ritplaner omdirigeras dock inte automatiskt till bilder som de är när de använder R. Så användaren måste uttryckligen spara alla ritplaner till PNG-filer.

Om du vill generera bilder från MatplotLib måste du vidta följande åtgärder:

1. Växla backend till "AGG" från standard Qt-baserade renderare.
1. Skapa ett nytt figurobjekt.
1. Få axeln och generera alla tomter i den.
1. Spara figuren i en PNG-fil.

Denna process illustreras i följande bilder som skapar en scatter plot matris med hjälp av scatter_matrix funktion i Pandor.

![Kod för att spara MatplotLib-figurer på bilder](./media/execute-python-scripts/figure-v1-8.png)

![Klicka på visualisera på en Execute Python Script-modul för att visa siffrorna](./media/execute-python-scripts/figure-v2-9a.png)

![Visualisera ritplaner för ett exempelexperiment med Python-kod](./media/execute-python-scripts/figure-v2-9b.png)

Det är möjligt att returnera flera siffror genom att spara dem i olika bilder. Studio (klassisk) runtime plockar upp alla bilder och sammanfogar dem för visualisering.

## <a name="advanced-examples"></a>Avancerade exempel

Den Anaconda-miljön som installeras i Studio (klassisk) innehåller vanliga paket som NumPy, SciPy och Scikits-Learn. Dessa paket kan effektivt användas för databearbetning i en machine learning pipeline.

Följande experiment och skript illustrerar till exempel användningen av ensembleinlärare i Scikits-Learn för att beräkna funktionsviktpoäng för en datauppsättning. Poängen kan användas för att utföra övervakad funktionsval innan de matas in i en annan modell.

Här är Python-funktionen som används för att beräkna prioritetspoängen och beställa funktionerna baserat på poängen:

![Funktion för att rangordna funktioner efter poäng](./media/execute-python-scripts/figure8.png)

Följande experiment beräknar och returnerar sedan betydelsen av funktioner i datauppsättningen "Pima Indian Diabetes" i Azure Machine Learning Studio (klassisk):

![Experimentera för att rangordna funktioner i Pima Indian Diabetes datauppsättning med Python](./media/execute-python-scripts/figure9a.png)

![Visualisering av utdata från modulen Kör Python-skript](./media/execute-python-scripts/figure9b.png)

## <a name="limitations"></a>Begränsningar

Modulen [Kör Python-skript][execute-python-script] har för närvarande följande begränsningar:

### <a name="sandboxed-execution"></a>Körning i begränsat läge

Python-körningen är för närvarande sandboxad och tillåter inte åtkomst till nätverket eller det lokala filsystemet på ett beständigt sätt. Alla filer som sparas lokalt isoleras och tas bort när modulen är klar. Python-koden kan inte komma åt de flesta kataloger på datorn som körs på, undantaget är den aktuella katalogen och dess underkataloger.

### <a name="lack-of-sophisticated-development-and-debugging-support"></a>Brist på sofistikerat utvecklings- och felsökningsstöd

Python-modulen stöder för närvarande inte IDE-funktioner som intellisense och felsökning. Om modulen misslyckas vid körning är den fullständiga Python-stackspårningen tillgänglig. Men det måste visas i utdataloggen för modulen. Vi rekommenderar för närvarande att du utvecklar och felsöker Python-skript i en miljö som IPython och sedan importerar koden till modulen.

### <a name="single-data-frame-output"></a>Utdata från en enda dataram

Startpunkten för Python tillåts endast att returnera en enda dataram som utdata. Det är för närvarande inte möjligt att returnera godtyckliga Python-objekt som tränade modeller direkt tillbaka till Studio (klassisk) runtime. Precis som [Kör R Script][execute-r-script], som har samma begränsning, är det möjligt att i många fall inlagma objekt i en byte array och sedan returnera den inuti en dataram.

### <a name="inability-to-customize-python-installation"></a>Oförmåga att anpassa Python-installation

För närvarande är det enda sättet att lägga till anpassade Python-moduler via zip-filmekanismen som beskrivs tidigare. Även om detta är möjligt för små moduler, det är besvärligt för stora moduler (särskilt moduler med inbyggda DLL) eller ett stort antal moduler.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Python Developer Center](https://azure.microsoft.com/develop/python/).

<!-- Module References -->
[execute-python-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-python-script
[execute-r-script]: https://docs.microsoft.com/azure/machine-learning/studio-module-reference/execute-r-script
