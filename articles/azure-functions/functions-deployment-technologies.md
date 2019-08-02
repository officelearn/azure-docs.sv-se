---
title: Distributions tekniker i Azure Functions | Microsoft Docs
description: Lär dig hur du kan distribuera kod till Azure Functions på olika sätt.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 7f931a72eab534bc2856e9e545b684d2b8ae7a60
ms.sourcegitcommit: a874064e903f845d755abffdb5eac4868b390de7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2019
ms.locfileid: "68444030"
---
# <a name="deployment-technologies-in-azure-functions"></a>Distributions tekniker i Azure Functions

Du kan använda flera olika tekniker för att distribuera din Azure Functions projekt kod till Azure. Den här artikeln innehåller en fullständig lista över dessa tekniker, beskriver vilka tekniker som är tillgängliga för vilka varianter-funktioner, förklarar vad som händer när du använder varje metod och ger rekommendationer för den bästa metoden att använda i olika scenarier . De olika verktyg som har stöd för distribution till Azure Functions är justerade till rätt teknik baserat på deras kontext.

## <a name="deployment-technology-availability"></a>Tillgänglighet för distributions teknik

Azure Functions stöder lokal utveckling på över plattformar och är värd för Windows och Linux. För närvarande är tre värd planer tillgängliga:

