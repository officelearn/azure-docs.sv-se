---
title: Självstudie för att integrera Azure Functions med Azure SignalR-tjänsten | Microsoft Docs
description: I den här självstudien lär du dig att använda Azure Functions med Azure SignalR-tjänsten
services: signalr
documentationcenter: ''
author: wesmc7777
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: signalr
ms.workload: tbd
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 04/24/2018
ms.author: wesmc
ms.openlocfilehash: b1bb6b3fe545d5a42fa985ab85bd7a914128e56b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/07/2018
---
# <a name="tutorial-integrate-azure-functions-with-azure-signalr-service"></a>Självstudie: Integrera Azure Functions med Azure SignalR-tjänsten

Den här självstudien bygger på chattrumsprogrammet du skapade i föregående självstudier. Om du inte har slutfört [Skapa ett chattrum med SignalR Service](signalr-quickstart-dotnet-core.md) och [Azure SignalR Service-autentisering](./signalr-authenticate-oauth.md) ska du först göra de övningarna. 

Ett vanligt scenario för realtidsprogram är att innehållsuppdateringar kommer från en server som ska publiceras på webbklienter. [Azure Functions](../azure-functions/functions-overview.md) är ett utmärkt alternativ för att generera sådana innehållsuppdateringar. En stor fördel när du använder Azure Functions är att du kan köra din kod på begäran utan att oroa dig för arkitekturen för hela programmet eller infrastrukturen för att köra den. Du betalar även bara för den tid då koden verkligen körs.  

Vanligtvis skulle det här scenariot utgöra ett problem när du försöker använda SignalR eftersom SignalR försöker att bibehålla en anslutning mellan klienten och servern för att skicka innehållsuppdateringar. Eftersom koden endast körs på begäran går det inte att bibehålla en anslutning. Dessutom kan Azure SignalR Service stödja det här scenariot eftersom det hanterar anslutningar för dig vid körning.

I den här självstudien använder du Azure Functions för att generera meddelanden med en timerfunktion i början av varje minut. Funktionen publicerar meddelanden till alla klienter i chattrummet som skapades i föregående självstudier. Mer information om timerfunktioner finns i [Timer Function](../azure-functions/functions-create-scheduled-function.md) (Timerfunktion).

