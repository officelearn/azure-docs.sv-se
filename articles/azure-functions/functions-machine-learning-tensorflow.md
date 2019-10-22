---
title: 'Självstudie: Använd python och TensorFlow i Azure Functions för att göra Machine Learning-inferences | Microsoft Docs'
description: Den här självstudien visar hur du använder TensorFlow Machine Learning-modeller i Azure Functions
services: functions
author: anthonychu
manager: gwallace
ms.service: azure-functions
ms.devlang: python
ms.topic: tutorial
ms.date: 07/29/2019
ms.author: antchu
ms.custom: mvc
ms.openlocfilehash: e243fd2f5c4a90e45f424ce39a97913df2332b2b
ms.sourcegitcommit: 1bd2207c69a0c45076848a094292735faa012d22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "72677887"
---
# <a name="tutorial-apply-machine-learning-models-in-azure-functions-with-python-and-tensorflow"></a>Självstudie: använda Machine Learning-modeller i Azure Functions med python och TensorFlow

Den här artikeln visar hur Azure Functions gör att du kan använda python och TensorFlow med en maskin inlärnings modell för att klassificera en avbildning baserat på dess innehåll.

I de här självstudierna får du lära dig att: 

> [!div class="checklist"]
> * Initiera en lokal miljö för att utveckla Azure Functions i python
> * Importera en anpassad TensorFlow Machine Learning-modell till en Function-app
> * Bygg en server lös HTTP API för förutsägelse av om ett foto innehåller en hund eller en katt
> * Använda API: et från ett webb program

![Skärm bild av färdig projekt](media/functions-machine-learning-tensorflow/functions-machine-learning-tensorflow-screenshot.png)

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav 

Om du vill skapa Azure Functions i python måste du installera några verktyg.

