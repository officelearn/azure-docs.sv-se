---
title: Tekniska begrepp för Azure Container-erbjudanden – Microsoft Commercial Marketplace
description: Teknisk resurs och rikt linjer som hjälper dig att konfigurera ett behållar erbjudande på Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 04/09/2020
ms.openlocfilehash: 3599052e49b91747580bf506f06ebfb8d2e34423
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2020
ms.locfileid: "93346816"
---
# <a name="create-an-azure-container-offer"></a>Skapa ett erbjudande för Azure Container

Den här artikeln innehåller tekniska resurser och rekommendationer som hjälper dig att skapa ett behållar erbjudande på Azure Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Snabb starter, självstudier och exempel finns i [Azure Container instances-dokumentationen](../../container-instances/index.yml).

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet.

Förutom din lösnings domän bör ditt tekniska team ha kunskap om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
- Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
- Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking)
- Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Arbeta med [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Rekommenderade verktyg

Välj en eller båda av följande skript miljöer för att hantera din behållar avbildning:

- [Azure PowerShell](/powershell/azure/)
- [Azure CLI](/cli/azure/).

Vi rekommenderar att du lägger till dessa verktyg i utvecklings miljön:

- [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Granska de tillgängliga verktygen på sidan [Azure utvecklarverktyg](https://azure.microsoft.com/) . Om du använder Visual Studio granskar du de verktyg som är tillgängliga i [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Skapa behållar avbildningen

Mer information finns i följande Självstudier:

- [Självstudie: skapa en behållar avbildning för distribution till Azure Container Instances](../../container-instances/container-instances-tutorial-prepare-app.md)
- [Självstudie: bygga och distribuera behållar avbildningar i molnet med Azure Container Registry uppgifter](../../container-registry/container-registry-tutorial-quick-task.md).

## <a name="next-steps"></a>Nästa steg

- [Skapa ett behållar erbjudande](create-azure-container-offer.md).