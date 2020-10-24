---
author: baanders
description: inkludera fil för Azure Digitals dubbla – konfigurera Cloud Shell och IoT-tillägget
ms.service: digital-twins
ms.topic: include
ms.date: 7/17/2020
ms.author: baanders
ms.openlocfilehash: 8a3efc9ba8fc8ffd8c0eca4340e1948c388c0a13
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494980"
---
För att börja arbeta med Azure Digitals dubbla i ett öppet [Azure Cloud Shells](https://shell.azure.com) fönster, är det första att göra-loggen att logga in och ange gränssnitts kontexten till din prenumeration för den här sessionen. Kör de här kommandona i Cloud Shell:

```azurecli-interactive
az login
az account set --subscription "<your-Azure-subscription-ID>"
```
> [!TIP]
> Du kan också använda ditt prenumerations namn i stället för ID: t i kommandot ovan. 

Om det här är första gången du har använt den här prenumerationen med Azure Digitals, kör du kommandot för att registrera dig för namn rymden Azure Digitals. (Om du inte är säker är det OK att köra det igen även om du har gjort det någon gång tidigare.)

```azurecli-interactive
az provider register --namespace 'Microsoft.DigitalTwins'
```

Härnäst ska du lägga till [**Microsoft Azure IoT-tillägget för Azure CLI**](https://docs.microsoft.com/cli/azure/ext/azure-iot/iot?view=azure-cli-latest&preserve-view=true) till din Cloud Shell, för att aktivera kommandon för att interagera med Azure Digitals och andra IoT-tjänster. 

[!INCLUDE [digital-twins-cloud-shell-extensions.md](digital-twins-cloud-shell-extensions.md)]

Nu är du redo att arbeta med Azure Digitals dubbla i Cloud Shell.

Du kan kontrol lera detta genom `az dt -h` att köra när som helst för att se en lista över de Azure Digital-kommandon som är tillgängliga på den översta nivån.