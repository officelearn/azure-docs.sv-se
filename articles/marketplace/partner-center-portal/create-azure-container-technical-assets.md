---
title: Förbereda dina tekniska resurser för Azure Container
description: I den här artikeln beskrivs stegen och kraven för att konfigurera ett behållarerbjudande på Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 62c77681accb19183b5e8aa3ae8d80c225b2d284
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266024"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Förbereda dina tekniska resurser för Azure Container

> [!IMPORTANT]
> Vi flyttar hanteringen av dina Azure Container-erbjudanden från Cloud Partner Portal till Partner Center. Tills dina erbjudanden har migrerats följer du instruktionerna i [Förbered dina behållarens tekniska resurser](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) för Cloud Partner Portal för att hantera dina erbjudanden.

I den här artikeln beskrivs stegen och kraven för att konfigurera ett behållarerbjudande på Azure Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Snabbstartar, självstudier och exempel finns i [Azure Container Instances](https://docs.microsoft.com/azure/container-instances).

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Designa, bygga och testa dessa tillgångar tar tid och kräver teknisk kunskap om både Azure-plattformen och de tekniker som används för att skapa erbjudandet.

Förutom din lösningsdomän bör ditt ingenjörsteam ha kunskap om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure Services](https://azure.microsoft.com/services/)
- Så här [utformar och arkitekt azure-program utformas](https://azure.microsoft.com/solutions/architecture/)
- Kunskap om [virtuella Azure-datorer,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage)och Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
- Kunskap om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Arbetskunskap om [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Föreslagna verktyg

Välj en eller båda av följande skriptmiljöer för att hantera containeravbildningen:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

Vi rekommenderar att du lägger till dessa verktyg i utvecklingsmiljön:

- [Azure Lagringsutforskaren](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio-koden](https://code.visualstudio.com/)
  - Tillägg: [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Förlängning: [Försköna](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Förlängning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Granska de tillgängliga verktygen på sidan [Azure Developer Tools.](https://azure.microsoft.com/) Om du använder Visual Studio läser du de verktyg som finns på [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Skapa behållaravbildningen

Mer information finns i följande självstudier:

- [Självstudiekurs: Skapa en behållaravbildning för distribution till Azure Container-instanser](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Självstudiekurs: Skapa och distribuera behållaravbildningar i molnet med Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-step"></a>Nästa steg

- [Skapa ditt containererbjudande](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
