---
title: 'Snabb start: köra en anpassad Linux-behållare'
description: Kom igång med Linux-behållare på Azure App Service genom att distribuera din första anpassade behållare med hjälp av Azure Container Registration.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 51607f44f75cbd93c7ecf1c77bb52c73b27b5f3c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/29/2020
ms.locfileid: "82159753"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Distribuera en anpassad Linux-behållare till Azure App Service

App Service på Linux ger fördefinierade program stackar i Linux med stöd för språk som .NET, PHP, Node. js och andra. Du kan också använda en anpassad Docker-avbildning för att köra din webbapp på en programstack som inte redan har definierats i Azure. Den här snabb starten visar hur du distribuerar en avbildning från en [Azure Container Registry](/azure/container-registry) (ACR) till App Service.

## <a name="prerequisites"></a>Krav

* Ett [Azure-konto](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio-koden](https://code.visualstudio.com/)
* [Azure App Service-tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azureappservice). Du kan använda det här tillägget för att skapa, hantera och distribuera Linux-Web Apps på Azure-plattformen as a Service (PaaS).
* [Docker-tillägget för vs Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker). Du kan använda det här tillägget för att förenkla hanteringen av lokala Docker-avbildningar och-kommandon och för att distribuera inbyggda app-avbildningar till Azure.

## <a name="create-an-image"></a>Skapa en avbildning

För att slutföra den här snabb starten behöver du en lämplig webb program avbildning som lagras i en [Azure Container Registry](/azure/container-registry). Följ anvisningarna i [snabb start: skapa ett privat behållar register med hjälp av Azure Portal](/azure/container-registry/container-registry-get-started-portal), `mcr.microsoft.com/azuredocs/go` men Använd avbildningen `hello-world` i stället för avbildningen. [Exempel-Dockerfile finns i Azure samples lagrings platsen](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Se till att ange alternativet för **Administratörs användare** för att **Aktivera** när du skapar behållar registret. Du kan också ställa in den från avsnittet **åtkomst nycklar** på register sidan i Azure Portal. Den här inställningen krävs för att App Service åtkomst.

## <a name="sign-in"></a>Logga in

Starta sedan VS Code och logga in på ditt Azure-konto med hjälp av App Service tillägget. Det gör du genom att välja Azure-logotypen i aktivitets fältet, gå till **App Service** Explorer och sedan välja **Logga in på Azure** och följa instruktionerna.

![Logga in på Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Kontrollera krav

Nu kan du kontrol lera om alla nödvändiga komponenter har installerats och kon figurer ATS korrekt.

I VS Code bör du se din Azure-e-postadress i statusfältet och din prenumeration i **App Service** Explorer.

Kontrol lera sedan att du har Docker installerat och körs. Följande kommando visar Docker-versionen om den körs.

```bash
docker --version
```

Se slutligen till att Azure Container Registry är ansluten. Det gör du genom att välja Docker-logo typen i aktivitets fältet och sedan navigera till **register**.

![Register](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Distribuera avbildningen till Azure App Service

Nu när allt har kon figurer ATS kan du distribuera avbildningen till [Azure App Service](https://azure.microsoft.com/services/app-service/) direkt från Docker-utöknings Utforskaren.

Hitta avbildningen under noden **register** i **Docker** Explorer och expandera den för att visa dess taggar. Högerklicka på en tagg och välj sedan **distribuera avbildning till Azure App Service**.

Härifrån följer du anvisningarna för att välja en prenumeration, ett globalt unikt app-namn, en resurs grupp och en App Service-plan. Välj **B1 Basic** för pris nivån och en region.

Efter distributionen är din app tillgänglig på `http://<app name>.azurewebsites.net`.

En **resurs grupp** är en namngiven samling av alla dina programs resurser i Azure. En resurs grupp kan till exempel innehålla en referens till en webbplats, en databas och en Azure-funktion.

En **App Service plan** definierar de fysiska resurser som ska användas som värd för din webbplats. I den här snabb starten används en **grundläggande** värd plan i **Linux** -infrastrukturen, vilket innebär att platsen kommer att finnas på en Linux-dator bredvid andra webbplatser. Om du börjar med **Basic** -planen kan du använda Azure Portal för att skala upp så att din enda plats körs på en dator.

## <a name="browse-the-website"></a>Bläddra på webbplatsen

Panelen **utdata** öppnas under distributionen för att visa status för åtgärden. När åtgärden har slutförts söker du efter den app som du skapade i **App Service** Explorer, högerklickar på den och väljer sedan **Bläddra webbplats** för att öppna webbplatsen i webbläsaren.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört den här snabb starten!

Kolla sedan in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem genom att installera tilläggs paketet för [Azure tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack) .
