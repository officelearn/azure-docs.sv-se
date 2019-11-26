---
title: Create Azure Functions on Linux using a custom image
description: Lär dig hur du skapar en Azure Functions som körs på en anpassad Linux-avbildning.
ms.date: 09/27/2019
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 7bf079f84978539735f3bbf5bb13b18130871fb1
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484397"
---
# <a name="create-a-function-on-linux-using-a-custom-image"></a>Create a function on Linux using a custom image

Med Azure Functions kan Linux användas som värd för funktionerna i en anpassad container. Du kan också använda [en standardcontainer i Azure App Service som värd](functions-create-first-azure-function-azure-cli-linux.md). This functionality requires [the Functions 2.x runtime](functions-versions.md).

I den här självstudiekursen lär du dig hur du distribuerar funktioner i Azure som en anpassad Docker-avbildning. This pattern is useful when you need to customize the built-in container image. Du kanske vill använda en anpassad avbildning när dina funktioner kräver en viss språkversion eller särskilda beroenden eller konfigurationer som inte är tillgängliga inom den inbyggda avbildningen. Supported base images for Azure Functions are found in the [Azure Functions base images repo](https://hub.docker.com/_/microsoft-azure-functions-base). 

Den här kursen går igen hur du använder Azure Functions Core Tools för att skapa en funktion i en anpassad Linux-avbildning. Du publicerar den här avbildningen i en funktionsapp i Azure, som har skapats med hjälp av Azure CLI. Later, you update your function to connect to Azure Queue storage. You also enable.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Skapa en funktionsapp och Dockerfile med hjälp av Core Tools.
> * skapa en anpassad avbildning med Docker
> * Publicera en anpassad avbildning till ett containerregister.
> * skapa ett Azure Storage-konto
> * Create a Premium hosting plan.
> * distribuera en funktionsapp från Docker Hub
> * lägga till programinställningar i funktionsappen.
> * Enable continuous deployment.
> * Enable SSH connections to the container.
> * Add a Queue storage output binding. 

Följande steg kan användas på en Mac-, Windows- eller Linux-dator. 

## <a name="prerequisites"></a>Krav

Innan du kör exemplet måste du ha följande:

* Installera [Azure Core Tools version 2.x](functions-run-local.md#v2).

* Installera [Azure CLI]( /cli/azure/install-azure-cli). Den här artikeln kräver Azure CLI version 2.0 eller senare. Kör `az --version` för att se vilken version du har.  
Du kan också använda [Azure Cloud Shell](https://shell.azure.com/bash).

* En aktiv Azure-prenumeration.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-cloud-shell-note](../../includes/functions-cloud-shell-note.md)]

## <a name="create-the-local-project"></a>Create the local project

Kör följande kommando från kommandoraden för att skapa ett funktionsapprojekt i mappen `MyFunctionProj` i den aktuella lokala katalogen. For a Python project, you [must be running in a virtual environment](functions-create-first-function-python.md#create-and-activate-a-virtual-environment).

```bash
func init MyFunctionProj --docker
```

När du inkluderar alternativet `--docker` skapas en Dockerfile för projektet. Den här filen används till att skapa en anpassad container där projektet körs. Vilken basavbildning som används beror på vilket arbetskörningsspråk som väljs.  

Vid uppmaning väljer du en arbetskörning från följande språk:

* `dotnet`: creates a .NET Core class library project (.csproj).
* `node`: skapar ett JavaScript-projekt.
* `python`: skapar ett Python-projekt.  

Använd följande kommando för att navigera till den nya `MyFunctionProj`-projektmappen.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

## <a name="build-from-the-docker-file"></a>Build from the Docker file

Ta en titt på _Dockerfile_ i rotmappen för projektet. Den här filen beskriver vilken miljö som krävs för att köra funktionsappen på Linux. Följande exempel i en Dockerfile som skapar en container som kör en funktionsapp på JavaScript-arbetskörningen (Node.js): 

```Dockerfile
FROM mcr.microsoft.com/azure-functions/node:2.0

ENV AzureWebJobsScriptRoot=/home/site/wwwroot
COPY . /home/site/wwwroot
```

> [!NOTE]
> The complete list of supported base images for Azure Functions can be found in the [Azure Functions base image page](https://hub.docker.com/_/microsoft-azure-functions-base).

### <a name="run-the-build-command"></a>Kör kommandot `build`

I rotmappen kör du kommandot [docker build](https://docs.docker.com/engine/reference/commandline/build/) och anger ett namn, `mydockerimage`, och en tagg, `v1.0.0`. Ersätt `<docker-id>` med ditt konto-ID för Docker Hub. Det här kommandot skapar Docker-avbildningen för containern.

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

When the command completes, you can run the new container locally.

### <a name="run-the-image-locally"></a>Run the image locally
Kontrollera att avbildningen du har skapat fungerar genom att köra Docker-avbildningen i en lokal container. Utfärda kommandot [docker run](https://docs.docker.com/engine/reference/commandline/run/) och skicka avbildningens namn och tagg till det. Se till att ange porten som använder argumentet `-p`.

```bash
docker run -p 8080:80 -it <docker-ID>/mydockerimage:v1.0.0
```

Kontrollera medan den anpassade avbildningen körs i en lokal Docker-container att funktionsappen och containern fungerar som de ska genom att gå till <http://localhost:8080>.

![Run the function app locally.](./media/functions-create-function-linux-custom-image/run-image-local-success.png)

> [!NOTE]
> At this point, when you try to call your specific HTTP function, you get an HTTP 401 error response. This is because your function runs in the local container as it would in Azure, which means that the function key is required. Because the container hasn't yet been published to a function app, there is no function key available. You'll see later that when you use Core Tools to publish your container, the function keys are shown to you. If you want to test your function running in the local container, you can change the [authorization key](functions-bindings-http-webhook.md#authorization-keys) to `anonymous`. 

Stoppa körningen när du har verifierat funktionsappen i containern. Nu kan överföra den anpassade avbildningen till ditt Docker Hub-konto.

## <a name="push-to-docker-hub"></a>Push to Docker Hub

Ett register är ett program som är värd för avbildningar och tillhandahåller tjänster för avbildningar och containrar. För att kunna dela avbildningen måste du push-överföra den till ett register. Docker Hub är ett register för Docker-avbildningar som gör att du kan vara värd för dina egna lagringsplatser, antingen offentliga eller privata.

Innan du kan push-överföra en avbildning måste du logga in på Docker Hub med kommandot [docker login](https://docs.docker.com/engine/reference/commandline/login/). Ersätt `<docker-id>` med ditt kontonamn och ange ditt lösenord i konsolen när det efterfrågas. För andra alternativ för Docker Hub-lösenord, se [kommandodokumentationen om dockerinloggning](https://docs.docker.com/engine/reference/commandline/login/).

```bash
docker login --username <docker-id>
```

A "login succeeded" message confirms that you're logged in. När du har loggat in push-överför du avbildningen till Docker Hub med kommandot [docker push](https://docs.docker.com/engine/reference/commandline/push/).

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

After the push succeeds, you can use the image as the deployment source for a new function app in Azure.

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-premium-plan"></a>Create a Premium plan

Linux hosting for custom Functions containers supported on [Dedicated (App Service) plans](functions-scale.md#app-service-plan) and [Premium plans](functions-premium-plan.md#features). This tutorial uses a Premium plan, which can scale as needed. Mer information om värdfunktioner finns i [Azure Functions hosting plans comparison](functions-scale.md) (Jämförelse av Azure Functions-värdplaner).

The following example creates a Premium plan named `myPremiumPlan` in the **Elastic Premium 1** pricing tier (`--sku EP1`), in the West US region (`-location WestUS`), and in a Linux container (`--is-linux`).

```azurecli-interactive
az functionapp plan create --resource-group myResourceGroup --name myPremiumPlan \
--location WestUS --number-of-workers 1 --sku EP1 --is-linux
```

## <a name="create-an-app-from-the-image"></a>Create an app from the image

The function app manages the execution of your functions in your hosting plan. Skapa en funktionsapp från en Docker Hub-avbildning med kommandot [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

I följande kommando infogar du ett unikt funktionsappnamn istället för platshållaren `<app_name>` och lagringskontonamnet istället för `<storage_name>`. `<app_name>` används som DNS-standarddomän för funktionsappen. Därför måste namnet vara unikt bland alla appar i Azure. Precis som tidigare är `<docker-id>` ditt Docker-kontonamn.

```azurecli-interactive
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--plan myPremiumPlan --deployment-container-image-name <docker-id>/mydockerimage:v1.0.0
```

Parametern _deployment-container-image-name_ anger vilken avbildning på Docker Hub som ska användas för att skapa funktionsappen. Use the [az functionapp config container show](/cli/azure/functionapp/config/container#az-functionapp-config-container-show) command to view information about the image used for deployment. Use the [az functionapp config container set](/cli/azure/functionapp/config/container#az-functionapp-config-container-set) command to deploy from a different image.

## <a name="configure-the-function-app"></a>Konfigurera funktionsappen

Funktionen behöver anslutningssträngen för att ansluta till standardlagringskontot. When you're publishing your custom image to a private container account, you should instead set these application settings as environment variables in the Dockerfile using the [ENV instruction](https://docs.docker.com/engine/reference/builder/#env), or something similar.

I det här fallet är `<storage_name>` namnet på det lagringskonto du skapade. Visa anslutningssträngen med kommandot [az storage account show-connection-string](/cli/azure/storage/account). Lägg till dessa programinställningar i funktionsappen med kommandot [az functionapp config appsettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set).

```azurecli-interactive
storageConnectionString=$(az storage account show-connection-string \
--resource-group myResourceGroup --name <storage_name> \
--query connectionString --output tsv)

az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings AzureWebJobsDashboard=$storageConnectionString \
AzureWebJobsStorage=$storageConnectionString
```

> [!NOTE]
> Om containern är privat skulle du även behöva ange följande programinställningar  
> - DOCKER_REGISTRY_SERVER_USERNAME  
> - DOCKER_REGISTRY_SERVER_PASSWORD  
>
> Du måste stoppa och sedan starta funktionsappen för att dessa värden ska hämtas

## <a name="verify-your-functions"></a>Verify your functions

<!-- we should replace this with a CLI or API-based approach, when we get something better than REST -->

The HTTP-triggered function you created requires a [function key](functions-bindings-http-webhook.md#authorization-keys) when calling the endpoint. At this time, the easiest way to get your function URL, including the key, is from the [Azure-portalen]. 

> [!TIP]
> You can also obtain your function keys by using the [Key management APIs](https://github.com/Azure/azure-functions-host/wiki/Key-management-API), which requires you to present a [bearer token for authentication](/cli/azure/account#az-account-get-access-token).

Locate your new function app in the [Azure-portalen] by typing your function app name in the **Search** box at the top of the page and selecting the **App Service** resource.

Select the **MyHttpTrigger** function, select **</> Get function URL** > **default (Function key)**  > **Copy**.

![Kopiera funktionswebbadressen från Azure Portal](./media/functions-create-function-linux-custom-image/functions-portal-get-url-key.png)

In this URL, the function key is the `code` query parameter. 

> [!NOTE]  
> Because your function app is deployed as a container, you can't make changes to your function code in the portal. You must instead update the project in local container and republish it to Azure.

Klistra in funktionens URL i adressfältet för din webbläsare. Lägg till frågesträngvärdet `&name=<yourname>` i slutet av den här webbadressen och tryck på knappen `Enter` på tangentbordet för att utföra begäran. Du bör se svaret som returnerades av funktionen som visas i webbläsaren.

I följande exempel visas svaret i webbläsaren:

![Funktionssvar i webbläsaren.](./media/functions-create-function-linux-custom-image/function-app-browser-testing.png)

Begäransadressen innehåller en nyckel som krävs för åtkomst till din funktion över HTTP. 

## <a name="enable-continuous-deployment"></a>Enable continuous deployment

One of the benefits of using containers is support for continuous deployment. Functions lets you automatically deploy updates when your container is updated in the registry. Enable continuous deployment with the [az functionapp deployment container config](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-config) command.

```azurecli-interactive
az functionapp deployment container config --enable-cd \
--query CI_CD_URL --output tsv \
--name <app_name> --resource-group myResourceGroup
```

This command returns the deployment webhook URL after continuous deployment is enabled. You can also use the [az functionapp deployment container show-cd-url](/cli/azure/functionapp/deployment/container#az-functionapp-deployment-container-show-cd-url) command to return this URL. 

Copy the deployment URL and browse to your DockerHub repo, choose the **Webhooks** tab, type a **Webhook name** for the webhook, paste your URL in **Webhook URL**, and then choose the plus sign ( **+** ).

![Add the webhook in your DockerHub repo](./media/functions-create-function-linux-custom-image/dockerhub-set-continuous-webhook.png)  

With the webhook set, any updates to the linked image in DockerHub result in the function app downloading and installing the latest image.

## <a name="enable-ssh-connections"></a>Enable SSH connections

SSH möjliggör säker kommunikation mellan en container och en klient. With SSH enabled, you can connect to your container using App Service Advanced Tools (Kudu). To make it easy to connect to your container using SSH, Functions provide a base image that has SSH already enabled. 

### <a name="change-the-base-image"></a>Change the base image

In your dockerfile, append the string `-appservice` to the base image in your `FROM` instruction, which for a JavaScript project looks like the following.

```docker
FROM mcr.microsoft.com/azure-functions/node:2.0-appservice
```

The differences in the two base images enable SSH connections into your container. These differences are detailed in [this App Services tutorial](../app-service/containers/tutorial-custom-docker-image.md#enable-ssh-connections).

### <a name="rebuild-and-redeploy-the-image"></a>Rebuild and redeploy the image

In the root folder, run the [docker build](https://docs.docker.com/engine/reference/commandline/build/) command again, as before, replace `<docker-id>` with your Docker Hub account ID. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Push the updated image back to Docker Hub.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

The updated image is redeployed to your function app.

### <a name="connect-to-your-container-in-azure"></a>Connect to your container in Azure

In the browser, navigate to the following Advanced Tools (Kudu) `scm.` endpoint for your function app container, replacing `<app_name>` with the name of your function app.

```
https://<app_name>.scm.azurewebsites.net/
```

Sign in to your Azure account, and then select the **SSH** tab to create an SSH connection into your container.

After the connection is established, run the `top` command to view the currently running processes. 

![Linux top command running in an SSH session.](media/functions-create-function-linux-custom-image/linux-custom-kudu-ssh-top.png)

## <a name="write-to-queue-storage"></a>Write to Queue storage

Functions lets you connect Azure services and other resources to functions without having to write your own integration code. These *bindings*, which represent both input and output, are declared within the function definition. Data from bindings is provided to the function as parameters. A *trigger* is a special type of input binding. Although a function has only one trigger, it can have multiple input and output bindings. To learn more, see [Azure Functions triggers and bindings concepts](functions-triggers-bindings.md).

This section shows you how to integrate your function with an Azure Storage queue. The output binding that you add to this function writes data from an HTTP request to a message in the queue.

### <a name="download-the-function-app-settings"></a>Download the function app settings

[!INCLUDE [functions-app-settings-download-local-cli](../../includes/functions-app-settings-download-local-cli.md)]

### <a name="enable-extension-bundles"></a>Enable extension bundles

Because you are using a Queue storage output binding, you must have the Storage bindings extension installed before you run the project. 


# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

With the exception of HTTP and timer triggers, bindings are implemented as extension packages. Run the following [dotnet add package](/dotnet/core/tools/dotnet-add-package) command in the Terminal window to add the Storage extension package to your project.

```bash
dotnet add package Microsoft.Azure.WebJobs.Extensions.Storage --version 3.0.4
```

> [!TIP]
> When using Visual Studio, you can also use the NuGet package manager to add this package.

---

Now, you can add a Storage output binding to your project.

### <a name="add-an-output-binding"></a>Lägg till en utdatabindning

In Functions, each type of binding requires a `direction`, `type`, and a unique `name` to be defined in the function.json file. The way you define these attributes depends on the language of your function app.

# <a name="javascript--pythontabnodejspython"></a>[JavaScript / Python](#tab/nodejs+python)

[!INCLUDE [functions-add-output-binding-json](../../includes/functions-add-output-binding-json.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library](../../includes/functions-add-storage-binding-csharp-library.md)]

---

### <a name="add-code-that-uses-the-output-binding"></a>Lägg till kod som använder utdatabindning

After the binding is defined, you can use the `name` of the binding to access it as an attribute in the function signature. By using an output binding, you don't have to use the Azure Storage SDK code for authentication, getting a queue reference, or writing data. The Functions runtime and queue output binding do those tasks for you.

# <a name="javascripttabnodejs"></a>[JavaScript](#tab/nodejs)

[!INCLUDE [functions-add-output-binding-js](../../includes/functions-add-output-binding-js.md)]

# <a name="pythontabpython"></a>[Python](#tab/python)

[!INCLUDE [functions-add-output-binding-python](../../includes/functions-add-output-binding-python.md)]

# <a name="ctabcsharp"></a>[C\#](#tab/csharp)

[!INCLUDE [functions-add-storage-binding-csharp-library-code](../../includes/functions-add-storage-binding-csharp-library-code.md)]

---

### <a name="update-the-hosted-container"></a>Update the hosted container

In the root folder, run the [docker build](https://docs.docker.com/engine/reference/commandline/build/) command again, and this time update the version in the tag to `v1.0.2`. As before, replace `<docker-id>` with your Docker Hub account ID. 

```bash
docker build --tag <docker-id>/mydockerimage:v1.0.0 .
```

Push the updated image back to the repository.

```bash
docker push <docker-id>/mydockerimage:v1.0.0
```

### <a name="verify-the-updates-in-azure"></a>Verify the updates in Azure

Use the same URL as before from the browser to trigger your function. You should see the same response. However, this time the string that you pass as the `name` parameter is written to the `outqueue` storage queue.

[!INCLUDE [functions-storage-account-set-cli](../../includes/functions-storage-account-set-cli.md)]

[!INCLUDE [functions-query-storage-cli](../../includes/functions-query-storage-cli.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Nästa steg

Now that you have successfully deployed your custom container to a function app in Azure, consider reading more about the following topics:

+ [Monitoring functions](functions-monitoring.md)
+ [Scale and hosting options](functions-scale.md)
+ [Kubernetes-based serverless hosting](functions-kubernetes-keda.md)

[Azure-portalen]: https://portal.azure.com
