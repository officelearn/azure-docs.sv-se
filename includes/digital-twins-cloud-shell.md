---
author: baanders
description: inkludera fil för Azure Digitals dubbla – konfigurera Cloud Shell och IoT-tillägget
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: b664303d86f8588fc210b11b363b21d523d63295
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2020
ms.locfileid: "87507533"
---
För att börja arbeta med Azure Digitals dubbla i ett öppet [Azure Cloud Shells](https://shell.azure.com) fönster, är det första att göra-loggen att logga in och ange gränssnitts kontexten till din prenumeration för den här sessionen. Kör de här kommandona i Cloud Shell:

```azurecli
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Du kan också använda ditt prenumerations namn i stället för ID: t i kommandot ovan. 

Om det här är första gången du har använt den här prenumerationen med Azure Digitals, kör du kommandot för att registrera dig för namn rymden Azure Digitals. (Om du inte är säker är det OK att köra det igen även om du har gjort det någon gång tidigare.)

```azurecli
az provider register --namespace 'Microsoft.DigitalTwins'
```

Härnäst ska du lägga till [**Microsoft Azure IoT-tillägget för Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest) till din Cloud Shell, för att aktivera kommandon för att interagera med Azure Digitals och andra IoT-tjänster. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Nu är du redo att arbeta med Azure Digitals dubbla i Cloud Shell.

Du kan kontrol lera detta genom `az dt -h` att köra när som helst för att se en lista över de Azure Digital-kommandon som är tillgängliga på den översta nivån.