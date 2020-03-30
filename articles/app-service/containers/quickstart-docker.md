---
title: 'Snabbstart: Kör en anpassad Linux-behållare'
description: Kom igång med Linux-behållare på Azure App Service genom att distribuera din första anpassade behållare med Hjälp av Azure Container Register.
author: msangapu-msft
ms.author: msangapu
ms.date: 08/28/2019
ms.topic: quickstart
ms.openlocfilehash: 62e6b007e89fc6be726d3d971ca838770db9cb6e
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "75422155"
---
# <a name="deploy-a-custom-linux-container-to-azure-app-service"></a>Distribuera en anpassad Linux-behållare till Azure App Service

App Service på Linux ger fördefinierade programstaplar på Linux med stöd för språk som .NET, PHP, Node.js och andra. Du kan också använda en anpassad Docker-avbildning för att köra din webbapp på en programstack som inte redan har definierats i Azure. Den här snabbstarten visar hur du distribuerar en avbildning från ett [Azure Container Registry](/azure/container-registry) (ACR) till App Service.

## <a name="prerequisites"></a>Krav

* Ett [Azure-konto](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-tutorial-docker-extension&mktingSource=vscode-tutorial-docker-extension)
* [Docker](https://www.docker.com/community-edition)
* [Visual Studio-kod](https://code.visualstudio.com/)
* [Azure App Service-tillägget för VS-kod](vscode:extension/ms-azuretools.vscode-azureappservice). Du kan använda det här tillägget för att skapa, hantera och distribuera Linux Web Apps på Azure-plattformen som en tjänst (PaaS).
* [Docker-tillägget för VS-kod](vscode:extension/ms-azuretools.vscode-docker). Du kan använda det här tillägget för att förenkla hanteringen av lokala Docker-avbildningar och kommandon och för att distribuera byggda appavbildningar till Azure.

## <a name="create-an-image"></a>Skapa en bild

För att slutföra den här snabbstarten behöver du en lämplig webbappavbildning som lagras i ett [Azure Container-register](/azure/container-registry). Följ instruktionerna i [Snabbstart: Skapa ett privat behållarregister med Azure-portalen](/azure/container-registry/container-registry-get-started-portal), men använd avbildningen `mcr.microsoft.com/azuredocs/go` i stället för avbildningen. `hello-world` Som referens [finns exempelläget Dockerfile i repoan för Azure Samples](https://github.com/Azure-Samples/go-docs-hello-world).

> [!IMPORTANT]
> Var noga med att ange alternativet **Admin User** som **ska aktiveras** när du skapar behållarregistret. Du kan också ange det från avsnittet **Access-nycklar** på registersidan i Azure-portalen. Den här inställningen krävs för åtkomst till App Service.

## <a name="sign-in"></a>Logga in

Starta sedan VS-kod och logga in på ditt Azure-konto med hjälp av tillägget App Service. Det gör du genom att välja Azure-logotypen i aktivitetsfältet, navigera till **APP SERVICE-utforskaren** och sedan **välja Logga in på Azure** och följ instruktionerna.

![logga in på Azure](./media/quickstart-docker/sign-in.png)

## <a name="check-prerequisites"></a>Kontrollera krav

Nu kan du kontrollera om du har alla förutsättningar installerade och konfigurerade på rätt sätt.

I VS-kod bör du se din Azure-e-postadress i statusfältet och din prenumeration i **APP SERVICE-utforskaren.**

Kontrollera sedan att Du har Installerat och kört Docker. Följande kommando visar Docker-versionen om den körs.

```bash
docker --version
```

Kontrollera slutligen att ditt Azure-behållarregister är anslutet. Det gör du genom att välja Docker-logotypen i aktivitetsfältet och sedan navigera till **register**.

![Register](./media/quickstart-docker/registries.png)

## <a name="deploy-the-image-to-azure-app-service"></a>Distribuera avbildningen till Azure App Service

Nu när allt är konfigurerat kan du distribuera avbildningen till [Azure App Service](https://azure.microsoft.com/services/app-service/) direkt från Docker-tilläggsutforskaren.

Leta reda på bilden under noden **Register** i **DOCKER-utforskaren** och expandera den för att visa taggarna. Högerklicka på en tagg och välj sedan **Distribuera avbildning till Azure App Service**.

Härifrån följer du anvisningarna om att välja en prenumeration, ett globalt unikt appnamn, en resursgrupp och en appserviceplan. Välj **B1 Basic** för prisnivån och en region.

Efter distributionen är appen `http://<app name>.azurewebsites.net`tillgänglig på .

En **resursgrupp** är en namngiven samling av alla programmets resurser i Azure. En resursgrupp kan till exempel innehålla en referens till en webbplats, en databas och en Azure-funktion.

En **apptjänstplan** definierar de fysiska resurser som ska användas för att vara värd för din webbplats. Denna snabbstart använder en **grundläggande** hosting plan på **Linux-infrastruktur,** vilket innebär att webbplatsen kommer att vara värd på en Linux-maskin tillsammans med andra webbplatser. Om du börjar med **Basic-abonnemanget** kan du använda Azure-portalen för att skala upp så att din är den enda platsen som körs på en dator.

## <a name="browse-the-website"></a>Surfa på webbplatsen

**Utdatapanelen** öppnas under distributionen för att ange åtgärdens status. När åtgärden är klar letar du reda på appen som du skapade i APP SERVICE-utforskaren, högerklickar på den och väljer sedan **Bläddra på webbplats** för att öppna webbplatsen i webbläsaren. **APP SERVICE**

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/PWZWZ52?tutorial=quickstart-docker&step=deploy-app)

## <a name="next-steps"></a>Nästa steg

Grattis, du har slutfört denna snabbstart!

Kolla sedan in de andra Azure-tilläggen.

* [Cosmos DB](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-cosmosdb)
* [Azure Functions](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)
* [Azure CLI-verktyg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azurecli)
* [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)

Eller hämta dem alla genom att installera Azure Tools-tilläggspaketet. [Azure Tools](https://marketplace.visualstudio.com/items?itemName=ms-vscode.vscode-node-azure-pack)
