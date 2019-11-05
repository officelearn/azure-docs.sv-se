---
title: 'Snabb start: skapa python-app på Linux – Azure App Service'
description: Distribuera din Hello World-app skriven i Python med Azure App Service i Linux på bara några minuter.
services: app-service\web
documentationcenter: ''
author: cephalin
manager: gwallace
editor: ''
ms.assetid: ''
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 10/22/2019
ms.author: cephalin
ms.custom: seo-python-october2019
experimental: false
experiment_id: 1e304dc9-5add-4b
ms.openlocfilehash: 69e7cfef01005432a99dd10ed5bc7f004562e582
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2019
ms.locfileid: "73470808"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snabb start: skapa en python-app i Azure App Service på Linux

I den här snabb starten distribuerar du en python-webbapp till [app service på Linux](app-service-linux-intro.md), Azures mycket skalbara, självkorrigerande webb värd tjänst. Du använder det lokala [Azures kommando rads gränssnitt (CLI)](/cli/azure/install-azure-cli) på en Mac-, Linux-eller Windows-dator. Webbappen som du konfigurerar använder en kostnads fri App Service nivå, så du debiteras inte några kostnader i samband med den här artikeln.

Om du föredrar att distribuera appar via en IDE, se [distribuera python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (python 3,6 stöds också)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

## <a name="download-the-sample"></a>Hämta exemplet

Kör följande kommando i ett terminalfönster för att klona exempel programmet till din lokala dator. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Gå sedan till mappen:

```terminal
cd python-docs-hello-world
```

Lagrings platsen innehåller en *Application.py* -fil som talar om för App Service att koden innehåller en kolv-app. Mer information finns i [Containerns startprocess och anpassningar](how-to-configure-python.md).

## <a name="run-the-sample"></a>Kör exemplet

Använd följande kommandon i ett terminalfönster (efter behov för operativ systemet) för att installera de nödvändiga beroendena och starta den inbyggda utvecklings servern. 

# <a name="bashtabbash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
FLASK_APP=application.py
flask run
```

# <a name="powershelltabpowershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmdtabcmd"></a>[Kommandot](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Öppna en webbläsare och gå till exempel appen på `http://localhost:5000/`. Appen visar meddelandet **Hello World!** .

![Kör en exempel-python-app lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

Tryck på **Ctrl**+**C** i terminalfönstret för att avsluta webb servern.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Azure CLI innehåller många praktiska kommandon som du använder från en lokal Terminal för att etablera och hantera Azure-resurser från kommando raden. Du kan använda kommandon för att utföra samma uppgifter som du skulle göra i Azure Portal i en webbläsare. Du kan också använda CLI-kommandon i skript för att automatisera hanterings processer.

Om du vill köra Azure-kommandon i Azure CLI måste du först logga in med kommandot `az login`. Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter.

```terminal
az login
```

## <a name="deploy-the-sample"></a>Distribuera exemplet

[`az webapp up`](/cli/azure/webapp#az-webapp-up) -kommandot skapar webbappen på App service och distribuerar koden.

Kör följande `az webapp up` kommando i mappen *python-dok-Hello-World* som innehåller exempel koden. Ersätt `<app-name>` med ett globalt unikt namn på appen (*giltiga tecken är `a-z`, `0-9`och `-`* ). Ersätt också `<location-name>` med en Azure-region, till exempel **Central**, **asienöstra**, **westeurope**, **koreasödra**, **centrala**, **Kanada**och så vidare. (Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra kommandot [`az account locations-list`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) .)


```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

Det kan ta några minuter att köra det här kommandot. Medan det körs visas information liknande den i följande exempel:

```output
The behavior of this command has been altered by the following extension: webapp
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '<app-name>' ....
Webapp creation complete
Creating zip with contents of dir /home/username/quickstart/python-docs-hello-world ...
Preparing to deploy contents to app.
All done.
{
  "app_url": "https:/<app-name>.azurewebsites.net",
  "location": "Central US",
  "name": "<app-name>",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus ",
  "serverfarm": "appsvc_asp_Linux_centralus",
  "sku": "BASIC",
  "src_path": "/home/username/quickstart/python-docs-hello-world ",
  "version_detected": "-",
  "version_to_create": "python|3.7"
}
```

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till det distribuerade programmet i webbläsaren på webb adressen `http://<app-name>.azurewebsites.net`.

Python-exempel koden kör en Linux-behållare i App Service med hjälp av en inbyggd avbildning.

![Köra en python-app i Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Grattis!** Du har distribuerat din python-app till App Service i Linux.

## <a name="redeploy-updates"></a>Distribuera om uppdateringar

Öppna *Application.py* i din favorit kod redigerare och ändra `return`-satsen på den sista raden så att den matchar följande kod. Instruktionen `print` finns här för att generera loggnings utdata som du arbetar med i nästa avsnitt. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Spara dina ändringar och avsluta redigeraren. 

Distribuera om appen med följande `az webapp up` kommando med samma kommando som du använde för att distribuera appen första gången, och ersätta `<app-name>` och `<location-name>` med samma namn som du använde tidigare. 

```terminal
az webapp up --sku F1 -n <app-name> -l <location-name>
```

När distributionen har slutförts växlar du tillbaka till webbläsarfönstret öppna för att `http://<app-name>.azurewebsites.net` och uppdatera sidan, som ska visa det ändrade meddelandet:

![Köra en uppdaterad exempel-python-app i Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code tillhandahåller kraftfulla tillägg för python och Azure App Service, vilket fören klar processen att distribuera python-webbappar till App Service. Mer information finns i [distribuera python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strömningsloggar

Du kan komma åt konsol loggarna som genereras inifrån appen och den behållare där den körs. Loggar innehåller alla utdata som genereras med hjälp av `print`-instruktioner.

Först aktiverar du behållar loggning genom att köra följande kommando i en Terminal, ersätta `<app-name>` med namnet på din app och `<resource-group-name>` med namnet på den resurs grupp som visas i utdata från `az webapp up` kommandot som du använde (till exempel "appsvc_rg_Linux_centralus" ):

```terminal
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

När behållar loggning har Aktiver ATS kör du följande kommando för att Visa logg strömmen:

```terminal
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Uppdatera appen i webbläsaren för att generera konsol loggar som ska innehålla rader som liknar följande text. Om utdata inte visas direkt, försök igen om 30 sekunder.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Om du vill stoppa logg strömningen när som helst, skriver du `Ctrl`+`C`.

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till <a href="https://portal.azure.com" target="_blank">Azure Portal</a> för att hantera den app som du skapade. Sök efter och välj **app Services**.

![Navigera till App Services i Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Välj namnet på din Azure-App.

![Navigera till python-appen i App Services i Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Du ser din apps översiktssida. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![Hantera din python-app på sidan Översikt i Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service-menyn innehåller olika sidor för att konfigurera din app.

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Resurs gruppen har ett namn som "appsvc_rg_Linux_CentralUS" beroende på din plats. Om du använder en annan App Service SKU än den kostnads fria F1-nivån kommer dessa resurser att medföra kontinuerliga kostnader.

Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen genom att köra följande kommando och ersätter `<resource-group-name>` med resurs gruppen som visas i utdata från kommandot `az webapp up`, till exempel "appsvc_rg_Linux_centralus". Det kan ta en minut att slutföra kommandot.

```terminal
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: python (django)-webbapp med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera python-app](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Självstudie: köra python-app i en anpassad behållare](tutorial-custom-docker-image.md)
