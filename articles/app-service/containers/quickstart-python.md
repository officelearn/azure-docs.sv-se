---
title: 'Snabbstart: Skapa en Linux Python-app'
description: Kom igång med Linux-appar på Azure App Service genom att distribuera din första Python-app till en Linux-behållare i App Service.
ms.topic: quickstart
ms.date: 10/22/2019
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 9cc314edf35d6a327522ed49fcc0c7798c7dcf63
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "80045671"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snabbstart: Skapa en Python-app i Azure App Service på Linux

I den här snabbstarten distribuerar du en Python-webbapp till [App-tjänsten på Linux](app-service-linux-intro.md), Azures mycket skalbara, självkorrigeringsbaserade webbhotell. Du använder det lokala [Azure-kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli) på en Mac-, Linux- eller Windows-dator. Webbappen som du konfigurerar använder en kostnadsfri App Service-nivå, så du ådrar dig inga kostnader under den här artikeln.

Om du föredrar att distribuera appar via en IDE läser [du Distribuera Python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 stöds också)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a>

## <a name="download-the-sample"></a>Hämta exemplet

I ett terminalfönster kör du följande kommando för att klona exempelprogrammet till den lokala datorn. 

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Gå sedan in i den mappen:

```terminal
cd python-docs-hello-world
```

Databasen innehåller en *application.py-fil* som talar om för App Service att koden innehåller en Flask-app. Mer information finns i [Containerns startprocess och anpassningar](how-to-configure-python.md).

## <a name="run-the-sample"></a>Kör exemplet

I ett terminalfönster använder du kommandona nedan (beroende på vilket operativsystem som krävs) för att installera de nödvändiga beroendena och starta den inbyggda utvecklingsservern. 

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[Powershell](#tab/powershell)

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

Öppna en webbläsare och gå till `http://localhost:5000/`exempelappen på . Appen visar meddelandet **Hello World!**.

![Köra ett exempel på Python-app lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Tryck på **Ctrl**+**C** i terminalfönstret för att avsluta webbservern.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Azure CLI ger dig många praktiska kommandon som du använder från en lokal terminal för att etablera och hantera Azure-resurser från kommandoraden. Du kan använda kommandon för att slutföra samma uppgifter som du skulle utföra via Azure-portalen i en webbläsare. Du kan också använda CLI-kommandon i skript för att automatisera hanteringsprocesser.

Om du vill köra Azure-kommandon i Azure CLI `az login` måste du först logga in med kommandot. Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter.

```azurecli
az login
```

## <a name="deploy-the-sample"></a>Distribuera exemplet

Kommandot [`az webapp up`](/cli/azure/webapp#az-webapp-up) skapar webbappen på App Service och distribuerar din kod.

I mappen *python-docs-hello-world* som innehåller exempelkoden kör `az webapp up` du följande kommando. Ersätt `<app-name>` med ett globalt unikt appnamn (*giltiga tecken är `a-z`, `0-9`och `-` *). Ersätt `<location-name>` också med en Azure-region som **centralus**, **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**och så vidare. (Du kan hämta en lista över tillåtna regioner [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) för ditt Azure-konto genom att köra kommandot.)


```azurecli
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

Bläddra till det distribuerade programmet i `http://<app-name>.azurewebsites.net`webbläsaren på URL: et .

Python-exempelkoden kör en Linux-behållare i App Service med hjälp av en inbyggd avbildning.

![Köra ett exempel på Python-appen i Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Grattis!** Du har distribuerat din Python-app till App Service på Linux.

## <a name="redeploy-updates"></a>Distribuera om uppdateringar

Öppna *application.py i* din favoritkodredigerare `return` och ändra satsen på den sista raden så att den matchar följande kod. Satsen `print` ingår här för att generera loggningsutdata som du arbetar med i nästa avsnitt. 

```python
print("Handling request to home page.")
return "Hello Azure!"
```

Spara dina ändringar och avsluta redigeraren. 

Distribuera om appen med följande `az webapp up` kommando med samma kommando som du använde för `<app-name>` att `<location-name>` distribuera appen första gången och ersätta och med samma namn som du använde tidigare. 

```azurecli
az webapp up --sku F1 -n <app-name> -l <location-name>
```

När distributionen är klar växlar du `http://<app-name>.azurewebsites.net` tillbaka till webbläsarfönstret som är öppet till och uppdaterar sidan, vilket ska visa det ändrade meddelandet:

![Köra en uppdaterad Python-exempelapp i Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code innehåller kraftfulla tillägg för Python och Azure App Service, vilket förenklar processen för att distribuera Python-webbappar till App Service. Mer information finns i [Distribuera Python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strömningsloggar

Du kan komma åt konsolloggarna som genereras inifrån appen och behållaren där den körs. Loggar inkluderar alla utdata som genereras med hjälp av `print` satser.

Aktivera först behållarloggning genom att köra följande `<app-name>` kommando i en terminal, ersätta med namnet på din app och `<resource-group-name>` med namnet på resursgruppen som visas i utdata för det `az webapp up` kommando du använde (till exempel "appsvc_rg_Linux_centralus"):

```azurecli
az webapp log config --name <app-name> --resource-group <resource-group-name> --docker-container-logging filesystem
```

När behållarloggning är aktiverat kör du följande kommando för att visa loggströmmen:

```azurecli
az webapp log tail --name <app-name> --resource-group <resource-group-name>
```

Uppdatera appen i webbläsaren för att generera konsolloggar, som bör innehålla rader som liknar följande text. Om du inte ser utdata omedelbart kan du försöka igen om 30 sekunder.

```output
2019-10-23T12:40:03.815574424Z Handling request to home page.
2019-10-23T12:40:03.815602424Z 172.16.0.1 - - [23/Oct/2019:12:40:03 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/78.0.3904.63 Safari/537.36 Edg/78.0.276.19"
```

Du kan också granska loggfilerna `https://<app-name>.scm.azurewebsites.net/api/logs/docker`från webbläsaren på .

Om du vill stoppa loggströmning när som helst skriver du `Ctrl` + `C`.

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade. Sök efter och välj **AppTjänster**.

![Navigera till App Services i Azure-portalen](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Välj namnet på din Azure-app.

![Navigera till python-appen i App Services i Azure-portalen](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

Nu visas översiktssidan för din app. Här kan du utföra grundläggande hanteringsåtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![Hantera din Python-app på översiktssidan i Azure-portalen](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

På menyn Appservice finns olika sidor där appen kan konfigureras.

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Resursgruppen har ett namn som "appsvc_rg_Linux_CentralUS" beroende på var du befinner dig. Om du använder en apptjänst SKU än den kostnadsfria F1-nivån medför dessa resurser löpande kostnader.

Om du inte förväntar dig att behöva dessa resurser i framtiden tar du `<resource-group-name>` bort resursgruppen genom att `az webapp up` köra följande kommando och ersätter med resursgruppen som visas i utdata från kommandot, till exempel "appsvc_rg_Linux_centralus". Kommandot kan ta en minut att slutföra.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Handledning: Python (Django) webbapp med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Konfigurera Python-appen](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Självstudiekurs: Kör Python-appen i anpassad behållare](tutorial-custom-docker-image.md)
