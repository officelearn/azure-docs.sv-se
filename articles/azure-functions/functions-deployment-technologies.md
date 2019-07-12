---
title: Distributionstekniker i Azure Functions | Microsoft Docs
description: Lär dig hur du kan distribuera kod till Azure Functions.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 47d8bf33fd686942326db3b1cc606978bf47a1bb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594400"
---
# <a name="deployment-technologies-in-azure-functions"></a>Distributionstekniker i Azure Functions

Du kan använda ett par olika tekniker för att distribuera projektkoden Azure Functions till Azure. Den här artikeln ger en fullständig förteckning av dessa tekniker, beskriver vilka tekniker är tillgängliga för vilka varianter av funktioner, beskriver vad som händer när du använder varje metod och ger rekommendationer för den bästa metoden ska användas i olika scenarier . De olika verktyg som har stöd för distribution till Azure Functions är anpassade till rätt teknik baserat på deras kontext.

## <a name="deployment-technology-availability"></a>Teknik distribueringstillgänglighet

> [!IMPORTANT]
> Azure Functions har stöd för lokal utveckling för flera plattformar och värdtjänster på Windows och Linux. För närvarande tre värdplaner som är tillgängliga: [Förbrukning](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), och [dedikerade (Azure App Service)](functions-scale.md#app-service-plan). Varje plan fungerar på olika sätt. Inte alla distributionstekniker är tillgängliga för varje version av Azure Functions.

| Distributionstekniken | Windows-förbrukning | Windows Premium (förhandsversion) | Windows Dedicated  | Linux-förbrukning (förhandsversion) | Linux Dedicated |
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
<sup>2</sup> portalen redigerar är bara aktiverat för HTTP och Timer utlösare för funktioner i Linux med dedikerad planen.

## <a name="key-concepts"></a>Viktiga begrepp

Några viktiga begrepp är viktigt att förstå hur distributioner fungerar i Azure Functions.

### <a name="trigger-syncing"></a>Utlösaren synkronisering

När du ändrar någon av dina utlösare måste Functions-infrastruktur vara medveten om ändringarna. Synkronisering sker automatiskt för många distributionstekniker. Men i vissa fall kan måste du manuellt synkronisera din utlösare. När du distribuerar dina uppdateringar genom att referera till ett externt paket URL, lokal Git, molnsynkronisering eller FTP, måste du manuellt synkronisera din utlösare. Du kan synkronisera utlösare på något av tre sätt:

* Starta om din funktionsapp i Azure portal
* Skicka en HTTP POST-begäran till `https://{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` med hjälp av den [huvudnyckeln](functions-bindings-http-webhook.md#authorization-keys).
* Skicka en HTTP POST-begäran till `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Ersätt platshållarna med ditt prenumerations-ID, resursgruppens namn och namnet på din funktionsapp.

## <a name="deployment-technology-details"></a>Distributionsinformation för teknik 

Följande distributionsmetoder finns i Azure Functions.

### <a name="external-package-url"></a>Externt paket-URL

Du kan använda ett externt paket-URL för att referera till en fjärransluten paketfil (.zip) som innehåller din funktionsapp. Filen laddas ned från den tillhandahållna URL: en och appen körs i [kör från paketet](run-functions-from-deployment-package.md) läge.

>__Hur du använder det:__ Lägg till `WEBSITE_RUN_FROM_PACKAGE` till programinställningarna. Värdet för den här inställningen ska vara en URL (platsen för specifika paketfilen du vill köra). Du kan lägga till inställningar antingen [i portalen](functions-how-to-use-azure-function-app-settings.md#settings) eller [med hjälp av Azure CLI](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). 
>
>Om du använder Azure Blob storage, använda en privat behållare med en [signatur för delad åtkomst (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#generate-a-sas-in-storage-explorer) att ge funktioner åtkomst till paketet. Varje gång programmet startas om, den hämtar en kopia av innehållet. Referens måste gälla för hela programmets livslängd.

>__När du ska använda den:__ Externt paket URL: en är den enda distributionsmetoden som stöds för Azure Functions som körs på Linux i förbrukningsplan (förhandsversion). När du uppdaterar paketfilen som refererar till en funktionsapp, måste du [manuellt synkronisera utlösare](#trigger-syncing) som talar om för Azure att ditt program har ändrats.

### <a name="zip-deploy"></a>Distribuera ZIP

Använd zip distribuera för att skicka en .zip-fil som innehåller din funktionsapp till Azure. Du kan ange din app för att starta i [kör från paketet](run-functions-from-deployment-package.md) läge.

>__Hur du använder det:__ Distribuera med hjälp av din favoritklient-verktyg: [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), eller [Azure CLI](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). För att manuellt distribuera en ZIP-fil till din funktionsapp, följer du anvisningarna i [distribuera från en .zip-fil eller URL](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>När du distribuerar med hjälp av zip distribuera kan du ange din app ska köras [kör från paketet](run-functions-from-deployment-package.md) läge. Att ställa in läget Kör från paketet den `WEBSITE_RUN_FROM_PACKAGE` program inställningens värde till `1`. Vi rekommenderar att zip-distribution. Det ger snabbare inläsning för dina program och det är standard för VS Code, Visual Studio och Azure CLI.

>__När du ska använda den:__ Distribuera ZIP är rekommenderad distribution-teknik för Azure Functions som körs på Windows och för Azure Functions som körs på Linux i dedikerade planen.

### <a name="docker-container"></a>Docker-behållare

Du kan distribuera en Linux-behållaravbildning som innehåller din funktionsapp.

>__Hur du använder det:__ Skapa en funktionsapp i Linux i dedikerade planen och ange vilka behållaravbildning kan köras från. Du kan göra detta på två sätt:
>
>* Skapa en funktionsapp i Linux på en Azure App Service-plan i Azure-portalen. För **publicera**väljer **Docker-avbildning**, och sedan konfigurera behållaren. Ange platsen där avbildningen finns.
>* Skapa en funktionsapp i Linux i en App Service-plan med hjälp av Azure CLI. Läs hur genom att läsa [skapa en funktion i Linux med hjälp av en anpassad avbildning](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Distribuera till en befintlig app genom att använda en anpassad behållare i [Azure Functions Core Tools](functions-run-local.md), använda den [ `func deploy` ](functions-run-local.md#publish) kommando.

>__När du ska använda den:__ Använd alternativet Docker-behållaren när du behöver mer kontroll över Linux-miljön där din funktionsapp körs. Mekanism för det här är bara tillgängligt för funktioner som körs på Linux i en App Service-plan.

### <a name="web-deploy-msdeploy"></a>Webdeploy (MSDeploy)

Web Deploy-paket och distribuerar dina Windows-program till alla IIS-servern, inklusive dina funktionsappar som körs på Windows i Azure.

>__Hur du använder det:__ Använd [Visual Studio-verktyg för Azure Functions](functions-create-your-first-function-visual-studio.md). Rensa den **kör paketfil (rekommenderas) från** markerar du kryssrutan.
>
>Du kan också hämta [Web distribuera 3.6](https://www.iis.net/downloads/microsoft/web-deploy) och anropa `MSDeploy.exe` direkt.

>__När du ska använda den:__ Webbdistribution som stöds och har inga problem, men den förvalda mekanismen är [zip distribuera kör från paketet aktiverat](#zip-deploy). Mer information finns i den [Utvecklingsguide för Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Källkontroll

Använd källkontroll för att ansluta din funktionsapp till en Git-lagringsplats. En uppdatering av koden i databasen utlöser distribution. Mer information finns i den [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Hur du använder det:__ Du kan använda Deployment Center i Azure Functions-portalen för att konfigurera publicering från källkontroll. Mer information finns i [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md).

>__När du ska använda den:__ Med hjälp av källkontroll är den bästa metoden för team som samarbetar på sina funktionsappar. Källkontroll är ett bra distributionsalternativ som gör att mer sofistikerade behållarutvecklings.

### <a name="local-git"></a>Lokal Git

Du kan använda lokal Git push-koden från din lokala dator till Azure Functions med hjälp av Git.

>__Hur du använder det:__ Följ instruktionerna i [lokal Git-distribution till Azure App Service](../app-service/deploy-local-git.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi att du använder en annan distribution-metoden. När du publicerar från lokal Git måste du [manuellt synkronisera utlösare](#trigger-syncing).

### <a name="cloud-sync"></a>Molnsynkronisering

Använd molnbaserad synkronisering att synkronisera innehållet från Dropbox och OneDrive till Azure Functions.

>__Hur du använder det:__ Följ instruktionerna i [synkronisera innehåll från en molnmapp](../app-service/deploy-content-sync.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra metoder för distribution. När du publicerar genom att använda molnsynkronisering, måste du [manuellt synkronisera utlösare](#trigger-syncing).

### <a name="ftp"></a>FTP

Du kan använda FTP för att direkt överföra filer till Azure Functions.

>__Hur du använder det:__ Följ instruktionerna i [distribuerar innehåll med hjälp av FTP/s](../app-service/deploy-ftp.md).

>__När du ska använda den:__ I allmänhet rekommenderar vi andra metoder för distribution. När du publicerar med FTP, måste du [manuellt synkronisera utlösare](#trigger-syncing).

### <a name="portal-editing"></a>Redigering av portalen

Redigeraren för portalbaserad kan du redigera filer som finns i din funktionsapp (distribuera i princip för varje gång du sparar dina ändringar) direkt.

>__Hur du använder det:__ Om du vill kunna redigera dina funktioner i Azure-portalen måste du ha [skapade dina funktioner i portalen](functions-create-first-azure-function.md). För att bevara en enda sanningskällan, med hjälp av någon annan distributionsmetod som gör din funktion skrivskyddat läge och förhindrar fortsatt redigering av portalen. Om du vill återgå till ett tillstånd där du kan redigera dina filer i Azure-portalen, kan du manuellt aktivera redigeringsläget tillbaka till `Read/Write` och ta bort alla inställningar för distribution-relaterade program (t.ex. `WEBSITE_RUN_FROM_PACKAGE`). 

>__När du ska använda den:__ Portalen är ett bra sätt att komma igång med Azure Functions. För mer intensiv utvecklingsarbete rekommenderar vi att du använder klienten verktyg:
>
>* [Komma igång med VS Code](functions-create-first-function-vs-code.md)
>* [Komma igång med Azure Functions Core Tools](functions-run-local.md)
>* [Kom igång med Visual Studio](functions-create-your-first-function-visual-studio.md)

I följande tabell visas de operativsystem och språk som stöd för portalen redigerar:

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

<sup>*</sup> Portalen redigerar är bara aktiverat för HTTP och Timer utlösare för funktioner i Linux med dedikerad planen.

## <a name="deployment-slots"></a>Distributionsplatser

När du distribuerar appen till Azure kan kan du distribuera till en separat distributionsplats i stället för att distribuera direkt till produktion. Mer information om distributionsplatser finns i [Azure App Service-fack](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Distributionsfack supportnivåer

Det finns två nivåer av stöd för distributionsplatser:

* **Allmän tillgänglighet (GA)** : Fullständigt stöds och godkända för användning i produktion.
* **Förhandsversion av**: Har ännu inte stöds, men förväntas bli allmänt tillgänglig status i framtiden.

| OS/hosting-planen | Supportnivå |
| --------------- | ------ |
| Windows-förbrukning | Förhandsversion |
| Windows Premium (förhandsversion) | Förhandsversion |
| Windows Dedicated | Allmän tillgänglighet |
| Linux-förbrukning | Stöds inte |
| Linux Dedicated | Allmän tillgänglighet |

## <a name="next-steps"></a>Nästa steg

Dessa artiklar innehåller mer information om hur du distribuerar dina funktionsappar: 

+ [Löpande distribution för Azure Functions](functions-continuous-deployment.md)
+ [Kontinuerlig leverans med hjälp av Azure DevOps](functions-how-to-azure-devops.md)
+ [ZIP-distributioner för Azure Functions](deployment-zip-push.md)
+ [Kör dina Azure-funktioner från en paketfil](run-functions-from-deployment-package.md)
+ [Automatisera resursdistribution för din funktionsapp i Azure Functions](functions-infrastructure-as-code.md)
