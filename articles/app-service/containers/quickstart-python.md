---
title: 'Snabbstart: Skapa en Linux Python-app'
description: Kom igång med Linux-appar på Azure App Service genom att distribuera din första Python-app till en Linux-behållare i App Service.
ms.topic: quickstart
ms.date: 04/03/2020
ms.custom: seo-python-october2019, cli-validate
experimental: true
experiment_id: 01a9132f-eaab-4c
ms.openlocfilehash: 7f8d21cbccc1a6eec7f62ab5d129f3f0637b8d13
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085135"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snabbstart: Skapa en Python-app i Azure App Service på Linux

I den här snabbstarten distribuerar du en Python-webbapp till [App-tjänsten på Linux](app-service-linux-intro.md), Azures mycket skalbara, självkorrigeringsbaserade webbhotell. Du använder det lokala [Azure-kommandoradsgränssnittet (CLI)](/cli/azure/install-azure-cli) på en Mac-, Linux- eller Windows-dator. Webbappen som du konfigurerar använder en kostnadsfri App Service-nivå, så du ådrar dig inga kostnader under den här artikeln.

Om du föredrar att distribuera appar via en IDE läser [du Distribuera Python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="prerequisites"></a>Krav

- Azure-prenumeration - [skapa en gratis](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- <a href="https://www.python.org/downloads/" target="_blank">Python 3.7</a> (Python 3.6 stöds också)
- <a href="https://git-scm.com/downloads" target="_blank">Git</a>
- <a href="https://docs.microsoft.com/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> 2.0.80 eller senare. Kör `az --version` för att kontrollera vilken version du har.

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

I mappen *python-docs-hello-world* som innehåller exempelkoden kör `az webapp up` du följande kommando. Ersätt `<app-name>` med ett globalt unikt appnamn (*giltiga tecken är `a-z`, `0-9`och `-` *).


```azurecli
az webapp up --sku F1 -n <app-name>
```

Argumentet `--sku F1` skapar webbappen på prisnivån Gratis. Du kan utelämna det här argumentet om du vill använda en premiumnivå i stället, vilket medför en timkostnad.

Du kan också inkludera `-l <location-name>` `<location_name>` argumentet där är en Azure-region som **centralus,** **eastasia**, **westeurope**, **koreasouth**, **brazilsouth**, **centralindia**och så vidare. Du kan hämta en lista över tillåtna regioner [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) för ditt Azure-konto genom att köra kommandot.

Kommandot `az webapp up` kan ta några minuter att köra helt. När du kör den visas information som `<app-name>` liknar följande exempel, där namnet du angav tidigare:

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

Bläddra till det distribuerade programmet i `http://<app-name>.azurewebsites.net`webbläsaren på URL: et .

Python-exempelkoden kör en Linux-behållare i App Service med hjälp av en inbyggd avbildning.

![Köra ett exempel på Python-appen i Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Grattis!** Du har distribuerat din Python-app till App Service på Linux.

## <a name="redeploy-updates"></a>Distribuera om uppdateringar

Öppna *application.py i* din favoritkodredigerare `hello` och uppdatera funktionen enligt följande. Den här `print` ändringen lägger till en sats för att generera loggningsutdata som du arbetar med i nästa avsnitt. 

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

Det här kommandot använder värden som cachelagras i *.azure/config-filen,* inklusive appens namn, resursgrupp och App Service-plan.

När distributionen är klar växlar du `http://<app-name>.azurewebsites.net` tillbaka till webbläsarfönstret som är öppet till och uppdaterar sidan, vilket ska visa det ändrade meddelandet:

![Köra en uppdaterad Python-exempelapp i Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code innehåller kraftfulla tillägg för Python och Azure App Service, vilket förenklar processen för att distribuera Python-webbappar till App Service. Mer information finns i [Distribuera Python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strömningsloggar

Du kan komma åt konsolloggarna som genereras inifrån appen och behållaren där den körs. Loggar inkluderar alla utdata som genereras med hjälp av `print` satser.

Om du vill strömma loggar kör du följande kommando:

```azurecli
az webapp log tail
```

Uppdatera appen i webbläsaren för att generera konsolloggar, som bör innehålla rader som liknar följande text. Om du inte ser utdata omedelbart kan du försöka igen om 30 sekunder.

<pre>
2020-04-03T22:54:04.236405938Z Handling request to home page.
2020-04-03T22:54:04.236497641Z 172.16.0.1 - - [03/Apr/2020:22:54:04 +0000] "GET / HTTP/1.1" 200 12 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/81.0.4044.83 Safari/537.36 Edg/81.0.416.41"
</pre>

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

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Resursgruppen har ett namn som "appsvc_rg_Linux_CentralUS" beroende på var du befinner dig. Om du använder en annan App Service SKU än den kostnadsfria F1-nivån medför dessa resurser löpande kostnader (se [priser för App-tjänst).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Om du inte förväntar dig att behöva dessa resurser i framtiden tar du `<resource-group-name>` bort resursgruppen genom att `az webapp up` köra följande kommando och ersätter med resursgruppen som visas i utdata från kommandot, till exempel "appsvc_rg_Linux_centralus". Kommandot kan ta en minut att slutföra.

```azurecli
az group delete -n <resource-group-name>
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Handledning: Python (Django) webbapp med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Lägga till användarloggning i en Python-webbapp](../../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Konfigurera Python-appen](how-to-configure-python.md)

> [!div class="nextstepaction"]
> [Självstudiekurs: Kör Python-appen i anpassad behållare](tutorial-custom-docker-image.md)
