---
title: 'Snabb start: skapa en Linux python-app'
description: Kom igång med Linux-appar på Azure App Service genom att distribuera din första python-app till en Linux-behållare i App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 606758cf81d069124c67bc06c650b96d91262deb
ms.sourcegitcommit: acc558d79d665c8d6a5f9e1689211da623ded90a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2020
ms.locfileid: "82597883"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snabb start: skapa en python-app i Azure App Service på Linux

I den här snabb starten distribuerar du en python-webbapp till [app service på Linux](app-service-linux-intro.md), Azures mycket skalbara, självkorrigerande webb värd tjänst. Du använder det lokala [Azures kommando rads gränssnitt (CLI)](/cli/azure/install-azure-cli) på en Mac-, Linux-eller Windows-dator. Webbappen som du konfigurerar använder en kostnads fri App Service nivå, så du debiteras inte några kostnader i samband med den här artikeln.

Om du föredrar att distribuera appar via en IDE, se [distribuera python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3,7</a> (python 3,6 stöds också)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 eller högre. Kör `az --version` för att kontrollera vilken version du har.

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

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
SET FLASK_APP=application.py
flask run
```

---

Öppna en webbläsare och gå till exempel appen på `http://localhost:5000/`. Appen visar meddelandet **Hello World!**.

![Kör en exempel-python-app lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Tryck på **CTRL**+**C** i terminalfönstret för att avsluta webb servern.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Azure CLI innehåller många praktiska kommandon som du använder från en lokal Terminal för att etablera och hantera Azure-resurser från kommando raden. Du kan använda kommandon för att utföra samma uppgifter som du skulle göra i Azure Portal i en webbläsare. Du kan också använda CLI-kommandon i skript för att automatisera hanterings processer.

Om du vill köra Azure-kommandon i Azure CLI måste du först logga in med `az login` kommandot. Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Distribuera exemplet

- [`az webapp up`](/cli/azure/webapp#az-webapp-up) Kommandot skapar webbappen på App service och distribuerar koden.

Kör följande `az webapp up` kommando i mappen *python-dok-Hello-World* som innehåller exempel koden. Ersätt `<app-name>` med ett globalt unikt namn på appen (*giltiga tecken `a-z`är `0-9`, och `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```
> [!CAUTION]
> Om du använder **Azure-CLI version 2.5.0** finns det en regression i `az webapp up` där vissa scenarier Miss kan uppstå om `-l <location-name>` parametern inte ingår. Det här problemet [spåras här](https://github.com/Azure/azure-cli/issues/13257).  
> 
>Du kan kontrol lera vilken version av Azure-CLI som du använder med `az --version` kommandot.
>

`--sku F1` Argumentet skapar webb programmet på den kostnads fria pris nivån. Du kan utelämna det här argumentet om du vill använda en Premium-nivå i stället, vilket innebär en timkostnad.

Du kan också inkludera argumentet `-l <location-name>` där `<location_name>` är en Azure-region, till exempel **Central**, **asienöstra**, **westeurope**, **koreasödra**, **centrala**, **Kanada**och så vidare. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) kommandot.

Det `az webapp up` kan ta några minuter att köra kommandot. När du kör visas information som liknar följande exempel, där `<app-name>` är det namn du angav tidigare:

<pre>
Creating Resource group 'appsvc_rg_Linux_centralus' ...
Resource group creation complete
Creating App service plan 'appsvc_asp_Linux_centralus' ...
App service plan creation complete
Creating app '&lt;app-name&gt;' ....
Configuring default logging for the app, if not already enabled
Creating zip with contents of dir D:\Examples\python-docs-hello-world ...
Getting scm site credentials for zip deployment
Starting zip deployment. This operation can take a while to complete ...
Deployment endpoint responded with status code 202
You can launch the app at http://&lt;app-name&gt;.azurewebsites.net
{
  "URL": "http://&lt;app-name&gt;.net",
  "appserviceplan": "appsvc_asp_Linux_centralus",
  "location": "eastus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "appsvc_rg_Linux_centralus",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "FREE",
  "src_path": "D:\\Examples\\python-docs-hello-world"
}
</pre>

[!INCLUDE [AZ Webapp Up Note](../../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till det distribuerade programmet i webbläsaren på webb adressen `http://<app-name>.azurewebsites.net`.

Python-exempel koden kör en Linux-behållare i App Service med hjälp av en inbyggd avbildning.

![Köra en python-app i Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Grattis!** Du har distribuerat din python-app till App Service i Linux.

## <a name="redeploy-updates"></a>Distribuera om uppdateringar

Öppna *Application.py* i din favorit kod redigerare och uppdatera `hello` funktionen enligt följande. Den här ändringen lägger `print` till en instruktion för att generera logga utdata som du arbetar med i nästa avsnitt. 

```python
def hello():
    print("Handling request to home page.")
    return "Hello Azure!"
```

Spara dina ändringar och avsluta redigeraren. 

Distribuera om appen med `az webapp up` kommandot igen:

```azurecli
az webapp up
```

Det här kommandot använder värden som är cachelagrade i *. Azure/config* -filen, inklusive app-namn, resurs grupp och App Service plan.

När distributionen är klar växlar du tillbaka till webbläsarfönstret öppna `http://<app-name>.azurewebsites.net` och uppdaterar sidan, som ska visa det ändrade meddelandet:

![Köra en uppdaterad exempel-python-app i Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code tillhandahåller kraftfulla tillägg för python och Azure App Service, vilket fören klar processen att distribuera python-webbappar till App Service. Mer information finns i [distribuera python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strömningsloggar

Du kan komma åt konsol loggarna som genereras inifrån appen och den behållare där den körs. Loggar innehåller alla utdata som genereras `print` med hjälp av instruktioner.

Kör följande kommando för att strömma loggar:

```azurecli
az webapp log tail
```

Uppdatera appen i webbläsaren för att generera konsol loggar som ska innehålla rader som liknar följande text. Om utdata inte visas direkt, försök igen om 30 sekunder.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Om du vill stoppa logg strömningen när som `Ctrl` + `C`helst skriver du.

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade. Sök efter och välj **app Services**.

![Navigera till App Services i Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Välj namnet på din Azure-App.

![Navigera till python-appen i App Services i Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Nu visas översiktssidan för din app. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![Hantera din python-app på sidan Översikt i Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service-menyn innehåller olika sidor för att konfigurera din app.

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Resurs gruppen har ett namn som "appsvc_rg_Linux_CentralUS" beroende på din plats. Om du använder en annan App Service SKU än den kostnads fria F1-nivån medför dessa resurser kontinuerliga kostnader (se [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen genom att köra följande kommando och ersätter `<resource-group-name>` med resurs gruppen som visas i `az webapp up` kommandots utdata, till exempel "appsvc_rg_Linux_centralus". Det kan ta en minut att slutföra kommandot.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: python (django)-webbapp med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Lägga till användar inloggning till en python-webbapp](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Konfigurera python-app](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Självstudie: köra python-app i en anpassad behållare](tutorial-custom-docker-image.md)
