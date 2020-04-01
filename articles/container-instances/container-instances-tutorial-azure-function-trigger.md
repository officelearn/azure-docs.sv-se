---
title: Självstudiekurs - Utlösarbehållare grupp av Azure-funktionen
description: Skapa en HTTP-utlöst, serverlös PowerShell-funktion för att automatisera skapandet av Azure-behållarinstanser
ms.topic: tutorial
ms.date: 09/20/2019
ms.custom: ''
ms.openlocfilehash: 9dbb22a2449e4c41bff802ab827da4489fc7ffeb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78331033"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>Självstudiekurs: Använda en HTTP-utlöst Azure-funktion för att skapa en behållargrupp

[Azure Functions](../azure-functions/functions-overview.md) är en serverlös beräkningstjänst som kan köra skript eller kod som svar på en mängd olika händelser, till exempel en HTTP-begäran, en timer eller ett meddelande i en Azure Storage-kö.

I den här självstudien skapar du en Azure-funktion som tar en HTTP-begäran och utlöser distribution av en [behållargrupp](container-instances-container-groups.md). I det här exemplet visas grunderna i att använda Azure-funktioner för att automatiskt skapa resurser i Azure Container Instances. Ändra eller utöka exemplet för mer komplexa scenarier eller andra händelseutlösare. 

Lär dig att:

> [!div class="checklist"]
> * Använd Visual Studio-kod med [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för att skapa en grundläggande HTTP-utlöst PowerShell-funktion.
> * Aktivera en identitet i funktionsappen och ge den behörighet att skapa Azure-resurser.
> * Ändra och publicera om PowerShell-funktionen för att automatisera distributionen av en containergrupp med en behållare.
> * Verifiera den HTTP-utlösta distributionen av behållaren.

> [!IMPORTANT]
> PowerShell för Azure-funktioner är för närvarande i förhandsversion. Förhandsversioner är tillgängliga för dig under förutsättning att du godkänner de [kompletterande användningsvillkoren][terms-of-use]. Vissa aspekter av funktionen kan ändras innan den är allmänt tillgänglig (GA).

## <a name="prerequisites"></a>Krav

Se [Skapa din första funktion i Azure](/azure/azure-functions/functions-create-first-function-vs-code?pivots=programming-language-powershell#configure-your-environment) för förutsättningar att installera och använda Visual Studio-kod med Azure-funktioner på ditt operativsystem.

Vissa steg i den här artikeln använder Azure CLI. Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att slutföra dessa steg. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="create-a-basic-powershell-function"></a>Skapa en grundläggande PowerShell-funktion

Följ stegen i [Skapa din första PowerShell-funktion i Azure](../azure-functions/functions-create-first-function-powershell.md) för att skapa en PowerShell-funktion med hjälp av HTTP-utlösarmallen. Använd standardnamnet för Azure-funktionen **HttpTrigger**. Som visas i snabbstarten testar du funktionen lokalt och publicerar projektet till en funktionsapp i Azure. Det här exemplet är en grundläggande HTTP-utlöst funktion som returnerar en textsträng. I senare steg i den här artikeln ändrar du funktionen för att skapa en behållargrupp.

Den här artikeln förutsätter att du publicerar projektet med namnet *myfunctionapp*, i en Azure-resursgrupp som automatiskt namnges enligt funktionsappnamnet (även *myfunctionapp*). Ersätt ditt unika funktionsappnamn och resursgruppsnamn i senare steg.

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>Aktivera en Azure-hanterad identitet i funktionsappen

Aktivera nu en systemtilldelad [hanterad identitet](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) i funktionsappen. PowerShell-värden som kör appen kan automatiskt autentisera med den här identiteten, vilket gör att funktioner kan vidta åtgärder för Azure-tjänster som identiteten har beviljats åtkomst till. I den här självstudien beviljar du de hanterade identitetsbehörigheterna för att skapa resurser i funktionsappens resursgrupp. 

Använd först kommandot [az group show][az-group-show] för att hämta ID:t för funktionsappens resursgrupp och lagra det i en miljövariabel. Det här exemplet förutsätter att du kör kommandot i ett Bash-skal.

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

Kör [az functionapp identity app tilldela][az-functionapp-identity-app-assign] att tilldela en lokal identitet till funktionsappen och tilldela en deltagarroll till resursgruppen. Med den här rollen kan identiteten skapa ytterligare resurser, till exempel behållargrupper i resursgruppen.

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>Ändra funktionen HttpTrigger

Ändra PowerShell-koden för **funktionen HttpTrigger** för att skapa en behållargrupp. Leta `run.ps1` reda på följande kodblock i filen för funktionen. Den här koden visar ett namnvärde om ett skickas som en frågesträng i funktions-URL:en:

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

Ersätt den här koden med följande exempelblock. Här, om ett namnvärde skickas i frågesträngen, används det för att namnge och skapa en behållargrupp med cmdleten [New-AzContainerGroup.][new-azcontainergroup] Se till att ersätta resursgruppnamnet *myfunctionapp* med namnet på resursgruppen för funktionsappen:

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

I det här exemplet skapas en behållargrupp som består av en enda behållarinstans som kör avbildningen. `alpine` Behållaren kör `echo` ett enda kommando och avslutas sedan. I ett verkligt exempel kan du utlösa skapandet av en eller flera behållargrupper för att köra ett batchjobb.
 
## <a name="test-function-app-locally"></a>Testa funktionsapp lokalt

Kontrollera att funktionen körs korrekt lokalt innan du publicerar om funktionsappprojektet till Azure. Som visas i [Snabbstarten i PowerShell](../azure-functions/functions-create-first-function-powershell.md)infogar du en `Wait-Debugger` lokal brytpunkt i PowerShell-skriptet och ett anrop ovanför det. Felsökningsvägledning finns i [Felsökning av PowerShell Azure Functions lokalt](../azure-functions/functions-debug-powershell-local.md).


## <a name="republish-azure-function-app"></a>Publicera om Azure-funktionsapp

När du har verifierat att funktionen körs korrekt på din lokala dator är det dags att publicera om projektet till den befintliga funktionsappen i Azure.

> [!NOTE]
> Kom ihåg att `Wait-Debugger` ta bort alla samtal innan du publicerar dina funktioner i Azure.

1. Öppna kommandopaletten i Visual Studio-kod. Sök efter `Azure Functions: Deploy to function app...`och välj .
1. Välj den aktuella arbetsmappen som ska zipas och distribueras.
1. Välj prenumerationen och sedan namnet på den befintliga funktionsappen *(myfunctionapp*). Bekräfta att du vill skriva över den tidigare distributionen.

Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. Välj **Visa utdata** i det här meddelandet om du vill visa skapande- och distributionsresultaten, inklusive De Azure-resurser som du har uppdaterat.

## <a name="run-the-function-in-azure"></a>Kör funktionen i Azure

När distributionen har slutförts får du funktions-URL:en. Använd till exempel området **Azure: Functions** i Visual Studio-kod för att kopiera httptrigger-funktions-URL:en eller hämta funktions-URL:en i **HttpTrigger** [Azure-portalen](../azure-functions/functions-create-first-azure-function.md#test-the-function).

Funktions-URL:en innehåller en unik kod och är av formuläret:

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>Kör funktion utan att skicka ett namn

Som ett första test `curl` kör du kommandot och skickar `name` funktions-URL:en utan att lägga till en frågesträng. Se till att inkludera funktionens unika kod.

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

Funktionen returnerar statuskod 400 `Please pass a name on the query string or in the request body`och texten:

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

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>Kör funktion och skicka namnet på en behållargrupp

Kör nu `curl` kommandot genom att lägga till namnet på en behållargrupp `&name=mycontainergroup`*(mincontainergroup)* som en frågesträng:

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

Funktionen returnerar statuskod 200 och utlöser skapandet av behållargruppen:

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

Kontrollera att behållaren kördes med kommandot [az container logs:][az-container-logs]

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

Exempel på utdata:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte längre behöver någon av de resurser som du har skapat i den här självstudien kan du köra kommandot [az group delete][az-group-delete] för att ta bort resursgruppen och alla resurser som den innehåller. Det här kommandot tar bort containerregistret som du skapade, den container som körs och alla relaterade resurser.

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien skapade du en Azure-funktion som tar en HTTP-begäran och utlöser distribution av en behållargrupp. Du har lärt dig att:

> [!div class="checklist"]
> * Använd Visual Studio-kod med Azure Functions-tillägget för att skapa en grundläggande HTTP-utlöst PowerShell-funktion.
> * Aktivera en identitet i funktionsappen och ge den behörighet att skapa Azure-resurser.
> * Ändra PowerShell-funktionskoden för att automatisera distributionen av en containergrupp med en behållare.
> * Verifiera den HTTP-utlösta distributionen av behållaren.

Ett detaljerat exempel för att starta och övervaka ett behållarjobb finns i blogginlägget [Event-Driven Serverless Containers med PowerShell Azure Functions och Azure Container Instances](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b) och medföljande [kodexempel](https://github.com/anthonychu/functions-powershell-run-aci).

Mer information om hur du skapar [Azure-funktioner](/azure/azure-functions/) och publicerar ett funktionsprojekt finns i dokumentationen till Azure Functions. 

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