- [Python 3,6](https://www.python.org/downloads/release/python-360/)
- [Azure Functions Core Tools](functions-run-local.md#install-the-azure-functions-core-tools)
- En kodredigerare såsom [Visual Studio Code](https://code.visualstudio.com/)

## <a name="clone-the-tutorial-repository"></a>Klona självstudiernas databas

Starta genom att öppna en Terminal och klona följande lagrings plats med git:

```console
git clone https://github.com/Azure-Samples/functions-python-tensorflow-tutorial.git
cd functions-python-tensorflow-tutorial
```

Databasen innehåller några få mappar.

- *Starta*: det här är din arbetsmapp för självstudien
- *slut*: det här är det slutliga resultatet och fullständig implementering för din referens
- *resurser*: innehåller Machine Learning-modellen och hjälp program bibliotek
- *klient*del: en webbplats som anropar Function-appen

## <a name="create-and-activate-a-python-virtual-environment"></a>Skapa och aktivera en virtuell python-miljö

Azure Functions kräver python 3.6. x. Du skapar en virtuell miljö för att se till att du använder den nödvändiga python-versionen.

Ändra den aktuella arbets katalogen till mappen *Start* . Skapa och aktivera sedan en virtuell miljö med namnet *. venv*. Beroende på din python-installation kan kommandona för att skapa en virtuell python 3,6-miljö skilja sig från följande instruktioner.

#### <a name="linux-and-macos"></a>Linux och macOS:

```bash
cd start
python3.6 -m venv .venv
source .venv/bin/activate
```

#### <a name="windows"></a>Windows:

```powershell
cd start
py -3.6 -m venv .venv
.venv\scripts\activate
```

Terminal-prompten föregås nu av `(.venv)` som anger att du har aktiverat den virtuella miljön. Bekräfta att `python` i den virtuella miljön verkligen är python 3.6. x.

```console
python --version
```

> [!NOTE]
> I resten av självstudien kör du kommandon i den virtuella miljön. Om du behöver återaktivera den virtuella miljön i en Terminal kör du lämpligt aktiverings kommando för ditt operativ system.

## <a name="create-an-azure-functions-project"></a>Skapa ett Azure Functions-projekt

I mappen *Start* använder du Azure Functions Core Tools för att initiera en python Function-app.

```console
func init --worker-runtime python
```

En Function-app kan innehålla en eller flera Azure Functions. Öppna mappen *Start* i en redigerare och granska innehållet.

- [*Local. Settings. JSON*](functions-run-local.md#local-settings-file): innehåller program inställningar som används för lokal utveckling
- [*Host. JSON*](functions-host-json.md): innehåller inställningar för Azure Functions värd och tillägg
- [*Requirements. txt*](functions-reference-python.md#python-version-and-package-management): innehåller python-paket som krävs av det här programmet

## <a name="create-an-http-function"></a>Skapa en HTTP-funktion

Programmet kräver en enda HTTP API-slutpunkt som tar en bild-URL som indata och returnerar en förutsägelse av om avbildningen innehåller en hund eller en katt.

Använd Azure Functions Core Tools för att Autogenerera en ny HTTP-funktion med namnet *klassificera*i terminalen.

```console
func new --language python --template HttpTrigger --name classify
```

En ny mapp med namnet *klassificera* skapas, som innehåller två filer.

- *\_ \_init \_ \_. py*: en fil för huvud funktionen
- *Function. JSON*: en fil som beskriver funktionens utlösare och dess indata och utdata-bindningar

### <a name="run-the-function"></a>Kör funktionen

Starta Function-appen i terminalen med den virtuella python-miljön aktive rad.

```console
func start
```

Öppna en webbläsare och gå till följande URL. Funktionen ska köra och returnera *Hej Azure!*

```
http://localhost:7071/api/classify?name=Azure
```

Använd `Ctrl-C` för att stoppa Function-appen.

## <a name="import-the-tensorflow-model"></a>Importera TensorFlow-modellen

Du använder en fördefinierad TensorFlow-modell som har tränats med och exporter ATS från Azure Custom Vision Service.

> [!NOTE]
> Om du vill bygga dina egna med Custom Vision Service kostnads fri nivå kan du följa [anvisningarna i exempel projektets lagrings plats](https://github.com/Azure-Samples/functions-python-tensorflow-tutorial/blob/master/train-custom-vision-model.md).

Modellen består av två filer i mappen *< REPOSITORY_ROOT >/Resources/Model* : *Model. PB* och *Labels. txt*. Kopiera dem till mappen *klassificera* funktion '.

#### <a name="linux-and-macos"></a>Linux och macOS:

```bash
cp ../resources/model/* classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\model\* classify
```

Se till att ta med \* i kommandot ovan. Bekräfta att *klassificerar* nu innehåller filer med namnet *Model. PB* och *Labels. txt*.

## <a name="add-the-helper-functions-and-dependencies"></a>Lägga till hjälp funktioner och beroenden

Vissa hjälp funktioner för att förbereda indatabilden och göra en förutsägelse med TensorFlow finns i en fil med namnet *predict.py* i mappen *resurser* . Kopiera den här filen till mappen *klassificera* funktion.

#### <a name="linux-and-macos"></a>Linux och macOS:

```bash
cp ../resources/predict.py classify
```

#### <a name="windows"></a>Windows:

```powershell
copy ..\resources\predict.py classify
```

Bekräfta att *klassificerar* nu innehåller en fil med namnet *predict.py*.

### <a name="install-dependencies"></a>Installera beroenden

Hjälp biblioteket har vissa beroenden som måste installeras. Öppna *Start/Requirements. txt* i redigeraren och Lägg till följande beroenden i filen.

```txt
tensorflow==1.15
Pillow
requests
```

Spara filen.

I terminalen med den virtuella miljön aktive rad kör du följande kommando i *startmappen för* att installera beroendena. Det kan ta några minuter att slutföra vissa installations steg.

```console
pip install --no-cache-dir -r requirements.txt
```

### <a name="caching-the-model-in-global-variables"></a>Cachelagra modellen i globala variabler

Öppna *predict.py* i redigeraren och titta på den `_initialize`-funktionen nästan överst i filen. Observera att TensorFlow-modellen läses in från disk första gången funktionen körs och sparas i globala variabler. Inläsningen från disken hoppas över vid efterföljande körningar av funktionen `_initialize`. Cachelagring av modellen i minnet med den här tekniken påskyndar senare förutsägelser.

Mer information om globala variabler finns i [Azure Functions python Developer Guide](functions-reference-python.md#global-variables).

## <a name="update-function-to-run-predictions"></a>Uppdaterings funktion för att köra förutsägelser

Öppna *klassificera/\_ \_init \_ \_. py* i redigeraren. Importera det *förutsägelse* bibliotek som du har lagt till i samma mapp tidigare. Lägg till följande `import`-instruktioner under de andra importerna som redan finns i filen.

```python
import json
from .predict import predict_image_from_url
```

Ersätt funktions mal len kod med följande.

```python
def main(req: func.HttpRequest) -> func.HttpResponse:
    image_url = req.params.get('img')
    results = predict_image_from_url(image_url)

    headers = {
        "Content-type": "application/json",
        "Access-Control-Allow-Origin": "*"
    }
    return func.HttpResponse(json.dumps(results), headers = headers)
```

Se till att spara ändringarna.

Den här funktionen tar emot en bild-URL i en frågesträngparametern med namnet `img`. Den anropar `predict_image_from_url` från hjälp biblioteket som laddar ned avbildningen och returnerar en förutsägelse med TensorFlow-modellen. Funktionen returnerar sedan ett HTTP-svar med resultatet.

Eftersom HTTP-slutpunkten anropas av en webb sida som finns på en annan domän, innehåller HTTP-svaret ett `Access-Control-Allow-Origin`-huvud för att uppfylla webbläsarens krav på resurs delning mellan ursprung (CORS).

> [!NOTE]
> I ett produktions program ändrar du `*` till webb sidans specifika ursprung för ytterligare säkerhet.

### <a name="run-the-function-app"></a>Kör Function-appen

Se till att den virtuella python-miljön fortfarande är aktive rad och starta appen funktion med hjälp av följande kommando.

```console
func start
```

Öppna den här URL: en i en webbläsare som anropar din funktion med URL: en till ett katt-foto. Bekräfta att ett giltigt förutsägelse resultat returneras.

```
http://localhost:7071/api/classify?img=https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png
```

Se till att Function-appen körs.

### <a name="run-the-web-app"></a>Kör webbappen

Det finns en enkel webbapp i mappen *frontend* som använder http API i Function-appen.

Öppna en *separat* Terminal och ändra till mappen *frontend* . Starta en HTTP-server med python 3,6.

#### <a name="linux-and-macos"></a>Linux och macOS:

```bash
cd <FRONT_END_FOLDER>
python3.6 -m http.server
```

#### <a name="windows"></a>Windows:

```powershell
cd <FRONT_END_FOLDER>
py -3.6  -m http.server
```

I en webbläsare navigerar du till HTTP-serverns URL som visas i terminalen. En webbapp bör visas. Ange en av följande Foto-URL: er i text rutan. Du kan också använda en URL för ett offentligt tillgängligt Cat-eller hund-foto.

- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/cat2.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog1.png`
- `https://raw.githubusercontent.com/Azure-Samples/functions-python-tensorflow-tutorial/master/resources/assets/samples/dog2.png`

När du klickar på Skicka, anropas Function-appen och resultatet visas på sidan.

## <a name="clean-up-resources"></a>Rensa resurser
Hela den här självstudien körs lokalt på din dator, så det finns inga Azure-resurser eller-tjänster att rensa.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar och anpassar ett HTTP-API med Azure Functions för att göra förutsägelser med en TensorFlow modell. Du har också lärt dig hur du anropar API: et från ett webb program.

Du kan använda metoderna i den här självstudien för att bygga ut API: er för all komplexitet, samtidigt som du kör på den serverbaserade beräknings modellen som tillhandahålls av Azure Functions.

Använd [Azure Functions Core tools](./functions-run-local.md#publish) eller [Visual Studio Code](https://code.visualstudio.com/docs/python/tutorial-azure-functions)för att distribuera Function-appen till Azure.

> [!div class="nextstepaction"]
> [Guide för Azure Functions python-utvecklare](./functions-reference-python.md)
