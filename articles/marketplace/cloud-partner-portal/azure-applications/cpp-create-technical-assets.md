---
title: Skapa tekniska till gångar för Azure-program | Azure Marketplace
description: Skapa tekniska till gångar för ett erbjudande för Azure-program.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: c9abaab4597e9fea43a2f0dcabc0e4a527ed5a6a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827608"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Förbered dina tekniska till gångar för Azure-program

I den här artikeln beskrivs resurserna för att förbereda tekniska till gångar för ditt Azure-program.

## <a name="before-you-begin"></a>Innan du börjar

Granska följande video, [skapa Solution-mallar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), en översikt över hur du skapar en Azure Resource Manager mall för att definiera en Azure-programlösning och sedan publicera app-erbjudandet på Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Läs följande dokumentation om Azure-program, som innehåller snabb starter, självstudier och exempel.

- [Förstå Azure Resource Manager mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Snabbstarter:

  - [Azure snabb starts mallar](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure snabb starts mallar](https://github.com/azure/azure-quickstart-templates)
  - [Publicera program definition](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Distribuera tjänst katalog program](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Självstudier:

  - [Skapa definitionsfiler](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Publicera marknadsplatsprogram](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Stickprov

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Hanterade program lösningar](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Grundläggande teknisk kunskap

Att utforma, skapa och testa dessa till gångar tar tid och kräver teknisk kunskap om både Azure-plattformen och teknikerna som används för att skapa erbjudandet.

Teknik teamet bör ha kunskap om följande Microsoft-tekniker:

- Grundläggande förståelse för [Azure-tjänster](https://azure.microsoft.com/services/)
- Så här [utformar och skapar du Azure-program](https://azure.microsoft.com/solutions/architecture/)
- Arbeta med kunskaper om [azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)och [Azure-nätverk](https://azure.microsoft.com/services/?filter=networking)
- Arbeta med [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Arbeta med kunskaper om [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Rekommenderade verktyg

Välj en eller båda av följande skript miljöer för att hantera ditt Azure-program:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Vi rekommenderar att du lägger till följande verktyg i utvecklings miljön:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) med följande fil namns tillägg:

  - Tillägg: [Azure Resource Manager verktyg](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Tillägg: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Tillägg: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

Vi rekommenderar också att du går igenom de tillgängliga verktygen på [Azure utvecklarverktyg](https://azure.microsoft.com/tools/) -sidan och, om du använder Visual Studio, [Visual Studio Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Nästa steg

[Skapa ett erbjudande för Azure-program](./cpp-create-offer.md)

