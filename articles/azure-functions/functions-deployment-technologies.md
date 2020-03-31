---
title: Distributionstekniker i Azure-funktioner
description: Lär dig olika sätt att distribuera kod till Azure Functions.
author: georgewallace
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: gwallace
ms.openlocfilehash: 43352117d149abbe41ba7bf49a1ffb68e46d2707
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277133"
---
# <a name="deployment-technologies-in-azure-functions"></a>Distributionstekniker i Azure-funktioner

Du kan använda några olika tekniker för att distribuera din Azure Functions-projektkod till Azure. Den här artikeln innehåller en uttömmande lista över dessa tekniker, beskriver vilka tekniker som är tillgängliga för vilka smaker av funktioner, förklarar vad som händer när du använder varje metod, och ger rekommendationer för den bästa metoden att använda i olika scenarier . De olika verktygen som stöder distribution till Azure-funktioner är anpassade till rätt teknik baserat på deras kontext. I allmänhet är zip-distribution den rekommenderade distributionstekniken för Azure Functions.

## <a name="deployment-technology-availability"></a>Tillgänglighet för driftsättningsteknik

Azure Functions stöder lokal utveckling och värdskap över flera plattformar på Windows och Linux. För närvarande finns tre hosting planer tillgängliga:

+ [Förbrukning](functions-scale.md#consumption-plan)
+ [Premium](functions-scale.md#premium-plan)
+ [Dedikerad (apptjänst)](functions-scale.md#app-service-plan)

Varje plan har olika beteenden. Alla distributionstekniker är inte tillgängliga för varje smak av Azure-funktioner. Följande diagram visar vilka distributionstekniker som stöds för varje kombination av operativsystem och värdplan:

| Driftsättningsteknik | Windows-förbrukning | Windows Premium | Windows Dedikerad  | Linux-konsumtion | Linux Premium | Linux Dedikerad |
|-----------------------|:-------------------:|:-------------------------:|:------------------:|:---------------------------:|:-------------:|:---------------:|
| URL för externt paket<sup>1</sup> |✔|✔|✔|✔|✔|✔|
| Zip-distribution |✔|✔|✔|✔|✔|✔|
| Docker-behållare | | | | |✔|✔|
| Webbdistribution |✔|✔|✔| | | |
| Källkontroll |✔|✔|✔| |✔|✔|
| Lokala Git<sup>1</sup> |✔|✔|✔| |✔|✔|
| Molnsynkronisering<sup>1</sup> |✔|✔|✔| |✔|✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|✔|
| Redigering av portaler |✔|✔|✔| |✔<sup>2</sup>|✔<sup>2</sup>|

<sup>1</sup> Distributionsteknik som kräver [manuell utlösarsynkronisering](#trigger-syncing).  
<sup>2</sup> Portal redigering är aktiverad endast för HTTP och Timer triggers för funktioner på Linux med Hjälp av Premium och dedikerade planer.

## <a name="key-concepts"></a>Viktiga begrepp

Vissa viktiga begrepp är avgörande för att förstå hur distributioner fungerar i Azure Functions.

### <a name="trigger-syncing"></a>Synkronisering av utlösare

När du ändrar någon av dina utlösare måste infrastrukturen Funktioner vara medveten om ändringarna. Synkronisering sker automatiskt för många distributionstekniker. I vissa fall måste du dock synkronisera dina utlösare manuellt. När du distribuerar dina uppdateringar genom att referera till en extern paket-URL, lokal Git, molnsynkronisering eller FTP måste du synkronisera dina utlösare manuellt. Du kan synkronisera utlösare på ett av tre sätt:

* Starta om funktionsappen i Azure-portalen
* Skicka en HTTP `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` POST-begäran till [huvudnyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys).
* Skicka en HTTP `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`POST-begäran till . Ersätt platshållarna med ditt prenumerations-ID, resursgruppsnamnet och namnet på funktionsappen.

### <a name="remote-build"></a>Fjärrbygge

Azure Functions kan automatiskt utföra versioner av koden som tas emot efter zip-distributioner. Dessa versioner beter sig lite annorlunda beroende på om din app körs på Windows eller Linux. Fjärrbyggen utförs inte när en app tidigare har ställts in för att köras i läget [Kör från paket.](run-functions-from-deployment-package.md) Om du vill veta hur du använder fjärrbygge navigerar du till [zip-distribution](#zip-deploy).

> [!NOTE]
> Om du har problem med fjärrbygge kan det bero på att appen skapades innan funktionen gjordes tillgänglig (1 augusti 2019). Prova att skapa en ny `az functionapp update -g <RESOURCE_GROUP_NAME> -n <APP_NAME>` funktionsapp eller köra för att uppdatera din funktionsapp. Det här kommandot kan ta två försök att lyckas.

#### <a name="remote-build-on-windows"></a>Fjärrversion på Windows

Alla funktionsappar som körs på Windows har en liten hanteringsapp, SCM-webbplatsen (eller [Kudu).](https://github.com/projectkudu/kudu) Den här webbplatsen hanterar mycket av distributions- och bygglogiken för Azure Functions.

När en app distribueras till Windows körs `dotnet restore` språkspecifika kommandon, `npm install` till exempel (C#) eller (JavaScript).

#### <a name="remote-build-on-linux"></a>Fjärrbygge på Linux

För att fjärrbyggan ska kunna byggas på Linux måste följande [programinställningar](functions-how-to-use-azure-function-app-settings.md#settings) ställas in:

* `ENABLE_ORYX_BUILD=true`
* `SCM_DO_BUILD_DURING_DEPLOYMENT=true`

Som standard utför både [Azure Functions Core Tools](functions-run-local.md) och Azure Functions Extension for Visual Studio [Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure) fjärrbyggen när de distribueras till Linux. På grund av detta skapar båda verktygen automatiskt dessa inställningar för dig i Azure. 

När appar skapas på distans på Linux körs de [från distributionspaketet](run-functions-from-deployment-package.md). 

##### <a name="consumption-plan"></a>Förbrukningsplan

Linux-funktionsappar som körs i förbrukningsplanen har ingen SCM/Kudu-plats, vilket begränsar distributionsalternativen. Funktionsappar på Linux som körs i förbrukningsplanen stöder dock fjärrversioner.

##### <a name="dedicated-and-premium-plans"></a>Särskilda och Premium-abonnemang

Funktionsappar som körs på Linux i [den dedikerade (App Service) planen](functions-scale.md#app-service-plan) och [Premium-planen](functions-scale.md#premium-plan) har också en begränsad SCM / Kudu webbplats.

## <a name="deployment-technology-details"></a>Information om driftsättningsteknik

Följande distributionsmetoder är tillgängliga i Azure Functions.

### <a name="external-package-url"></a>URL för externt paket

Du kan använda en extern paket-URL för att referera till en fjärrpaketfil (.zip) som innehåller din funktionsapp. Filen hämtas från den angivna URL:en och appen körs i läget [Kör från paket.](run-functions-from-deployment-package.md)

>__Så här använder du den:__ Lägg `WEBSITE_RUN_FROM_PACKAGE` till dina programinställningar. Värdet för den här inställningen ska vara en URL (platsen för den specifika paketfil som du vill köra). Du kan lägga till inställningar antingen [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) eller [med hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Om du använder Azure Blob-lagring använder du en privat behållare med en [SIGNATUR för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) för att ge Funktioner åtkomst till paketet. Varje gång programmet startas om hämtas en kopia av innehållet. Din referens måste vara giltig under programmets livstid.

>__När du ska använda den:__ Extern paket-URL är den enda distributionsmetod som stöds för Azure Functions som körs på Linux i förbrukningsplanen, om användaren inte vill att en [fjärrversion](#remote-build) ska ske. När du uppdaterar paketfilen som en funktionsapp refererar till måste du [synkronisera utlösare manuellt](#trigger-syncing) för att berätta för Azure att ditt program har ändrats.

### <a name="zip-deploy"></a>Zip-distribution

Använd zip-distribuera för att skicka en ZIP-fil som innehåller din funktionsapp till Azure. Du kan också ange att appen ska börja [köras från paketet](run-functions-from-deployment-package.md)eller ange att en [fjärrversion](#remote-build) ska ske.

>__Så här använder du den:__ Distribuera med hjälp av ditt favoritklientverktyg: [Visual Studio Code](functions-develop-vs-code.md#publish-to-azure), Visual [Studio](functions-develop-vs.md#publish-to-azure)eller från kommandoraden med hjälp av Azure Functions [Core Tools](functions-run-local.md#project-file-deployment). Som standard använder dessa verktyg zip-distribution och [körs från paketet](run-functions-from-deployment-package.md). Core Tools och Visual Studio-kodtillägget möjliggör båda [fjärrbygge](#remote-build) vid distribution till Linux. Om du vill distribuera en ZIP-fil manuellt till funktionsappen följer du instruktionerna i [Distribuera från en ZIP-fil eller URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

>När du distribuerar med hjälp av zip-distribution kan du ställa in appen så att [den körs från paketet](run-functions-from-deployment-package.md). Om du vill köra `WEBSITE_RUN_FROM_PACKAGE` från paketet `1`anger du programinställningsvärdet till . Vi rekommenderar zip-distribution. Det ger snabbare laddningstider för dina program och det är standardvärdet för VS-kod, Visual Studio och Azure CLI. 

>__När du ska använda den:__ Zip-distribution är den rekommenderade distributionstekniken för Azure Functions.

### <a name="docker-container"></a>Docker-behållare

Du kan distribuera en Linux-behållaravbildning som innehåller din funktionsapp.

>__Så här använder du den:__ Skapa en Linux-funktionsapp i Premium- eller Dedicated-planen och ange vilken behållaravbildning som ska köras från. Du kan göra det här på två sätt:
>
>* Skapa en Linux-funktionsapp på en Azure App Service-plan i Azure-portalen. För **Publicera**väljer du **Docker Image**och konfigurerar sedan behållaren. Ange den plats där bilden finns.
>* Skapa en Linux-funktionsapp på en App Service-plan med hjälp av Azure CLI. Mer information finns i [Skapa en funktion på Linux med hjälp av en anpassad avbildning](functions-create-function-linux-custom-image.md#create-supporting-azure-resources-for-your-function).
>
>Om du vill distribuera till en befintlig app med hjälp [`func deploy`](functions-run-local.md#publish) av en anpassad behållare använder du kommandot i [Azure Functions Core Tools.](functions-run-local.md)

>__När du ska använda den:__ Använd docker-behållaralternativet när du behöver mer kontroll över Linux-miljön där din funktionsapp körs. Den här distributionsmekanismen är endast tillgänglig för funktioner som körs på Linux.

### <a name="web-deploy-msdeploy"></a>Webbdistribution (MSDeploy)

Webb distribuera paket och distribuera dina Windows-program till alla IIS-servrar, inklusive dina funktionsappar som körs på Windows i Azure.

>__Så här använder du den:__ Använd [Visual Studio-verktyg för Azure Functions](functions-create-your-first-function-visual-studio.md). Avmarkera kryssrutan **Kör från paketfil (rekommenderas).**
>
>Du kan också ladda ner Web Deploy `MSDeploy.exe` [3.6](https://www.iis.net/downloads/microsoft/web-deploy) och ringa direkt.

>__När du ska använda den:__ Webb distribuera stöds och har inga problem, men den föredragna mekanismen är [zip distribuera med Kör från paket aktiverat](#zip-deploy). Mer information finns i [utvecklingsguiden för Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Källkontroll

Använd källkontrollen för att ansluta funktionsappen till en Git-databas. En uppdatering av koden i databasen utlöser distributionen. Mer information finns i [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Så här använder du den:__ Använd Distributionscenter i området Funktioner i portalen för att ställa in publicering från källkontroll. Mer information finns i [Kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

>__När du ska använda den:__ Att använda källkontroll är den bästa metoden för team som samarbetar i sina funktionsappar. Källkontroll är ett bra distributionsalternativ som möjliggör mer avancerade distributionspipelor.

### <a name="local-git"></a>Lokal Git

Du kan använda lokal Git för att skicka kod från din lokala dator till Azure Functions med hjälp av Git.

>__Så här använder du den:__ Följ instruktionerna i [lokal Git-distribution till Azure App Service](../app-service/deploy-local-git.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi att du använder en annan distributionsmetod. När du publicerar från lokal Git måste du [synkronisera utlösare manuellt](#trigger-syncing).

### <a name="cloud-sync"></a>Molnsynkronisering

Använd molnsynkronisering för att synkronisera ditt innehåll från Dropbox och OneDrive till Azure Functions.

>__Så här använder du den:__ Följ instruktionerna i [Synkronisera innehåll från en molnmapp](../app-service/deploy-content-sync.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributionsmetoder. När du publicerar med molnsynkronisering måste du [synkronisera utlösare manuellt](#trigger-syncing).

### <a name="ftp"></a>FTP

Du kan använda FTP för att direkt överföra filer till Azure Functions.

>__Så här använder du den:__ Följ instruktionerna i [Distribuera innehåll med hjälp av FTP/s](../app-service/deploy-ftp.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra distributionsmetoder. När du publicerar med FTP måste du [synkronisera utlösare manuellt](#trigger-syncing).

### <a name="portal-editing"></a>Redigering av portaler

I den portalbaserade redigeraren kan du redigera filerna direkt i funktionsappen (distribueras i huvudsak varje gång du sparar ändringarna).

>__Så här använder du den:__ För att kunna redigera dina funktioner i Azure-portalen måste du ha [skapat dina funktioner i portalen](functions-create-first-azure-function.md). Om du vill bevara en enda sanningskälla gör du din funktion skrivskyddad med hjälp av någon annan distributionsmetod och förhindrar fortsatt portalredigering. Om du vill återgå till ett tillstånd där du kan redigera dina filer i `Read/Write` Azure-portalen kan du `WEBSITE_RUN_FROM_PACKAGE`manuellt återställa redigeringsläget till och ta bort alla distributionsrelaterade programinställningar (till exempel ). 

>__När du ska använda den:__ Portalen är ett bra sätt att komma igång med Azure Functions. För ett mer intensivt utvecklingsarbete rekommenderar vi att du använder något av följande klientverktyg:
>
>* [Visual Studio-kod](functions-create-first-function-vs-code.md)
>* [Kärnverktyg för Azure Functions (kommandorad)](functions-run-local.md)
>* [Visual Studio](functions-create-your-first-function-visual-studio.md)

I följande tabell visas de operativsystem och språk som stöder portalredigering:

| | Windows-förbrukning | Windows Premium | Windows Dedikerad | Linux-konsumtion | Linux Premium | Linux Dedikerad |
|-|:-----------------: |:----------------:|:-----------------:|:-----------------:|:-------------:|:---------------:|
| C# | | | | | |
| C# Skript |✔|✔|✔| |✔<sup>\*</sup> |✔<sup>\*</sup>|
| F# | | | | | | |
| Java | | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>\*</sup>|✔<sup>\*</sup>|
| Python (förhandsversion) | | | | | | |
| PowerShell (förhandsversion) |✔|✔|✔| | | |
| TypeScript (nod.js) | | | | | | |

<sup>*</sup>Portalredigering är endast aktiverat för HTTP- och Timer-utlösare för funktioner på Linux med hjälp av Premium- och Dedikerade planer.

## <a name="deployment-slots"></a>Distributionsplatser

När du distribuerar din funktionsapp till Azure kan du distribuera till en separat distributionsplats i stället för direkt till produktion. Mer information om distributionsplatser finns i dokumentationen till [Azure Functions Deployment Slots](../app-service/deploy-staging-slots.md) för mer information.

## <a name="next-steps"></a>Nästa steg

Läs de här artiklarna om du vill veta mer om hur du distribuerar dina funktionsappar: 

+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)
+ [Kontinuerlig leverans med hjälp av Azure DevOps](functions-how-to-azure-devops.md)
+ [Zip-distributioner för Azure-funktioner](deployment-zip-push.md)
+ [Köra dina Azure-funktioner från en paketfil](run-functions-from-deployment-package.md)
+ [Automatisera resursdistribution för din funktionsapp i Azure Functions](functions-infrastructure-as-code.md)
