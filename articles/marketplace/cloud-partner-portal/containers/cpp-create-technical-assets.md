---
title: Skapa tekniska till gångar för Azure-behållare | Azure Marketplace
description: Skapa tekniska till gångar för en Azure-behållare.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 6aae1957a3dcc58339eb4f3c9e250513843ceb18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819055"
---
# <a name="prepare-your-container-technical-assets"></a>Förbered dina tekniska till gångar för behållare

I den här artikeln beskrivs steg och krav för att konfigurera en container för Azure Marketplace.

## <a name="before-you-begin"></a>Innan du börjar

Läs [Azure Container instances](https://docs.microsoft.com/azure/container-instances) -dokumentationen, som innehåller snabb starter, självstudier och exempel.

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet.
 
Förutom din lösnings domän bör ditt tekniska team ha kunskap om följande Microsoft-tekniker:

-   Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/) 
-   Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
-   Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking)
-   Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Arbeta med kunskaper om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Rekommenderade verktyg

Välj en eller båda av följande skript miljöer för att hantera din behållar avbildning:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Dessutom rekommenderar vi att du lägger till följande verktyg i utvecklings miljön:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också att du går igenom de tillgängliga verktygen på [Azure utvecklarverktyg](https://azure.microsoft.com/tools/) -sidan och, om du använder Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>Skapa behållar avbildningen

Mer information finns i följande avsnitt:

* [Självstudie: skapa en behållar avbildning för distribution till Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Självstudie: bygga och distribuera behållar avbildningar i molnet med Azure Container Registry uppgifter](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Nästa steg

[Skapa ditt container erbjudande](./cpp-create-offer.md)