Du kan använda alla kodredigeringsprogram för att slutföra stegen i den här snabbstarten. Men [Visual Studio Code](https://code.visualstudio.com/) är ett utmärkt alternativ som är tillgängligt på Windows-, macOS- och Linux-plattformar.

Koden för de här självstudierna är tillgänglig för nedladdning på [GitHub-lagringsplatsen AzureSignalR-samples](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer).

![Chattapp med servermeddelanden](./media/signalr-integrate-functions/signalr-functions-complete.png)

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en ny timerfunktion med Azure Functions med Azure CLI.
> * Konfigurera timerfunktionen för distribution av lokal Git-lagringsplats.
> * Anslut timern till din SignalR Service för att skicka uppdateringar varje minut

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Nödvändiga komponenter

För att kunna slutföra den här självstudien behöver du följande:

* [Git](https://git-scm.com/)
* [.NET Core SDK](https://www.microsoft.com/net/download/windows) 
* [Azure Cloud Shell konfigurerat](https://docs.microsoft.com/azure/cloud-shell/quickstart)
* Ladda ned eller klona GitHub-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).


[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-function-app"></a>Skapa en funktionsapp

Du måste skapa en funktionsapp för att definiera körningsmiljön för dina funktioner. Med funktionsappen kan du gruppera funktioner som en logisk enhet så att det blir enklare att hantera, distribuera och dela resurser. Mer information finns i [Skapa din första funktion med Azure CLI](../azure-functions/functions-create-first-azure-function-azure-cli.md).

I det här avsnittet använder du Azure Cloud Shell för att skapa en ny Azure Function-app som är konfigurerad för distribution från en lokal Git-lagringsplats. 

När du skapar funktionsappens resurser ska du skapa dem i samma resursgrupp som du använde i de föregående självstudierna. Med den metoden blir det enklare att hantera alla självstudieresurser.

Kopiera skriptet nedan i ditt textredigeringsprogram och ersätt värdena för variabelparametrarna med resursernas värden. Kopiera och klistra in det uppdaterade skriptet i din Azure Cloud Shell och tryck på **Retur** för att skapa ett lagringskonto och en funktionsapp.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your values from previous    === 
#=== tutorials.                                                   ===
#====================================================================
ResourceGroupName=SignalRTestResources
location=eastus

#====================================================================
#=== Update these variables for the new function app and storage  ===
#=== account.                                                     ===
#====================================================================
functionappName=mysignalfunctionapp
storageAccountName=mystorageaccount

# Create a storage account to hold function app code and settings
az storage account create --resource-group $ResourceGroupName \
--name $storageAccountName \
--location $location --sku Standard_LRS

# Create the function app
az functionapp create --resource-group $ResourceGroupName \
--name $functionappName \
--consumption-plan-location $location \
--storage-account $storageAccountName

```

| Parameter | Beskrivning |
| -------------------- | --------------- |
| ResourceGroupName | Den här resursgruppens namn föreslogs i föregående självstudier. Det är bra att hålla alla resurser från självstudier grupperade ihop. Använd samma resursgrupp som du använde i de tidigare självstudierna. | 
| location | Uppdatera variabeln till samma plats som du använde för att skapa resursgruppen i tidigare självstudier. | 
| functionappName | Uppdatera den här variabeln med ett unikt namn för din nya funktionsapp. Till exempel signalrfunctionapp22665120. | 
| storageAccountName | Ange ett namn på det nya lagringskontot som ska innehålla funktionsappkoden och inställningarna. | 



## <a name="configure-the-function-app"></a>Konfigurera funktionsappen

I det här avsnittet ska du konfigurera funktionsappen med en appinställning som innehåller anslutningssträngen för din Azure SignalR Service-resurs. Din funktionskod kommer att använda den här inställningen för att ansluta och publicera meddelanden till chattrummet. Du konfigurerar också funktionsappen för distribuering från en lokal Git-lagringsplats.

Kopiera skriptet nedan och ersätt värdena för parametrarna. Klistra in det uppdaterade skriptet i din Azure Cloud Shell och tryck på **Retur**.

```azurecli-interactive
#====================================================================
#=== Update these variables to match your resources.              === 
#====================================================================
ResourceGroupName=SignalRTestResources
functionappName=mysignalfunctionapp

#========================================================================
#=== Replace this value with the connection string for your           ===
#=== SignalR Service resource.                                        ===
#========================================================================
connstring="Endpoint=<service_endpoint>;AccessKey=<access_key>;"

# Add the SignalR Service connection string app setting
az functionapp config appsettings set --resource-group $ResourceGroupName \
    --name $functionappName \
    --setting "Azure:SignalR:ConnectionString=$connstring"

# configure for deployment from a local Git repository
az functionapp deployment source config-local-git --name $functionappName \
    --resource-group $ResourceGroupName

```

| Parameter | Beskrivning |
| -------------------- | --------------- |
| ResourceGroupName | Den här resursgruppens namn föreslogs i föregående självstudier. Det är bra att hålla alla resurser från självstudier grupperade ihop. Använd samma resursgrupp som du använde i de tidigare självstudierna. | 
| functionappName | Uppdatera den här variabeln med ett unikt namn för din nya funktionsapp. Till exempel signalrfunctionapp22665120. | 
| connstring | Ange anslutningssträng för din SignalR Service-resurs. Du kan hämta anslutningssträngen från din SignalR Service-resurssida i Azure Portal genom att klicka på **Nycklar** under **INSTÄLLNINGAR**. | 



Anteckna Git-distributions-URL:en som returnerades från det förra kommandot. Du kommer att använda denna URL för att distribuera funktionskoden.


## <a name="the-timer-function"></a>Timerfunktionen

Timerfunktionsexemplet finns i katalogen */samples/Timer* i nedladdningen, eller så kan du klona Github-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples/tree/master/samples/Timer). Timerfunktionens kod finns i *TimerFunction.cs*. Koden använder anslutningssträngen som är lagrad med standardkonfigurationsnyckeln (*Azure:SignalR:ConnectionString*) för att skapa en proxy till hubben. Eftersom funktionskoden körs på serversidan finns det ingen anledning att kräva att den autentiseras som en vanlig klient. Koden är betrodd att använda anslutningssträngen. Med denna hubbproxy kan funktionskoden anropa alla metoder du har definierat på din hubb. Koden anropar `BroadcastMessage`-metoden för att publicera ett meddelande som innehåller den aktuella tiden då utlösaren utlöstes.

Utlösaren för funktionskoden är en *timerTrigger*, och definieras i bindningarna i *TimerFunction/function.json*. Den innehåller ett [CRON-uttryck](https://wikipedia.org/wiki/Cron#CRON_expression) för att ange att timern ska utlösas i början av varje minut.

```json
{
  "bindings": [
    {
      "type": "timerTrigger",
      "schedule": "0 * * * * *",
      "useMonitor": true,
      "runOnStartup": false,
      "name": "myTimer"
    }
  ],
  "disabled": false,
  "scriptFile": "../Timer.dll",
  "entryPoint": "Timer.TimerFunction.Run"
}
```


## <a name="building-the-timer-function"></a>Skapa timerfunktionen

Använd [.NET Core-kommandoradsgränssnittet (CLI)](https://docs.microsoft.com/dotnet/core/tools/) i följande steg för att skapa funktionen och förbereda den för distribution:

1. Gå till underkatalogen */samples/Timer* i nedladdningen, eller så kan du klona Github-lagringsplatsen [AzureSignalR-sample](https://github.com/aspnet/AzureSignalR-samples).

2. Återställ NuGet-paketen med följande kommando:

        dotnet restore

3. Skapa *timer*-funktionsappen med följande kommando:

        msbuild /p:Configuration=Release


## <a name="create-and-deploy-the-local-git-repo"></a>Skapa och distribuera den lokala Git-lagringsplatsen

1. I ett Git-gränssnitt går du till versionens underkategori, */samples/Timer/bin/Release/net461*.

        cd ./AzureSignalR-samples/samples/Timer/bin/Release/net461

2. Initiera katalogen som en ny Git-lagringsplats med följande kommando:

        git init

3. Lägg till en ny incheckning för alla filer i versionens katalog.

        git add -A
        git commit -v -a -m "Initial Timer function commit"        

4. Lägg till en fjärrslutpunkt för den Git-distributions-URL du antecknade när du konfigurerade funktionsappen:

        git remote add Azure <enter your Git deployment URL>

5. Distribuera funktionsappen

        git push Azure master

   När koden har distribuerats börjar timern genast köras varje minut för att köra koden.

## <a name="test-the-chat-app"></a>Testa chattappen

Gå till chattprogrammet. Timerfunktionen du nyss skapade rapporterar nu tiden i början av varje minut.

![Chattapp med servermeddelanden](./media/signalr-integrate-functions/signalr-functions-complete.png)



## <a name="clean-up-resources"></a>Rensa resurser

Om du vill fortsätta att testa programmet kan du behålla resurserna du har skapat.

Om du är klar med exempelprogrammet kan du ta bort Azure-resurserna för att undvika ändringar. 

> [!IMPORTANT]
> Det går inte att ångra borttagningen av en resursgrupp och att resursgruppen och alla resurser i den tas bort permanent. Kontrollera att du inte av misstag tar bort fel resursgrupp eller resurser. Om du har skapat resurserna som värd för det här exemplet i en befintlig resursgrupp som innehåller resurser som du vill behålla kan du ta bort varje resurs separat från deras respektive blad istället för att ta bort resursgruppen.
> 
> 

Logga in på [Azure Portal](https://portal.azure.com) och klicka på **Resursgrupper**.

Skriv namnet på din resursgrupp i textrutan **Filtrera efter namn...**. Anvisningarna för den här artikeln använde en resursgrupp med namnet *SignalRTestResources*. På din resursgrupp i resultatlistan klickar du på **...** och därefter **Ta bort resursgrupp**.

   
![Ta bort](./media/signalr-integrate-functions/signalr-delete-resource-group.png)


Du blir ombedd att bekräfta borttagningen av resursgruppen. Skriv namnet på din resursgrupp för att bekräfta och klicka på **Ta bort**.
   
Efter en liten stund tas resursgruppen och resurser som finns i den bort.


## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att integrera med Azure Function för att skicka uppdateringar till klienter baserat på Azure Functions-utlösare. Om du vill veta mer om att använda Azure SignalR Server fortsätter du till Azure CLI-exemplen för SignalR Service.

> [!div class="nextstepaction"]
> [Azure SignalR CLI-exempel](./signalr-cli-samples.md)



