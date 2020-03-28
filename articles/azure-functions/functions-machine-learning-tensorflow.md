---
title: Använda Python och TensorFlow för maskininlärning i Azure
description: Använd Python, TensorFlow och Azure Functions med en maskininlärningsmodell för att klassificera en avbildning baserat på dess innehåll.
author: anthonychu
ms.topic: tutorial
ms.date: 01/15/2020
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: c64d87b2430cc1d733a67bbc1e803590a37b1714
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78190780"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Självstudiekurs: Tillämpa maskininlärningsmodeller i Azure-funktioner med Python och TensorFlow

I den här artikeln får du lära dig hur du använder Python, TensorFlow och Azure Functions med en maskininlärningsmodell för att klassificera en avbildning baserat på dess innehåll. Eftersom du gör allt arbete lokalt och inte skapar några Azure-resurser i molnet, kostar det inget att slutföra den här självstudien.

> [!div class="checklist"]
> * Initiera en lokal miljö för att utveckla Azure-funktioner i Python.
> * Importera en anpassad TensorFlow-maskininlärningsmodell till en funktionsapp.
> * Skapa ett serverlöst HTTP-API för klassificering av en avbildning som innehåller en hund eller en katt.
> * Använda API:et från en webbapp.

## <a name="prerequisites"></a>Krav 

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4](https://www.python.org/downloads/release/python-374/). (Python 3.7.4 och Python 3.6.x verifieras med Azure Functions; Python 3.8 och senare versioner stöds ännu inte.)
- [Kärnverktygen för Azure-funktioner](functions-run-local.md#install-the-azure-functions-core-tools)
- En kodredigerare som t.ex. [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Förutsättningskontroll

1. I ett terminal- eller `func --version` kommandofönster kör du för att kontrollera att Azure Functions Core Tools är version 2.7.1846 eller senare.
1. Kör `python --version` (Linux/MacOS) `py --version` eller (Windows) för att kontrollera dina Python-versionsrapporter 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Klona självstudiedatabasen

1. I ett terminal- eller kommandofönster klonar du följande databas med Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
    ```

1. Navigera till mappen och undersök dess innehåll.

    ```
    cd functions-python-tensorflow-tutorial
    ```

    - *start* är din arbetsmapp för handledningen.
    - *slutet* är det slutliga resultatet och full implementering för din referens.
    - *innehåller* maskininlärningsmodellen och hjälpbiblioteken.
    - *frontend* är en webbplats som anropar funktionsappen.
    
## <a name="create-and-activate-a-python-virtual-environment"></a>Skapa och aktivera en virtuell Python-miljö

Navigera till *startmappen* och kör följande kommandon för att `.venv`skapa och aktivera en virtuell miljö med namnet . Var noga med att använda Python 3.7, som stöds av Azure Functions.


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
```

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Om Python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
cd start
```

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
```

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Du kör alla efterföljande kommandon i den här aktiverade virtuella miljön. (Kör `deactivate`.) Om du vill avsluta den virtuella miljön.


## <a name="create-a-local-functions-project"></a>Skapa ett projekt för lokala funktioner

I Azure Functions är ett funktionsprojekt en behållare för en eller flera enskilda funktioner som var och en svarar på en viss utlösare. Alla funktioner i ett projekt har samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som `classify` innehåller en enda standardfunktion med namnet som tillhandahåller en HTTP-slutpunkt. Du lägger till mer specifik kod i ett senare avsnitt.

1. I *startmappen* använder du Azure Functions Core Tools för att initiera en Python-funktionsapp:

    ```
    func init --worker-runtime python
    ```

    Efter initieringen innehåller *startmappen* olika filer för projektet, inklusive konfigurationsfiler med namnet [local.settings.json](functions-run-local.md#local-settings-file) och [host.json](functions-host-json.md). Eftersom *local.settings.json* kan innehålla hemligheter som hämtats från Azure, är filen undantagen från källkontrollen som standard i *.gitignore-filen.*

    > [!TIP]
    > Eftersom ett funktionsprojekt är kopplat till en viss körning måste alla funktioner i projektet skrivas med samma språk.

1. Lägg till en funktion i projektet med `--name` hjälp av följande kommando, där `--template` argumentet är det unika namnet på din funktion och argumentet anger funktionens utlösare. `func new`skapa en undermapp som matchar funktionsnamnet som innehåller en kodfil som är lämplig för projektets valda språk och en konfigurationsfil med namnet *function.json*.

    ```
    func new --name classify --template "HTTP trigger"
    ```

    Det här kommandot skapar en mapp som matchar namnet på funktionen, *klassificera*. I den mappen finns två filer: * \_ \_init\_\_.py*, som innehåller funktionskoden och *function.json*, som beskriver funktionens utlösare och dess in- och utdatabindningar. Mer information om innehållet i dessa filer finns i [Granska filinnehållet](/azure/azure-functions/functions-create-first-azure-function-azure-cli?pivots=programming-language-python#optional-examine-the-file-contents) i snabbstarten python.


## <a name="run-the-function-locally"></a>Kör funktionen lokalt

1. Starta funktionen genom att starta den lokala *start* Azure Functions-körtidsvärden i startmappen:

    ```
    func start
    ```
    
1. När du `classify` ser slutpunkten visas i utdata ```http://localhost:7071/api/classify?name=Azure```navigerar du till WEBBADRESSEN. Meddelandet "Hello Azure!" ska visas i utdata.

1. Använd **Ctrl**-**C** för att stoppa värden.


## <a name="import-the-tensorflow-model-and-add-helper-code"></a>Importera TensorFlow-modellen och lägg till hjälpkod

Om du `classify` vill ändra funktionen för att klassificera en avbildning baserat på dess innehåll använder du en förbyggd TensorFlow-modell som har tränats med och exporterats från Azure Custom Vision Service. Modellen, som finns i *resursmappen* i exemplet som du klonade tidigare, klassificerar en bild baserat på om den innehåller en hund eller en katt. Du lägger sedan till hjälpkod och beroenden i projektet.

> [!TIP]
> Om du vill skapa en egen modell med den kostnadsfria nivån för Custom Vision-tjänsten följer du instruktionerna i [exempelprojektdatabasen](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

1. I *startmappen* kör du följande kommando för att kopiera modellfilerna till *klassificera* mappen. Var noga `\*` med att inkludera i kommandot. 

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/model/* classify
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    copy ..\resources\model\* classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\model\* classify
    ```
    
    ---
    
1. Kontrollera att *klassificera mappen* innehåller filer med namnet *model.pb* och *labels.txt*. Om inte, kontrollera att du *start* körde kommandot i startmappen.

1. I *startmappen* kör du följande kommando för att kopiera en fil med hjälpkod till *mappen klassificera:*

    # <a name="bash"></a>[Bash](#tab/bash)
    
    ```bash
    cp ../resources/predict.py classify
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)
    
    ```powershell
    copy ..\resources\predict.py classify
    ```
    
    # <a name="cmd"></a>[Cmd](#tab/cmd)
    
    ```cmd
    copy ..\resources\predict.py classify
    ```
    
    ---

1. Kontrollera att *klassificera mappen* nu innehåller en fil med namnet *predict.py*.

1. Öppna *start/requirements.txt* i en textredigerare och lägg till följande beroenden som krävs av hjälpkoden:

    ```txt
    tensorflow==1.14
    Pillow
    requests
    ```
    
1. Spara *requirements.txt*.

1. Installera beroenden genom att köra *start* följande kommando i startmappen. Installationen kan ta några minuter, under vilken tid du kan fortsätta med att ändra funktionen i nästa avsnitt.

    ```
    pip install --no-cache-dir -r requirements.txt
    ```
    
    I Windows kan felet "Det gick inte att installera paket på grund av en EnvironmentError: [Errno 2] Ingen sådan fil eller katalog:" följt av ett långt sökvägsnamn till en fil som *sharded_mutable_dense_hashtable.cpython-37.pyc*. Det här felet inträffar vanligtvis eftersom djupen på mappsökvägen blir för lång. I det här fallet anger `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` `1` du registernyckeln så att långa sökvägar ska aktiveras. Alternativt kan du kontrollera var din Python-tolk är installerad. Om den platsen har en lång sökväg kan du prova att installera om i en mapp med en kortare sökväg.

> [!TIP]
> När du uppmanar *predict.py* att göra sin första `_initialize` förutsägelse läser en funktion med namnet TensorFlow-modellen från disken och cachelagrar den i globala variabler. Den här cachelagringen snabbar upp efterföljande förutsägelser. Mer information om hur du använder globala variabler finns i [utvecklarhandboken](functions-reference-python.md#global-variables)för Azure Functions Python .

## <a name="update-the-function-to-run-predictions"></a>Uppdatera funktionen för att köra förutsägelser

1. Öppna *klassificera/\_\_init\_\_.py* i en textredigerare `import` och lägg till följande rader efter de befintliga uttalandena för att importera standard-JSON-biblioteket och *förutsäga* hjälparna:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Ersätt hela innehållet `main` i funktionen med följande kod:

    :::code language="python" source="~/functions-python-tensorflow-tutorial/end/classify/__init__.py" range="8-19":::

    Den här funktionen tar emot en `img`bild-URL i en frågesträngparameter med namnet . Det ringer `predict_image_from_url` sedan från hjälpbiblioteket för att ladda ner och klassificera bilden med TensorFlow-modellen. Funktionen returnerar sedan ett HTTP-svar med resultatet. 

    > [!IMPORTANT]
    > Eftersom den här HTTP-slutpunkten anropas av en webbsida som `Access-Control-Allow-Origin` finns på en annan domän innehåller svaret ett sidhuvud för att uppfylla webbläsarens CORS-krav (Cross-Origin Resource Sharing).
    >
    > I ett produktionsprogram `*` ändrar du till webbsidans specifika ursprung för ökad säkerhet.

1. Spara ändringarna och anta sedan att beroenden har installerats, `func start`starta den lokala funktionsvärden igen med . Var noga med att köra värden i *startmappen* med den virtuella miljön aktiverad. Annars startar värden, men du ser fel när du anropar funktionen.

    ```
    func start
    ```

1. Öppna följande WEBBADRESS i en webbläsare för att anropa funktionen med webbadressen till en kattavbildning och bekräfta att den returnerade JSON klassificerar bilden som en katt.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
    ```
    
1. Håll värden igång eftersom du använder den i nästa steg. 

### <a name="run-the-local-web-app-front-end-to-test-the-function"></a>Kör den lokala webbappens klientdel för att testa funktionen

Om du vill testa att anropa funktionsslutpunkten från en annan webbapp finns det en enkel app i databasens klientdelsmapp. *frontend*

1. Öppna en ny terminal eller kommandotolk och aktivera den virtuella miljön (som beskrivs tidigare under [Skapa och aktivera en virtuell Python-miljö](#create-and-activate-a-python-virtual-environment)).

1. Navigera till databasens *klientdelsmapp.*

1. Starta en HTTP-server med Python:

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash 
    python -m http.server
    ```
    
    # <a name="powershell"></a>[Powershell](#tab/powershell)

    ```powershell
    py -m http.server
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    py -m http.server
    ```

1. I en webbläsare `localhost:8000`navigerar du till och anger sedan en av följande fotoadresser i textrutan eller använder url:en för en offentligt tillgänglig bild.

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`
    
1. Välj **Skicka** om du vill anropa funktionsslutpunkten för att klassificera bilden.

    ![Skärmbild av avslutat projekt](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

    Om webbläsaren rapporterar ett fel när du skickar bild-URL:en kontrollerar du terminalen där du kör funktionsappen. Om du ser ett felmeddelande som "Ingen modul hittades"PIL'" kan du ha startat funktionsappen i *startmappen* utan att först aktivera den virtuella miljön som du skapade tidigare. Om du fortfarande ser `pip install -r requirements.txt` fel kan du köra igen med den virtuella miljön aktiverad och leta efter fel.

> [!NOTE]
> Modellen klassificerar alltid innehållet i bilden som en katt eller en hund, oavsett om bilden innehåller antingen, som standard för hund. Bilder av tigrar och pantrar, till exempel, vanligtvis klassificera som katt, men bilder av elefanter, morötter eller flygplan klassificera som hund.

## <a name="clean-up-resources"></a>Rensa resurser

Eftersom hela den här självstudien körs lokalt på din dator finns det inga Azure-resurser eller tjänster att rensa.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar och anpassar en HTTP API-slutpunkt med Azure Functions för att klassificera avbildningar med hjälp av en TensorFlow-modell. Du har också lärt dig hur du anropar API:et från en webbapp. Du kan använda teknikerna i den här självstudien för att bygga ut API:er oavsett komplexitet, samtidigt som du kör på den serverlösa beräkningsmodellen som tillhandahålls av Azure Functions.

> [!div class="nextstepaction"]
> [Distribuera funktionen till Azure-funktioner med Hjälp av Azure CLI Guide](./functions-run-local.md#publish)

Se även:

- [Distribuera funktionen till Azure med Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Utvecklarhandboken för Azure Functions Python](./functions-reference-python.md)
