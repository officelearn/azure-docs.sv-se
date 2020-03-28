---
title: 'Självstudiekurs: Starta den uppslukande läsaren med Python'
titleSuffix: Azure Cognitive Services
description: I den här självstudien ska du skapa ett Python-program som startar Immersive Reader.
services: cognitive-services
author: dylankil
manager: nitinme
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: tutorial
ms.date: 01/14/2020
ms.author: dylankil
ms.openlocfilehash: a252afae0a007ee0b791b56d19ffb0685848d30a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844368"
---
# <a name="tutorial-launch-the-immersive-reader-using-the-python-sample-project"></a>Självstudiekurs: Starta Immersive Reader med exempelprojektet Python

I [översikten](./overview.md)lärde du dig om vad Immersive Reader är och hur den implementerar beprövade tekniker för att förbättra läsförståelsen för språkstuderande, nya läsare och elever med inlärningsskillnader. Den här självstudien beskriver hur du skapar ett Python-webbprogram som startar Immersive Reader. I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Skapa en Python-webbapp med Pip, Flask, Jinja och virtualenv med hjälp av ett exempelprojekt
> * Hämta en åtkomsttoken
> * Starta Immersive Reader med exempelinnehåll

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="prerequisites"></a>Krav

* En Immersive Reader-resurs som konfigurerats för Azure Active Directory-autentisering. Följ [dessa instruktioner](./how-to-create-immersive-reader.md) för att komma igång. Du behöver några av de värden som skapas här när du konfigurerar miljöegenskaperna. Spara utdata från sessionen i en textfil för framtida referens.
* [Git](https://git-scm.com/)
* [Uppslukande läsare SDK](https://github.com/microsoft/immersive-reader-sdk)
* [Python](https://www.python.org/downloads/) och [pip](https://docs.python.org/3/installing/index.html). Från och med Python 3.4 ingår pip som standard med python-binära installationsprogram.
* [Kolven](https://flask.palletsprojects.com/en/1.0.x/)
* [Jinja](http://jinja.pocoo.org/docs/2.10/)
* [virtualenv](https://virtualenv.pypa.io/en/latest/) och [virtualenvwrapper-win för Windows](https://pypi.org/project/virtualenvwrapper-win/) eller [virtualenvwrapper för OSX](https://virtualenvwrapper.readthedocs.io/en/latest/)
* [begär modul](https://pypi.org/project/requests/2.7.0/)
* En IDE som [Visual Studio-kod](https://code.visualstudio.com/)

## <a name="configure-authentication-credentials"></a>Konfigurera autentiseringsuppgifter

Skapa en ny fil med namnet _.env_och klistra in följande kod i den och ange de värden som angavs när du skapade Immersive Reader-resursen.

```text
TENANT_ID={YOUR_TENANT_ID}
CLIENT_ID={YOUR_CLIENT_ID}
CLIENT_SECRET={YOUR_CLIENT_SECRET}
SUBDOMAIN={YOUR_SUBDOMAIN}
```

Se till att inte arkivera den här filen i källkontroll, eftersom den innehåller hemligheter som inte bör offentliggöras.

Den **getimmersivereadertoken** API-slutpunkten bör skyddas bakom någon form av autentisering (till exempel [OAuth)](https://oauth.net/2/)för att förhindra obehöriga användare från att få token att använda mot din Immersive Reader-tjänst och fakturering; att arbetet är utanför ramen för den här självstudien.

## <a name="create-a-python-web-app-on-windows"></a>Skapa en Python-webbapp i Windows

Skapa en Python-webbapp med i `flask` Windows.

Installera [Git](https://git-scm.com/).

När Git har installerats öppnar du en kommandotolk och "klonar" Immersive Reader SDK Git-databasen till en mapp på datorn

```cmd
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installera [Python](https://www.python.org/downloads/).

Markera rutan Lägg till Python i SÖKVÄG.

![Dialogrutan Installera Python Windows](./media/pythoninstallone.jpg)

Lägg till valfria funktioner genom att markera rutorna och klicka sedan på knappen "Nästa".

![Dialogrutan Installera Python Windows](./media/pythoninstalltwo.jpg)

Välj "Anpassad installation" och ställ in installationssökvägen `C:\Python37-32\` som rotmapp, t.ex.

![Dialogrutan Installera Python Windows](./media/pythoninstallthree.jpg)

När Python-installationen är klar öppnar `cd` du en kommandotolk och till mappen Python Scripts.

```cmd
cd C:\Python37-32\Scripts
```

Installera kolv.

```cmd
pip install flask
```

Installera Jinja2. En fullfjädrad mallmotor för Python.

```cmd
pip install jinja2
```

Installera virtualenv. Ett verktyg för att skapa isolerade Python-miljöer.

```cmd
pip install virtualenv
```

Installera virtualenvwrapper-win. Tanken bakom virtualenvwrapper är att underlätta användningen av virtualenv.

```cmd
pip install virtualenvwrapper-win
```

Installera modulen begäranden. Begäranden är ett Apache2-licensierat HTTP-bibliotek, skrivet i Python.

```cmd
pip install requests
```

Installera python-dotenv-modulen. Den här modulen läser nyckel-värde-paret från .env-filen och lägger till dem i miljövariabeln.

```cmd
pip install python-dotenv
```

Skapa en virtuell miljö

```cmd
mkvirtualenv advanced-python
```

`cd`till exempelprojektets rotmapp.

```cmd
cd C:\immersive-reader-sdk\js\samples\advanced-python
```

Anslut exempelprojektet med miljön. Detta mappar den nyskapade virtuella miljön till exempelprojektets rotmapp.

```cmd
setprojectdir .
```

Aktivera den virtuella miljön.

```cmd
activate
```

Projektet ska nu vara aktivt och du `(advanced-python) C:\immersive-reader-sdk\js\samples\advanced-python>` ser något liknande i kommandotolken.

Inaktivera miljön.

```cmd
deactivate
```

Prefixet `(advanced-python)` ska nu vara borta eftersom miljön nu är inaktiverad.

Om du vill återaktivera miljön körs `workon advanced-python` från exempelprojektrotmappen.

```cmd
workon advanced-python
```

### <a name="launch-the-immersive-reader-with-sample-content"></a>Starta Immersive Reader med exempelinnehåll

När miljön är aktiv kör du exempelprojektet genom att gå in `flask run` från exempelprojektets rotmapp.

```cmd
flask run
```

Öppna webbläsaren och _http://localhost:5000_navigera till .

## <a name="create-a-python-web-app-on-osx"></a>Skapa en Python-webbapp på OSX

Skapa en Python-webbapp med på `flask` OSX.

Installera [Git](https://git-scm.com/).

När Git har installerats öppna Terminal och "klona" Immersive Reader SDK Git-databasen till en mapp på datorn

```bash
git clone https://github.com/microsoft/immersive-reader-sdk.git
```

Installera [Python](https://www.python.org/downloads/).

Python-rotmappen t.ex. `Python37-32`

När Python-installationen är klar `cd` öppnar du Terminal och till mappen Python Scripts.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Installera pip.

```bash
curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py
```

Kör sedan följande för att installera pip för den inloggade användaren för att undvika behörighetsproblem.

```bash
python get-pip.py --user
```

```bash
sudo nano /etc/paths
```

- Ange ditt lösenord när du uppmanas att göra det.
- Lägg till sökvägen för pipinstallationen till sökvägens variabel.
- Gå längst ned i filen och ange den sökväg som du vill lägga `PATH=$PATH:/usr/local/bin`till som det sista objektet i listan t.ex.
- Tryck på control-x för att avsluta.
- Ange `Y` för att spara den ändrade bufferten.
- Klart! Om du vill testa det skriver `echo $PATH`du i det nya terminalfönstret: .

Installera kolv.

```bash
pip install flask --user
```

Installera Jinja2. En fullfjädrad mallmotor för Python.

```bash
pip install Jinja2 --user
```

Installera virtualenv. Ett verktyg för att skapa isolerade Python-miljöer.

```bash
pip install virtualenv --user
```

Installera virtualenvwrapper. Tanken bakom virtualenvwrapper är att underlätta användningen av virtualenv.

```bash
pip install virtualenvwrapper --user
```

Installera modulen begäranden. Begäranden är ett Apache2-licensierat HTTP-bibliotek, skrivet i Python.

```bash
pip install requests --user
```

Installera python-dotenv-modulen. Den här modulen läser nyckel-värde-paret från .env-filen och lägger till dem i miljövariabeln.

```bash
pip install python-dotenv --user
```

Välj en mapp där du vill behålla dina virtuella miljöer och köra det här kommandot

```bash
mkdir ~/.virtualenvs
```

`cd`till exempelprogrammappen för Immersive Reader SDK Python.

```bash
cd immersive-reader-sdk/js/samples/advanced-python
```

Skapa en virtuell miljö

```bash
mkvirtualenv -p /usr/local/bin/python3 advanced-python
```

Anslut exempelprojektet med miljön. Detta mappar den nyskapade virtuella miljön till exempelprojektets rotmapp.

```bash
setprojectdir .
```

Aktivera den virtuella miljön.

```bash
activate
```

Projektet ska nu vara aktivt och du `(advanced-python) /immersive-reader-sdk/js/samples/advanced-python>` ser något liknande i kommandotolken.

Inaktivera miljön.

```bash
deactivate
```

Prefixet `(advanced-python)` ska nu vara borta eftersom miljön nu är inaktiverad.

Om du vill återaktivera miljön körs `workon advanced-python` från exempelprojektrotmappen.

```bash
workon advanced-python
```

## <a name="launch-the-immersive-reader-with-sample-content"></a>Starta Immersive Reader med exempelinnehåll

När miljön är aktiv kör du exempelprojektet genom att gå in `flask run` från exempelprojektets rotmapp.

```bash
flask run
```

Öppna webbläsaren och _http://localhost:5000_navigera till .

## <a name="next-steps"></a>Nästa steg

* Utforska [den uppslukande läsaren SDK](https://github.com/microsoft/immersive-reader-sdk) och [Immersive Reader SDK-referensen](./reference.md)
* Visa kodexempel på [GitHub](https://github.com/microsoft/immersive-reader-sdk/tree/master/js/samples/)
