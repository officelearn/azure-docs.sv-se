---
title: 'Snabb start: skapa en python-app'
description: Kom igång med Azure App Service genom att distribuera din första python-app till en Linux-behållare i App Service.
ms.topic: quickstart
ms.date: 06/30/2020
ms.custom: seo-python-october2019, cli-validate, devx-track-python
ms.openlocfilehash: b258185d2fb7220332ee1287d399f0e49e5708a9
ms.sourcegitcommit: 2ffa5bae1545c660d6f3b62f31c4efa69c1e957f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/11/2020
ms.locfileid: "88085279"
---
# <a name="quickstart-create-a-python-app-in-azure-app-service-on-linux"></a>Snabb start: skapa en python-app i Azure App Service på Linux

I den här snabb starten distribuerar du en python-webbapp till [app service på Linux](overview.md#app-service-on-linux), Azures mycket skalbara, självkorrigerande webb värd tjänst. Du använder det lokala [Azures kommando rads gränssnitt (CLI)](/cli/azure/install-azure-cli) på en Mac-, Linux-eller Windows-dator. Webbappen som du konfigurerar använder en kostnads fri App Service nivå, så du debiteras inte några kostnader i samband med den här artikeln.

Om du föredrar att distribuera appar via en IDE, se [distribuera python-appar till App Service från Visual Studio Code](/azure/developer/python/tutorial-deploy-app-service-on-linux-01).

## <a name="set-up-your-initial-environment"></a>Konfigurera din inledande miljö

Innan du börjar måste du ha följande:

1. Ha ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
1. Installera <a href="https://www.python.org/downloads/" target="_blank">Python 3,6 eller högre</a>.
1. Installera <a href="/cli/azure/install-azure-cli" target="_blank">Azure CLI</a> -2.0.80 eller högre, med vilken du kör kommandon i alla gränssnitt för att etablera och konfigurera Azure-resurser.

Öppna ett terminalfönster och kontrol lera att python-versionen är 3,6 eller högre:

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python3 --version
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```cmd
py -3 --version
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -3 --version
```

---

Kontrol lera att din Azure CLI-version är 2.0.80 eller högre:

```azurecli
az --version
```

Logga sedan in på Azure via CLI:

```azurecli
az login
```

Det här kommandot öppnar en webbläsare för att samla in dina autentiseringsuppgifter. När kommandot har slutförts visas JSON-utdata som innehåller information om dina prenumerationer.

När du har loggat in kan du köra Azure-kommandon med Azure CLI för att arbeta med resurser i din prenumeration.

## <a name="clone-the-sample"></a>Klona exemplet

Klona exempel lagrings platsen med följande kommando. ([Installera git](https://git-scm.com/downloads) om du inte redan har git.)

```terminal
git clone https://github.com/Azure-Samples/python-docs-hello-world
```

Gå sedan till mappen:

```terminal
cd python-docs-hello-world
```

Exempel koden innehåller en *Application.py* -fil som talar om för App Service att koden innehåller en kolv-app. Mer information finns i [behållare start process](configure-language-python.md#container-startup-process).

## <a name="run-the-sample"></a>Kör exemplet

# <a name="bash"></a>[Bash](#tab/bash)

Skapa först en virtuell miljö och installera beroenden:

```bash
python3 -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

Ställ sedan in `FLASK_APP` miljövariabeln till appens Entry-modul och kör kolv-utvecklings servern:

```
export FLASK_APP=application.py
flask run
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Skapa först en virtuell miljö och installera beroenden:

```powershell
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Ställ sedan in `FLASK_APP` miljövariabeln till appens Entry-modul och kör kolv-utvecklings servern:

```powershell
Set-Item Env:FLASK_APP ".\application.py"
flask run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

Skapa först en virtuell miljö och installera beroenden:

```cmd
py -3 -m venv env
env\scripts\activate
pip install -r requirements.txt
```

Ställ sedan in `FLASK_APP` miljövariabeln till appens Entry-modul och kör kolv-utvecklings servern:

```cmd
SET FLASK_APP=application.py
flask run
```

---

Öppna en webbläsare och gå till exempel appen på `http://localhost:5000/` . Appen visar meddelandet **Hello World!**.

![Kör en exempel-python-app lokalt](./media/quickstart-python/run-hello-world-sample-python-app-in-browser-localhost.png)

Tryck på **CTRL** + **C** i terminalfönstret för att avsluta kolv utvecklings servern.

## <a name="deploy-the-sample"></a>Distribuera exemplet

Distribuera koden i din lokala mapp (*python-dok-Hello-World*) med hjälp av `az webapp up` kommandot:

```azurecli
az webapp up --sku F1 -n <app-name>
```

- Om `az` kommandot inte känns igen kontrollerar du att du har installerat Azure CLI enligt beskrivningen i [Konfigurera din inledande miljö](#set-up-your-initial-environment).
- Ersätt `<app_name>` med ett namn som är unikt för alla Azure (*giltiga tecken är `a-z` , `0-9` och `-` *). Ett utmärkt mönster är att använda en kombination av företagets namn och en app-ID.
- `--sku F1`Argumentet skapar webb programmet på den kostnads fria pris nivån. Utelämna det här argumentet om du vill använda en snabbare Premium-nivå, vilket innebär en timkostnad.
- Du kan också inkludera argumentet `-l <location-name>` där `<location_name>` är en Azure-region, till exempel **Central**, **asienöstra**, **westeurope**, **koreasödra**, **centrala**, **Kanada**och så vidare. Du kan hämta en lista över tillåtna regioner för ditt Azure-konto genom att köra [`az account list-locations`](/cli/azure/appservice?view=azure-cli-latest.md#az-appservice-list-locations) kommandot.

Det kan ta några minuter att slutföra kommandot. Under körningen innehåller den meddelanden om att skapa resurs gruppen, App Service plan och värd appen, Konfigurera loggning och sedan utföra ZIP-distribution. Det ger meddelandet "du kan starta appen på http:// &lt; App-Name &gt; . azurewebsites.net", som är appens URL på Azure.

![Exempel på utdata från kommandot AZ webapp up](./media/quickstart-python/az-webapp-up-output.png)

[!INCLUDE [AZ Webapp Up Note](../../includes/app-service-web-az-webapp-up-note.md)]

## <a name="browse-to-the-app"></a>Bläddra till appen

Bläddra till det distribuerade programmet i webbläsaren på webb adressen `http://<app-name>.azurewebsites.net` .

Python-exempel koden kör en Linux-behållare i App Service med hjälp av en inbyggd avbildning.

![Köra en python-app i Azure](./media/quickstart-python/run-hello-world-sample-python-app-in-browser.png)

**Grattis!** Du har distribuerat din python-app till App Service.

## <a name="redeploy-updates"></a>Distribuera om uppdateringar

Öppna *Application.py* i din favorit kod redigerare och uppdatera `hello` funktionen enligt följande. Den här ändringen lägger till en `print` instruktion för att generera logga utdata som du arbetar med i nästa avsnitt. 

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

Det här kommandot använder värden som cachelagras lokalt i *Azure/config* -filen, inklusive app-namn, resurs grupp och App Service plan.

När distributionen är klar går du tillbaka till webbläsarfönstret öppna till `http://<app-name>.azurewebsites.net` . Uppdatera sidan, som ska visa det ändrade meddelandet:

![Köra en uppdaterad exempel-python-app i Azure](./media/quickstart-python/run-updated-hello-world-sample-python-app-in-browser.png)

> [!TIP]
> Visual Studio Code tillhandahåller kraftfulla tillägg för python och Azure App Service, vilket fören klar processen att distribuera python-webbappar till App Service. Mer information finns i [distribuera python-appar till App Service från Visual Studio Code](/azure/python/tutorial-deploy-app-service-on-linux-01).

## <a name="stream-logs"></a>Strömningsloggar

Du kan komma åt konsol loggarna som genereras inifrån appen och den behållare där den körs. Loggar innehåller alla utdata som genereras med hjälp av `print` instruktioner.

Kör följande kommando för att strömma loggar:

```azurecli
az webapp log tail
```

Uppdatera appen i webbläsaren för att generera konsol loggar som innehåller meddelanden som beskriver HTTP-begäranden till appen. Om inga utdata visas omedelbart, försök igen om 30 sekunder.

Du kan också granska loggfilerna från webbläsaren på `https://<app-name>.scm.azurewebsites.net/api/logs/docker` .

Om du vill stoppa logg strömningen när som helst, skriver du **CTRL** + **C**.

## <a name="manage-the-azure-app"></a>Hantera Azure-appen

Gå till <a href="https://portal.azure.com" target="_blank">Azure-portalen</a> för att hantera den app som du skapade. Sök efter och välj **app Services**.

![Navigera till App Services i Azure Portal](./media/quickstart-python/navigate-to-app-services-in-the-azure-portal.png)

Välj namnet på din Azure-App.

![Navigera till python-appen i App Services i Azure Portal](./media/quickstart-python/navigate-to-app-in-app-services-in-the-azure-portal.png)

När du väljer appen öppnas sidan **Översikt** där du kan utföra grundläggande hanterings åtgärder som att bläddra, stoppa, starta, starta om och ta bort.

![Hantera din python-app på sidan Översikt i Azure Portal](./media/quickstart-python/manage-an-app-in-app-services-in-the-azure-portal.png)

App Service-menyn innehåller olika sidor för att konfigurera din app.

## <a name="clean-up-resources"></a>Rensa resurser

I de föregående stegen skapade du Azure-resurser i en resursgrupp. Resurs gruppen har ett namn som "appsvc_rg_Linux_CentralUS" beroende på din plats. Om du använder en annan App Service SKU än den kostnads fria F1-nivån medför dessa resurser kontinuerliga kostnader (se [App Service prissättning](https://azure.microsoft.com/pricing/details/app-service/linux/)).

Om du inte tror att du behöver dessa resurser i framtiden tar du bort resurs gruppen genom att köra följande kommando:

```azurecli
az group delete
```

Kommandot använder resurs grupps namnet cachelagrat i *. Azure/config-* filen.

Det kan ta en minut att slutföra kommandot.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Självstudie: python (django)-webbapp med PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Lägga till användar inloggning till en python-webbapp](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Konfigurera python-app](configure-language-python.md)

> [!div class="nextstepaction"]
> [Självstudie: köra python-app i en anpassad behållare](tutorial-custom-container.md)
