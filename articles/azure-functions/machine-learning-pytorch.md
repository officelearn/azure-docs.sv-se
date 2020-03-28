---
title: Distribuera en PyTorch-modell som ett Azure Functions-program
description: Använd ett förtränat ResNet 18 djupt neuralt nätverk från PyTorch med Azure Functions för att tilldela 1 av 1000 ImageNet-etiketter till en avbildning.
author: gvashishtha
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: gopalv
ms.openlocfilehash: 17acb7e351d5f1c009a6a8a14717e987fae3e895
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78379901"
---
# <a name="tutorial-deploy-a-pre-trained-image-classification-model-to-azure-functions-with-pytorch"></a>Självstudiekurs: Distribuera en förtränad avbildningsklassificeringsmodell till Azure Functions med PyTorch

I den här artikeln får du lära dig hur du använder Python, PyTorch och Azure Functions för att läsa in en förtränad modell för att klassificera en avbildning baserat på dess innehåll. Eftersom du gör allt arbete lokalt och inte skapar några Azure-resurser i molnet, kostar det inget att slutföra den här självstudien.

> [!div class="checklist"]
> * Initiera en lokal miljö för att utveckla Azure-funktioner i Python.
> * Importera en förtränad PyTorch maskininlärningsmodell till en funktionsapp.
> * Skapa ett serverlöst HTTP-API för att klassificera en avbildning som en av 1000 [ImageNet-klasser](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a).
> * Använda API:et från en webbapp.

## <a name="prerequisites"></a>Krav

- Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
- [Python 3.7.4 eller högre](https://www.python.org/downloads/release/python-374/). (Python 3.8.x och Python 3.6.x verifieras också med Azure-funktioner.)
- [Kärnverktygen för Azure-funktioner](functions-run-local.md#install-the-azure-functions-core-tools)
- En kodredigerare som t.ex. [Visual Studio Code](https://code.visualstudio.com/)

### <a name="prerequisite-check"></a>Förutsättningskontroll

1. I ett terminal- eller `func --version` kommandofönster kör du för att kontrollera att Azure Functions Core Tools är version 2.7.1846 eller senare.
1. Kör `python --version` (Linux/MacOS) `py --version` eller (Windows) för att kontrollera dina Python-versionsrapporter 3.7.x.

## <a name="clone-the-tutorial-repository"></a>Klona självstudiedatabasen

1. I ett terminal- eller kommandofönster klonar du följande databas med Git:

    ```
    git clone https://github.com/Azure-Samples/functions-python-pytorch-tutorial.git
    ```

1. Navigera till mappen och undersök dess innehåll.

    ```
    cd functions-python-pytorch-tutorial
    ```

    - *start* är din arbetsmapp för handledningen.
    - *slutet* är det slutliga resultatet och full implementering för din referens.
    - *innehåller* maskininlärningsmodellen och hjälpbiblioteken.
    - *frontend* är en webbplats som anropar funktionsappen.

## <a name="create-and-activate-a-python-virtual-environment"></a>Skapa och aktivera en virtuell Python-miljö

Navigera till *startmappen* och kör följande kommandon för att `.venv`skapa och aktivera en virtuell miljö med namnet .


# <a name="bash"></a>[Bash](#tab/bash)

```bash
cd start
python -m venv .venv
source .venv/bin/activate
```

Om Python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

```powershell
cd start
py -m venv .venv
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd start
py -m venv .venv
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


## <a name="import-the-pytorch-model-and-add-helper-code"></a>Importera PyTorch-modellen och lägg till hjälpkod

Om du `classify` vill ändra funktionen för att klassificera en bild baserat på dess innehåll använder du en förtränad [ResNet-modell.](https://arxiv.org/abs/1512.03385) Den förtränade modellen, som kommer från [PyTorch](https://pytorch.org/hub/pytorch_vision_resnet/), klassificerar en bild i 1 av 1000 [ImageNet-klasser](https://gist.github.com/yrevar/942d3a0ac09ec9e5eb3a). Du lägger sedan till hjälpkod och beroenden i projektet.

1. I *startmappen* kör du följande kommando för att kopiera förutsägelsekoden och etiketterna till *klassificera mappen.*

    # <a name="bash"></a>[Bash](#tab/bash)

    ```bash
    cp ../resources/predict.py classify
    cp ../resources/labels.txt classify
    ```

    # <a name="powershell"></a>[Powershell](#tab/powershell)

    ```powershell
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    # <a name="cmd"></a>[Cmd](#tab/cmd)

    ```cmd
    copy ..\resources\predict.py classify
    copy ..\resources\labels.txt classify
    ```

    ---

1. Kontrollera att *klassificera mappen* innehåller filer med namnet *predict.py* och *labels.txt*. Om inte, kontrollera att du *start* körde kommandot i startmappen.

1. Öppna *start/requirements.txt* i en textredigerare och lägg till de beroenden som krävs av hjälpkoden, som ska se ut så här:

    ```txt
    azure-functions
    requests
    numpy==1.15.4
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp36-cp36m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.6'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-win_amd64.whl; sys_platform == 'win32' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp37-cp37m-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.7'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-win_amd64.whl; sys_platform == 'win32' and python_version == '3.8'
    https://download.pytorch.org/whl/cpu/torch-1.4.0%2Bcpu-cp38-cp38-linux_x86_64.whl; sys_platform == 'linux' and python_version == '3.8'
    torchvision==0.5.0
    ```

1. Spara *requirements.txt*och kör sedan följande kommando från *startmappen* för att installera beroenden.


    ```
    pip install --no-cache-dir -r requirements.txt
    ```

Installationen kan ta några minuter, under vilken tid du kan fortsätta med att ändra funktionen i nästa avsnitt.
> [!TIP]
> >I Windows kan felet "Det gick inte att installera paket på grund av en EnvironmentError: [Errno 2] Ingen sådan fil eller katalog:" följt av ett långt sökvägsnamn till en fil som *sharded_mutable_dense_hashtable.cpython-37.pyc*. Det här felet inträffar vanligtvis eftersom djupen på mappsökvägen blir för lång. I det här fallet anger `HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\FileSystem@LongPathsEnabled` `1` du registernyckeln så att långa sökvägar ska aktiveras. Alternativt kan du kontrollera var din Python-tolk är installerad. Om den platsen har en lång sökväg kan du prova att installera om i en mapp med en kortare sökväg.

## <a name="update-the-function-to-run-predictions"></a>Uppdatera funktionen för att köra förutsägelser

1. Öppna *klassificera/\_\_init\_\_.py* i en textredigerare `import` och lägg till följande rader efter de befintliga uttalandena för att importera standard-JSON-biblioteket och *förutsäga* hjälparna:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="1-6" highlight="5-6":::

1. Ersätt hela innehållet `main` i funktionen med följande kod:

    :::code language="python" source="~/functions-pytorch/end/classify/__init__.py" range="8-19":::

    Den här funktionen tar emot en `img`bild-URL i en frågesträngparameter med namnet . Det ringer `predict_image_from_url` sedan från hjälpbiblioteket för att ladda ner och klassificera bilden med PyTorch-modellen. Funktionen returnerar sedan ett HTTP-svar med resultatet.

    > [!IMPORTANT]
    > Eftersom den här HTTP-slutpunkten anropas av en webbsida som `Access-Control-Allow-Origin` finns på en annan domän innehåller svaret ett sidhuvud för att uppfylla webbläsarens CORS-krav (Cross-Origin Resource Sharing).
    >
    > I ett produktionsprogram `*` ändrar du till webbsidans specifika ursprung för ökad säkerhet.

1. Spara ändringarna och anta sedan att beroenden har installerats, `func start`starta den lokala funktionsvärden igen med . Var noga med att köra värden i *startmappen* med den virtuella miljön aktiverad. Annars startar värden, men du ser fel när du anropar funktionen.

    ```
    func start
    ```

1. Öppna följande webbadress i en webbläsare för att anropa funktionen med webbadressen till en Bernese Mountain Dog-bild och bekräfta att den returnerade JSON klassificerar bilden som en Bernese Mountain Dog.

    ```
    http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg
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

    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/Bernese-Mountain-Dog-Temperament-long.jpg`
    - `https://github.com/Azure-Samples/functions-python-pytorch-tutorial/blob/master/resources/assets/bald-eagle.jpg?raw=true`
    - `https://raw.githubusercontent.com/Azure-Samples/functions-python-pytorch-tutorial/master/resources/assets/penguin.jpg`

1. Välj **Skicka** om du vill anropa funktionsslutpunkten för att klassificera bilden.

    ![Skärmbild av avslutat projekt](media/machine-learning-pytorch/screenshot.png)

    Om webbläsaren rapporterar ett fel när du skickar bild-URL:en kontrollerar du terminalen där du kör funktionsappen. Om du ser ett felmeddelande som "Ingen modul hittades"PIL'" kan du ha startat funktionsappen i *startmappen* utan att först aktivera den virtuella miljön som du skapade tidigare. Om du fortfarande ser `pip install -r requirements.txt` fel kan du köra igen med den virtuella miljön aktiverad och leta efter fel.

## <a name="clean-up-resources"></a>Rensa resurser

Eftersom hela den här självstudien körs lokalt på din dator finns det inga Azure-resurser eller tjänster att rensa.

## <a name="next-steps"></a>Nästa steg

I den här självstudien lärde du dig hur du skapar och anpassar en HTTP API-slutpunkt med Azure Functions för att klassificera avbildningar med hjälp av en PyTorch-modell. Du har också lärt dig hur du anropar API:et från en webbapp. Du kan använda teknikerna i den här självstudien för att bygga ut API:er oavsett komplexitet, samtidigt som du kör på den serverlösa beräkningsmodellen som tillhandahålls av Azure Functions.

Se även:

- [Distribuera funktionen till Azure med Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions).
- [Utvecklarhandboken för Azure Functions Python](./functions-reference-python.md)


> [!div class="nextstepaction"]
> [Distribuera funktionen till Azure-funktioner med Hjälp av Azure CLI Guide](./functions-run-local.md#publish)
