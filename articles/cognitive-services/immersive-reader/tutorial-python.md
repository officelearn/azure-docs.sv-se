---
title: 'Självstudier: Starta den fördjupade läsaren med python'
titleSuffix: Azure Cognitive Services
description: I den här självstudien skapar du ett python-program som startar den fördjupade läsaren.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 08/02/2019
ms.author: dylankil
ms.openlocfilehash: 5e33108c9fc674abaf980a1272cca31aa21cffff
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991066"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Självstudier: Starta den fördjupade läsaren med python-exempelprojektet

I [översikten](./overview.md)har du lärt dig om vad den fördjupade läsaren är och hur den implementerar beprövade tekniker för att förbättra läsningen av förståelse för språkstuderande, nya läsare och studenter med inlärnings skillnader. Den här självstudien beskriver hur du skapar ett python-webbprogram som startar den fördjupade läsaren. I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en python-webbapp med pip, kolv, Jinja och virtuell miljö med ett exempel projekt
> * Hämta en åtkomsttoken
> * Starta den fördjupade läsaren med exempel innehåll

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* En fördjupad läsar resurs som kon figurer ATS för autentisering med Azure Active Directory (Azure AD). Följ [dessa instruktioner](./azure-active-directory-authentication.md) för att konfigurera. Du behöver några av de värden som skapas här när du konfigurerar miljö egenskaperna. Spara utdata från sessionen i en textfil för framtida bruk.
* [Git](https://git-scm.com/)
* [SDK för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) och [pip](https://docs.python.org/3/installing/index.html). Från och med python 3,4 ingår pip som standard med de binära installations programmen för python.
* [Kol](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtuell miljö](https://virtualenv.pypa.io/en/latest/) och [virtualenvwrapper – Win för Windows](https://pypi.org/project/virtualenvwrapper-win/) eller [virtualenvwrapper för OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [modulen begär Anden](https://pypi.org/project/requests/2.7.0/)
* En IDE, till exempel [Visual Studio Code](https://code.visualstudio.com/)

## <a name="acquire-an-azure-ad-authentication-token"></a>Hämta en Azure AD-autentiseringstoken

Skriv ett Server dels-API för att hämta en Azure AD-autentiseringstoken.

Du behöver vissa värden från det nödvändiga steget för Azure AD auth Configuration ovan för den här delen. Se tillbaka till text filen som du sparade i sessionen.

````text
TenantId     => Azure subscription TenantId
ClientId     => Azure AD ApplicationId
ClientSecret => Azure AD Application Service Principal password
Subdomain    => Immersive Reader resource subdomain (resource 'Name' if the resource was created in the Azure portal, or 'CustomSubDomain' option if the resource was created with Azure CLI Powershell. Check the Azure portal for the subdomain on the Endpoint in the resource Overview page, for example, 'https://[SUBDOMAIN].cognitiveservices.azure.com/')
````

När du har dessa värden skapar du en ny fil med namnet _. kuvert_och klistrar in följande kod i den och anger dina egna egenskaps värden ovan. Ersätt _. kuvert_ . fil i exempel appen med den nya filen som skapats.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Se till att du inte utför den här filen i käll kontrollen eftersom den innehåller hemligheter som inte bör göras offentliga.

**Getimmersivereadertoken** API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth](https://oauth.net/2/)) för att hindra obehöriga användare från att hämta token som ska användas mot tjänsten för avancerad läsare och fakturering. Detta arbete ligger utanför den här självstudiens omfattning.

## <a name="create-a-python-web-app-on-windows"></a>Skapa en python-webbapp i Windows

Skapa en python-webbapp med `flask` på Windows.

Installera [Git](https://git-scm.com/).

När git har installerats öppnar du en kommando tolk och ' klona ' Avancerad Reader SDK git-lagringsplats till en mapp på din dator

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installera [Python](https://www.python.org/downloads/).

Markera rutan Lägg till python till sökväg.

![Dialog rutan python Windows-installation steg 1](./media/pythoninstallone.jpg)

Lägg till valfria funktioner genom att markera kryss rutorna och klicka sedan på Nästa.

![Dialog rutan python Windows-installation steg 2](./media/pythoninstalltwo.jpg)

Välj anpassad installation och ange installations Sök vägen som rotmapp, t. ex. `C:\Python37-32\` Klicka på knappen Installera.

![Dialog rutan python Windows-installation steg 3](./media/pythoninstallthree.jpg)

När python-installationen är klar öppnar du en kommando tolk `cd` och till mappen Python-skript.

```cmd
cd C:\Python37-32\Scripts
```

Installations flaska.

```cmd
pip install flask
```

Installera Jinja2. En mall för en komplett motor för python.

```cmd
pip install jinja2
```

Installera virtuell miljö. Ett verktyg för att skapa isolerade python-miljöer.

```cmd
pip install pip install virtualenv
```

Installera virtualenvwrapper – Win. Idén bakom virtualenvwrapper är att under lätta användningen av virtuell miljö.

```cmd
pip install virtualenvwrapper-win
```

Installera modulen begär Anden. Begär Anden är ett apache2 licensierat HTTP-bibliotek, skrivet i python.

```cmd
pip install requests
```

Skapa en virtuell miljö

```cmd
mkvirtualenv advanced-python
```

`cd`till rotmappen för exempel projekt.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Anslut exempel projektet till-miljön. Detta mappar den nyligen skapade virtuella miljön till rotmappen för exempel projekt.

```cmd
setprojectdir .
```

Aktivera den virtuella miljön.

```cmd
activate
```

Projektet bör nu vara aktivt och du ser något som liknar `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` i kommando tolken.

Inaktivera miljön.

```cmd
deactivate
```

`(advanced-python)` Prefixet bör nu vara borta eftersom miljön nu är inaktive rad.

Så här återaktiverar du `workon advanced-python` miljön från rotmappen för exempelprojektet.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

När miljön är aktiv kan du köra exempelprojektet genom att ange `flask run` från rotmappen för exempel projekt.

```cmd
flask run
```

Öppna webbläsaren och gå till _http://localhost:5000_ .

## <a name="create-a-python-web-app-on-osx"></a>Skapa en python-webbapp på OSX

Skapa en python-webbapp med `flask` på OSX.

Installera [Git](https://git-scm.com/).

När git har installerats med öppen Terminal och klonad git-lagringsplatsen för avancerad Reader-SDK till en mapp på din dator

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installera [Python](https://www.python.org/downloads/).

Mappen python rotmapp t. ex `Python37-32` . bör nu finnas i mappen program.

När python-installationen är klar öppnar du Terminal `cd` och mappen Python-skript.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installera pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Kör sedan följande för att installera pip för den aktuella inloggade användaren för att undvika problem med behörighet.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Ange ditt lösen ord när du uppmanas till det.
- Lägg till sökvägen till din pip-installation till din PATH-variabel.
- Gå till slutet av filen och ange den sökväg som du vill lägga till som sista objekt i listan, t. ex. `PATH=$PATH:/usr/local/bin`.
- Tryck på CTRL-x för att avsluta.
- Ange `Y` för att spara den ändrade bufferten.
- Klart! Testa det genom att skriva följande i nytt terminalfönster: `echo $PATH`.

Installations flaska.

```bash
pip install flask --user
```

Installera Jinja2. En mall för en komplett motor för python.

```bash
pip install Jinja2 --user
```

Installera virtuell miljö. Ett verktyg för att skapa isolerade python-miljöer.

```bash
pip install virtualenv --user
```

Installera virtualenvwrapper. Idén bakom virtualenvwrapper är att under lätta användningen av virtuell miljö.

```bash
pip install virtualenvwrapper --user
```

Installera modulen begär Anden. Begär Anden är ett apache2 licensierat HTTP-bibliotek, skrivet i python.

```bash
pip install requests --user
```

Välj en mapp där du vill behålla dina virtuella miljöer och kör det här kommandot

```bash
mkdir ~/.virtualenvs
```

`cd`till mappen avancerad Reader-SDK python-exempel.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Skapa en virtuell miljö

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Anslut exempel projektet till-miljön. Detta mappar den nyligen skapade virtuella miljön till rotmappen för exempel projekt.

```bash
setprojectdir .
```

Aktivera den virtuella miljön.

```bash
activate
```

Projektet bör nu vara aktivt och du ser något som liknar `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` i kommando tolken.

Inaktivera miljön.

```bash
deactivate
```

`(advanced-python)` Prefixet bör nu vara borta eftersom miljön nu är inaktive rad.

Så här återaktiverar du `workon advanced-python` miljön från rotmappen för exempelprojektet.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta den fördjupade läsaren med exempel innehåll

När miljön är aktiv kan du köra exempelprojektet genom att ange `flask run` från rotmappen för exempel projekt.

```bash
flask run
```

Öppna webbläsaren och gå till _http://localhost:5000_ .

## <a name="next-steps"></a>Nästa steg

* Utforska SDK: [n för avancerad läsare](https://github.com/microsoft/immersive-reader-sdk) och [Avancerad läsare SDK-referens](./reference.md)
* Visa kod exempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
