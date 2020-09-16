---
title: Snabb start – skapa en Azure API Management instans med CLI (för hands version)
description: Skapa en ny Azure API Management Service-instans med hjälp av Azure CLI.
author: dlepow
ms.service: api-management
ms.topic: quickstart
ms.custom: ''
ms.date: 09/10/2020
ms.author: apimpm
ms.openlocfilehash: 8e41e20752e4203bf9916813b86cd901f4f75c09
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/16/2020
ms.locfileid: "90708231"
---
# <a name="quickstart-create-a-new-azure-api-management-service-instance-by-using-the-azure-cli-preview"></a>Snabb start: skapa en ny Azure API Management Service-instans med hjälp av Azure CLI (för hands version)

Azure API Management (APIM) hjälper organisationer att publicera API:er till externa partner och interna utvecklare så att de kan få ut maximalt av sina data och tjänster. API Management lägger grunden till ett effektivt API-program genom engagerade utvecklare, affärsinsikter, analyser, hög säkerhet och skydd. Med APIM kan du skapa och hantera moderna API-gatewayer för befintliga Server dels tjänster var som helst. Mer information finns i [Översikt](api-management-key-concepts.md).

I den här snabb starten beskrivs stegen för att skapa en ny API Management-instans med hjälp av [AZ APIM](/cli/azure/apim) -kommandon i Azure CLI. Kommandona i `az apim` kommando gruppen är för närvarande i för hands version och kan ändras eller tas bort i framtida versioner.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Du kan använda Azure Cloud Shell eller en lokal installation av Azure CLI för att genomföra den här snabbstarten. Om du vill använda det lokalt rekommenderar vi version 2.11.1 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Azure API Management-instanser, t. ex. alla Azure-resurser, måste distribueras till en resurs grupp. Resursgrupper gör det enkelt att organisera och hantera relaterade Azure-resurser.

Börja med att skapa en resurs grupp med namnet *myResourceGroup* på platsen för centrala USA med följande [AZ Group Create](/cli/azure/group#az-group-create) -kommando:

```azurecli-interactive
az group create --name myResourceGroup --location centralus
```

## <a name="create-a-new-service"></a>Skapa en ny tjänst

Nu när du har en resurs grupp kan du skapa en API Management tjänst instans. Skapa ett med hjälp av kommandot [AZ APIM Create](/cli/azure/apim#az-apim-create) och ange ett tjänst namn och en utgivar information. Tjänst namnet måste vara unikt inom Azure. 

I följande exempel används *myapim* som tjänst namn. Uppdatera namnet till ett unikt värde. Uppdatera även namnet på API-utgivarens organisation och e-postadressen för att ta emot meddelanden. 

```azurecli-interactive
az apim create --name myapim --resource-group myResourceGroup \
  --publisher-name Contoso --publisher-email admin@contoso.com \
  --no-wait
```

Som standard skapar kommandot instansen på nivån utvecklare, ett ekonomiskt alternativ för att utvärdera Azure API Management. Den här nivån är inte för produktions användning. Mer information om att skala API Management-nivåerna finns i avsnittet [Uppgradera och skala](upgrade-and-scale.md). 

> [!TIP]
> Det kan ta mellan 30 och 40 minuter att skapa och aktivera en API Management tjänst på den här nivån. Föregående kommando använder `--no-wait` alternativet så att kommandot returnerar omedelbart när tjänsten skapas.

Kontrol lera statusen för distributionen genom att köra kommandot [AZ APIM show](/cli/azure/apim#az-apim-show) :

```azurecli-interactive
az apim show --name myapim --resource-group myResourceGroup --output table
```

Från början liknar utdata följande och visar `Activating` statusen:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR    PUBLIC IP    PRIVATE IP    STATUS      TIER       UNITS
-----------  ----------------  ----------  --------------  -----------  ------------  ----------  ---------  -------
myapim       myResourceGroup   Central US                                             Activating  Developer  1
```

Efter aktiveringen är statusen `Online` och tjänst instansen har en gateway-adress och en offentlig IP-adress. För tillfället visar dessa adresser inget innehåll. Exempel:

```console
NAME         RESOURCE GROUP    LOCATION    GATEWAY ADDR                       PUBLIC IP     PRIVATE IP    STATUS    TIER       UNITS
-----------  ----------------  ----------  ---------------------------------  ------------  ------------  --------  ---------  -------
myapim       myResourceGroup   Central US  https://myapim.azure-api.net       203.0.113.1                 Online    Developer  1
```

När din API Management tjänst instans är online är du redo att använda den. Börja med självstudien för att [Importera och publicera ditt första API](import-and-publish.md).

## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda kommandot [AZ Group Delete](/cli/azure/group#az-group-delete) för att ta bort resurs gruppen och API Management tjänst instansen.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Importera och publicera ditt första API](import-and-publish.md)
