---
title: Skapa tekniska resurser för Azure-program | Azure Marketplace
description: Skapa de tekniska tillgångarna för ett Azure-programerbjudande.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 041b2133ed63a906d3fea3ab67890a0057151b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285273"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Förbereda dina tekniska resurser för Azure-program

I den här artikeln beskrivs resurserna för att förbereda de tekniska resurserna för ditt Azure-programerbjudande.

## <a name="before-you-begin"></a>Innan du börjar

Granska följande video, [Skapa lösningsmallar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), en översikt över hur du skapar en Azure Resource Manager-mall för att definiera en Azure-programlösning och sedan hur du publicerar apperbjudandet till Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Granska följande Azure-programdokumentation, som innehåller snabbstarter, självstudier och exempel.

- [Förstå Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Snabbstarter:

  - [Snabbstartsmallar för Azure](https://azure.microsoft.com/documentation/templates/)
  - [Snabbstartsmallar för GitHub Azure](https://github.com/azure/azure-quickstart-templates)
  - [Publicera programdefinition](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Distribuera tjänstkatalogapp](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Självstudier:

  - [Skapa definitionsfiler](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicera marknadsplatsprogram](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Exempel:

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Hanterade programlösningar](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Designa, bygga och testa dessa tillgångar tar tid och kräver teknisk kunskap om både Azure-plattformen och de tekniker som används för att skapa erbjudandet.

Ditt ingenjörsteam bör ha kunskap om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure Services](https://azure.microsoft.com/services/)
- Så här [utformar och arkitekt azure-program utformas](https://azure.microsoft.com/solutions/architecture/)
- Kunskap om [virtuella Azure-datorer,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage)och Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
- Kunskap om [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Arbetskunskap om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Föreslagna verktyg

Välj en eller båda av följande skriptmiljöer för att hantera ditt Azure-program:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar att du lägger till följande verktyg i utvecklingsmiljön:

- [Utforskaren för Azure Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio-kod](https://code.visualstudio.com/) med följande tillägg:

  - Tillägg: [Verktyg för Azure Resource Manager](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Förlängning: [Försköna](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Förlängning: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi föreslår också att du granskar de tillgängliga verktygen på sidan [Azure Developer Tools](https://azure.microsoft.com/tools/) och, om du använder Visual Studio, Visual Studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Nästa steg

[Skapa ett erbjudande för Azure-program](./cpp-create-offer.md)

