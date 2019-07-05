---
title: Distributionstekniker i Azure Functions | Microsoft Docs
description: Lär dig och nackdelar med de olika sätt som du kan distribuera kod till Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 118daf02ab59646f2926071763aa4d7e97846e04
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67508234"
---
# <a name="deployment-technologies-in-azure-functions"></a>Distributionstekniker i Azure Functions

Det finns några olika tekniker som du kan använda för att distribuera projektkoden Azure Functions till Azure. Den här artikeln ger en fullständig förteckning av dessa tekniker, meddelar vilka tekniker är tillgängliga för vilka varianter av funktioner, förklarar vad som faktiskt händer när varje metod används och innehåller rekommendationer för den bästa metoden ska användas i olika scenarier. De olika verktyg som har stöd för distribution till Azure Functions är anpassade till rätt teknik baserat på deras kontext.

## <a name="deployment-technology-availability"></a>Teknik distribueringstillgänglighet

> [!IMPORTANT]
> Azure Functions stöder flera lokala plattformsutveckling och värdtjänster på två operativsystem: Windows och Linux. Det finns tre värdplaner för närvarande tillgängligt, var och en med olika beteenden - [förbrukning](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), och [dedikerade (App Service)](functions-scale.md#app-service-plan). Inte alla distributionstekniker är tillgängliga för varje version av Azure Functions.

| Distributionstekniken | Windows-förbrukning | Windows premium (förhandsversion) | Windows dedicated  | Linux-förbrukning (förhandsversion) | Linux dedicated |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| Externt paket URL<sup>1</sup> |✔|✔|✔|✔|✔|
| Distribuera ZIP |✔|✔|✔| |✔|
| Docker-behållare | | | | |✔|
| Webbdistribution |✔|✔|✔| | |
| Källkontroll |✔|✔|✔| |✔|
| Lokal Git<sup>1</sup> |✔|✔|✔| |✔|
| Molnet synkronisering<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| Redigering av portalen |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> distributionsteknik som kräver [manuella utlösaren synkroniserar](#trigger-syncing).
<sup>2</sup> portalen redigerar har aktiverats för endast HTTP och Timer utlösare för funktioner i Linux med dedikerade planera.

## <a name="key-concepts"></a>Viktiga begrepp

Det är viktigt att lära dig några viktiga begrepp som är nödvändiga för att förstå hur distributioner fungerar i Azure Functions innan du fortsätter.

### <a name="trigger-syncing"></a>Utlösaren synkronisering

När du ändrar någon av dina utlösare måste Functions-infrastruktur vara medveten om dessa ändringar. Synkroniseringen sker automatiskt för många distributionstekniker. Men i vissa fall måste du manuellt synkronisera din utlösare. När du distribuerar dina uppdateringar med hjälp av ett externt paket URL, lokal Git, molnsynkronisering eller FTP, måste du vara noga med att manuellt synkronisera din utlösare. Du kan synkronisera utlösare på något av tre sätt:

* Starta om din funktionsapp i Azure portal
* Skicka en HTTP POST-begäran till `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` med hjälp av den [huvudnyckeln](functions-bindings-http-webhook.md#authorization-keys).
* Skicka en HTTP POST-begäran till `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Ersätt platshållarna med ditt prenumerations-ID, resursgruppens namn och namnet på din funktionsapp.

## <a name="deployment-technology-details"></a>Distributionsinformation för teknik  

Dessa följande distributionsmetoder stöds av Azure Functions.

### <a name="external-package-url"></a>Externt paket-URL

Kan du referera till en fjärransluten paketfil (.zip) som innehåller din funktionsapp. Filen laddas ned från den tillhandahållna URL: en och appen körs i [kör från paketet](run-functions-from-deployment-package.md) läge.

>__Hur du använder det:__ Lägg till `WEBSITE_RUN_FROM_PACKAGE` till programinställningarna. Värdet för den här inställningen ska vara en URL - platsen för specifika paketfilen du vill köra. Du kan lägga till inställningar antingen [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) eller [med hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Om du använder Azure blob-lagring, bör du använda en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) att ge funktioner åtkomst till paketet. När som helst programmet startar om den ska hämta en kopia av innehållet, vilket innebär att din referens måste gälla för hela programmets livslängd.

>__När du ska använda den:__ Det här är den enda distributionsmetod som stöds för Azure Functions som körs på Linux i förbrukningsplan (förhandsversion). När du uppdaterar paketfilen refererar till en funktionsapp, måste du [manuellt synkronisera utlösare](#trigger-syncing) som talar om för Azure att ditt program har ändrats.

### <a name="zip-deploy"></a>Distribuera ZIP

Låter dig en zip-fil som innehåller din funktionsapp till Azure. Du kan också du kan också ange om du vill att din app börja [kör från paketet](run-functions-from-deployment-package.md) läge.

>__Hur du använder det:__ Distribuera med hjälp av din favorit klientverktyg - [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), eller [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). För att manuellt distribuera en zip-fil till din funktionsapp, följer du instruktionerna i [distribuera från en zip-fil eller url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>När du distribuerar via zip distribuera användare kan också ange om du vill köra sina från i [kör från paketet](run-functions-from-deployment-package.md) läge genom att ange den `WEBSITE_RUN_FROM_PACKAGE` program inställningens värde som `1`. Det här alternativet rekommenderas och ger snabbare inläsning för dina program. Detta görs som standard för ovanstående klientverktyg.

>__När du ska använda den:__ Det här är rekommenderad distribution-teknik för Azure Functions som körs på Windows och Azure Functions som körs på Linux i dedikerade planen.

### <a name="docker-container"></a>Docker-behållare

Distribuera en Linux-behållaravbildning som innehåller din funktionsapp.

>__Hur du använder det:__ Skapa en funktionsapp i Linux i dedikerade planen och ange vilka behållaravbildning kan köras från. Du kan göra detta på två sätt:
>
>* Skapa en funktionsapp i Linux på en App Service-plan i Azure-portalen. Välj **Docker-avbildning** för **publicera**, och konfigurera behållare, vilket ger den plats där avbildningen finns.
>* Skapa en funktionsapp i Linux på en App Service-plan via Azure CLI. Lär dig hur genom att granska [skapa en funktion i Linux med en anpassad avbildning](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Om du vill distribuera till en befintlig app som använder en anpassad behållare genom att använda den [ `func deploy` ](functions-run-local.md#publish) kommandot av den [Azure Functions Core Tools](functions-run-local.md).

>__När du ska använda den:__ Använd det här alternativet när du behöver mer kontroll över Linux-miljön där din funktionsapp körs. Mekanism för det här är endast tillgängliga för funktioner som körs på Linux i en App Service-plan.

### <a name="web-deploy-msdeploy"></a>Webdeploy (MSDeploy)

Paket och distribuerar dina Windows-program till alla IIS-servern, inklusive dina funktionsappar som körs på Windows i Azure.

>__Hur du använder det:__ Använd den [Visual Studio-verktyg för Azure Functions](functions-create-your-first-function-visual-studio.md), och avmarkera de `Run from package file (recommended)` box.
>
> Du kan också hämta [Web distribuera 3.6](https://www.iis.net/downloads/microsoft/web-deploy) och anropa `MSDeploy.exe` direkt.

>__När du ska använda den:__ Den här distributionstekniken som stöds och har inga problem, men den rekommenderade metoden är nu [Zip distribuera kör från paketet aktiverat](#zip-deploy). Mer information finns i [Utvecklingsguide för Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Källkontroll

Kan du ansluta din funktionsapp till en git-lagringsplats, så att eventuella uppdateringar av koden i databasen utlöser distribution. Mer information, ta en titt på de [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Hur du använder det:__ Använd Distribution Center i Azure Functions-portalen för att konfigurera publicering från källkontroll. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

>__När du ska använda den:__ Med hjälp av källkontroll är den bästa metoden för team att samarbeta på sina funktionsappar och det är ett bra alternativ som gör att mer sofistikerade behållarutvecklings.

### <a name="local-git"></a>Lokal git

Kan du skicka koden från din lokala dator till Azure Functions med Git.

>__Hur du använder det:__ Följ anvisningarna på [lokal Git-distribution till Azure App Service](../app-service/deploy-local-git.md).

>__När du ska använda den:__ I allmänhet rekommenderas andra metoder för distribution. När du publicerar från lokal git måste du [manuellt synkronisera utlösare](#trigger-syncing).

### <a name="cloud-sync"></a>Molnsynkronisering

Kan du synkronisera din innehåll från Dropbox och OneDrive till Azure Functions.

>__Hur du använder det:__ Följ instruktionerna i [synkronisera innehåll från en molnmapp](../app-service/deploy-content-sync.md).

>__När du ska använda den:__ I allmänhet rekommenderas andra metoder för distribution. När du publicerar med molnsynkronisering, måste du [manuellt synkronisera utlösare](#trigger-syncing).

### <a name="ftp"></a>FTP

Kan du direkt överföra filer till Azure Functions.

>__Hur du använder det:__ Följ instruktionerna i [distribuerar innehåll med FTP/s](../app-service/deploy-ftp.md).

>__När du ska använda den:__ I allmänhet rekommenderas andra metoder för distribution. När du publicerar med FTP, måste du [manuellt synkronisera utlösare](#trigger-syncing).

### <a name="portal-editing"></a>Redigering av portalen

Med hjälp av portal-baserade redigeraren kan du direkt redigera filer på din funktionsapp (i stort sett distribuerar varje gång du klickar på **spara**).

>__Hur du använder det:__ Om du vill kunna redigera dina funktioner i Azure-portalen måste du ha [skapade dina funktioner i portalen](functions-create-first-azure-function.md). Med hjälp av någon annan distributionsmetod som gör din funktion som skrivskyddad och förhindrar fortsatt portal redigering, att bevara en enda sanningskällan. Om du vill återgå till ett tillstånd där du kan redigera dina filer med Azure-portalen, kan du manuellt aktivera redigeringsläget tillbaka till `Read/Write` och ta bort alla inställningar för distribution-relaterade program (t.ex. `WEBSITE_RUN_FROM_PACKAGE`). 

>__När du ska använda den:__ Portalen är ett bra sätt att komma igång med Azure Functions, men verktyg som rekommenderas för alla mer intensiv utvecklingsarbete med hjälp av klienten:
>
>* [Komma igång med VS Code](functions-create-first-function-vs-code.md)
>* [Komma igång med Azure Functions Core Tools](functions-run-local.md)
>* [Kom igång med Visual Studio](functions-create-your-first-function-visual-studio.md)

I följande tabell visas de operativsystem och språk som portalen redigering stöds:

| | Windows-förbrukning | Windows Premium (förhandsversion) | Windows Dedicated | Linux-förbrukning (förhandsversion) | Linux Dedicated |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C#Skriptet |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (förhandsversion) | | | | | |
| PowerShell (förhandsversion) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> Portalen redigerar har aktiverats för endast HTTP och Timer utlösare för funktioner i Linux med dedikerade planera.

## <a name="deployment-slots"></a>Distributionsplatser

När du distribuerar appen till Azure kan kan du distribuera till en separat distributionsplats i stället för direkt till produktion. Mer information om distributionsplatser finns i [i dokumentationen för Azure App Service-fack](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Distributionsfack supportnivåer

Det finns två nivåer för support:

* _Allmänt tillgänglig (GA)_ – fullständigt stöd och godkänts för användning i produktion.
* _Förhandsversion av_ – har ännu inte stöds men förväntas bli allmänt tillgänglig status i framtiden.

| OS/Hosting-planen | Supportnivå |
| --------------- | ------ |
| Windows-förbrukning | Förhandsversion |
| Windows Premium (förhandsversion) | Förhandsversion |
| Windows Dedicated | Allmänt tillgänglig |
| Linux-förbrukning | Stöds inte |
| Linux Dedicated | Allmänt tillgänglig |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur du distribuerar dina funktionsappar i följande artiklar: 

+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)
+ [Kontinuerlig leverans med Azure DevOps](functions-how-to-azure-devops.md)
+ [ZIP-distributioner för Azure Functions](deployment-zip-push.md)
+ [Kör dina Azure-funktioner från en paketfil](run-functions-from-deployment-package.md)
+ [Automatisera resursdistribution för din funktionsapp i Azure Functions](functions-infrastructure-as-code.md)
