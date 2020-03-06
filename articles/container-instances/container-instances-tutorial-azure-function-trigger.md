---
title: Självstudie – Utlös container grupp efter Azure Function
description: Skapa en HTTP-utlöst, Server lös PowerShell-funktion för att automatisera skapandet av Azure Container instances
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 021ccbbd42dea64d45d4129d70fff5148a1759fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78331033"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Självstudie: Använd en HTTP-utlöst Azure-funktion för att skapa en behållar grupp

[Azure Functions](../azure-functions/functions-overview.md) är en server lös beräknings tjänst som kan köra skript eller kod som svar på en rad olika händelser, till exempel en http-begäran, en timer eller ett meddelande i en Azure Storage kö.

I den här självstudien skapar du en Azure-funktion som tar en HTTP-förfrågan och utlöser distributionen av en [behållar grupp](container-instances-container-groups.md). Det här exemplet visar grunderna i hur du använder Azure Functions för att automatiskt skapa resurser i Azure Container Instances. Ändra eller utöka exemplet för mer komplexa scenarier eller andra händelse utlösare. 

Lär dig att:

> [!div class="checklist"]
> * Använd Visual Studio Code med [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för att skapa en grundläggande http-utlöst PowerShell-funktion.
> * Aktivera en identitet i Function-appen och ge den behörighet att skapa Azure-resurser.
> * Ändra och publicera om PowerShell-funktionen för att automatisera distributionen av en container grupp med en behållare.
> * Verifiera den HTTP-utlösta distributionen av containern.

> [!IMPORTANT]
> PowerShell för Azure Functions är för närvarande en för hands version. Förhandsversioner görs tillgängliga för dig under förutsättning att du godkänner [kompletterande användningsvillkor][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Förutsättningar

Se [skapa din första funktion i Azure](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) för krav för att installera och använda Visual Studio Code med Azure Functions i ditt operativ system.

Vissa steg i den här artikeln använder Azure CLI. Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra de här stegen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Skapa en grundläggande PowerShell-funktion

Följ stegen i [skapa din första PowerShell-funktion i Azure](../azure-functions/functions-create-first-function-powershell.md) för att skapa en PowerShell-funktion med hjälp av http-utlösaren. Använd standard namnet **HttpTrigger**i Azure function. Som du ser i snabb starten, testa funktionen lokalt och publicera projektet till en Function-app i Azure. Det här exemplet är en grundläggande HTTP-utlöst funktion som returnerar en text sträng. I senare steg i den här artikeln ändrar du funktionen för att skapa en behållar grupp.

I den här artikeln förutsätter vi att du publicerar projektet med namnet *myfunctionapp*i en Azure-resurs grupp automatiskt med namnet enligt namnet på Function-appen (även *myfunctionapp*). Ersätt namnet på din unika Function-app och resurs grupps namnet i senare steg.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Aktivera en Azure-hanterad identitet i Function-appen

Aktivera nu en systemtilldelad [hanterad identitet](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) i din Function-app. PowerShell-värden som kör appen kan autentiseras automatiskt med den här identiteten, vilket gör att funktioner kan vidta åtgärder på Azure-tjänster som identiteten har beviljats åtkomst till. I den här självstudien beviljar du hanterade identitets behörigheter för att skapa resurser i Function-appens resurs grupp. 

Använd först kommandot [AZ Group show][az-group-show] för att hämta ID: t för Function-appens resurs grupp och lagra den i en miljö variabel. I det här exemplet förutsätts att du kör kommandot i ett bash-gränssnitt.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Kör [AZ functionapp Identity app tilldela][az-functionapp-identity-app-assign] för att tilldela en lokal identitet till Function-appen och tilldela en deltagar roll till resurs gruppen. Med den här rollen kan identiteten skapa ytterligare resurser, till exempel behållar grupper i resurs gruppen.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Ändra funktionen HttpTrigger

Ändra PowerShell-koden för funktionen **HttpTrigger** för att skapa en behållar grupp. Leta upp följande kodblock i fil `run.ps1` för funktionen. Den här koden visar ett namn värde, om ett sådant skickas som en frågesträng i funktions webb adressen:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Ersätt den här koden med följande exempel block. Om ett namn-värde skickas i frågesträngen används det för att namnge och skapa en behållar grupp med cmdleten [New-AzContainerGroup][new-azcontainergroup] . Se till att ersätta resurs grupps namnet *myfunctionapp* med namnet på resurs gruppen för din Function-app:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

I det här exemplet skapas en behållar grupp som består av en enda behållar instans som kör `alpine` avbildningen. Behållaren kör ett enda `echo`-kommando och avslutas sedan. I ett verkligt exempel kan du utlösa skapandet av en eller flera behållar grupper för att köra ett batch-jobb.
 
## <a name="test-function-app-locally"></a>Testa Function-appen lokalt

Se till att funktionen körs korrekt lokalt innan du publicerar om Function-Appaketet till Azure. Som det visas i [PowerShell-snabb](../azure-functions/functions-create-first-function-powershell.md)starten infogar du en lokal Bryt punkt i PowerShell-skriptet och ett `Wait-Debugger` anrop ovanför det. Fel söknings anvisningar finns i [Felsöka PowerShell Azure Functions lokalt](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Publicera Azure Function-appen igen

När du har kontrollerat att funktionen fungerar korrekt på den lokala datorn är det dags att publicera projektet på den befintliga Function-appen i Azure.

> [!NOTE]
> Kom ihåg att ta bort alla anrop till `Wait-Debugger` innan du publicerar dina funktioner till Azure.

1. Öppna kommando-paletten i Visual Studio Code. Sök efter och välj `Azure Functions: Deploy to function app...`.
1. Välj den aktuella arbetsmappen till zip och distribuera.
1. Välj prenumerationen och sedan namnet på den befintliga Function-appen (*myfunctionapp*). Bekräfta att du vill skriva över den tidigare distributionen.

Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har uppdaterat.

## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

När distributionen har slutförts hämtar du funktions-URL: en. Använd till exempel avsnittet **Azure: Functions** i Visual Studio Code för att kopiera **HttpTrigger** -funktionens URL eller hämta funktions webb adressen i [Azure Portal](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Funktions webb adressen innehåller en unik kod och har formatet:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Kör funktion utan att skicka ett namn

Det första testet är att köra kommandot `curl` och skicka funktions webb adressen utan att lägga till en `name` frågesträng. Se till att inkludera funktionens unika kod.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

Funktionen returnerar status koden 400 och text `Please pass a name on the query string or in the request body`:

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Kör funktionen och skicka namnet på en behållar grupp

Kör nu kommandot `curl` genom att lägga till namnet på en behållar grupp (*mycontainergroup*) som en frågesträng `&name=mycontainergroup`:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

Funktionen returnerar status koden 200 och utlöser skapandet av behållar gruppen:

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

Kontrol lera att behållaren kördes med [AZ container logs][az-container-logs] -kommandot:

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Exempel på utdata:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av resurserna som du skapade i den här självstudien kan du köra kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen och alla resurser den innehåller. Det här kommandot tar bort containerregistret som du skapade, den container som körs och alla relaterade resurser.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en Azure-funktion som tar en HTTP-förfrågan och utlöser distributionen av en behållar grupp. Du har lärt dig att:

> [!div class="checklist"]
> * Använd Visual Studio Code med Azure Functions-tillägget för att skapa en grundläggande HTTP-utlöst PowerShell-funktion.
> * Aktivera en identitet i Function-appen och ge den behörighet att skapa Azure-resurser.
> * Ändra PowerShell-funktions koden för att automatisera distribution av en container grupp med en behållare.
> * Verifiera den HTTP-utlösta distributionen av containern.

Ett detaljerat exempel för att starta och övervaka ett behållar jobb finns i avsnittet om [händelse drivna serverbaserade behållare för blogg inlägg med PowerShell Azure Functions och Azure Container instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) och tillhör ande [kod exempel](https://github.com/anthonychu/functions-powershell-run-aci).

I [Azure Functions-dokumentationen](/azure/azure-functions/) finns detaljerad information om hur du skapar Azure Functions och publicerar ett Functions-projekt. 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