+ [Bruk](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedikerad (App Service)](functions-scale.md#app-service-plan)

Varje plan har olika beteenden. Alla distributions tekniker är inte tillgängliga för varje smak av Azure Functions. Följande diagram visar vilka distributions tekniker som stöds för varje kombination av operativ system och värd plan:

| Distributions teknik | Windows-förbrukning | Windows Premium (för hands version) | Windows-dedikerad  | Linux-förbrukning (för hands version) | Linux-dedikerad |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| URL för externt paket<sup>1</sup> |✔|✔|✔|✔|✔|
| Zip-distribution |✔|✔|✔| |✔|
| Dockercontainer | | | | |✔|
| Webb distribution |✔|✔|✔| | |
| Käll kontroll |✔|✔|✔| |✔|
| Lokal git<sup>1</sup> |✔|✔|✔| |✔|
| Molnbaserad synkronisering<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Portal redigering |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> distributions teknik som kräver [synkronisering av manuella](#trigger-syncing)utlösare.  
<sup>2</sup> Portal redigering är bara aktive rad för http-och timer-utlösare för funktioner i Linux med hjälp av Premium och dedikerade planer

## <a name="key-concepts"></a>Viktiga begrepp

Vissa viktiga begrepp är viktiga för att förstå hur distributioner fungerar i Azure Functions.

### <a name="trigger-syncing"></a>Utlös synkronisering

När du ändrar någon av utlösarna måste funktions infrastrukturen vara medveten om ändringarna. Synkronisering sker automatiskt för många distributions tekniker. I vissa fall måste du dock synkronisera dina utlösare manuellt. När du distribuerar dina uppdateringar genom att referera till en extern paket-URL, lokal git, molnbaserad synkronisering eller FTP, måste du synkronisera utlösarna manuellt. Du kan synkronisera utlösare på ett av tre sätt:

* Starta om din Function-app i Azure Portal
* Skicka en http post-begäran `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` till att använda [huvud nyckeln](functions-bindings-http-webhook.md#authorization-keys).
* Skicka en HTTP POST-begäran `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`till. Ersätt plats hållarna med ditt prenumerations-ID, resurs gruppens namn och namnet på din Function-app.

## <a name="deployment-technology-details"></a>Information om distributions teknik 

Följande distributions metoder är tillgängliga i Azure Functions.

### <a name="external-package-url"></a>URL för externt paket

Du kan använda en extern paket-URL för att referera till en fjärrfil-fil (zip) som innehåller din Function-app. Filen hämtas från den angivna URL: en och appen körs i läget [Kör från paket](run-functions-from-deployment-package.md) .

>__Så här använder du den:__ Lägg `WEBSITE_RUN_FROM_PACKAGE` till i dina program inställningar. Värdet för den här inställningen ska vara en URL (platsen för den angivna paket filen som du vill köra). Du kan lägga till inställningar antingen [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) eller med [hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Om du använder Azure Blob Storage använder du en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att ge funktioner åtkomst till paketet. När programmet startas om, hämtas en kopia av innehållet. Din referens måste vara giltig för programmets livs längd.

>__När du ska använda den:__ URL för externt paket är den enda distributions metod som stöds för Azure Functions som körs på Linux i förbruknings planen (för hands version). När du uppdaterar paket filen som en Function-app refererar till måste du [manuellt synkronisera](#trigger-syncing) utlösare för att meddela Azure att ditt program har ändrats.

### <a name="zip-deploy"></a>Zip-distribution

Använd zip Deploy för att skicka en. zip-fil som innehåller din Function-app till Azure. Du kan också ange att appen ska starta i läget [Kör från paket](run-functions-from-deployment-package.md) .

>__Så här använder du den:__ Distribuera med hjälp av ditt favorit klient verktyg: [Vs Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure)eller [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Om du vill distribuera en. zip-fil manuellt till Function-appen följer du anvisningarna i [distribuera från en. zip-fil eller URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>När du distribuerar med hjälp av zip-distribution kan du ange att appen ska köras [från paket](run-functions-from-deployment-package.md) läge. Ange värdet för `WEBSITE_RUN_FROM_PACKAGE` program inställningen till `1`om du vill ange kör från paket läge. Vi rekommenderar zip-distribution. Det ger snabbare inläsnings tider för dina program och är standard för VS Code, Visual Studio och Azure CLI.

>__När du ska använda den:__ Zip Deploy är den rekommenderade distributions tekniken för funktioner som körs på Windows och Linux i Premium-eller dedikerade planen.

### <a name="docker-container"></a>Dockercontainer

Du kan distribuera en Linux container-avbildning som innehåller din Function-app.

>__Så här använder du den:__ Skapa en Linux Function-app i Premium-eller dedikerade planen och ange vilken behållar avbildning som ska köras från. Du kan göra detta på två sätt:
>
>* Skapa en Linux Function-app i en Azure App Service plan i Azure Portal. För **publicera**väljer du **Docker-avbildning**och konfigurerar sedan behållaren. Ange den plats där avbildningen finns.
>* Skapa en Linux Function-app på en App Service plan med hjälp av Azure CLI. Mer information finns i [skapa en funktion i Linux med hjälp av en anpassad avbildning](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Om du vill distribuera till en befintlig app med hjälp av en anpassad [](functions-run-local.md)behållare, använder du [`func deploy`](functions-run-local.md#publish) i Azure Functions Core tools kommandot.

>__När du ska använda den:__ Använd alternativet Docker-behållare när du behöver mer kontroll över Linux-miljön där funktions programmet körs. Den här distributions mekanismen är bara tillgänglig för funktioner som körs på Linux i en App Service plan.

### <a name="web-deploy-msdeploy"></a>Webb distribution (MSDeploy)

Webb distributions paket och distribuerar dina Windows-program till alla IIS-servrar, inklusive dina funktions program som körs på Windows i Azure.

>__Så här använder du den:__ Använd [Visual Studio-verktyg för Azure Functions](functions-create-your-first-function-visual-studio.md). Avmarkera kryss rutan **Kör från paket fil (rekommenderas)** .
>
>Du kan också hämta [webb distribution 3,6](https://www.iis.net/downloads/microsoft/web-deploy) och ringa `MSDeploy.exe` direkt.

>__När du ska använda den:__ Webb distribution stöds och har inga problem, men den rekommenderade mekanismen är [zip-distribution med kör från-paketet aktiverat](#zip-deploy). Mer information finns i [utvecklings guiden för Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Käll kontroll

Använd käll kontroll för att ansluta din Function-app till en git-lagringsplats. En uppdatering av kod i den här databasen utlöser distributionen. Mer information finns i wiki- [kudu](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Så här använder du den:__ Använd Deployment Center i funktions områden i portalen för att konfigurera publicering från käll kontroll. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

>__När du ska använda den:__ Att använda käll kontroll är det bästa sättet för team som samarbetar på sina funktions appar. Käll kontroll är ett lämpligt distributions alternativ som möjliggör mer avancerade distributions pipeliner.

### <a name="local-git"></a>Lokal Git

Du kan använda lokal git för att skicka kod från den lokala datorn till Azure Functions med hjälp av git.

>__Så här använder du den:__ Följ instruktionerna i [lokal Git-distribution till Azure App Service](../app-service/deploy-local-git.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi att du använder en annan distributions metod. När du publicerar från lokal git måste du [Synkronisera utlösare manuellt](#trigger-syncing).

### <a name="cloud-sync"></a>Molnbaserad synkronisering

Använd Cloud Sync för att synkronisera innehållet från Dropbox och OneDrive till Azure Functions.

>__Så här använder du den:__ Följ anvisningarna i [Synkronisera innehåll från en molnbaserad mapp](../app-service/deploy-content-sync.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributions metoder. När du publicerar med hjälp av molnbaserad synkronisering måste du [Synkronisera utlösare manuellt](#trigger-syncing).

### <a name="ftp"></a>FTP

Du kan använda FTP för att överföra filer direkt till Azure Functions.

>__Så här använder du den:__ Följ anvisningarna i [distribuera innehåll med FTP/s](../app-service/deploy-ftp.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributions metoder. När du publicerar med hjälp av FTP måste du [Synkronisera utlösare manuellt](#trigger-syncing).

### <a name="portal-editing"></a>Portal redigering

I det portalbaserade redigerings programmet kan du redigera filerna som finns i din Function-app direkt (distribueras i princip varje gång du sparar ändringarna).

>__Så här använder du den:__ För att kunna redigera dina funktioner i Azure Portal måste du ha [skapat dina funktioner i portalen](functions-create-first-azure-function.md). Om du vill bevara en enda källa till sanningen kan du använda en annan distributions metod för att göra din funktion skrivskyddad och förhindra fortsatt Portal redigering. Om du vill gå tillbaka till ett tillstånd där du kan redigera dina filer i Azure Portal kan du manuellt växla redigerings läget till `Read/Write` och ta bort eventuella distributions program inställningar (t. ex `WEBSITE_RUN_FROM_PACKAGE`.). 

>__När du ska använda den:__ Portalen är ett bra sätt att komma igång med Azure Functions. För mer intensiv utvecklings arbete rekommenderar vi att du använder något av följande klient verktyg:
>
>* [Visual Studio Code](functions-create-first-function-vs-code.md)
>* [Azure Functions Core Tools (kommando rad)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

I följande tabell visas de operativ system och språk som stöder Portal redigering:

| | Windows-förbrukning | Windows Premium (för hands version) | Windows-dedikerad | Linux-förbrukning (för hands version) | Linux Premium (för hands version)| Linux-dedikerad |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|:---------------:|
| C# | | | | | |
| C#Över |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (förhandsversion) | | | | | | |
| PowerShell (för hands version) |✔|✔|✔| | | |
| TypeScript (Node. js) | | | | | | |

<sup>*</sup>Portal redigering är bara aktive rad för HTTP-och timer-utlösare för funktioner i Linux med hjälp av Premium och dedikerade planer.

## <a name="deployment-slots"></a>Distributionsplatser

När du distribuerar din Function-app till Azure kan du distribuera till en separat distributions plats i stället för att distribuera direkt till produktion. Mer information om distributions platser finns i [Azure App Service platser](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Support nivåer för distributions platser

Det finns två nivåer av stöd för distributions platser:

* **Allmän tillgänglighet (ga)** : Fullständigt stöd och godkänts för produktions användning.
* För **hands version**: Stöds inte ännu, men förväntas komma att uppnå GA-status i framtiden.

| Operativ system/värd plan | Support nivå |
| --------------- | ------ |
| Windows-förbrukning | Förhandsversion |
| Windows Premium (för hands version) | Förhandsversion |
| Windows-dedikerad | Allmän tillgänglighet |
| Linux-förbrukning | Stöds inte |
| Linux Premium (för hands version) | Förhandsversion |
| Linux-dedikerad | Allmän tillgänglighet |

## <a name="next-steps"></a>Nästa steg

Läs de här artiklarna om du vill veta mer om hur du distribuerar dina funktions appar: 

+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)
+ [Kontinuerlig leverans med hjälp av Azure-DevOps](functions-how-to-azure-devops.md)
+ [Zip-distributioner för Azure Functions](deployment-zip-push.md)
+ [Köra Azure Functions från en paketfil](run-functions-from-deployment-package.md)
+ [Automatisera resurs distributionen för din Function-app i Azure Functions](functions-infrastructure-as-code.md)
