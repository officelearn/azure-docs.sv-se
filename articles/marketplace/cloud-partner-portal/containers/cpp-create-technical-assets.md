---
title: Skapa tekniska resurser för Azure Containers-avbildning | Azure Marketplace
description: Skapa de tekniska tillgångarna för en Azure-behållare.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 71b3ec4bf505c333e5eca170e1f5e808ab51f41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280073"
---
# <a name="prepare-your-container-technical-assets"></a>Tekniska tillgångar för att förbereda containrar

I den här artikeln beskrivs steg och krav för att konfigurera en behållare som erbjuder Azure Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Granska dokumentationen [för Azure Container Instances,](https://docs.microsoft.com/azure/container-instances) som innehåller snabbstarter, självstudier och exempel.

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Designa, bygga och testa dessa tillgångar tar tid och kräver teknisk kunskap om både Azure-plattformen och de tekniker som används för att skapa erbjudandet.
 
Förutom din lösningsdomän bör ditt ingenjörsteam ha kunskap om följande Microsoft-tekniker:

-   Grundläggande förståelse för [Azure Services](https://azure.microsoft.com/services/) 
-   Så här [utformar och arkitekt azure-program utformas](https://azure.microsoft.com/solutions/architecture/)
-   Kunskap om [virtuella Azure-datorer,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
-   Kunskap om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Arbetskunskap om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Föreslagna verktyg

Välj en eller båda av följande skriptmiljöer för att hantera behållaravbildningen:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Dessutom rekommenderar vi att du lägger till följande verktyg i utvecklingsmiljön:

-   [Utforskaren för Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio-kod](https://code.visualstudio.com/)
    *   Tillägg: [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Förlängning: [Försköna](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Förlängning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi föreslår också att du granskar de tillgängliga verktygen på sidan [Azure Developer Tools](https://azure.microsoft.com/tools/) och, om du använder Visual Studio, Visual Studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Skapa behållaravbildningen

Se följande för mer information:

* [Självstudiekurs: Skapa en behållaravbildning för distribution till Azure Container-instanser](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Självstudiekurs: Skapa och distribuera behållaravbildningar i molnet med Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Nästa steg

[Skapa ditt behållarerbjudande](./cpp-create-offer.md)
